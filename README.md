你好！
很冒昧用这样的方式来和你沟通，如有打扰请忽略我的提交哈。我是光年实验室（gnlab.com）的HR，在招Golang开发工程师，我们是一个技术型团队，技术氛围非常好。全职和兼职都可以，不过最好是全职，工作地点杭州。
我们公司是做流量增长的，Golang负责开发SAAS平台的应用，我们做的很多应用是全新的，工作非常有挑战也很有意思，是国内很多大厂的顾问。
如果有兴趣的话加我微信：13515810775  ，也可以访问 https://gnlab.com/，联系客服转发给HR。
# wechat_spider


微信公众号爬虫 (支持全自动化批量爬取微信公众号所有文章 Go语言实现)


## 注意
此项目是微信公众号批量自动化爬虫的核心实现, 面向开发者开源, 可以当做go语言包引入到自己项目中, 完整产品必须二次开发实现,QQ交流群: 563954381

## 常见问题
  [FAQ][3]

## 代理服务端: 
通过Man-In-Middle 代理方式获取微信服务端返回,自动模拟请求自动分页,抓取对应点击的所有历史文章

## 客户端:  
android,iPhone等微信客户端平台, win和osx的微信客户端暂不兼容

代理协议: http && https,  https需要导入certs文件夹的goproxy证书,并且添加受信权限,详细教程请google


## Install
 依赖go1.7环境,`go get github.com/sundy-li/wechat_spider`

## 代理服务端
- 15行代码实现一个简单的爬虫服务  [simple_server.go][1]

```
package main

import (
	spider "github.com/sundy-li/wechat_spider"
)

func main() {
	var port = "8899"
	spider.InitConfig(&spider.Config{
		Verbose:    false, // Open to see detail logs
		AutoScroll: false, // Open to crawl scroll pages
	})
	spider.Regist(spider.NewBaseProcessor())
	spider.Run(port)
}

```

* 上面贴的是一个精简的服务端,拦截客户端请求,将微信文章url打印到终端
* 如果想自定义输出源以及实现批量自动化爬取,可以实现`Processor`接口的`Output`和`NextBiz`方法, 参考  [custom_output_server.go][2]
* 抓取阅读数,点赞数请实现`Processor`接口的`Output`和`NextUrl`方法,参考[custom_output_detail_server.go][4]


[1]: https://github.com/sundy-li/wechat_spider/blob/master/examples/simple_server.go
[2]: https://github.com/sundy-li/wechat_spider/blob/master/examples/custom_output_server.go
[3]: https://github.com/sundy-li/wechat_spider/blob/master/docs/FAQ.md
[4]: https://github.com/sundy-li/wechat_spider/blob/master/examples/custom_output_detail_server.go

* 微信会屏蔽频繁的请求,所以历史文章的翻页请求调用了Sleep()方法, 默认每个请求休眠50ms,可以根据实际情况自定义Processor覆盖此方法




## 客户端使用:    
  (确保客户端 能正常访问 代理服务端的服务) 

* Android客户端使用方法:
  运行后, 设置手机的代理为 本机ip 8899端口,  打开微信客户端, 点击任一公众号查看历史文章按钮, 即可爬取该公众号的所有历史文章(已经支持自动翻页爬取)
*  win/mac客户端,设置下全局代理对应 代理服务端的服务和端口,同理点击任一公众号查看历史文章按钮


## 批量化


* 动态修改js实现批量化(推荐使用),参考[custom_output_server.go][2] 

* 模拟点击实现批量化(比较麻烦,不推荐使用)
	windows端 :  Windows客户端获取批量公众号所有历史文章方法,对应原理请参考 http://stackbox.cn/2016-07-21-weixin-spider-notes/ ,同时也感谢博文作者提供此windows模拟点击的思路 

  1. 要求安装windows +  微信pc版本 + ActivePython3 + autogui, 设置windows下全局代理对应 代理服务端的服务和端口
  2. 修改 win_client.py 中的 bizs参数, 通过pyautogui.position() 瞄点设置 first_ret, rel_link 坐标
  3. 在examples目录下面, 执行 python win_client.py 将自动生成链接,模拟点击

## TODO
* ~~点赞数,阅读数~~  ← Complete!
* ~~过滤其他请求(图片,视频等),节省带宽开销~~ ← Complete!
* ~~本地缓存的清理,防止页面的请求不经过服务代理~~  ← Complete!
* 评论(我个人觉得这个东西是没什么意义的)
