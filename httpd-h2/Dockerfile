FROM ubuntu:14.04
MAINTAINER Bluesoft Fire <devops@bluesoft.com.br>

# Install dependencies
RUN apt-get -q update && \
    apt-get -q -y --no-install-recommends install \
        ca-certificates \
        g++ \
        gcc \
        libpcre4-dev \
        make \
        wget && \
    rm -rf /var/lib/apt/lists/*

# Download apache apr and apr-util
RUN mkdir -p $HOME/src && \
    wget -nv http://ftp.unicamp.br/pub/apache/apr/apr-1.5.2.tar.gz -O - | tar -xz -C $HOME/src && \
    wget -nv http://ftp.unicamp.br/pub/apache/apr/apr-util-1.5.4.tar.gz -O - | tar -xz -C $HOME/src

# Download nghttp2
RUN wget -nv https://github.com/tatsuhiro-t/nghttp2/releases/download/v1.3.4/nghttp2-1.3.4.tar.gz -O - | tar -xz -C $HOME/src

# Download openssl
RUN wget -nv ftp://ftp.openssl.org/source/openssl-1.0.2d.tar.gz -O - | tar -xz -C $HOME/src

# Download apache
RUN wget -nv http://ftp.unicamp.br/pub/apache//httpd/httpd-2.4.17.tar.gz -O - | tar -xz -C $HOME/src

# Compile apache apr
RUN cd $HOME/src/apr-1.5.2/ && \
    ./configure --prefix=/usr/local/apr/ && \
    make && \
    make install

# Compile apache apr-util
RUN cd $HOME/src/apr-util-1.5.4/ && \
    ./configure --prefix=/usr/local/apr-util/ --with-apr=/usr/local/apr/ && \
    make && \
    make install

# Compile nghttp2
RUN cd $HOME/src/nghttp2-1.3.4/ && \
    ./configure --prefix=/usr/local/nghttp2/ && \
    make && \
    make install

# Compile openssl
RUN cd $HOME/src/openssl-1.0.2d/ && \
    ./config --prefix=/usr/local/openssl/ && \
    make && \
    make install

# Compile apache
RUN cd $HOME/src/httpd-2.4.17 && \
    ./configure \
        --prefix=/usr/local/httpd \
        --enable-http2 \
        --enable-http \
        --enable-ssl \
        --enable-ssl-staticlib-deps \
        --with-apr=/usr/local/apr \
        --with-apr-util=/usr/local/apr-util \
        --with-nghttp2=/usr/local/nghttp2 \
        --with-ssl=/usr/local/openssl \
        --enable-mods-static=ssl && \
    make && \
    make install

# Remove downloaded tarballs
RUN rm -rf $HOME/src

# Generate ssl key and certificate
RUN mkdir /usr/local/httpd/conf/ssl && \
    openssl genrsa -out /usr/local/httpd/conf/ssl/server.key 2048 && \
    openssl req -subj "/C=BR/ST=SP/L=São Paulo/O=Acme, Inc./OU=DevOps" -new -key /usr/local/httpd/conf/ssl/server.key -out /usr/local/httpd/conf/ssl/server.csr && \
    openssl x509 -req -days 365 -in /usr/local/httpd/conf/ssl/server.csr -signkey /usr/local/httpd/conf/ssl/server.key -out /usr/local/httpd/conf/ssl/server.crt

# Configure apache
COPY httpd.conf.docker /usr/local/httpd/conf/httpd.conf
RUN mkdir -p /usr/local/httpd/htdocs/

COPY httpd-vhost.conf.docker /usr/local/httpd/conf/extra/httpd-vhosts.conf

# Expose ports
EXPOSE 80 443

## Run apache
ENTRYPOINT [ "/usr/local/httpd/bin/httpd", "-k", "start", "-DFOREGROUND" ]
