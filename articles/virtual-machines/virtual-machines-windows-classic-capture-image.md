---
title: "Captura de una imagen de una máquina virtual Windows de Azure | Microsoft Docs"
description: "Capture una imagen de una máquina virtual Windows de Azure creada con el modelo de implementación clásica."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: a5986eac-4cf3-40bd-9b79-7c811806b880
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: 6b68d41daeea780d70b5ce1389d05f1f4fdf65ea


---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Capture una imagen de una máquina virtual Windows de Azure creada con el modelo de implementación clásica.
> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Para más información sobre el modelo de Resource Manager, consulte [Create a copy Windows VM running in Azure](virtual-machines-windows-vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Creación de una copia de una máquina virtual Windows que se ejecuta en Azure).

En este artículo se muestra cómo puede capturar una máquina virtual de Azure con Windows para usarla como imagen en la creación de otras máquinas virtuales. Esta imagen incluye el disco del sistema operativo y los discos de datos que están conectados a la máquina virtual. No incluye configuraciones de red, por lo que deberá configurarlas usted mismo cuando cree las otras máquinas virtuales que usen la imagen.

Azure almacena la imagen en **Mis imágenes**. Este es el mismo lugar donde se almacenan las imágenes que se han cargado. Para obtener más información acerca de las imágenes, vea [Acerca de las imágenes para las máquinas virtuales](virtual-machines-linux-classic-about-images.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="before-you-begin"></a>Antes de empezar
Para seguir estos pasos se supone que ya ha creado un máquina virtual Azure y ha configurado el sistema operativo, incluida la anexión de cualquier disco de datos. Si no ha realizado esto todavía, siga estas instrucciones:

* [Cree una máquina virtual desde una imagen](virtual-machines-windows-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Acoplamiento de un disco de datos a una máquina virtual](virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* Asegúrese de que los roles de servidor admiten Sysprep. Para más información, consulte [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)(Compatibilidad de Sysprep con roles de servidor).

> [!WARNING]
> Este proceso elimina la máquina virtual original después de capturarla. 
> 
> 

Antes de capturar una imagen de una máquina virtual de Azure, se recomienda hacer una copia de seguridad de la máquina virtual de destino. Esto se puede hacer mediante Copia de seguridad de Azure. Para obtener más información, consulte [Copia de seguridad de máquinas virtuales de Azure](../backup/backup-azure-vms.md). Hay otras soluciones disponibles de socios certificados. Para averiguar lo que está actualmente disponible, busque Azure Marketplace.

## <a name="capture-the-virtual-machine"></a>Captura de la máquina virtual
1. En el [Portal de Azure clásico](http://manage.windowsazure.com), **Conecte** con la máquina virtual. Para obtener instrucciones, consulte [Inicio de sesión en una máquina virtual con Windows Server][How to sign in to a virtual machine running Windows Server].
2. Abra una ventana de símbolo del sistema como administrador.
3. Cambie el directorio a `%windir%\system32\sysprep`y después ejecute sysprep.exe.
4. Aparecerá el cuadro de diálogo **Herramienta de preparación del sistema** . Haga lo siguiente:
   
   * En **Acción de limpieza del sistema**, seleccione **Iniciar la configuración rápida (OOBE) del sistema ** y asegúrese de que la opción **Generalizar** está marcada. Para obtener más información sobre el uso de Sysprep, consulte [Uso de Sysprep: Introducción][How to Use Sysprep: An Introduction].
   * En **Opciones de apagado**, seleccione **Apagar**.
   * Haga clic en **Aceptar**.
   
   ![Ejecute Sysprep](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)
5. Sysprep apaga la máquina virtual, lo que cambia su estado en el Portal de Azure clásico a **Detenido**.
6. En el Portal de Azure clásico, haga clic en **Máquinas virtuales** y seleccione la máquina virtual que desee capturar.
7. En la barra de comandos, haga clic en **Capturar**.
   
   ![Capture la máquina virtual](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)
   
   Aparece el cuadro de diálogo **Capturar la máquina virtual** .
8. En **Nombre de la imagen**, escriba un nombre para la nueva imagen.
9. Antes de agregar una imagen de Windows Server al conjunto de imágenes personalizadas, ejecute Sysprep para generalizarla siguiendo las instrucciones de los pasos anteriores. Haga clic en **He ejecutado Sysprep en la máquina virtual** para indicar que lo ha hecho.
10. Haga clic en la marca de verificación para capturar la imagen. La nueva imagen ahora está disponible en **Imágenes**.
    
    ![Captura correcta de la imagen](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Pasos siguientes
La imagen está lista para usarse para crear máquinas virtuales. Para ello, creará una máquina virtual mediante el elemento del menú **Desde la galería** y seleccionando la imagen que acaba de crear. Para obtener instrucciones, consulte [Creación de una máquina virtual desde una imagen](virtual-machines-windows-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[How to sign in to a virtual machine running Windows Server]: virtual-machines-windows-classic-connect-logon.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png



<!--HONumber=Feb17_HO3-->


