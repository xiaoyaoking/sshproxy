# sshproxy

ssh转发代理，服务器不需要安装任何服务端。

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
    "listen":":1080",  //监听的端口  :1080 代表任何人都可以连，127.0.0.1:1080 代表只有你本机可连
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
		"rules": [						//规则列表，规则会按照规则顺序作为优先级执行匹配。
			{
				"type": "ip",			//规则类型 type 为 IP 则请求类型为IP地址的 会走此规则
				"rule": "CN|HK",        //IP归属地为 中国 或者 香港的 走此线路, 特殊值: NOTME IP归属地不是 你所在国家  则走此线路
				"go": "local"           //对应线路 值: local 本地网络   remote 远程网络
			},
			{
				"type": "domain",		//规则类型 type 为 domain 则请求类型为域名的 会走此规则
				"rule": "ip138.com|baidu.com|www.qq.com",	//域名匹配正则，ip138.com 或 baidu.com 的所有子域名 或 www.qq.com 域名走此线路
				"go": "local"			//对应线路 值: local 本地网络   remote 远程网络
			},
			{
				"type": "domainip",    //规则类型 type 为 domainip 则请求类型为域名的 会走此规则
				"rule": "CN|HK",		//域名DNS匹配正则，IP归属地为 中国 或者 香港的 走此线路
				"go": "local"			//对应线路 值: local 本地网络   remote 远程网络
			},
			{
				"type": "ip",			/规则类型 type 为 IP 则请求类型为IP地址的 会走此规则
				"rule": "NOTME",		//特殊值: NOTME IP归属地不是 你所在国家  则走此线路
				"go": "remote"			//对应线路 值: local 本地网络   remote 远程网络
			},
			{
				"type": "domainip1",	//规则类型 type 为 domain 则请求类型为域名的 会走此规则
				"rule": "NOTME",		//特殊值: NOTME IP归属地不是 你所在国家  则走此线路
				"go": "remote"			//对应线路 值: local 本地网络   remote 远程网络
			}
		]
	}
}
