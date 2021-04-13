# git指令


```
git config --global --unset http.proxy #取消代理
git commit --allow-empty-message -m "" #不加备注

# Failed to connect to github.com port 443: Timed out
git config --global --unset http.proxy
git config --global --unset https.proxy
# OpenSSL SSL_read: SSL_ERROR_SYSCALL, errno 10054
git config http.sslVerify "false"
```


