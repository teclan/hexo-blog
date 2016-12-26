---
title: AngularJS 常见错误及解决办法
date: 2016-12-26 09:40:12
tags: [AngularJS]
---

### 1、启动时错误
 当执行 npm start (或 cnpm start )时，出现以下错误时
 ```
npm ERR! Linux 4.4.0-51-generic
npm ERR! argv "/home/dev/Apps/node-v6.9.2/bin/node" "/home/dev/Apps/node-v6.9.2/lib/node_modules/cnpm/node_modules/.bin/npm" "--userconfig=/home/dev/.cnpmrc" "--disturl=https://npm.taobao.org/mirrors/node" "--registry=https://registry.npm.taobao.org" "start"
npm ERR! node v6.9.2
npm ERR! npm  v3.10.10
npm ERR! code ELIFECYCLE
npm ERR! angular-quickstart@1.0.0 start: `tsc && concurrently "tsc -w" "lite-server" `
npm ERR! Exit status 2
npm ERR!
npm ERR! Failed at the angular-quickstart@1.0.0 start script 'tsc && concurrently "tsc -w" "lite-server" '.
npm ERR! Make sure you have the latest version of node.js and npm installed.
npm ERR! If you do, this is most likely a problem with the angular-quickstart package,
npm ERR! not with npm itself.
npm ERR! Tell the author that this fails on your system:
npm ERR!     tsc && concurrently "tsc -w" "lite-server"
npm ERR! You can get information on how to open an issue for this project with:
npm ERR!     npm bugs angular-quickstart
npm ERR! Or if that isn't available, you can get their info via:
npm ERR!     npm owner ls angular-quickstart
npm ERR! There is likely additional logging output above.
npm WARN Local package.json exists, but node_modules missing, did you mean to install?

npm ERR! Please include the following file with any support request:
npm ERR!     /home/dev/workspace/teclan/Angular/angularjs-learning/npm-debug.log

 ```
 将 package.json 中的
 ```
 "start": "tsc && concurrently \"npm run tsc:w\" \"npm run lite\",
 ```
 改成
 ```
 "start": "concurrently \"npm run tsc:w\" \"npm run lite\" ",
 ```
