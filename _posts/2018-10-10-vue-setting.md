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
  // 5- 起始页 startup -----------------------------------------
  "workbench.startupEditor": "none", // 起始页啥也不显示

  // 11- eslint 配置---------------------------------------------
  // 需要安装eslint,并npm i eslint // 自动修复简单错误// 和格式化 在缩进上有一些重合
  "eslint.validate": [
    // 支持eslint的文件 奇怪的是这里设置的autoFix居然不起作用
    "javascript",
    "javascriptreact",
    {
      // "language": "vue",
      "autoFix": true
    }
  ],
  "eslint.autoFixOnSave": true, // 保存时自动修复,格式化  和 editor formatonsave,略冲突

  "vetur.format.defaultFormatterOptions": {
    "js-beautify-html": {
      // force-aligned | force-expand-multiline
      "wrap_attributes": "force-aligned"
    },
    "prettyhtml": {
      "printWidth": 200,
      "singleQuote": false,
      "wrapAttributes": false,
      "sortAttributes": true
    },
    "prettier": {
      "eslintIntegration": true, // prettier的eslint格式化从eslintrc.js中读取
      "semi": false, // 不加分号
      "singleQuote": true, // 单引号
      "rmatOnSave": true, // prettier用的是系统默认的保存格式化
      "printWidth": 200, // 换行字符串阈值
      "trailingComma": "none", // 最后一个对象元素加逗号
      "arrowParens": "avoid", // (x) => {} 是否要有小括号
      "proseWrap": "preserve" // 是否要换行
    }
  },
  // 13- 自动补全 suggest -------------------------------------------------------
  "editor.quickSuggestionsDelay": 400, //设置的延迟稍微高一些,400ms 连续输入之后在出现// 默认30ms,一个字母就要出现一下,神烦
  "html.suggest.html5": false, // 鼠标悬停在html标签上会提示,这个是啥,怎么用等等. 干掉,也是神烦,我会不知道标签? 小看我,哼!
  // 14- 烦人的括号匹配matchBrackets ----------------------------------------------- 需要安装Subtle Match Brackets
  "editor.matchBrackets": false, // 干掉原先的丑陋的恼人的匹配
  "subtleBrackets.style": {
    // 换上我们漂亮的小金条
    "global": {
      "borderColor": "GoldenRod",
      "borderWidth": "4px"
    }
  }
  //"editor.cursorWidth": 4, // 顺便光标美个容,破费!
  // 15- Selection 选择的烦恼 默认点选一个单词或者字母,都会全部匹配上,你说烦不烦啊,
  //"editor.selectionHighlight": false, // 歇歇不行么,强行切换到懒惰模式
```
