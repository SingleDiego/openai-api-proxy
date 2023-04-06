### 准备工作

* 1. 拥有 openai api key (略）

* 2. 拥有一台可以连通 [https://api.openai.com](https://api.openai.com/) 的 VPS

远程登陆到 VPS 后，可使用 ``curl`` 工具来检查你的 VPS 是否跟 openai 的服务器联通。

```
curl https://api.openai.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
     "model": "gpt-3.5-turbo",
     "messages": [{"role": "user", "content": "Say this is a test!"}],
     "temperature": 0.7
   }'
```

更多参加：[API Reference - OpenAI API](https://platform.openai.com/docs/api-reference/making-requests)

* 3. 拥有一个域名，并解析到你的 VPS


<br>
<hr>
<br>

### Nginx 安装及配置

##### 安装 Nginx

```
sudo apt install nginx
```

##### Nginx 常用命令 

```
nginx -s stop       快速关闭Nginx，可能不保存相关信息，并迅速终止web服务。
nginx -s quit       平稳关闭Nginx，保存相关信息，有安排的结束web服务。
nginx -s reload     因改变了Nginx相关配置，需要重新加载配置而重载。
nginx -s reopen     重新打开日志文件。
nginx -c filename   为 Nginx 指定一个配置文件，来代替缺省的。
nginx -t            不运行，仅仅测试配置文件。nginx 将检查配置文件的语法的正确性，并尝试打开配置文件中所引用到的文件。
nginx -v            显示 nginx 的版本。
nginx -V            显示 nginx 的版本，编译器版本和配置参数。
```

##### Nginx 配置文件

Nginx 的配置文件位置在：``/etc/nginx/sites-available`` 文件夹内，文件名为 ``default``。修改该文件的 ``server`` 配置。

```
server {
	listen 80 default_server;
	listen [::]:80 default_server;
    # 改成自己的域名
	server_name abcdefg.xyz; 

	root /var/www/html;

	index index.html index.htm index.nginx-debian.html;

	location / {
        proxy_pass  https://api.openai.com/;
        proxy_set_header Host api.openai.com;
        proxy_set_header Connection '';
        proxy_http_version 1.1;
        chunked_transfer_encoding off;
        proxy_buffering off;
        proxy_cache off;
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_set_header X-Forwarded-Proto $scheme;
		proxy_ssl_server_name on;
		proxy_ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    }
}
```

修改后重载 Nginx：
```
nginx -s reload
```

如无意外，现在已经配置好你的 API。


<br>
<hr>
<br>


### 使用 API

我们用第三方 Chrome 插件 [ChatHub](https://chrome.google.com/webstore/detail/chathub-all-in-one-chatbo/iaakpnchhognanibcahlpcplchdfmgma) 来运行 ChatGPT。填上我们自定的 API 地址就可正常使用了。

![](https://upload-images.jianshu.io/upload_images/2070024-0dea9fe03457e757.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
