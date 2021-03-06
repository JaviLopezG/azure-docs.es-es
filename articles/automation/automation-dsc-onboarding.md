---
title: "Incorporación de máquinas para administrarlas con DSC de Azure Automation | Microsoft Docs"
description: "Cómo configurar las máquinas para administrarlas con DSC de Automatización de Azure"
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
ms.assetid: da13e1f5-2a1c-443b-8e3b-9f0d6f9e4810
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 12/13/2016
ms.author: eslesar
translationtype: Human Translation
ms.sourcegitcommit: e2257730f0c62dbc0313ce7953fc5f953dae8ac3
ms.openlocfilehash: f81536322ad1bb16e4af326e0b053da47690619c
ms.lasthandoff: 02/15/2017


---
# <a name="onboarding-machines-for-management-by-azure-automation-dsc"></a>Incorporación de máquinas para administrarlas con DSC de Automatización de Azure

## <a name="why-manage-machines-with-azure-automation-dsc"></a>¿Por qué administrar máquinas con DSC de Automatización de Azure?

Al igual que [Configuración de estado deseado de PowerShell](https://technet.microsoft.com/library/dn249912.aspx), Configuración de estado deseado de Automatización de Azure es un servicio de administración de configuración, sencillo pero eficaz, para los nodos de DSC (máquinas físicas y virtuales) en cualquier centro de datos local o en la nube. Permite la escalabilidad en miles de máquinas de forma rápida y sencilla desde una ubicación central y segura. Puede incorporar fácilmente máquinas, asignarles configuraciones declarativas y ver informes que muestran el cumplimiento por parte de cada máquina del estado deseado especificado. El nivel de administración de DSC de Automatización de Azure es a DSC lo que el nivel de administración de Automatización de Azure es al scripting de PowerShell. En otras palabras, Automatización de Azure le ayuda a administrar las configuraciones de DSC, de la misma manera que lo hace con los scripts de PowerShell. Para más información acerca de las ventajas de usar DSC de Automatización de Azure, consulte [Información general de DSC de Automatización de Azure](automation-dsc-overview.md).

DSC de Automatización de Azure puede usarse para administrar diversas máquinas:

* Máquinas virtuales de Azure (clásico)
* Máquinas virtuales de Azure
* Máquinas virtuales de Amazon Web Services (AWS)
* Máquinas físicas y virtuales con Windows locales o en una nube que no sea Azure/AWS
* Máquinas físicas y virtuales con Linux locales, en Azure o en una nube que no sea Azure

Además, si no está preparado para administrar la configuración de máquina desde la nube, DSC de Automatización de Azure también puede utilizarse como punto de conexión meramente informativo. Esto le permite establecer (insertar) la configuración deseada a través del DSC local y ver numerosos detalles de informe sobre el cumplimiento del nodo con el estado deseado en Automatización de Azure.

En las secciones siguientes, se describe cómo incorporar cada tipo de máquina a DSC de Automatización de Azure.

## <a name="azure-virtual-machines-classic"></a>Azure Virtual Machines (clásico)

Con DSC de Automatización de Azure, puede incorporar fácilmente máquinas virtuales de Azure (clásico) para la administración de configuraciones mediante el Portal de Azure o PowerShell. Internamente y sin que un administrador tenga que acceder de forma remota a la máquina virtual, la extensión Configuración de estado deseado de la máquina virtual de Azure registra la máquina virtual con DSC de Automatización de Azure. Puesto que la extensión Configuración de estado deseado de la máquina virtual de Azure se ejecuta de forma asincrónica, se proporcionan los pasos para realizar un seguimiento de su progreso o solucionar problemas en la sección [**Solución de problemas de incorporación de máquinas virtuales de Azure**](#troubleshooting-azure-virtual-machine-onboarding) .

### <a name="azure-portal"></a>Azure Portal

En [Azure Portal](http://portal.azure.com/) , haga clic en **Examinar** -> **Virtual Machines (clásico)**. Seleccione la máquina virtual de Windows que desea incorporar. En la hoja del panel de la máquina virtual, haga clic en **Toda la configuración** -> **Extensiones** -> **Agregar** -> **DSC de Azure Automation** -> **Crear**. Escriba los [valores del Administrador de configuración local de DSC de PowerShell](https://msdn.microsoft.com/powershell/dsc/metaconfig4) necesarios para su caso de uso, la clave de registro de su cuenta de Automation y la dirección URL de registro, así como, opcionalmente, una configuración de nodo para asignarla a la máquina virtual.

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

Para buscar la dirección URL de registro y la clave de la cuenta de Automatización a la que incorporar la máquina, consulte la sección [**Registro seguro**](#secure-registration) .

### <a name="powershell"></a>PowerShell

```powershell
# log in to both Azure Service Management and Azure Resource Manager
Add-AzureAccount
Add-AzureRmAccount

# fill in correct values for your VM/Automation account here
$VMName = ""
$ServiceName = ""
$AutomationAccountName = ""
$AutomationAccountResourceGroup = ""

# fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
$NodeConfigName = ""

# get Azure Automation DSC registration info
$Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
$RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

# use the DSC extension to onboard the VM for management with Azure Automation DSC
$VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

$PublicConfiguration = ConvertTo-Json -Depth 8 @{
    SasToken = ""
    ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
    ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

# update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
# See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
    Properties = @{
    RegistrationKey = @{
        UserName = 'notused'
        Password = 'PrivateSettingsRef:RegistrationKey'
    }
    RegistrationUrl = $RegistrationInfo.Endpoint
    NodeConfigurationName = $NodeConfigName
    ConfigurationMode = "ApplyAndMonitor"
    ConfigurationModeFrequencyMins = 15
    RefreshFrequencyMins = 30
    RebootNodeIfNeeded = $False
    ActionAfterReboot = "ContinueConfiguration"
    AllowModuleOverwrite = $False
    }
}

$PrivateConfiguration = ConvertTo-Json -Depth 8 @{
    Items = @{
        RegistrationKey = $RegistrationInfo.PrimaryKey
    }
}

$VM = Set-AzureVMExtension `
    -VM $vm `
    -Publisher Microsoft.Powershell `
    -ExtensionName DSC `
    -Version 2.19 `
    -PublicConfiguration $PublicConfiguration `
    -PrivateConfiguration $PrivateConfiguration `
    -ForceUpdate

$VM | Update-AzureVM
```

## <a name="azure-virtual-machines"></a>Máquinas virtuales de Azure

Con DSC de Automatización de Azure, puede incorporar fácilmente máquinas virtuales de Azure para la administración de configuraciones, mediante el Portal de Azure, las plantillas del Administrador de recursos de Azure o PowerShell. Internamente y sin que un administrador tenga que acceder de forma remota a la máquina virtual, la extensión Configuración de estado deseado de la máquina virtual de Azure registra la máquina virtual con DSC de Automatización de Azure. Puesto que la extensión Configuración de estado deseado de la máquina virtual de Azure se ejecuta de forma asincrónica, se proporcionan los pasos para realizar un seguimiento de su progreso o solucionar problemas en la sección [**Solución de problemas de incorporación de máquinas virtuales de Azure**](#troubleshooting-azure-virtual-machine-onboarding) .

### <a name="azure-portal"></a>Portal de Azure

En el [Portal de Azure](https://portal.azure.com/), vaya a la cuenta de Automatización de Azure en la que quiere incorporar máquinas virtuales. En el panel de la cuenta de Automation, haga clic en **Nodos de DSC** -> **Agregar máquina virtual de Azure**.

En **Seleccionar máquinas virtuales que incorporar**, seleccione una o más máquinas virtuales de Azure para incorporarlas.

![](./media/automation-dsc-onboarding/DSC_Onboarding_2.png)

En **Configurar datos de registro**, escriba los [valores del Administrador de configuración local de DSC de PowerShell](https://msdn.microsoft.com/powershell/dsc/metaconfig4) necesarios para su caso de uso, así como, opcionalmente, una configuración de nodo para asignarla a la máquina virtual.

![](./media/automation-dsc-onboarding/DSC_Onboarding_3.png)

### <a name="azure-resource-manager-templates"></a>Plantillas del Administrador de recursos de Azure

Se pueden implementar máquinas virtuales de Azure e incorporarlas a DSC de Automatización de Azure mediante las plantillas del Administrador de recursos de Azure. Consulte la página sobre cómo [configurar una máquina virtual mediante la extensión DSC y DSC de Automatización de Azure](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) para ver una plantilla de ejemplo que incorpora una máquina virtual existente a DSC de Automatización de Azure. Para buscar la clave de registro y la dirección URL de registro tomadas como entrada en esta plantilla, consulte la sección [**Registro seguro**](#secure-registration) .

### <a name="powershell"></a>PowerShell

El cmdlet [Register-AzureRmAutomationDscNode](https://msdn.microsoft.com/library/mt603833.aspx) sirve para incorporar máquinas virtuales en el Portal de Azure por medio de PowerShell.

## <a name="amazon-web-services-aws-virtual-machines"></a>Máquinas virtuales de Amazon Web Services (AWS)

Puede incorporar fácilmente máquinas virtuales de Amazon Web Services para administración de la configuración mediante el Kit de herramientas de DSC AWS de DSC de Automatización de Azure. Puede obtener más información acerca de estas herramientas [aquí](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Máquinas físicas y virtuales con Windows locales o en una nube que no sea Azure/AWS

Las máquinas con Windows locales y las ubicadas en nubes que no sean de Azure (por ejemplo, Amazon Web Services) también se pueden incorporar a DSC de Automatización de Azure, siempre y cuando tengan acceso saliente a Internet, en unos pocos pasos sencillos:

1. Asegúrese de que la versión más reciente de [WMF 5](http://aka.ms/wmf5latest) esté instalada en las máquinas que desee incorporar a DSC de Automatización de Azure.
2. Siga las indicaciones de la sección [**Generación de metaconfiguraciones de DSC**](#generating-dsc-metaconfigurations) que aparece a continuación para generar una carpeta que contenga las metaconfiguraciones de DSC necesarias.
3. Aplique de forma remota la metaconfiguración de DSC de PowerShell a las máquinas que quiere incorporar. **La máquina desde la que se ejecuta este comando debe tener instalada la versión más reciente de [WMF 5](http://aka.ms/wmf5latest)**:

    ```powershell
    Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
    ```

4. Si no puede aplicar las metaconfiguraciones de DSC de PowerShell de forma remota, copie la carpeta de metaconfiguraciones del paso 2 en cada máquina que vaya a incorporar. Después, llame a **Set-DscLocalConfigurationManager** localmente en cada máquina que vaya a incorporar.
5. Mediante el Portal de Azure o los cmdlets, compruebe que las máquinas que vaya a incorporar aparezcan ahora como nodos de DSC registrados en su cuenta de Automatización de Azure.

## <a name="physicalvirtual-linux-machines-on-premises-in-azure-or-in-a-cloud-other-than-azure"></a>Máquinas físicas y virtuales con Linux locales, en Azure o en una nube que no sea Azure

Las máquinas de Linux locales, de Azure y ubicadas en nubes que no sean de Azure también se pueden incorporar a DSC de Automatización de Azure, siempre y cuando tengan acceso saliente a Internet, en unos pocos pasos sencillos:

1. Asegúrese de que la versión más reciente del [agente de Linux de DSC](http://www.microsoft.com/download/details.aspx?id=49150) esté instalada en las máquinas que desee incorporar a DSC de Automatización de Azure.
2. Si los [valores predeterminados del Administrador de configuración local de DSC de PowerShell](https://msdn.microsoft.com/powershell/dsc/metaconfig4) coinciden con su caso de uso, y quiere incorporar máquinas de modo que **tanto** extraigan información del DSC de Automatización de Azure como que envíen informes allí:

   + En cada máquina con Linux que vaya a incorporar a DSC de Automatización de Azure, use Register.py para incorporarla con los valores predeterminados del Administrador de configuración local de DSC de PowerShell:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   + Para buscar la clave de registro y la dirección URL de registro para su cuenta de Automatización, consulte la sección [**Registro seguro**](#secure-registration) .

     Si los valores predeterminados del Administrador de configuración local de DSC de PowerShell **no** coinciden con su caso de uso, o desea incorporar equipos que solo informen a DSC de Azure Automation, pero que no extraigan configuración ni módulos de PowerShell de allí, siga los pasos 3 a 6.** ** De lo contrario, vaya directamente al paso 6.

3. Siga las indicaciones de la sección [**Generación de metaconfiguraciones de DSC**](#generating-dsc-metaconfigurations) que aparece a continuación para generar una carpeta que contenga las metaconfiguraciones de DSC necesarias.
4. Aplique de forma remota la metaconfiguración de DSC de PowerShell a las máquinas que desea incorporar:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String "<root password>" -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard

    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

La máquina desde la que se ejecuta este comando debe tener instalada la versión más reciente de [WMF 5](http://aka.ms/wmf5latest) .

1. Si no se pueden aplicar las metaconfiguraciones de DSC de PowerShell de forma remota, para cada máquina con Linux que vaya a incorporar, copie la metaconfiguración correspondiente a esa máquina desde la carpeta del paso 5 en la máquina con Linux. Después, llame a `SetDscLocalConfigurationManager.py` localmente en cada máquina Linux que desee incorporar a DSC de Automatización de Azure:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Mediante el Portal de Azure o los cmdlets, compruebe que las máquinas que vaya a incorporar aparezcan ahora como nodos de DSC registrados en su cuenta de Automatización de Azure.

## <a name="generating-dsc-metaconfigurations"></a>Generación de metaconfiguraciones de DSC

Para incorporar genéricamente cualquier máquina a DSC de Automatización de Azure, se puede generar una [metaconfiguración de DSC](https://msdn.microsoft.com/en-us/powershell/dsc/metaconfig) que, cuando se aplique, indique al agente de DSC de la máquina que extraiga de DSC de Automatización de Azure o informe allí. Las metaconfiguraciones de DSC para DSC de Automatización de Azure se pueden generar con una configuración de DSC de PowerShell o con los cmdlets de PowerShell de Automatización de Azure.

> [!NOTE]
> Las metaconfiguraciones de DSC contienen los secretos necesarios para incorporar una máquina a una cuenta de Automation para su administración. Asegúrese de proteger adecuadamente cualquier metaconfiguración de DSC que cree o elimínelos tras su uso.

### <a name="using-a-dsc-configuration"></a>Uso de una configuración de DSC

1. Abra PowerShell ISE como administrador en una máquina de su entorno local. La máquina debe tener instalada la versión más reciente de [WMF 5](http://aka.ms/wmf5latest) .
2. Copie el siguiente script localmente. Este script contiene una configuración de DSC de PowerShell para crear metaconfiguraciones y un comando para iniciar la creación de la metaconfiguración.

    ```powershell
    # The DSC configuration that will generate metaconfigurations
    [DscLocalConfigurationManager()]
    Configuration DscMetaConfigs
    {

        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = "ApplyAndMonitor",

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = "ContinueConfiguration",

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq "")
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
        $RefreshMode = "PUSH"
        }
        else
        {
        $RefreshMode = "PULL"
        }

        Node $ComputerName
        {

            Settings
            {
                RefreshFrequencyMins = $RefreshFrequencyMins
                RefreshMode = $RefreshMode
                ConfigurationMode = $ConfigurationMode
                AllowModuleOverwrite = $AllowModuleOverwrite
                RebootNodeIfNeeded = $RebootNodeIfNeeded
                ActionAfterReboot = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationDSC
                {
                    ServerUrl = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationDSC
                {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationDSC
            {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
    }

    # Create the metaconfigurations
    # TODO: edit the below as needed for your use case
    $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
    }

    # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    DscMetaConfigs @Params
    ```

3. Rellene la clave del Registro y la dirección URL para su cuenta de Automatización, así como los nombres de las máquinas que quiere incorporar. Todos los demás parámetros son opcionales. Para buscar la clave de registro y la dirección URL de registro para su cuenta de Automatización, consulte la sección [**Registro seguro**](#secure-registration) .
4. Si quiere que las máquinas notifiquen la información de estado de DSC a DSC de Automatización de Azure, pero que no extraigan configuración o módulos de PowerShell, establezca el parámetro **ReportOnly** en verdadero.
5. Ejecute el script. Ahora debería tener una carpeta llamada **DscMetaConfigs** en el directorio de trabajo que contenga las metaconfiguraciones de DSC de PowerShell para las máquinas que quiere incorporar (como administrador):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Uso de los cmdlets de Automatización de Azure

Si los valores predeterminados del Administrador de configuración local de DSC de PowerShell coinciden con su caso de uso, y quiere incorporar máquinas de modo que ambas extraigan de DSC de automatización de Azure y notifiquen allí, los cmdlets de Automatización de Azure ofrecen un método simplificado de generar las configuraciones de DSC necesarias:

1. Abra la consola de PowerShell o PowerShell ISE como administrador en una máquina de su entorno local.
2. Conéctese a Azure Resource Manager con **Add-AzureRmAccount**
3. Descargue las metaconfiguraciones de DSC de PowerShell para las máquinas que quiere incorporar desde la cuenta de Automatización a la que quiere incorporar nodos:

    ```powershell
    # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
    $Params = @{

        ResourceGroupName = 'ContosoResources'; # The name of the ARM Resource Group that contains your Azure Automation Account
        AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
        ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
        OutputFolder = "$env:UserProfile\Desktop\";
    }
    # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    Get-AzureRmAutomationDscOnboardingMetaconfig @Params
    ```
    
4. Ahora debería tener una carpeta llamada ***DscMetaConfigs***, que contenga las metaconfiguraciones de DSC de PowerShell para las máquinas que quiere incorporar (como administrador):
    
    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Registro seguro

Las máquinas se pueden incorporar de forma segura a una cuenta de Automatización de Azure mediante el protocolo de registro WMF 5 de DSC, que permite que un nodo de DSC se autentique en un servidor de informes o de extracción de DSC de PowerShell V2 (lo que incluye DSC de Automatización de Azure). El nodo se registra en el servidor en una **dirección URL de registro**, que se autentica mediante una **clave de registro**. Durante el registro, el nodo de DSC y el servidor de informes o de extracción de DSC negocian un certificado único que este nodo usará para autenticarse en el servidor después del registro. Este proceso impide que los nodos incorporados se suplanten entre sí, como por ejemplo si un nodo está afectado y se comporta de forma malintencionada. Después del registro, la clave de registro no se vuelve a usar para la autenticación y se elimina del nodo.

Puede obtener la información necesaria para este protocolo de registro de DSC en la hoja **Administrar claves** del Portal de vista previa de Azure. Para abrir esta hoja, haga clic en el icono de llave en el panel **Essentials** de la cuenta de Automatización.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

* La dirección URL de registro es el campo de dirección URL en la hoja Administrar claves.
* La clave de registro es la clave de acceso principal o la clave de acceso secundaria en la hoja Administrar claves. Se puede usar cualquiera de las dos.

Para mayor seguridad, se pueden volver a generar las claves de acceso primaria y secundaria de una cuenta de Automatización en cualquier momento (en la hoja **Administrar claves** ) para evitar que los registros de nodo futuros usen claves anteriores.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Solución de problemas de incorporación de máquinas virtuales de Azure

DSC de Automatización de Azure permite incorporar fácilmente máquinas virtuales de Azure con Windows para la administración de configuraciones. Internamente, la extensión Configuración de estado deseado de la máquina virtual de Azure se usa para registrar la máquina virtual con DSC de Automatización de Azure. Puesto que la extensión Configuración de estado deseado de la máquina virtual de Azure se ejecuta de forma asincrónica, puede ser importante realizar el seguimiento de su progreso y solucionar los problemas de ejecución.

> [!NOTE]
> Cualquier método para incorporar una máquina virtual de Azure con Windows a DSC de Automatización de Azure que use la extensión Configuración de estado deseado de la máquina virtual de Azure podría tardar hasta una hora en mostrar el nodo como registrado en Automatización de Azure. Esto se debe a la instalación de Windows Management Framework 5.0 en la máquina virtual por la extensión de DSC de la máquina virtual de Azure, que es necesario para incorporar la máquina a DSC de Automatización de Azure.

Para solucionar problemas o ver el estado de la extensión de configuración de estado deseado de una máquina virtual de Azure, en Azure Portal navegue hasta la máquina virtual que se va a incorporar y haga clic en -> **Toda la configuración** -> **Extensiones** -> **DSC**. Para obtener más detalles, puede hacer clic en **Ver estado detallado**.

[![](./media/automation-dsc-onboarding/DSC_Onboarding_5.png)](https://technet.microsoft.com/library/dn249912.aspx)

## <a name="certificate-expiration-and-reregistration"></a>Expiración y repetición del registro del certificado

Después de registrar un equipo como un nodo de DSC en DSC de automatización de Azure, hay una serie de motivos por los que puede que necesite volver a registrar ese nodo en el futuro:

* Tras el registro, cada nodo negocia automáticamente un certificado único para la autenticación que expira después de un año. En la actualidad, el protocolo de registro de DSC de PowerShell no puede renovar automáticamente los certificados que vayan a expirar, por lo que tendrá que volver a registrar los nodos transcurrido un año. Antes de volver a registrar, asegúrese de que cada nodo ejecute Windows Management Framework 5.0 RTM. Si el certificado de autenticación de un nodo expira y el nodo no se registra de nuevo, este no podrá comunicarse con la Azure Automation y se marcará como "No responde". Si dicha actualización se realiza en un plazo de 90 días o menos desde la fecha de expiración del certificado o en cualquier momento después de dicha fecha, se generará y usará un certificado nuevo.
* Para cambiar cualquier [valor del Administrador de configuración local de DSC de PowerShell](https://msdn.microsoft.com/powershell/dsc/metaconfig4) que se estableció durante el registro inicial del nodo, como ConfigurationMode. Actualmente, estos valores de agente DSC solo pueden cambiarse mediante un nuevo registro. La única excepción es la configuración de nodo asignada al nodo, que se puede cambiar directamente en DSC de Automatización de Azure.

El nuevo registro se puede realizar tal y como registró el nodo inicialmente, con cualquiera de los métodos incorporados descritos en este documento. No es necesario anular el registro de un nodo desde DSC de Automatización de Azure antes de volver a registrarlo.

## <a name="related-articles"></a>Artículos relacionados

* [Información general de DSC de Automatización de Azure](automation-dsc-overview.md)
* [Cmdlets de DSC de Automatización de Azure](https://msdn.microsoft.com/library/mt244122.aspx)
* [Precios de DSC de Automatización de Azure](https://azure.microsoft.com/pricing/details/automation/)

