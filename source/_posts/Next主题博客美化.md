---
title: Next主题博客美化
abbrlink: e23f9c5e
date: 2022-06-25 14:26:36
tags: 7
---

**优化美化博客插件推荐（自用）**

<!--more-->

# 唯一ID链接博文

插件名及链接地址：[hexo-abbrlink](https://github.com/rozbo/hexo-abbrlink)

Git Bash 输入：

```
npm install hexo-abbrlink --save
```

站点config.yml添加：

```
# URL
## Set your site url here. For example, if you use GitHub Page, set url as 'https://username.github.io/project'
url: http://example.com
permalink: posts/:abbrlink.html
permalink_defaults:
pretty_urls:
  trailing_index: true # Set to false to remove trailing 'index.html' from permalinks
  trailing_html: true # Set to false to remove trailing '.html' from permalinks

## abbrlink config
abbrlink:
  alg: crc32      #support crc16(default) and crc32 进制
  rep: hex        #support dec(default) and hex  算法
  drafts: false   #(true)Process draft,(false)Do not process draft. false(default) 
  ## Generate categories from directory-tree
  ## depth: the max_depth of directory-tree you want to generate, should > 0
  auto_category:
     enable: true  #true(default)
     depth:        #3(default)
     over_write: false 
  auto_title: false #enable auto title, it can auto fill the title by path
  auto_date: false #enable auto date, it can auto fill the date by time today
  force: false #enable force mode,in this mode, the plugin will ignore the cache, and calc the abbrlink for every post even it already had abbrlink.
```

------

# 本地搜索功能

插件名及链接地址：[hexo-generator-searchdb](https://github.com/theme-next/hexo-generator-searchdb)

Git Bash 输入：

```
npm install hexo-generator-searchdb --save
```

站点config.yml添加：

```
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```

------

# 加密浏览博文

插件名及链接地址：[hexo-blog-encrypt](https://github.com/D0n9X1n/hexo-blog-encrypt)

Git Bash 输入：

```
npm install hexo-blog-encrypt --save
```

站点config.yml添加：

```
# Security
encrypt: # hexo-blog-encrypt
  silent: true
  abstract: 本文加密，请输入密码以查看！
  message: 当前文章暂不对外可见，请输入密码后查看！
  tags:
  - {name: private, password: hello}
  wrong_pass_message: 密码错误，请重新输入！
  wrong_hash_message: 抱歉, 当前文章不能被校验, 不过您还是可以看看解密后的内容！
```

------

# 静态看板娘

插件名及链接地址：[hexo-helper-live2d](https://github.com/EYHN/hexo-helper-live2d)

看板娘模板：

- `live2d-widget-model-chitose`
- `live2d-widget-model-epsilon2_1`
- `live2d-widget-model-gf`
- `live2d-widget-model-haru/01` (use `npm install --save live2d-widget-model-haru`)
- `live2d-widget-model-haru/02` (use `npm install --save live2d-widget-model-haru`)
- `live2d-widget-model-haruto`
- `live2d-widget-model-hibiki`
- `live2d-widget-model-hijiki`
- `live2d-widget-model-izumi`
- `live2d-widget-model-koharu`
- `live2d-widget-model-miku`
- `live2d-widget-model-ni-j`
- `live2d-widget-model-nico`
- `live2d-widget-model-nietzsche`
- `live2d-widget-model-nipsilon`
- `live2d-widget-model-nito`
- `live2d-widget-model-shizuku`
- `live2d-widget-model-tororo`
- `live2d-widget-model-tsumiki`
- `live2d-widget-model-unitychan`
- `live2d-widget-model-wanko`
- `live2d-widget-model-z16`

根据以上模板挑选并Git Bash 输入：

```
npm install live2d-widget-model-您选择的模板名 --save
```

Git Bash 输入：

```
npm install hexo-helper-live2d --save
```

站点config.yml添加：

```
live2d:
  enable: true    # 是否启用看板娘
  scriptFrom: local    # 默认
  pluginRootPath: live2dw/    # 插件在站点上的根目录(相对路径)
  pluginJsPath: lib/    # 脚本文件相对与插件根目录路径
  pluginModelPath: assets/    # 模型文件相对与插件根目录路径
  # scriptFrom: jsdelivr    # jsdelivr CDN
  # scriptFrom: unpkg    # unpkg CDN
  # scriptFrom: https://cdn.jsdelivr.net/npm/live2d-widget@3.x/lib/L2Dwidget.min.js    # 你的自定义 url
  tagMode: false # 标签模式, 是否仅替换 live2d tag标签而非插入到所有页面中
  debug: false # 调试, 是否在控制台输出日志
  model:
    use: live2d-widget-model-z16 # 根据自己选择并下载的模板改名！！！
    scale: 1
    hHeadPos: 0.5
    vHeadPos: 0.618
  display:
    superSample: 2
    width: 200	# 宽
    height: 400	# 高
    position: left    # 左侧还是右侧
    hOffset: 0
    vOffset: -10    # 距底部距离
  mobile:
    show: false    # 手机端是否可见，建议不可见否则可能影响阅读
    scale: 0.5
  react:
    opacityDefault: 0.7
    opacityOnHover: 0.2
```

------

# 一键部署博客

插件名及链接地址：[hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)

Git Bash 输入：

```
npm install hexo-deployer-git --save
```

站点config.yml配置：

```
deploy:
  type: git
  repo: <仓库链接> # 可以是 https 链接也可以是 git 链接
  branch: [分支] # GitHub 的网站分支为 gh-pages，其他网站可能有所不同
  message: [message] # 默认是 Site updated: {{ now('YYYY-MM-DD HH:mm:ss') }}

```

------

# 统计博文字数和阅读时长

插件名及链接地址：[hexo-symbols-count-time](https://github.com/theme-next/hexo-symbols-count-time)

Git Bash 输入：

```
npm install hexo-symbols-count-time --save
```

站点config.yml添加：

```
symbols_count_time:
  symbols: true
  time: true
  total_symbols: true
  total_time: true
  exclude_codeblock: false
  awl: 4
  wpm: 275
  suffix: "mins."

```

------

# sitemap浏览器爬取博客

插件名及链接地址：[hexo-generator-sitemap](https://github.com/hexojs/hexo-generator-sitemap)

Git Bash 输入：

```
npm install hexo-generator-sitemap --save
```

站点config.yml添加：

```
# hexo-generator-sitemap
## https://github.com/hexojs/hexo-generator-sitemap
sitemap:
  path: sitemap.xml
  # template: ./sitemap_template.xml
  rel: true
  tags: false
  categories: false
```

具体参考：https://ywang-wnlo.github.io/posts/abac0c46

------

# 鼠标左键点击爱心特效

Blog文件夹 -> themes -> next（你的主题名文件夹） -> source -> js

在js文件夹下新建一个“love.js”，并存入以下内容保存。

```js
(function (window, document, undefined) {
    var hearts = [];
    window.requestAnimationFrame = (function () {
        return window.requestAnimationFrame ||
            window.webkitRequestAnimationFrame ||
            window.mozRequestAnimationFrame ||
            window.oRequestAnimationFrame ||
            window.msRequestAnimationFrame ||
            function (callback) {
                setTimeout(callback, 1000 / 60);
            }
    })();
    init();

    function init() {
        css(
            ".heart{width: 10px;height: 10px;position: fixed;background: #f00;transform: rotate(45deg);-webkit-transform: rotate(45deg);-moz-transform: rotate(45deg);}.heart:after,.heart:before{content: '';width: inherit;height: inherit;background: inherit;border-radius: 50%;-webkit-border-radius: 50%;-moz-border-radius: 50%;position: absolute;}.heart:after{top: -5px;}.heart:before{left: -5px;}"
        );
        attachEvent();
        gameloop();
    }

    function gameloop() {
        for (var i = 0; i < hearts.length; i++) {
            if (hearts[i].alpha <= 0) {
                document.body.removeChild(hearts[i].el);
                hearts.splice(i, 1);
                continue;
            }
            hearts[i].y--;
            hearts[i].scale += 0.004;
            hearts[i].alpha -= 0.013;
            hearts[i].el.style.cssText = "left:" + hearts[i].x + "px;top:" + hearts[i].y + "px;opacity:" + hearts[i]
                .alpha + ";transform:scale(" + hearts[i].scale + "," + hearts[i].scale +
                ") rotate(45deg);background:" + hearts[i].color;
        }
        requestAnimationFrame(gameloop);
    }

    function attachEvent() {
        var old = typeof window.onclick === "function" && window.onclick;
        window.onclick = function (event) {
            old && old();
            createHeart(event);
        }
    }

    function createHeart(event) {
        var d = document.createElement("div");
        d.className = "heart";
        hearts.push({
            el: d,
            x: event.clientX - 5,
            y: event.clientY - 5,
            scale: 1,
            alpha: 1,
            color: randomColor()
        });
        document.body.appendChild(d);
    }

    function css(css) {
        var style = document.createElement("style");
        style.type = "text/css";
        try {
            style.appendChild(document.createTextNode(css));
        } catch (ex) {
            style.styleSheet.cssText = css;
        }
        document.getElementsByTagName('head')[0].appendChild(style);
    }

    function randomColor() {
        return "rgb(" + (~~(Math.random() * 255)) + "," + (~~(Math.random() * 255)) + "," + (~~(Math.random() * 255)) +
            ")";
    }
})(window, document);
```



再到	themes -> next（你的主题名文件夹） -> layout

打开“_layout.swig”文件夹，在最后的 body 之前添加：

```js
<!-- 页面点击小红心 -->
<script type="text/javascript" src="/js/love.js"></script>
```

