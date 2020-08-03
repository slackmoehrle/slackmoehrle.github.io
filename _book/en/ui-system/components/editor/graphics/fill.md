# Fill

The `fill()` method is used to fill the current image (path). The color used is [`fillColor`](./fillColor.md).

> __Note__: If the path is not closed, the `fill()` method adds a line from the end of the path to the start point to close the path and then fills the path.

## Example

```javascript
var ctx = node.getComponent(cc.GraphicsComponent);
ctx.rect(20,20,150,100);
ctx.fillColor = cc.Color.GREEN;
ctx.fill();
```

<a href="fill.png"><img src="fill.png"></a>

<hr>

Return to the [Graphics Component Reference](../graphics.md) documentation.
