---
title: Lista de cuentas de almacenamiento de Azure
description: "Administrar la configuración de su cuenta de almacenamiento con el Kit de herramientas de Azure para Eclipse"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: bbacfcd8-dbf5-4265-a930-59f508de5325
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 9ef430dfaa43f9bc4294593c8abbdaf457ef07c8


---
# <a name="azure-storage-account-list"></a>Lista de cuentas de almacenamiento de Azure
Las cuentas de almacenamiento de Azure permiten el uso de las ubicaciones de descarga para su JDK, el servidor de aplicaciones y los componentes arbitrarios, así como para almacenar el estado cuando se usa el almacenamiento en caché. Eclipse mantiene una lista de cuentas de almacenamiento conocidas que están disponibles para sus proyectos en el área de trabajo de Eclipse. Para abrir el cuadro de diálogo **Cuentas de almacenamiento** que se usa para administrar esa lista, en Eclipse, haga clic en **Ventana**, en **Preferencias**, expanda **Azure** y luego haga clic en **Cuentas de almacenamiento**.

Lo siguiente muestra el cuadro de diálogo **Cuentas de almacenamiento** .

![][ic719496]

También se puede abrir este cuadro de diálogo desde un vínculo **Cuentas** en cuadros de diálogo que usan cuentas de almacenamiento, como los siguientes:

* La pestaña **JDK** del cuadro de diálogo **Configuración del servidor**.
* La pestaña **Servidor** del cuadro de diálogo **Configuración del servidor**.
* El cuadro de diálogo **Agregar componente** .
* El cuadro de diálogo de propiedades de **Almacenamiento en caché** .

## <a name="to-import-your-storage-accounts-using-a-publish-settings-file"></a>Para importar sus cuentas de almacenamiento mediante un archivo de configuración de publicación
1. Dentro del cuadro de diálogo **Cuentas de almacenamiento**, haga clic en **Importar desde el archivo PUBLISH-SETTINGS**.
2. (Omita este paso si ya ha guardado un archivo de configuración de publicación en su máquina local). En el cuadro de diálogo **Importar desde el archivo de suscripción**, haga clic en **Descargar archivo PUBLISH-SETTINGS**. Si todavía no ha iniciado sesión en su cuenta de Azure, se le solicitará que lo haga. Después, se le solicitará que guarde un archivo de configuración de publicación de Azure. (Puede ignorar las instrucciones resultantes que se muestran en las páginas de inicio de sesión: se ofrecen por el portal de Azure y están pensadas para los usuarios de Visual Studio.) Guárdelo en su equipo local.
3. Todavía en el cuadro de diálogo **Importar información de suscripción**, haga clic en el botón **Examinar**, seleccione el archivo de configuración de publicación que guardó localmente anteriormente y luego haga clic en **Abrir**.
4. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Importar información de la suscripción**.

## <a name="to-create-a-new-storage-account"></a>Para crear una cuenta de almacenamiento
1. En el cuadro de diálogo **Cuentas de almacenamiento**, haga clic en **Agregar**.
2. En el cuadro de diálogo **Agregar cuenta de almacenamiento**, haga clic en **Nueva**.
3. En el cuadro de diálogo **Nueva cuenta de almacenamiento** , especifique los siguientes valores:
   * Nombre de la cuenta de almacenamiento.
   * Ubicación de la cuenta de almacenamiento.
   * Descripción de la cuenta de almacenamiento.
   * La suscripción a la que pertenece la cuenta de almacenamiento.
4. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Nueva cuenta de almacenamiento**.

Se puede tardar unos minutos en crearse su cuenta de almacenamiento. Una vez creada, haga clic en **Aceptar** para cerrar el cuadro de diálogo **Agregar cuenta de almacenamiento** y se agregará la nueva cuenta de almacenamiento a la lista de cuentas de almacenamiento disponibles.

## <a name="to-add-an-existing-storage-account-to-the-list"></a>Para agregar una cuenta de almacenamiento existente a la lista
1. Si no dispone ya de una cuenta de almacenamiento de Azure, cree una siguiendo los pasosde la sección **Creación de una cuenta de almacenamiento nueva** que aparece anteriormente. (También puede crear una cuenta de almacenamiento nueva en el [Portal de administración de Azure][Azure Management Portal]).
2. En el cuadro de diálogo **Cuentas de almacenamiento**, haga clic en **Agregar**.
3. En el cuadro de diálogo **Agregar cuenta de almacenamiento**, especifique valores para **Nombre** y **Clave de acceso**. El nombre de la cuenta y la clave de acceso deben ser para una cuenta de almacenamiento de Azure existente. Use la sección **Almacenamiento** del [Portal de administración de Azure][Azure Management Portal] para ver sus claves y nombres de cuenta de almacenamiento. Su cuadro de diálogo **Agregar cuenta de almacenamiento** tendrá un aspecto similar al siguiente.
   
    ![][ic719497]
4. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Agregar cuenta de almacenamiento**.

## <a name="to-modify-a-storage-account-to-use-a-new-access-key"></a>Para modificar una cuenta de almacenamiento para que use una nueva clave de acceso
1. En el cuadro de diálogo **Cuentas de almacenamiento**, haga clic en la cuenta de almacenamiento que quiera editar y luego haga clic en **Editar**.
2. En el cuadro de diálogo **Editar la clave de acceso de la cuenta de almacenamiento**, modifique el valor de la **Clave de acceso**.
3. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Editar la clave de acceso de la cuenta de almacenamiento**.

## <a name="to-remove-a-storage-account-from-the-list-maintained-in-eclipse"></a>Para quitar una cuenta de almacenamiento de la lista mantenida en Eclipse
1. En el cuadro de diálogo **Cuentas de almacenamiento**, haga clic en la cuenta de almacenamiento que quiera editar y luego haga clic en **Quitar**.
2. Haga clic en **Aceptar** cuando se pregunte si quiere quitar la cuenta de almacenamiento.

> [!NOTE]
> Al quitar la cuenta de almacenamiento mediante el cuadro de diálogo **Cuentas de almacenamiento** solo se quita de la lista de cuentas de almacenamiento que se pueden ver en Eclipse. No elimina la cuenta de almacenamiento de su suscripción de Azure. Además, la cuenta de almacenamiento podría aparecer de nuevo en la lista después de que Eclipse vuelva a cargar los detalles de su suscripción.
> 
> 

## <a name="see-also"></a>Otras referencias
[kit de herramientas de Azure para Eclipse][Azure Toolkit for Eclipse]

[Instalación del Kit de herramientas de Azure para Eclipse][Installing the Azure Toolkit for Eclipse] 

[Creación de una aplicación Hola a todos para Azure en Eclipse][Creating a Hello World Application for Azure in Eclipse]

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure][Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->



<!--HONumber=Jan17_HO1-->


