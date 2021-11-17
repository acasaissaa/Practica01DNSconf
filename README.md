# Práctica 1 - DNS Linux: MEMORIA DEL CONF

## Configurar la zona y comprobar que funciona.

Primero ubicamos el volumen asociado al directorio /etc/bind/, hacemos click derecho y le damos a Explore in a Development Container. El primer archivo que modificaremos será **named.conf.options**, donde descomentaremos estas líneas:
~~~
forwarders {
    8.8.8.8;
    4.4.4.4;
};
~~~
Estas lineas indican al servidor que esas ips son los servidores dns a los que enviaremos una solicitud. Para confirmar los datos, reiniciamos el contenedor: 
- docker restart Contenedor

Ahora hay que modificar el archivo **named.conf.local**, que nos permite agregar una zona DNS, convirtiendo a nuestro servidor en primario de la zona.
En dicho archivo colocaremos estas lineas, las cuales establecen una zona llamada example.com en el archivo:
~~~
zone "example.com" {
    type master;
    file "/etc/bind/db.example.com";
};
~~~
Después, creamos el archivo db.example.com el cual contiene toda la configuración de la zona y es donde se establece el nombre del servidor maestro (example.com) y el correo electronico del administrador usando el registro SOA:
~~~
;
; BIND data file for example.com
;
$TTL	604800
@	IN	SOA	example.com. root.example.com. (
			      2		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
;
@	IN	NS	ns.example.com.
@	IN	A	172.27.0.2
@	IN	AAAA	::1
ns  IN  A   172.27.0.2
aaa	IN	A	10.80.89.81
ejemplo1	IN 	A 	10.80.89.80
~~~

Tenemos que establecer el ns.example.com. y darle la ip que le corresponde. Por último, tenemos que reiniciar el contenedor (docker restart Contenedor) para confirmar los cambios.

Ahora, desde una shell del cliente, realizamos una solicitud dns con dig para preguntar por la ip del dominio:
~~~
dig aaa.example.com
~~~
Al principio la respuesta será 127.0.0.11, pero esto puede simplemente deberse al servicio systemd encargado de la configuración.

Otro sistema de comprobación es utlizando ping, el cual se ejecutará perfectamente si el servidor responde correctamente:
~~~
ping aaa.example.com
~~~
