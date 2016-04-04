<properties 
pageTitle="Cloud Services-Rollenkonfiguration-XPath-Spickzettel | Microsoft Azure" 
description="Die verschiedenen XPath-Einstellungen, die Sie in der Clouddienst-Rollenkonfiguration verwenden können, um Einstellungen als Umgebungsvariable verfügbar zu machen." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="02/25/2016" 
ms.author="adegeo"/>

# Verfügbarmachen von Rollenkonfigurationseinstellungen als Umgebungsvariable mit XPath

In der Dienstdefinitionsdatei der Clouddienst-Worker- oder Webrolle können Sie Laufzeitkonfigurationswerte als Umgebungsvariablen verfügbar machen. Die folgenden XPath-Werte (die API-Werten entsprechen) werden unterstützt.

Diese XPath-Werte sind auch über die [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx)-Bibliothek verfügbar.

## Im Emulator ausgeführte App

Gibt an, dass die Anwendung im Emulator ausgeführt wird

| Typ | Beispiel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@emulated" |
| Code | var x = RoleEnvironment.IsEmulated; |


## Bereitstellungs-ID

Ruft die Bereitstellungs-ID für die Instanz ab

| Typ | Beispiel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@id" |
| Code | var deploymentId = RoleEnvironment.DeploymentId; |


## Rollen-ID 

Ruft die aktuelle Rollen-ID für die Instanz ab

| Typ | Beispiel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@id" |
| Code | var id = RoleEnvironment.CurrentRoleInstance.Id; |


## Domäne aktualisieren

Ruft die Updatedomäne der Instanz ab

| Typ | Beispiel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Code | var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |


## Fehlerdomäne

Ruft die Fehlerdomäne der Instanz ab

| Typ | Beispiel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Code | var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |


## Rollenname

Ruft den Rollennamen der Instanzen ab

| Typ | Beispiel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Code | var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |


## Konfigurationseinstellung

Ruft den Wert der angegebenen Konfigurationseinstellung ab

| Typ | Beispiel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Code | var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |
 
## Lokaler Speicherpfad

Ruft den lokalen Speicherpfad für die Instanz ab

| Typ | Beispiel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Code | var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1").RootPath; |


## Größe des lokalen Speichers

Ruft die Größe des lokalen Speichers für die Instanz ab

| Typ | Beispiel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Code | var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## Endpunktprotokoll 

Ruft das Endpunktprotokoll für die Instanz ab

| Typ | Beispiel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Code | var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].Protocol; |

## Endpunkt-IP

Ruft die IP-Adresse des angegebenen Endpunkts ab

| Typ | Beispiel |
| ----- | ---- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Code | var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Address |

## Endpunktport 

Ruft den Endpunktport für die Instanz ab

| Typ | Beispiel |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Code | var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Port; |





## Beispiel

Hier ist ein Beispiel für eine Workerrolle, die eine Startaufgabe mit der Umgebungsvariable `TestIsEmulated` erstellt, festgelegt auf den [XPath-Wert @emulated](#app-running-in-emulator).

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## Nächste Schritte

Erfahren Sie mehr über die Datei [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg).

Erstellen Sie ein [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg)-Paket.

Aktivieren Sie [Remotedesktop](cloud-services-role-enable-remote-desktop.md) für eine Rolle.

<!---HONumber=AcomDC_0323_2016-->