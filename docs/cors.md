## HTTP访问控制（CORS）

## 极为重要
CORS是浏览器的策略，只要是运行在浏览器上的程序，就必须要遵循该策略，浏览器是指（Chrome，IE，Firefox等等）

## 同等重要
1. CORS使用HTTP头来告诉浏览器是否允许跨域访问
2. HTTP头包含Request头和Response头
3. 是否允许跨域访问的头信息在Response头
4. Response头是HTTP服务器返回的
5. 结论：需要服务器的Response里面包含了允许跨域访问的头信息，客户端才能跨域访问资源

## 跨域相关的头信息
1. Access-Control-Allow-Origin *
2. Access-Control-Allow-Methods GET,POST,OPTIONS,
3. Access-Control-Allow-Headers *  
以上信息均需要服务器设置

## 其他客户端行为
1. 本地exe客户端，不存在跨域问题，可以直接进行HTTP访问
2. 服务器访问三方HTTP服务器，不存在跨域问题，可以直接进行HTTP访问

## web应用访问HTTP跨域解决
1. HTTP服务器设置跨域头
2. 如果HTTP服务器不愿意修改跨域信息，需要架设nginx代理服务器，使用nginx代理请求三方服务  
	客户端请求代理服务器=》代理服务器允许跨域=》代理服务器请求三方HTTP(服务器之间无跨域问题)=》将返回的数据返回给客户端
	
## Tips
1. OPTIONS是POST请求的时候，浏览器自主发起的行为；GET请求的时候没有OPTIONS
2. GET传参的方式是将参数接在URL后面;  content-type：application/x-www-form-urlencoded
2. POST传参是放在Body中，类型可以是JSON和FormData;  content-type:application/json 或者content-type:multipart/form-data