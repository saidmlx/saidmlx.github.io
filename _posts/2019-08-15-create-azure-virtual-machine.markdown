---
layout: post
title: Crear una máquina virtual en Azure
date: 2019-08-15 12:00:00 
categories: Azure Linux
tags: IasS Linux
---

Cuando se habla de IasS se tiene que hablar de  maquinas virtuales (VM) que la base de toda infraestructura, 
en este post hablaremos de que recursos constituyen una máquina virtual en Azure y cómo crearlos.

Cuando se crea una máquina virtual (VM) en Azure se crean varios servicios alrededor de esta VM
1. Una máquina con CPU y recursos
2. Una Azure Storage para sostener los discos virtuales
3. Un disco virtual para sostener el sistema operativo
4. Una red virtual (VNET) para conectar la VM y servicios adicionales
5. Una interfaz de red para comunicarse con la VNET
6. Opcionalmente una dirección IP pública para acceder a la VM  

## Requerimientos

1. tener una cuenta en [Azure](https://azure.microsoft.com/es-es/free/), puedes crear una gratuita
2. tener [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) instalado


## Crear el grupo de recursos

Un grupo de recursos (Resource Group) es una forma de agrupar recursos, y un recurso siempre debe de estar dentro de un grupo,
espor eso que para iniciar es necesario crear uno. 

```shell 
$ az group create 
    --name myResourceGroup 
    --location eastus
```

## Crear la máquina virtual 

### Elegir la imagen a instalar
Azure tiene un set sistemas operativos Linux y Windows, 
para ver la lista podemos utilizar el comando ``` az vm image list``` 
para este ejemplo utilizaremos __\--image UbuntuLTS__ 

```shell
$ az vm image list 
    --output table 
```

|Offer             |Publisher                |Sku                 |Urn                                                                |UrnAlias             |Version  |
|----------------- |----------------------   |------------------  |-----------------------------------------------------------------  |-------------------  |---------|
|CentOS            |Open Logic               |7.5                 |OpenLogic: CentOS: 7.5: latest                                     |CentOS               |latest   |
|CoreOS            |Core OS                  |Stable              |CoreOS: CoreOS: Stable: latest                                     |CoreOS               |latest   |
|debian-10         |Debian                   |10                  |Debian: debian-10: 10: latest                                      |Debian               |latest   |
|openSUSE-Leap     |SUSE                     |42.3                |SUSE: openSUSE-Leap: 42.3: latest                                  |openSUSE-Leap        |latest   |
|RHEL              |Red Hat                  |7-LVM               |RedHat: RHEL: 7-LVM :latest                                        |RHEL                 |latest   |
|SLES              |SUSE                     |15                  |SUSE: SLES: 15: latest                                             |SLES                 |latest   |
|__*UbuntuServer*__|__*Canonical*__          |__*18.04-LTS*__     |__*Canonical: UbuntuServer: 18.04-LTS: latest*__                   |__*UbuntuLTS*__      |__*latest*__|
|WindowsServer     |Microsoft WindowsServer  |2019-Datacenter     |MicrosoftWindowsServer: WindowsServer: 2019-Datacenter:latest     |Win2019Datacenter    |latest   |
|WindowsServer     |Microsoft WindowsServer  |2016-Datacenter     |MicrosoftWindowsServer: WindowsServer: 2016-Datacenter:latest     |Win2016Datacenter    |latest   |
|WindowsServer     |Microsoft WindowsServer  |2012-R2-Datacenter  |MicrosoftWindowsServer: WindowsServer: 2012-R2-Datacenter:latest  |Win2012R2Datacenter  |latest   |
|WindowsServer     |Microsoft WindowsServer  |2012-Datacenter     |MicrosoftWindowsServer: WindowsServer: 2012-Datacenter:latest     |Win2012Datacenter    |latest   |
|WindowsServer     |Microsoft WindowsServer  |2008-R2-SP1         |MicrosoftWindowsServer: WindowsServer: 2008-R2-SP1:latest         |Win2008R2SP1         |latest   |

#### Que capacidad 

Azure tiene la siguiente clasificación, para nuestro caso utilizaremos uno de propósito general **\--size Standard_B2s**

|Type                 |Common sizes                              |Description                   |
|---------------------|---------------------------------------|---------------------------------|
|__*General purpose*__|__*B, Dsv3, Dv3, DSv2, Dv2, Av2, DC*__ |__*Balanced CPU-to-memory. Ideal for dev / test and small to medium applications and data solutions.*__|
|Compute optimized    |Fsv2	                                  |High CPU-to-memory. Good for medium traffic applications, network appliances, and batch processes.             |
|Memory optimized     |Esv3, Ev3, M, DSv2, Dv2                |High memory-to-core. Great for relational databases, medium to large caches, and in-memory analytics.          |
|Storage optimized    |Lsv2, Ls                               |High disk throughput and IO. Ideal for Big Data, SQL, and NoSQL databases.                                     |
|GPU                  |NV, NVv2, NC, NCv2, NCv3, ND           |Specialized VMs targeted for heavy graphic rendering and video editing.                                        |
|High performance     |H                                      |Our most powerful CPU VMs with optional high-throughput network interfaces (RDMA).                             |

Si queremos saber más acerca de las capacidades de cada una podemos hacerlo vía ```az vm list-sizes```

```shell
$ az vm list-sizes 
    --output table 
    --location eastus
```

|Max Data Disk Count |Memory In Mb  |Name                    |Number Of Cores  |Os Disk Size In Mb|Resource Disk Size In Mb|
|------------------  |------------  |----------------------  |---------------  |----------------  |------------------------|
|2                   |512           |Standard_B1ls           |1                |1047552           |1024                    |
|2                   |2048          |Standard_B1ms           |1                |1047552           |4096                    |
|2                   |1024          |Standard_B1s            |1                |1047552           |2048                    |
|4                   |8192          |Standard_B2ms           |2                |1047552           |16384                   |
|__*4*__             |__*4096*__    |__*Standard_B2s*__      |__*2*__          |__*1047552*__     |__*8192*__              |
|8                   |16384         |Standard_B4ms           |4                |1047552           |32768                   |
|16                  |32768         |Standard_B8ms           |8                |1047552           |65536                   |
|16                  |49152         |Standard_B12ms          |12               |1047552           |98304                   |
|32                  |65536         |Standard_B16ms          |16               |1047552           |131072                  |
|32                  |81920         |Standard_B20ms          |20               |1047552           |163840                  |
|4                   |3584          |Standard_DS1_v2         |1                |1047552           |7168                    |

## Crear la máquina virtual

__Donde__
>__az vm create__: es el comando para crear una máquina virtual

__Parámetros__ 
>**--resource-group myResourcegroup**: nombre del grupo de recursos donde se creara la MV
> 
>**--admin-user azureusr**: usuario 
> 
>**--size Standard_B2s**: tamaño de la MV
>
>**--image UbuntuLTS**: imagen a utilizar
> 
>**--generate-ssh-keys**: indica que la autenticacion sera por ssh
> 
>**--name myvm01**: nombre de la máquina virtual
```shell
$ az vm create 
--resource-group myResourceGroup 
--name myVM01 
--admin-user azureusr 
--size Standard_B2s
--image UbuntuLTS 
--generate-ssh-keys
```

![Azure Load Balancer as @saidmlx]({{ site.url }}/assets/images/azure/2019-08-15-create-azure-virtual-machine/1a_az_vm_create.png)
__Imagen 2.1: recursos creados__

Cuando se crea una máquina virtual se crean 

1. Máquina virtual
2. Unidad de disco
3. Grupo de seguridad
4. Dirección IP Pública
5. Interfaz de red
6. red virtual 

### Ingresar a la VM creada
Del recurso *Dirección IP Pública* se obtiene la dirección pública con la cual se puede ingresar via SSH
Ingresamos via SSH con el usuario azureusr y a la ip pública

```shell
$ ssh azureusr@52.168.148.83
```

```shell
Warning: Permanently added '52.168.148.83' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 5.0.0-1018-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


  System load:  0.03              Processes:           125
  Usage of /:   4.2% of 28.90GB   Users logged in:     0
  Memory usage: 8%                IP address for eth0: 10.0.0.4
  Swap usage:   0%

7 packages can be updated.
7 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.


azureusr@myVM01:~$
```
