---
title: "Solución de problemas de conexión comunes relacionados con la base de datos SQL de Azure"
description: "Pasos para identificar y resolver errores de conexión comunes para la base de datos SQL de Azure."
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: ac463d1c-aec8-443d-b66e-fa5eadcccfa8
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: daleche
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 7e48069b84c1048617a86fbc334a04462b52deda
ms.lasthandoff: 03/10/2017


---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Solución de problemas de conexión de Base de datos SQL de Azure
Cuando la conexión a Base de datos SQL de Azure no se logra establecer, se reciben [mensajes de error](sql-database-develop-error-messages.md). Este artículo es un tema centralizado que ayuda a la solución de problemas de conectividad de Base de datos SQL de Azure. Se presentan [las causas habituales](#cause) de los problemas de conexión, se recomienda [una herramienta de solución de problemas](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) que lo ayuda a identificar el problema y se proporcionan pasos de solución de problemas para resolver [errores transitorios](#troubleshoot-transient-errors) y [errores persistentes o no transitorios](#troubleshoot-the-persistent-errors). Por último, se enumeran [todos los artículos relacionados con la conectividad de Base de datos SQL de Azure](#all-topics-for-azure-sql-database-connection-problems).

Si encuentra problemas de conexión, pruebe los pasos de solución de problemas que se describen en este artículo.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Causa
Los problemas de conexión pueden tener alguna de las siguientes causas:

* Error al aplicar los procedimientos recomendados y las directrices de diseño durante el proceso de diseño de aplicaciones.  Para comenzar, consulte [Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md) .
* Reconfiguración de la Base de datos SQL de Azure
* Configuración de firewall
* Tiempo de espera de conexión agotado
* Información de inicio de sesión incorrecta
* Límite máximo alcanzado en algunos recursos de Base de datos SQL de Azure

Por lo general, los problemas de conexión de Base de datos SQL de Azure se pueden clasificar en los siguientes:

* [Errores transitorios (corta duración o intermitentes)](#troubleshoot-transient-errors)
* [Errores persistentes o no transitorios (errores que se repiten con frecuencia)](#troubleshoot-the-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Pruebe el solucionador de problemas para los problemas de conectividad de Base de datos SQL de Azure
Si se produce un error de conexión específico, pruebe [esta herramienta](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), que le ayudará a identificar y resolver el problema.

## <a name="troubleshoot-transient-errors"></a>Solución de problemas de errores transitorios

Cuando una aplicación se conecte a una base de datos SQL de Azure, recibirá el mensaje de error siguiente:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Este mensaje de error suele ser transitorio (breve).
> 
> 

Este error se produce cuando la base de datos de Azure se está moviendo (o reconfigurando) y la aplicación pierde la conexión con la base de datos SQL. Los eventos de reconfiguración de la base de datos SQL se producen debido a un evento planeado (por ejemplo, una actualización de software) o a un evento no planeado (por ejemplo, el bloqueo de un proceso o un equilibrio de carga). La mayoría de los eventos de reconfiguración son generalmente de corta duración y se completarán en menos de 60 segundos. Sin embargo, ocasionalmente estos eventos pueden tardar más tiempo de finalizar, por ejemplo, cuando una transacción grande produce una recuperación de larga duración.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Pasos para resolver los problemas de conectividad transitorios

1. Compruebe el [panel de Estado de Microsoft Azure](https://azure.microsoft.com/status) para comprobar si hay interrupciones conocidas que se hayan producido durante el tiempo en el que la aplicación informó de los errores.
2. Para las aplicaciones que se conectan a un servicio en la nube, como la base de datos SQL de Azure, se deben prever eventos periódicos de reconfiguración e implementación de la lógica de reintento para gestionar estos errores en lugar de mostrarlos como errores de la aplicación. Consulte la sección [Errores transitorios](sql-database-connectivity-issues.md), así como los procedimientos recomendados e instrucciones de diseño en [Información general de desarrollo de SQL Database](sql-database-develop-overview.md), donde encontrará más información e instrucciones para las estrategias generales de reintento. Después, para información específica, consulte ejemplos de código en [Bibliotecas de conexiones para Base de datos SQL y SQL Server](sql-database-libraries.md) .
3. Conforme una base de datos se acerca a sus límites de recursos, puede parecer un problema de conectividad transitorio. Vea [Solucionar problemas de rendimiento](sql-database-troubleshoot-performance.md).
4. Si los problemas de conectividad continúan, si el tiempo de detección del error por parte de la aplicación supera los 60 segundos o si el error se repite varias veces en un día determinado, realice una solicitud de soporte técnico a Azure; para ello, seleccione **Obtener soporte** en el sitio [Soporte técnico de Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors-non-transient-errors"></a>Solución de problemas de errores persistentes (no transitorios)
Si la aplicación no se puede conectar a la Base de datos SQL de Azure de forma persistente, normalmente indica un problema con uno de los siguientes elementos:

* Configuración del firewall. La base de datos SQL de Azure o el firewall del cliente están bloqueando las conexiones de la base de datos SQL de Azure.
* Reconfiguración de la red en el lado cliente: por ejemplo, un nuevo servidor de proxy o dirección IP.
* Error de usuario: por ejemplo, escribió incorrectamente los parámetros de conexión, como el nombre del servidor en la cadena de conexión.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Pasos para resolver los problemas de conectividad persistentes
1. Configure las [reglas de firewall](sql-database-configure-firewall-settings.md) para permitir la dirección IP del cliente. Con fines de prueba temporales, configure una regla de firewall mediante 0.0.0.0 como intervalo de direcciones IP inicial y 255.255.255.255 como intervalo de dirección IP final. Se abrirá el servidor a todas las direcciones IP. Si se resuelve el problema de conectividad, quite esta regla y cree una regla de firewall para una dirección IP o intervalo de direcciones apropiadamente limitados. 
2. En todos los firewalls entre el cliente e Internet, asegúrese de que el puerto 1433 está abierto para las conexiones salientes. Consulte [Configure the Windows Firewall to Allow SQL Server Access](https://msdn.microsoft.com/library/cc646023.aspx) (Configuración del Firewall de Windows para permitir el acceso de SQL Server) y [Puertos y protocolos requeridos para la identidad híbrida](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) para obtener información sobre punteros relacionados con los puertos adicionales que necesite abrir para la autenticación de Azure Active Directory.
3. Compruebe la cadena de conexión y otras opciones de conexión. Vea la sección Cadena de conexión en el [tema de los problemas de conectividad](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4. Compruebe el estado del servicio en el panel. Si piensa que hay un interrupción regional, vea [Recuperación tras una interrupción](sql-database-disaster-recovery.md) para los pasos para recuperarse para una región nueva.

## <a name="all-topics-for-azure-sql-database-connection-problems"></a>Todos los temas de problemas de conexión de Base de datos SQL de Azure
En la siguiente tabla se enumeran todos los temas de problemas de conexión que se aplican directamente al servicio Base de datos SQL de Azure.

| &nbsp; | Título | Descripción |
| ---:|:--- |:--- |
| 1 |[Solución de problemas de conexión comunes relacionados con la base de datos SQL de Azure](sql-database-troubleshoot-common-connection-issues.md) |Se trata de la página de inicio para solucionar problemas de conectividad de Base de datos SQL de Azure. En ella se describe cómo identificar y resolver errores transitorios y errores persistentes o no transitorios en Base de datos SQL de Azure. |
| 2 |[Acciones para solucionar problemas, diagnosticar y evitar errores de conexión y errores transitorios en Base de datos SQL](sql-database-connectivity-issues.md) |Aprenda a solucionar problemas, diagnosticar y evitar un error de conexión de SQL o un error transitorio en la Base de datos SQL de Azure. |
| 3 |[Orientación general sobre reintentos](../best-practices-retry-general.md) |Se proporcionan instrucciones generales para el tratamiento de los errores transitorios al conectarse a Base de datos SQL de Azure. |
| 4 |[Solucionar problemas de conectividad con la Base de datos SQL de Microsoft Azure](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) |Esta herramienta ayuda a identificar problemas y a resolver problemas de conexión. |
| 5 |[Solucionar el problema del mensaje "Database &lt;x&gt; v &lt;y&gt; is not currently available. Please retry the connection later" (La base de datos &lt;x&gt; del servidor &lt;y&gt; no está disponible. Vuelva a intentar la conexión más tarde)](sql-database-troubleshoot-connection.md) |Se describe cómo identificar y resolver un error 40613: "Database &lt;x&gt; on server &lt;y&gt; is not currently available. Vuelva a intentar la conexión más tarde.) |
| 6 |[Códigos de error para las aplicaciones cliente de la Base de datos SQL: error de conexión de base de datos y otros problemas.](sql-database-develop-error-messages.md) |Proporciona información sobre los códigos de error de SQL de las aplicaciones cliente de Base de datos SQL, como errores de conexión de base de datos más comunes, problemas de copia de la base de datos y errores generales. |
| 7 |[Guía de rendimiento de Base de datos SQL de Azure](sql-database-performance-guidance.md) |Se proporcionan instrucciones para ayudarle a determinar qué nivel de servicio es adecuado para su aplicación. También se incluyen recomendaciones para optimizar la aplicación y así sacar el máximo provecho de su instancia de Base de datos SQL de Azure. |
| 8 |[Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md) |Proporciona vínculos a ejemplos de código para diversas tecnologías que puede usar para conectarse a Base de datos SQL de Azure e interactuar con este servicio. |

## <a name="next-steps"></a>Pasos siguientes
* [Evaluación y mejora del rendimiento de la base de datos con la Base de datos SQL de Azure](sql-database-troubleshoot-performance.md)
* [Buscar en la documentación de Microsoft Azure](http://azure.microsoft.com/search/documentation/)
* [Ver las últimas actualizaciones del servicio Base de datos SQL](http://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Recursos adicionales
* [Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md)
* [Orientación general sobre reintentos](../best-practices-retry-general.md)
* [Bibliotecas de conexiones para Base de datos SQL y SQL Server](sql-database-libraries.md)


