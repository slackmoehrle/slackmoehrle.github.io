# Clears

The `clear()` function is used to clear all paths.

## Example

```javascript
update: function (dt) {
    var ctx = node.getComponent(cc.GraphicsComponent);
    ctx.clear();
    ctx.circle(200,200, 200);
    ctx.stroke();
}

```

<hr>

Return to the [Graphics Component Reference](../graphics.md) documentation.
