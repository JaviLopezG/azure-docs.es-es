---
title: "Ejemplo de script de la CLI de Azure: escalado manual de una aplicación web mediante la CLI de Azure 2.0 | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: escalado manual de una aplicación web mediante la CLI de Azure 2.0"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 251d9074-8fff-4121-ad16-9eca9556ac96
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 16e10ce51758f8d0d62457cac311b0449721ec34
ms.lasthandoff: 03/11/2017

---

# <a name="scale-a-web-app-manually"></a>Escalado manual de una aplicación web

En este escenario, aprenderá a crear un grupo de recursos, un plan de App Service y una aplicación web. A continuación, escalará el plan de App Service desde una sola instancia a varias instancias.

Antes de ejecutar este script, asegúrese de que se haya creado una conexión con Azure mediante el comando `az login`.

Este ejemplo funciona en un shell de Bash. Para ver las opciones de ejecución de scripts de la CLI de Azure en un cliente Windows, consulte [Using the Azure CLI on Windows](../../virtual-machines/virtual-machines-windows-cli-options.md) (Uso de la CLI de Azure en Windows).

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[principal](../../../cli_scripts/app-service/scale-manual/scale-manual.sh "Escalado manual")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una aplicación web y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crea un plan de App Service, que es como una granja de servidores para una aplicación web de Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Crea una aplicación web de Azure en el plan de App Service. |
| [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update) | Actualiza las propiedades del plan de App Service. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de App Service en la [documentación de Azure App Service](../app-service-cli-samples.md).
