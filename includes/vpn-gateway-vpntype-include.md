* **PolicyBased:** las VPN basadas en directivas se denominaban anteriormente puertas de enlace de enrutamiento estático en el modelo de implementación clásica. Las VPN basadas en directivas cifran y dirigen los paquetes a través de túneles de IPsec basados en las directivas de IPsec configuradas con las combinaciones de prefijos de dirección entre su red local y la red virtual de Azure. La directiva (o selector de tráfico) se define normalmente como una lista de acceso en la configuración del dispositivo VPN. El valor de un tipo de VPN basada en directivas es *PolicyBased*. Cuando se utiliza una VPN PolicyBased, tenga en cuenta las siguientes limitaciones:
  
  * Las VPN PolicyBased **solo** se utilizan en la SKU de puerta de enlace Basic. Este tipo VPN no es compatible con otras SKU de puerta de enlace.
  * Solo puede tener 1 túnel al usar una VPN PolicyBased.
  * Solo puede utilizar las VPN de PolicyBase para las conexiones S2S (solo para determinadas configuraciones). La mayoría de las configuraciones de VPN Gateway requieren una VPN basada en enrutamientos.
* **RouteBased:**las VPN basadas en enrutamiento se denominaban anteriormente puertas de enlace de enrutamiento dinámico en el modelo de implementación clásica. Las VPN basadas en enrutamiento utilizan "rutas" en la dirección IP de reenvío o en la tabla de enrutamiento para dirigir los paquetes a sus correspondientes interfaces de túnel. A continuación, las interfaces de túnel cifran o descifran los paquetes dentro y fuera de los túneles. La directiva o selector de tráfico para las VPN basadas en enrutamiento se configura como conectividad de tipo any-to-any (o caracteres comodín). El valor de un tipo de VPN basado en enrutamiento es *RouteBased*.



<!--HONumber=Nov16_HO3-->


