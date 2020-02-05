基于rook v1.2.3 & gaozhiwen改过的镜像 参考https://rook.io/docs/rook/v1.2/ceph-cluster-crd.html#external-cluster 使用外部ceph rbd集群 只测了rbd

## workdir cluster/examples/kubernetes/ceph

#### 1. 第一步 生成crd 

Kubectl apply -f common.yaml 此处无修改

#### 2. 启动rook operator

kubectl apply -f operator.yaml

此处镜像地址版本有修改 代码可@gaozhiwen

![image-20200205154715051](/Users/swift/Library/Application Support/typora-user-images/image-20200205154715051.png)



![image-20200205154847275](/Users/swift/Library/Application Support/typora-user-images/image-20200205154847275.png)

##### By gaozhiwen ->

目前的 ceph 集群需要关闭 ceph.conf 认证，ceph.conf 文件是在 r.qihoo.cloud/gcr.io/google_containers/cephcsi/cephcsi:v1.2.1 镜像中写死的，目前修改了 cephcsi 代码，使用 v1.2.1-qihoo tag 



![image-20200205155022331](/Users/swift/Library/Application Support/typora-user-images/image-20200205155022331.png)

手动制定metrics端口 默认是9090与prometheus有冲突

#### 3. 导入环境变量 具体值可找hulk同学索要


export NAMESPACE=rook-ceph && export ROOK_EXTERNAL_FSID=xxx && export ROOK_EXTERNAL_ADMIN_SECRET=xxx&& export ROOK_EXTERNAL_CEPH_MON_DATA=xxx

bash import-external-cluster.sh

#### 4. 创建外部集群 

kubectl apply -f cluster-external.yaml

此处有修改 修改了namespace 以及去掉了镜像版本

![image-20200205155645653](/Users/swift/Library/Application Support/typora-user-images/image-20200205155645653.png)

#### 5. 出现下图就是创建成功

![image-20200205160007847](/Users/swift/Library/Application Support/typora-user-images/image-20200205160007847.png)

#### 6.创建一个pool

kubectl apply -f pool.yaml

![image-20200205160822672](/Users/swift/Library/Application Support/typora-user-images/image-20200205160822672.png)

#### 7. 创建一个测试的pod挂载一个pvc 这个里面创建了pvc pod storageclass

kubectl apply -f pod.yaml

反复删除这个pod 节点漂移也不会导致数据丢失 则说明挂载成功

