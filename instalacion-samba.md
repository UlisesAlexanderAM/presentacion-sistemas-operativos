## Instalación de samba

apt-get install samba samba-common python-glade2 system-config-samba

Ahora lo que tendremos que hacer es editar el archivo /etc/samba/smb.conf, que es el que lleva toda la configuración de nuestro servidor Samba. Previo a esto hacemos un backup del archivo actual:

# cp /etc/samba.conf /etc/samba/smb.conf.back

Ahora si editamos el archivo principal:

# nano /etc/samba/smb.conf

Editamos la sección [global], que es en la que especificamos el nombre del grupo de trabajo, la cadena con la que se identifica en la red local, el nombre netbios, tipo de seguridad y demás. Lo dejamos como sigue (podemos cambiar los tres primeros parámetros si lo deseamos):

[global]
workgroup = GRUPODETRABAJO
server string = Servidor Samba %v
netbios name = ubuntu
security = user
map to guest = bad user
dns proxy = no

A continuación nos vamos bien abajo en el archivo, a la sección que dice ‘Share Definitions’ y que comienza con [Anonymous]. Allí añadimos (desde luego, podemos cambiar la ruta a la carpeta que vamos a compartir):

[Anonymous]
path = /samba/anonymous
browsable =yes
writable = yes
guest ok = yes
read only = no

Ahora reiniciamos el servidor Samba:

# service smbd restart

Un par de aspectos a considerar son el hecho de que la carpeta que vamos a ofrecer para acceso anónimo tiene que existir en nuestro sistema de archivos y además debe ser accesible para todos los usuarios, es decir que al listarla con:

ls -l

Nos debe mostrar permisos de lectura y ejecución para todos, es decir drwxr-xr-x, o 755 en la jerga numérica. Si no es el caso debemos hacer que así sea (cambiamos ‘carpetaacompartir’ por el nombre y ruta que deseemos):

# chmod -R 0755 /carpetaacompartir

Una vez que tenemos configuardo el acceso anónimo vamos a hacer lo propio con el acceso restringido por contraseña, y esto es algo que lleva un poco más de trabajo, así que vamos a empezar. En primer lugar, dado que en la configuración general establecimos que la seguridad es a través de user, esto significa que para acceder a las carpetas protegidas tendremos que hacerlo mediante nombre de usuario y contraseña que exista en el servidor Ubuntu 14.10 Utopic Unicorn, y por ello habrá que crear esa cuenta (podemos usar el nombre que deseemos, en lugar de usuariosamba como hemos hecho nosotros):

# useradd usuariosamba -G sambashare

Ingresamos la contraseña del usuario cuando se nos solicita, y luego añadimos la contraseña de samba:

# smbpasswd -a usuariosamba

También se nos pedirá que ingresemos la contraseña dos veces, tras lo cual el usuario que hemos creado ya tendrá su contraseña de Samba. Ahora debemos añadir las opciones de configuración para compartir una carpeta protegida mediante contraseña, así que volvemos a abrir para edición el archivo de configuración de Samba.

# nano /etc/samba/smb.conf

Añadimos:

[accesoseguro]
path = /home/samba/compartido
valid users = @sambashare
guest ok = no
writable = yes
browsable = yes

La carpeta /home/samba/compartido debe tener acceso de lectura, escritura y ejecución para todo el grupo sambashare, así que para esto vamos a ejecutar:

# chmod -R 0770 /home/samba/compartido

#chown -R root:sambashare /home/samba/compartido

Eso es todo, ya hemos podido configurar Samba y con ello podremos acceder a esta carpeta desde cualquier equipo de la red local que forme parte del grupo de trabajo GRUPODETRABAJO, y al hacerlo incluso podremos guardar la contraseña para un futuro acceso más rápido desde Windows, Mac OS X o desde otros equipos Linux.
