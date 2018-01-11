FROM nginx:alpine

ARG PROJECT_DOMAIN

ADD project.conf /etc/nginx/conf.d/

RUN sed -i 's#__PROJECT_DOMAIN__#'"${PROJECT_DOMAIN}"'#g' /etc/nginx/conf.d/project.conf
RUN echo "upstream php-upstream { server php:9000; }" > /etc/nginx/conf.d/upstream.conf
