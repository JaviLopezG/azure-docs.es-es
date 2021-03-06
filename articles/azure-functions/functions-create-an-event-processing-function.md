---
title: "Creación de una función de procesamiento de eventos | Microsoft Docs"
description: "Utilice Funciones de Azure para crear una función de C# que se ejecuta según un temporizador de eventos."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 84bd0373-65e2-4022-bcca-2b9cd9e696f5
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/25/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 44e397c7521ba8f0ba11893c364f51177561bee4
ms.openlocfilehash: df3d303ee10fcc982552ea9756eb59198c87b650


---
# <a name="create-an-event-processing-azure-function"></a>Creación de una función de Azure de procesamiento de eventos
Funciones de Azure es una experiencia de procesos a petición orientada a eventos que permite crear unidades de código programadas o desencadenadas que se implementan en diversos lenguajes de programación. Para más información sobre Funciones de Azure, consulte [Información general sobre Funciones de Azure](functions-overview.md).

En este tema se muestra cómo crear una nueva función en C# que se ejecuta según un temporizador de eventos para agregar mensajes a una cola de almacenamiento. 

## <a name="prerequisites"></a>Requisitos previos
Una aplicación de función hospeda la ejecución de sus funciones en Azure. Si ya no tiene una cuenta de Azure, consulte la experiencia [Probar Functions](https://functions.azure.com/try) o [cree una cuenta gratis de Azure](https://azure.microsoft.com/free/). 

## <a name="create-a-timer-triggered-function-from-the-template"></a>Creación de una función desencadenada por un temporizador a partir de la plantilla
Una aplicación de función hospeda la ejecución de sus funciones en Azure. Para poder crear una función, tiene que tener una cuenta de Azure activa. Si aún no tiene una cuenta de Azure, [puede crear una gratuita](https://azure.microsoft.com/free/). 

1. Vaya al [portal de Funciones de Azure](https://functions.azure.com/signin) e inicie sesión con su cuenta de Azure.
2. Si tiene una aplicación de función existente, selecciónela en **Your function apps** (Sus aplicaciones de función) y haga clic en **Abrir**. Para crear una aplicación de función, escriba un **nombre** único para ella o acepte el que se genera, seleccione su **región** preferida y haga clic en **Create + get started** (Crear y comenzar). 
3. En la aplicación de función, haga clic en **+ New Function (+ Nueva función)** > **TimerTrigger - C#** > **Create (Crear)**. Esto crea una función con un nombre predeterminado que se ejecuta en la programación predeterminada de una vez cada minuto. 
   
    ![Creación de una nueva función desencadenada por un temporizador](./media/functions-create-an-event-processing-function/functions-create-new-timer-trigger.png)
4. En la nueva función, haga clic en la pestaña **Integrate** (Integrar) > **New Output (Nueva salida)** > **Azure Storage Queue** > **Select (Seleccionar)**.
   
    ![Creación de una nueva función desencadenada por un temporizador](./media/functions-create-an-event-processing-function/functions-create-storage-queue-output-binding.png)
5. En **Azure Storage Queue output** (Salida de cola de Azure Storage), en **Storage account connection** (Conexión de cuenta de almacenamiento), seleccione una conexión existente o cree una nueva y haga clic en **Save** (Guardar). 
   
    ![Creación de una nueva función desencadenada por un temporizador](./media/functions-create-an-event-processing-function/functions-create-storage-queue-output-binding-2.png)
6. Vuelva a la pestaña **Develop** (Desarrollar) y reemplace el script de C# existente en la ventana **Code** (Código) por el código siguiente:
    ```cs   
    using System;

    public static void Run(TimerInfo myTimer, out string outputQueueItem, TraceWriter log)
    {
        // Add a new scheduled message to the queue.
        outputQueueItem = $"Ping message added to the queue at: {DateTime.Now}.";

        // Also write the message to the logs.
        log.Info(outputQueueItem);
    }
    ```
   
    Este código agrega un nuevo mensaje a la cola con la fecha y hora actuales cuando se ejecuta la función.
7. Haga clic en **Save** (Guardar) y fíjese si en la ventana **Logs** (Registros) se ejecuta la siguiente función.
8. (Opcional) Vaya a la cuenta de almacenamiento y compruebe que los mensajes se agregan a la cola.
9. Vuelva a la pestaña **Integrate** (Integrar) y cambie el campo de programación a `0 0 * * * *`. La función ahora se ejecuta una vez cada hora. 

Esto es un ejemplo muy simplificado de un desencadenador de temporizador y de un enlace de salida de cola de almacenamiento. Para más información, consulte los temas [Desencadenador de temporizador de Azure Functions](functions-bindings-timer.md) y [Enlaces y desencadenadores de Azure Functions para Azure Storage](functions-bindings-storage.md).

## <a name="next-steps"></a>Pasos siguientes
Consulte estos temas para más información sobre Azure Functions.

* [Referencia para desarrolladores de Funciones de Azure](functions-reference.md)  
   contiene las referencias del programador para codificar funciones y definir desencadenadores y enlaces.
* [Prueba de Azure Functions](functions-test-a-function.md)  
   describe las diversas herramientas y técnicas para probar sus funciones.
* [How to scale Azure Functions](functions-scale.md)  
  Trata los planes de servicio disponibles con Azure Functions, incluido el plan de hospedaje de Consumo, y cómo elegir el plan adecuado.  

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO3-->


