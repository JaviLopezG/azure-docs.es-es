---
title: "Azure Active Directory B2C: configuración de Google+ | Microsoft Docs"
description: "Proporcione funciones de registro e inicio de sesión a los consumidores con cuentas de Google+ en las aplicaciones protegidas por Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 4dcca66f-29e4-4b4d-8840-50baad736bd7
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: e37c48d6c92a8a2cd480458abdff0a3a1ca9338f
ms.openlocfilehash: dbc544ad4dbb651aeb24faf2f89074c7aa59cf2a


---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure Active Directory B2C: provisión de registro e inicio de sesión a los usuarios con cuentas de Google+
## <a name="create-a-google-application"></a>Creación de una aplicación de Google+
Para usar Google+ como proveedor de identidades en Azure Active Directory (Azure AD) B2C, debe crear una aplicación de Google+ y suministrarle los parámetros correctos. Necesita una cuenta de Google+ para hacerlo. Si no tiene una, puede obtenerla en [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Vaya a [Google Developers Console](https://console.developers.google.com/) e inicie sesión con las credenciales de su cuenta de Google+.
2. Haga clic en **Create project** (Crear proyecto), escriba un nombre de proyecto en **Project name** y haga clic en **Create** (Crear).
   
    ![Google+: introducción](./media/active-directory-b2c-setup-goog-app/google-get-started.png)
   
    ![Google+: nuevo proyecto](./media/active-directory-b2c-setup-goog-app/google-new-project.png)
3. En el panel de navegación izquierdo, haga clic en **API Manager** (Administrador de API) y, luego, en **Credentials** (Credenciales).
4. Haga clic en la **pantalla de autorización de OAuth** en la parte superior.
   
    ![Google+: credenciales](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)
5. Seleccione o especifique una dirección de correo electrónico válida en **Email address**, especifique un nombre de producto en **Product name** y haga clic en **Save** (Guardar).
   
    ![Google+: pantalla de consentimiento de OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)
6. Haga clic en **New credentials** (Nuevas credenciales) y, luego, elija **OAuth client ID** (Id. de cliente de OAuth).
   
    ![Google+: pantalla de consentimiento de OAuth](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)
7. En **Application type** (Tipo de aplicación), seleccione **Web application** (Aplicación web).
   
    ![Google+: pantalla de consentimiento de OAuth](./media/active-directory-b2c-setup-goog-app/google-web-app.png)
8. Especifique un **nombre** para la aplicación, escriba `https://login.microsoftonline.com` en el campo **Authorized JavaScript origins** (Orígenes de JavaScript autorizados) y `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **Authorized redirect URIs** (URI de redirección autorizados). Reemplace **{tenant}** por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com). El valor de **{tenant}** distingue mayúsculas de minúsculas. Haga clic en **Create**(Crear).
   
    ![Google+: creación del identificador de cliente](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)
9. Copie los valores de **Client ID** y **Client Secret**. Necesitará ambos para configurar Google+ como proveedor de identidades de su inquilino. **Secreto del cliente** es una credencial de seguridad importante.
   
    ![Google+: secreto de cliente](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Configuración de Google+ como proveedor de identidades del inquilino
1. Siga estos pasos para [ir a la hoja de características de B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) del Portal de Azure.
2. En la hoja de características B2C, haga clic en **Proveedores de identidades**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, "G+".
5. Haga clic en **Identity provider type** (Tipo de proveedor de identidades), seleccione **Google** y haga clic en **OK** (Aceptar).
6. Haga clic en **Set up this identity provider** (Configurar este proveedor de identidades) y escriba el identificador de cliente y el secreto de cliente de la aplicación de Google+ que creó anteriormente.
7. Haga clic en **OK** (Aceptar) y, luego, en **Create** (Crear) para guardar la configuración de Google+.




<!--HONumber=Feb17_HO2-->


