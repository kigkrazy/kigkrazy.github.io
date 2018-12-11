---
title: vscode下VUE开发环境配置
categories:
  - linux
tags:
  - linux
  - systemd
---

1. 安装插件

   - atom one dark
   - vetur
   - material icon
   - prettier
   - Subtle Match Brackets

2. 打开设置窗口


    * 从打开`文件`->`首选项`->`设置`
    * 在`扩展`栏中随便找一个插件设置，并且下啦找到`在setting.json中编辑`
    * 打开`setting.json`

3. 往`setting.json`写入设置

```
{
  // 0- 使用说明:下载(修改setting.json1 为json) || 直接复制文本到setting.json
  // 0- 总原则:Less is More
  // 0- 插件配置原则:够用即可,不贪婪插件,绝对不装功能重复的,比如各种snippet,蛋疼
  // 0- snippet配置原则:我一般很少用别人的snippet,一下太多了,掌握不了,更不利于熟练运用.用到什么就把snippet加到自己的snippet中
  // 0- 界面原则:能不显示的就不显示(比如minimap,)需要显示的底部状态栏,顶部文件栏(仅此而已),半显示的(explorer(alt+.),menu(alt)),全屏不是很喜欢,影响其他功能
  // 1- 主题 Theme------------------------------------------ 需要安装atom one dark 和 material icon
  "workbench.colorTheme": "Atom One Dark", // atom 主题,字体配色好,比 Material配色舒服  sublime直接是 material
  "workbench.iconTheme": "material-icon-theme", // icon主题 material
  "material-icon-theme.folders.theme": "specific", // spaecific风格
  "material-icon-theme.activeIconPack": "angular_ngrx", //主题样式
  "material-icon-theme.hidesExplorerArrows": true, //隐藏图标箭头
  // 2- 字体 Font---------------------------------------------
  // 关于注释为什么是斜体,也是非常纠结啊,中文斜体不好看,英文斜体很漂亮,真是鱼与熊掌
  //"editor.fontFamily": "Consolas,'Microsoft yahei", // 英文:consolas 中文:雅黑
  // "editor.fontSize": 20, // 字号
  // "editor.lineHeight": 40, // 行高
  // 3- 显示 view-----------------------------------------------
  "window.zoomLevel": 0, //视窗缩放
  "editor.renderWhitespace": "all", // 显示空格,写代码嘛,显示空格舒服,区分tab和空格 ,''和 ' '
  "workbench.activityBar.visible": true, // 显示右侧快捷菜单 alt+shift+. 切换
  "editor.minimap.enabled": false, // 不显示右侧 minimap
  "workbench.statusBar.visible": true, // 显示状态条
  //"window.menuBarVisibility": "toggle", //隐藏菜单栏
  "explorer.openEditors.visible": 0, //最近打开的文件不显示
  // 5- 起始页 startup -----------------------------------------
  "workbench.startupEditor": "none", // 起始页啥也不显示
  // 14- 烦人的括号匹配matchBrackets ----------------------------------------------- 需要安装Subtle Match Brackets
  // "editor.matchBrackets": false, // 干掉原先的丑陋的恼人的匹配
  // "subtleBrackets.style": {
  //   // 换上我们漂亮的小金条
  //   "global": {
  //     "borderColor": "GoldenRod",
  //     "borderWidth": "4px"
  //   }
  // },

  // 强制单引号
  "prettier.singleQuote": true,
  // 尽可能控制尾随逗号的打印
  "prettier.trailingComma": "all",
  // 开启 eslint 支持
  "prettier.eslintIntegration": true,
  "prettier.printWidth": 200, // 换行字符串阈值
  // 保存时自动fix
  "eslint.autoFixOnSave": true,
  // 添加 vue 支持
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    {
      "language": "vue",
      "autoFix": true
    }
  ],
  // 使用插件格式化 html
  "vetur.format.defaultFormatter.html": "js-beautify-html",
  // 格式化插件的配置
  "vetur.format.defaultFormatterOptions": {
    "js-beautify-html": {
      // 属性强制折行对齐
      "wrap_attributes": "force-aligned"
    },
    "prettyhtml": {
      "printWidth": 200,
      "singleQuote": false,
      "wrapAttributes": false,
      "sortAttributes": true
    }
  },
  "editor.matchBrackets": false
}
```
