---
title: "Información general sobre registros y zonas DNS - Azure DNS | Microsoft Docs"
description: "Información general de soporte técnico para hospedar registros y zonas DNS en DNS de Microsoft Azure."
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
editor: 
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 119275f335344858cd20b6a17ef87e3ef32b6e12
ms.openlocfilehash: 4e25ec1ece6017dc58c24ce593802293b7fc12b8
ms.lasthandoff: 03/01/2017

---

# <a name="overview-of-dns-zones-and-records"></a>Información general sobre zonas y registros de DNS

En esta página se explican los conceptos básicos sobre dominios, zonas DNS y registros y conjuntos de registros DNS, y cómo se admiten en DNS de Azure.

## <a name="domain-names"></a>Nombres de dominio

El sistema de nombres de dominio es una jerarquía de dominios. La jerarquía empieza por el dominio "raíz", cuyo nombre es sencillamente "**.**".  Después de él, se encuentran los dominios de primer nivel, a saber, “com”, “net”, “org”, “uk” o “jp”.  A continuación, se colocan los dominios de segundo nivel, como “org.uk” o “co.jp”. Los dominios están distribuidos globalmente, hospedados por servidores de nombres DNS de todo el mundo.

Un registrador de nombres de dominio es una organización que le permite adquirir un nombre de dominio, como "contoso.com".  Comprar un nombre de dominio le concede el derecho a controlar la jerarquía DNS bajo ese nombre, por ejemplo, permitiéndole dirigir el nombre "www.contoso.com" al sitio web de empresa. El propio registrador puede hospedar el dominio del usuario en los servidores de nombres de este último, o bien el usuario puede especificar servidores de nombres alternativos.

DNS de Azure proporciona una infraestructura de servidores de nombre de alta disponibilidad distribuida globalmente, que se puede utilizar para hospedar el dominio. Al hospedar los dominios en DNS de Azure, puede administrar los registros DNS con las mismas credenciales, interfaces API, herramientas, facturación y soporte técnico que con los demás servicios de Azure.

DNS de Azure actualmente no admite la adquisición de nombres de dominio. Si desea adquirir un dominio, debe usar un registrador de nombres de dominio de un tercero. El registrador suele cobrar una tarifa anual reducida. Los dominios se pueden hospedar en DNS de Azure para la administración de registros de DNS. Consulte [Delegación de un dominio en DNS de Azure](dns-domain-delegation.md) para más información.

## <a name="dns-zones"></a>Zonas DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Registros DNS

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Período de vida

El período de vida, o TTL, especifica durante cuánto tiempo los clientes almacenan cada registro en caché antes de volver a consultarlo. En el ejemplo anterior, el TTL es 3600 segundos o 1 hora.

En DNS de Azure, el TTL se especifica para el conjunto de registros, no para cada registro, por lo que se usa el mismo valor para todos los registros del conjunto de registros.  Se puede especificar cualquier valor TTL entre 1 y 2 147 483 647 segundos.

### <a name="wildcard-records"></a>Registros de carácter comodín

