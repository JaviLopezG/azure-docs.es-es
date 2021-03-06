---
title: "Problemas de reinicio o cambio de tamaño de las máquinas virtuales | Microsoft Docs"
description: "Solución de problemas de la implementación clásica con el reinicio o el cambio de tamaño de una máquina virtual con Linux existente en Azure"
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 73f2672c-602e-4766-8948-2b180115d299
ms.service: virtual-machines-linux
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: required
ms.date: 01/10/2017
ms.devlang: na
ms.author: delhan
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: 846a403ea016e63838af3e6aad09942b6a836499


---
# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>Solución de problemas de la implementación clásica con el reinicio o el cambio de tamaño de una máquina virtual con Linux existente en Azure
> [!div class="op_single_selector"]
> * [Clásico](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
> * [Resource Manager](virtual-machines-linux-restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

Al intentar iniciar una máquina virtual de Azure detenida o cambiar el tamaño de una máquina virtual de Azure existente, es común encontrarse un error de asignación. Dicho error se produce cuando el clúster o la región no tienen recursos disponibles o no admiten el tamaño de máquina virtual solicitado.

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Para la versión de Resource Manager, consulte [aquí](virtual-machines-linux-restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Recopilación de registros de auditoría
Para iniciar la solución de problemas, recopile los registros de auditoría para identificar el error asociado con el problema.

En Azure Portal, haga clic en **Examinar** > **Máquinas virtuales** > *su máquina virtual de Linux* > **Configuración** > **Registros de auditoría**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: Error al iniciar una máquina virtual detenida
Intenta iniciar una máquina virtual detenida, pero obtiene un error de asignación.

### <a name="cause"></a>Causa
La solicitud de iniciar la máquina virtual detenida se debe intentar en el clúster original que hospeda el servicio en la nube. Sin embargo, el clúster no tiene espacio libre disponible para responder a la solicitud.

### <a name="resolution"></a>Resolución
* Cree un nuevo servicio en la nube y asócielo con una región o con una red virtual basada en regiones, pero con no un grupo de afinidad.
* Elimine la máquina virtual detenida.
* Vuelva a crear la máquina virtual en el nuevo servicio en la nube mediante los discos.
* Inicie la máquina virtual recién creada.

Si se produce un error al intentar crear un nuevo servicio en la nube, vuelva a intentarlo más tarde o cambie la región de dicho servicio.

> [!IMPORTANT]
> El nuevo servicio en la nube tendrá un nuevo nombre y dirección VIP, por lo que deberá cambiar dicha información en todas las dependencias que utilicen dicha información para el servicio en la nube existente.
> 
> 

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: error al reiniciar una máquina virtual existente
Intenta cambiar el tamaño de una máquina virtual existente, pero obtiene un error de asignación.

### <a name="cause"></a>Causa
La solicitud de cambiar el tamaño de la máquina virtual se debe realizar en el clúster original que alberga el servicio en la nube. Sin embargo, el clúster no admite el tamaño de máquina virtual solicitado.

### <a name="resolution"></a>Resolución
Reduzca el tamaño de máquina virtual solicitado y vuelva a intentar la solicitud de cambio de tamaño.

* Haga clic en **Examinar todo** > **Máquinas virtuales (clásico)** > *su máquina virtual* > **Configuración** > **Tamaño**. Para información detallada, vea [Cambiar el tamaño de la máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).

Si no es posible reducir el tamaño de la máquina virtual, siga estos pasos:

* Cree un nuevo servicio en la nube, pero asegúrese de que no está vinculado a ningún grupo de afinidad ni asociado a ninguna red virtual vinculada a un grupo de afinidad.
* Cree una máquina virtual nueva y de mayor tamaño en dicho servicio.

Puede consolidar todas las máquinas virtuales en el mismo servicio en la nube. Si el servicio en la nube existente está asociado a una red virtual basada en regiones, puede conectar el nuevo servicio en la nube a la red virtual existente.

Si el servicio en la nube existente no está asociado a una red virtual basada en regiones, tendrá que eliminar sus máquinas virtuales y volver a crearlas en el nuevo servicio en la nube desde los discos. No obstante, es importante recordar que el nuevo servicio en la nube tendrá un nuevo nombre y dirección VIP, por lo que deberá actualizar esta información en todas las dependencias que utilicen esta información para el servicio en la nube existente.

## <a name="next-steps"></a>Pasos siguientes
Si surgen problemas al crear una nueva máquina virtual Linux en Azure, consulte [Solución de problemas de la implementación de Resource Manager con la creación de una nueva máquina virtual de Linux en Azure](virtual-machines-linux-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).




<!--HONumber=Dec16_HO1-->


