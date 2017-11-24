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

Sigiendo la **figura 1**, se despliega el siguiente sistema

poner pic

#### Servidor de descubrimiento de servicio

Después de instalar las dependencias necesarias para el servidor (Guía so-discovery-service), se inicia el agente en modo servidor

poner pic

El servidor queda **alive** 

poner pic

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
ponerpic
Se inicia el microservicio
```
$ python iguazoservice.py
```
| Microservicio en ejecución | Prueba local |
|--- | --- |
| xdxd | xdxd  |
Se crea un archivo de configuración para el microservicio con un healthcheck
poner
Se inicia

4. Adicione un microservicio igual al ya desplegado. Muestre a través de evidencias como las peticiones realizadas al balanceador son dirigidas a la replica del microservicio (30%)
5. Describa los cambios o adiciones necesarias en el diagrama de la **figura 1** para adicionar un microservicio diferente al ya desplegado en el ambiente, tenga en cuenta los siguientes conceptos en su descripción: API Gateway, paradigma reactivo, load balancer, protocolo publicador/suscriptor (interconexión de microservicios) (20%)
6. El informe debe ser entregado en formato pdf a través del moodle y el informe en formato README.md debe ser subido a un repositorio de github. El repositorio de github debe ser un fork de https://github.com/ICESI-Training/so-exam3 y para la entrega deberá hacer un Pull Request (PR) respetando la estructura definida. El código fuente y la url de github deben incluirse en el informe (10%)  

### Referencias
https://github.com/ICESI/so-microservices-python  
http://microservices.io/patterns/microservices.html
