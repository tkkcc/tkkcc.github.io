## remote debugging

vscode, python远程调试, 笔记本<->集群master<->集群节点node

在笔记本上写代码,在集群节点上跑,需要远程调试.[vscode python debug](https://code.visualstudio.com/docs/python/debugging)介绍了一种方法,通过[ptvsd](https://github.com/Microsoft/ptvsd)模块来实现.集群节点上跑ptvsd作服务端,笔记本上用vscode连上就可以进行调试

这里涉及3个问题
1. 代码同步
2. 客户端服务端通信
3. 单边反复调试

### 代码同步
用sshfs挂载集群目录,改完直接F5触发自动保存然后调试.或者写个rsync同步任务,作为调试的preLaunchTask.我选择前者
```sh
mou h:code/code/ $H/code sshfs
```
实际执行
```sh
sshfs "$1" "$2" -o reconnect
```

### 通信
假设一种极端情况,集群(h)能ssh进,只有22端口可以访问,集群节点(node?)只能在集群中才能ssh进,因此需要ssh的LocalForward功能,先配置笔记本的`~/.ssh/config`
```sh
Host h
User bilabila
HostName 111.111.111.111
IdentityFile ~/.ssh/no
LocalForward 40056 127.0.0.1:40056
LocalForward 40066 127.0.0.1:40066
Host node?
ProxyCommand ssh h -W %h:%p
IdentityFile ~/.ssh/no
```
在ssh连接建立后,会把对笔记本40056端口的连接转发到集群的127.0.0.1:40056端口

再在集群的`~/.ssh/config`中,把对集群40056端口的连接转给节点的127.0.0.1:40056
```sh
Host node?
LocalForward 40056 127.0.0.1:40056
LocalForward 40066 127.0.0.1:40066
IdentityFile ~/.ssh/no
```

从笔记本ssh进集群,再在集群中ssh进节点,路由就OK了.

> 笔记本与集群节点也能通过ProxyCommand来连,但对我并不好使.在集群中应该先提交任务请求,分配到节点后再跑程序,因此在切换节点时,要在集群ssh中重新提交,再在笔记本(本地)上切换与集群节点的ssh连接.而使用两层人工ssh,只需把集群与集群节点的ssh加到任务提交前即可,无需本地再操作,使用`qsi`
>  ```sh
>  #!/bin/sh
>  ssh -nN node${1:-1}&
>  #qsub -I -l nodes=${1:-1}:ppn=${3:-4}:gpus=${2:-2} -N debug -S ~/.linuxbrew/bin/fish -X -d .;kill $!
>  qsub -I -l nodes=node${1:-1}:ppn=${3:-4}:gpus=${2:-2} -N debug -S ~/.linuxbrew/bin/fish -d .;kill $!
>  ```
> 例如要在node4上用1个gpu,2个cpu,就用`qsi 4 1 2`,它会先用ssh打通集群与节点的连接,再提交任务进入节点,等任务结束出来kill掉ssh


vscode中debug配置为python attach
```json
{
  "name": "40056",
  "type": "python",
  "request": "attach",
  "port": 40056,
  "host": "localhost",
  "pathMappings": [
    {
      "localRoot": "${workspaceFolder}", // You may also manually specify the directory containing your source code.
      "remoteRoot": "/home/bilabila/code/code" // Linux example; adjust as necessary for your OS and situation.
    }
  ]
}
```

### 调试

在集群节点上跑
```sh
python -m ptvsd --host 0.0.0.0 --port 40056 --wait 2.py
```
笔记本vscode按F5调试

每次调试需要重启节点上的ptvsd, 因此需要写个循环`loop`

```sh
#!/bin/bash
while :; do
    trap 'kill $!;tail --pid $! -f /dev/null' 2
    echo $(date)
    "$@"&
    tail --pid $! -f /dev/null
    trap 2
    sleep 1
done
```

在集群节点上跑
```
loop python -m ptvsd --host 0.0.0.0 --port 40056 --wait 2.py
```
一次`ctrl-c`重启ptvsd,连续两次退出循环

vscode里按F5,调试修改后再按F5重新调试.有时需要在节点中手动`ctrl-c`重启