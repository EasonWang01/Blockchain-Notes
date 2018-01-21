啟動

```
sudo docker daemon

// 如果有錯誤可先確認有沒有之前的沒停掉
sudo service docker stop

// 如果出現timeout無法啟動 可輸入如下
ps axf | grep docker | grep -v grep | awk '{print "kill -9 " $1}' | sudo sh

//如果docker run ... 出現找不到daemon但已經啟動了  在前面使用sudo run ...即可
```

使用docker hub可搜尋所有別人寫好的可用container  
[https://hub.docker.com/](https://hub.docker.com/)

## 1.列出所有執行中的container

```
docker ps
```

## 2.列出被使用過名稱的container

```
docker ps -a
```

## 3.停止特定container

輸入

```
docker stats
```

會顯示類似如下

```
CONTAINER           CPU %               MEM USAGE / LIMIT       MEM %               NET I/O             BLOCK I/O           PIDS
01f51f8c9f7b        30.80%              326.6 MiB / 1.952 GiB   16.34%              3.6 MB / 522 kB     9.84 MB / 0 B       12
```

接著

```
docker stop 01f51f8c9f7b
```

## 4.停止所有container

```
docker stop $(docker ps -a -q)
```

## 5.移除所有使用過的container名稱

在kill或stop container後要再把名稱移除才可再次重新使用

```
docker rm $(docker ps -q -f status=exited)
```

## 6.以名字顯示running中的container

```
docker stats $(docker ps --format={{.Names}})
```

# 7.進入特定Process的container bash

先輸入docker ps ，然後跟去PS ID輸入以下指令

```
 docker exec -i -t 665b4a1e17b6 /bin/bash
```



