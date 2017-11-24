### Examen 3
**Universidad ICESI**  
**Curso:** Sistemas Operativos  
**Docente:** Daniel Barragán C.  
**Tema:** Descubrimiento de servicios, Microservicios  
**Correo:** daniel.barragan at correo.icesi.edu.co  
**Nombre:** Alejandro Bueno Cardona  
**Código:** A00335472  

### Objetivos
* Implementar servicios web que puedan ser consumidos por usuarios o aplicaciones
* Conocer y emplear tecnologías de descubrimiento de servicio

### Prerrequisitos
* Virtualbox o WMWare
* Máquina virtual con sistema operativo CentOS7
* Framework consul, zookeper o etcd

### Descripción
El tercer parcial del curso sistemas operativos trata sobre la creación de servicios web y el uso de tecnologías para el descubrimiento de servicio

![][1]
**Figura 1.** Despliegue básico de microservicios

### Actividades
1. Incluir nombre, código (5%)
2. Ortografía y redacción cuando sea necesario (5%)
3. Despliegue un esquema como el mostrado en la **figura 1**. Empleen un servicio web de su preferencia (puede usar alguno de los ejemplos de clase). No es necesario incluir los componentes para monitoreo (Elasticsearch, Kibana, Logstash) (30%)

Sigiendo la **figura 1**, se despliega el siguiente esquema

![][2]

#### Servidor de descubrimiento de servicio

Después de instalar las dependencias necesarias para el servidor (Guía so-discovery-service), se inicia el agente en modo servidor

![][3]

El servidor queda **alive** 

![][4]

###  Microservicio

Después de instalar las dependencias para el cliente (Guía so-discovery-service), se crea un ambiente de nombre iguazoservice y se activa
```
# su microservices
$ mkvirtualenv iguazoservice
$ work-on iguazoservice
```
Se instala la librería flask en el ambiente y se crea el script iguazoservice.py
```
$ pip install flask
$ vi iguazoservice.py
```
![][5]
Se inicia el microservicio
```
$ python iguazoservice.py
```
| Microservicio en ejecución | Prueba local |
|--- | --- |
| ![][6] | ![][7]  |

Se crea un archivo de configuración para el microservicio con un healthcheck
```
echo '{"service": {"name": "iguazoservice", "tags": ["flask"], "port": 8080,
  "check": {"script": "curl localhost:8080/health >/dev/null 2>&1", "interval": "10s"}}}' >/etc/consul.d/iguazoservice.json
```
Se inicia el agente en modo cliente
![][9]
Se une el cliente al ambiente de descubrimiento de servicio. Luego, se verifica la lista de miembros del ambiente
![][10]

### Balanceador de carga

Primero, se instalan las dependencias necesarias
```
$ sudo yum info haproxy
$ sudo yum install gcc pcre-static pcre-devel -y
$ wget https://www.haproxy.org/download/1.7/src/haproxy-1.7.8.tar.gz -O ~/haproxy.tar.gz
$ tar xzvf ~/haproxy.tar.gz -C ~/
$ cd ~/haproxy-1.7.8
$ make TARGET=linux2628
$ sudo make install
```
Se configura el HAProxy
```
$ sudo mkdir -p /etc/haproxy
$ sudo mkdir -p /var/lib/haproxy 
$ sudo touch /var/lib/haproxy/stats
$ sudo cp ~/haproxy-1.7.8/examples/haproxy.init /etc/init.d/haproxy
$ sudo chmod 755 /etc/init.d/haproxy
$ sudo systemctl daemon-reload
$ sudo chkconfig haproxy on
$ sudo useradd -r haproxy
```
Se configura el archivo **haproxy.cfg** y se reinicia el servicio
```
$ sudo vi /etc/haproxy/haproxy.cfg
$ sudo systemctl restart haproxy
```
![][11]
Se configura las plantillas de consul-template
```
# vi /etc/consul-template/haproxy.tpl
```
![][12]
Por último, se inicia el agente de consul-template (use una sesión de screen)
![][13]

