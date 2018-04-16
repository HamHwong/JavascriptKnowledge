# JSONP
## Introduce
> JSONP全称JSON with Padding，用于跨域调用。  
> 其本身是利用“Bug”来越过“同源策略”而导致的无法跨域的问题。
## Concept 
> 在服务端定义好API,访问时拼接成JS函数调用的模式，然后在前端引用该API作为外部js调用。
> 如定义API为  
> `GET 200 http://domain.com/getFunction?JSFunction=demo&param1=1&param2=2`  
> 服务端是实现字符串拼接和渲染数据  
> JSFunction + `"("`+ ServerCall(1,2) +`")"`  
> //这里通过服务器调用函数渲染出结果   
> << `demo(3)`  
> //返回demo(3)
> 前端引用api,然后定义`Function demo(result){}`
## Disadvantage
> 1. 只支持Get
> 2. 发送数据过于繁杂，后台不容易获取参数
> 3. 由于跨域，无法使用Session来确定登陆与否