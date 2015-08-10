<properties 
	pageTitle="Azure Cloud Services – Dienstdefinition und Dienstkonfiguration – XML-Zertifikat" 
	description="Informationen zum Konfigurieren eines Zertifikats in den Dienstdefinitions- und -konfigurationsdateien." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015"
	ms.author="adegeo"/>



# Konfigurieren der Dienstdefinition und Konfiguration für ein Zertifikat

Virtuellen Computern, in denen Ihre Web- oder Workerrollen ausgeführt werden, können Zertifikate zugeordnet werden. Nachdem Sie das Zertifikat in das Portal hochgeladen haben, müssen Sie die Dienstdefinition \(.csdef\) und Dienstkonfigurationsdateien \(.cscfg\) für das Zertifikat konfigurieren.

Der virtuelle Computer kann auf den privaten Schlüssel des Zertifikats zugreifen, nachdem es installiert wurde. Aus diesem Grund sollten Sie den Zugriff auf Prozesse mit erhöhten Berechtigungen einschränken.

## Beispiel einer Dienstdefinition

Hier ist ein Beispiel eines in der Dienstdefinition definierten Zertifikats.

```xml
<ServiceDefinition name="WindowsAzureProject4" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="MyWokerRole"> <!-- or <WebRole name="MyWebRole" vmsize="Small"> -->
    <ConfigurationSettings>
      ...
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="MySSLCert" storeLocation="LocalMachine" storeName="My" permissionLevel="elevated" />
    </Certificates>
  </WorkerRole>
</ServiceDefinition>
```

### Berechtigungen
Berechtigungen \(`permisionLevel`-Attribut\) können auf eine der folgenden Einstellungen festgelegt werden:

| Berechtigungswert | Beschreibung |
| ----------------  | ----------- |
| limitedOrElevated | **\(Standard\)** Alle Rollenprozesse können auf den privaten Schlüssel zugreifen. |
| elevated | Nur Prozesse mit erhöhten Rechten können auf den privaten Schlüssel zugreifen.|

## Beispiel einer Dienstkonfiguration

Hier ist ein Beispiel eines in der Dienstkonfiguration definierten Zertifikats.

```xml
<Role name="MyWokerRole">
...
    <Certificates>
        <Certificate name="MySSLCert" 
            thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
            thumbprintAlgorithm="sha1" />
    </Certificates>
...
</Role>
```

**Beachten Sie** die übereinstimmenden `name`-Attribute.

## Nächste Schritte
Überprüfen Sie das [XML-Schema der Dienstdefinition](https://msdn.microsoft.com/library/azure/ee758711.aspx) und das [XML-Schema der Dienstkonfiguration](https://msdn.microsoft.com/library/azure/ee758710.aspx).

<!---HONumber=July15_HO5-->