#How to compose a docker image

## Steps:

1. pull images

[tant3@dd_docker test2]$ sudo docker pull centos

Note: because of firewall in company, docker hub is prevented from pulling images. We may need find a local image and load it.

2. list current images

  [tant3@dd_docker ~]$ sudo docker images
  REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
  mynewimage          latest              51512e0818c5        3 minutes ago       188.3 MB
  docker-dev          master              96f491f3b066        23 hours ago        1.907 GB
  <none>              <none>              29358f50e6c1        23 hours ago        1.907 GB
  <none>              <none>              40aef1aace35        3 days ago          1.907 GB
  <none>              <none>              c8f34c38c642        13 days ago         1.803 GB
  <none>              <none>              9a1d10421ee8        13 days ago         776.9 MB
  <none>              <none>              b0bee996294e        2 weeks ago         1.908 GB
  docker.io/ubuntu    14.04               91e54dfb1179        7 weeks ago         188.3 MB
  ubuntu              latest              91e54dfb1179        7 weeks ago         188.3 MB

3. save images locally

[tant3@dd_docker ~]$ sudo docker save -o /tmp/mynewimage.tar mynewimage

[tant3@dd_docker ~]$ ls -lht /tmp/mynewimage.tar
-rw-r--r--. 1 root root 189M Oct 13 08:19 /tmp/mynewimage.tar

4. load image from local path

[tant3@dd_docker ~]$ sudo docker load < /tmp/mynewimage.tar
[tant3@dd_docker ~]$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
mynewimage          latest              51512e0818c5        4 minutes ago       188.3 MB
docker-dev          master              96f491f3b066        23 hours ago        1.907 GB
<none>              <none>              29358f50e6c1        23 hours ago        1.907 GB
<none>              <none>              40aef1aace35        3 days ago          1.907 GB
<none>              <none>              c8f34c38c642        13 days ago         1.803 GB
<none>              <none>              9a1d10421ee8        13 days ago         776.9 MB
<none>              <none>              b0bee996294e        2 weeks ago         1.908 GB
docker.io/ubuntu    14.04               91e54dfb1179        7 weeks ago         188.3 MB
ubuntu              latest              91e54dfb1179        7 weeks ago         188.3 MB

5. run container by using image

[tant3@dd_docker ~]$ sudo docker run -t -i mynewimage /bin/bash
Usage of loopback devices is strongly discouraged for production use. Either use `--storage-opt dm.thinpooldev` or use `--storage-opt dm.no_warn_on_loop_devices=true` to suppress this warning.
root@26b5b508f085:/# hostname
26b5b508f085
root@26b5b508f085:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var 

6. custom our image/container (e.g. gem install kinds of packages like open4, log4r)

7. commint copy of container to an image

[tant3@dd_docker ~]$ sudo docker commit -m "Added ruby and gem" -a "Docker qa" 26b5b508f085 qa:v1
[sudo] password for tant3:
7fc0020ab9e4d4e74e4b477043d13683eb9d7508b193af5ebe1f52ef4e3011ed
[tant3@dd_docker ~]$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
qa                  v1                  7fc0020ab9e4        28 seconds ago      305.2 MB
mynewimage          latest              51512e0818c5        31 minutes ago      188.3 MB
docker-dev          master              96f491f3b066        23 hours ago        1.907 GB
<none>              <none>              29358f50e6c1        23 hours ago        1.907 GB
<none>              <none>              40aef1aace35        3 days ago          1.907 GB
<none>              <none>              c8f34c38c642        13 days ago         1.803 GB
<none>              <none>              9a1d10421ee8        13 days ago         776.9 MB
<none>              <none>              b0bee996294e        2 weeks ago         1.908 GB
docker.io/ubuntu    14.04               91e54dfb1179        7 weeks ago         188.3 MB
ubuntu              latest              91e54dfb1179        7 weeks ago         188.3 MB

