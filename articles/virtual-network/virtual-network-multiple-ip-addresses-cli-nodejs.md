---
title: "Máquina virtual con varias direcciones IP mediante la CLI de Azure 1.0 | Microsoft Docs"
description: "Aprenda a asignar varias direcciones IP a una máquina virtual con la CLI de Azure 1.0 | Resource Manager."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: d52694b2604510f1926142ace89cc7781ca83a1c
ms.openlocfilehash: d2e0b4d7cf88eebade80bb7633d2599fe65fe32a
ms.lasthandoff: 02/27/2017


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli-10"></a>Asignación de varias direcciones IP a máquinas virtuales mediante la CLI de Azure 1.0

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

En este artículo se describe cómo crear una máquina virtual con el modelo de implementación de Azure Resource Manager mediante la CLI de Azure 1.0. No se pueden asignar varias direcciones IP a los recursos creados mediante el modelo de implementación clásica. Para información acerca de los modelos de implementación de Azure, lea el artículo [Understand deployment models](../resource-manager-deployment-model.md) (Descripción de los modelos de implementación).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Creación de una máquina virtual con varias direcciones IP

Puede completar esta tarea mediante la CLI de Azure 1.0 (en este artículo) o la [CLI de Azure 2.0](virtual-network-multiple-ip-addresses-cli.md). En los pasos siguientes se explica cómo crear una VM de ejemplo con varias direcciones IP, tal como se describe en el escenario. Cambie los nombres de variable los y tipos de direcciones IP según sea necesario para la implementación.

1. Instale y configure la CLI de Azure 1.0 siguiendo los pasos del artículo [Instalación de la CLI de Azure](../xplat-cli-install.md) e inicie sesión en la cuenta de Azure con el comando `azure-login`.

2. Regístrese para obtener la versión preliminar ejecutando los siguientes comandos de PowerShell (no se puede registrar con la CLI) después de iniciar sesión y seleccionar la suscripción adecuada:
    
    ```powershell
    Register-AzureRmProviderFeature  -FeatureName AllowMultipleIpConfigurationsPerNic -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature  -FeatureName AllowLoadBalancingonSecondaryIpconfigs -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
    No trate de completar los pasos restantes hasta que vea el siguiente resultado cuando ejecute el comando ```Get-AzureRmProviderFeature```:
    
    ```powershell
    FeatureName                            ProviderName      RegistrationState
    -----------                            ------------      -----------------
    AllowLoadBalancingOnSecondaryIpConfigs Microsoft.Network Registered       
    AllowMultipleIpConfigurationsPerNic    Microsoft.Network Registered       
    ```
    
    >[!NOTE] 
    >Esta operación puede tardar unos minutos.

3. [Cree un grupo de recursos](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations) seguido de una [red virtual y una subred ](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet). Cambie los campos ``` --address-prefixes ``` y ```--address-prefix``` por lo siguiente para seguir el escenario exacto descrito en este artículo:

    ```azurecli
    --address-prefixes 10.0.0.0/16
    --address-prefix 10.0.0.0/24
    ```
    >[!NOTE] 
    >El artículo al que se hace referencia anteriormente utiliza Europa Occidental como la ubicación para crear recursos, pero en este artículo se usa Centro occidental de EE. UU. Realice cambios de ubicación correctamente.

4. [Cree una cuenta de almacenamiento](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account) para su VM.

5. Cree la NIC y las configuraciones de IP que desee asignar a la NIC. Puede agregar, quitar o cambiar las configuraciones según sea necesario. En el escenario se describen las siguientes configuraciones:

    **IPConfig-1**

    Escriba los comandos siguientes para crear:

    - Un recurso de dirección IP pública con una dirección IP pública estática
    - Una configuración de IP con el recurso de dirección IP pública y una dirección IP privada dinámica

    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP --domain-name-label mypublicdns --allocation-method Static
    ```
    > [!NOTE]
    > Las direcciones IP públicas tienen un precio simbólico. Para más información sobre los precios de las direcciones IP, lea la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Existe un límite para el número de direcciones IP públicas que pueden usarse dentro de una suscripción. Para más información sobre los límites, lea el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md#networking-limits).

    ```azurecli
    azure network nic create --resource-group myResourceGroup --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 --public-ip-name myPublicIP
    ```

    **IPConfig-2**

     Escriba los comandos siguientes para crear un nuevo recurso de dirección IP pública y una nueva configuración de IP con una dirección IP pública estática y una dirección IP privada estática:
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns2 --allocation-method Static

    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-2 --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
    ```

    **IPConfig-3**

    Escriba los comandos siguientes para crear una configuración de IP con una dirección IP privada dinámica y ninguna dirección IP no pública:

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3
    ```

    >[!NOTE] 
    >Aunque este artículo asigna todas las configuraciones de IP a una NIC única, también puede asignar varias configuraciones de IP a una NIC en una VM. Para aprender a crear una VM con varias NIC, lea el artículo Implementación de máquinas virtuales con varias NIC mediante PowerShell.

