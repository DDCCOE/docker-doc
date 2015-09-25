
#Build Docker from source code

## Official document

For Docker source code build, we can follow the instructions in this document:
[Setting Up a Dev Environment](https://docs.docker.com/v1.5/contributing/devenvironment)

## Some workarounds for build issues

1. Cannot pull docker image due to company firewall settings

   Docker run command can fail at image pull phase. As docker build needs ubuntu:latest image, we just need find a local ubuntu image and load it
   in VM,

		$ sudo docker save -o ./ubuntu.tar ubuntu:latest

   If we cannot find the local ubuntu image, we need pull ubuntu image from a network without firewall,

		$ sudo docker pull ubuntu:latest

   We can check ubuntu image by `docker images` command. Then save the image and copy it to your VM.

2. curl SSL certification verification errors

	Before build docker by clone our private repository,

		$ git clone https://github.com/DDCCOE/docker.git ccoe-docker
		
	Switch to hack branch,

		$ git checkout hack

	Or, just patch [this patch](https://github.com/DDCCOE/docker/commit/23ebf643d9d087f4f155c68ac176d4800d805bb6) against official code.
	The patch just try to add -k option for all of curl cli during build process.

3. go get SSL certification verification errors

	In company, we can access golang.org, but we still can hit this problem due to company firewall setting.
	As current docker source code uses the go package without --insecure option support, the go get can fail due to certification verification errors.

    Following two steps workaround could be used,

	* clone source code from VM, at docker source code directory,

		$ git clone https://github.com/golang/tools.git

	* vi Dockerfile, and modify it with following lines,

		#RUN go get golang.org/x/tools/cmd/cover
		ADD tools /go/src/golang.org/x/tools
		RUN go install golang.org/x/tools/cmd/cover

4. golang.org could bot be accessed outside the company

	This is China firewall problem, please use VPN to work around it. We may need use -x option for curl https proxy in Dockerfile.
