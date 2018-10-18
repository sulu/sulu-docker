FROM mysql:5.7

ARG MYSQL_DATABASE
ARG MYSQL_USER

# create test database that is used during test execution
ADD test-database.sql /etc/mysql/test-database.sql
RUN sed -i 's/MYSQL_DATABASE/'"$MYSQL_DATABASE"'/g' /etc/mysql/test-database.sql
RUN sed -i 's/MYSQL_USER/'"$MYSQL_USER"'/g' /etc/mysql/test-database.sql
RUN cp /etc/mysql/test-database.sql /docker-entrypoint-initdb.d/