6. Artículo [Creación de las máquinas virtuales Linux](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms). Asegúrese de quitar la propiedad ```  --availset-name myAvailabilitySet \ ```, ya que no es necesaria para este escenario. Utilice la ubicación adecuada en función del escenario. 

    >[!WARNING] 
    > El paso 6 del artículo para crear una VM da error si el tamaño de dicha VM no se admite en la ubicación seleccionada. Ejecute el siguiente comando para obtener una lista completa de las máquinas virtuales de Oeste de EE. UU., por ejemplo: `azure vm sizes --location westcentralus` El nombre de esta ubicación se puede cambiar en función de su escenario.

    Para cambiar el tamaño de VM a Estándar DS2 v2, por ejemplo, simplemente agregue la siguiente propiedad ```  --vm-size Standard_DS3_v2``` al comando ``` azure vm create ``` en el paso 6.

7. Escriba el siguiente comando para ver la NIC y las configuraciones de IP asociadas:

    ```azurecli
    azure network nic show --resource-group myResourceGroup --name myNic1
    ```
8. Agregue al sistema operativo de la máquina virtual la dirección IP privada siguiendo las instrucciones de la sección [Incorporación de direcciones IP a un sistema operativo de la VM](#os-config) de este artículo.

## <a name="add"></a>Incorporación de direcciones IP a una VM

Puede agregar más direcciones IP públicas y privadas a una NIC existente completando los pasos siguientes. Los ejemplos se crean en el [escenario](#Scenario) descrito en este artículo.

1. Abra la CLI de Azure y complete los pasos restantes de esta sección dentro de una sola sesión de CLI. Si todavía no tiene la CLI de Azure instalada y configurada, complete los pasos del artículo [Instalación de la CLI de Azure](../xplat-cli-install.md) e inicie sesión en la cuenta de Azure.

2. Regístrese para la versión preliminar pública siguiendo el paso 2 de la sección **Creación de una máquina virtual con varias direcciones IP**.

3. Complete los pasos de una de las secciones siguientes, según sus requisitos:

    **Incorporación de una dirección IP privada**
    
    Para agregar una dirección IP privada a una NIC, debe crear una configuración de IP mediante el comando siguiente.  Si desea agregar una dirección IP privada dinámica, quite ```-PrivateIpAddress 10.0.0.7``` antes de escribir el comando. Al especificar una dirección IP estática, debe ser una dirección no utilizada para la subred.

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --private-ip-address 10.0.0.7 --name IPConfig-4
    ```
    Cree tantas configuraciones como sea necesario mediante nombres de configuración únicos y direcciones IP privadas (para las configuraciones con direcciones IP estáticas).

    **Incorporación de una dirección IP pública**
    
    Se agrega una dirección IP pública mediante la asociación a una nueva configuración de IP o una configuración de IP existente. Complete los pasos de una de las secciones siguientes, según sea preciso.

    > [!NOTE]
    > Las direcciones IP públicas tienen un precio simbólico. Para más información sobre los precios de las direcciones IP, lea la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Existe un límite para el número de direcciones IP públicas que pueden usarse dentro de una suscripción. Para más información sobre los límites, lea el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md#networking-limits).
    >

    **Asociación del recurso a una nueva configuración de IP**
    
    Siempre que agregue una dirección IP pública en una nueva configuración de IP, también debe agregar una dirección IP privada, porque todas las configuraciones de IP deben tener una dirección IP privada. Puede agregar un recurso de dirección IP pública existente o crear uno nuevo. Para crear uno nuevo, escriba el comando siguiente:
    
    ```azurecli
      azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3
    ```

     Para crear una nueva configuración de IP con una dirección IP privada dinámica y el recurso de dirección IP pública *myPublicIP3*, escriba el comando siguiente:

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 --public-ip-name myPublicIP3
    ```

    **Asocie el recurso a una configuración de IP existente**
   . Solo se puede asociar un recurso de dirección IP pública a una configuración de IP que ya no tiene asociado uno. Puede determinar si una configuración de IP tiene una dirección IP pública asociada escribiendo el comando siguiente:

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

    Busque una línea similar a la que aparece a continuación en la salida devuelta:
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet
     
    Puesto que la columna **IP pública** para *IpConfig-3* está en blanco, ningún recurso de dirección IP pública está asociado actualmente a ella. Puede agregar un recurso de dirección IP pública existente a IpConfig-3 o escribir el siguiente comando para crear uno:

    ```azurecli
    azure network public-ip create --resource-group  myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
    ```
    
    Escriba el siguiente comando para asociar el recurso de dirección IP pública a la configuración de IP existente llamada *IPConfig-3*:
    
    ```azurecli
    azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 --public-ip-name myPublicIP3
    ```

7. Vea las direcciones IP privadas y los recursos de dirección IP pública asignados a la NIC escribiendo el siguiente comando:

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```
    Debería ver una salida similar a la siguiente: 
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet  myPublicIP3
     
9. Agregue al sistema operativo de la VM las direcciones IP privadas que agregó a la NIC siguiendo las instrucciones de la sección [Incorporación de direcciones IP a un sistema operativo de la VM](#os-config) de este artículo. No agregue las direcciones IP públicas al sistema operativo.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

