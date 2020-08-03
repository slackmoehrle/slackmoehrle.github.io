# 引擎定制工作流程

Cocos Creator 3D 的引擎部分包括 JavaScript、Cocos2d-x-lite 和 adapter 三个部分(暂不支持Cocos2d-x-lite 和 adapter 引擎定制)。引擎在 github 上开源。地址在：

- JavaScript 引擎：<https://github.com/cocos-creator/engine>

建议你通过 GitHub 的 fork 工作流程来维护自己定制的代码，以便在将来引擎升级时，可以方便地将定制的部分更新上去，具体操作方式请阅读 [Fork a repo](https://help.github.com/articles/fork-a-repo)。如果你愿意帮助 Cocos 越做越好，欢迎在 GitHub 提交你的修改，请参考 [如何向 Cocos 提交代码](../../submit-pr/submit-pr.md)。关于更多 GitHub 相关工作流程请参考 [GitHub Help](https://help.github.com)。

另外，根据不同的 Creator 3D 版本，还需要切换不同的引擎分支，例如：

- **master/develop** 分支：当前最新版本所用分支
- **vX.Y-release** 分支：对应 X.Y 版本所用分支
- **vX.Y** 分支：和 vX.Y-release 分支相同，主要用于范例工程
- **next** 分支：大型重构所用分支

通常建议使用和所用 Creator 3D 相同版本的 vX.Y-release 分支，如果找不到的话，则使用 master 分支。

## 1 定制 JavaScript 引擎

如果您只需要定制 Web 版游戏的引擎功能，或只需要修改纯 JavaScript 层逻辑（如 UI 系统，动画系统），那么您只要按照下面的流程修改 JS 引擎就可以了。

### 1.1 获取 JS 引擎

如果您仅需基于当前的版本做一些调整，那么在 Cocos Creator 3D 内置的引擎基础上修改就可以了。点击 Creator 3D 编辑器右上方的 **打开程序安装路径**，然后将内置的 **engine** 目录拷贝到本地其他路径。

![](engine-customization/open-engine.png)

如果您想获得官方正在开发中的最新版本，首先您需要从 github 上 fork 或者克隆 JavaScript 引擎的原始版本（地址见上文）。JavaScript 引擎在使用前请根据 Creator 3D 版本切换相对应的分支。下载完成后存放到任意本地路径。

![](engine-customization/download-repo-js.png)

### 1.2 安装编译依赖

```bash
# 在命令行中进入引擎路径
cd /Users/yufang/engine
# 安装 gulp 构建工具
npm install -g gulp
# 安装依赖的模块
npm install
```
备注：生成debuginfos需要gulp构建工具。

### 1.3 进行修改然后编译

接下来您可以定制引擎修改了，修改之后请在命令行中继续执行：

```bash
npm run build
```

也可以在 Cocos Creator 3D 中通过 `开发者 -> 编译引擎` 选项进行编译。

![](engine-customization/build.png)

该命令会在引擎目录下生成一个 `bin` 文件夹，并将引擎源码编译到 `bin` 目录下。

![](engine-customization/bin.png)

### 1.4 在 Cocos Creator 3D 中使用定制版引擎

通过 `项目 -> 项目设置` 面板的 **引擎设置** 选项卡，设置本地定制后的 JavaScript 引擎路径。

![](engine-customization/setting-js.png)
