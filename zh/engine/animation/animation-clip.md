
# 动画剪辑

动画剪辑是一组动画曲线，包含了所有动画数据。

## 动画曲线

动画曲线描述了某一对象上某一属性值随着时间的变化。
在内部，动画曲线存储了一系列时间点，每个时间点都对应着一个（曲线）值，称为一帧，或关键帧。
当动画系统运作时，动画组件根据当前动画状态计算出指定时间点应有的（结果）值并赋值给对象，完成属性变化；这一计算过程称为采样。

以下代码片段演示了如何程序化地创建动画剪辑。
```ts
import { AnimationClip, animation, js } from "cc";
const animationClip = new AnimationClip();
animationClip.duration = 1.0; // 整个动画剪辑的周期。任何帧时间都不应该大于此属性。
animationClip.keys = [ [ 0.3, 0.6, 0.9 ] ]; // 该动画剪辑所有曲线共享的帧时间
animationClip.curves = [{ // 组件上的属性曲线
    modifiers: [ // 目标是当前结点的
        // "Body" 子结点的
        new animation.HierarchyPath('Body'),
        // `MyComponent`的
        new animation.ComponentPath(js.getClassName(MyComponent)),
        // `value` 属性
        'value',
    ],
    data: {
        keys: 0, // 索引至 `AnimationClip.keys`, 即 [ 0.3, 0.6, 0.9]
        values: [ 0.0, 0.5, 1.0 ],
    },
}];
```

上述动画剪辑包含了一条曲线以控制 "Body" 子结点的 `MyComponent` 组件的 `value` 属性，曲线有三帧，使得 `value` 属性在 0.3 秒时变为 0.5，在0.6 秒时变为 0.5，在 0.9 秒时变为 1.0。

注意，曲线的帧时间是以引用方式索引到 `AnimationClip.keys` 数组中的。
如此一来，多条曲线可以共享帧时间。这将带来额外的性能优化。

### 目标对象

动画曲线的目标可以是任意 JavaScript 对象。
`modifiers` 字段指定了在 _运行时_ 如何从当前结点对象寻址到目标对象。

`modifiers` 是一个数组，
它的每一个元素表达了如何从上一级的对象寻址到另一个对象，
最后一个元素寻址到的对象就作为曲线的目标对象。
这种行为就好像文件系统的路径，因此每个元素都被称为“目标路径”。

当目标路径是`string` / `number` 时，
表示寻址到上一级对象的属性，其本身就指定了属性名。
否则，目标路径必须是实现接口 `animation.TargetPath` 的对象。

Cocos Creator 3D 内置了以下几个实现自接口 `animation.TargetPath` 的类：
- `animation.HierarchyPath` 将上一级的对象视为结点，并寻址到它的某个子结点；
- `animation.ComponentPath` 将上一级的对象视为结点，并寻址到它的某个组件。

目标路径可以任意组合，只要它们具有正确的含义：
```ts
// 目标对象是
modifiers: [
    // "nested_1" 子结点的 "nested_2" 子结点的 "nested_3" 子结点的
    new animation.HierarchyPath('nested_1/nested_2/nested_3'),
    // `BlahBlahComponent` 组件的
    new animation.ComponentPath(js.getClassName(BlahBlahComponent)),
    // `names` 属性的
    'names',
    // 第一个元素
    0,
]
```

当你的目标对象不是一个属性，而是必须从一个方法返回时，自定义目标路径就很有用：
```ts
class BlahBlahComponent extends Component {
    public getName(index: number) { return _names[index]; }
    private _names: string[] = [];
}

// 目标对象是
modifiers: [
    // "nested_1" 子结点的 "nested_2" 子结点的 "nested_3" 子结点的
    new animation.HierarchyPath('nested_1/nested_2/nested_3'),
    // `BlahBlahComponent` 组件的
    new animation.ComponentPath(js.getClassName(BlahBlahComponent)),
    // 第一个 "name"
    {
        get: (target: BlahBlahComponent) => target.getName(0),
    },
]
```
如果希望你的自定义目标路径是可序列化的，将它们声明为类：
```ts
@ccclass
class MyPath implements animation.TargetPath {
    @property
    public index = 0;
    constructor(index: number) { this.index = index; }
    get (target: BlahBlahComponent) {
        return target.getName(this.index);
    }
}

// 目标对象是
modifiers: [
    // "nested_1" 子结点的 "nested_2" 子结点的 "nested_3" 子结点的
    new animation.HierarchyPath('nested_1/nested_2/nested_3'),
    // `BlahBlahComponent` 组件的
    new animation.ComponentPath(js.getClassName(BlahBlahComponent)),
    // 第一个 "name"
    new MyPath(0),
]
```

