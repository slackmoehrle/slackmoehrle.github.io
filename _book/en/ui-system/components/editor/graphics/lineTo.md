# Line To

The `lineTo()` method is used to add a new point, and then create a line from the current graphic cursor to that point.

| Parameter | Description |
| --------- | ----------- |
| *x* | The x coordinate of the target location of the path. |
| *y* | The y coordinate of the target position of the path. |

## Example

```javascript
var ctx = node.getComponent(cc.GraphicsComponent);
ctx.moveTo(20,100);
ctx.lineTo(20,20);
ctx.lineTo(70,20);
ctx.stroke();
```

<a href="lineTo.png"><img src="lineTo.png"></a>

<hr>

Return to the [Graphics Component Reference](../graphics.md) documentation.
