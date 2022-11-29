
# IIC2173 - E3 - ERT MMCSIT

*aka. Equipos de respuesta temprana, Multiverse metacombination to cultivate synergistic integrated teamsa*


## Objetivo

La entrega está intencionada para que apliquen conceptos aprendidos a lo largo del semestre y puedan integrarse con otros grupos. Además, aprenderán a integrar y mejorar un servicio preparado

## Enunciado

Ahora que su entrega está más sólida, van por el último plan, y el que aporta más valor a su idea. Van a integrar un chat preensamblado, open source para que los usuarios puedan comunicarse y conversar sobre las alertas de importancia, añadiendo verificaciones. Con este nuevo hit de interactividad esperan tener una plataforma lista para salir al mercado. Para lograr la parte colaborativa mencionada la entrega pasada, se les pide que los usuarios de otros grupos puedan acceder a estos chats.

Adicionalmente, se les pide que incorporen ciertas mejoras para lograr una entrega más solida y extensible, utilizando IaC.


## Especificaciones

**Si un requisito está marcado como *crit*, el no cumplirlo en un grado mínimo (al menos un punto) reducirá la nota máxima a *4.0*. NO se revisaran entregas que no estén en la nube**

Por otro lado, debido a que esta entrega presenta una buena cantidad de *bonus*, la nota no puede sumar más de 8, para que decidan bien que les gustaría aprovechar.

Para esta entrega, deben completar todos los requisitos de la entrega pasada marcados como **Esencial**, puesto que son necesarios para completar esta entrega. **Cada feature *Esencial* faltante, incurre en un descuento de *0.4 pto***

Al final de la entrega, la idea es que se pongan de acuerdo con su ayudante para agendar una hora y hacer una demo en vivo para su corrección.

### Requisitos funcionales (16 ptos)

* ***2 ptos*** Debe poder designar usuarios como "Verified responder". Esto debe ser un booleano en su chat

* ***2 ptos*** Cada usuario debe tener asignado un UUID v4. Esto es necesario para poder conectarlos a otro equipo con una mínima probabilidad de colisión

* Deben integrar el chat con su app:
    * ***3 ptos*** Debe haber un room general para que cualquier usuario identificado pueda conectarse. Los usuarios deben poder ver el historial de ese chat (Pueden mostrar los 1000 últimos mensajes)
    * ***5 ptos*** Deben mostrar una lista de salas disponibles, y poder unirse a ellas. Indicar quienes están conectados a cada sala en tiempo real tiene un bonus de ***5 ptos***
    * ***4 ptos*** Cada alerta con level superior a 9000 debe generar una sala de chat a la que se puedan unir usuarios designados como "Verified responder"
    * Deben usar Secure websockets para el chat, con Let's encrypt sobre nginx, configurado como reverse proxy

### Requisitos No Funcionales (44 ptos)

* Deben modificar el chat proporcionado
    * Bonus!: ***6 ptos*** Cada mensaje puede recibir un análisis de sentimientos, que indica el tono del mensaje: bueno, malo y neutro. Para esto debe implementarlo con Amazon Comprehend corriendo desde AWS Lambda, usando la API Gateway que tenían antes
    * ***7 ptos*** Debe agregar monitoreo a su app de diversas métricas y estados. Para esto deben aparecer en un dashboard
        * 2 métricas básicas de su servicio backend (CPU, RAM)
        * 1 alarma de disponibilidad
            * Puede usar New Relic o Prometheus/Grafana para esto. Cualquier otra herramienta debe consultar con su ayudante
        * Agregar una traza funcional sintética (e.g. el proceso de login) tiene un bonus de ***3 ptos***
    * ***7 ptos*** El chat debe poder replicarse en 3 instancias. Para esto pueden utilizar la directiva de docker-compose ***--scale service_name=3***. Deben implementar un balanceo de carga de los websockets con política de balanceo "round-robin"

