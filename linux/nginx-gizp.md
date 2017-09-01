
## 服务器nginx gzip配置

``` ts
vim /usr/local/nginx/conf/nginx.conf //打开nginx配置文件
```
## 找到并修改
```conf
gzip on; //开启Gzip
gzip_buffers 16 8k; //buffer，就是，嗯，算了不解释了，不用改
gzip_comp_level 6; // 压缩级别，1-10，数字越大压缩的越好，时间也越长，看心情随便改吧
# gzip_http_version 1.1; // 1.0; // 用了反向代理的话，末端通信是HTTP/1.0，有需求的应该也不用看我这科普文了；有这句的话注释了就行了，默认是HTTP/1.1  
gzip_min_length 256; //不压缩临界值，大于1K的才压缩，一般不用改
# gzip_proxied any;
gzip_vary off; // 跟Squid等缓存服务有关，on的话会在Header里增加"Vary: Accept-Encoding"，我不需要这玩意，自己对照情况看着办吧
gzip_types
    text/xml application/xml application/atom+xml application/rss+xml application/xhtml+xml image/svg+xml
    text/javascript application/javascript application/x-javascript
    text/x-json application/json application/x-web-app-manifest+json
    text/css text/plain text/x-component
    font/opentype application/x-font-ttf application/vnd.ms-fontobject
    image/xicon; //进行压缩的文件类型，缺啥补啥就行了，JavaScript有两种写法，最好都写上吧，总有人抱怨js文件没有压缩，其实多写一种格式就行了
gzip_disable  "msie6"; //IE6对Gzip不怎么友好，不给它Gzip了
```

## 修改完配置文件,记得重启nginx或重启服务器!