---
title: node作业
date: 2019-05-09 10:10:46
tags: [Node]
---

## 1. 文件服务器


[在浏览器输入`http://localhost:8080`时，会返回404，原因是程序识别出HTTP请求的不是文件，而是目录。请修改file_server.js，如果遇到请求的路径是目录，则自动在目录下依次搜索index.html、default.html，如果找到了，就返回HTML文件的内容。](https://www.liaoxuefeng.com/wiki/1022910821149312/1023025830950720#0)


`file_server.mjs`

```js
/* 
target: file system server
for example: input a url, then show file content of the target path
0. 启动服务(http)
1. 读取输入, 解析拼出path(url, path)
2. 读取本地文件, 放入response(fs=> stream, pipe)
 */

'use strict';

import fs from 'fs';
import url from 'url';
import http from 'http';
import path from 'path';

const root = path.resolve(process.argv[2] || '.');
console.log(`Static root dir: ${root}`);

function path2Type(path) {
  return new Promise((resolve, reject) => {
    fs.stat(path, (err, stats) => {
      if (!err) {
        if (stats.isFile()) {
          resolve('file')
        } else if (stats.isDirectory()) {
          resolve('dir')
        } else {
          resolve('other')
        }
      } else {
        resolve('error')
      }
    })  
  })
}

const handleErr = (response, request) => {
  console.log(`404 ${request.url}`);
  response.writeHead(404);
  response.end('404 Not Found');
}
const handleSucc = (filepath, response, request) => {
  console.log(`200 ${request.url}`);
  response.writeHead(200);
  fs.createReadStream(filepath).pipe(response);
}

const filterUsefulHtmlFromDir = async (dir) => {
  const newIndexfilepath = path.join(dir, 'index.html')
  const newDefaultfilepath = path.join(dir, 'default.html')

  const newIndexType = await path2Type(newIndexfilepath)

  if (newIndexType === 'file') {
    return newIndexfilepath;
  } else {
    const newDefaultType = await path2Type(newDefaultfilepath)
    if (newDefaultType === 'file') {
      return newDefaultfilepath;
    } else {
      return null;
    }
  }
}

const handler = async (filepath, request, response) => {
  const type = await path2Type(filepath)
  switch(type) {
    case 'err':
      handleErr(response, request)
      break;
    case 'file':
      handleSucc(filepath, response, request)
      break;
    case 'dir':
      const usefulFile = await filterUsefulHtmlFromDir(filepath);
      if (usefulFile) {
        handleSucc(usefulFile, response, request);
      } else {
        handleErr(response, request)
      }
      break;
    case 'other':
      handleErr(response, request)
      break;
    default:
      break;
  }
}


const server = http.createServer((request, response) => {
  const { pathname } = url.parse(request.url);
  const filepath = path.join(root, pathname);
  handler(filepath, request, response);
})

server.listen(8080);

console.log('Server is running at http://127.0.0.1:8080/');

```

运行(*es6语法的node模块需要1. 后缀改为mjs; 2. 由于是一个实验功能, 要加参数`--experimental-modules`* )

```bash
node --experimental-modules file_server.mjs
```