M ubuntu:trusty

RUN apt-get update && apt-get install -y openssh-server
RUN mkdir /var/run/sshd

# Setup ansible user
RUN useradd ansible -s /bin/bash
RUN mkdir -p /home/ansible/.ssh/
RUN chmod 0700 /home/ansible/.ssh/
COPY ssh_config /home/ansible/.ssh/config
COPY ansible /home/ansible/.ssh/id_rsa
COPY ansible.pub /home/ansible/.ssh/id_rsa.pub
COPY ansible.pub /home/ansible/.ssh/authorized_keys
RUN chown -R ansible:ansible /home/ansible/
RUN echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

ADD init-fake.conf /etc/init/fake-container-events.conf
RUN rm /usr/sbin/policy-rc.d; rm /sbin/initctl; dpkg-divert --rename --remove /sbin/initctl

RUN locale-gen en_US.UTF-8 && update-locale LANG=en_US.UTF-8

# remove some pointless services
RUN /usr/sbin/update-rc.d -f ondemand remove; \
  for f in \
    /etc/init/u*.conf \
    /etc/init/mounted-dev.conf \
    /etc/init/mounted-proc.conf \
    /etc/init/mounted-run.conf \
    /etc/init/mounted-tmp.conf \
    /etc/init/mounted-var.conf \
    /etc/init/hostname.conf \
    /etc/init/networking.conf \
    /etc/init/tty*.conf \
    /etc/init/plymouth*.conf \
    /etc/init/hwclock*.conf \
    /etc/init/module*.conf\
  ; do \
    dpkg-divert --local --rename --add "$f"; \
  done; \
  echo '# /lib/init/fstab: cleared out for bare-bones Docker' > /lib/init/fstab

ENV container docker

EXPOSE 22
CMD ["/sbin/init"]

