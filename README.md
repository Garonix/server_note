## 总则：只讲过程不讲理论
## 一、购买流程
对于VPS的选购方面我不懂，我只能说我用的是hostyun
[我的推广连接](https://my.hostyun.com/page.aspx?c=referral&u=31085)  
用这个链接注册和购买VPS我能获得10%的奖励金  
当然你也可以不用这个链接，普通域名是[my.hostyun.com](my.hostyun.com)  

### 1.先注册账号
![1679404715825.png](https://img.zeges.top/5l1LFg.png)  
  
   
完事儿进入用户中心，要先实名认证才能购买VPS  
### 2.购买VPS

同样没有推荐，我只能说我买的是这个  

![1679405239374.png](https://img.zeges.top/rVTAEM.png)  

点击**立即购买**  

![1679405384530.png](https://img.zeges.top/Iohu9J.png)  

来到图中第二步，选择操作系统，选择Debian10  

![1679405483482.png](https://img.zeges.top/yxkmXf.png)  

第三步付款周期模式，大火自己酌情考虑，可以先买一个月的看看能不能成，好使的话再续费。但他这个买一年的折扣还可以，酌情考虑吧。优惠码`hostyun`，打9折。

![1679405743915.png](https://img.zeges.top/OE6BQg.png)  

如果**买1年**的话总价是**180**  
购买先点击右下角的在线充值。这部分没什么好说的，买1个月就充20，买一年就充180

### 3. 初始化VPS
先按图中红框点击  

![1679406325627.png](https://img.zeges.top/96VlLc.png)  

来到这个界面  

![1679406454999.png](https://img.zeges.top/Eegnx9.png)  

记住这个主机IP地址，它就是你购买的VPS的IP，之后我们**所有**对VPS的操作都基于**这个IP**进行，一般情况下不要暴露到公网。
同时查看下面的**初始密码**，这是root账号的密码，绝对不要告诉别人，可以自行重置密码  

到这儿购买流程就结束了，接下来我们就连接VPS进行一系列配置  

## 二、配置VPS  
### 1.进行SSH连接  
有很多支持SSH连接的终端，没有推荐，我只能说我用的[final shell](https://www.hostbuf.com/t/988.html)  
点击超链接，进去下载、安装、打开。具体过程不阐述  

![1679407584329.png](https://img.zeges.top/03LqDA.png)  

按图中红框依次点击，选择`SSH连接`  

![1679407723517.png](https://img.zeges.top/Z3yClF.png)  

填写图示4个位置，然后点击确定，回到上一个图的页面，**双击**你的VPS  

![1679407870565.png](https://img.zeges.top/I5kStK.png)  

顺利的话，初次连接会弹一个框，问你是否把密钥保存在本地，如果用的是自己的电脑，就点“是”  

### 2.配置VPS  
现在已经可以通过终端控制VPS了  
啥也不管，先**依次**输入这两行命令

```
apt update -y && apt upgrade -y  #软件更新
apt install sudo curl wget -y #安装常用的3个软件
```

然后输入下面这行命令

```Bash
wget -O box.sh https://raw.githubusercontent.com/BlueSkyXN/SKY-BOX/main/box.sh && chmod +x box.sh && clear && ./box.sh
```

![1679408595974.png](https://img.zeges.top/H7AxLZ.png)  

输入`18`，然后敲回车  

![1679408737491.png](https://img.zeges.top/i4QEnE.png)  

依次输入`1`（回车）`1024`（回车）

继续输命令 ，怕出错，可以一行一行按序执行
```
wget -qO- get.docker.com | bash
systemctl enable docker
curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version    # 这步执行完后，屏幕会显示docker-compose version
mkdir -p /home/docker/npm
cd /home/docker/npm
touch docker-compose.yaml
```
到这儿暂时告一段落，接下来目光移到`final shell`下半部分  
一般来说，命令输入完后，路径会自动跳转到`/home/docker/npm`  
如果没有自动跳转也没关系，按下图手动点进来，看到`docker-compose.yaml`就行   

![1679448705738.png](https://img.zeges.top/B4zBpK.png)  

然后右键点击`docker-compise.yaml`，按下图选择`内部编辑器`(外部也行，要自己设置)  

![1679448904581.png](https://img.zeges.top/3RDyUd.png)  

然后**左键双击**打开`docker-compose.yaml`，直接把下面的代码粘贴进去  
```yaml
version: "3"
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      # These ports are in format <host-port>:<container-port>
      - '80:80' # Public HTTP Port
      - '443:443' # Public HTTPS Port
      - '81:81' # Admin Web Port
      # Add any other Stream port you want to expose
      # - '21:21' # FTP
    environment:
      DB_MYSQL_HOST: "db"
      DB_MYSQL_PORT: 3306
      DB_MYSQL_USER: "myself"
      DB_MYSQL_PASSWORD: "myself_password"
      DB_MYSQL_NAME: "npm"
      # Uncomment this if IPv6 is not enabled on your host
      # DISABLE_IPV6: 'true'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
    depends_on:
      - db

  db:
    image: 'jc21/mariadb-aria:latest'
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: 'myself_password'
      MYSQL_DATABASE: 'npm'
      MYSQL_USER: 'myself'
      MYSQL_PASSWORD: 'myself_password'
    volumes:
      - ./data/mysql:/var/lib/mysql

```
按`ctrl + s`保存，然后关闭窗口  
输入下面的命令，等待程序执行完成  
```
docker-compose up -d
``` 
执行完毕后，输入下面的命令  
```
cat > /etc/docker/daemon.json <<EOF
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "20m",
        "max-file": "3"
    }
}
EOF
```

继续输入命令  

```
systemctl restart docker
```  

之后新建一个网页，建议无痕浏览  
打开如下网址：`http://服务器ip:81`，形如`http://xx.xx.xx.xx:81`，能正常显示即可  
> 不能正常访问说明防火墙`81`端口未开，用如下命令开启
> ```
> ufw allow 81
> ```
  
然后进入`npm`的网页
```
初始账号: admin@example.com
初始密码: changeme
```

![1679452770640.png](https://img.zeges.top/52oESQ.png)  

初次登陆后需要修改初始账号和密码，按提示操作即可

到这儿基本准备工作完成，之后我们需要买一个域名  
### 2.购买域名  
访问`https://www.namesilo.com/login`  
右侧注册登录  

![1679453303349.png](https://img.zeges.top/Hu4PqS.png)  

登陆后点击`home`  

![1679453643843.png](https://img.zeges.top/Pdu5s0.png)  

在页面中随便输入一个你想注册的域名，点击搜索，一般**4个字符**以上会有比较便宜的

![1679453870676.png](https://img.zeges.top/vnbLSP.png)  

我们选这个最便宜的，注意我只是用`zegee`举例  

![1679453960990.png](https://img.zeges.top/ybcRNQ.png)  

点击`Add`，`Checkout`  

![1679454096998.png](https://img.zeges.top/v7PIJc.png)  

结算时输入优惠码`zege2023`可以便宜**最多**一刀，但域名价格最低0.99刀  

![1679454749718.png](https://img.zeges.top/I6SrtF.png)  

点击`CHECKOUT`，可以选择支付宝支付  
购买成功后，会收到官方发来的邮件  
下一步，我们需要把域名DNS解析转移到`cloudflare`上，方便管理  
### 3.DNS解析转移  
访问`https://cloudflare.com/`注册，登录，不赘述  
点击页面上方的`添加站点`  

![1679455421345.png](https://img.zeges.top/iMNdr7.png)  

输入你购买的域名，点击`添加站点`  

![1679455459465.png](https://img.zeges.top/d04pP8.png)  

选择`free`计划，点击`继续`  

![1679455547490.png](https://img.zeges.top/v1n436.png)  

如果有一个快速转移的过程，点击页面最底部的`继续`  

![1679456743113.png](https://img.zeges.top/tuYCM1.png)  

待会儿需要这两个地址  

新开一个网页，访问`https://www.namesilo.com/account_domains.php`  
点击**图中红框**  

![1679456575815.png](https://img.zeges.top/H1A3A6.png)  

`nameserver1`和`nameserver2`改成刚才`cloudflare`中写的内容  

![1679456685965.png](https://img.zeges.top/ZhkaQs.png)  

点击`SUBMIT`  
回到`cloudflare` ，点击`完成，检查名称服务器`  
不一定及时生效，耐心等待，会收到邮件  
如果有下面这步，点击`以后完成`  

![1679456978873.png](https://img.zeges.top/iwXyWM.png)  

### 4.添加记录  
访问`https://dash.cloudflare.com/`  
点击**你的域名**  

![1679457164207.png](https://img.zeges.top/C3yELf.png)  

点击右侧`快速操作`中的`DNS设置`  

![1679457247010.png](https://img.zeges.top/6g2Y5z.png)  

点击`添加记录`  

![1679457346228.png](https://img.zeges.top/5cyVJg.png)  

**按图操作**

![1679467127651.png](https://img.zeges.top/hgXpyY.png)  

然后访问**刚才搭建的npm**的地址`http://服务器ip:81`  
依次点击`Proxy Hosts`，`Add Proxy Hosts`
![1679457810607.png](https://img.zeges.top/togq10.png)  
![1679457828659.png](https://img.zeges.top/ooXJyi.png)  

**按图操作**  

![1679457988235.png](https://img.zeges.top/Oc0FqB.png)  
![1679458162112.png](https://img.zeges.top/4tSPAo.png)  

然后等待，不报错的话继续  
点击刚才已经操作完毕的域名旁边的`3个小点图标`  

![1679458295942.png](https://img.zeges.top/w5SFf8.png)  

点击`edit` ，然后按图操作  

![1679458386342.png](https://img.zeges.top/7zu0Kd.png)  

现在，你应该可以直接用`刚才在cloudflare中添加的域名记录`访问`npm`了  

### 5.部署面板  
输入以下命令  
```Bash
bash <(curl -Ls https://raw.githubusercontent.com/FranzKafkaYu/x-ui/master/install.sh)
```

根据提示设置`端口号`，`用户名`，`密码`  
完成后，访问`http://ip:端口号`  
输入用户名密码登录  
`xray`切换`1.7.5`  

![1679461436203.png](https://img.zeges.top/20j3GB.png)  

接下来**按图操作**  

![1679461560861.png](https://img.zeges.top/SgyyEI.png)  
记得最后还要点击`重启面板`
完成之后，重新访问`http://ip:端口号/path`  
然后继续**按图操作**  

![1679461627975.png](https://img.zeges.top/T2jrbm.png)  
![1679461865982.png](https://img.zeges.top/xideon.png) 

如果需要添加用户，默认即可
**注意区分**`访问面板用的端口`和`此处设置的端口`

然后访问`npm`，你可以用`npm的域名记录`，也可以用`ip:81`的方式访问  
`edit`任意一条记录，接下来继续**按图操作**  

![1679462158913.png](https://img.zeges.top/PJCgqG.png)  
![1679462424511.png](https://img.zeges.top/jjVSBm.png)  

文本框中输入以下代码，内容**需要稍作修改，看注释**  
```
  location /haha {                 # haha改成你前面设置的ws的路径, 我示例用的haha
          proxy_redirect off;
          proxy_pass http://ip:port;     # IP填服务器IP，port换成你入站规则那边的IP
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection "upgrade";
          proxy_set_header Host $http_host;
          proxy_read_timeout 300s;
          # Show realip in v2ray access.log
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
``` 

这步完成之后，服务器方面的工作就基本结束了  

## 三、客户端  
分成PC和移动端，移动端只讲安卓，IOS需要自行摸索
### 1.PC  
访问[客户端下载页](https://alist.zeges.top/123%E4%BA%91%E7%9B%98/%E8%BD%AF%E4%BB%B6/%E5%B7%A5%E5%85%B7)  
下载`.zip`文件，之后解压，打开`v2rayn.exe`  
访问`https://npm的域名记录/path`  
登录，点击`入站列表`  

![1679463605536.png](https://img.zeges.top/gQ0paQ.png)  

依次点击`查看`，`复制链接`  
在客户端**空白处**，`ctrl+c和v`  
应该就自动导入了，**双击**它  
然后**按图修改**  

![1679463912557.png](https://img.zeges.top/QKJve6.png)  

任务栏找到客户端
![1679464019038.png](https://img.zeges.top/LY5ssW.png)  
>右键--系统代理--自动配置系统代理  
右键--路由--全局
右键--服务器--你的服务器  

访问[测试站](https://google.com)

到这儿应该就大功告成了  

### 2.安卓/模拟器  
访问[客户端下载页](https://alist.zeges.top/123%E4%BA%91%E7%9B%98/%E8%BD%AF%E4%BB%B6/%E5%B7%A5%E5%85%B7)  
下载`.apk`文件，安装并运行  
把PC上的配置信息复制下来，就不用在移动端重新配置了  
>PC客户端上，对着你的服务器右键单击，倒数第二个选项  

安卓软件上点击**右上角**的`+`号，然后`从剪贴板导入`  

![1679464680961.png](https://img.zeges.top/Uatj8m.png)  

访问[测试站](https://google.com)  

施工完毕
