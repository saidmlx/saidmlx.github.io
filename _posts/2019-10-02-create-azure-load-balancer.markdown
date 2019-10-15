---
layout: post
title: Crear un Azure Load Balancer
date: 2019-09-09 12:00:00 
categories: Azure Linux
tags: [Load Balancer, IaaS]
---

Azure Load Balancer nos ayuda a crear una arquitectura de alta disponibilidad.
En esta ocasión crearemos un Azure Load Balancer con tres máquinas virtuales y un servidor NGINX

![Azure Load Balancer as @saidmlx]({{ site.url }}/assets/images/azure/azure-load-balancer.png)

### Requerimientos

1. tener una cuenta en [Azure](https://azure.microsoft.com/es-es/free/), puedes crear una gratuita
2. tener [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) instalado

## Crear un Load Balancer desde línea de comandos

Una vez instalado [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) iniciamos sesión desde la terminal ```$ az login```



### Creamos un grupo de recursos 

__Donde__
 
>__az group create__: comando para crear  un grupo de recursos

__Parámetros__

>__--name myResourceGroup__: se creará un grupo de recursos con el nombre de *myResourceGroup*
>
>__--location eastus__: zona donde se creará la infraestructura en este caso *East US* puedes listar las zonas disponibles 
con el comando __az account list-locations__     

```shell
$ az group create  
    --name myResourceGroup  
    --location eastus
```

![Azure Load Balancer as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/1_az_group_create.png)
Imagen 1.1: vista de grupo de recursos creados

![Azure Load Balancer as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/1b_az_group_create.png)
Imagen 1.2: vista del recurso creado


### Creamos una dirección pública

__Donde__ 

>__az network public-ip create__: comando para crear una IP pública

__Parámetros__

>__--name myPublicIp__: nombre de la IP pública
>
>__--resource-group myResourceGroup__: grupo de recursos donde será creada la IP  

```shell 
$ az network public-ip create 
    --resource-group myResourceGroup 
    --name myPublicIp
```

![Azure create public-ip as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/2a_az_network_public_ip_create.png)
__Imagen 2.1: vista de grupo de recursos creados__

![Azure Load Balancer as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/2b_az_network_public_ip_create.png)
__Imagen 2.2: vista del recurso *public-ip* creada__


### Creamos el balanceador

__Donde__ 
>**az network lb create**: comando para crear un balanceador (Load Balancer)

__Parámetros__

>**--resource-group myResourceGroup**: grupo de recursos donde será creado el balanceador
>
>**--frontend-ip-name myFrontendIpName**: nombre de la configuración frontal del balanceador
>
>**--backend-pool-name myBackendPoolName**: nombre de la configuración que se utilizara de forma interna
>
>**--public-ip-address myPublicIp**: sobre que dirección IP pública estará el balanceador
>
>**--name myLoadBalancer**: nombre de nuestro balanceador

```shell
$ az network lb create 
    --resource-group myResourceGroup 
    --frontend-ip-name myFrontendIpName 
    --backend-pool-name myBackendPoolName 
    --public-ip-address myPublicIp 
    --name myLoadBalancer
```

![Azure create lb as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/3a_az_network_lb_create.png)
__Imagen 3.1: vista de grupo de recursos creados__

![Azure create lb as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/3b_az_network_lb_create.png)
__Imagen 3.2: vista del recurso *lb* creada__


### Creamos una sonda de monitoreo para el Load Balancer

este recurso creado quedará a nivel de Load balancer entonces para poder verlo en el portal azure hay que revisar el Load Balancer

__Donde__
> **az network lb probe create**: comando para crear una sonda de monitoreo

__Parámetros__
 
>**--resource-group myResourceGroup**: a que grupo pertenece
> 
>**--lb-name myLoadBalancer**: a que Load Balancer  pertenece
> 
>**--protocol tcp**: el protocolo a monitorear (http, https, tcp)
> 
>**--port 80**: el puerto a vigilar
>  
>**--name myHealthProbe**: el nombre de la sonda   

```shell
az network lb probe create 
    --resource-group myResourceGroup 
    --lb-name myLoadBalancer 
    --protocol tcp 
    --port 80  
    --name myHealthProbe
```

![Azure create lb probe as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/4a_az_network_lb_probe_create.png)
__Imagen 4.1: vista del *lb prove*  creado__


### Creamos una regla de red del Load Balancer

__Donde__
>**az network lb rule create**: comando para crear una regla de red

__Parámetros__
 
>**--resource-group myResourceGroup**: a que grupo pertenece
>
>**--lb-name myLoadBalancer**: a que Load Balancer  pertenece
>
>**--name myLoadBalancerRule**: nombre de la regla
>
>**--protocol tcp**: protocolo sobre el cual se va aplicar
>
>**--frontend-port 80**: puerto de comunicación de entrada
> 
>**--backend-port 80**: puerto de comunicación hacia las máquinas virtuales
>
>**--frontend-ip-name myFrontendIpName**: nombre de la interfaz de entrada
>  
>**--backend-pool-name myBackendPoolName**: nombre de la colección de comunicación hacia las máquinas virtuales
>
>**--probe-name myHealthprobe**: nombre de la regla de red

```shell
$ az network lb rule create 
    --resource-group myResourceGroup 
    --lb-name myLoadBalancer 
    --name myLoadBalancerRule 
    --protocol tcp 
    --frontend-port 80 
    --backend-port 80 
    --frontend-ip-name myFrontendIpName 
    --backend-pool-name myBackendPoolName 
    --probe-name myHealthprobe
```

![Azure create network lb rule as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/5a_az_network_lb_rule_create.png)
__Imagen 5.1: vista del *Load Balancer*  creado__

![Azure create network lb rule as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/5b_az_network_lb_rule_create.png)
__Imagen 5.2: vista de la regla creada__


### Creamos una red virtual
Para ensamblar todo necesitamos una red virtual y para eso creamos un Red  y una Subred virtual 

__Donde__

>**az network vnet create**:

__Parámetros__ 
>**--resource-group myResourceGroup**: grupo al que pertenece
> 
>**--name myVnet**: nombre de la red virtual
> 
>**--subnet-name mySubnet**:nombre de la subred

```shell
az network vnet create 
    --resource-group myResourceGroup 
    --name myVnet 
    --subnet-name mySubnet
```

![Azure create network lb rule as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/6a_az_network_vnet_create.png)
__Imagen 6.1: vista del grupo de recursos__

![Azure create network lb rule as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/6b_az_network_vnet_create.png)
__Imagen 6.2: vista de la red virtual__

### Crear un conjunto de disponibilidad

Para asegurar que el servicio siempre esté disponible es necesario un conjunto de disponibilidad ; 
esto nos ayudara que cuando haya algun mantenimiento o error en una máquina virtual almenos una pueda proveer el servicio; 
o en caso de saturacion esto ayudara a distribuir el trafico. 

__donde__

>**az vm availability-set create**: comando para crear un conjunto de disponibilidad 
 
__Parámetros__
>**--resource-group myResourceGroup**:  grupo de recursos al que pertenece
>
>**--name myAvailabilitySet**: nombre del conjunto de disponibilidad

```shell
az vm availability-set create 
    --resource-group myResourceGroup 
    --name myAvailabilitySet
```

![Azure create availability set as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/7b_az_vm_availability_set_create.png)
__Imagen 7.1: vista del grupo de recursos__

![Azure create availability set as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/7b_az_vm_availability_set_create.png)
__Imagen 7.2: vista del grupo de disponibilidad__


### Creamos un grupo de seguridad de red (NSG)

__Donde__

>**az network nsg  create**: comando para crear un grupo de seguridad (Network security Group)

__Parámetros__

>**--resource-group myResourceGroup**: grupo al que pertenece
>  
>**--name myNetworkSecurityGroup**: nombre del grupo de seguridad de red (NSG)

```shell
az network nsg  create 
    --resource-group myResourceGroup  
    --name myNetworkSecurityGroup 
```

![Azure create availability set as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/8a_az_network_nsg_create.png)
__Imagen 8.1: vista del grupo de recursos__

![Azure create availability set as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/8b_az_network_nsg_create.png)
__Imagen 8.2: vista del grupo de grupo de seguridad de red (Network Security Group NSG)__

### Creamos una regla TCP para el grupo de seguridad de red

Esta regla esta a nivel de NSG y habilitara el puerto  80 sobre el protocolo TCP

__Donde__

>**az network nsg rule create**: comando para crear un regla TCP para  el grupo de seguridad

__Parámetros__ 

>**--resource-group myResourceGroup**: grupo al que pertenece
> 
>**--nsg-name myNetworkSecurityGroup**: nombre del grupo de seguridad de red al que pertenece
> 
>**--name myNetworkSecurityGroupRule**: nombre de la regla para el grupo de seguridad de red 
>
>**--priority 1001**: que prioridad tiene donde 100 es la más alta y 4096 la más baja, esta prioridad no se puede duplicar si se tiene más de una regla
>
>**--protocol tcp**: protocolo sobre el cual va actuar (*, Esp, Icmp, Tcp, Udp)
>
>**--destination-port-range 80**: aquí  definimos  los puertos del protocolo que va actuar 

```shell
az network nsg rule create 
    --resource-group myResourceGroup 
    --nsg-name myNetworkSecurityGroup 
    --name myNetworkSecurityGroupRule 
    --priority 1001 
    --protocol tcp 
    --destination-port-range 80
```


![Azure create availability set as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/9a_az_network_nsg_rule_create.png)
__Imagen 9.1: vista de la regla del grupo de grupo de seguridad de red (Network Security Group NSG)__

### Creamos interfaces de red para nuestras máquinas virtuales

En este caso tres porque vamos a crear tres maquinas interfaces de red una para cada una de las máquinas virtuales 

**_Este proceso lo tenemos que realizar de acuerdo al número de máquinas virtuales que deseemos crear para este caso 3, por lo cual al final del proceso tendremos tres interfaces de red myNick1, myNick2 y myNick3_**

__Donde__
>**az network nic create**: comando para crear la interfaz de red

__Parámetros__ 
>**--resource-group myResourceGroup**: grupo al que pertenece
>  
>**--vnet-name myVnet**: nombre de la red a la que pertenece
>
>**--subnet mySubnet**: nombre de la subred a la que pertenece
>
>**--network-security-group myNetworkSecurityGroup**: nombre del grupo de seguridad de red al que pertenece 
> 
>**--lb-name myloadBalancer**: nombre del Load balancer al que pertenece
>
>**--lb-address-pools myBackendPoolName**: nombre de la colección de comunicación al que pertenece
>
>**--name myNick1**: nombre de la interfaz de red

```shell
$ az network nic create 
    --resource-group myResourceGroup  
    --vnet-name myVnet 
    --subnet mySubnet 
    --network-security-group mynetworkSecurityGroup 
    --lb-name myloadBalancer 
    --lb-address-pools myBackendPoolName
    --name myNick1
```

![Azure create availability set as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/10a_az_network_nic_create.png)
__Imagen 10.1: vista de grupo de recursos__


![Azure create availability set as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/10b_az_network_nic_create.png)
__Imagen 10.2: vista de la interfaz de red creada__


### Creamos las máquinas virtuales 

Por fin llegamos a las máquinas virtuales, aqui vamos a crear tres máquinas virtuales, este proceso creará dos recursos por cada ejecución;
 una maquina virtual y una unidad de disco. 

*__Este proceso al igual que las interfaces de red hay que hacerlo tres veces__*

__Donde__
>**az vm create**:  comando para crear una máquina virtual

__Parámetros__

>**--resource-group myResourceGroup**: grupo de recursos la que pertenece
> 
>**--availability-set myAvailabilitySet**: nombre del conjunto de disponibilidad al que pertenece
> 
>**--nics myNick1**: nombre de la interfaz de red asociado
> 
>**--image UbuntuLTS**: imagen de sistema operativo que vamos a crear
> 
>**--admin-username azureuser**: usuario para esta máquina virtual
> 
>**--generate-ssh-key**: indicamos que nos autenticaremos por SSH
> 
>**--custom-data cloud-init.txt**: este archivo de texto es el script para generar nuestro servidor NGNIX en la parte inferior hablamos de él. 
>asegurate de poner la ubicación correcta, ya que de lo contrario se creara la maquina virtual pero no el server.
> 
>**--name myVM1**: nombre de la máquina virtual, recuerda que los nombres no pueden repetir

```shell
az vm create 
    --resource-group myResourceGroup 
    --availability-set myAvailabilitySet 
    --image UbuntuLTS 
    --admin-username azureuser 
    --generate-ssh-key 
    --custom-data cloud-init.txt
    --nics myNick1 
    --name myVM1
```


![Azure create availability set as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/11a_az_vm_create.png)
__Imagen 11.1: vista de grupo de recursos__


![Azure create availability set as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/11b_az_vm_create.png)
__Imagen 11.2: vista de la máquina virtual creada__

![Azure create availability set as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/11c_az_vm_create.png)
__Imagen 11.3: vista del disco de la máquina virtual creada__


### Script para crear un servidor NGINX 



```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### El resultado 

de esta forna cuando tecleemos en el navegador la direccion ip publica podremos ver nuestro servidor corriendo.

Para este caso podemos ver desde a que máquina virtual estamos acediendo, aqui podemos ver que la de la izquierda esta accediuendo a VM2 y el de la derecha a VM1 


![Azure create availability set as @saidmlx]({{ site.url }}/assets/images/azure/2019-09-09_create-azure-load-balancer/12a_azure_web_server.png)
__11.3: Vista de navegador__
