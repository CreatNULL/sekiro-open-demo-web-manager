# sekiro-open-demo 管理UI

## 一、描述

​	不想逆js代码了，想使用 sekiro 直接调用 js 文件中的函数 ，但是免费的版本不支持 wss，使用的时候网上搜了半天，配置繁琐，为了简化流程，故开发本项目，实现通过 web 管理本地证书生成、nginx管理、sekiro服务管理、一键生成油猴脚本、调用 sekior 获取结果。有bug，但是不怎么影响。

​	默认第一个注册的为管理员，管理员可以关闭注册功能。

 	注册服务主要使用开源项目 https://github.com/winsw/winsw，
        证书的管理使用开源项目 https://github.com/FiloSottile/mkcert


	
## 二、开发使用环境

工具 - idea

nodejs 版本 - v20.15.1

python版本  - 3.8.10



## 三、注意

- **<span style="color: red">会请求管理员权限运行</span>，用来注册服务、安装CA**

- **只能部署在Windows 上**

- **仅限本地使用（ssl证书的原因）**

- **流程**

  - **1.先安装python依赖**
  - **2. 创建数据库**
  - **3.修改配置文件中的数据库账号密码**
  - **4. 启动 python脚本**
  - **5. 安装本地CA（第一次需要,后面就不用了)，生成 ssl 证书**
  - **6.  启动nginx**
  - **7. 启动sekiro**
  - **8. 生成油猴脚本, 放到油猴插件，保存，刷新页面 （<span style="color: red">请使用谷歌浏览器，Edge似乎不行</span>）**
  - **9. 调用 API查看结果**

- **注意使用 127.0.0.1:5000 访问，而不是 localhost:5000, 不然好像登录不成功**

- **使用 wss 的时候，需要添加证书中添加 ip 127.0.0.1**

- **如果想改改前端，nodejs版本不要太低，不然编译代码报错 TypeError: crypto$2.getRandomValues is not a function**

