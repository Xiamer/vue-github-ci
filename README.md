<!--
 * @Author: xiaoguang_10@qq.com
 * @LastEditors: xiaoguang_10@qq.com
 * @Date: 2020-12-29 15:53:37
 * @LastEditTime: 2021-01-05 10:22:22
-->
# hello-world

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).


## deploy configure

### 创建阿里云密钥对

* 请参考创建[SSH密钥对](https://www.alibabacloud.com/help/zh/doc-detail/51793.htm)和[绑定SSH密钥对](https://www.alibabacloud.com/help/zh/doc-detail/51796.htm?spm=a2c63.p38356.879954.9.cf992580IYf2O7#concept-zzt-nl1-ydb) ，将你的 ECS 服务器实例和密钥绑定，然后将私钥保存到你的电脑（例如保存在 ecs.pem 文件）。
* 打开你要部署到阿里云的 Github 项目，点击 setting->secrets。
* secret 名称为 **SERVER_SSH_KEY**，并将刚才的阿里云密钥填入内容。
* 重启 ECS 服务器实例。
  
### 在你项目下建立 .github\workflows\main.yml 

```yml
name: Build app and deploy to aliyun

on: # 监听 main 分支上的 push 事件
  push:
    branches:
      - main

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      # 执行npm脚本打包项目
      - name: Install and Build
        run: |
          npm install
          npm run build

      - name: Deploy
        uses: easingthemes/ssh-deploy@v2.1.5
        env: 
          SSH_PRIVATE_KEY: ${{ secrets.SERVER_SSH_KEY }}
          ARGS: '-rltgoDzvO --delete'
          SOURCE: dist/ # 这是要复制到阿里云静态服务器的文件夹名称
          REMOTE_HOST: '39.105.150.167' # 阿里云公网地址
          REMOTE_PORT: '22'
          REMOTE_USER: root # 阿里云登录后默认为 root 用户，并且所在文件夹为 root
          TARGET: /www/m/vue-ci # 打包后的 dist 文件夹将放在 **** （对应 nginx 映射的地址）
```


## reference resources
* [ssh-deplooy](https://github.com/easingthemes/ssh-deploy)
* [ruanyifeng github actions](http://www.ruanyifeng.com/blog/2019/09/getting-started-with-github-actions.html)
* [github-action](https://docs.github.com/cn/free-pro-team@latest/actions/reference/workflow-syntax-for-github-actions)
* [Jenkins、Github Actions](https://juejin.cn/post/6887751398499287054#heading-9)
