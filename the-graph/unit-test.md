# Unit test

這邊使用 matchstick 框架來進行。

{% embed url="https://github.com/LimeChain/matchstick" %}

可參考範例：[https://github.com/LimeChain/demo-subgraph](https://github.com/LimeChain/demo-subgraph)

## 執行方式

1. `git clone git@github.com:LimeChain/demo-subgraph.git`
2. 修改 DockerFile 12 行 matchstick version
3. ```
   docker build -t matchstick .
   ```
4. `docker run -e ARGS="gravity" -it --rm --mount type=bind,source=<absolute/path/to/project>,target=/matchstick matchstick`

之後修改 mapping 檔案後重複執行第四個指令即可
