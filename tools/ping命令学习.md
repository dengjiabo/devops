# ping命令学习

可以通过ping命令来查看网络连通性

ping ip 		直接在ping命令后面跟上ip地址，这个时候传输默认大小的数据包

ping ip -l 1500	在ip后面跟上-l参数，可以指定传输的数据包大小

路由器一般有一个默认的数据包大小 (1500)，最大传输单元：MTU

windowns 	查看MTU值

```
netsh interface ipv4 show subinterfaces
```

linux	查看MTU值

```
ifconfig
```

注意事项：如果在使用ping的指定数据包大小为1500，那么实际的数据包大小是超过1500的

ping ip -l 1472	1472对应的数据包大小正好为1500