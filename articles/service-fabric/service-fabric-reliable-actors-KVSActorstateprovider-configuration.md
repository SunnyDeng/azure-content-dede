<properties
   pageTitle="Übersicht über die Konfiguration von Azure Service Fabric Reliable Actors vom Typ „KVSActorStateProvider“ | Microsoft Azure"
   description="Erfahren Sie mehr über das Konfigurieren von statusbehafteten Azure Service Fabric Actors vom Typ „KVSActorStateProvider“"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/28/2015"
   ms.author="sumukhs"/>

# Konfigurieren von Reliable Actors – KVSActorStateProvider
Sie können die Standardkonfiguration von KVSActorStateProvider ändern, indem Sie die Datei „settings.xml“, die im Stammverzeichnis des Visual Studio-Pakets im Ordner „Config“ generiert wurde, für den betreffenden Actor ändern.

Standardmäßig sucht die Azure Service Fabric-Laufzeit in der Datei „settings.xml“ nach vordefinierten Abschnittsnamen und nutzt die Konfigurationswerte beim Erstellen der zugrunde liegenden Laufzeitkomponenten.

>[AZURE.NOTE]Löschen bzw. ändern Sie **nicht** die Abschnittsnamen der folgenden Konfigurationen in der Datei „settings.xml“, die in der Visual Studio-Lösung generiert wird.

## Replicator-Sicherheitskonfiguration
Replicator-Sicherheitskonfigurationen werden verwendet, um den während der Replikation verwendeten Kommunikationskanal zu sichern. Dies bedeutet, dass Dienste ihren gegenseitigen Replikationsdatenverkehr nicht erkennen können. Dadurch wird sichergestellt, dass die Daten nicht nur hochverfügbar, sondern auch sicher sind. Standardmäßig wird die Replikationssicherheit durch einen leeren Sicherheitskonfigurationsabschnitt verhindert.

### Name des Abschnitts
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## Replicator-Konfiguration
Replicator-Konfigurationen konfigurieren den Replicator, der dafür verantwortlich ist, den Status des Actor-Status-Anbieters hochverfügbar zu machen. Die Standardkonfiguration wird von der Visual Studio-Vorlage generiert und sollte ausreichen. Dieser Abschnitt befasst sich mit zusätzlichen Konfigurationen, die zum Optimieren des Replicators verfügbar sind.

### Name des Abschnitts
&lt;ActorName&gt;ServiceReplicatorConfig

### Konfigurationsnamen

|Name|Unit|Standardwert|Hinweise|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Sekunden|0,05|So lange wartet der Replicator auf dem sekundären Replicator nach dem Empfang eines Vorgangs, bevor er eine Bestätigung an den primären Replicator sendet. Alle anderen Bestätigungen, die für innerhalb dieses Intervalls verarbeitete Vorgänge gesendet werden, werden als eine einzelne Antwort gesendet.|
|ReplicatorEndpoint|N/V|Kein Standardwert – Erforderlicher Parameter|Die IP-Adresse und der Port, die der primäre/sekundäre Replicator für die Kommunikation mit anderen Replicatoren in der Replikatgruppe verwendet. Dabei sollte im Dienstmanifest auf einen TCP-Ressourcenendpunkt verwiesen werden. Weitere Informationen zum Definieren von Endpunktressourcen im Dienstmanifest finden Sie unter [Dienstmanifestressourcen](service-fabric-service-manifest-resources.md). |
|RetryInterval|Sekunden|5|Der Zeitraum, nach dem der Replicator eine Nachricht erneut überträgt, wenn er keine Bestätigung für einen Vorgang erhält.|
|MaxReplicationMessageSize|Byte|50 MB|Die maximale Größe der Replikationsdaten, die in einer einzelnen Nachricht übertragen werden können.|
|MaxPrimaryReplicationQueueSize|Anzahl der Vorgänge|1024|Die maximale Anzahl der Vorgänge in der primären Warteschlange. Ein Vorgang wird freigegeben, nachdem der primäre Replicator eine Bestätigung von allen sekundären Replicators empfangen hat. Dieser Wert muss größer als 64 und eine Potenz von 2 sein.|
|MaxSecondaryReplicationQueueSize|Anzahl der Vorgänge|2048|Die maximale Anzahl der Vorgänge in der sekundären Warteschlange. Ein Vorgang wird freigegeben, nachdem sein Zustand durch Persistenz hochverfügbar gemacht wurde. Dieser Wert muss größer als 64 und eine Potenz von 2 sein.|

## Speicherkonfiguration
Speicherkonfigurationen werden zum Konfigurieren des lokalen Speichers verwendet, der zum Beibehalten des zu replizierenden Status verwendet wird. Die Standardkonfiguration wird von der Visual Studio-Vorlage generiert und sollte ausreichen. Dieser Abschnitt befasst sich mit zusätzlichen Konfigurationen, die zum Optimieren des lokalen Speichers verfügbar sind.

### Name des Abschnitts
&lt;ActorName&gt;ServiceLocalStoreConfig

### Konfigurationsnamen

|Name|Unit|Standardwert|Hinweise|
|----|----|-------------|-------|
|MaxAsyncCommitDelay|Millisekunden|200|Legt das maximale Batchverarbeitungsintervall für permanente Commits des lokalen Speichers fest.|
|MaxVerPages|Anzahl von Seiten|8192|Die maximale Anzahl von Versionsseiten in der lokalen Speicherdatenbank. Sie bestimmt die maximale Anzahl von ausstehenden Transaktionen.|

## Beispiel einer Konfigurationsdatei

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```
## Hinweise

Der Parameter „BatchAcknowledgementInterval“ steuert die Replikationslatenz. Der Wert "0" ergibt die geringstmögliche Latenz, allerdings auf Kosten des Durchsatzes (da eine größer Anzahl von Bestätigungsnachrichten gesendet und verarbeitet werden muss, von denen jede weniger Bestätigungen enthält). Je größer der Wert für "BatchAcknowledgementInterval" ist, um so höher ist der Gesamtdurchsatz der Replikation, zu Lasten einer höheren Vorgangslatenz. Daraus ergibt sich direkt die Latenz von Transaktions-Commits.

<!---HONumber=AcomDC_0114_2016-->