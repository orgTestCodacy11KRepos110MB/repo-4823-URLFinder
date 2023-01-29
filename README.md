# URLFinder
致敬JSFinder，开发由来就是使用JSFinder经常返回空或链接不全，作者也很久没更新修bug，就萌生了自己开发的想法

URLFinder是一款快速提取检测页面中JS与URL的工具  

通常用于查找隐藏在页面或js中的敏感或未授权api接口  

URLFinder更专注于提取页面中的JS与URL链接，提取的数据更完善且可查看状态码、内容大小、标题等（后续加了一些功能，但主要还是抓链接）  

基于golang的多线程特性，几千个链接也能几秒内出结果  

有什么需求或bug欢迎各位师傅提交lssues


#### 注意:  

内置的fuzz功能不是常规的目录爆破，是基于抓到的404目录和路径当作字典去随机组合然后碰撞出有效路径，是解决路径拼接错误的一种处理方式

为了更好的兼容和防止漏抓链接，后续更新放弃了低误报率，错误的链接会变多但漏抓概率变低，可通过 ‘-s 200’ 筛选状态码过滤无效的链接


## 功能说明
1.提取页面与JS中的JS及URL链接（URL深入一层，JS深入三层 防止抓偏），以及部分敏感信息  
2.提取到的链接会显示状态码、响应大小、标题等（带cookie操作时请使用-m 3 安全模式，防止误操作）  
3.提取批量URL  
4.yml配置Headers请求头、代理  
5.结果导出到csv、json、html  
6.记录抓取来源，便于手动分析（-o 导出才有）
7.指定抓取域名  
8.指定baseurl路径(指定目录拼接)
9.设置代理    
10.对404链接Fuzz（测试版，有问题提issue）

结果会优先显示输入的url顶级域名，其他域名不做区分显示在 other  
结果会优先显示200，按从小到大排序（输入的域名最优先，就算是404也会排序在其他子域名的200前面）

## 使用截图

