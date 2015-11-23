<properties
 pageTitle="IoT-Geräteverwaltung | Microsoft Azure"
 description="Übersicht über die Verwaltung von IoT-Geräten mit IoT Hub und IoT Suite"
 services="iot-hub,iot-suite"
 documentationCenter=""
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/10/2015"
 ms.author="sethm"/>

# IoT-Geräteverwaltung mithilfe der Azure IoT Suite und Azure IoT Hub

Azure IoT Suite und Azure IoT Hub bieten grundlegende Funktionen, um die Geräteverwaltung für IoT-Lösungen präzise und für verschiedene Geräte und Gerätetopologien zu ermöglichen. Geräteverwaltung bezieht sich in diesem Artikel ausdrücklich auf die IoT-Geräteverwaltung.

## Einführung

Dienstanbieter und Unternehmen oder Organisationen, die den IoT-Gerätebestand verwalten, nutzen die Geräteverwaltungsfunktionen, um die folgenden Geschäftsprozesse durchzuführen:

* Registrierung und Suche von IoT-Geräten.
* Herstellung von Konnektivität.
* Remotekonfiguration und -aktualisierung von Software auf Geräten. Beim Verwalten von Geräten in Fertigungsstätten oder auf Ölfeldern gibt es z. B. Richtlinien für die Remotekonfiguration und -aktualisierung von Geräten, bei denen Bewilligungsketten, behördliche Auditauflagen, das Vorhandensein von physischen Sicherheitsvorkehrungen und mehr berücksichtigt werden müssen.

Wenn Sie die IoT-Geräteverwaltung für Ihre IoT-Lösung aktivieren, sollten Sie die folgenden Funktionen berücksichtigen und deren Bedeutung im Hinblick auf Ihre Geschäftsziele festlegen:

