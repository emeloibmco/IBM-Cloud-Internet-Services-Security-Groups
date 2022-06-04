# IBM-Cloud-Internet-Services-Security-Groups :shield:
*IBM® Cloud Security Groups* son conjuntos de reglas para filtrar IPs (source and destination IP, source and destination port, protocol), que definen cómo manejar el tráfico de entrada (ingress) y salida (egress) para las interfaces privadas y públicas de una instancia de virtual server.

La presente guía esta enfocada en realizar las configuraciones necesarias para gestionar el tráfico de entrada y salida a un Virtual Server de infraestructura clásica para permitir únicamente el tráfico proveniente de *IBM® Cloud Internet Services*. De esta forma se asegura que todas las entradas al Virtual Server pasen por la seguridad proveída por *IBM® Cloud Internet Services*.

<br />

## Índice  📰
1. [Pre-Requisitos](#Pre-Requisitos-pencil)
2. [Asignación de subdominio](#asignación-de-subdominio)
3. [Configuración de Security Groups](#configuración-de-security-groups)
4. [Aplicación de reglas](#aplicación-de-reglas)
4. [Referencias](#Referencias-mag)
5. [Autores](#Autores-black_nib)
<br />

## Pre-Requisitos :pencil:
* Contar con una instancia de <a href="https://cloud.ibm.com/catalog/services/internet-services"> IBM Cloud Internet Services </a> con un dominio asignado.
* Contar con un servicio de kubernetes desplegado en un servidor de infraestructura clásica.
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
<p align="center"><img width="900" src="https://github.com/emeloibmco/IBM-Cloud-Internet-Services-Security-Groups/blob/main/Images/DNSsubdominio.png"></p>
<br />

## Configuración de Security Groups
Ingrese a los security Groups de su cuenta a través del menú desplegable de la izquierda: ```Classic Infrastructure``` ➡ ```Network Security``` ➡ ```Security Groups```.
 <br/>

 **Security Group para tráfico CIS**
 
 1. Dé clic en crear y asigne el nombre de su preferencia, por ejemplo ```allow_cis_traffic```, este security group se encargará de permitir el acceso de las IP pertenecientes a la instancia de *IBM® Cloud Internet Services*.

 2. En el banner de la izquierda seleccione la pestaña ```Assigned Instances``` y seleccione las interfaces públicas y privadas de los nodos del clúster donde tiene desplegados sus servicios.

<br />
<img align="center" width="600" src="https://github.com/emeloibmco/IBM-Cloud-Internet-Services-Security-Groups/blob/main/Images/asignar-instancias.png"/>
<br />
<br />
<p align="center"><img width="600" src="https://github.com/emeloibmco/IBM-Cloud-Internet-Services-Security-Groups/blob/main/Images/asignar-instancias.png"></p>
<br />

 3. Ingrese a <a href="https://api.cis.cloud.ibm.com/v1/ips"> https://api.cis.cloud.ibm.com/v1/ips</a> estas IPs se usarán en el próximo numeral.

 4. Nuevamente en IBM Cloud, en el banner de la izquierda seleccione la pestaña ```Rules```, en la sección ```Inbound Rules```, para cada IPv4 de la lista encontrada en el link del numeral 3 dé clic en el botón ```Add Rule``` y complete los campos de la siguiente forma:

    * ```IP type```: IPv4 
    * ```Protocol```: TCP
    * ```Port Range```: 80 - 80
    * ```Source type```: CIDR Block
    * ```Source```: Ingrese la IP correspondiente
    </br>

    Para cada IP repita el procedimiento con el rango de puertos ```8080 - 8080```

 **Security Group para tráfico de entrada de kubernetes**
 
 1. Dé clic en crear y asigne el nombre de su preferencia, por ejemplo ```allow_k8_traffic```, este security group se encargará de permitir el acceso de las IP públicas pertenecientes a la región en la que se ubica el clúster.

 2. En el banner de la izquierda seleccione la pestaña ```Assigned Instances``` y seleccione las interfaces públicas y privadas de los nodos del clúster donde tiene desplegados sus servicios.

 3. Ingrese a <a href="https://cloud.ibm.com/docs/containers?topic=containers-firewall#master_ips"> https://cloud.ibm.com/docs/containers?topic=containers-firewall#master_ips</a> estas IPs se usarán en el próximo numeral.

 4. Nuevamente en IBM Cloud, en el banner de la izquierda seleccione la pestaña ```Rules```, en la sección ```Inbound Rules```, para cada IP pública de la región correspondiente a su clúster, encontrada en el link del numeral 3, dé clic en el botón ```Add Rule``` y complete los campos de la siguiente forma:

    * ```IP type```: IPv4 
    * ```Protocol```: TCP
    * ```Port Range```: 30000 - 32767
    * ```Source type```: CIDR Block
    * ```Source```: Ingrese la IP correspondiente
    </br>

    Para cada IP repita el procedimiento con el rango de puertos ```443 - 443```

 **Security Group para tráfico de salida de kubernetes**
 
 1. Dé clic en crear y asigne el nombre de su preferencia, por ejemplo ```allow_outbound_k8```, este security group se encargará de permitir el tráfico de salida del clúster.

 2. En el banner de la izquierda seleccione la pestaña ```Assigned Instances``` y seleccione las interfaces públicas y privadas de los nodos del clúster donde tiene desplegados sus servicios.

 4. En el banner de la izquierda seleccione la pestaña ```Rules```, en la sección ```Outbound Rules```, dé clic en el botón ```Add Rule``` y complete los campos de la siguiente forma:

    * ```IP type```: IPv4 
    * ```Protocol```: Any
    * ```Port Range```: Any
    * ```Source type```: CIDR Block
    * ```Destination```: 0.0.0.0/0
    </br>
    
    Nuevamente dé clic en el botón ```Add Rule``` y complete los campos de la siguiente forma:

    * ```IP type```: IPv6
    * ```Protocol```: TCP
    * ```Port Range```: Any
    * ```Source type```: CIDR Block
    * ```Destination```: ::/0
    </br>

## Aplicación de reglas

Finalmente, para aplicar las reglas establecidas en los security groups, ingrese al menú desplegable de la izquierda y acceda a su clúster de kubernetes, en el menú de la izquierda dé clic en ```Worker Nodes``` y seleccione todos los nodos de su clúster, finalmente, dé clic en ```Reboot``` para reiniciar los nodos del clúster y aplicar las reglas incluidas en los security groups.


## Referencias :mag:
* <a href="https://www.ibm.com/cloud/blog/network-security-groups"> IBM Cloud Security Groups</a>

* <a href="https://cloud.ibm.com/docs/containers?topic=containers-firewall"> Classic: Opening required ports and IP addresses in your firewall</a>


<br />


## Autores :black_nib:
Equipo IBM Cloud Tech Sales Colombia.
<br />