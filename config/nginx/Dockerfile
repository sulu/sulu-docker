FROM debian:jessie

ARG PROJECT_DOMAIN

RUN apt-get update && apt-get install -y nginx gettext

ADD nginx.conf /etc/nginx/
ADD project.conf /etc/nginx/sites-available/

RUN sed -i 's#__PROJECT_DOMAIN__#'"${PROJECT_DOMAIN}"'#g' /etc/nginx/sites-available/project.conf
RUN cat /etc/nginx/sites-available/project.conf

RUN ln -s /etc/nginx/sites-available/project.conf /etc/nginx/sites-enabled/project
RUN rm /etc/nginx/sites-enabled/default

RUN echo "upstream php-upstream { server php:9000; }" > /etc/nginx/conf.d/upstream.conf

RUN usermod -u 1000 www-data

CMD ["nginx"]

EXPOSE 80
EXPOSE 443
