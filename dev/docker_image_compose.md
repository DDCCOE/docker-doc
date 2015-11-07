#How to compose a docker image

## Create your own images:

1. pull images

			[tant3@dd_docker test2]$ sudo docker pull centos

			Note: because of firewall in company, docker hub is prevented from pulling images. We may need find a 			local image and load it.

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
			Usage of loopback devices is strongly discouraged for production use. Either use `--storage-opt 			dm.thinpooldev` or use `--storage-opt dm.no_warn_on_loop_devices=true` to suppress this warning.
			root@26b5b508f085:/# hostname
			26b5b508f085
			root@26b5b508f085:/# ls
			bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  			var 

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


## Use docker compose to manage your container applications

We will use a apache web server applications to show how docker compose manages our services conveniently.
Some prepared works are needed done firstly.

1. Install docker-compose

We can download docker-compose binary and place it in our bin directory.
Please refer to this [link](https://docs.docker.com/compose/install)

2. Pull a apache image

Because docker hub is blocked by company firewall, we can get it by using docker mirorrs in our country.

            docker pull index.alauda.cn/library/httpd:latest
            [wangj85@dd_docker ~]$ sudo docker images
            [sudo] password for wangj85:
            REPOSITORY                      TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
            index.alauda.cn/library/httpd   latest              924d2d66a3b5        2 weeks ago         161.9 MB
            qa                              v1                  7fc0020ab9e4        3 weeks ago         305.2 MB
            mynewimage                      latest              51512e0818c5        3 weeks ago         188.3 MB
            docker-dev                      master              96f491f3b066        3 weeks ago         1.907 GB
            <none>                          <none>              29358f50e6c1        3 weeks ago         1.907 GB
            <none>                          <none>              40aef1aace35        4 weeks ago         1.907 GB
            <none>                          <none>              c8f34c38c642        5 weeks ago         1.803 GB
            <none>                          <none>              9a1d10421ee8        5 weeks ago         776.9 MB
            docker.io/ubuntu                14.04               91e54dfb1179        11 weeks ago        188.3 MB
            ubuntu                          latest              91e54dfb1179        11 weeks ago        188.3 MB

        
Now let us set up the apache server.

1. Create a work directory.

            $ mkdir httpd
            $ cd httpd

2. Create an example html file which will be our default page.

            $ mkdir public-html   
            $ cat public-html/index.html
              hello docker compose!

3. Create a httpd docker image

            $ cat Dockerfile
              FROM index.alauda.cn/library/httpd:latest
              COPY ./public-html  /usr/local/apache2/htdocs/

   This Dockerfile just tells docker build a image from index.alauda.cn/library/httpd:latest image and copy ./public-html to apache container default site directory. 


4. Define our service using docker-compose.yml

            $ cat docker-compose.yml
              web:
                  build: .

   It just includes one service web. It tells docker to build from current directory. You can refer to [compose file reference](http://docs.docker.com/compose/compose-file/) for more detail.

5. Use docker-compose start our service.

           $ docker-compose up
           Building web
           Step 0 : FROM index.alauda.cn/library/httpd:latest
            ---> 924d2d66a3b5
            Step 1 : COPY ./public-html /usr/local/apache2/htdocs/
             ---> fd3e50c77f47
             Removing intermediate container 277b7b1e2dc0
             Successfully built fd3e50c77f47
             Creating httpd_web_1
             Attaching to httpd_web_1
             web_1 | AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.59. Set the 'ServerName' directive globally to suppress this message
             web_1 | AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.59. Set the 'ServerName' directive globally to suppress this message
             web_1 | [Sat Nov 07 15:12:04.635115 2015] [mpm_event:notice] [pid 1:tid 139631174924160] AH00489: Apache/2.4.17 (Unix) configured -- resuming normal operations
             web_1 | [Sat Nov 07 15:12:04.636322 2015] [core:notice] [pid 1:tid 139631174924160] AH00094: Command line: 'httpd -D FOREGROUND'

6. Check our service.

            $ docker-compose ps
               Name           Command        State          Ports
               -------------------------------------------------------------
               httpd_web_1   httpd-foreground   Up      0.0.0.0:8080->80/tcp

    We can use curl to visit the server.
            $ curl 172.17.0.59
            hello docker compose!

7. Stop and clean our service.
            
            $ docker-compose stop
            Stopping httpd_web_1 ... done
            $ docker-compose rm -f
            Removing httpd_web_1 ... done


Actually, we can define more services in docker-compose.yml and mutual operations between services are allowed. More complicated examples are [here](https://docs.docker.com/compose/gettingstarted/)
