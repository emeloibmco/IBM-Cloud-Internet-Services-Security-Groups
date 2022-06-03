# IBM-Cloud-Internet-Services-Security-Groups :shield:
*IBM® Cloud Security Groups* son conjuntos de reglas para filtrar IPs (source and destination IP, source and destination port, protocol), que definen cómo manejar el tráfico de entrada (ingress) y salida (egress) para las interfaces privadas y públicas de una instancia de virtual server.

La presente guía esta enfocada en realizar las configuraciones necesarias para gestionar el tráfico de entrada y salida a un Virtual Server de infraestructura clásica para permitir únicamente el tráfico proveniente de *IBM® Cloud Internet Services*. De esta forma se asegura que todas las entradas al Virtual Server pasen por la seguridad proveída por *IBM® Cloud Internet Services*.

<br />

## Índice  📰
1. [Pre-Requisitos](#Pre-Requisitos-pencil)
2. [Asignación de subdominio](#asignación-de-subdominio)
3. [Configuración de Security Groups](#configuración-de-security-groups)
4. [Referencias](#Referencias-mag)
5. [Autores](#Autores-black_nib)
<br />

## Pre-Requisitos :pencil:
* Contar con una instancia de <a href="https://cloud.ibm.com/catalog/services/internet-services"> IBM Cloud Internet Services </a> con un dominio asignado.
* Contar con un servicio de kubernetes desplegado en una IP en un servidor de infraestructura clásica.
<br />

## Asignación de subdominio
Ingrese a su instancia de *IBM® Cloud Internet Services* y acceda a la pestaña ```reliability``` y posteriormente a la sección ```DNS```. Dé clic en agregar DNS record y complete la información según corresponda:
* ```Type```: Tipo A para direcciones IP. 
* ```TTL```: Automatic.
* ```Name```: Escriba el subdominio que desea agregar.
* ```IPv4 Address```: Ingrese la dirección IP donde está desplegado su servicio.
</br>

Dé clic en crear y posteriormente active la opción de ```Proxy``` para habilitar el tráfico a través de CIS y aplicar las normas que serán agregadas a los security groups

<br />
<p align="center"><img width="900" src="https://github.com/emeloibmco/IBM-Cloud-Internet-services-Security-Groups/DNSsubdominio"></p>
<br />

## Configuración de Security Groups
Ingrese a los security Groups de su cuenta a través del menú desplegable de la izquierda: ```Classic Infrastructure``` ➡ ```Network Security``` ➡ ```Security Groups```.
 <br/>

 **Security Group para tráfico CIS**
 
 1. Dé clic en crear y asigne el nombre de su preferencia, por ejemplo ```allow_cis_traffic```, este security group se encargará de permitir el acceso de las IP pertenecientes a la instancia de *IBM® Cloud Internet Services*.

 2. En el banner de la izquierda seleccione la pestaña ```Assigned Instances``` y seleccione las interfaces públicas y privadas de los nodos del clúster donde tiene desplegados sus servicios.

 3. Ingrese a <a href="https://api.cis.cloud.ibm.com/v1/ips"> https://api.cis.cloud.ibm.com/v1/ips</a>




## Referencias :mag:
* <a href="https://www.ibm.com/cloud/blog/network-security-groups"> IBM Cloud Security Groups</a>


<br />


## Autores :black_nib:
Equipo IBM Cloud Tech Sales Colombia.
<br />