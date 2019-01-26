

## [原文](https://itbilu.com/other/relate/EJ3fKUwUx.html#http-response-headers)

# HTTP消息头（HTTP headers）－常用的HTTP请求头与响应头


HTTP消息头是指，在超文本传输协议（ Hypertext Transfer Protocol ，HTTP）的请求和响应消息中，协议头部分的那些组件。
HTTP消息头用来准确描述正在获取的资源、服务器或者客户端的行为，定义了HTTP事务中的具体操作参数。

- 关于HTTP消息头

- 常用的HTTP请求头

- 常用的HTTP响应头

## 1. 关于HTTP消息头
HTTP消息头是在，客户端请求（Request）或服务器响应（Response）时传递的，
位请求或响应的第一行，HTTP消息体（请求或响应的内容）是其后传输。
HTTP消息头，以明文的字符串格式传送，是以冒号分隔的键/值对，如：Accept-Charset: utf-8，
每一个消息头最后以回车符(CR)和换行符(LF)结尾。
HTTP消息头结束后，会用一个空白的字段来标识，这样就会出现两个连续的CR-LF。

HTTP消息头由IANA（The Internet Assigned Numbers Authority，互联网数字分配机构）来整理和维护。
其标准最早来源于RFC 4229。IANA将其整理到了消息头文档，文档中还包括了一些新提出的信息头。

HTTP消息头支持自定义， 自定义的专用消息头一般会添加'X-'前缀。


## 2. 常用的HTTP请求头

