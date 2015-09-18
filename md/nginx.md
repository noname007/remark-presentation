class: center, middle

# Nginx
yangzz
.footnote[Powered by [remark](https://github.com/gnab/remark)]
---

##特点:
-----
--

**高并发** -- 异步非阻塞
--


  - 餐馆
--


**高扩展性**
--


其本身就是由功能不同，层次不同、类型不同，耦合度极低的模块组成，也造就了Nginx有庞大的第三方模块、nginx-rtmp,ngx_lua
--



**配置简洁**
--

```ruby
server{
    listen 88;
    location /{
        root /var/flvs;
        autoindex on;
    }
}
```

**web server 配置成功！！！**

---
layout: true
#####nginx-rtmp
-----

---

**下载源码**

![](./img/nginx_rtmp.png)

---
**安装**

命令:

```shell
./configure --add-module=../nginx-rtmp-module-master/ --with-debug
make
make install
```

默认会被安装到 **/usr/local/nginx**

---
layout: true
#####nginx-rtmp 直播、点播、鉴权配置
-----

---

**直播**

```shell
rtmp {
        server {
                listen 1935;

                application live {
                        live on; 
                }    

        }   
}
```
---
**点播配置**
```shell  
    application vod_http {
       
        play_local_path /tmp/videos; 
        play /tmp/videos http://127.0.0.1:88;

    }    

```

**VLC rtmp://192.168.1.169/vod_http//abcd.flv**


---
**鉴权**

```shell 
    application vod {

        on_play http://localhost:91/auth;
        play /var/flvs;

    }

```


```shell
 curl -I 192.168.1.169:91/auth
```


---

class: middle,center
layout: false
#OpenResty

---
layout:true
#####OpenResty
-----


---

```lua
    server{
        listen 91;
        location /auth{
            access_by_lua '
                  local args = {}
                  local r_m = ngx.var.request_method 
                  -- ngx.log(ngx.ERR,r_m)
                  -- ngx.log(ngx.ERR,"===========")
                  if "GET" == r_m or "HEAD" == r_m then
                        -- ngx.log(ngx.ERR,"GET============")
                        args = ngx.req.get_uri_args()
                  elseif "POST" == r_m then
                        ngx.req.read_body()
                        args = ngx.req.get_post_args()
                        -- ngx.log(ngx.ERR,"POSTD-----------")
                  end 
                  if args["sign"] == nil then
                        ngx.header.Location = "301.mp4"
                        ngx.exit(304)
                  else
                        ngx.exit(200)   
                  end  
            ';
        }
    }

```

---
layout: false
![](./img/vksyun.png)

---
class: middle,center
#谢谢
