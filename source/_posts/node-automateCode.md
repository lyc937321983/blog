---
title: Node打包dist代码到服务器
date: 2024-07-03 14:14:29
tags: node
comment: 'valine'
categories: 
- node
---

# node打包dist代码到服务器

### 1、node代码

```js
const chalk = require('chalk')
const path = require('path')
const fs = require('fs')
const Client = require('ssh2-sftp-client')
const sftp = new Client()
const envConfig = require('./env.config')

const defalutConfig = {
  port: '80', // 端口号
  username: 'root', // 用户名
  password: 'root123', // 密码
  localStatic: './dist.tar.gz',
}

const config = {
  ...defalutConfig,
  host: envConfig.host,
  remoteStatic: envConfig.remoteStatic,
}

const error = chalk.bold.red
const success = chalk.bold.green
function upload(config, options) {
  if (!fs.existsSync('./dist') && !fs.existsSync(options.localStatic)) {
    return
  }
  // 标志上传dist目录
  let isDist = false
  sftp
    .connect(config)
    .then(() => {
      // 判断gz文件存在时 上传gz 不存在时上传dist
      if (fs.existsSync(options.localStatic)) {
        return sftp.put(options.localStatic, options.remoteStatic)
      } else if (fs.existsSync('./dist')) {
        isDist = true
        return sftp.uploadDir('./dist', options.remoteStatic.slice(0, -12))
      }
    })
    .then(() => {
      sftp.end()
      if (!isDist) {
        const { Client } = require('ssh2')
        const conn = new Client()
        conn
          .on('ready', () => {
            // 远程解压
            const remoteModule = options.remoteStatic.replace('dist.tar.gz', '')
            conn.exec(
              `cd ${remoteModule};tar xvf dist.tar.gz`,
              (err, stream) => {
                if (err) throw err
                stream
                  .on('close', (code) => {
                    code === 0
                    conn.end()
                    // 解压完成 删除本地文件
                    fs.unlink(options.localStatic, (err) => {
                      if (err) throw err
                    })
                  })
                  .on('data', (data) => {})
              }
            )
          })
          .connect(config)
      }
    })
    .catch((err) => {
      sftp.end()
    })
}

// 上传文件
upload(config, {
  localStatic: path.resolve(__dirname, config.localStatic), // 本地文件夹路径
  remoteStatic: config.remoteStatic, // 服务器文件夹路径器
})
```

### 2、上传命令

```json
"build":"vue-cli-service build",
"zip":"cd dist && tar -zcvf ../dist.tar.gz css js index.html",
"upload": "node upload",
"deploy":"npm run build && && npm run zip && npm run upload"
```

