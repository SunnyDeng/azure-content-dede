<properties
   pageTitle="Sicherer Replikationsdatenverkehr zustandsbehafteter Dienste in Azure Service Fabric"
   description="Wenn die Replikation aktiviert ist, replizieren zustandsbehaftete Dienste ihren Zustand von einem primären Replikat auf sekundäre Replikate. Dieser Datenverkehr muss vor Lauschangriffen und Manipulation geschützt werden."
   services="service-fabric"
   documentationCenter=".net"
   authors="leikong"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="leikong"/>

# Sicherer Replikationsdatenverkehr

Wenn die Replikation aktiviert ist, replizieren zustandsbehaftete Dienste den Zustand auf allen Replikaten. Auf dieser Seite wird erläutert, wie Sie den Schutz dieses Datenverkehrs konfigurieren.

Es gibt zwei Arten von Sicherheitseinstellungen, die unterstützt werden:

- X509: Schützt den Kommunikationskanal mit dem X509-Zertifikat. Von den Benutzern wird erwartet, dass sie private Schlüssel mit ACL zertifizieren, um Actor/Dienst-Hostprozesse zu ermöglichen und die Zertifikatanmeldeinformationen verwenden zu können.
- Windows: Schützt den Kommunikationskanal mit Windows-Sicherheit (erfordert Active Directory).

Der folgende Abschnitt zeigt, wie die beiden oben angegebenen Einstellungstypen konfiguriert werden. Die Konfiguration "CredentialType" legt fest, welcher Typ verwendet wird.

## CredentialType=X509

### Konfigurationsnamen

|Name|Anmerkungen|
|----|-------|
|StoreLocation|Speicherort des Zertifikats: "CurrentUser" oder "LocalMachine"|
|StoreName|Speichername des Zertifikats|
|FindType|Identifiziert den im Parameter "FindValue" angegebenen Werttyp: "FindBySubjectName" oder "FindByThumbPrint"|
|FindValue|Suchziel für die Suche nach dem Zertifikat|
|AllowedCommonNames|Eine kommagetrennte Liste mit allgemeinen Zertifikat-/DNS-Namen, die von Replikatoren verwendet werden|
|IssuerThumbprints|Eine kommagetrennte Liste der Zertifikatfingerabdrücke des Ausstellers. Wenn angegeben, wird das eingehende Zertifikat überprüft, wenn es von einem der Einträge in der Liste ausgestellt wurde. Die Überprüfung der Zertifikatkette wird immer ausgeführt.|
|RemoteCertThumbprints|Eine kommagetrennte Liste der von Replikatoren verwendeten Zertifikatfingerabdrücke.|
|ProtectionLevel|Gibt an, wie die Daten geschützt werden: "Sign", "EncryptAndSign" oder "None"|

## CredentialType=Windows

### Konfigurationsnamen

|Name|Anmerkungen|
|----|-------|
|ServicePrincipalName|Der für den Dienst registrierte Dienstprinzipalname. Kann leer sein, wenn der Dienst/Actor-Hostprozesse als Computerkonto ausgeführt wird (z. B. NetworkService, LocalSystem usw.)|
|WindowsIdentities|Eine kommagetrennte Liste mit Windows-Identitäten aller Service/Actor-Hostprozesse
|ProtectionLevel|Gibt an, wie die Daten geschützt werden: "Sign", "EncryptAndSign" oder "None"|

## Beispiele

### Beispiel 1: CredentialType=X509

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="X509" />
  <Parameter Name="FindType" Value="FindByThumbprint" />
  <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
  <Parameter Name="StoreLocation" Value="LocalMachine" />
  <Parameter Name="StoreName" Value="My" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
  <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
</Section>
```

### Beispiel 2: CredentialType=Windows
In diesem Ausschnitt werden als Beispiel alle Dienst/Actor-Hostprozesse als NetworkService oder LocalSystem ausgeführt. ServicePrincipalName ist leer.

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="" />
  <!--This machine group contains all machines in a cluster-->
  <Parameter Name="WindowsIdentities" Value="redmond\ClusterMachineGroup" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```

### Beispiel 3: CredentialType=Windows
In diesem Ausschnitt werden als Beispiel alle Dienst/Actor-Hostprozesse als gruppenverwaltetes Dienstkonto mit einem gültigen ServicePrincipalName ausgeführt.

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="servicefabric/cluster.microsoft.com" />
  <--All actor/service host processes run as redmond\GroupManagedServiceAccount-->
  <Parameter Name="WindowsIdentities" Value="redmond\GroupManagedServiceAccount" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```
 

<!---HONumber=July15_HO2-->