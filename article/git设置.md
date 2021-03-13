git设置

1、设置账号密码

全局

```shell
git config --global user.name "qpzm7903";
git config --global user.email "yx372114301@qq.com"
```

局部

把--global去掉

2、设置github密钥

生成密钥

```shell
ssh-keygen -t rsa -b 4096 -C "yx372114301@qq.com"
```

密钥添加到github上

```shell
clip < ~/.ssh/id_rsa.pub
```