- 注册功能可以关闭，点了勾选，但是你看不见，哈哈，一个小bug，但是点击提交即可。可以正常开关注册功能 , 注册 页面直接访问是不行的，必须从登录页面跳转进入，否则显示未开放注册。

  
  
  ## 演示示例
  
  <video src="./../../../../../BandicamPortable-屏幕录制/录屏/使用示例.mp4"></video>
  坏了视频丢失了，算了截屏一些好了
  ![image](https://github.com/user-attachments/assets/69373d7f-7dfa-4a74-b887-0953a9a085b0)

  ![image](https://github.com/user-attachments/assets/e43cf2fe-5610-413c-8bc1-bb06b267712e)

  ![image](https://github.com/user-attachments/assets/9fadc2a0-ef23-463a-9822-aa763da6ccdc)

  ![image](https://github.com/user-attachments/assets/10fc12de-d4f0-4c95-838b-b5728c213361)

  ![image](https://github.com/user-attachments/assets/2a7f5058-fff8-44bf-8f76-302b6c20a9db)

  ![image](https://github.com/user-attachments/assets/74760ac3-2b3c-4987-9ffa-259637076869)

  ![image](https://github.com/user-attachments/assets/ce632f52-a70f-4185-8743-4f6066040bd7)

  ![image](https://github.com/user-attachments/assets/c426d9d7-430b-4b16-bd3f-c41e33bbea2b)

  ![image](https://github.com/user-attachments/assets/02f232f7-c68f-413d-bfc3-1a5ef0a30e43)

  ![image](https://github.com/user-attachments/assets/db3b8879-ee51-4bfb-a87f-1731f6a45941)


## 四、默认占用端口

- 80 、443 - Nginx
- 5612 - sekiro
- 5000 - flask
- 3306  - MySQL

## 五、项目结构

1. 通过 vue 和 flask (来实现项目，前后端独立开发 (前端: front_end_services, 后端: back_end_service)
2. 数据库选择 mysql （我用小皮的哈哈，安装简单一点）

### vue

```text
1. 分为两个 Login 页面 和 Home 页面
2. Home 实现登录展示站点icp信息等
3. Home 页面下分为多个子组件分别为:
   (1). 主页数据面板 - 主页
   (2). 管理员管理用户页面 - 管理用户
      1. 修改用户的密码、密码长度等
      2. 创建用户
   (3). 管理员系统配置设置 - 系统设置 
   (4). 各个用户中心 - 用户中心
   (5). 证书管理 - 注册本地CA，生成Nginx使用证书
   (6). Nginx管理 - Nginx 启动关闭配置修改
   (7). Sekiro管理 - Sekiro 启动关闭配置修改
   (8). 生成油猴脚本
   (9). Sekiro Api - 查看调用api执行结果
```

```text
src
  - components/
    - Home.vue   # 家页面父组件
    - Home  # 家目录子组件 
      - HomePage.vue  # 用户页面
      - Manager  # 管理员管理用户 - 子组件
        - CreateUser.vue  # 创建用户
        - QueryManagement.vue # 查询用户，编辑/删除
      - UserCenter # 个人用户中心子组件 - 子组件
      	- UserAvatar.vue # 用户头像子组件
      	- UserLoginSet.vue # 用户登录子组件
      - CertificateManager.vue  # 创建注册本地CA、生成 Nginx使用的 SSL证书
      - CreateTampermonkey.vue  # 创建使用的油猴脚本
      - CustomizeMessage.vue # 展示重要信息
      - Dashboard.vue # 主页 没有啥用
      - ManageUser.vue  # 管理员管理用户 - 父组件
      - NginxManager.vue  # Nginx管理页面
      - QueryData.vue  # 查询数据
      - SekiroApi.vue # Sekiro API调用页面
      - SekiroManager.vue # sekiro 管理页面
      - SystemSettings.vue  # 系统设置
      - UserCenter.vue  # 用户个人中心 - 父组件
    - Login.vue # 登录页面
```

### flask

```text
mkcert # GitHub下载的 mkcert
	- mkcerts
nginx  # 下载的 nginx 
	- certs # 自己创建的目录保存 ssl证书
	- conf
		- nginx.conf  # 自己的配置，完整的配置在下面
sekiro-open-demo # github 下载的，然后自己编译
	- 
static # 静态文件存储的地方， css、js （flask的默认，除非手动指定其他目录)
	- css #vue编译后放入
	- js  # vue编译后放入
	- uploads  # 存储文件上传后的目录, img -> avatar -> 用户的名称 -> 存放对应的头像
	- favicon.ico  # 网站图标
templates  # html 模板存放的地方 （flask的默认，除非手动指定其他目录)
	- index.html # vue编译后放入，不过需要略作修改js路径和css路径例如：    <script type="module" crossorigin src="{{url_for('static', filename='js/app.js')}}"></script>	
utils
	- service
		- servicemanager.py  # 用来管理（注册、删除、启动、关闭、重启服务的
	- runas
		- adminstrator.py # 以管理员身份运行 flask 
views  # 用
    - db_init.py  # 调试使用api，数据库的创建和初始化
    - systemset.py  # 管理系统的配置，文件上传类型限制，站点标题设置，版权信息设置，站点icp设置，密码长度设置
    - user.py  # 用户相关，用户的注册，删除，修改昵称，修改密码等操作均在里面
    - util.py # 公共的工具函数，文件上传的，确保登录的，获取当前登录的用户信息 等
    - mkcert # mkcert 调用
    	- platform_mkcert.py
    - nginx # nginx 管理
    	platform_nginx.py
    - sekiro # sekiro 管理
    	platform_sekiro.py
app.py  # 注册蓝图、创建主页路由
config.py  # flask 配置
models.py # 对数据库的表映射
winsw.exe  # github 下载的管理Windows服务的
```

![image-20250216133719981](./image-20250216133719981.png)



## 六、项目依赖

### 1. vue

```cmd
npm install element-plus
npm install vue-ele-form
npm install axios
npm install vue-router@4  # 路由功能
npm install @form-create/element-ui
npm install @ag-grid-community/core@latest
npm install @ag-grid-community/vue3@latest 
npm uninstall element-ui
npm install element-plus
npm install vxe-table  # 表格显示等
npm install xe-utils
npm install core-js
npm install regenerator-runtime
npm install  @vxe-ui/plugin-export-xlsx@4.0.7 exceljs@4.2.1 xlsx # 导入导出xlsx
npm install vuex  # 
npm install monaco-editor  # 代码显示
```
### 2. python

```cmd
pip install flask
pip install flask-cors  # 跨域问题
pip install flask-sqlalchemy    # 数据库连接
pip install PyJWT   # 身份验证
pip install captcha  # 验证码 - 预留 -并未开放
pip install pymysql
```
### 3. MySQL

请先创建对应的数据库 例如: test_school，然后

```text
在 config.py 的
SQLALCHEMY_DATABASE_URI = 'mysql+pymysql://root:123456@localhost/test_school'
指定数据库名称，账号密码
```

## 七、开发遇到问题
### (1)、问题1 - vue 中无法使用 css 修改 input 的样式，

```text
有人用去掉 scoped 的方法解决，但这会污染全局样式，不可取。

使用 /deep/ 或者 >>> 解决
/deep/

.child /deep/ span {
*some prop
}
>>>

.child >>> span {
*some prop
}
```

### (2)、问题 2 flask + vue 部署后，报错

`Failed to load module script: Expected a JavaScript module script but the server responded with a MIME type of "text/html". Strict MIME type checking is enforced for module scripts per HTML spec`



![image-20250216115110596](./image-20250216115110596.png)

![image-20250216115252999](./image-20250216115252999.png)



找到解决: [flask+Vue(Vite)部署，js文件不能被正确解析解决方案_strict mime type checking is enforced for module s-CSDN博客](https://blog.csdn.net/u013661233/article/details/127894609)

添加代码

```
#更改js文件的返回头
@app.after_request
def changeHeader(response):
    disposition = response.get_wsgi_headers('environ').get(
        'Content-Disposition')  or ''#获取返回头文件名描述，如'inline; filename=index.562b9b5a.js'
    if disposition.rfind('.js') == len(disposition) - 3:
        response.mimetype = 'application/javascript'
    return response
```





## 八、开发备注

### (1)、修改服务器 IP （Nginx)

    1. 
        vue 中的 main.js 中 app.config.globalProperties.$apiUrl = 'http://127.0.0.1:5000/api'; 指定服务端api

![image-20250215225802480](./image-20250215225802480.png)

### (2)、vue不登陆，可访问后端

![image-20250215232612042](./image-20250215232612042.png)

### (3)、不同身份看到不同的功能

![image-20250215233229866](./image-20250215233229866.png)

### (4)、flask 上下文解释

    3. 
        flask 对于路由之外的，统一认为在flask上下文之外，所以这里使用的是from config import Config 然后，这样调用，而不是使用 curret_app['']。

![image-20250215232958478](./image-20250215232958478.png)



### (5)、 vue 新增图标

```
  vue中添加新的ico从阿里https://www.iconfont.cn 获取，
     1. 添加入库
     2. 点击购物车
     3. 添加到项目，没有则创建
     4. 添加到项目下载到本地选择 Font class
     5. 项目调用需要 iconfont <图标名称>
```

![image-20250215224920175](./image-20250215224920175.png)



![image-20250215225037546](./image-20250215225037546.png)



![image-20250215224829503](./image-20250215224829503.png)



![image-20250215225215797](./image-20250215225215797.png)



```
如果想要修改前缀，可以点击项目设置 iconfont
```

![image-20250215225314604](./image-20250215225314604.png)

![image-20250215225404301](./image-20250215225404301.png)

### (6)、 Vue 编译后放入flask

```
npm run build
```

![image-20250215233728074](./image-20250215233728074.png)

生成的在 dist 目录下

js 放入js目录

![image-20250215233922892](./image-20250215233922892.png)

css放入 css 目录

![image-20250215234006258](./image-20250215234006258.png)

index.html 放入 templates, 修改 index.html

![image-20250215234100436](./image-20250215234100436.png)

修改后：

![image-20250215234215857](./image-20250215234215857.png)

### (7)、其它

    1. 
        相关 api 公共函数放在 util.py 中 （文件上传等)

```text
2.
        app.py 中不要导入 models.py 否则可能会出现重复定义表的报错.
        
        原因:
            app 中导入 models， 
            app 需要导入视图中的蓝图，并注册蓝图
            视图中需要导入 models 中的表操纵数据库,
            这样会被判定，类似导致循环导入问题，被认为重复定义表模型，报错
```



## 九 、存在Bug

localhost:5000 登录报错

![image-20250216121302914](./image-20250216121302914.png)

127.0.0.1:5000 登录成功

![image-20250216121355940](./image-20250216121355940.png)

## 十、附件

### Nginx 配置

```
events {
    worker_connections  1024;
}

http{
upstream sekiro_business_netty {	# sekiro_business_netty 和下面的 pass 后面的值对应
  # sekiro 项目 地址
  server 127.0.0.1:5612;
}

server {
		    listen		 80;
        listen       443 default  ssl;  # 监听433端口
        keepalive_timeout 100;  # 开启keepalive 激活keepalive长连接，减少客户端请求次数

        ssl_certificate      ./certs/server.crt;   # server端证书位置
        ssl_certificate_key  ./certs/server.key;   # server端私钥位置

        ssl_session_timeout  10m;                    # session会话    10分钟过期

        ssl_ciphers  HIGH:!aNULL:!MD5;	# 定义了支持的 SSL 加密算法套件，这里选择了较高安全性的算法，并排除了一些不安全的算法（如 MD5 ）。
        ssl_prefer_server_ciphers  on;	# 表示在选择加密算法时，优先使用服务器支持的算法。

        server_name ~^.*sekiro.*$;		# 但凡包含了 sekiro 字符串的域名
		server_name *.com;
		server_name *.cn;
		server_name *.top;
		server_name *.gov;
		server_name *.org;
		server_name *.edu;
		server_name *.net;
		server_name *.vip;
		server_name *.tech;
		server_name *.xyz;
		server_name *.vip ;
		server_name *.biz;
		server_name *.info;
		server_name *.mobi;
		server_name *.cc;
		server_name *.co;
		server_name *.video;
		server_name *.site;
		server_name *.host;
		server_name *.store;
		server_name *.team;
		server_name *.plus;
		server_name *.host;
		server_name *.club;
		server_name *.icu;
		server_name *.cloud;
		server_name *.band;
		server_name *.fans;
		server_name *.group;
		server_name *.live;
		server_name *.space;
		server_name *.work;
		server_name *.zone;
		server_name *.games;
		server_name *.asia;
		server_name *.pro;
        charset utf-8;				# 设置响应的字符编码为 UTF-8 。

        location /business-demo {    # 后续获取加密参数时候用到的路由
          gzip on;						# 启用 Gzip 压缩
          gzip_min_length 1k;			# 只有当响应内容长度大于 1KB 时才进行压缩
          gzip_buffers 4 16k;			# 设置用于压缩的缓冲区数量和大小。
          gzip_http_version 1.0;		# 对 HTTP 1.0 的响应也进行压缩
          gzip_comp_level 2;			# 设置压缩级别为 2（1 到 9 之间，数字越大压缩程度越高，但处理时间也越长）。
          gzip_types application/json text/plain application/x-javascript text/css application/xml;	# 指定对这些类型的内容进行压缩。
          gzip_vary on;					# 响应头中添加 Vary: Accept-Encoding,以便客户端根据自身是否支持压缩来正确处理响应。
          proxy_read_timeout      500;	# 设置从后端服务器读取响应的超时时间为 500 秒。
          proxy_connect_timeout   300;	# 设置与后端服务器建立连接的超时时间为 300 秒。
          proxy_redirect          off;	# 禁止 Nginx 自动处理后端服务器的重定向。

          proxy_http_version 1.1;												# 设置代理使用的 HTTP 版本为 1.1 。
          proxy_set_header    Upgrade $http_upgrade;							# 将请求头中的 Upgrade 值设置为客户端发送的 $http_upgrade 的值。
          proxy_set_header    Host                $http_host;					# 将请求头中的 Host 设置为客户端发送的 $http_host 的值。
          proxy_set_header    X-Real-IP           $remote_addr;					# 将客户端的真实 IP 地址（$remote_addr）设置到 X-Real-IP 请求头中。
          proxy_set_header    X-Forwarded-For     $proxy_add_x_forwarded_for;	# 构建 X-Forwarded-For 请求头，包含客户端和各级代理的 IP 地址。
          proxy_set_header    X-Forwarded-Proto   $scheme;						# 将请求的协议（http 或 https ）设置到 X-Forwarded-Proto 请求头中。

          proxy_pass http://sekiro_business_netty;								# 将匹配到 /business-demo 路径的请求转发到后端的 http://sekiro_business_netty 服务。
        }

        location /register { # 开头的请求路径的规则。      # 后续页面或者手机进行wss连接用到的路由
          proxy_http_version 1.1;					    # 设置代理使用的 HTTP 版本为 1.1
          proxy_set_header Upgrade $http_upgrade;		# 将请求头中的 Upgrade 值设置为客户端发送的 $http_upgrade 的值。这通常用于支持 WebSocket 协议的升级
          proxy_set_header Connection "Upgrade";		# 将 Connection 请求头设置为 Upgrade ，以表明支持协议升级。
          proxy_set_header X-Real-IP $remote_addr;		# 将客户端的真实 IP 地址（$remote_addr）设置到 X-Real-IP 请求头中。

          proxy_pass http://sekiro_business_netty;		# 将匹配到 /register 路径的请求转发到上面 upstream 同名 指定的服务器。
        }

        location / {									# 表示匹配所有的请求路径。
          client_max_body_size 0;						# 设置客户端请求主体的大小限制为无限制。
          proxy_read_timeout      300;					# 设置从后端服务器读取响应的超时时间为 300 秒。
          proxy_connect_timeout   300;					# 设置与后端服务器建立连接的超时时间为 300 秒。
          proxy_redirect          off;					# 禁止 Nginx 自动处理后端服务器的重定向
          proxy_http_version 1.1;						# 设置代理使用的 HTTP 版本为 1.1 。
          proxy_set_header    Host                $http_host;					# 将请求头中的 Host 设置为客户端发送的 $http_host 的值。
          proxy_set_header    X-Real-IP           $remote_addr;					# 将客户端的真实 IP 地址（$remote_addr）设置到 X-Real-IP 请求头中。
          proxy_set_header    X-Forwarded-For     $proxy_add_x_forwarded_for;	# 构建 X-Forwarded-For 请求头，包含客户端和各级代理的 IP 地址。
          proxy_set_header    X-Forwarded-Proto   $scheme;						# 将请求的协议（http 或 https ）设置到 X-Forwarded-Proto 请求头中。

          proxy_pass http://sekiro_business_netty;
        }
    }
}
```