DNS de Azure admite [registros de carácter comodín](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Estos se devuelven como respuesta a cualquier consulta con un nombre coincidente (a menos que haya una coincidencia más próxima de un conjunto de registros que no sean de caracteres comodín). Azure DNS admite los conjuntos de registros de carácter comodín para todos los tipos de registros, excepto NS y SOA.

Para crear un conjunto de registros comodín, utilice el nombre de conjunto de registros "\*". Como alternativa, también puede utilizar un nombre con "\*" como su etiqueta a la izquierda, por ejemplo,"\*.foo".

### <a name="cname-records"></a>Registros CNAME

Los conjuntos de registros CNAME no pueden coexistir con otros conjuntos de registros que tienen el mismo nombre. Por ejemplo, no se puede crear un conjunto de registros CNAME con el nombre relativo "www" y un registro A con el nombre relativo "www" al mismo tiempo.

Dado que el vértice de la zona (nombre = "@") siempre contiene los conjuntos de registros NS y SOA creados cuando se genera la zona, no puede crear un conjunto de registros CNAME en el vértice de la zona.

Estas restricciones surgen de los estándares DNS; no son limitaciones de DNS de Azure.

### <a name="ns-records"></a>Registros NS

Un conjunto de registros NS se crea automáticamente en el vértice de cada zona (nombre = "@") y se elimina automáticamente cuando se elimina la zona (no se puede eliminar por separado).  Se puede modificar el TTL del conjunto de registros, pero no los registros, que están preconfigurados para hacer referencia a los servidores de nombres de DNS de Azure asignados a la zona.

Puede crear y eliminar otros registros NS dentro de la zona, aparte de en el vértice de zona.  Esto le permite configurar zonas secundarias (vea [Delegación de subdominios en DNS de Azure](dns-domain-delegation.md#delegating-sub-domains-in-azure-dns)).

### <a name="soa-records"></a>Registros SOA

Un conjunto de registros SOA se crea automáticamente en el vértice de cada zona (nombre = "@") y se elimina automáticamente cuando se elimina la zona.  Los registros SOA no pueden crearse ni eliminarse por separado.

Puede modificar todas las propiedades del registro SOA excepto la propiedad 'host', que está preconfigurada para hacer referencia al nombre del servidor de nombres principal proporcionado por DNS de Azure.

### <a name="spf-records"></a>Registros SPF

Los registros de marco de directivas de remitente (SPF) se utilizan para especificar los servidores de correo electrónico que tienen permiso para enviar correo en nombre de un nombre de dominio determinado.  Es importante configurar correctamente los registros SPF para evitar que los destinatarios marquen el correo como 'correo no deseado'.

Las RFC de DNS originalmente introdujeron un nuevo tipo de registro "SPF" que admite este escenario. Para admitir servidores de nombres anteriores, permiten además el uso del tipo de registro TXT para especificar registros SPF.  Esta ambigüedad llevó a confusión, que se resolvió mediante [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1).  Esta indica que solo deben crearse registros SPF con el tipo de registro TXT, y que el tipo de registro SPF ha quedado en desuso.

**Los registros SPF son compatibles con DNS de Azure y deben crearse con el tipo de registro TXT.** No se admite el tipo de registro SPF obsoleto. Al [importar un archivo de zona DNS](dns-import-export.md), los registros SPF con el tipo de registro SPF se convierten al tipo de registro TXT.

### <a name="srv-records"></a>Registros SRV

Los [registros SRV](https://en.wikipedia.org/wiki/SRV_record) se utilizan en diversos servicios para especificar ubicaciones de servidor. Al especificar un registro SRV en DNS de Azure:

* Debe especificarse el *servicio* y el *protocolo* como parte del nombre del conjunto de registros, precedidos por caracteres de subrayado.  Por ejemplo, "\_sip.\_tcp.name".  En un registro en el vértice de la zona, no es necesario especificar "@" en el nombre del registro, simplemente utilice el servicio y el protocolo, por ejemplo, "\_sip.\_tcp".
* La *prioridad*, el *peso*, el *puerto* y el *destino* se especifican como parámetros de cada registro del conjunto de registros.

### <a name="txt-records"></a>Registros TXT

Los registros TXT se usan para asignar nombres de dominio a cadenas de texto arbitrarias. Se utilizan en varias aplicaciones, especialmente en lo referente a la configuración de correo electrónico, como el [marco de directivas de remitente (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) y el [correo identificado por claves de dominio (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

Los estándares DNS permiten que un único registro TXT contenga varias cadenas, y cada una de ellas puede tener una longitud máxima de 254 caracteres. Cuando se utilizan varias cadenas, se concatenan según los clientes y se tratan como una sola cadena.

Cuando se llama a la API de REST de Azure DNS, debe especificar cada cadena TXT por separado.  Al usar las interfaces de Azure Portal, PowerShell o CLI, debe especificar una sola cadena por cada registro, que automáticamente se divide en segmentos de 254 caracteres, si es necesario.

Las distintas cadenas de un registro DNS no deben confundirse con los diferentes registros TXT de un conjunto de registros TXT.  Un conjunto de registros TXT puede contener varios registros, *cada uno de los cuales* puede contener varias cadenas.  Azure DNS admite una longitud de cadena total de hasta 1024 caracteres en cada conjunto de registros TXT (entre todos los registros combinados). 

## <a name="tags-and-metadata"></a>Etiquetas y metadatos

### <a name="tags"></a>Etiquetas

Las etiquetas son una lista de pares nombre-valor que Azure Resource Manager usa para etiquetar los recursos.  Azure Resource Manager utiliza etiquetas para habilitar vistas filtradas de la factura de Azure y también permite establecer una directiva en la que se requieran etiquetas. Para obtener más información sobre las etiquetas, consulte [Uso de etiquetas para organizar los recursos de Azure](../azure-resource-manager/resource-group-using-tags.md).

DNS de Azure admite el uso de etiquetas de Azure Resource Manager en recursos de zona DNS.  No admite etiquetas en conjuntos de registros de DNS, aunque, como alternativa, se admiten "metadatos" en estos tipos de conjuntos, como se explica a continuación.

### <a name="metadata"></a>Metadatos

Como alternativa a las etiquetas de conjunto de registros, DNS de Azure admite la anotación de conjuntos de registros mediante "metadatos".  De forma similar a las etiquetas, los metadatos permiten asociar pares nombre-valor a cada conjunto de registros.  Esto puede ser útil, por ejemplo, para registrar el propósito de cada conjunto de registros.  A diferencia de las etiquetas, los metadatos no se pueden usar para proporcionar una vista filtrada de la factura de Azure ni se pueden especificar en una directiva de Azure Resource Manager.

## <a name="etags"></a>Etag

Supongamos que dos personas o dos procesos tratan de modificar un registro DNS al mismo tiempo. ¿Cuál gana? ¿Y el ganador sabe que ha sobrescrito cambios creados por otra persona?

DNS de Azure usa Etag para administrar de forma segura los cambios simultáneos realizados al mismo recurso. Las etiquetas de entidad (ETag) son independientes de las ["etiquetas" de Azure Resource Manager](#tags). Cada recurso DNS (zona o conjunto de registros) tiene un valor de Etag asociado a él. Siempre que se recupera un recurso, también se recupera su Etag. Al actualizar un recurso, puede elegir entre devolver el valor de ETag para que Azure DNS pueda verificar dicho valor en las coincidencias de servidor. Puesto que cada actualización a un recurso conlleva la regeneración de Etag, una incoherencia de Etag indica que se ha producido un cambio simultáneo. Los valores de ETag también se pueden usar al crear un recurso para asegurarse de que este no existe aún.

De forma predeterminada, PowerShell para DNS de Azure utiliza Etags para bloquear los cambios simultáneos a zonas y conjuntos de registros. El modificador *-Overwrite* se puede usar para suprimir las comprobaciones de ETag, en cuyo caso, se sobrescribirán todos los cambios simultáneos que se hayan producido.

En el nivel de la API de REST de DNS de Azure, los valores de Etag se especifican mediante los encabezados HTTP.  Su comportamiento se indica en la siguiente tabla:

| Encabezado | Comportamiento |
| --- | --- |
| None |PUT always succeeds (no Etag checks) |
| If-match <etag> |PUT only succeeds if resource exists and Etag matches |
| If-match * |PUT only succeeds if resource exists |
| If-none-match * |PUT only succeeds if resource does not exist |


## <a name="limits"></a>límites

Se aplican los límites predeterminados siguientes cuando se usa DNS de Azure:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Pasos siguientes

* Para empezar a usar DNS de Azure, vea cómo [crear una zona DNS](dns-getstarted-create-dnszone-portal.md) y [crear registros DNS](dns-getstarted-create-recordset-portal.md).
* Para migrar una zona DNS existente, vea cómo [importar y exportar un archivo de zona DNS](dns-import-export.md).


