### <b>ETCD</b>

beginner info:</br>
1- ETCD is a distributed reliable key, value store</br>
2- ETCD stores data in form of keys and values unlike the SQL database which stores tables and uses a structral representation of data</br>

     - Key      values
       Name     Himanjan
       Age      29
       Location UK
3- We can not use duplicate kyes
4- used to store small chunks of data that requires faster r/w operation

Installing ETCD as a service

** installing in ubuntu 18.04 or 20 lts

1- Download the binary and extract
   

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






