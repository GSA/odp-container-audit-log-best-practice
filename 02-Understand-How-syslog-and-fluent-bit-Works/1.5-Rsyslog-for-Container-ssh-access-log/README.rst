Rsyslog for Container Level System Log
==============================================================================

In this example, assuming you are managing a Server running many container applications. You want to use ``rsyslog`` to collect container audit log.

For ``rsyslog``, a ``rsyslog`` service is running on Server. And you need to change the config to allow it listen to UDP 514 port. Because your container won't have access to the ``/dev/log`` on the host, it has to use the network to talk to syslog on your host.

For docker, you need to specified ``log-driver=syslog`` when running your containers. Logs are stored on Host disk.

1. **Install and Run docker daemon**::

    # Install
    sudo amazon-linux-extras install docker

    # Run
    sudo service docker start

2. **Change rsyslog config**::

    # back up the default rsyslog.conf file
    sudo cp /etc/rsyslog.conf /etc/rsyslog.conf.bkp

    # change rsyslog.conf file
    vi /etc/rsyslog.conf

    # uncomment these two line, it should looks like
    $ModLoad imudp.so
    $UDPServerRun 514

3. **Run a test container using log driver** ``syslog``::

    # Reference: https://docs.docker.com/config/containers/logging/syslog/
    # invoke container system command, generate some log
    repo_name="ubuntu"
    sudo docker run --log-driver syslog --log-opt syslog-address=udp://127.0.0.1:514 ${repo_name} echo Hello World

    # view the most recent log
    sudo tail -n 10 /var/log/messages

    # container log will be something like this
    # 1a9223846c89 is the container id that generates the log
    # you can find host name information from $HOSTNAME env var
    Jan 28 03:09:10 localhost 1a9223846c89[3828]: Hello World

To allow ssh to a container, you need t install ssh server in your docker image first::

    # content of Dockerfile
    FROM ubuntu
    # SSH login fix. Otherwise user is kicked off after login
    RUN apt-get update
    RUN apt-get install openssh-server -y
    RUN echo 'root:pass' | chpasswd
    RUN mkdir /var/run/sshd
    RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

    EXPOSE 22
    CMD ["/usr/sbin/sshd", "-D"]

::

    # build docker mage
    sudo docker build . -t ubuntu:ssh

    # run the newly built image
    sudo docker run --rm -dt --name dev --log-driver syslog --log-opt syslog-address=udp://127.0.0.1:514 ubuntu:ssh

    # find the ip
    container_ip="$(sudo docker inspect -f "{{ .NetworkSettings.IPAddress }}" dev)"

    # ping it
    ping –c 3 172.17.0.2 # the ip address

    # ssh to it, it requires passwrd
    ssh root@172.17.0.2


Reference:

- https://phoenixnap.com/kb/how-to-ssh-into-docker-container