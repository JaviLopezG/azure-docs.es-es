---
title: "Integración del SDK de iOS de Azure Mobile Engagement | Microsoft Docs"
description: "Actualizaciones y procedimientos más recientes para el SDK de iOS para Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 947ea44b-00c1-450f-9a3b-74437954dc56
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 09/14/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7c4e962527d0b4cd66f6ca4974851c5e5d523de7


---
# <a name="how-to-integrate-engagement-on-ios"></a>Integración de Engagement en iOS
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Este procedimiento describe la manera más sencilla de activar las funciones de supervisión y análisis de Engagement en su aplicación iOS.

El SDK de Engagement requiere iOS6+ y Xcode 8: el destino de implementación de la aplicación debe ser como mínimo iOS 6.

> [!NOTE]
> Si realmente depende de XCode 7, puede usar el [SDK v3.2.4 de Engagement para iOS](https://aka.ms/r6oouh). Existe un problema conocido en el módulo de cobertura de esta versión anterior cuando se ejecuta en dispositivos iOS 10. Consulte la [integración del módulo de cobertura](mobile-engagement-ios-integrate-engagement-reach.md) para más información. Si decide usar el SDK v3.2.4, simplemente omita la importación de `UserNotifications.framework` en el siguiente paso.
> 
> 

Los siguientes pasos son suficientes para activar el informe de los registros necesarios para calcular todas las estadísticas en relación con los usuarios, las sesiones, las actividades, los bloqueos y los aspectos técnicos. El informe de los registros necesarios para calcular otras estadísticas, como eventos, errores y trabajos debe realizarse manualmente mediante la API de Engagement (consulte [Cómo usar la API de etiquetado avanzado de Mobile Engagement en su aplicación iOS](mobile-engagement-ios-use-engagement-api.md)) debido a que estas estadísticas dependen de la aplicación.

## <a name="embed-the-engagement-sdk-into-your-ios-project"></a>Incrustación del SDK de Engagement en su proyecto de iOS
* Descargue el SDK de iOS [aquí](http://aka.ms/qk2rnj).
* Agregue el SDK de Engagement al proyecto de iOS: en Xcode, haga clic con el botón derecho en el proyecto, elija **"Agregar archivos a..."** y elija la carpeta `EngagementSDK`.
* Engagement requiere la contratación de marcos adicionales para trabajar: en el Explorador de proyectos, abra el panel de proyectos y elija el destino correcto. A continuación, abra la pestaña **"Generar fases"** en el menú **"Vincular binario con bibliotecas"** y agregue estos marcos:
  
  * `UserNotifications.framework`- configure el vínculo como `Optional`
  * `AdSupport.framework`- configure el vínculo como `Optional`
  * `SystemConfiguration.framework`
  * `CoreTelephony.framework`
  * `CFNetwork.framework`
  * `CoreLocation.framework`
  * `libxml2.dylib`

> [!NOTE]
> El marco de trabajo AdSupport se puede quitar. Engagement necesita este marco para recopilar el IDFA. No obstante, la recopilación de IDFA se puede deshabilitar \<ios-sdk-engagement-idfa\> para cumplir con la nueva directiva de Apple con respecto a este identificador.
> 
> 

## <a name="initialize-the-engagement-sdk"></a>Inicializar el SDK de Engagement
Debe modificar el delegado de la aplicación:

* En la parte superior del archivo de implementación, importe el agente de Engagement.
  
      [...]
      #import "EngagementAgent.h"
* Inicialice Engagement dentro del método '**applicationDidFinishLaunching:**' o '**application:didFinishLaunchingWithOptions:**':
  
      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
      {
        [...]
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
        [...]
      }

## <a name="basic-reporting"></a>Informes básicos
### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>Método recomendado: sobrecargar las clases `UIViewController`
Si desea que se generen todos los registros que requiere Engagement para calcular los usuarios, las sesiones, las actividades, los bloqueos y las estadísticas técnicas, simplemente puede hacer que todas las subclases `UIViewController` se hereden de las clases `EngagementViewController` (misma regla para `UITableViewController` -\> `EngagementTableViewController`).

**Sin Engagement:**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Con Engagement:**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>Método alternativo: llamar a `startActivity()` manualmente
Si no puede o no desea sobrecargar las clases `UIViewController`, en su lugar, puede iniciar sus actividades mediante una llamada directa a los métodos de `EngagementAgent`.

> [!IMPORTANT]
> El SDK de iOS llama automáticamente al método `endActivity()` cuando se cierra la aplicación. Por lo tanto, es *MUY* recomendable llamar al método `startActivity` cada vez que cambie la actividad del usuario y *NUNCA* llamar al método `endActivity`, ya que fuerza la finalización de la sesión actual.
> 
> 

## <a name="location-reporting"></a>Informes de ubicación
Las condiciones de servicio de Apple no permiten a las aplicaciones utilizar el seguimiento de ubicación con fines de estadísticas únicamente. Por lo tanto, se recomienda habilitar los informes de ubicación solo si la aplicación también utiliza el seguimiento de ubicación por otro motivo.

A partir de iOS 8, debe proporcionar una descripción del modo en que su aplicación utiliza los servicios de ubicación estableciendo una cadena para la clave [NSLocationWhenInUseUsageDescription] o [NSLocationAlwaysUsageDescription] en el archivo Info.plist de la aplicación. Si desea registrar la ubicación en segundo plano con Engagement, agregue la clave NSLocationAlwaysUsageDescription. En los demás casos, agregue la clave NSLocationWhenInUseUsageDescription.

### <a name="lazy-area-location-reporting"></a>Informes de ubicaciones de áreas diferidas
Los informes de ubicación de área diferida permiten notificar el país, la región y la localidad asociados con los dispositivos. Este tipo de informe de ubicación sólo emplea ubicaciones de red (basadas en el identificador del teléfono móvil o en WIFI). El área del dispositivo se notifica como máximo una vez por sesión. El GPS no se utiliza nunca y, por tanto, este tipo de informe de ubicación tiene muy poco impacto (por no decir ninguno) en la batería.

Las áreas notificadas se utilizan para elaborar estadísticas geográficas acerca de los usuarios, las sesiones, los eventos y los errores. También se pueden usar como criterios en campañas de cobertura. La última área conocida registrada para un dispositivo se puede recuperar gracias a la [API del dispositivo].

Para habilitar los informes de ubicación de área diferida, agregue la siguiente línea después de inicializar el agente de Engagement:

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>Informes de ubicación en tiempo real
Los informes de ubicación en tiempo real permiten notificar la latitud y la longitud asociadas con los dispositivos. De forma predeterminada, este tipo de informes de ubicación solo emplea ubicaciones de red (basadas en el identificador del teléfono móvil o en WIFI), y los informes solo están activos cuando la aplicación se ejecuta en primer plano (por ejemplo, durante una sesión).

Las ubicaciones en tiempo real *NO* se usan para calcular las estadísticas. Su único objetivo es permitir el uso de criterios de geovallas en tiempo real \<Reach-Audience-geofencing\> en las campañas de cobertura.

Para habilitar los informes de ubicación en tiempo real, agregue la siguiente línea después de inicializar el agente de Engagement:

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>Informes basados en GPS
De forma predeterminada, los informes de ubicación en tiempo real solo emplean ubicaciones de red. Para habilitar el uso de ubicaciones basadas en GPS (que son mucho más precisas), agregue:

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>Informes en segundo plano
De forma predeterminada, los informes de ubicación en tiempo real solo están activos cuando la aplicación se ejecuta en primer plano (es decir, durante una sesión). Para habilitar los informes también en segundo plano, agregue:

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [!NOTE]
> Cuando la aplicación se ejecuta en segundo plano, solo se notifican las ubicaciones de red, incluso si ha habilitado el GPS.
> 
> 

La implementación de esta función efectuará una llamada a [startMonitoringSignificantLocationChanges] cuando la aplicación pase a un segundo plano. Tenga en cuenta que la aplicación se reiniciará automáticamente en segundo plano si se produce un nuevo evento de ubicación.

## <a name="advanced-reporting"></a>Informes avanzados
De manera opcional, si desea notificar eventos, errores y trabajos específicos de la aplicación, deberá usar la API de Engagement a través de los métodos de la clase `EngagementAgent` . Un objeto de esta clase se puede recuperar mediante una llamada al método estático `[EngagementAgent shared]` .

La API de Engagement permite usar todas las capacidades avanzadas de Engagement, que se detallan en Cómo usar la API de Engagement en iOS (así como en la documentación técnica de la clase `EngagementAgent` ).

## <a name="disable-idfa-collection"></a>Deshabilitación de la recopilación de IDFA
De forma predeterminada, Engagement utilizará [IDFA] para identificar a un usuario de forma exclusiva. Pero si no está utilizando publicidad en otra parte de la aplicación, es posible que el proceso de revisión de la tienda de aplicaciones le rechace. La colección de IDFA se puede deshabilitar mediante la adición de la macro de preprocesador `ENGAGEMENT_DISABLE_IDFA` en el archivo pch (o en la `Build Settings` de la aplicación). Así se asegurará de que no haya ninguna referencia a `ASIdentifierManager`, `advertisingIdentifier` ni a `isAdvertisingTrackingEnabled` en la compilación de la aplicación.

Integración en el archivo **prefix.pch** :

    #define ENGAGEMENT_DISABLE_IDFA
    ...

Puede verificar que la colección IDFA está correctamente deshabilitada en la aplicación comprobando los registros de prueba de Engagement. Consulte la documentación de las pruebas de integración \<ios-sdk-engagement-test-idfa\> para más información.

## <a name="disable-log-reporting"></a>Deshabilitación de los informes de registro
### <a name="using-a-method-call"></a>Mediante una llamada al método
Si desea que Engagement deje de enviar registros, puede efectuar una llamada:

    [[EngagementAgent shared] setEnabled:NO];

Esta llamada es persistente: usa `NSUserDefaults` para almacenar la información.

Puede habilitar de nuevo los informes de registro mediante la llamada a la misma función con `YES`.

### <a name="integration-in-your-settings-bundle"></a>Integración en la agrupación de configuraciones
En lugar de llamar a esta función, también puede integrar esta configuración directamente en el archivo `Settings.bundle` existente. La cadena `engagement_agent_enabled` debe utilizarse como un identificador de preferencia y debe estar asociada a un modificador para alternar (`PSToggleSwitchSpecifier`).

El siguiente ejemplo de `Settings.bundle` muestra el proceso de implementación:

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[API del dispositivo]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier



<!--HONumber=Nov16_HO3-->


