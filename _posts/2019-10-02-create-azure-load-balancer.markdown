---
layout: post
title: Crear un Azure Load Balancer
date: 2019-09-09 12:00:00 
categories: Azure 
tags: LoadBalancer IaaS
---

Azure Load Balancer nos ayuda a crear una arquitectura de alta disponibilidad.
En esta ocación crearemos un Azure Load Balancer con tres maquinas virtuales y un servidor NGINX

![vue as @saidmlx]({{ site.url }}/assets/images/azure/azure-load-balancer.png)

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

### Creamos una dirección pública

__Donde__ 

>__az network public-ip create__: comando para crear una IP pública

__Parámetros__

>__--name myPublicIp__: nombre de la IP pública
>
>__--resource-group myResourceGroup__: grupo de recursos donde sera creada la IP  

```shell 
$ az network public-ip create 
    --resource-group myResourceGroup 
    --name myPublicIp
```
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

### Creamos una sonda de monitoreo para el Load Balancer
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
>**--backend-pool-name myBackendPool**: nombre de la colección de comunicación hacia las máquinas virtuales
>
>**--probe-name myHealthprobe**: nombre de la regla de red

```shell
az network lb rule create 
    --resource-group myResourceGroup 
    --lb-name myLoadBalancer 
    --name myLoadBalancerRule 
    --protocol tcp 
    --frontend-port 80 
    --backend-port 80 
    --frontend-ip-name myFrontendIpName 
    --backend-pool-name myBackendPool 
    --probe-name myHealthprobe
```

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

### creamos una regla TCP para el grupo de seguridad de red

__Donde__

>**az network nsg rule create**: comando para crear un regla TCP para  el grupo de seguridad

__Parámetros__ 

>**--resource-group myResourceGroup**: grupo al que pertenece
> 
>**--nsg-name myNetworkSecurityGroup**: nombre del grupo de seguridad de red al que pertenece
> 
>**--name myNetworkSecurityGroupRule**: nombre de la regla para el grupo de seguridad de red 
>
>**--priority 1001**: que prioridad tiene donde 100 es la mas alta y 4096 la más baja, esta prioridad no se puede duplicar si se tiene más de una regla
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

### Creamos interfaces de red para nuestras máquinas virtuales

En este caso tres porque vamos a crear tres maquinas interfaces de red una para cada una de las maquinas virtuales 

**_Este proceso lo tenemos que realizar de acuerdo al número de maquinas virtuales que deseemos crear para este caso 3, por lo cual al final del proceso tendremos tres interfaces de red myNick1, myNick2 y myNick3_**

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
>**--lb-address-pools myBackendPoolName**: nombre de la coleción de comunicación al que pertenece
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

### Crear un conjunto de disponibilidad

Para asegurar que el servicio siempre esté disponible es necesario un conjunto de disponibilidad al grupo de recursos; 
esto nos ayudara que al menos una máquina virtual este despachando el servicio, muy útil en caso de mantenimiento

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

### Creamos las máquinas virtuales 

Por fin llegamos a las máquinas virtuales, aqui vamos a crear tres maquinas virtuales dependiendo

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
>**--custom-data cloud-init.txt**: este archivo de texto es el script para generar nuestro servidor NGNIX en la parte inferior hablamos de él
> 
>**--name myVM1**: nombre de la máquina virtual, recuerda que los nombres no pueden repetir

```shell
az vm create 
    --resource-group myResourceGroup 
    --availability-set myAvailabilitySet 
    --nics myNick1 
    --name myVM1
    --image UbuntuLTS 
    --admin-username azureuser 
    --generate-ssh-key 
    --custom-data cloud-init.txt
```


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
