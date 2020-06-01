# nginx全局变量

"X-tenant-ID:$http\_x\_tenant\_id" **开发在前端的header添加x\_tenant\_id，nginx通过$http\_x\_tenant\_id获取值**

arg\_PARAMETER

## 这个变量包含GET请求中，如果有变量PARAMETER时的值。

args

## 这个变量等于请求行中\(GET请求\)的参数，如：foo=123&bar=blahblah;

binary\_remote\_addr

## 二进制的客户地址。

body\_bytes\_sent

## 响应时送出的body字节数数量。即使连接中断，这个数据也是精确的。

content\_length

## 请求头中的Content-length字段。

content\_type

## 请求头中的Content-Type字段。

cookie\_COOKIE

## cookie COOKIE变量的值

document\_root

## 当前请求在root指令中指定的值。

document\_uri

## 与uri相同。

host

## 请求主机头字段，否则为服务器名称。

hostname

## Set to themachine’s hostname as returned by gethostname

http\_HEADER is\_args

## 如果有args参数，这个变量等于”?”，否则等于”"，空值。

http\_user\_agent

## 客户端agent信息

http\_cookie

## 客户端cookie信息

limit\_rate

## 这个变量可以限制连接速率。

query\_string

## 与args相同。

request\_body\_file

## 客户端请求主体信息的临时文件名。

request\_method

## 客户端请求的动作，通常为GET或POST。

remote\_addr

## 客户端的IP地址。

remote\_port

## 客户端的端口。

remote\_user

## 已经经过Auth Basic Module验证的用户名。

request\_completion

## 如果请求结束，设置为OK. 当请求未结束或如果该请求不是请求链串的最后一个时，为空\(Empty\)。

request\_method

## GET或POST

request\_filename

## 当前请求的文件路径，由root或alias指令与URI请求生成。

request\_uri

## 包含请求参数的原始URI，不包含主机名，如：”/foo/bar.php?arg=baz”。不能修改。

scheme

## HTTP方法（如http，https）。

server\_protocol

## 请求使用的协议，通常是HTTP/1.0或HTTP/1.1。

server\_addr

## 服务器地址，在完成一次系统调用后可以确定这个值。

server\_name

## 服务器名称。

server\_port

## 请求到达服务器的端口号。