目标对象的寻址是在运行时完成的，这种特性使得动画剪辑可以复用到多个对象上。

### 赋值

当采样出值后，默认情况下将使用赋值操作符 `=` 将值设置给目标对象。

然而有时候，你并不能用赋值操作符来完成设置。
例如，当你想设置材质对象的 Uniform 时，你就无法通过赋值操作符来完成。
因为材质对象仅提供了 `setUniform(uniformName, value)` 方法来改变 uniform。

对于这种情况，曲线字段 `valueAdapter` 提供了一种机制使你自定义如何
将值设置到目标对象。

示例如下：
```ts
class BlahBlahComponent {
    public setUniform(index: number, value: number) { /* */ }
}

{ // 曲线
    valueAdapter: {
        // 在实例化曲线时调用
        forTarget(target: BlahBlahComponent) {
            // 在这里做一些有用的事
            return {
                // 在每一次设置目标对象的值时调用
                set(value: number) {
                    target.setUniform(0, value);
                }
            };
        }
    },
};
```

如果希望你的“自定义赋值”是可序列化的，将它们声明为类：
```ts
@ccclass
class MyValueProxy implements animation.ValueProxyFactory {
    @property
    public index: number = 0;
    constructor(index: number) { this.index = index; }
    // 在实例化曲线时调用
    public forTarget(target: BlahBlahComponent) {
        // 在这里做一些有用的事
        return {
            // 在每一次设置目标对象的值时调用
            set(value: number) {
                target.setUniform(0, value);
            }
        };
    }
}
```

`animation.UniformProxyFactory` 就是这样一种“自定义赋值”的类，
它实现了设置材质的 uniform 值：
```ts
{ // 目标对象是
    modifiers: [
        // `ModelComponent` 组件的
        new animation.ComponentPath(js.getClassName(ModelComponent)),
        // `sharedMaterials` 属性的
        'sharedMaterials',
        // 第一个材质
        0,
    ],
    valueAdapter: new animation.UniformProxyFactory(
        0, // Pass 索引
        'albedo', // Uniform 名称
    ),
};
```

### 采样

若采样时间点恰好就等于某一关键帧的时间点，则使用该关键帧上的动画数据。
否则——当采样时间点居于两帧之间时，结果值应同时受两帧数据的影响，
采样时间点在两处关键帧的时刻区间上的比例（`[0,1]`）反应了影响的程度。
Cocos Creator 3D 允许将该比例映射为另一个比例，以实现不同的“渐变”效果。
这些映射方式，在 Cocos Creator 3D中称为**渐变方式**。
在比例确定之后，根据指定的**插值方式**计算出最终的结果值。
渐变方式和插值方式都影响着动画的平滑度。

#### 渐变方式

可以为每一帧指定渐变方式，也可以为所有帧指定统一的渐变方式。
渐变方式可以是内置渐变方式的名称或贝塞尔控制点。

以下列出了几种常用的渐变方式。
- `linear` 保持原有比例，即线性渐变；当未指定渐变方式时默认使用这种方式；
- `constant` 始终使用比例 0，即不进行渐变；与插值方式 `Step` 类似；
- `quadIn` 渐变由慢到快。
- `quadOut` 渐变由快到慢。
- `quadInOut` 渐变由慢到快再到慢。
- `quadOutIn` 渐变由快到慢再到快。
- `IBezierControlPoints`

<script src="./easing-method-example.js"> </script>
<button onclick="onEasingMethodExampleButtonClicked()">展开对比</button>
<div id="easing-method-example-panel"> </div>

#### 曲线值与插值方式

有些插值算法需要每一帧的曲线值中存储额外的数据，因此，
曲线值与目标属性的值类型不一定相同。
对于数值类型或值类型，Cocos Creator 3D 提供了几种通用的插值方式；
同时，也可以定义自己的插值方式。

