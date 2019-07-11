FROM nginx

RUN usermod -u 1001 www-data

ADD ./config/default.conf /etc/nginx/conf.d/default.conf

RUN mkdir /app

WORKDIR /app

VOLUME /app

EXPOSE 8000

