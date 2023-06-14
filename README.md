# sshproxy

没什么技术含量就是SSH -D 加强版,只是方便我自己使用，用过其他第三方的都不支持轮询.就自己撸了一个。
可以根据请求的IP归属地、域名、域名解析IP归属地 进行路由。
配置文件支持多vps，单vps多端口，自己轮询。

Google Translate:
There is no technical content, it is the enhanced version of SSH -D, which is just convenient for my own use. I have used other third-party ones that do not support polling. I wrote one myself.
Routing can be performed according to the requested IP address, domain name, and domain name resolution IP address.
The configuration file supports multiple vps, single vps with multiple ports, and self-polling.
Support http and socks5 proxy protocols


<pre>
ssh转发代理，服务器不需要安装任何服务端。 设置好配置信息后，浏览器或wifi设置中可以设置SOCKS5代理 

xyproxy.exe	windows x64平台
xyproxy		linux x64 平台
xyproxy_arm	arm 平台

建议通过下面命令创建一个只允许 TCP 转发而不允许登陆（交互）的 SSH 账号。
比如要创建的专用账号为： xyproxy
useradd -s /bin/false xyproxy
通过下面命令来给 username 这个账号设置密码
passwd xyproxy
输入这个账号的新密码

配置文件 xyproxy.json 说明：

{
    "listen":":1080",  //本地监听的SOCKS5端口  :1080 代表任何人都可以连，127.0.0.1:1080 代表只有你本机可连
    "debug":false,   //调试模式 默认关闭
    "errwait":60,      //连接出错等待时间，单位秒。默认60秒
    "nodes":{          //可用节点列表
        "vps1":{
            "address":"192.168.1.1",                             	 //vps地址
            "port":[22],     			//ssh端口,支持多端口 例子 [22,23,24]
            "username":"xyproxy",			//ssh账号
            "password":"xyproxy123456"		//ssh密码
        }
    },
    "routing": {						//路由功能
		"default": "remote",   			//默认线路(未匹配到规则,或者IP归属地查询失败则走此线路),值: local 本地网络 , remote 远程网络
		"me": "CN",						//你所在国家
		"rules": [						//规则列表，
			{							//规则可单条可多条,优先级为上下顺序。
				"type": "ip",			//规则类型 type 为 IP 根据IP地址匹配 (值: ip <IP地址>  domain <域名>  domainip <域名解析IP>)
				"rule": "CN|HK",        //IP归属地为 中国 或者 香港的 走此线路, 特殊值: NOTME IP归属地不是 你所在国家  则走此线路
				"go": "local"           //对应线路 值: local 本地网络   remote 远程网络
			},
			{
				"type": "domain",		//规则类型 type 为 domain 根据域名匹配
				"rule": "ip138.com|baidu.com|www.qq.com",	//域名匹配正则，ip138.com 或 baidu.com 的所有子域名 或 www.qq.com 域名走此线路
				"go": "local"			//对应线路 值: local 本地网络   remote 远程网络
			},
			{
				"type": "domainip",    	//规则类型 type 为 domainip 根据域名解析IP匹配
				"rule": "CN|HK",		//域名DNS匹配正则，IP归属地为 中国 或者 香港的 走此线路
				"go": "local"			//对应线路 值: local 本地网络   remote 远程网络
			},
			{
				"type": "ip",			/规则类型 type 为 IP 根据IP地址匹配
				"rule": "NOTME",		//特殊值: NOTME IP归属地不是 你所在国家  则走此线路
				"go": "remote"			//对应线路 值: local 本地网络   remote 远程网络
			},
			{
				"type": "domainip",		//规则类型 type 为 domain 根据域名解析IP匹配
				"rule": "NOTME",		//特殊值: NOTME IP归属地不是 你所在国家  则走此线路
				"go": "remote"			//对应线路 值: local 本地网络   remote 远程网络
			}
		]
	}
}

IP库采用：GeoLite2-Country_20230602.tar  可以自行更新替换 ip.db
已知问题：IP库一些IP定位不准或者IP未收录，出现这种情况都会走 默认线路

</pre>
