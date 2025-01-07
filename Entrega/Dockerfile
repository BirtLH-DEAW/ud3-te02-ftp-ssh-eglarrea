# Utilizamos una imagen oficial de Ubuntu
FROM ubuntu:latest

# Damos información sobre la imagen que estamos creando
LABEL \
    version="1.0" \
    description="Ubuntu + Apache2 + virtual host" \
    maintainer="eglarrea <eglarrea@birt.eus>"

# Actualizamos la lista de paquetes e instalamos nano y apache2
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y nano apache2 proftpd proftpd-mod-crypto openssl openssh-server  git && \
    rm -rf /var/lib/apt/lists/*

# Creamos directorios  con la creacion de los usuario para los sitios web y configuraciones
#RUN mkdir -p /var/www/html/sitioPrimero /var/www/html/sitioSegundo 

RUN useradd eglarrea -d /srv/ftp -g nogroup --shell /usr/sbin/nologin
RUN useradd eglarrea1 -m -d /var/www/html/sitioPrimero -g www-data --password "$(openssl passwd -1 "eglarrea1")"  --shell /bin/false 
RUN useradd eglarrea2 -m -d /var/www/html/sitioSegundo -g www-data --password "$(openssl passwd -1 "eglarrea2")" --shell /bin/bash 

# Copiamos archivos al contenedor
COPY /web/indexPrimero.html /web/indexSegundo.html /web/sitioPrimero.conf /web/sitioSegundo.conf /web/sitioPrimero.key /web/sitioPrimero.cer /

COPY ["/ftp/proftpd.conf", "/etc/proftpd/proftpd.conf"] 
COPY ["/ftp/tls.conf", "/etc/proftpd/tls.conf"]
COPY ["/ftp/modules.conf", "/etc/proftpd/modules.conf"]
COPY ["/ssh/sshd_config", "/etc/ssh/sshd_config"]

#ID_RSA DE BIRT
ADD ["/ssh/id_rsa", "/root/.ssh/id_rsa"] 

#ID_RSA CREADO POR MI UN REPOSITORIO PRIVADO CON EL MISMO PROYECTO QUE TIENE BIRT
#ADD ["/sshpropio/id_rsa", "/root/.ssh/id_rsa"] 

# Movemos los archivos a sus ubicaciones adecuadas
RUN mv /indexPrimero.html /var/www/html/sitioPrimero/index.html && \
    mv /indexSegundo.html /var/www/html/sitioSegundo/index.html && \
    mv /sitioPrimero.conf /etc/apache2/sites-available/sitioPrimero.conf && \
    mv /sitioSegundo.conf /etc/apache2/sites-available/sitioSegundo.conf && \
    mv /sitioPrimero.key /etc/ssl/private/sitioPrimero.key && \
    mv /sitioPrimero.cer /etc/ssl/certs/sitioPrimero.cer

# Habilitamos los sitios y el módulo SSL
RUN a2ensite sitioPrimero.conf && \
    a2ensite sitioSegundo.conf && \
    a2enmod ssl

RUN openssl genpkey -algorithm RSA   -out /etc/ssl/private/proftpd.key -pkeyopt rsa_keygen_bits:4096
#Generamos la clave publica 
RUN openssl req -new -key /etc/ssl/private/proftpd.key -x509 -days 365 -out /etc/ssl/certs/proftpd.crt -subj "/C=ES/ST=Bizkaiz/L=Bizkaia/O=Egoitz Larrea Manzano/OU=Birt/CN=www.eglarrea-birt.eus" 

## Preparcion y clonacion de proyecto git
RUN eval "$(ssh-agent -s)" \
&& chmod 700 /root/.ssh/id_rsa  \
&& ssh-add /root/.ssh/id_rsa  \
&& ssh-keyscan -H github.com >> /root/.ssh/known_hosts \
#&& git clone git@github.com:eglarrea/Larrea_Manzano_Egoitz_DEAW03_02.git /srv/ftp/proyecto
&& git clone git@github.com:deaw-birt/UD3-ftp_anonimo.git /srv/ftp/proyecto


# Exponemos los puetoscd
EXPOSE 80
EXPOSE 443
EXPOSE 21 50000-50030
EXPOSE 33 

# Comando por defecto al iniciar el contenedor
HEALTHCHECK --interval=1s --timeout=3s \
CMD ["apache2ctl", "-D", "FOREGROUND"] 
CMD ["proftpd", "--nodaemon"] 
