Create a docker base image has become a much easier work with the scripts support by docker.
But it is still a little tricky to create a CentOS 5 docker base image because those scripts cannot work well with CentOS5.
Here are two useable ways.

## Use febootstrap.

1. Install a CentOS5 virutal machine.

2. Install febootstrap tools on this machine. Because CentOS5 yum repository doesn't contain febootstrap, we need download RPM packages to install it.

3. Create the image.

            # mkdir centos5
            # febootstrap --names "bash" "ls" -o centos5
            (This means we will install bash and ls.)

4. Reconstruct the image.

            # febootstrap-supermin-help -f ext2 centos5 x86_64 kernel initrd root
            (root is the image we wanted.)

5. Then transfer the image to a host with docker engine.

6. create docker image.

            # mount root /mnt
            # cd /mnt
            # tar -c . | docker import - centos5base

## Use mkimage.sh

1. Install a CentOS5 virutal machine.

2. Run mkimage.sh script. Serval places to note:

>* Work dir must be abosulte path, or yum installroot will fail.
>* Need to build RPM DB by ourself.
>* Need to install a RPM reposity by ourself.

3. After that, a rootfs will be installed in the target work dir.
            
            # tar -czf /path/to/rootfs.tgz -C /path/of/target/work/dir .

4. Copy the tar to a docker host and create the docker image.
            
            cat /path/to/rootfs.tgz  | docker import - centosbase
