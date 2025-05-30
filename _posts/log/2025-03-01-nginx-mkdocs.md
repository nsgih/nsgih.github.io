---
title: Nginx静态部署mkdocs
author: nsgi
categories: [log]
image: assets/image-20250505230220.png
---
# mkdocs+nginx部署

之前.local用nginx静态部署了CRA demo，现在加一个mkdocs。尝试了`localhost:8080`下用子目录隔开，也尝试了 `localhost:4000`不同端口根目录隔开。最后是保留了子目录的的形式，分别保留在`localhost/cms/`和`localhost/docs/`。

另外nginx的.conf好像是初始化自动生成的。

### 1.环境

[![Static Badge](https://img.shields.io/badge/win11%E5%AE%B6%E5%BA%AD%E4%B8%AD%E6%96%87-10.0.26100-55acee)](#) [![Static Badge](https://img.shields.io/badge/mkdocs-1.6.1-55acee)](https://github.com/mkdocs/mkdocs/tree/master) [![Static Badge](https://img.shields.io/badge/mkdocs--material-9.6.12-55acee?logo=materialformkdocs&logoColor=%23526CFE)](https://squidfunk.github.io/mkdocs-material/) [![Static Badge](https://img.shields.io/badge/python-3.12.4-55acee?logo=python&logoColor=%233776AB)](https://www.python.org/downloads/release/python-3124/)

```shell
systeminfo # win11家庭中文@10.0.26100
py -V # py@3.12.4
pip list | findstr mkdocs # mkdocs@1.6.1, mkdocs-material@9.6.12
nginx -v # nginx/1.27.5

# | is a pipe command, which is used to 传输前者输出到后者
```



### 2.安装Py

这边用lts版本了（不然只有二进制文件，还得是lts提供exe）[python@3.12.4](https://www.python.org/downloads/release/python-3124/)。exe安装好之后没配置环境变量，直接where会出现windows的假地址（目的是引导你去store里下载）。

```shell
C:\Users\nagih>where python
C:\Users\nagih\AppData\Local\Microsoft\WindowsApps\python.exe
```

配置Path环境变量就能用了。

```shell
D:\Python\Python312\ # py本体
D:\Python\Python312\Scripts\ # py 脚本
```

哦对了，`certutil -hashfile`验证一下md5：

```shell
C:\Users\nagih>certutil -hashfile "D:\python-3.12.4-amd64.exe" MD5
MD5 的 D:\python-3.12.4-amd64.exe 哈希:
f3df1be26cc7cbd8252ab5632b62d740
CertUtil: -hashfile 命令成功完成。

#这是官网的哈希
f3df1be26cc7cbd8252ab5632b62d740
```

### 3.mkdocs@启动

```shell
pip install mkdocs
mkdocs serve
```

在localhost:8000直接打开。使用起来很简单，在index.md写就行了，mkdocs.yml也直接的（虽然我至今没理解.yml是个啥玩意儿）。

有个问题是table渲染不出来，内建的theme:readthedoc是可以渲染的，但我正在安装material：

```shell
pip install mkdocs-material
```

我觉得一个天上一个地下了：

![image-20250423163117530](./assets/image-20250423163117530.png)

![image-20250423163138098](./assets/image-20250423163138098.png)

#### mkdocs.yml

确实挺直观的json感：

```yaml
site_name: A23-OSC

theme:
  name: material  # 使用 Material 主题

nav:
  - Home: index.md
  - About: about.md
  - MIT License: license.md



# markdown_extensions:
#   - tables
```



### 4.nginx@启动

```
VScode默认快捷键
Windows/Linux:

折叠全部：按下 Ctrl + K，然后按 Ctrl + 0（数字零）。
展开全部：按下 Ctrl + K，然后按 Ctrl + J。
```

这下在vscode里看.conf舒服多了。

#### nginx静态代理方案一：localhost/docs/

这个就是在之前的cms项目下加子目录/docs，然后把mkdocs build得到的/site下所有静态文件拷到子目录/html/docs。

然后重启一下nginx：

```shell
D:\nginx-1.27.5\nginx-1.27.5>nginx -s reload
D:\nginx-1.27.5\nginx-1.27.5>
```

端口80是没有变的：

```shell
# D:\nginx-1.27.5\nginx-1.27.5\html
D:.
├─docs
│  ├─about
│  ├─assets
│  │  ├─images
│  │  ├─javascripts
│  │  │  ├─lunr
│  │  │  │  └─min
│  │  │  └─workers
│  │  └─stylesheets
│  ├─license
│  └─search
└─static
    ├─css
    ├─js
    └─media
```

```nginx
# nginx.conf
worker_processes  1;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    
    server {
        listen       80;
        server_name  localhost;

        location / {
            root   html;
            index  index.html index.htm;
            try_files $uri /index.html; 
        }

        # MkDocs 项目（放到 html/docs/ 目录）
        location /docs/ {
            root html;
            index index.html;
            try_files $uri $uri/ =404;
        }
	    # ###
        
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}

```

没啥问题：

![image-20250423172438977](./assets/image-20250423172438977.png)

**那既然都可以套娃了，肯定顺手对齐一下了：**

```shell
# D:\nginx-1.27.5\nginx-1.27.5\html
D:.
├─cms
│  └─static
│      ├─css
│      ├─js
│      └─media
└─docs
    ├─about
    ├─assets
    │  ├─images
    │  ├─javascripts
    │  │  ├─lunr
    │  │  │  └─min
    │  │  └─workers
    │  └─stylesheets
    ├─license
    └─search
```

```nginx
# nginx.conf
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    
    server {
        listen       80;
        server_name  localhost;

        # 就改了cms的文件路径加载locaiton
        location /cms/ {
            root   html; # bonus: 这边其实可以用alias精确指定本地映射，用来区分文件路径和请求路径；root直接二合一了
            index  index.html index.htm;
            try_files $uri index.html; # $uri: 这个就是自动匹配了当找不到index.html
        }

        location /docs/ {
            root html; 
            index index.html;
            try_files $uri $uri/ =404; # 404是状态码
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
}
```

没啥问题：

![image-20250423175025554](./assets/image-20250423175025554.png)

> [!NOTE]
>
> CRA配置路由是要用basename="/cms"的。

#### nginx静态代理方案二：换个端口8081

文件路径不变。

```nginx
# nginx.conf
worker_processes  1;
events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    
    server {
        listen       80;
        server_name  localhost;
        location / {
            root   html/cms;
            index  index.html index.htm;
            # 比较奇怪的是开了新端口的情况下，原来"index.html"是会报错的
            # 逆天的是你直接注释掉都不报错，就是原来的index.html是会报错的
            try_files $uri /index.html; 
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
    
    #加了个新server，端口是8081
    server {
        listen       8081;
        server_name  localhost;
        location / {
            root   html/docs;
            index  index.html;
            try_files $uri $uri/ =404;
        }
        
        error_page 404 /404.html;
        location = /404.html {
            root html/docs;
            internal;
        }
    }
    # ###
}

```

没啥问题：

![image-20250423182052079](./assets/image-20250423182052079.png)

![image-20250423182037329](./assets/image-20250423182037329.png)

#### .conf@alias, root

拿之前的代码聊聊alias（别名）。

```nginx
        # 就改了cms的文件路径加载locaiton
        location /cms/ {
            root   html; # bonus: 这边其实可以用alias精确指定本地映射，用来区分文件路径和请求路径；root直接二合一了
            index  index.html index.htm;
            try_files $uri index.html; # $uri: 这个就是自动匹配了当找不到index.html
        }
```

本质上alias就是精确文件路径，区别于root的“请求路径和文件路径对齐”。

比如你local写/cms/。

用root时候就会有个拼接的动作，在请求的时候会拼接上/cms/html。浏览器是“./cms”,文件也是到“/cms/”去找，是对齐的。

但你如果用alias，就可以分开请求路径和文件路径。你alias docs/html，他不会拼接成/cms/docs/html，而是直接新月突击到/docs/html，以此达到浏览器请求路径是“~/cms”，但实质上请求路径新月突击到“~/docs”进行NTR的效果。

#### CI/CD配置

| 工具           | 用途                            | 适合你吗？ |
| -------------- | ------------------------------- | ---------- |
| GitHub Actions | 最流行的 CI/CD，GitHub 免费内置 | ✅ 推荐！   |
| GitLab CI      | 如果你用 GitLab                 | 可考虑     |
| Jenkins        | 老牌 CI/CD 工具，自部署         | 太重了     |
| Vercel/Netlify | 部署前端自动化，适合 React 项目 | 仅前端适用 |

至此，完成了前端调研和mkdocs展示，以及nginx的再次使用。

最后再把完整版本的.md更新一下，然后mkdocs build放到nginx，然后nginx -s reload就结束了。
