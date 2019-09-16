# kubernetes-垃圾回收     
标签: kubernetes     

---

`kubernetes` 可以清理当前未使用的 `images`, 官方说每五分钟就会进行一次垃圾收集。

### 为什么要使用垃圾回收

`kubernetes` 会在启动 `pods` 时, 这时pods他所在的 `node` 会去拉取当前 `pods` 启动时的 `images`, 等 `pods` 在滚动更新或者死亡后, 就会产生过期的 `images` , 长期以往, `node` 上就会存在很多过期的 `images` 占用大量磁盘空间, `kubernetes` 默认在磁盘使用量达到 85% 的时候, 会发生资源不足(`pods` 无法正常运行), 如果这是在 `master` 节点上，那就更糟糕了, `kubernetes apiserver` 等组件也有可能会终止运行;     
     
由于 `kubelet` 需要通过容器来判断 `pod` 的运行状态, 所以建议不要使用外部垃圾收集工具, 不然有可能导致 `kubelet` 无法正常运行(亲测);     


### 配置 
用户可以使用以下 `kubelet` 标志调整以下阈值以调整 `images` 垃圾回收:   

| 参数 | 参数作用 | 默认值 |
| :-----: | :-----: | :-----: |
| `--image-gc-high-threshold` | 触发图像垃圾收集的磁盘使用百分比 | 默认值为 85％ |
| `--image-gc-low-threshold` | 图像垃圾回收尝试释放的磁盘使用百分比 | 默认值为 80％ |
     
在 `kubelet` 配置文件添加    

```shell
[root@node1 ~]# cat /etc/sysconfig/kubelet
KUBELET_EXTRA_ARGS=" \
--image-gc-high-threshold=65 \
--image-gc-low-threshold=50 \
```