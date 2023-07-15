# Use the official CentOS 7 base image
FROM centos:7

# Update packages and install necessary dependencies
RUN yum -y update && \
    yum -y install epel-release && \
    yum -y install wget && \
    yum -y install httpd mariadb mariadb-server && \
    yum clean all

# Install PHP and necessary extensions
RUN yum -y install http://rpms.remirepo.net/enterprise/remi-release-7.rpm && \
    yum -y install yum-utils && \
    yum-config-manager --enable remi-php74 && \
    yum -y install php php-cli php-common php-mysqlnd php-pdo php-gd php-xml php-mbstring php-json

# Install phpMyAdmin
RUN wget https://files.phpmyadmin.net/phpMyAdmin/5.1.1/phpMyAdmin-5.1.1-all-languages.tar.gz && \
    tar xzf phpMyAdmin-5.1.1-all-languages.tar.gz && \
    rm phpMyAdmin-5.1.1-all-languages.tar.gz && \
    mv phpMyAdmin-5.1.1-all-languages /var/www/html/phpmyadmin

# Configure phpMyAdmin to work with Apache
RUN echo "Include /etc/httpd/conf.d/phpmyadmin.conf" >> /etc/httpd/conf/httpd.conf

# Expose ports for Apache, MariaDB, and phpMyAdmin
EXPOSE 80 3306

# Start Apache and MariaDB services
CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"] && ["/usr/bin/mysqld_safe"]