<table class="table table-striped table-bordered">
	<thead>
		<tr>
			<th>
				协议头
			</th>
			<th>
				说明
			</th>
			<th>
				示例
			</th>
			<th>
				状态
			</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>
				Accept
			</td>
			<td>
				可接受的响应内容类型（<code>Content-Types</code>）。
			</td>
			<td>
				<code>Accept: text/plain</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Accept-Charset
			</td>
			<td>
				可接受的字符集
			</td>
			<td>
				<code>Accept-Charset: utf-8</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr id="Accept-Encoding">
			<td>
				Accept-Encoding
			</td>
			<td>
				可接受的响应内容的编码方式。
			</td>
			<td>
				<code>Accept-Encoding: gzip, deflate</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr id="Accept-Language">
			<td>
				Accept-Language
			</td>
			<td>
				可接受的响应内容语言列表。
			</td>
			<td>
				<code>Accept-Language: en-US</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr id="Accept-Datetime">
			<td>
				Accept-Datetime
			</td>
			<td>
				可接受的按照时间来表示的响应内容版本
			</td>
			<td>
				Accept-Datetime: Sat, 26 Dec 2015 17:30:00 GMT
			</td>
			<td>
				临时
			</td>
		</tr>
		<tr>
			<td>
				Authorization
			</td>
			<td>
				用于表示HTTP协议中需要认证资源的认证信息
			</td>
			<td>
				Authorization: Basic OSdjJGRpbjpvcGVuIANlc2SdDE==
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Cache-Control
			</td>
			<td>
				用来指定当前的请求/回复中的，是否使用缓存机制。
			</td>
			<td>
				<code>Cache-Control: no-cache</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Connection
			</td>
			<td>
				客户端（浏览器）想要优先使用的连接类型
			</td>
			<td>
				<code>Connection: keep-alive</code> 
				<p>
					<code>Connection: Upgrade</code> 
				</p>
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Cookie
			</td>
			<td>
				由之前服务器通过<code>Set-Cookie</code>（见下文）设置的一个HTTP协议Cookie
			</td>
			<td>
				<code>Cookie: $Version=1; Skin=new;</code> 
			</td>
			<td>
				固定：标准
			</td>
		</tr>
		<tr id="Content-Length">
			<td>
				Content-Length
			</td>
			<td>
				以8进制表示的请求体的长度
			</td>
			<td>
				<code>Content-Length: 348</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Content-MD5
			</td>
			<td>
				请求体的内容的二进制 MD5 散列值（数字签名），以 Base64 编码的结果
			</td>
			<td>
				Content-MD5: oD8dH2sgSW50ZWdyaIEd9D==
			</td>
			<td>
				废弃
			</td>
		</tr>
		<tr>
			<td>
				Content-Type
			</td>
			<td>
				请求体的MIME类型 （用于POST和PUT请求中）
			</td>
			<td>
				Content-Type: application/x-www-form-urlencoded
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Date
			</td>
			<td>
				发送该消息的日期和时间（以<a href="http://tools.ietf.org/html/rfc7231#section-7.1.1.1" target="_blank">RFC 7231</a>中定义的"HTTP日期"格式来发送）
			</td>
			<td>
				Date: Dec, 26 Dec 2015 17:30:00 GMT
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Expect
			</td>
			<td>
				表示客户端要求服务器做出特定的行为
			</td>
			<td>
				<code>Expect: 100-continue</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				From
			</td>
			<td>
				发起此请求的用户的邮件地址
			</td>
			<td>
				<code>From: user@itbilu.com</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Host
			</td>
			<td>
				表示服务器的域名以及服务器所监听的端口号。如果所请求的端口是对应的服务的标准端口（80），则端口号可以省略。
			</td>
			<td>
				<code>Host: www.itbilu.com:80</code> 
				<p>
					<code>Host: www.itbilu.com</code> 
				</p>
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				If-Match
			</td>
			<td>
				仅当客户端提供的实体与服务器上对应的实体相匹配时，才进行对应的操作。主要用于像 PUT 这样的方法中，仅当从用户上次更新某个资源后，该资源未被修改的情况下，才更新该资源。
			</td>
			<td>
				If-Match: "9jd00cdj34pss9ejqiw39d82f20d0ikd"
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				If-Modified-Since
			</td>
			<td>
				允许在对应的资源未被修改的情况下返回304未修改
			</td>
			<td>
				If-Modified-Since: Dec, 26 Dec 2015 17:30:00 GMT
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				If-None-Match
			</td>
			<td>
				允许在对应的内容未被修改的情况下返回304未修改（ 304 Not Modified ），参考 超文本传输协议 的实体标记
			</td>
			<td>
				If-None-Match: "9jd00cdj34pss9ejqiw39d82f20d0ikd"
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				If-Range
			</td>
			<td>
				如果该实体未被修改过，则向返回所缺少的那一个或多个部分。否则，返回整个新的实体
			</td>
			<td>
				If-Range: "9jd00cdj34pss9ejqiw39d82f20d0ikd"
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td style="white-space:nowrap;">
				If-Unmodified-Since
			</td>
			<td>
				仅当该实体自某个特定时间以来未被修改的情况下，才发送回应。
			</td>
			<td>
				If-Unmodified-Since: Dec, 26 Dec 2015 17:30:00 GMT
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Max-Forwards
			</td>
			<td>
				限制该消息可被代理及网关转发的次数。
			</td>
			<td>
				<code>Max-Forwards: 10</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Origin
			</td>
			<td>
				发起一个针对<a href="http://itbilu.com/javascript/js/VkiXuUcC.html" target="_blank">跨域资源共享</a>的请求（该请求要求服务器在响应中加入一个<code>Access-Control-Allow-Origin</code>的消息头，表示访问控制所允许的来源）。
			</td>
			<td>
				<code>Origin: http://www.itbilu.com</code> 
			</td>
			<td>
				固定: 标准
			</td>
		</tr>
		<tr>
			<td>
				Pragma
			</td>
			<td>
				与具体的实现相关，这些字段可能在请求/回应链中的任何时候产生。
			</td>
			<td>
				<code>Pragma: no-cache</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Proxy-Authorization
			</td>
			<td>
				用于向代理进行认证的认证信息。
			</td>
			<td>
				Proxy-Authorization: Basic IOoDZRgDOi0vcGVuIHNlNidJi2==
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr id="range-request-header">
			<td>
				Range
			</td>
			<td>
				表示请求某个实体的一部分，字节偏移以0开始。
			</td>
			<td>
				<code>Range: bytes=500-999</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Referer
			</td>
			<td>
				表示浏览器所访问的前一个页面，可以认为是之前访问页面的链接将浏览器带到了当前页面。<code>Referer</code>其实是<code>Referrer</code>这个单词，但RFC制作标准时给拼错了，后来也就将错就错使用<code>Referer</code>了。
			</td>
			<td>
				Referer: http://itbilu.com/nodejs
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr id="te-request-header">
			<td>
				TE
			</td>
			<td>
				浏览器预期接受的传输时的编码方式：可使用回应协议头<code>Transfer-Encoding</code>中的值（还可以使用"trailers"表示数据传输时的分块方式）用来表示浏览器希望在最后一个大小为0的块之后还接收到一些额外的字段。
			</td>
			<td>
				<code>TE: trailers,deflate</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				User-Agent
			</td>
			<td>
				浏览器的身份标识字符串
			</td>
			<td>
				<code>User-Agent: Mozilla/……</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Upgrade
			</td>
			<td>
				要求服务器升级到一个高版本协议。
			</td>
			<td>
				Upgrade: HTTP/2.0, SHTTP/1.3, IRC/6.9, RTA/x11
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Via
			</td>
			<td>
				告诉服务器，这个请求是由哪些代理发出的。
			</td>
			<td>
				Via: 1.0 fred, 1.1 itbilu.com.com (Apache/1.1)
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Warning
			</td>
			<td>
				一个一般性的警告，表示在实体内容体中可能存在错误。
			</td>
			<td>
				Warning: 199 Miscellaneous warning
			</td>
			<td>
				固定
			</td>
		</tr>
	</tbody>
