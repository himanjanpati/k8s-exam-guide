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



