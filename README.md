# http
## 网络背后的流程
     startTime->redirectStart(开始跳转)->redirect(跳转)->redirectEnd(跳转结束)以上就做跳转的原因是有可能重定向了判断需不需要跳转
     
     fetchStart(fetch开始)->App cache(应用缓存)(有缓存就不请求了)
     
     domainLookupStart(域名解析开始)->DNS(DNS查找)->domainLookupEnd(域名解析结束)
     
     connectStart(开始创建链接)->secureConnectStart(开始创建安全链接)->TCP(创建TCP链接)->connectEnd(创建链接结束)（三次握手）
     
     requestStart(开始发送请求)->request(发送请求)
     
     responseStart(开始发送请求)->reponese(接受响应)->responseEnd(结束接收返回)

##   五层模型 
           应用层->http,ftp                              应用层->http,ftp
           传输层->tcp,Udp                               传输层->tcp,Udp
    客户端  网络层                               服务层    网络层   
           数据链路层                                     数据链路层
           物理层                                        物理层
           
           
           物理层主要作用是定义物理设备如何传输数据（硬件：网线.....）
           
           数据链路层在通信的实体间建立数据链路连接（软件：0，1）
           
           网络层为数据在结点之间传输创建逻辑链路（比如怎么寻找地址）
           
           传输层向用户提供可靠的端到端（end-to-end）服务(协议)
           传输层向高层屏蔽了下层数据通信的细节
           
           应用层为应用软件提供了很多服务
           构建与tcp协议之上
           屏蔽网络传输相关细节
      
 ## 三次握手时序图
    
    client -> SYN=1,SEQ=X ->server（一次）-> SYN->ACK=X+1,SEQ = Y->client(二次) -> ACK =Y+1,SEQ=Z->server(三次)
    为什么建立TCP三次握手：当客户端发送请求时服务端返回时，服务端不知道返回有没有成功建立这次的连接，所以第三次客户端发送表示成功了
    
##  uri url urn

    uri=uri和urn
    
    uri uniform resource Identifier/统一资源标志符
    用来唯一标识互联网上的信息资源
    包括URL和URN
    
    utl uniform resource locator /统一资源定位器
    http://user:pass@host.com:80/path?query=string
    
    urn 永久统一资源定位符
    在资源移动之后还能被找到
    
## 跨域
     服务端允许跨域方法 
     response.writeHead(200,{
		'Access-Control-Allow-Origin':'*'
	})
     允许百度访问
     response.writeHead(200,{
		'Access-Control-Allow-Origin':'http://baidu.com'
	})
     客户端允许跨域
     <script src="http://127.0.0.1:8887"></script>
     浏览器允许在link,script,img标签写的请求跨域
     fetch('http://localhost:8887',{
		method: 'post',
		headers:{
			'X-Test-Cors':'123'
		}
	})
      服务端设置
      response.writeHead(200,{
		'Access-Control-Allow-Origin':'http://baidu.com',
		'Access-Control-Allow-Headers':'x-test-cors',
		'Access-Control-Allow-Methods':'POSt,put,Delete',
		'Access-Control-Max-Age':'1000',
	})
## 预请求 

    允许方法
    Get
    Head
    Post
    
    允许Content-type
    text/plain
    multipart/form-data
    application/x-www-form-urlencoded
    
    其他限制
    请求头限制
    fetch('http://localhost:8887',{
			method: 'post',
			headers:{
				'X-Test-Cors':'123'
			}
		})
 ## 缓存头Cache-Controlde 
    可缓存
    puiblic
    private
    no-cache(服务器验证之后可以缓存)
    到期
    max-age=<seconds>(浏览器做的缓存设置)
    s-maxage=<seconds>(代理服务器做的缓存时间设置)
    max-stale=<seconds>(使用过期的缓存)
    response.writeHead(200,{
		'Content-Type': 'text/javascript',
		'Cache-control':'max-age=200'
	})
    重新验证
    must-revalidate
    proxy-revalidate
    其他
    no-store(不能缓存)
    no-transform(不能转换)
## last-Modified
    上次修改时间
    配合If-Modified-Since或者If-Unmodified-Since使用
    对比上次修改时间以验证资源是否需要更新
## Etag
    数据签名
    配合if-Match或者if-Non-Match使用
    对比资源的签名判断时候使用缓存
    const etag = request.headers['if-none-match'];
		if(etag==777){
			console.log(etag)
			response.writeHead(304,{
				'Content-Type': 'text/javascript',
				'Cache-control':'max-age=200000, no-cache',
				'Last-Modified':'123',
				'Etag':'777'
			})
			response.end();
		}else{
			response.writeHead(200,{
				'Content-Type': 'text/javascript',
				'Cache-control':'max-age=200000, no-cache',
				'Last-Modified':'123',
				'Etag':'777'
			})
			response.end('console.log("script 1111111")');
		}  
		
## Cookie 属性
    max-age和expires设置过期时间
    Secure只在https的时候发送
    httpOnly无法通过document.cookie访问
    
## 数据协商
    分类
    请求
    返回
    //浏览器设置
    Accecp (定数据类型)
    Accept-Encoding(编码方式传输)
    Accept-Language(展示语言)
    User-Agent(端口页面 移动端和pc端)
    
    //服务端设置
    Cntent-type (数据格式)
    Content-Encoding
    Content-language 
## redirect
     if(request.url==='/'){
                //302 临时跳转 301 永久跳转区别浏览器会直接用/new跳转
		response.writeHead(302,{
			'Location':'/new'
		})
	}
	if(request.url==='/'){
		response.writeHead(200,{
			'Content-Type':'text/html'
		})
		response.end('<div>this is content</div>');
	}
   
    Vary 是一个HTTP响应头部信息，它决定了对于未来的一个请求头，应该用一个缓存的回复(response)还是向源服务器请求一个新的回复
    后端
    response.writeHead(304,{
				
				'Cache-control':'max-age=200000,  s-maxage=20 no-cache',
				 'Vary':'x-test-cache'
	})
	
	  fetch('http://localhost:8887',{
			method: 'post',
			headers:{
				'X-Test-cache':'123'
			}
		})
