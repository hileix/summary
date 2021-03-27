# git clone 慢解决方案

在翻墙的情况下，配置 `~/.ssh/config` 文件:

> 需要先 `brew install connect`

```
Host github.com *.github.com
    User git
    Port 22
    Hostname %h
    # 7890 修改为你 sockets 的端口
    ProxyCommand connect -S 127.0.0.1:7890 %h %p
```
