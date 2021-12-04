# Base image https://hub.docker.com/u/rocker/
FROM centos:7
ENV container docker

# system libraries of general use
## install debian packages
USER root
RUN yum install -y https://releases.hashicorp.com/vagrant/2.2.9/vagrant_2.2.9_x86_64.rpm
RUN yum install -y net-tools

RUN yum -y update; yum clean all && \
    yum -y install openssh-server openssh-clients passwd sudo; yum clean all

RUN (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
    rm -f /lib/systemd/system/multi-user.target.wants/*; \
    rm -f /etc/systemd/system/*.wants/*; \
    rm -f /lib/systemd/system/local-fs.target.wants/*; \
    rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
    rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
    rm -f /lib/systemd/system/basic.target.wants/*; \
    rm -f /lib/systemd/system/anaconda.target.wants/*;

RUN mkdir /var/run/sshd; \
    ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key -N ''; \
    systemctl enable sshd.service; \
    sed -i '/^session.*pam_loginuid.so/s/^session/# session/' /etc/pam.d/sshd; \
    sed -i 's/Defaults.*requiretty/#Defaults requiretty/g' /etc/sudoers; \
    rm /usr/lib/tmpfiles.d/systemd-nologin.conf;

RUN useradd --create-home -s /bin/bash vagrant; \
    echo -e "vagrant\nvagrant" | (passwd --stdin vagrant); \
    echo 'vagrant ALL=(ALL) NOPASSWD: ALL' > /etc/sudoers.d/vagrant; \
    chmod 440 /etc/sudoers.d/vagrant

RUN mkdir -p /home/vagrant/.ssh; \
    chmod 700 /home/vagrant/.ssh
ADD https://raw.githubusercontent.com/hashicorp/vagrant/master/keys/vagrant.pub /home/vagrant/.ssh/authorized_keys
RUN chmod 600 /home/vagrant/.ssh/authorized_keys; \
    chown -R vagrant:vagrant /home/vagrant/.ssh

VOLUME [ "/sys/fs/cgroup" ]

CMD ["/usr/sbin/init"]

RUN yum install epel-release
RUN yum install R
RUN R -e \"install.packages(c('shiny', 'rmarkdown', 'devtools', 'RJDBC'), repos='http://cran.rstudio.com/')\"
RUN yum install --nogpgcheck https://download3.rstudio.org/centos5.9/x86_64/shiny-server-1.4.2.786-rh5-x86_64.rpm

# Start Shiny Server
RUN systemctl start shiny-server
RUN systemctl enable shiny-server



# copy necessary files
## app folder
COPY /example-app ./app
## renv.lock file
COPY /example-app/renv.lock ./renv.lock

# install renv & restore packages
RUN Rscript -e 'install.packages("renv")'
RUN Rscript -e 'renv::consent(provided = TRUE)'
RUN Rscript -e 'renv::restore()'

# expose port
EXPOSE 3838

# run app on container start
CMD ["R", "-e", "shiny::runApp('/app', host = '0.0.0.0', port = 3838)"]