当曲线数据的 `interpolate` 属性为 `true` 时，曲线将尝试使用插值函数：
- 若曲线值的类型为 `number`、`Number`，将应用线性插值；
- 否则，若曲线值继承自 `ValueType`，将调用 `ValueType` 的 `lerp` 函数完成插值，
Cocos Creator 3D 内置的大多数值类型都将其 `lerp` 实现为线性插值；
- 否则，若曲线值是[可插值的](https://docs.cocos.com/creator3d/api/zh/interfaces/animation.ilerpable.html)，将调用曲线值的 `lerp` 函数完成插值<sup id="a2">[2](#f2)</sup>。

若曲线值不满足上述任何条件，或当曲线数据的 `interpolate` 属性为 `false`时，
将不会进行插值操作 --- 永远使用前一帧的曲线值作为结果。

```ts
import { AnimationClip, color, IPropertyCurveData, SpriteFrame, v3 } from "cc";

const animationClip = new AnimationClip();

const keys = [ 0, 0.5, 1.0, 2.0 ];
animationClip.duration = keys.length === 0 ? 0 : keys[keys.length - 1];
animationClip.keys = [ keys ]; // 所有曲线共享一列帧时间

// 使用数值的线性插值
const numberCurve: IPropertyCurveData = {
    keys: 0,
    values: [ 0, 1, 2, 3 ],
    /* interpolate: true, */ // interpolate 属性默认打开
};

// 使用值类型 Vec3 的 lerp()
const vec3Curve: IPropertyCurveData = {
    keys: 0,
    values: [ v3(0), v3(2), v3(4), v4(6) ],
    interpolate: true,
};

// 不插值（因为显式禁用了插值）
const colorCuve: IPropertyCurveData = {
    keys: 0,
    values: [ color(255), color(128), color(61), color(0) ],
    interpolate: false, // 不进行插值
};

// 不插值（因为 SpriteFrame 无法进行插值）
const spriteCurve: IPropertyCurveData = {
    keys: 0,
    values: [
        new SpriteFrame(),
        new SpriteFrame(),
        new SpriteFrame(),
        new SpriteFrame()
    ],
};
```

下列代码展示了如何自定义插值算法：

```ts
import { ILerpable, IPropertyCurveData, Quat, quat, Vec3, v3, vmath } from "cc";

class MyCurveValue implements ILerpable {
    public position: Vec3;
    public rotation: Quat;

    constructor(position: Vec3, rotation: Quat) {
        this.position = position;
        this.rotation = rotation;
    }

    /** 将调用此方法进行插值。
     * @param this 起始曲线值
     * @param to 目标曲线值
     * @param t 插值比率，取值范围为 [0, 1]
     * @param dt 起始曲线值和目标曲线值之间的帧时间间隔
     */
    lerp (to: MyCurveValue, t: number, dt: number) {
        return new MyCurveValue(
            // 位置属性不插值
            this.position.clone(),
            // 旋转属性使用 Quat 的 lerp() 方法
            this.rotation.lerp(to.rotation, t), //
        );
    }

    /** 此方法在不插值时调用。
      * 它是可选的，若未定义此方法，则使用曲线值本身（即 `this`）作为结果值。
      */
    getNoLerp () {
        return this;
    }
}

/**
 * 创建了一条曲线，它实现了在整个周期内平滑地旋转但是骤然地变换位置。
 */
function createMyCurve (): IPropertyCurveData {
    const rotation1 = quat();
    const rotation2 = quat();
    const rotation3 = quat();

    vmath.quat.rotateY(rotation1, rotation1, 0);
    vmath.quat.rotateY(rotation2, rotation2, Math.PI);
    vmath.quat.rotateY(rotation3, rotation3, 0);

    return {
        keys: 0 /* 帧时间 */,
        values: [
            new MyCurveValue(v3(0), rotation1),
            new MyCurveValue(v3(10), rotation2),
            new MyCurveValue(v3(0), rotation3),
        ],
    };
}
```

渐变方式和插值方式都影响着动画的平滑度。


## 循环模式

可以通过设置 `AnimationClip.wrapMode` 为动画剪辑设置不同的循环模式。

以下列出出了几种常用的循环模式：

| AnimationClip.wrapMode | 效果 |
|---|---|
| WrapMode.Normal | 播放到结尾后停止。 |
| WrapMode.Loop | 循环播放。 |
| WrapMode.PingPng | 从动画开头播放到结尾后，从结尾开始反向播放到开头，如此循环 |

对于更多的循环模式，见 [WrapMode](https://docs.cocos.com/creator3d/api/zh/enums/animation.wrapmode.html)。

<b id="f1">1</b> 动画剪辑的所在结点是指引用该动画剪辑的动画状态对象所在动画组件所附加的结点。 [↩](#a1)

<b id="f2">2</b> 对于数值、四元数以及各种向量，Cocos 提供了相应的可插值类以实现[三次样条插值](https://en.wikipedia.org/wiki/Spline_interpolation)。 [↩](#a2)
