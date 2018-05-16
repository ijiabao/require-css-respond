# require-css 兼容IE Respond

* 根据require-css, respond.js 修改而来，原CSS项目使用@import方式加载时，则IE-Respond.js不生效， 且动态加载css时，respond.js需要重载，否则也不生效。(原项目的respond.js必须在所有CSS加载完后再调用，这不符合require-css的意义)
* respond.js 源项目在 https://github.com/scottjehl/Respond
* require-css 源项目在 https://github.com/guybedford/require-css

## css.min.js
* require-css 使用此文件，兼容IE respond.js
* 因respond.js只认link标签，所以修改CSS加载方式仅为动态创建link标签， 且在加载成功后刷新`respond.js 或 respond.proxy.js`

## respond.proxy.js
* 需要处理CDN样式文件时, 为respond对象添加接口: `updateProxy(link)`
* 实例中使用 respond.min.js respond.proxy.js 二选一，前者不使用CDN文件，后者使用CDN

## 如果项目不加载CDN中的CSS文件，则使用原始respond.js即可
```
<head>
  <!-- 非动态加载的css样式 -->
  <link href="some/static/css">
  <!--[if lt IE 9]>
  <script src="libs/respond/respond.min.js" type="text/javascript"></script>
  <![endif]-->
  </head>
  <body>
  <script src="/js/require.js"></script>
  <script src="/js/config.js"></script>
  <script>
    // 正常使用 css!加载
    // requirejs(['css!some/path/file']);
  </script>
  </body>
```

## 如果项目使用CDN中的样式,则需把respond/respond.proxy.html置入CDN的目标域
```
<head>
  <link href="some/static/css">
  
  <!--[if lt IE 9]>
  <link href="http://your-cdn/respond/respond-proxy.html" id="respond-proxy" rel="respond-proxy" />
  <script id="proxy-js" src="libs/respond/respond.proxy.min.js" type="text/javascript"></script>
  <![endif]-->
</head>
<body>
    <script src="/js/require.js"></script>
    <script src="/js/config.js"></script>
    <script>
      // 正常使用 css!加载CDN、本地资源
      // requirejs(['css!your-cdn/path/file', 'css!your-site/path/file']);
    </script>
</body>
```

## 或者直接使用requirejs 加载 respond.js
```
// require-config.js

// for respond.proxy
window.RESPOND_PROXY_URL = 'your-cdn/respond/respond.proxy.html'; // 远程

requirejs.config({
  map:{
    "*": {"css":"cdn/or/site/css.min"}
  },
  paths:{
    'respond':'respond/respond.min',  // your cdn or local site, respond.min.js 本地或远程
    'respond.proxy':'respond/respond.proxy',  // respond.proxy.js 本地
  },
  shim:{
    // css 依赖于respond, 仅在IE<9时加载， 参见：https://github.com/ijiabao/require-ltie9
    'css': ['ltie9!respond']  // or respond.proxy (need RESPOND_PROXY_URL)
  }
});

// 建议把css.min.js内容直接放在config.js 或 requirejs 尾部，而不必加载此文件
define('css', function(){
  ...复制css.min.js闭包内容
});

requirejs(['ltie9!respond']); // load once in config

// end require-config.js

// 实例页面：
requirejs(['css!some/path']);

```

## 注：html5shiv.min.js不可使用requirejs加载，必须放在body前
