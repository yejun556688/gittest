****Webrtc服务器包括：房间服务器（Room Server）、信令服务器（Signaling Server）、防火墙打洞服务器（STUN/TURN/ICE Server）**

### 一、房间服务器搭建

**1、代码下载**
服务器项目地址[https://github.com/webrtc/apprtc](https://link.jianshu.com?t=https://github.com/webrtc/apprtc)

```
git clone https://github.com/webrtc/apprtc
```

**2、配置依赖环境**

```
sudo apt-get install npm
npm -g install grunt-cli
```

Google App Engine SDK for Python：
下载[Google App Engine SDK for Python](https://link.jianshu.com?t=https://cloud.google.com/appengine/downloads#Google_App_Engine_SDK_for_Python)  google-cloud-sdk-183.0.0-linux-x86_64.tar.gz（百度云盘） 并解压

```
./google-cloud-sdk/install.sh
./google-cloud-sdk/bin/gcloud components install app-engine-python
```

编辑`~/.bashrc`

```
export PATH=$PATH:/home/mi/tools/google-cloud-sdk/bin
```

node直接使用`sudo apt-get install nodejs-legacy`安装版本过低，使用下面方法安装：

```
wget https://nodejs.org/dist/v7.7.0/node-v7.7.0-linux-x64.tar.gz
tar xvf node-v7.7.0-linux-x64.tar.gz
```

配置环境，编辑`~/.bashrc`

```
export PATH=$PATH:/home/mi/tools/node-v7.7.0-linux-x64/bin
```

安装`python-requests`

```
sudo apt-get install python-requests
```

**3、安装apprtc代码中的grunt依赖**

```
cd apprtc
npm install
grunt build //编译
```

**4、修改配置文件**
主要是`src/app_engine`目录下的`apprtc.py`和`constants.py`文件。对于`src/app_engine`目录下的文件每次修改后需执行命令`grunt build`重新编译，也可以直接编辑`out/app_engine`目录下的`apprtc.py`和`constants.py`避免重新编译。
**constants.py**

```
#TURN_BASE_URL = 'https://computeengineondemand.appspot.com'
TURN_BASE_URL = 'http://192.168.2.128:3033'
#TURN_URL_TEMPLATE = '%s/turn?username=%s&key=%s'
TURN_URL_TEMPLATE = '%s/turn.php?username=%s&key=%s'
#CEOD_KEY = '4080218913'
CEOD_KEY = '1234' 

WSS_INSTANCES = [{
    #WSS_INSTANCE_HOST_KEY: 'apprtc-ws.webrtc.org:443',
    WSS_INSTANCE_HOST_KEY: '192.168.2.128:8089',
    WSS_INSTANCE_NAME_KEY: 'wsserver-std',
    WSS_INSTANCE_ZONE_KEY: 'us-central1-a'
}, {
    #WSS_INSTANCE_HOST_KEY: 'apprtc-ws-2.webrtc.org:443',
    WSS_INSTANCE_HOST_KEY: '192.168.2.128:8089',
    WSS_INSTANCE_NAME_KEY: 'wsserver-std-2',
    WSS_INSTANCE_ZONE_KEY: 'us-central1-f'
}]

```

**apprtc.py**

```
 if wss_tls and wss_tls == 'false':
    wss_url = 'ws://' + wss_host_port_pair + '/ws'
    wss_post_url = 'http://' + wss_host_port_pair
  else:
    #wss_url = 'wss://' + wss_host_port_pair + '/ws'
    wss_url = 'ws://' + wss_host_port_pair + '/ws'
    #wss_post_url = 'https://' + wss_host_port_pair
    wss_post_url = 'http://' + wss_host_port_pair

def make_pc_config(ice_transports):
  config = {
  #'iceServers': [],
  'iceServers': [{"urls":"stun:192.168.2.128"},{"urls":"turn:lin@192.168.2.128","credential":"1234"}],
  'bundlePolicy': 'max-bundle',
  'rtcpMuxPolicy': 'require'
  };

  if ice_transports:
    config['iceTransports'] = ice_transports
  return config

```

把原来的wss和https的scheme都改为ws和http，不要让客户端或者浏览器去使用SSL链接。若有第三方根证书的签名机构颁发的证书可忽略。

修改完后重新执行`grunt build`。

**5、启动房间服务器**

```
dev_appserver.py --host=192.168.2.128 ./apprtc/out/app_engine
```

### 二、信令服务器搭建

**1、安装GO环境**
直接使用命令`sudo apt-get install golang-go`安装的版本太低，后面执行`go get collidermain`会报错，所以采用下面一种方法：
下载GO安装包并解压，百度云盘直接有安装包

```
wget https://storage.googleapis.com/golang/go1.6.3.linux-amd64.tar.gz
tar xvf go1.6.3.linux-amd64.tar.gz
```

编辑打开文件/etc/profile（也可根据自己需求选择其他环境配置文件编辑），在文件末尾添加两行

```
export GOROOT=$HOME/go 
export PATH=$PATH:$GOROOT/bin
```

保存退出执行`source /etc/profile`。

**2、配置信令服务器**
新建目录(collider_root)用于存放Collider的go代码程序。

```
mkdir -p ~/collider_root
mkdir ~/collider_root/src

```

同设置GOROOT设置，在/etc/profile中添加

```
export GOPATH=$HOME/collider_root
export PATH=$PATH:$GOPATH/bin

```

建立链接（也可以直接将`~/apprtc/src/collider/`目录中的`collider、collidermain、collidertest`直接拷贝到`~/collider_root/src`目录下）

```
ln -sf ~/apprtc/src/collider/collider $GOPATH/src/
ln -sf ~/apprtc/src/collider/collidermain $GOPATH/src/
ln -sf ~/apprtc/src/collider/collidertest $GOPATH/src/

```

编辑$GOPATH/collidermain/main.go,修改房间服务器为我们前面的房间服务器：

```
//var roomSrv = flag.String("room-server", "https://appr.tc", "The origin of the room server")
var roomSrv = flag.String("room-server", "http://192.168.2.128:8080", "The origin of the room server")

```

**3、安装信令服务器依赖和collidermain**

```
go get collidermain
go install collidermain

```

若`go get collidermain`命令运行失败,那么则用下面这个麻烦的方法:

```
cd $GOPATH/src
wget http://www.golangtc.com/static/download/packages/golang.org.x.net.tar.gz
tar xvf golang.org.x.net.tar.gz
go install golang.org/x/net/websocket/

```

**4、运行**

```
 $GOPATH/bin/collidermain -port=8089 -tls=false -room-server="http://192.168.2.128:8080"
```

**5、测试**

```
go test collider

```

## 三、STUN/TURN/ICE服务器的搭建

**1、下载并安装（详细阅读安装手册 INSTALL）**

coturn 是一个 turn server，其支持浏览器通过 stun 协议与其进行通讯，以获取浏览器自己的公网地址及 NAT 信息，如果建立 ICE Server 则需要自己建立一个 http Server，客户端通过 REST API 进行访问以获取 turn Server 地址及认证信息等；我们将首先通过配置，保证浏览器使用我们自己的 TURN Server。

**1、下载**

下载　Ubuntu 下对应的最新版本，[下载地址](https://link.jianshu.com?t=http://turnserver.open-sys.org/downloads/v4.5.0.6/turnserver-4.5.0.6.tar.gz)coturn 底层网络库依赖 libevent，所以需首先安装　libevent，这个我们就直接通过 apt-get 进行下载安装，不单独下载源码进行编译安装了：

```
sudo apt-get install libevent-dev
```

**2、编译**

coturn 下载的是源码，需自己编译，进入 coturn 源码目录，依次执行以下命令：

```
./configure
make
```

编译成功后，会在源码 bin 目录中生成配置及可执行文件（手动将 example 目录中的 turnserver.conf 配置文件拷贝到 bin 目录）：

```
gobert@gobert-ThinkPad-X230:~/OpenSource/coturn/bin$ ls
turnadmin            turnutils_natdiscovery  turnutils_stunclient
turnserver           turnutils_oauth         turnutils_uclient
turnserver.conf      turnutils_peer          turnutils_rfc5769check
```

**3、编辑配置文件**

然后编辑`turnserver.conf`

```
listening-device=eth0
relay-device=eth1
Verbose
fingerprint
lt-cred-mech
use-auth-secret
static-auth-secret=1234
user=lin:1234
user=xml:1234
stale-nonce
cert=/etc/turn_server_cert.pem
pkey=/etc/turn_server_pkey.pem
no-loopback-peers
no-multicast-peers

```

上面cert和pkey配置的自签名证书用Openssl命令生成:

```
sudo openssl req -x509 -newkey rsa:2048 -keyout /etc/turn_server_pkey.pem -out /etc/turn_server_cert.pem -days 99999 -nodes

```

**3、启动**

进入 bin 目录

```
./turnserver
```

## 四、配置 ICE REST API

首先安装 Express，Express 是一种保持最低程度规模的灵活 Node.js Web 应用程序框架，为 Web 和移动应用程序提供一组强大的功能。

```
npm install express --save
```

生成 index.js 代码文件，配置 REST API

在一个自定义目录中新建 index.js 文件，内容如下：

```
var express = require('express')
var crypto = require('crypto')
var app = express()

var hmac = function (key, content) {
  var method = crypto.createHmac('sha1', key)
  method.setEncoding('base64')
  method.write(content)
  method.end()
  return method.read()
}

app.get('/iceconfig', function (req, resp) {
  var query = req.query
  var key = '1234'
  var time_to_live = 600
  var timestamp = Math.floor(Date.now() / 1000) + time_to_live
  var turn_username = timestamp + ':lin'
  var password = hmac(key, turn_username)

  resp.setHeader("Access-Control-Allow-Origin", "*");   //设置跨域访问
  
  return resp.send({
    iceServers: [
      {
        urls: [
          'turn:192.168.1.112:3478?transport=udp',
          'turn:192.168.1.112:3478?transport=tcp',
          'turn:192.168.1.112:3479?transport=udp',
          'turn:192.168.1.112:3479?transport=tcp'
        ],
        username: turn_username,
        credential: password
      }
    ]
  })
})

app.listen('3033', function () {
  console.log('server started')
})
```

最后通过 node 启动此脚本，外部即可访问：

```
node index.js
```

访问方法如：

```
http://192.168.1.112:3033/iceconfig?key=none
```