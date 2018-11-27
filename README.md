# RPC远程过程调用使用手记（hprose框架）
* 使用RPC在PHP、Golang微服务相互调用（使用hprose）
* PHP流行的RPC框架：phprpc，yar, thrift, gRPC, swoole, hprose

## hprose官方文档：
* [hprose官网](http://hprose.com/)
* [PHP RPC 服务端说明文档](https://github.com/hprose/hprose-php/wiki/06-Hprose-服务器)
* [PHP RPC 客户端说明文档](https://github.com/hprose/hprose-php/wiki/05-Hprose-客户端)
* [Golang RPC 服务端说明文档](https://github.com/hprose/hprose-golang/wiki/Hprose-服务器)
* [Golang RPC 客户端说明文档](https://github.com/hprose/hprose-golang/wiki/Hprose-客户端)


# 安装方法：
* PHP安装hprose：在composer.json文件添加"hprose/hprose": ">=2.0.0"，然后composer update即可
* Golang安装hprose：go get github.com/hprose/hprose-golang

## 使用方法：
> PHP服务端server.php：（使用php -f server.php运行并监听端口）
```
function hello($path, $params)
{
    echo "access php server: {$path} " . date('Y-m-d H:i:s') . PHP_EOL;
    return "access php server: {$path} " . date('Y-m-d H:i:s');
}

$server = new \Hprose\Socket\Server('tcp://0.0.0.0:1314');
$server->addFunction('hello');
$server->start();
```
---

> PHP客户端client.php：
```
$client = new \Hprose\Socket\Client('tcp://127.0.0.1:1314', false);
$result = $client->hello('Order.detail', 'jsonData');
```
---

> Golang服务端server.go：（使用go run server.go运行并监听端口）
```
package main

import (
    "fmt"

    "github.com/hprose/hprose-golang/rpc"
)

func main() {
    client := rpc.NewTCPClient("tcp://127.0.0.1:1313/")
    server.AddFunction("hello", hello)
    server.Start()
}

func hello(path string, params interface{}) string {
    fmt.Printf("access golang micro service: %s %s\n", path, time.Now().Format("2006-01-02 15:04:05"))
    return fmt.Sprintf("access golang micro service: %s %s\n", path, time.Now().Format("2006-01-02 15:04:05"))
}
```
---

> Golang客户端client.go
```
package main

import (
    "fmt"

    "github.com/hprose/hprose-golang/rpc"
)

type HandleService struct {
    Hello func(path, params string) (string, error)
}

func main() {
    var service *HandleService
    client := rpc.NewTCPClient("tcp://0.0.0.0:1313/")
    client.UseService(&service)
    result, _ := service.Hello("Goods.delete", "jsonData")
    fmt.Println(result)
    client.Close()
}
```
