# kubernetes 常用基础命令合集

1、get 命令

kubectl get node					--查询所有节点

kubectl get pod -n kube-public		--查询指定名字空间下的pod信息

kubectl get all -n kube-public		--查询指定名字空间下的所有资源（pod、rc、svc等）

kubectl get endpoint				--查询pod ip及其对外开放的端口

kubectl get pod -o wide 			--查询所有pod及其详细信息

kubectl get pods -l app=nginx		--通过标签进行查询

kubectl get rc,svc,namespace,pv,pvc    --可以通过get命令获取到所有的资源信息

2、describe命令

get命令可以只能获取到资源的一些简单信息，可以通过describe命令来获取详细信息

kubectl describe pod pod-name -n kube-public

3、create命令

kubectl create -f pod.yaml -n devtest			--通过yaml来创建资源的命令只此一条

4、delete命令

删除资源一般先get查到对应的资源名称，然后通过delete命令进行删除

kubectl delete pod pod-name -n devtest

kubectl delete pods,services -l name=myLabel	--通过标签进行筛选，批量删除

5、exec 命令

通过该命令可以进入到运行中的pod

kubectl exec -ti pod-name /bin/bash -n devtest

6、replace命令

修改yaml文件，然后再通过yaml文件来进行替换。（并不会一次性替换全部，先删掉第一个，等第一个创建好之后再删除第二个，依次）

kubectl replace -f rc-nginx.yaml

7、patch命令

此命令是在不影响pod运行的情况下替换修改某样属性

kubectl patch pod pod-name -p '{"metadata":{"labels":{"app":"nginx-3"}}}'

kubectl patch pod valid-pod --type='json' -p='[{"op": "replace", "path": "/spec/containers/0/image", "value":"new image"}]'		--修改镜像版本

8、scale命令

kubectl scale statefulset web --replicas=5		--扩展副本数

