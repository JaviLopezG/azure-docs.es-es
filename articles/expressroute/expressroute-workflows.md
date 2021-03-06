---
title: Flujo de trabajo para configurar un circuito ExpressRoute | Microsoft Docs
description: "Esta página le guiará a través de los flujos de trabajo para configurar el circuito ExpressRoute y las configuraciones entre pares"
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: 55e0418c-e0bf-44a7-9aa1-720076df9297
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 570a98aafca6babc5a7089880d4120c2a8f4a0d8
ms.openlocfilehash: 5a275620639a801d7e60ef9ada1af29062dfe440


---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Flujos de trabajo de ExpressRoute para aprovisionamiento de circuitos y estados de circuitos de ExpressRoute
Esta página le guiará a través del aprovisionamiento de servicios y de los flujos de trabajo de configuración del enrutamiento a alto nivel.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Tanto la ilustración como los pasos correspondientes siguientes muestran las tareas que se deben realizar para aprovisionar un circuito ExpressRoute de un extremo a otro. 

1. Use PowerShell para configurar un circuito ExpressRoute. Para obtener más información, siga las instrucciones del artículo [Creación y modificación de circuitos de ExpressRoute](expressroute-howto-circuit-classic.md) .
2. Solicite conectividad al proveedor de servicios. Este proceso varía. Para obtener más información sobre cómo solicitar conectividad, póngase en contacto con su proveedor de conectividad.
3. Asegúrese de que el circuito se ha aprovisionado correctamente, para lo que debe comprobar el estado de aprovisionamiento del circuito ExpressRoute a través de PowerShell. 
4. Configure los dominios de enrutamiento. Si el proveedor de conectividad administra el nivel 3, configurará el enrutamiento del circuito. Si el proveedor de conectividad solo ofrece servicios de nivel 2, debe configurar el enrutamiento según las instrucciones que se describen en las páginas de [requisitos de enrutamiento](expressroute-routing.md) y [configuración de enrutamiento](expressroute-howto-routing-classic.md).
   
   * Habilitar la configuración entre pares privados de Azure: debe habilitar esta configuración entre pares para conectarse a las máquinas virtuales/servicios en la nube implementados en redes virtuales.
   * Habilitar la configuración entre pares públicos de Azure: debe habilitar la configuración entre pares públicos de Azure si desea conectarse a los servicios de Azure hospedados en direcciones IP públicas. Se trata de un requisito para acceder a los recursos de Azure si ha elegido habilitar el enrutamiento predeterminado para la configuración entre pares privados de Azure.
   * Habilitar la configuración entre pares de Microsoft: debe habilitarlo para acceder a Office 365 y a los servicios en línea de CRM. 
     
     > [!IMPORTANT]
     > Debe asegurarse de que usa un proxy o borde independientes para conectarse a Microsoft distinto del que usa para Internet. Si usa el mismo borde para ExpressRoute e Internet se producirá un enrutamiento asimétrico y causará interrupciones en la conectividad de la red.
     > 
     > 
     
     ![](./media/expressroute-workflows/routing-workflow.png)
5. Vinculación de redes virtuales a circuitos de ExpressRoute: puede vincular redes virtuales a un circuito ExpressRoute. Siga las instrucciones [para vincular redes virtuales](expressroute-howto-linkvnet-arm.md) al circuito. Dichas redes virtuales pueden estar en la misma suscripción de Azure que el circuito ExpressRoute, o bien pueden estar en una suscripción diferente.

## <a name="expressroute-circuit-provisioning-states"></a>Estados de aprovisionamiento de circuitos de ExpressRoute
Cada circuito ExpressRoute tiene dos estados:

* Estado de aprovisionamiento de proveedor de servicio
* Estado

Estado representa el estado de aprovisionamiento de Microsoft. Esta propiedad se establece en Habilitado al crear un circuito Expressroute.

El estado de aprovisionamiento del proveedor de conectividad representa el estado del lado del proveedor de conectividad. Puede ser *No aprovisionado*, *Aprovisionando* o *Aprovisionado*. El circuito ExpressRoute debe estar en estado Aprovisionado para poder usarlo.

### <a name="possible-states-of-an-expressroute-circuit"></a>Posibles estados de un circuito ExpressRoute
En esta sección se enumeran los posibles estados de un circuito ExpressRoute.

**En tiempo de creación**

Verá el circuito ExpressRoute en el estado siguiente en cuanto ejecute el cmdlet de PowerShell para crear el circuito ExpressRoute.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Cuando el proveedor de conectividad está en el proceso de aprovisionamiento del circuito**

Verá el circuito ExpressRoute en el estado siguiente en cuanto pase la clave de servicio al proveedor de conectividad y hayan iniciado el proceso de aprovisionamiento.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Cuando el proveedor de conectividad haya completado el proceso de aprovisionamiento**

Verá el circuito ExpressRoute en el estado siguiente en cuanto el proveedor de conectividad haya completado el proceso de aprovisionamiento.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Aprovisionado y habilitado es el único estado en que puede estar el circuito para poder usarlo. Si usa un proveedor de nivel 2, puede configurar el enrutamiento para el circuito solo cuando se encuentre en este estado.

**Cuando el proveedor de conectividad está desaprovisionando el circuito**

Si solicitó al proveedor de servicios que desaprovisione el circuito ExpressRoute, verá el circuito establecido en el estado siguiente una vez que el proveedor de servicios haya completado el proceso de desaprovisionamiento.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Si es necesario, puede volver a habilitarlo, o bien ejecutar los cmdlets de PowerShell para eliminar el circuito.  

> [!IMPORTANT]
> Si ejecuta el cmdlet de PowerShell para eliminar el circuito cuando el estado de ServiceProviderProvisioningState es Aprovisionamiento o Aprovisionado, se producirá un error en la operación. Colabore con el proveedor de conectividad para desaprovisionar el circuito ExpressRoute primero y, después, eliminar el circuito. Microsoft seguirá facturando el circuito hasta que se ejecute el cmdlet de PowerShell para eliminar el circuito.
> 
> 

## <a name="routing-session-configuration-state"></a>Estado de configuración de sesión de enrutamiento
El estado de aprovisionamiento de BGP permite saber si la sesión BGP se ha habilitado en el borde de Microsoft. El estado debe habilitarse para poder usar la configuración entre pares.

Es importante comprobar el estado de sesión de BGP, especialmente para la configuración entre pares de Microsoft. Además del estado de aprovisionamiento de BGP, hay otro estado denominado *estado de prefijos públicos anunciados*. El estado de los prefijos públicos anunciados debe estar en estado *configurado* , tanto para que la sesión BGP esté activa como para que el enrutamiento funcione de un extremo a otro. 

Si el estado de los prefijos públicos anunciados se establece en el estado de *validación necesaria* , la sesión BGP no se habilita, ya que los prefijos anunciados no coincidían con el número AS en ninguno de los registros de enrutamiento. 

> [!IMPORTANT]
> Si el estado de los prefijos públicos anunciados es *validación manual* , debe abrir una incidencia de soporte técnico en el [servicio de soporte técnico de Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) y aportar una evidencia de que posee la dirección IP anunciada, junto con el número del sistema autónomo asociado.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* Configure su conexión ExpressRoute.
  
  * [Creación de un circuito ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configuración del enrutamiento](expressroute-howto-routing-arm.md)
  * [Vinculación de redes virtuales a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md)




<!--HONumber=Nov16_HO3-->


