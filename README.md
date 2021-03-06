<p align="center">
  <img src="https://www.fdevops.com/wp-content/uploads/2020/07/1595066253-ferry_logo_meitu_1.png">
</p>


<p align="center">
  <a href="https://github.com/lanyulei/ferry">
    <img src="https://www.fdevops.com/wp-content/uploads/2020/07/1595067271-badge.png">
  </a>
  <a href="https://github.com/lanyulei/ferry">
    <img src="https://www.fdevops.com/wp-content/uploads/2020/07/1595067272-apistatus.png" alt="license">
  </a>
    <a href="https://github.com/lanyulei/ferry">
    <img src="https://www.fdevops.com/wp-content/uploads/2020/07/1595067269-donate.png" alt="donate">
  </a>
</p>

## 基于Gin + Vue + Element UI前后端分离的工单系统

**流程中心**

通过灵活的配置流程、模版等数据，非常快速方便的生成工单流程，通过对流程进行任务绑定，实现流程中的钩子操作，未兼容更多的通知方式，因此未在代码中直接写死通知方式，可通过任务绑定实现处理通知。

**系统管理**

基于casbin的RBAC权限控制，借鉴了go-admin项目的前端权限管理，可以在页面对API、菜单、页面按钮等操作，进行灵活且简单的配置。

演示demo: [http://fdevops.com:8001/#/dashboard](http://fdevops.com:8001/#/dashboard)

账号密码：admin/123456

文档: [https://www.fdevops.com/docs/ferry](https://www.fdevops.com/docs/ferry-tutorial-document/introduction)

```
需注意，因有人恶意删除演示数据，将可删除的数据全都删除了，因此演示的Demo上已经将删除操作的隐藏了。

但是直接在Github或者Gitee下载下来的代码是完整的，请放心。

如果总是出现此类删除数据，关闭演示用户的情况的话，可能考虑不在维护demo，仅放置一些项目截图。

请大家一起监督。
```

## 安装部署

```
go >= 1.14
vue >= 2.6
npm >= 6.14
```

#### 本地二次开发

后端

```
# 1. 获取代码
git clone https://github.com/lanyulei/ferry.git
or
git clone https://gitee.com/yllan/ferry.git

# 2. 进入工作路径
cd ./ferry

# 3. 修改配置 ferry/config/settings.dev.yml
vi ferry/config/settings.dev.yml

# 配置信息注意事项：
1. 程序的启动参数
2. 数据库的相关信息
3. 日志的路径

# 4. 初始化数据库
go run main.go init -c=config/settings.dev.yml

# 5. 启动程序
go run main.go server -c=config/settings.dev.yml
```

前端

```
# 1. 获取代码
git clone https://github.com/lanyulei/ferry_web.git
or
git clone https://gitee.com/yllan/ferry_web.git

# 2. 进入工作路径
cd ./ferry_web

# 3. 安装依赖
npm config set registry https://registry.npm.taobao.org
npm install

# 4. 启动程序
npm run dev
```


#### 上线部署

后端

```
# 1. 进入到项目路径下进行交叉编译（centos）
env GOOS=linux GOARCH=amd64 go build

更多交叉编译内容，请访问 https://www.fdevops.com/2020/03/08/go-locale-configuration

# 2. config目录上传到项目根路径下，并确认配置信息是否正确
vi ferry/config/settings.yml

# 配置信息注意事项：
1. 程序的启动参数
2. 数据库的相关信息
3. 日志的路径

# 3. 创建日志路径及静态文件经历
mkdir -p log static/uploadfile

# 4. 初始化数据
./ferry init -c=config/settings.yml

# 5. 启动程序，推荐通过"进程管理工具"进行启动维护
nohup ./ferry server -c=config/settings.yml > /dev/null 2>&1 &
```

前端

```
# 1. 编译
npm run build:prod

# 2. 将dist目录上传至项目路径下即可。
mv dist web

# 3. nginx配置，根据业务自行调整即可
  server {
    listen 8001; # 监听端口
    server_name localhost; # 域名可以有多个，用空格隔开
  
    #charset koi8-r;
  
    #access_log  logs/host.access.log  main;
    location / {
      root /data/ferry/web;
      index index.html index.htm; #目录内的默认打开文件,如果没有匹配到index.html,则搜索index.htm,依次类推
    }
  
    #ssl配置省略
    location /api {
      # rewrite ^.+api/?(.*)$ /$1 break;
      proxy_pass http://127.0.0.1:8002; #node api server 即需要代理的IP地址
      proxy_redirect off;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
  
    # 登陆
    location /login {
      proxy_pass http://127.0.0.1:8002; #node api server 即需要代理的IP地址
    }
  
    # 刷新token
    location /refresh_token {
      proxy_pass http://127.0.0.1:8002; #node api server 即需要代理的IP地址
    }
  
    # 接口地址
    location /swagger {
      proxy_pass http://127.0.0.1:8002; #node api server 即需要代理的IP地址
    }
  
    # 后端静态文件路径
    location /static/uploadfile {
      proxy_pass http://127.0.0.1:8002; #node api server 即需要代理的IP地址
    }
  
    #error_page  404              /404.html;    #对错误页面404.html 做了定向配置
  
    # redirect server error pages to the static page /50x.html
    #将服务器错误页面重定向到静态页面/50x.html
    #
    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
      root html;
    }
  }
```

## 交流群

加群条件是需给项目一个star，不需要您费多大的功夫与力气，一个小小的star是作者能维护下去的动力。

如果您只是使用本项目的话，您可以在群内提出您使用中需要改进的地方，我会尽快修改。

如果您是想基于此项目二次开发的话，您可以在群里提出您在开发过程中的任何疑问，我会尽快答复并讲解。

群里只要不说骂人、侮辱人之类人身攻击的话，您就可以畅所欲言，有bug我及时修改，使用中有不懂的，我会及时回复，感谢。

<p>
  <img src="https://www.fdevops.com/wp-content/uploads/2020/07/2020072209114938.png">
</p>

QQ群：1127401830

[兰玉磊的技术博客](https://www.fdevops.com/)

## 特别感谢
[go-amdin # 不错的后台开发框架](https://github.com/wenjianzhang/go-admin.git)

[vue-element-admin # 不错的前端模版框架](https://github.com/PanJiaChen/vue-element-admin)

[vue-form-making # 表单设计器，开源版本比较简单，如果有能力的话可以自己进行二次开发 ](https://github.com/GavinZhuLei/vue-form-making.git)

[wfd-vue # 流程设计器](https://github.com/guozhaolong/wfd-vue)

[machinery # 任务队列](https://github.com/RichardKnop/machinery.git)

等等...

## 打赏

> 如果您觉得这个项目帮助到了您，您可以请作者喝一杯咖啡表示鼓励:

<img class="no-margin" src="https://www.fdevops.com/wp-content/uploads/2020/07/1595075890-81595075871_.pic_hd.png"  height="200px" >

## License

开源不易，请尊重作者的付出，感谢。

[MIT](https://github.com/lanyulei/ferry/blob/master/LICENSE)

Copyright (c) 2020 lanyulei
