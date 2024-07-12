## vue与Electron

### 相关文档

Vite: https://vitejs.cn
Vue : https://cn.vuejs.org
Electron: https://www.electronjs.org/
electron-builder: https://github.com/electron-userland/electron-builder
concurrently: https://github.com/open-cli-tools/concurrently

### 示例环境
系统： macOS 14.5
Node 版本： v16.14.0
npm 版本： v7.1.2
Electron 版本： v20.1.0
Vue 版本： v3.2.37

### 搭建项目
1、创建 Vue 项目
参考：https://cn.vuejs.org/guide/quick-start.html

确保你安装了最新版本的 Node.js，然后在命令行中运行以下命令：

npm init vue@latest

这一指令将会安装并执行 create-vue，它是 Vue 官方的项目脚手架工具。你将会看到一些诸如 TypeScript 和测试支持之类的可选功能提示，本项目只做示例，所以只选了 Vue Router ：

在项目被创建后，通过以下步骤安装依赖并启动开发服务器：

cd <your-project-name>
npm install
npm run dev


### 安装 Electron
参考：https://www.electronjs.org/zh/docs/latest/tutorial/installation

执行以下命令安装 Electron ：
npm install electron --save-dev

如果安装过程中卡住，可尝试设置以下环境变量
ELECTRON_MIRROR="https://npmmirror.com/mirrors/electron/"

```
mac 修改环境变量的方法：
在 终端 中输入 vim ~/.zshrc 打开文件
按 i 进入编辑模式
添加一行 export ELECTRON_MIRROR="https://npmmirror.com/mirrors/electron/"
按 esc 退出编辑模式
输入 :wq 保存并退出
在 终端 输入 source ~/.zshrc 使刚才的修改生效
```

运行 Electron
参考：https://www.electronjs.org/zh/docs/latest/tutorial/quick-start

在根目录创建 electron 文件夹，新建 index.html 文件，添加如下代码：

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <!-- https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP -->
    <meta
      http-equiv="Content-Security-Policy"
      content="default-src 'self'; script-src 'self'"
    />
    <meta
      http-equiv="X-Content-Security-Policy"
      content="default-src 'self'; script-src 'self'"
    />
    <title>Hello from Electron renderer!</title>
  </head>
  <body>
    <h1>Hello from Electron renderer!</h1>
    <p>👋</p>
  </body>
</html>
```

在 electron 目录下新建文件 main.js，并添加如下代码：
```
const { app, BrowserWindow } = require('electron')
const path = require("path")

const createWindow = () => {
  const mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
  })
  
  // 使用 loadFile 加载 electron/index.html 文件
  mainWindow.loadFile(path.join(__dirname, "./index.html"));
}

// 在应用准备就绪时调用函数
app.whenReady().then(() => {
  createWindow()
})
```

修改 package.json 文件，指定 electron/main.js 为 Electron 的入口文件，并添加 electron:dev 命令以开发模式运行 Electron：
```
{
  "main": "electron/main.js",
  "scripts": {
    "electron:dev": "electron ."
  }
}
```

然后打开终端，执行 npm run electron:dev ，显示窗口如下所示，Electron 运行成功

### Vue 和 Electron 结合
删除 electron/index.html 文件，修改 electron/main.js 文件代码如下：
```
const { app, BrowserWindow } = require('electron')
// const path = require("path")

const createWindow = () => {
  const mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
  })

  // 主要改了这里
  // mainWindow.loadFile(path.join(__dirname, "./index.html"));
  // 使用 loadURL 加载 http://localhost:3004 ，也就是我们刚才创建的 Vue 项目地址
  // 3004 改为你 Vue 项目的端口号
  mainWindow.loadURL("http://localhost:3004/");
}

app.whenReady().then(() => {
  createWindow()
})
```
有时开发需要，会使用 nginx 代理为线上地址，则将 loadURL 的参数改为代理的地址，并在 electron/main.js 中添加如下代码：

```
// 证书的链接验证失败时，触发该事件
app.on(
  "certificate-error",
  function (event, webContents, url, error, certificate, callback) {
    event.preventDefault();
    callback(true);
  }
);
```
打开终端，执行 npm run dev 启动 Vue 项目，再新建一个终端，执行 npm run electron:dev 启动 Electron ，即可成功启动，显示如下：

这样每次启动项目需要分别执行两个命令，有些麻烦，我们使用 concurrently把它们合并成一个命令。

执行以下命令，安装 concurrently：
```
 npm install concurrently --save-dev
```
修改 package.json 文件中的 electron:dev 命令，同时执行 vite 和 electron . 两个命令，用引号将单独的命令括起来，并使用 \ 转义引号，代码如下：
```
// package.json
{
  "scripts": {
    "electron:dev": "concurrently vite \"electron .\""
  }
}
```
重新执行 npm run electron:dev ，即可成功显示。







