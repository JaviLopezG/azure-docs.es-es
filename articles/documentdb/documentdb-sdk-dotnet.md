---
title: SDK y recursos de .NET para Azure DocumentDB | Microsoft Docs
description: "Obtenga toda la información sobre el SDK y la API para .NET como, por ejemplo, fechas de lanzamiento, fechas de retirada y cambios de una versión a otra del SDK para .NET de DocumentDB."
services: documentdb
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 8e239217-9085-49f5-b0a7-58d6e6b61949
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/22/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: a3aa00bce54ad9d2aa0236cc211affaa537a36e5
ms.lasthandoff: 03/07/2017


---
# <a name="documentdb-net-sdk-download-and-release-notes"></a>SDK de .NET para DocumentDB: descarga y notas de la versión
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [Proveedor de recursos de REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**Descarga del SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>

<tr><td>**Documentación de la API**</td><td>[Documentación de referencia de API de .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>

<tr><td>**Ejemplos**</td><td>[Ejemplos de código de .NET](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**Primeros pasos**</td><td>[Introducción al SDK de .NET de DocumentDB](documentdb-get-started.md)</td></tr>

<tr><td>**Tutorial de la aplicación web**</td><td>[Desarrollo de aplicaciones web con DocumentDB](documentdb-dotnet-application.md)</td></tr>

<tr><td>**Plataforma admitida actualmente**</td><td>[Microsoft .NET 4.5 Framework](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de la versión

### <a name="a-name11201120httpswwwnugetorgpackagesmicrosoftazuredocumentdb1120"></a><a name="1.12.0"/>[1.12.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.12.0)
* Se agregó compatibilidad con consultas de agregación (COUNT, MIN, MAX, SUM y AVG). Consulte [Compatibilidad con agregación](documentdb-sql-query.md#Aggregates).
* Reducción del procesamiento mínimo en las colecciones particionadas de 10 100 RU/s a 2500 RU/s.

### <a name="a-name11141114httpswwwnugetorgpackagesmicrosoftazuredocumentdb1114"></a><a name="1.11.4"/>[1.11.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.4)
* Corrección para un problema por el cual algunas de las consultas entre particiones no funcionaban correctamente en el proceso de host de 32 bits.
* Corrección para un problema por el cual el contenedor de la sesión no se actualizaba con el token para solicitudes erróneas en el modo de puerta de enlace.
* Corrección para un problema por el cual una consulta con llamadas UDF en proyección daba error en algunos casos.
* Revisiones de rendimiento de lado cliente para aumentar el procesamiento de lectura y escritura de las solicitudes.

### <a name="a-name11131113httpswwwnugetorgpackagesmicrosoftazuredocumentdb1113"></a><a name="1.11.3"/>[1.11.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.3)
* Corrección para un problema por el cual el contenedor de la sesión no se actualizaba con el token para solicitudes erróneas.
* Se agregó compatibilidad para que el SDK funcione en un proceso de host de 32 bits. Tenga en cuenta que si utiliza consultas entre particiones, se recomienda el procesamiento de host de 64 bits para obtener un mejor rendimiento.
* Se mejoró el rendimiento en escenarios con consultas que tienen un número elevado de valores de clave de partición en una expresión IN.
* Se rellenaron diversas estadísticas de cuotas de recursos en ResourceResponse para las solicitudes de lectura de recopilación de documentos al establecer la opción de solicitud de PopulateQuotaInfo.

### <a name="a-name11111111httpswwwnugetorgpackagesmicrosoftazuredocumentdb1111"></a><a name="1.11.1"/>[1.11.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.1)
* Corrección menor del rendimiento de la API de CreateDocumentCollectionIfNotExistsAsync incluida en 1.11.0.
* Corrección del rendimiento en el SDK para escenarios que implican un alto grado de solicitudes simultáneas.

### <a name="a-name11101110httpswwwnugetorgpackagesmicrosoftazuredocumentdb1110"></a><a name="1.11.0"/>[1.11.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.0)
* Compatibilidad con nuevas clases y nuevos métodos para procesar la [fuente de cambios](documentdb-change-feed.md) de documentos que forman parte de una colección.
* Compatibilidad con la continuación de consultas entre particiones y algunas mejoras de rendimiento para las consultas entre particiones.
* Adición de métodos CreateDatabaseIfNotExistsAsync y CreateDocumentCollectionIfNotExistsAsync.
* Compatibilidad de LINQ para las funciones del sistema: IsDefined, IsNull e IsPrimitive.
* Corrección para colocación automática de bins de los ensamblados Microsoft.Azure.Documents.ServiceInterop.dll y DocumentDB.Spatial.Sql.dll a la carpeta bin de la aplicación cuando se usa el paquete NuGet con proyectos que tienen herramientas de project.json.
* Compatibilidad con la emisión de los seguimientos de ETW de lado cliente que podrían resultar útiles en escenarios de depuración.

### <a name="a-name11001100httpswwwnugetorgpackagesmicrosoftazuredocumentdb1100"></a><a name="1.10.0"/>[1.10.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.10.0)
* Se agregó compatibilidad de conectividad directa con colecciones con particiones.
* Mejoró el rendimiento para el nivel de coherencia de uso vinculado.
* Se han agregado los tipos de datos Polygon y LineString al especificar la directiva de indización de colecciones para las consultas espaciales de geovallado.
* Se agregó compatibilidad de LINQ con StringEnumConverter, IsoDateTimeConverter y UnixDateTimeConverter, a la vez que se traducen los predicados.
* Se corrigieron varios errores de SDK.

### <a name="a-name195195httpswwwnugetorgpackagesmicrosoftazuredocumentdb195"></a><a name="1.9.5"/>[1.9.5](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.5)
* Se ha corregido un problema que provocaba el siguiente mensaje de error NotFoundException: The read session is not available for the input session token (La sesión de lectura no está disponible para el token de sesión de entrada). Esta excepción se produjo en algunos casos al consultar la región de lectura de una cuenta distribuida geográficamente.
* Se ha expuesto la propiedad ResponseStream en la clase ResourceResponse que permite el acceso directo a la secuencia subyacente de una respuesta.

### <a name="a-name194194httpswwwnugetorgpackagesmicrosoftazuredocumentdb194"></a><a name="1.9.4"/>[1.9.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.4)
* Se han modificado las clases ResourceResponse, FeedResponse, StoredProcedureResponse y MediaResponse a fin de implementar la interfaz pública correspondiente para que se puedan simular con la finalidad de realizar una implementación basada en pruebas (TDD).
* Se ha corregido un problema que generaba un encabezado de clave de partición con formato incorrecto al usar un objeto JsonSerializerSettings personalizado para serializar los datos.

### <a name="a-name193193httpswwwnugetorgpackagesmicrosoftazuredocumentdb193"></a><a name="1.9.3"/>[1.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)
* Se corrige un problema que causaba el error siguiente en las consultas de larga duración: El token de autorización no es válido en este momento.
* Se corrige un problema que quitaba la colección SqlParameterCollection original de las consultas multipartición principal/ordenar por.

### <a name="a-name192192httpswwwnugetorgpackagesmicrosoftazuredocumentdb192"></a><a name="1.9.2"/>[1.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)
* Se ha agregado compatibilidad con las consultas paralelas en las colecciones particionadas.
* Se ha agregado compatibilidad con las consultas ORDER BY y TOP entre particiones en las colecciones particionadas.
* Se han corregido las referencias que faltaban en DocumentDB.Spatial.Sql.dll y Microsoft.Azure.Documents.ServiceInterop.dll, que se necesitan para hacer referencia a un proyecto de DocumentDB con una referencia al paquete NuGet de DocumentDB.
* Se ha corregido la funcionalidad relacionada con el uso de parámetros de diferentes tipos al utilizar las funciones definidas por el usuario de LINQ. 
* Se ha corregido un error en las cuentas replicadas globalmente que provocaba que las llamadas de Upsert se dirigieran a ubicaciones de lectura y no de escritura.
* Se han agregado a la interfaz IDocumentClient los métodos que faltaban: 
  * El método UpsertAttachmentAsync que toma mediaStream y opciones como parámetros
  * El método CreateAttachmentAsync que toma opciones como un parámetro
  * El método CreateOfferQuery que toma querySpec como un parámetro
* Se ha quitado el sello de las clases públicas expuestas en la interfaz IDocumentClient.

### <a name="a-name180180httpswwwnugetorgpackagesmicrosoftazuredocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
* Se ha agregado compatibilidad con cuentas de base de datos de varias regiones.
* Se ha agregado compatibilidad con el reintento en solicitudes limitadas.  El usuario puede personalizar el número de reintentos y el tiempo de espera máximo mediante la configuración de la propiedad ConnectionPolicy.RetryOptions.
* Se ha agregado una nueva interfaz IDocumentClient que define las firmas de todas las propiedades y métodos de DocumenClient.  Como parte de este cambio, también se han cambiado los métodos de extensión que crean IQueryable y IOrderedQueryable por los métodos de la propia clase DocumentClient.
* Se ha agregado la opción de configuración para definir el valor de ServicePoint.ConnectionLimit para un identificador URI de punto de conexión dado de DocumentDB.  Use ConnectionPolicy.MaxConnectionLimit para cambiar el valor predeterminado, que está establecido en 50.
* Se ha dejado de utilizar IPartitionResolver y su implementación.  La compatibilidad con IPartitionResolver está ahora obsoleta. Se recomienda usar colecciones con particiones para conseguir un almacenamiento y un rendimiento más elevados.

### <a name="a-name171171httpswwwnugetorgpackagesmicrosoftazuredocumentdb171"></a><a name="1.7.1"/>[1.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
* Se ha agregado una sobrecarga al identificador URI según el método ExecuteStoredProcedureAsync que toma RequestOptions como un parámetro.

### <a name="a-name170170httpswwwnugetorgpackagesmicrosoftazuredocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
* Se ha agregado compatibilidad con período de vida (TTL) para los documentos.

### <a name="a-name163163httpswwwnugetorgpackagesmicrosoftazuredocumentdb163"></a><a name="1.6.3"/>[1.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
* Se corrige un error en el paquete de Nuget del SDK de .NET para empaquetarlo como parte de una solución de servicio en la nube de Azure.

### <a name="a-name162162httpswwwnugetorgpackagesmicrosoftazuredocumentdb162"></a><a name="1.6.2"/>[1.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
* Se han implementado [colecciones particionadas](documentdb-partition-data.md) y [niveles de rendimiento definidos por el usuario](documentdb-performance-levels.md). 

### <a name="a-name153153httpswwwnugetorgpackagesmicrosoftazuredocumentdb153"></a><a name="1.5.3"/>[1.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
* **[Corregido]** La consulta del punto de conexión de DocumentDB genera: "System.Net.Http.HttpRequestException: Error al copiar contenido a una secuencia".

### <a name="a-name152152httpswwwnugetorgpackagesmicrosoftazuredocumentdb152"></a><a name="1.5.2"/>[1.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
* Compatibilidad de LINQ ampliada, incluidos nuevos operadores de paginación, expresiones condicionales y comparación de intervalos.
  * Operador Take para habilitar el comportamiento de SELECT TOP en LINQ
  * Operador CompareTo para habilitar las comparaciones de intervalos de cadenas
  * Operadores conditional (?) y coalesce (??)
* **[Corregido]** ArgumentOutOfRangeException al combinar la proyección Model en la consulta Where-In de LINQ.  [81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151httpswwwnugetorgpackagesmicrosoftazuredocumentdb151"></a><a name="1.5.1"/>[1.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
* **[Corregido]** Si Select no es la última expresión, el proveedor LINQ suponía que no había ninguna proyección y generaba SELECT * incorrectamente.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150httpswwwnugetorgpackagesmicrosoftazuredocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
* Upsert implementada, métodos de UpsertXXXAsync agregados
* Mejoras de rendimiento para todas las solicitudes
* Compatibilidad del proveedor de LINQ con los métodos condicionales, de fusión y CompareTo para cadenas
* **[Corregido]** proveedor LINQ--> la implementación del método Contains en List para generar el mismo SQL que en IEnumerable y en una matriz
* **[Corregido]** BackoffRetryUtility usa el mismo HttpRequestMessage de nuevo en lugar de crear uno nuevo al reintentar
* **[Obsoleto]** UriFactory.CreateCollection--> debería usar ahora UriFactory.CreateDocumentCollection

### <a name="a-name141141httpswwwnugetorgpackagesmicrosoftazuredocumentdb141"></a><a name="1.4.1"/>[1.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
* **[Corregido]** problemas de localización al usar la información de referencia cultural diferente de EN como nl-NL etc. 

### <a name="a-name140140httpswwwnugetorgpackagesmicrosoftazuredocumentdb140"></a><a name="1.4.0"/>[1.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
* Enrutamiento por identificador.
  * Nueva aplicación auxiliar UriFactory para ayudar a construir vínculos de recursos basados en identificador
  * Nuevas sobrecargas en DocumentClient para tener en cuenta URI
* Se agregaron IsValid() y IsValidDetailed() en LINQ para geoespaciales
* Compatibilidad mejorada con el proveedor de LINQ
  * **Math** : Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate
  * **String** : Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper
  * **Array** : Concat, Contains, Count
  * **IN** 

### <a name="a-name130130httpswwwnugetorgpackagesmicrosoftazuredocumentdb130"></a><a name="1.3.0"/>[1.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
* Se agregó compatibilidad para la modificación de las directivas de indexación
  * Nuevo método de ReplaceDocumentCollectionAsync en DocumentClient
  * Nueva propiedad IndexTransformationProgress en ResourceResponse<T> para el seguimiento del porcentaje de progreso de cambios de la directiva de índice
  * DocumentCollection.IndexingPolicy ahora es mutable
* Se agregó compatibilidad para consulta e indexación espacial
  * Nuevo espacio de nombres Microsoft.Azure.Documents.Spatial para serializar y deserializar tipos espaciales como punto y polígono
  * Nueva clase de SpatialIndex para la indexación de datos GeoJSON almacenados en DocumentDB
* **[Corregido]** : consulta SQL incorrecta generada a partir de la expresión linq [n.º 38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="a-name120120httpswwwnugetorgpackagesmicrosoftazuredocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
* Dependencia de Newtonsoft.Json v5.0.7 
* Cambios para admitir Order By
  
  * Compatibilidad del proveedor LINQ para OrderBy() u OrderByDescending()
  * IndexingPolicy para admitir Order By 
    
    **NB: Posible cambio brusco** 
    
    Si tiene un código existente que aprovisiona colecciones con una directiva de indexación personalizada, habrá que actualizar el código existente para admitir la nueva clase IndexingPolicy. Si no tiene ninguna directiva de indexación personalizada, este cambio no le afectará.

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
* Compatibilidad para las particiones de datos mediante las nuevas clases HashPartitionResolver y RangePartitionResolver y el IPartitionResolver
* Serialización de DataContract
* Compatibilidad con el GUID de proveedor de LINQ
* Compatibilidad con UDF en LINQ

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
* SDK de GA

## <a name="release--retirement-dates"></a>Fechas de lanzamiento y de retirada
Microsoft notificará la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

Solo se agregan nuevas características, funcionalidad y optimizaciones al SDK actual, por lo que se recomienda actualizar siempre a la última versión del SDK tan pronto como sea posible. 

El servicio rechazará cualquier solicitud realizada en DocumentDB mediante un SDK retirado.

<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada |
| --- | --- | --- |
| [1.12.0](#1.12.0) |15 de febrero de 2017 |--- |
| [1.11.4](#1.11.4) |06 de febrero de 2017 |--- |
| [1.11.3](#1.11.3) |26 de enero de 2017 |--- |
| [1.11.1](#1.11.1) |21 de diciembre de 2016 |--- |
| [1.11.0](#1.11.0) |08 de diciembre de 2016 |--- |
| [1.10.0](#1.10.0) |27 de septiembre de 2016 |--- |
| [1.9.5](#1.9.5) |01 de septiembre de 2016 |--- |
| [1.9.4](#1.9.4) |24 de agosto de 2016 |--- |
| [1.9.3](#1.9.3) |15 de agosto de 2016 |--- |
| [1.9.2](#1.9.2) |23 de julio de 2016 |--- |
| [1.8.0](#1.8.0) |14 de junio de 2016 |--- |
| [1.7.1](#1.7.1) |06 de mayo de 2016 |--- |
| [1.7.0](#1.7.0) |26 de abril de 2016 |--- |
| [1.6.3](#1.6.3) |08 de abril de 2016 |--- |
| [1.6.2](#1.6.2) |29 de marzo de 2016 |--- |
| [1.5.3](#1.5.3) |19 de febrero de 2016 |--- |
| [1.5.2](#1.5.2) |14 de diciembre de 2015 |--- |
| [1.5.1](#1.5.1) |23 de noviembre de 2015 |--- |
| [1.5.0](#1.5.0) |05 de octubre de 2015 |--- |
| [1.4.1](#1.4.1) |25 de agosto de 2015 |--- |
| [1.4.0](#1.4.0) |13 de agosto de 2015 |--- |
| [1.3.0](#1.3.0) |05 de agosto de 2015 |--- |
| [1.2.0](#1.2.0) |06 de julio de 2015 |--- |
| [1.1.0](#1.1.0) |30 de abril de 2015 |--- |
| [1.0.0](#1.0.0) |08 de abril de 2015 |--- |


## <a name="faq"></a>Preguntas más frecuentes
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Otras referencias
Para más información sobre DocumentDB, consulte la página del servicio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) . 