Ahora, el cliente puede realizar peticiones hacia el balanceador, que se presenta como frontend del esquema implementado.

| Petición Load Balancer | Prueba desde Consul Server |
| --- | --- |
| ![][14] | ![][15] |

4. Adicione un microservicio igual al ya desplegado. Muestre a través de evidencias como las peticiones realizadas al balanceador son dirigidas a la replica del microservicio (30%)

Se agregan 3 microservicios al esquema. Cada uno fue implementado de manera similar al microservicio existente por los estudiantes
Luis Trochez, Ana Valderrama y Nicolás Recalde. 

![][25]

**La dirección .152, se cambió a .231**

| Microservicio 192.168.130.231 | Microservicio 192.168.130.157 | Microservicio 192.168.130.245 |
| --- | --- | --- |
| ![][16] | ![][17] | ![][18] |


Se verifica la lista de consul members
![][19]


Se realizan peticiones al balanceador, quien redirige a las replicas usando balanceo **roundrobin**

| Redirección a 192.168.130.231 | Redirección a 192.168.130.157 | Redirección a 192.168.130.245 | Redirección a 192.168.168.130.236 |
| --- | --- | --- | --- |
| ![][20] | ![][21] | ![][22] | ![][23] |


5. Describa los cambios o adiciones necesarias en el diagrama de la **figura 1** para adicionar un microservicio diferente al ya desplegado en el ambiente, tenga en cuenta los siguientes conceptos en su descripción: API Gateway, paradigma reactivo, load balancer, protocolo publicador/suscriptor (interconexión de microservicios) (20%)

En el esquema, se realiza una mínima analogía a una arquitectura con API Gateway pattern, que significa poner un API Gateway en frente de los microservicios y hacerlo el punto de entrada para cada solicitud del cliente (web, móvil, etc.). El API Gateway puede ser el balanceador de carga y viceversa, quien se encarga de realizar la interconexión de microservicios para el cliente. 

En el esquema, el load balancer fue el API Gateway también. Para agregar un microservicio diferente, se puede agregar un API Gateway a la arquitectura y liberarlo de responsabilidades de balanceo de carga. Se crea un nuevo balanceador de carga y discovery service para el microservicio. Por último, se crean y registran los clientes del nuevo microservicio.

![][24]


6. El informe debe ser entregado en formato pdf a través del moodle y el informe en formato README.md debe ser subido a un repositorio de github. El repositorio de github debe ser un fork de https://github.com/ICESI-Training/so-exam3 y para la entrega deberá hacer un Pull Request (PR) respetando la estructura definida. El código fuente y la url de github deben incluirse en el informe (10%)  

### Referencias
https://github.com/ICESI/so-microservices-python  
http://microservices.io/patterns/microservices.html
https://www.nginx.com/blog/microservices-api-gateways-part-1-why-an-api-gateway/
https://github.com/ICESI/so-discovery-service/blob/master/README.md

[1]: images/esquema.png
[2]: images/0.png
[3]: images/consul_agent_server.PNG
[4]: images/consul_logs.PNG
[5]: images/parcial3a.PNG
[6]: images/parcial3d.PNG
[7]: images/parcial3e.PNG
[9]: images/parcial3b.PNG
[10]: images/parcia3c.png
[11]: images/HAProxyActualizado.png
[12]: images/configuracionConsulTemplates.png
[13]: images/ActualzacionDinamicaBalanceador.png
[14]: images/DemostracionFuncionBalanceador.png
[15]: images/browser_alejo.PNG
[16]: images/6.PNG
[17]: images/4.PNG
[18]: images/5.PNG
[19]: images/parcial3c.PNG
[20]: images/loadbalancer3.JPG
[21]: images/lb157.png  
[22]: images/loadbalancer1.JPG  
[23]: images/loadbalancer2.JPG  
[24]: images/2.png  
[25]: images/1.png  