* **[Gerätebereitstellung und -suche](#device-provisioning-and-discovery)**: Prozess, mit dem ein Gerät im System registriert wird.
* **[Geräteregistrierung und -modelle](#device-registry-and-device-models)**: Methode, wie die Gerätemodelle die strukturierte Verwendung von Metadaten für Gerätebeziehungen, Rollen im System und Validierungsmethoden darstellen.
* **[Gerätezugriffsverwaltung](#device-access-management)**: Methode, wie Geräte den Zugriff auf Geräteressourcen von Clouddiensten steuern.
* **[Remotesteuerung](#remote-control)**: Methode, wie Remotebenutzer Zugriff auf Geräte erhalten und Geräteänderungen anweisen.
* **[Remoteverwaltung](#remote-administration-and-monitoring)**: Prozess, mit dem ein Administrator die Integrität des Gerätebestands definiert.  
* **[Remotekonfiguration](#remote-configuration)**: Methode für Administratoren zum Ändern der Gerätekonfiguration.
* **[Remoteaktualisierung von Firmware und Software](#remote-firmware-and-software-update)**: Prozess, mit dem Administratoren die Gerätefirmware und -software aktualisieren können.

Die folgenden Abschnitte bieten einen tiefer reichenden Einblick in die einzelnen Geräteverwaltungsfunktionen sowie ein allgemeines Modell für die Implementierung dieser Funktionen mithilfe von Azure IoT Hub.

## Gerätebereitstellung und -suche

Sie stellen ein Gerät mit Azure IoT Hub über die Registrierungs-API bereit. Nachdem Sie Ihr Gerät registriert und einen Schlüssel bereitgestellt oder erhalten haben, können Sie Ihr Gerät aktivieren, damit mit diesem Schlüssel eine Verbindung mit IoT Hub hergestellt werden kann. Azure IoT Hub kommuniziert nur mit registrierten Geräten, die über autorisierte Anmeldeinformationen verfügen. Administratoren können den Gerätezugriff für Azure IoT Hub über das Geräteverwaltungsportal deaktivieren.

Je nachdem, wie die IoT-Geräte hergestellt, bereitgestellt und implementiert wurden, kann ein Bootstrap-Prozess verwendet werden. Sie können einen Bootstrapdienst als Teil Ihrer Lösung herstellen, um eine einfache Konnektivität zu ermöglichen und den Prozess der Zuweisung eines Geräts zu einem bestimmten IoT Hub zu verzögern. Der Ort, an dem das Gerät eingesetzt wird, kann zum Zeitpunkt der Herstellung des Geräts unbekannt sein. Dies ist nur ein Beispiel für eine große Anzahl von potenziell komplexen Workflows, mit denen Azure IoT Hub auf ein Gerät hingewiesen werden kann. Darüber hinaus lassen sie sich auch in bestehende Geschäftsprozesse integrieren.

Wenn Sie einen Bootstrapdienst verwenden, wird ein IoT-Gerät gestartet und gibt eine Anforderung aus, das letztendlich Zugriff auf einen zugewiesenen Azure IoT Hub bieten kann. Die Anforderung sollte Bootstrapanmeldeinformationen des Geräts und alle anderen erforderlichen Daten enthalten. Bei autorisierten Geräten sollte der Bootstrapdienst das Gerät bei einem zugewiesenen Azure IoT Hub registrieren und dem Gerät, das einen Bootstrap anfordert, Konnektivitätsdetails bereitstellen. IoT Hub stellt dem Gerät, das einen Bootstrap anfordert, Konnektivitätsdetails zur Verfügung. Bei autorisierten Geräten sollte der Bootstrapdienst das Gerät bei einem zugewiesenen Azure IoT Hub registrieren und dem Gerät, das einen Bootstrap anfordert, Konnektivitätsdetails bereitstellen.

## Geräteregistrierung und Gerätemodelle

Die Verwendung des Begriffs *Gerätemodell* bezieht sich auf das Modell, das Eigenschaften enthält, die von einem Clouddienst gelesen oder geschrieben werden können. Es umfasst auch Gerätebefehle, die von einem Clouddienst remote ausgeführt werden können. In dem dienstorientierten Modell, das im nächsten Abschnitt beschrieben wird, kennt das Gerät nicht das Modell, aber Clouddienste können weiterhin Metadaten für Geräte nachverfolgen und verwenden.

Das Speichern von Geräteinformationen und zugehörigen Metadaten ist maßgeblich für das IoT-System und für die Rolle der Geräteregistrierung. Dies gilt besonders für ältere Geräte, die nicht geändert werden können, oder für Geräte, die kein Gerätemodell verwenden. Ein dienstorientiertes Modell kann einen IoT-Dienst für die Interaktion mit einem Gerätebestand aktivieren. Wenn Geräte ein definiertes Modell verwenden, dient die Geräteregistrierung als eine konsistente Ansicht der Gerätedaten, bei denen das Gerät als Master fungiert. In diesem Fall informiert der Dienst das Gerät über die gewünschten Änderungen, die erst gültig werden, nachdem das Gerät die Änderung bestätigt hat.

### Dienstorientiertes Modell

Wenn ein Gerät eine Verbindung mit Azure IoT Hub herstellt und kein Gerätemodell bietet, muss unbedingt eine Geräteregistrierung implementiert werden, mit der registrierte Geräte und die zugehörigen Metadaten nachverfolgt werden. In diesem Fall ist die Geräteregistrierung der einzige Speicherort für die Gerätemetadaten. Beispiele für die Gerätemetadaten, die Sie nachverfolgen sollten, sind die logische Topologie (z. B. Etage 4, Gebäude 109), die Gerätefunktion (z. B. Türsensor) oder andere Taginformationen.

### Geräteorientiertes Modell

Um es Ihrer IoT-Lösung zu ermöglichen, die Funktionen Ihres IoT-Geräts zu nutzen, müssen sich die Geräte selbst für die Cloud beschreiben, nachdem sie eine Verbindung mit IoT Hub hergestellt haben. Im Folgenden finden Sie zwei Typen von Gerätemodellen, die in einer IoT-Lösung verwendet werden können:

#### Selbstdefiniertes Gerätemodell

Ein Geräteingenieur (oder Entwickler, der einen Gerätesimulator verwendet) nutzt das selbstdefinierte Gerätemodell, indem die Funktionen des Geräts bei dessen Herstellung durchlaufen werden. Ein Geräteingenieur kann zunächst ein Gerät mit wenigen Eigenschaften und unterstützten Befehlen erstellen und dann später weitere hinzufügen. Dieser Geräteingenieur könnte aber auch über viele Geräte verfügen, die jeweils einzigartige Funktionen aufweisen und das selbstdefinierte Modell verwenden. In diesem Fall muss der Geräteingenieur die Struktur des Gerätemodells nicht registrieren. Beträchtliche Unterschiede bei selbstdefinierten Modellen stellen möglicherweise bei der Skalierung auf eine große Anzahl von Geräten eine Herausforderung dar.

#### Vordefiniertes Gerätemodell

Eine Produktions-IoT-Bereitstellung, bei der Netzwerk- und Leistungs-/Verarbeitungseinschränkungen vorliegen, kann maßgeblich von einem vordefinierten Gerätemodell profitieren, bei dem das Gerät nur minimale Verarbeitungsleistung und minimale Leistungsaufnahme aufbringen muss. Auf ähnliche Weise ermöglicht ein minimaler Netzwerkverkehr es den Geräten, Übertragungen über heterogene Netzwerke (WiFi, 2G/3G/4G, BLE, Sat usw.) durchzuführen, besonders bei einer eingeschränkten und teuren Infrastruktur (z. B. Satelliten). Bei der Implementierung eines vordefinierten Gerätemodells kann ein Geräteingenieur Geräteinformationen senden, die als ein oder zwei Bytes codiert sind und die als Schlüssel für das vordefinierte Gerätemodell fungieren. Die Übersichtlichkeit dieses Ansatzes führt im Vergleich zum selbstdefinierten Gerätemodell zu gesteigerter Effizienz von einer oder zwei Größenordnungen.

### Die vorkonfigurierte Lösung zur Remoteüberwachung und das Gerätemodell

Die vorkonfigurierte Azure IoT Suite-Lösung zur Remoteüberwachung implementiert ein selbstdefiniertes Gerätemodell. Mit diesem Modell ist bei der Definition und Entwicklung von Funktionen des Geräts ein schnelles Durchlaufen möglich.

Der Code, mit dem die vorkonfigurierte Lösung zur Remoteüberwachung ein Geräteobjekt erstellt und dann an den Dienst sendet, ist in **Simulator/Simulator.WorkerRole/SimulatorCore/Devices/DeviceBase.cs** definiert. Bei den Methoden `SendDeviceInfo` und `GetDeviceInfo` sehen Sie, wie das selbstbeschreibende Gerätemodell erstellt und an den Azure IoT Hub gesendet wird.

Der Code, mit dem der Clouddienst die mit dem Gerätemodell verbundenen Ereignisse verarbeitet, befindet sich in **EventProcessor/EventProcessor.WorkerRole/Processors/DeviceManagementProcessor.cs**. Die Hauptarbeit in Zusammenhang mit Reaktionen auf die mit dem Gerätemodell verbundenen Nachrichten, die an die Serverseite der vorkonfigurierten Lösung gesendet werden, erfolgt in der `ProcessJToken`-Methode.

Sobald eine mit dem Gerätemodell verbundene Nachricht empfangen wird, sind die Methoden `UpdateDeviceFromSimulatedDeviceInfoPacketAsync` und `UpdateDeviceFromRealDeviceInfoPacketAsync` in **DeviceManagement/Infrastructure/BusinessLogic/DeviceLogic.cs** für das Aktualisieren der Geräteregistrierung zuständig. Die Geräteregistrierungs-APIs in der vorkonfigurierten Lösung zur Remoteüberwachung finden Sie in **DeviceManagement/Web/WebApiControllers/DeviceApiController.cs**.

### Bereichsgateway-Gerätemodelle

Bereichsgateways werden häufig zur Aktivierung der Konnektivität und zur Protokollierung für Geräte verwendet, die sich nicht direkt mit dem Internet verbinden können oder sollen. Wenn das Gerät, das Sie erstellen, ein Bereichsgateway ist, kann es die Geräte darstellen, die bei allen Interaktionen über das Bereichsgateway mit Azure IoT Hub verbunden sind. Als Hersteller des Bereichsgateways ist es Ihre Aufgabe, die Übersetzung zwischen Geräteprotokollen und Protokollen, die vom IoT-Dienst unterstützt werden, zu implementieren. Wenn das Bereichsgateway eine Verbindung mit BLE-Geräten (Bluetooth Low Energy) herstellen können soll, müssen Sie die BLE-Schnittstelle für Geräte und die Schnittstelle mit Azure IoT Hub implementieren.

## Gerätezugriffsverwaltung

Die vorkonfigurierte IoT Suite-Lösung steuert den Zugriff auf verschiedene Aspekte des Geräts, darunter Lese-/Schreibzugriffsrechte für Geräteeigenschaften und Ausführungsrechte für Gerätebefehle. In den vorkonfigurierten Lösungen wird dieser Zugriff mithilfe von Azure Active Directory (AAD) im Geräteverwaltungsportal gesteuert. Sie können rollenbasierte Zugriffssicherheit durch erweiterte Verwendung von AAD in der vorkonfigurierten Lösung aktivieren.

## Remotesteuerung

Remotesteuerung ist kein gültiges Szenario für vorkonfigurierte Azure IoT Suite-Lösungen. Im Folgenden finden Sie jedoch eine allgemeine Analyse der Optionen zum Aktivieren der Remotesteuerung in Ihrer IoT-Lösung.

In IT-Szenarien wird die Remotesteuerung häufig zur Unterstützung von Remotebenutzern oder zur Remotekonfiguration von Remoteservern verwendet. In IoT-Szenarien haben die meisten Geräte keine spezifischen Benutzer, daher wird die Remotesteuerung in Remotekonfigurations- und Diagnoseszenarien verwendet. Die Remotesteuerung kann mit zwei verschiedenen Modellen implementiert werden:

* **Direkte Verbindung**: Um die Remotesteuerung über eine direkte Verbindung mit einem Gerät zu aktivieren (z. B. SSH unter Linux, Remotedesktop unter Windows oder über Tools zum Remotedebuggen), müssen Sie eine Verbindung mit dem Gerät herstellen können. Aufgrund des Sicherheitsrisikos für ein Gerät, das über das offene Internet erreichbar ist, wird empfohlen, einen Relaydienst (wie den Azure [Service Bus Relay-Dienst](service-bus-relay-overview.md)) zu verwenden, um die Verbindung und den Datenverkehr zum und vom Gerät zu ermöglichen. Da eine Relayverbindung eine ausgehende Verbindung vom Gerät ist, lässt sich damit die Angriffsfläche von offenen TCP-Ports auf dem Gerät beschränken.

* **Gerätebefehl**: Die Remotesteuerung über Gerätebefehle nutzt die vorhandene Verbindung und den Kommunikationskanal zwischen dem Gerät und Azure IoT Hub. Um die Remotesteuerung basierend auf Gerätebefehlen zu aktivieren, müssen Sie die folgenden Anforderungen beachten:
  * Die Software, die auf dem Gerät ausgeführt wird, muss den IoT-Dienst darüber informieren, dass die Gerätebefehle auf dem Gerät verfügbar sind. Dies wird in der Regel als Teil des Gerätemodells definiert.
  * Die Software, die auf dem Gerät ausgeführt wird, muss die Remotesteuerungsbefehle implementieren. Diese Gerätebefehle sollten einem Anforderungsmuster (vom IoT-Dienst zum Gerät) und einem Antwortmuster (vom Gerät zum IoT-Dienst) folgen. Das Ausführen von Gerätebefehlen kann sich auf den Gerätestatus auswirken; dieser neue Status muss in der Geräteregistrierung aktualisiert werden.

Wenn das Gerät der Masterspeicher für Gerätekonfiguration und -status ist, verwendet die Geräteregistrierung ein letztendlich konsistentes Modell. Änderungen am Gerätestatus müssen mittels Push an die Geräteregistrierung übertragen werden. Die Aktualisierung des Geräteregistrierungsstatus kann durch eine Anforderung vom IoT-Dienst an das Gerät erzwungen werden; alternativ kann das Gerät den Dienst automatisch aktualisieren, wenn eine Änderung des Systemstatus erkannt wird. Eine automatische Aktualisierung des Diensts vom Gerät generiert möglicherweise starken Netzwerkverkehr und steigert womöglich den Einsatz des Geräteprozessors und der verfügbaren Leistung.

## Remoteverwaltung und -überwachung

Da die meisten IoT-Geräte zwischen Betriebs- und Verwaltungsbenutzerrollen unterscheiden, können Administratoren über die Remoteverwaltung den Status der Geräte überwachen, den Datenfluss von Geräten an Geschäftsprozesse und Anwendungen (z. B. CRM, ERP, Wartungssysteme usw.) konfigurieren und den Status oder die Konfiguration von Geräten mithilfe von Gerätebefehlen remote aktualisieren.

Die vorkonfigurierten Azure IoT Suite-Lösungen bieten eine Azure-Website, die einen Ausgangspunkt für die Geräteverwaltung darstellt und die Sie für Szenarien in der vertikalen IoT-Lösung erweitern können.

Administratoren definieren die Grenzen der Geräteintegrität als Funktion der Gerätebetriebsdaten (schnell) und der Gerätemetadaten (langsam). Sie können zum Aktivieren des Geräteintegritäts-Warnsystem Regeln verwenden, die im Gegensatz zu einer Abfragestrategie über ein Datenstromverarbeitungsmodul (z. B. [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)) implementiert werden.

## Remotekonfiguration

Es kann für verschiedene Phasen des Lebenszyklus eines Geräts wichtig sein, die Gerätekonfiguration remote zu ändern: Bereitstellung, Diagnose und Integration in Geschäftsprozesse. Änderungen im Rahmen der Remotekonfiguration werden über eine Kombination aus Gerätemodell sowie der Möglichkeit des IoT-Diensts, den Status der Instanz eines Gerätemodells remote zu aktualisieren, ermöglicht.

In der vorkonfigurierten Remoteüberwachungslösung beschreibt das Gerät die Tatsache, dass es die folgenden Befehle für die Remotekonfiguration unterstützt:

* ChangeKey
* ChangeConfig
* ChangeSystemProperties

Diese Gerätebefehle werden im Geräteverwaltungsportal für die vorkonfigurierte Lösung nicht als verfügbare Gerätbefehle angezeigt, da es sich um Gerätebefehle handelt, die remote von bestimmten Teilen des Portals ausgeführt werden. Sobald ein Gerätebefehl vom Gerät empfangen wird, wird eine Bestätigung vom Gerät an den Dienst gesendet. Nach der Verarbeitung des Gerätebefehls sendet das Gerät ein Ergebnis, mit dem der Dienst darüber informiert wird, dass die Ausführung des Gerätebefehls erfolgreich war (oder mit einem Fehlercode abgeschlossen wurde). Zu diesem Zeitpunkt wird der gewünschte Status für das Gerät auf dem Gerät bestätigt, und die Geräteregistrierung wird aktualisiert.

## Remoteaktualisierung von Firmware und Software

Die Remoteaktualisierung von Firmware und Software ist eine wichtige Funktion eines IoT-Systems. Durch Updates wird auf Geräten die aktuellste und sicherste Software ausgeführt. Die Remoteaktualisierung von Firmware und Software auf einem Gerät ist ein Beispiel für einen verteilten, lang andauernden Prozess, der in der Regel Geschäftsprozesse umfasst. Für das Aktualisieren der Firmware auf einem Gerät, das eine Hochdruckbetankungspumpe steuert, sind möglicherweise zusätzliche Schritte in angrenzenden Systemen erforderlich, um den Treibstoff umzuleiten, während das Update ausgeführt und überprüft wird.

### Eine Analyse von Firmwareupdates

Im folgenden Beispiel wird eine Möglichkeit zum Implementieren von Firmwareupdates beschrieben, die u. U. Ihren Geschäftsanforderungen entspricht. Ziel dieses Beispiels ist die Darstellung der verschiedenen Prozessaspekte; es geht nicht darum, eine bestimmte Implementierung zu erzwingen. Der Entwurf Ihrer Updateanforderungen unter Berücksichtigung vieler geschäftlicher und technischer Faktoren würde den Rahmen dieses Artikels sprengen.

Geräteupdates werden im IoT-Dienst initiiert, und die Geräte werden zu einem bestimmten Zeitpunkt über einen Gerätebefehl informiert. Wenn ein Gerät die Remoteaktualisierung der Firmware oder Software explizit unterstützt, sollte der IoT-Dienst die Updatebefehle basierend auf definierten Geschäftsprozessen und Richtlinien übermitteln. Nach dem Empfang des Gerätebefehls zum Aktualisieren muss das Gerät das Updatepaket herunterladen und bereitstellen, die neu bereitgestellte Konfiguration neu starten (im Fall von Firmwareupdates) oder das neue Softwarepaket starten und überprüfen, ob die neue Firmware und Software wie erwartet ausgeführt wird. Beim Durchlaufen dieses mehrschrittigen Prozesses muss das Gerät den IoT-Dienst über den Status des Geräts informieren.

Ein Speicherdienst wie Azure Storage oder ein Content Delivery Network (CDN) kann das Updatepaket bereitstellen. Es ist wichtig, die Integrität des heruntergeladenen Pakets vor dem Anwenden des Firmware- oder Softwareupdates zu überprüfen, um sicherzustellen, dass das Paket von der erwarteten Quelle stammt.

Nach Abschluss der Aktualisierung einer Firmware, muss das Gerät in der Lage sein, einen funktionierenden Zustand zu überprüfen und zu identifizieren. Wenn das Gerät nicht erfolgreich in den funktionierenden Zustand wechselt, sollte die Software auf dem Gerät ein Rollback auf einen als funktionierend bekannten Zustand initiieren. Der bekannte funktionierende Zustand könnte der letzte als funktionierend bekannte Zustand sein oder ein als *Golden State* bekanntes Gerätefirmware-Image, das in einer Speicherpartition gespeichert wird.

## Nächste Schritte

Weitere Informationen zu Azure IoT Hub finden Sie unter den folgenden Links:

* [Erste Schritte mit IoT Hub][]
* [Was ist Azure IoT Hub?][]
* [Ihr Gerät verbinden][]

[Erste Schritte mit IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Was ist Azure IoT Hub?]: iot-hub-what-is-iot-hub.md
[Ihr Gerät verbinden]: https://azure.microsoft.com/develop/iot/

<!---HONumber=Nov15_HO3-->