* ***20 ptos*** ***Esencial*** Deben integrar otros usuarios de otro equipo (solo uno) en sus chats
    * Para esto deberán implementar un chequeo de JWT utilizando clave pública (RSA). El equipo que se integra a su chat (Equipo A) debe emitir un token firmado con una clave privada e identificado con un kid en el header. Paralelamente, el Equipo A debe publicar una llave en formato JWK con un kid coincidente. Su equipo, a la hora de recibir este token debe:
        * Chequear de este token que coincidan los claims estándar
            * *aud*
            * *iss*
            * *iat*
            * *exp*
        * El equipo A debe agregar los siguientes claims 
            * *val*: Si el usuario está validado 
            * *sub*: Un UUID para el usuario que viene a inscribirse
    * Un token válido presentado del equipo A permite a su chat darle un token de acceso nuevo (con una firma privada) para que este usuario nuevo pueda enviar mensajes al chat directamente con su UUID. 
    * Hay un bonus de ***3 ptos*** por integrarse con un segundo grupo

* ***10 ptos*** Deben crear una especificación de IaaC para levantar su backend con una de las siguientes herramientas (Levantar su frontend con IaaC tiene un bonus de **5ptos**)
    * Terraform
    * AWS CDK


### Consideraciones

No se considerarán entregas:
* Con componentes que corran en sus computadores o servidores que no sean los básicos de Azure/AWS/GCP/Cloudfront. Algunos ejemplos, los servicios de AWS serían:
    * EC2
    * VPC
    * IAM
    * S3
    * Lambda
* Montadas en Heroku/Firebase/Elastic beanstalk/Lightsail/Netlify o similares.
* Que no estén documentadas.

# Puntaje

### Atraso

Para esta entrega se les descontará 0.5 puntos en la nota máxima por horas Fibonacci con F1 = 6 y F2 = 6. 

Se considerará como atraso cualquier modificación en features o implementación que tenga que ver solo con lo que se pide en esta entrega.

| Fibonacci | Hora               | Nota maxima |
|-----------|--------------------|-------------|
| 6         | 0:01 - 5:59        | 6.5         |
| 6         | 6:00 - 11:59       | 6           |
| 12        | 12:00 - 23:59      | 5           |
| 18        | 24:00 - 41:59      | 4.5         |
| 30        | 42:00 - 71:59      | 4           |
| ...       | 72:00 en adelante  | 1           |

### Grupal

La nota se calcula como:

**E<sub>1 grupal</sub> = 1 + E<sub>1 RF</sub> + E<sub>1 RNF</sub> + E<sub>1 RDOC</sub>**

Siendo **E<sub>1 RF</sub>** el puntaje ponderado de los requisitos funcionales, y **E<sub>1 RNF</sub>** el correspondiente a los requisitos no funcionales y **E<sub>1 RDOC</sub>** de la documentación.

### Individual

Segun el programa del curso<sup>5</sup> , esto se evalua como:

**E<sub>1</sub> = 1 + ((E<sub>1 grupal</sub> - 1) * F<sub>g</sub>)**			
Donde F<sub>g</sub> es un factor de coevaluación asignado por el grupo que va de 0 a 1.2. Para esto se enviará un form de coevaluación donde cada integrante deberá evaluar a sus compañeros de grupo con una puntuación entre 1 y 5. 

**No podrán asignarle 5 puntos a más de un compañero, y sí lo hacen, se considerará que se entregó un máximo de 4 puntos a cada compañero**.

De no realizar la coevaluación, asumiremos que se le entregó el mismo puntaje de coevaluación a cada integrante, es decir 4 puntos.

## Links útiles

IaaC:
* 

* [Terraform Basics](https://www.adictosaltrabajo.com/2020/06/19/primeros-pasos-con-terraform-crear-instancia-ec2-en-aws/)
* [Terraform EC2](https://registry.terraform.io/modules/terraform-aws-modules/ec2-instance/aws/latest)
* [Terraform Api Gateway](https://registry.terraform.io/modules/terraform-aws-modules/apigateway-v2/aws/latest)
* [Terraform S3](https://registry.terraform.io/modules/terraform-aws-modules/s3-bucket/aws/latest)


# Apoyo

Las ayudantías programadas relevantes para esto por ahora son:
* Presentación del chat y del enunciado
* Monitoreo
