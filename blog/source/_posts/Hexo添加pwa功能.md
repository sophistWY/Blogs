---
title: Hexo添加pwa功能

date: 2022-7-21 11:31:43

tags:

  - 方法总结

categories:

  - Hexo

---



##  Hexo添加pwa功能

<!--more-->



#### 一.什么是PWA

PWA（Progressive Web App）是一种理念，使用多种技术来增强web app的功能，可以让网站的体验变得更好，能够模拟一些原生功能，比如通知推送。在移动端利用标准化[框架](https://so.csdn.net/so/search?q=框架&spm=1001.2101.3001.7020)，让网页应用呈现和原生应用相似的体验。

参阅： https://baike.baidu.com/item/PWA/22378897?fr=aladdin



#### 二.安装PWA插件

```shell
npm install hexo-pwa --save
```



####  三.添加manifest.json和sw.js放在根目录

###### 1.[manifest.json](HEXO添加PWA功能/manifest.json) 

```json
{
  "name": "SophistWY",
  "short_name": "SophistWY",
  "theme_color": "#fff",
  "background_color": "#49b1f5",
  "display": "standalone",
  "scope": "/",
  "start_url": "/",
  "icons": [
    {
      "src": "/images/icons/icon-512x512.png",
      "sizes": "96x96",
      "type": "image/png"
    },
    {
      "src": "/images/icons/icon-512x512.png",
      "sizes": "128x128",
      "type": "image/png"
    },
    {
      "src": "/images/icons/icon-512x512.png",
      "sizes": "144x144",
      "type": "image/png"
    }
  ],
  "splash_pages": null
}

```

##### 2. [sw.js](HEXO添加PWA功能/sw.js) 

```javascript
importScripts('https://g.alicdn.com/kg/workbox/3.3.0/workbox-sw.js');

if (workbox) {
    workbox.setConfig({ modulePathPrefix: 'https://g.alicdn.com/kg/workbox/3.3.0/' });

    workbox.precaching.precache(['/', '/index.html']);

    workbox.routing.registerRoute(new RegExp('^https?://sophistwy.com/?$'), workbox.strategies.networkFirst());

    workbox.routing.registerRoute(new RegExp('.*.html'), workbox.strategies.networkFirst());

    workbox.routing.registerRoute(new RegExp('.*.(?:js|css|jpg|png|gif)'), workbox.strategies.staleWhileRevalidate());

}
```



#### 三.修改根目录下 _config.yml 关于PWA的配置

将下面内容复制添加末尾即可：

```yaml
pwa:
  manifest:
    path: /manifest.json
    body:
      "name": "sophistwy"
      "short_name": "sophist"
      "theme_color": "rgba(203,7,83,0.86)"
      "background_color": "#FAFAFA"
      "display": "standalone"
      "Scope": "/"
      "start_url": "/"
      icons:
        - src: /images/icons/icon-512x512.png
          sizes: 144x144
          type: image/png
        - src: /images/icons/icon-512x512.png
          sizes: 128x128
          type: image/png
        - src: /images/icons/icon-512x512.png
          sizes: 96x96
          type: image/png
  serviceWorker:
    path: /sw.js
    preload:
      urls:
        - /
      posts: 5
    opts:
      networkTimeoutSeconds: 5
    routes:
      - pattern: !!js/regexp /hm.baidu.com/
        strategy: networkOnly
      - pattern: !!js/regexp /.*\.(js|css|jpg|jpeg|png|gif)$/
        strategy: cacheFirst
      - pattern: !!js/regexp /\//
        strategy: networkFirst
  priority: 5
```



#### 四.增加图片素材

source目录下新增 images/icons 放入指定尺寸的icon

```
source/images/icons/icon-512x512.png
```



#### 五. hexo cl && hexo g && hexo s

![pic](HEXO添加PWA功能/pic.png)

#### 大功告成~



##### 参考

https://blog.csdn.net/victoryxa/article/details/104286431