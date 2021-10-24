https://gobyexample.com/



# 入门

## 安装

https://golang.org/doc/install



## helloworld



## go命令



模块



单元测试

文件名+_test.go

go test



打包

go build

go install

查看安装路径

```sh
go list -f '{{.Target}}'
```



将安装路径添加到环境变量

```sh
export PATH=$PATH:/path/to/your/install/directory
```



修改安装目录

```sh
go env -w GOBIN=/path/to/your/bin
```





依赖管理

https://golang.org/doc/modules/managing-dependencies



开发和发布

https://golang.org/doc/modules/developing







