FROM raspbian/jessie

LABEL maintainer "carcaret"

# UPDATE
RUN apt-get update && \
    rm raspberrypi.gpg.key

# TOOLS
RUN apt-get -y install build-essential git

# NGINX
RUN apt-get -y install libssl-dev nginx && \
    apt-get -y remove nginx && \
    apt-get -y clean nginx && \
    apt-get -y install libpcre3 libpcre3-dev && \
    git clone https://github.com/sergey-dryabzhinsky/nginx-rtmp-module.git && \
    wget http://nginx.org/download/nginx-1.13.6.tar.gz && \
    tar xvf nginx-1.13.6.tar.gz && \
    rm nginx-1.13.6.tar.gz && \
    cd nginx-1.13.6 && \
    ./configure --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-http_ssl_module --with-http_realip_module --with-http_addition_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_random_index_module --with-http_secure_link_module --with-http_stub_status_module --with-http_auth_request_module --with-mail --with-mail_ssl_module --with-file-aio --with-http_v2_module --with-ipv6 --with-threads --with-stream --with-stream_ssl_module  --with-http_slice_module --add-module=../nginx-rtmp-module && \
    make && \
    make install && \
    cd .. && \
    rm -rf nginx-rtmp-module && \
    rm -rf nginx-1.13.6 && \
    mkdir /var/cache/nginx && \
    touch /var/cache/nginx/client_temp && \
    adduser --no-create-home nginx

#LIBAV (FFMPEG)
RUN apt-get -y install libav-tools && \
    echo "alias ffmpeg=avconv" >> ~/.bashrc

COPY make_dirs.sh .

# PICAM
RUN apt-get -y install libharfbuzz0b libfontconfig1 && \
    chmod +x make_dirs.sh && \
    ./make_dirs.sh && \
    wget https://github.com/iizukanao/picam/releases/download/v1.4.6/picam-1.4.6-binary-jessie.tar.xz && \
    tar xvf picam-1.4.6-binary-jessie.tar.xz && \
    rm picam-1.4.6-binary-jessie.tar.xz && \
    mkdir -p /opt/picam && \
    cp picam-1.4.6-binary-jessie/picam /opt/picam/ && \
    ln -s /opt/picam/picam /usr/local/bin/picam && \
    rm -rf picam-1.4.6-binary-jessie && \
    rm make_dirs.sh

COPY requirements.txt .

# PYTHON
RUN apt-get -y install python3-dev python3-pyaudio python3-numpy redis-server && \
    wget https://bootstrap.pypa.io/get-pip.py && \
    python3 get-pip.py && \
    python3 -m pip install -r requirements.txt && \
    rm get-pip.py && \
    rm requirements.txt

COPY app.sh / 

CMD service nginx start && \
    service redis-server start && \
    /app.sh 
   
