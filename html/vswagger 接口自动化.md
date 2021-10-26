# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## vswagger接口自动化

vswagger是一个基于 swagger 快速生成 API 调用文件的命令行工具, 主要功能将接口同步到本地文件
• vswagger是一个基于 swagger 快速生成 API 调用文件的命令行工具
• 支持多分支、多模块接口管理
• 支持冗余接口清理
• 支持接口丢失提示
npm install -g vswagger-cli


添加根目录配置文件 .vswagger.js
/**
 * .vswagger 配置文件
 */
module.exports = {
    template: 'souche-finance#v2', // 可为空使用默认接口生成模板
    safe: false, // 是否生成保护数据(需要后台支持)
    output: "src/api", // 输出到api目录
    projectDir: "src", // 代码存放目录(可不配置默认为src路径)
    suffix: [".js",".vue"], // 指定过滤的文件(可不配置，默认.js,.vue文件)
    projects: [{
        domain: '',  // 环境变量
        token: '值', // swagger令牌
        modelName: "demo1", // 模块化名称
        docUrl: ['api-docs', 'api-docs', 'api-docs', 'api-docs']  // swagger base-url
    }, {
        domain: '',  // 环境变量
        token: '值', // swagger令牌
        modelName: 'demo2',
        docUrl: ['api-docs'] // 多个
    }] // 项目配置
};
// api-docs 是指swagger接口文档最下面的BASE URL链接
// token 是指在需要登录后才能访问的swager文档字符串(_security_token)

可直接运行案例附上:
/**
 * .vswagger.js 配置文件
 */
module.exports = {
    template: 'souche-finance#v2', // 可为空使用默认接口生成模板
    safe: false, // 是否生成保护数据(需要后台支持)
    output: "src/api", // 输出到api目录
    projectDir: "src", // 代码存放目录(可不配置默认为src路径)
    projects: [{
        domain: 'LEASECONSUMER',  // 环境变量
        token: '', // swagger令牌
        modelName: "leaseconsumer", // 模块化名称
        docUrl: ['http://leaseconsumer-a.sqaproxy.dasouche.net/api-docs']  // swagger base-url
    }, {
        domain: 'FMC',  // 环境变量
        token: '', // swagger令牌
        modelName: 'fmc',
        docUrl: ['http://fmc.sqaproxy.dasouche-inc.net/api-docs'] // 多个
    }] // 项目配置
};
vswagger init 项目目录(.vswagger.js目录) 模块名称(a模块,b模块,c模块)
例: vswagger init ./ demo1,demo2


直接上图看效果
index.js 文件是接口存放文件 instance.js 文件是配置 开发/预发/线上 接口访问的域名 util.js 文件是工具方法