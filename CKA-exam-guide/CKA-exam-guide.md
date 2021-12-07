### <b>ETCD</b>

<b>Basic info:</b></br>
1- ETCD is a distributed reliable key, value store</br>
2- ETCD stores data in form of keys and values unlike the SQL database which stores tables and uses a structral representation of data</br>

     - Key      values
       Name     Himanjan
       Age      29
       Location UK
3- We can not use duplicate kyes</br>
4- used to store small chunks of data that requires faster r/w operation

<i>Installing ETCD as a service</i>

** installing in ubuntu 18.04 or 20 lts

1- Download the binary and etextract
   

    export RELEASE=3.5.1

    wget https://github.com/etcd-io/etcd/releases/download/v${RELEASE}/etcd-v${RELEASE}-linux-amd64.tar.gz

    tar xvf etcd-v${RELEASE}-linux-amd64.tar.gz

    cd etcd-v3.5.1-linux-amd64/

    sudo mv etcd etcdctl /usr/local/bin

    etcd --version
    etcd Version: 3.5.1
    Git SHA: e8732fb5f
    Go Version: go1.16.3
    Go OS/Arch: linux/amd64

2- configure ETCD as a service

    sudo mkdir -p /var/lib/etcd
    sudo mkdir -p /etc/etcd

    sudo groupadd --system etcd
    sudo useradd -s /sbin/nologin --system -g etcd etcd

    sudo chown -R etcd:etcd /var/lib/etcd/

    sudo vim /etc/systemd/system/etcd.service

    [Unit]
    Description=etcd key-value store
    Documentation=https://github.com/etcd-io/etcd
    After=network.target

    [Service]
    User=etcd
    Type=notify
    Environment=ETCD_DATA_DIR=/var/lib/etcd
    Environment=ETCD_NAME=%m
    ExecStart=/usr/local/bin/etcd
    Restart=always
    RestartSec=10s
    LimitNOFILE=40000

    [Install]
    WantedBy=multi-user.target


    sudo systemctl  daemon-reload
    sudo systemctl  start etcd.service
    sudo systemctl  status etcd.service

3- verify the ETCD service running in port 2379

    ss -tunelp | grep 2379
    tcp   LISTEN  0        4096              127.0.0.1:2379           0.0.0.0:*      uid:999 ino:1039294 sk:a <-> 

    etcdctl member list
    8e9e05c52164694d, started, d0d184b989e71cb2951e97e4f9d12bba, http://localhost:2380, http://localhost:2379, false

<b>Advance info:</b></br>
1- ETCD acts as main central memory for k8s cluster, stores information on all the k8s objects (deployments, nodes, pods, services etc.)</br> 
2- kubectl get command retieves the information from ETCD server</br>
3- Creating PODs, adding nodes gets updated in the ETCD database and a successful update result in change to complete</br> 
4- --advertise-client-urls https://${internal_ip}:2379 address where ETCD listens in k8s cluster. This URL is configured in kube-apiserver so that it can connect to ETCD</br> 
5- Using kubeadm tool ETCD will be deployed to k8s as a pod</br> 
6- Below command will show the k8s objects (keys) stored in ETCD</br> 
   
   <b>The command has to be run in controlplane</b>
        
    kubectl get po -n kube-system
    
    kubectl exec <etcd_pod_name> -n kube-system -- sh -c "ETCDCTL_API=3 etcdctl get / --prefix --keys-only --limit=10 --cacert /etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/etcd/server.crt  --key /etc/kubernetes/pki/etcd/server.key"

7- The above commnd will show the structure in which ETCD stores the k8s objects under the registry directory

<b>/registry/<k8s_object_type>/<name_space>/<k8s_object_name></b>

    /registry/pods/default/<pod_name>
    /registry/pods/kube-system/kube-flannel-ds-8rzd2
    /registry/roles/kube-system/kube-proxy
    /registry/apiregistration.k8s.io/apiservices/v1.apps
    /registry/deployments/default/nginx-webapp

8- In HA k8s cluster having multiple ETCD master nodes the --intial-cluster has to be set. This will ensure each ETCD nodes running master nodes can talk to each other.

<b>k8s kubeadm installation: check the etcd.yaml of k8s manifets in controlplane node for the parameters specified for etcd or kubectl describe po <apiserver_pod_name> -n kube-system</b>


    cat /etc/kubernetes/manifests/etcd.yaml 

    spec:
    containers:
    - command:
        - etcd
        - --advertise-client-urls=https://10.240.0.10:2379
        - --cert-file=/etc/kubernetes/pki/etcd/server.crt
        - --client-cert-auth=true
        - --data-dir=/var/lib/etcd
        - --initial-advertise-peer-urls=https://10.240.0.10:2380
        - --initial-cluster=controller-0=https://10.240.0.10:2380
        - --key-file=/etc/kubernetes/pki/etcd/server.key
        - --listen-client-urls=https://127.0.0.1:2379,https://10.240.0.10:2379
        - --listen-metrics-urls=http://127.0.0.1:2381
        - --listen-peer-urls=https://10.240.0.10:2380
        - --name=controller-0
        - --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
        - --peer-client-cert-auth=true
        - --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
        - --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
        - --snapshot-count=10000
        - --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt









