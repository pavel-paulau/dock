FROM ubuntu:14.04

MAINTAINER Pavel Paulau <Pavel.Paulau@gmail.com>

# Serve HTTP and HTTPS traffic
EXPOSE 80 443

# Data volumes
VOLUME /etc/nginx/conf.d
VOLUME /etc/nginx/ssl
VOLUME /var/log/nginx
VOLUME /var/cache/nginx

# Versions
ENV NGINX_VERSION 1.9.5
ENV NPS_VERSION 1.9.32.6

# Install core dependencies
RUN apt-get update
RUN apt-get install -y apache2-utils build-essential libpcre3 libpcre3-dev libssl-dev unzip wget zlib1g-dev

# Pagespeed
WORKDIR /root
RUN wget -q https://github.com/pagespeed/ngx_pagespeed/archive/release-${NPS_VERSION}-beta.zip
RUN unzip release-${NPS_VERSION}-beta.zip

WORKDIR /root/ngx_pagespeed-release-${NPS_VERSION}-beta/
RUN wget -q https://dl.google.com/dl/page-speed/psol/${NPS_VERSION}.tar.gz
RUN tar -xzvf ${NPS_VERSION}.tar.gz

# Nginx
WORKDIR /root
RUN wget -q http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz
RUN tar -xvzf nginx-${NGINX_VERSION}.tar.gz

WORKDIR /root/nginx-${NGINX_VERSION}/
RUN ./configure \
	--user=nginx \
	--group=nginx \
	--prefix=/etc/nginx \
	--sbin-path=/usr/sbin/nginx \
	--conf-path=/etc/nginx/nginx.conf \
	--with-http_gzip_static_module \
	--with-http_v2_module \
	--with-http_ssl_module \
	--with-http_realip_module \
	--add-module=/root/ngx_pagespeed-release-${NPS_VERSION}-beta
RUN make
RUN make install
RUN useradd nginx

# Add configuration files
COPY conf/nginx.conf /etc/nginx/nginx.conf
COPY conf/pagespeed.conf /etc/nginx/pagespeed.conf

# Run nginx in foreground
CMD ["nginx"]
