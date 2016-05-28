FROM centos:latest
MAINTAINER y-sugasawa<aigis.developer@gmail.com>

# install required package
RUN yum update -y && yum install -y \
    openssh-server \
    openssh-clients \
    tar \
    bzip2 \
    make \
    git \
    gcc \
    openssl-devel \
    libyaml-devel \
    readline-devel \
    zlib-devel \
    mysql-devel

# user
RUN useradd webservice
RUN echo 'webservice:webservice!' | chpasswd
RUN echo 'webservice ALL=(ALL) ALL' >> /etc/sudoers

# Default
RUN mkdir -p /app/projects
RUN chmod 775 -R /app
RUN chown webservice:webservice -R /app

# ssh
RUN mkdir /var/run/sshd
RUN sed -ri 's/^#PermitRootLogin yes/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN echo 'root:root' | chpasswd
RUN ssh-keygen -t rsa -N "" -f /etc/ssh/ssh_host_rsa_key
#RUN sed -ri "s/^UsePAM yes/#UsePAM yes/" /etc/ssh/sshd_config
#RUN sed -ri "s/^#UsePAM no/UsePAM no/" /etc/ssh/sshd_config

# install rbenv, ruby-build
RUN git clone https://github.com/sstephenson/rbenv.git /usr/local/rbenv
RUN git clone https://github.com/sstephenson/ruby-build.git /usr/local/rbenv/plugins/ruby-build
ENV RBENV_ROOT /usr/local/rbenv
ENV PATH $RBENV_ROOT/bin:$PATH
ENV CONFIGURE_OPTS --disable-install-doc
RUN echo 'export RBENV_ROOT="/usr/local/rbenv"' >> /etc/profile.d/rbenv.sh
RUN echo 'PATH="$RBENV_ROOT/bin:$PATH"' >> /etc/profile.d/rbenv.sh
RUN echo 'export PATH' >> /etc/profile.d/rbenv.sh
RUN echo 'eval "$(rbenv init -)"' >> /etc/profile.d/rbenv.sh
RUN ./usr/local/rbenv/plugins/ruby-build/install.sh
RUN source /etc/profile.d/rbenv.sh

# install ruby
ADD ./ruby-version.txt /root/ruby-version.txt
RUN xargs -L 1 rbenv install < /root/ruby-version.txt
RUN xargs -L 1 rbenv global < /root/ruby-version.txt
RUN rbenv rehash

# install bundler
RUN rbenv exec gem install bundler
RUN rbenv rehash

# install rails
RUN rbenv exec gem install rails

# install node gulp
RUN git clone https://github.com/creationix/nvm.git /usr/local/nvm
RUN echo 'export NVM_ROOT="/usr/local/nvm"' >> /etc/profile.d/nvm.sh
RUN echo 'source $NVM_ROOT/nvm.sh' >> /etc/profile.d/nvm.sh
ADD ./node-version.txt /root/node-version.txt
RUN source /etc/profile.d/nvm.sh && nvm install `cat /root/node-version.txt`
RUN source /etc/profile.d/nvm.sh && npm install -g gulp

# port
EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]
