---
layout: post
title: Crear un Azure Load Balancer
date: 2019-09-09 12:00:00 
categories: Azure 
tags: LoadBalancer IaaS
---

Azure Load Balancer nos ayuda a crear una arquitectura de alta disponibilidad; en esta ocación crearemos un Azure Load Balancer con tres maquinas virtuales y un servidor NGINX

### Creamos un grupo de recursos 

Donde 
- __az group create__: comando para crear  un grupo de recursos

Parámetros
- __--name myResourceGroup__: se creará un grupo de recursos con el nombre de *myResourceGroup*
- __--location eastus__: zona donde se creara la infraestructura en este caso *East US* puedes listar las zonas disponibles con el comando az account list-locations     

```shell script
$ az group create  
--name myResourceGroup  
--location eastus
```

### Creamos una dirección pública
Donde 
- __az network public-ip create__: comando para crear una IP pública

Parámetros
- __--name myPublicIp__: nombre de la IP pública
- __--resource-group myResourceGroup__: grupo de recursos donde sera creada la IP  

```shell script
az network public-ip create 
--resource-group myResourceGroup 
--name myPublicIp
```
### Creamos el balanceador

Donde 
- __az network lb create__: comando para crear un balanceador (Load Balancer)
- __--resource-group myResourceGroup__: grupo de recursos donde sera creado el balanceador
- __--frontend-ip-name myFrontendIpName__: nombre de la configuracion frontal del balanceador
- __--backend-pool-name myBackendPoolName__: nombre de la configuracion que se utilizara de forma interna
- __--public-ip-address myPublicIp __: sobre que direccion IP publica estara el balanceador
- __--name myLoadBalancer__: nombre de nuestro balanceador

``` 
az network lb create --resource-group myResourceGroup --frontend-ip-name myFrontendIpName --backend-pool-name myBackendPoolName --public-ip-address myPublicIp --name myLoadBalancer
```

### Creamos una sonda de monitoreo para el Load Balancer

```
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer --protocol tcp --port 80  --name myHealthProbe
```

### Creamos una regla de red del Load Balancer
```
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRule --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontendIpName --backend-pool-name myBackendPool --probe-name myHealthprobe
```

### Creamos una red virtual

```
az network vnet create --resource-group myResourceGroup --name myVnet --subnet-name mySubnet
```

### Creamos un grupo de seguridad de red (NSG)
```
az network nsg  create --resource-group myResourceGroup  --name myNetworkSecurityGroup 
```

### creamos una regla TCP para el grupo de seguridad de red
```
az network nsg rule create --resource-group myResourceGroup --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule --priority 1001 --protocol tcp --destination-port-range 80
```

### Creamos interfaces de red para nuestras maquinas virtuales

En este caso tres porque vamos a crear tres maquinas virtuales 

```
for i in `seq 1 3`; do
for> az network nic create --resource-group myResourceGroup --name myNick$i --vnet-name myVnet --subnet mySubnet --network-security-group mynetworkSecurityGroup --lb-name myloadBalancer --lb-address-pools myBackendPoolName
for> done
```

###Crear un conjunto de disponibilidad
```
az vm availability-set create --resource-group myResourceGroup --name myAvailabilitySet
```

### Creamos las maquinas virtuales 

```
for i in `seq 1 3`; do
az vm create --resource-group myResourceGroup --name myVM$i --availability-set myAvailabilitySet --nics myNick$i --image UbuntuLTS --admin-username azureuser --generate-ssh-key --custom-data cloud-init.txt --no-wait
done
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
