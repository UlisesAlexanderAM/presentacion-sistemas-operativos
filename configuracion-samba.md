## Configuracion de Samba

Configuración

La configuración de Samba en Linux es realizada a través de la edición de un solo archivo ubicado en /etc/samba/smb.conf. 

A continuación, un ejemplo de una configuración básica:

#============== Configuración global ===================#

[global] workgroup = PRUEBAGROUP

server string = Samba %v

wins support = no

load printers = no


#======= Seguridad =======#

security = user

map to guest = bad user

guest ok = yes

public = yes

hosts allow = 127.0.0.1 192.168.22.0/24

hosts deny = 0.0.0.0/0


#============== Definición comparticiones ==================#

[Musica] comment = Música prueba.

path = /home/Datos/Musica/

available = yes

browsable = yes

writable = no




[Videos] copy = Musica 

comment = Videos prueba.

path = /home/Datos/Videos/

[Box] copy = Musica

comment = Otros datos.

path = /home/Datos/Box/

writable = yes
