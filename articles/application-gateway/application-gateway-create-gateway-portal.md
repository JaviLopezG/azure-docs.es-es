---
title: "Creación de una puerta de enlace de aplicaciones mediante el portal | Microsoft Docs"
description: Aprenda a crear una puerta de enlace de aplicaciones mediante el portal
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: e20f7349f30c309059c2867d7473fa6fdefa9b61
ms.openlocfilehash: f7036e8e629e78c5346688556a5aa5794bde3955


---
# <a name="create-an-application-gateway-by-using-the-portal"></a>Creación de una puerta de enlace de aplicaciones mediante el portal

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Plantilla del Administrador de recursos de Azure](application-gateway-create-gateway-arm-template.md)
> * [CLI de Azure](application-gateway-create-gateway-cli.md)

Puerta de enlace de aplicaciones de Azure es un equilibrador de carga de nivel&7;. Proporciona conmutación por error, solicitudes HTTP de enrutamiento de rendimiento entre distintos servidores, independientemente de que se encuentren en la nube o en una implementación local. Application Gateway proporciona numerosas características del Controlador de entrega de aplicaciones (ADC), entre las que se incluyen el equilibrio de carga HTTP, la afinidad de sesiones basada en cookies, la descarga SSL (Capa de sockets seguros), los sondeos personalizados sobre el estado, la compatibilidad con multisitio, etc. Para obtener una lista completa de las características admitidas, visite [Introducción a Application Gateway](application-gateway-introduction.md)

## <a name="scenario"></a>Escenario

En este escenario, aprenderá a crear una puerta de enlace de aplicaciones mediante el Portal de Azure.

En este escenario:

* Creará una puerta de enlace de aplicaciones media con dos instancias.
* Creará una red virtual denominada AdatumAppGatewayVNET con un bloque CIDR reservado de 10.0.0.0/16.
* Creará una subred denominada Appgatewaysubnet que usa 10.0.0.0/28 como bloque CIDR.
* Configurará un certificado para la descarga SSL.

![Escenario de ejemplo][scenario]

> [!IMPORTANT]
> La configuración adicional de la puerta de enlace de aplicaciones, incluidos los sondeos personalizados sobre el estado, las direcciones del grupo de back-end y las reglas se realiza después de que se configura la puerta de enlace de aplicaciones, no durante la implementación inicial.
> 
> 

## <a name="before-you-begin"></a>Antes de empezar

Puerta de enlace de aplicaciones de Azure requiere su propia subred. Al crear una red virtual, asegúrese de dejar suficiente espacio de direcciones para que tenga varias subredes. Una vez que se implementa una puerta de enlace de aplicaciones en una subred adicional solo se pueden agregar a ella puertas de enlace de aplicaciones adicionales.

## <a name="create-the-application-gateway"></a>Creación de Application Gateway

### <a name="step-1"></a>Paso 1

Navegue a Azure Portal, haga clic en **Nuevo** > **Redes** > **Application Gateway**

![Creación de una puerta de enlace de aplicaciones][1]

### <a name="step-2"></a>Paso 2

A continuación, rellene la información básica de la puerta de enlace de aplicaciones. Cuando haya terminado, haga clic en **Aceptar**

La información necesaria para la configuración básica es:

* **Nombre** : nombre de la puerta de enlace de aplicaciones.
* **Nivel**: esta configuración es el nivel de la puerta de enlace de aplicaciones. Existen dos niveles **WAF** y **Estándar**. WAF habilita la característica de firewall de aplicaciones web.
* **Tamaño de SKU**: es el tamaño de la puerta de enlace de aplicaciones, las opciones disponibles son (**Pequeño**, **Mediano** y **Grande**). En el nivel WAF, la opción Pequeño no está disponible.
* **Número de instancias** : el número de instancias, este valor debe ser un número comprendido entre 2 y 10.
* **Grupo de recursos** : el grupo de recursos que mantiene la puerta de enlace de aplicaciones, puede ser un grupo de recursos existente o uno nuevo.
* **Ubicación** : la región de la puerta de enlace de aplicaciones, es la misma ubicación en el grupo de recursos. La ubicación es importante, ya que la red virtual y la dirección IP pública deben estar en la misma ubicación que la puerta de enlace.

![hoja que muestra configuración básica][2]

> [!NOTE]
> Para las pruebas se puede elegir 1 en Número de instancias. Es importante saber que el SLA no cubre ningún número de instancias que esté por debajo de las dos instancias y, por consiguiente, no se recomienda. Las puertas de enlace pequeñas se deben usar para pruebas de desarrollo, no con fines de producción.
> 
> 

### <a name="step-3"></a>Paso 3

Una vez que se define la configuración básica, el paso siguiente es definir la red virtual que se va a usar. La red virtual albergará la aplicación para la que la puerta de enlace de aplicaciones no equilibra la carga.

Haga clic en **Elegir una red virtual** para configurar la red virtual.

![hoja que muestra configuración de puerta de enlace de aplicaciones][3]

### <a name="step-4"></a>Paso 4

En la hoja **Elegir red virtual** , haga clic en **Crear nuevo**

Aunque no se explica en este escenario, en este momento se puede seleccionar una red virtual existente.  Si se utiliza una red virtual existente, es importante saber que necesita una subred vacía o una de red virtual con solo los recursos de la puerta de enlace de aplicaciones para utilizarse.

![elegir hoja de red virtual][4]

### <a name="step-5"></a>Paso 5

