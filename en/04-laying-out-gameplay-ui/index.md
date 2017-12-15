## Laying out the User Interface
The first thing we need to do is start to create our __user interface__. This is what the player will see when they play your game. __Cocos Creator__ promotes a true team workflow by allowing __artists__ and __developers__ to work together on a project at the same time. Artists can work inside __Cocos Creator__ to so `Scene` layout while the developers can be adding and testing game play code. They don't step on each others toes.

### Table of contents
- []()



### Customizing our first layer
Now that we have our `Node` we need to assign it a color and how much space it will occupy on the `Canvas`. Typically a background layer might take up the entire `Canvas`. Let's adjust the __Background__ node's properties to do exactly this.

* First, select the __Background__ node from the __Node Tree__ panel.

* Second, in the __Properties__ panel, set the __color__ to anything you like. I am going to stick close to the magenta shade from previous old school screenshot we referenced before.

    ![](img/background_color_picker.png)

* Third, in the __Properties__ panel, set the __size__ and __position__ of our __Background__ node. We want it to take up the entire canvas, so this this should be set to the same size as the __design resolution__ or in this case __640 x 960__. Also, the __position__ should be set to __0, 0__ so the node starts at the bottom left corner of the canvas.

    ![](img/size.png)  ![](img/position.png)

* Last, double check to make sure what you have matches our progress so far.

    ![](img/background_finished.png)

__Task:__ Now is a good time to save your project! From the __File__ menu, select __Save Scene__ or use your operating systems shortcut key.
