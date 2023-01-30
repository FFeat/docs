# git语句

> 处理遇到的场景，记录了下来

推送到远程库

    git push -u <https://github.com/FFeat/docs.git>  master

## 同时存在远程库和本地库，怎么关联绑定？

直接强制合并

``` git

    git pull 远程库git地址  master  --allow-unrelated-histories
```
