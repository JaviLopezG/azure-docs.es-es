---
title: "Tutorial: Integración de Azure Active Directory con Overdrive Books | Microsoft Docs"
description: "Aprenda cómo usar Overdrive Books con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e68cede7-1130-4813-bd55-22a9a6fc6bf5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: 35c1dfb5aaee4427f40e72a2293c756ae4fcb76c


---
# <a name="tutorial-azure-active-directory-integration-with-overdrive-books"></a>Tutorial: Integración de Azure Active Directory con Overdrive Books
El objetivo de este tutorial es mostrar la integración de Azure y OverDrive.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en OverDrive

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a OverDrive podrán realizar un inicio de sesión único en la aplicación en el sitio de OverDrive de la compañía (inicio de sesión iniciado por el proveedor de servicios) o con la [introducción al panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para OverDrive
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Escenario")

## <a name="enabling-the-application-integration-for-overdrive"></a>Habilitación de la integración de aplicaciones para OverDrive
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para OverDrive.

**Siga estos pasos para habilitar la integración de aplicaciones para OverDrive:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **OverDrive**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **OverDrive** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![OverDrive](./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
   
## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en OverDrive con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

**Siga estos pasos para configurar el inicio de sesión único:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **OverDrive**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Habilitar inicio de sesión único](./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "Habilitar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en OverDrive?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de OverDrive**, escriba su dirección URL con el siguiente patrón *http://mslibrarytest.libraryreserve.com* y luego haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "Configurar dirección URL de la aplicación")
4. En la página **Configurar inicio de sesión único en OverDrive** , para descargar el archivo de metadatos, y luego envíelo al equipo de soporte de OverDrive.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "Configurar inicio de sesión único")
   
   > [!NOTE]
   > El equipo de soporte técnico de OverDrive configura para usted el inicio de sesión único y le envía una notificación cuando se ha completado la configuración.
   > 
   > 
5. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "Configurar inicio de sesión único")
   
## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

No hay elemento de acción para que configure el aprovisionamiento de usuarios en OverDrive.  
Cuando un usuario asignado intenta iniciar sesión en OverDrive, se crea automáticamente una cuenta de OverDrive si es necesario.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de OverDrive que proporcione OverDrive para aprovisionar cuentas de usuario de AAD.
> 
 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar usuarios a OverDrive, lleve a cabo los siguientes pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones **OverDrive**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Asignar usuarios")
   
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Feb17_HO1-->