[![0.jpg](https://github.com/pingc0y/URLFinder/master/img/0.jpg)](https://github.com/pingc0y/URLFinder/master/img/0.jpg)   
[![1.jpg](https://github.com/pingc0y/URLFinder/master/img/1.jpg)](https://github.com/pingc0y/URLFinder/master/img/1.jpg)  
[![2.jpg](https://github.com/pingc0y/URLFinder/master/img/2.jpg)](https://github.com/pingc0y/URLFinder/master/img/2.jpg)  
[![3.jpg](https://github.com/pingc0y/URLFinder/master/img/3.jpg)](https://github.com/pingc0y/URLFinder/master/img/3.jpg)  
[![4.jpg](https://github.com/pingc0y/URLFinder/master/img/4.jpg)](https://github.com/pingc0y/URLFinder/master/img/4.jpg)  
[![5.jpg](https://github.com/pingc0y/URLFinder/master/img/5.jpg)](https://github.com/pingc0y/URLFinder/master/img/5.jpg)  



## 使用教程
单url时使用  
```
URLFinder.exe -u http://www.baidu.com -s all -m 2

URLFinder.exe -u http://www.baidu.com -s 200,403 -m 2
```
批量url时使用  
```
URLFinder.exe -s all -m 2 -f url.txt -o d:/
```
参数：  
```
-a  自定义user-agent请求头  
-b  自定义baseurl路径  
-c  请求添加cookie  
-d  指定获取的域名  
-f  批量url抓取，需指定url文本路径  
-h  帮助信息 （可以看到当前版本更新日期） 
-i  加载yaml配置文件（不存在时，会在当前目录创建一个默认yaml配置文件）  
-m  抓取模式：
        1  正常抓取（默认）
        2  深入抓取 （url只深入一层，防止抓偏）
        3  安全深入抓取（过滤delete，remove等敏感路由） 
-o  结果导出到csv文件，需指定导出文件目录（.代表当前目录）
-s  显示指定状态码，all为显示全部  
-t  设置线程数（默认50）
-u  目标URL  
-x  设置代理,格式: http://username:password@127.0.0.1:8877
-z  提取所有目录对404链接进行fuzz(只对主域名下的链接生效,需要与-s一起使用）  
        1  目录递减fuzz  
        2  2级目录组合fuzz
        3  3级目录组合fuzz（适合少量链接使用）
```
##  编译  
以下是在windows环境下，编译出各平台可执行文件的命令  

```
SET CGO_ENABLED=0
SET GOOS=windows
SET GOARCH=amd64
go build -ldflags "-s -w" -o ./URLFinder-windows-amd64.exe

SET CGO_ENABLED=0
SET GOOS=windows
SET GOARCH=386
go build -ldflags "-s -w" -o ../URLFinder-windows-386.exe

SET CGO_ENABLED=0
SET GOOS=linux
SET GOARCH=amd64
go build -ldflags "-s -w" -o ../URLFinder-linux-amd64

SET CGO_ENABLED=0
SET GOOS=linux
SET GOARCH=arm64
go build -ldflags "-s -w" -o ../URLFinder-linux-arm64

SET CGO_ENABLED=0
SET GOOS=linux
SET GOARCH=386
go build -ldflags "-s -w" -o ../URLFinder-linux-386

SET CGO_ENABLED=0
SET GOOS=darwin
SET GOARCH=amd64
go build -ldflags "-s -w" -o ../URLFinder-macos-amd64

SET CGO_ENABLED=0
SET GOOS=darwin
SET GOARCH=arm64
go build -ldflags "-s -w" -o ../URLFinder-macos-arm64
```
## 更新说明  
2023/1/29  
新增 -b 设置baseurl路径  
新增 -o json、html格式导出  
新增 部分敏感信息获取  
新增 默认会进行简单的js爆破  
变化 能抓到更多链接，但垃圾数据变多  
变化 代理设置方式变更

2022/10/25  
新增 -t 设置线程数(默认50)  
新增 -z 对主域名的404链接fuzz测试  
优化 部分细节  

2022/10/6  
新增 -x http代理设置  
修改 多个相同域名导出时覆盖问题处理  

2022/9/23  
新增 对base标签的兼容  
修复 正则bug  

2022/9/16  
新增 -m 3 安全的深入抓取，过滤delete、remove等危险URL   
新增 -d 获取指定域名资源  
新增 -o 导出到文件显示获取来源source  
修复 已知bug  

2022/9/15  
修复 某种情况下的数组越界  

2022/9/12  
修复 linux与mac下的配置文件生成错误  
修复 已知逻辑bug  

2022/9/5  
新增 链接存在标题时，显示标题  
新增 -i 参数，加载yaml配置文件（目前只支持配置请求头headers）  
修改 部分代码逻辑  
修复 当ip存在端口时，导出会去除端口

2022/8/29  
新增 抓取url数量显示  
优化 部分代码  
新增 提供各平台可执行文件

2022/8/27   
新增 -o 改为自定义文件目录  
新增 导出文件改为csv后缀，表格查看更方便  
修复 已知正则bug

2022/8/19  
优化 加长超时时间避免误判    

2022/8/5  
新增 状态码过滤  
新增 状态码验证显示进度  
修复 域名带端口输出本地错误问题  

2022/7/25   
优化 js规则  
优化 排序  
新增 根据状态码显示彩色字体  

2022/7/6   
完善 规则  

2022/6/27   
优化 规则  
新增 提供linux成品程序  

2022/6/21   
修改 获取状态码从自动改为手动（-s）  
新增 显示响应内容大小  

2022/6/16   
优化 提取规则增强兼容性  
修复 数组越界错误处理  

2022/6/14  
修复 部分网站返回空值的问题  

2022/6/13  
新增 自定义user-agent请求头功能  
新增 批量url抓取功能  
新增 结果导出功能  
优化 过滤规则  
优化 结果排版  

2022/6/8  
修复 忽略ssl证书错误  

