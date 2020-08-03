# UIModel 组件参考

UIModel 是一个将 3D 模型从 3D 渲染管线转换到 2D 渲染管线的带有转换功能的渲染组件。该组件支持 3D 模型和粒子在 UI 上的显示，没有这个组件，即使模型和粒子节点在 UI 里也不会被渲染。

该组件的添加方式是在层级管理器中选中带有或继承自 ModelComponent 组件的节点，然后点击 **属性检查器** 下方的 **添加组件** 按钮，选择 **UI/Model** 即可。而粒子则是添加到粒子节点上。通常结构如下所示：

![ui-model-hierachy](uimodel/ui-model-hierarchy.png)

---

- [其他基础模块参考](base-component.md)

- [渲染模块参考](render-component.md)
