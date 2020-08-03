# Auto Layout Container

The layout component can be mounted to any node, making the node into a container with the auto layout function. The so-called auto layout container can automatically array the child nodes according to certain rules and adjust the container type nodes of its own size according to the sum total of bounding boxes of the node content.

## Layout Type

Auto layout components have several basic layout types. These can be set up by the `Layout Type` property, which includes the following types.

### Horizontal Layout

![horizontal](auto-layout/horizontal.png)

When `Layout Type` is set as `Horizontal`, all the child nodes will automatically be arrayed horizontally, and the width of the Layout node will be set up according to the sum `Width` of the child nodes. Then the two Label nodes included in the picture above will automatically be arrayed horizontally.

In the horizontal layout type, the Layout component will not interfere with the position or height properties of the node on the y axis. The subnode can even be put outside the maximal height of the layout node's bounding box. If child nodes need to be aligned upward along the y axis, you can add the Widget component to the child nodes and open the alignment mode of the Top or Bottom.

### Vertical Layout

![vertical](auto-layout/vertical.png)

When `Layout Type` is set as `Vertical`, all the child nodes will automatically be arrayed vertically and the height of the Layout node will be set up according to the sum `Height` of the child nodes.

In the vertical layout type, the Layout component will not modify the position or width properties of the node on the x axis. Child nodes can only be neatly arrayed by adding the Widget and opening the Left or Right alignment mode.

## Node Direction

The Layout arrays' child nodes are based on the display order of child nodes in __Hierarchy__ and refers to the array directions set up by the `Vertical Direction` or `Horizontal Direction` properties.

### Horizontal Direction

You can set up two directions: `Left to Right` or `Right to Left`. The former will array the nodes from left to right according to their display order in __Hierarchy__; the later will array the nodes from right to left according to their display order in __Hierarchy__.

### Vertical Direction

You can set up two directions: `Top to Bottom` or `Bottom to Top`. The former will array the nodes from top to bottom according to their display order in __Hierarchy__; the later will array the nodes from bottom to top according to their display order in __Hierarchy__.

## Other layout types are coming soon

We will update this part of the document in later edition.

For the properties of other Layout components, please check [Layout](../editor/layout.md) document.