</table>

## 3. 常用的HTTP响应头

<table class="table table-striped table-bordered">
	<thead>
		<tr>
			<th>
				响应头
			</th>
			<th>
				说明
			</th>
			<th>
				示例
			</th>
			<th>
				状态
			</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>
				Access-Control-Allow-Origin
			</td>
			<td>
				指定哪些网站可以<code>跨域源资源共享</code> 
			</td>
			<td>
				<code>Access-Control-Allow-Origin: *</code> 
			</td>
			<td>
				临时
			</td>
		</tr>
		<tr id="accept-patch-response-header">
			<td>
				Accept-Patch
			</td>
			<td>
				指定服务器所支持的文档补丁格式
			</td>
			<td>
				Accept-Patch: text/example;charset=utf-8
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr id="accept-ranges-response-header">
			<td>
				Accept-Ranges
			</td>
			<td>
				服务器所支持的内容范围
			</td>
			<td>
				<code>Accept-Ranges: bytes</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Age
			</td>
			<td>
				响应对象在代理缓存中存在的时间，以秒为单位
			</td>
			<td>
				<code>Age: 12</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Allow
			</td>
			<td>
				对于特定资源的有效动作;
			</td>
			<td>
				<code>Allow: GET, HEAD</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Cache-Control
			</td>
			<td>
				通知从服务器到客户端内的所有缓存机制，表示它们是否可以缓存这个对象及缓存有效时间。其单位为秒
			</td>
			<td>
				<code>Cache-Control: max-age=3600</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Connection
			</td>
			<td>
				针对该连接所预期的选项
			</td>
			<td>
				<code>Connection: close</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Content-Disposition
			</td>
			<td>
				对已知MIME类型资源的描述，浏览器可以根据这个响应头决定是对返回资源的动作，如：将其下载或是打开。
			</td>
			<td>
				Content-Disposition: attachment; filename="fname.ext"
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Content-Encoding
			</td>
			<td>
				响应资源所使用的编码类型。
			</td>
			<td>
				<code>Content-Encoding: gzip</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Content-Language
			</td>
			<td>
				响就内容所使用的语言
			</td>
			<td>
				<code>Content-Language: zh-cn</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr id="content-length-response-header">
			<td>
				Content-Length
			</td>
			<td>
				响应消息体的长度，用8进制字节表示
			</td>
			<td>
				<code>Content-Length: 348</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Content-Location
			</td>
			<td>
				所返回的数据的一个候选位置
			</td>
			<td>
				<code>Content-Location: /index.htm</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Content-MD5
			</td>
			<td>
				响应内容的二进制 MD5 散列值，以 Base64 方式编码
			</td>
			<td>
				Content-MD5: IDK0iSsgSW50ZWd0DiJUi==
			</td>
			<td>
				已淘汰
			</td>
		</tr>
		<tr id="content-range-response-header">
			<td>
				Content-Range
			</td>
			<td>
				如果是响应部分消息，表示属于完整消息的哪个部分
			</td>
			<td>
				Content-Range: bytes 21010-47021/47022
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Content-Type
			</td>
			<td>
				当前内容的<code>MIME</code>类型
			</td>
			<td>
				Content-Type: text/html; charset=utf-8
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Date
			</td>
			<td>
				此条消息被发送时的日期和时间(以<a href="http://tools.ietf.org/html/rfc7231#section-7.1.1.1" target="_blank">RFC 7231</a>中定义的"HTTP日期"格式来表示)
			</td>
			<td>
				Date: Tue, 15 Nov 1994 08:12:31 GMT
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				ETag
			</td>
			<td>
				对于某个资源的某个特定版本的一个标识符，通常是一个 消息散列
			</td>
			<td>
				ETag: "737060cd8c284d8af7ad3082f209582d"
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr id="Expires">
			<td>
				Expires
			</td>
			<td>
				指定一个日期/时间，超过该时间则认为此回应已经过期
			</td>
			<td>
				Expires: Thu, 01 Dec 1994 16:00:00 GMT
			</td>
			<td>
				固定: 标准
			</td>
		</tr>
		<tr>
			<td>
				Last-Modified
			</td>
			<td>
				所请求的对象的最后修改日期(按照 RFC 7231 中定义的“超文本传输协议日期”格式来表示)
			</td>
			<td>
				Last-Modified: Dec, 26 Dec 2015 17:30:00 GMT
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Link
			</td>
			<td>
				用来表示与另一个资源之间的类型关系，此类型关系是在<a href="https://tools.ietf.org/html/rfc5988" target="_blank">RFC 5988</a>中定义
			</td>
			<td>
				<code>Link: </code>; rel="alternate"
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Location
			</td>
			<td>
				用于在进行重定向，或在创建了某个新资源时使用。
			</td>
			<td>
				Location: http://www.itbilu.com/nodejs
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				P3P
			</td>
			<td>
				P3P策略相关设置
			</td>
			<td>
				P3P: CP="This is not a P3P policy!
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Pragma
			</td>
			<td>
				与具体的实现相关，这些响应头可能在请求/回应链中的不同时候产生不同的效果
			</td>
			<td>
				<code>Pragma: no-cache</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Proxy-Authenticate
			</td>
			<td>
				要求在访问代理时提供身份认证信息。
			</td>
			<td>
				<code>Proxy-Authenticate: Basic</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr id="PKP">
			<td>
				Public-Key-Pins
			</td>
			<td>
				用于防止中间攻击，声明网站认证中传输层安全协议的证书散列值
			</td>
			<td>
				Public-Key-Pins: max-age=2592000; pin-sha256="……";
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr id="Refresh">
			<td>
				Refresh
			</td>
			<td>
				用于重定向，或者当一个新的资源被创建时。默认会在5秒后刷新重定向。
			</td>
			<td>
				Refresh: 5; url=http://itbilu.com
			</td>
			<td>
			</td>
		</tr>
		<tr>
			<td>
				Retry-After
			</td>
			<td>
				如果某个实体临时不可用，那么此协议头用于告知客户端稍后重试。其值可以是一个特定的时间段(以秒为单位)或一个超文本传输协议日期。
			</td>
			<td>
				<ul>
					<li>
						示例1:Retry-After: 120
					</li>
					<li>
						示例2: Retry-After: Dec, 26 Dec 2015 17:30:00 GMT
					</li>
				</ul>
			</td>
			<td>
				<p>
					固定
				</p>
			</td>
		</tr>
		<tr>
			<td>
				Server
			</td>
			<td>
				服务器的名称
			</td>
			<td>
				<code>Server: nginx/1.6.3</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td id="innerlink_set-cookie">
				Set-Cookie
			</td>
			<td>
				设置<code>HTTP cookie</code> 
			</td>
			<td>
				Set-Cookie: UserID=itbilu; Max-Age=3600; Version=1
			</td>
			<td>
				固定: 标准
			</td>
		</tr>
		<tr>
			<td>
				Status
			</td>
			<td>
				通用网关接口的响应头字段，用来说明当前HTTP连接的响应状态。
			</td>
			<td>
				<code>Status: 200 OK</code> 
			</td>
			<td>
			</td>
		</tr>
		<tr id="trailer-response-header">
			<td>
				Trailer
			</td>
			<td>
				<code>Trailer</code>用户说明传输中分块编码的编码信息
			</td>
			<td>
				<code>Trailer: Max-Forwards</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr id="transfer-encoding-response-header">
			<td>
				Transfer-Encoding
			</td>
			<td>
				用表示实体传输给用户的编码形式。包括：<code>chunked</code>、<code>compress</code>、 <code>deflate</code>、<code>gzip</code>、<code>identity</code>。
			</td>
			<td>
				Transfer-Encoding: chunked
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Upgrade
			</td>
			<td>
				要求客户端升级到另一个高版本协议。
			</td>
			<td>
				Upgrade: HTTP/2.0, SHTTP/1.3, IRC/6.9, RTA/x11
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Vary
			</td>
			<td>
				告知下游的代理服务器，应当如何对以后的请求协议头进行匹配，以决定是否可使用已缓存的响应内容而不是重新从原服务器请求新的内容。
			</td>
			<td>
				<code>Vary: *</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Via
			</td>
			<td>
				告知代理服务器的客户端，当前响应是通过什么途径发送的。
			</td>
			<td>
				Via: 1.0 fred, 1.1 itbilu.com (nginx/1.6.3)
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr>
			<td>
				Warning
			</td>
			<td>
				一般性警告，告知在实体内容体中可能存在错误。
			</td>
			<td>
				Warning: 199 Miscellaneous warning
			</td>
			<td>
				固定
			</td>
		</tr>
		<tr id="www-authenticate-response-header">
			<td>
				WWW-Authenticate
			</td>
			<td>
				表示在请求获取这个实体时应当使用的认证模式。
			</td>
			<td>
				<code>WWW-Authenticate: Basic</code> 
			</td>
			<td>
				固定
			</td>
		</tr>
	</tbody>
</table>