Rellene la información de la red en la hoja **Crear red virtual** como se describe en la descripción de [Escenario](#scenario) anterior.

![Crear hoja de red virtual con la información especificada][5]

### <a name="step-6"></a>Paso 6

Una vez creada la red virtual, el siguiente paso es definir la dirección IP de front-end de la puerta de enlace de aplicaciones. En este momento, es preciso elegir si la dirección IP de front-end es pública o privada. La elección depende de si la aplicación tiene acceso a Internet o es interna. En este escenario se asume que se usa una dirección IP pública. Para elegir una dirección IP privada, se puede hacer clic en el botón **Privada** . Se elige una dirección IP asignada automáticamente, o bien se puede hacer clic en la casilla **Elija una dirección IP privada específica** para especificarla manualmente.

### <a name="step-7"></a>Paso 7

Haga clic en **Elegir una dirección IP pública**. Si hay alguna dirección IP pública existente disponible, este es el momento de elegirla, en este escenario se crea una nueva dirección IP pública. Haga clic en **Crear nuevo**

![Elegir hoja de dirección IP pública][6]

### <a name="step-8"></a>Paso 8

A continuación, asigne un nombre descriptivo a la dirección IP pública y haga clic en **Aceptar**

![Crear hoja de dirección IP pública][7]

### <a name="step-9"></a>Paso 9:

El último ajuste que se configura al crear una puerta de enlace de aplicaciones es la configuración del agente de escucha.  Si se usa **http**, no queda nada por configurar, así que se puede hacer clic en **Aceptar**. Para usar **https** , se requiere mayor configuración.

Para usar **https**, se requiere un certificado. La clave privada del certificado es necesaria. Es preciso proporcionar un .pfx exportado del certificado y la contraseña.

### <a name="step-10"></a>Paso 10

Haga clic en **HTTPS**, haga clic en el icono de **carpeta** que hay junto al cuadro de texto **Cargar certificado PFX**.
Navegue hasta el archivo de certificados .pfx en el sistema de archivos. Una vez seleccionado, asigne un nombre descriptivo al certificado y escriba la contraseña del archivo. pfx.

Cuando termine, haga clic en **Aceptar** para revisar la configuración de Puerta de enlace de aplicaciones.

![Sección Configuración de agente de escucha en la hoja Configuración][9]

### <a name="step-11"></a>Paso 11

Revise la página Resumen y haga clic en **Aceptar**.  La puerta de enlace de aplicaciones se pone en cola y se crea.

### <a name="step-12"></a>Paso 12

Una vez creada la puerta de enlace de aplicaciones, navegue hasta ella en el portal para continuar con su configuración.

![Vista de recursos de Puerta de enlace de aplicaciones][10]

Estos pasos permiten crear una puerta de enlace de aplicaciones básica con la configuración predeterminada para el agente de escucha, el grupo de back-end, la configuración de http de back-end y las reglas. Esta configuración se puede modificar para adaptarse a la implementación una vez que el aprovisionamiento sea correcto.

## <a name="add-servers-to-backend-pools"></a>Adición de servidores a grupos de back-end

Cuando se crea la puerta de enlace de aplicaciones, los sistemas que hospeda la aplicación para el equilibrio de carga todavía deben agregarse a la puerta de enlace de aplicaciones. Las direcciones IP o los valores de nombre completo de estos servidores se agregan a los grupos de direcciones de back-end.

### <a name="step-1"></a>Paso 1

Haga clic en la puerta de enlace de aplicaciones que ha creado, haga clic en **Grupos de back-end** y seleccione el grupo de back-end actual.

![Grupos de back-end de Application Gateway][11]

### <a name="step-2"></a>Paso 2

Agregue las direcciones IP o los valores del nombre completo de los cuadros de texto y haga clic en **Guardar**.

![adición de valores a los grupos de back-end de Application Gateway][12]

Esto guarda los valores en el grupo de back-end. Cuando se ha actualizado la puerta de enlace de aplicaciones, se enruta el tráfico que entra en la puerta de enlace de aplicaciones a las direcciones de back-end que se han agregado en este paso.

## <a name="next-steps"></a>Pasos siguientes

En este escenario se crea una puerta de enlace de aplicaciones predeterminada. Los siguientes pasos se usan para configurar la puerta de enlace de aplicaciones mediante la modificación de la configuración y el ajuste de las reglas de la puerta de enlace. Estos pasos se pueden encontrar si consulta los siguientes artículos:

Para aprender a crear sondeos de estado personalizado, visite [Create a custom probe for Application Gateway by using the portal](application-gateway-create-probe-portal.md)

Para aprender a configurar la descarga de SSL y eliminar la cara descripción de SSL de los servidores web, visite [Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el Administrador de recursos de Azure](application-gateway-ssl-portal.md)

Aprenda a proteger aplicaciones con [Firewall de aplicaciones web de Application Gateway (versión preliminar)](application-gateway-webapplicationfirewall-overview.md), una característica de Application Gateway.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[6]: ./media/application-gateway-create-gateway-portal/figure6.png
[7]: ./media/application-gateway-create-gateway-portal/figure7.png
[8]: ./media/application-gateway-create-gateway-portal/figure8.png
[9]: ./media/application-gateway-create-gateway-portal/figure9.png
[10]: ./media/application-gateway-create-gateway-portal/figure10.png
[11]: ./media/application-gateway-create-gateway-portal/figure11.png
[12]: ./media/application-gateway-create-gateway-portal/figure12.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png



<!--HONumber=Dec16_HO2-->


