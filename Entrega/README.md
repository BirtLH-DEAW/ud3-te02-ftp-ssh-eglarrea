## Crear imagen
docker build -t larreamanzanoegoitzdeaw0302 . 

## Arrancar el contenedor
docker run -itd --name larreamanzanoegoitzdeaw0302 -p 21:21 -p 50000-50030:50000-50030 -p 8080:80 -p 443:443 -p 33:33 larreamanzanoegoitzdeaw0302  

## Arrancar el ssh
docker exec -it <id_contenedor> bash   

service ssh start


## Usuarios
tunombre1-->eglarrea1 pass:eglarrea1 (ftp)
tunombre2-->eglarrea2 pass:eglarrea2 (ssh)
tunombre->eglarrea (ftp como anónimo)
