<properties
 pageTitle="IoT-Geräteverwaltung | Microsoft Azure"
 description="Übersicht über die Verwaltung von IoT-Geräten mit IoT Hub und IoT Suite"
 services="iot-hub,iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="01/21/2016"
 ms.author="dobett"/>

# IoT-Geräteverwaltung mithilfe der Azure IoT Suite und Azure IoT Hub

Azure IoT Suite und Azure IoT Hub bieten grundlegende Funktionen, die die Geräteverwaltung für umfangreiche IoT-Lösungen und für verschiedene Geräte und Gerätetopologien ermöglichen. Geräteverwaltung bezieht sich in diesem Artikel ausdrücklich auf die IoT-Geräteverwaltung.

## Einführung

Dienstanbieter und Unternehmen oder Organisationen, die IoT-Gerätebestände verwalten, nutzen die Geräteverwaltungsfunktionen, um die folgenden Geschäftsprozesse durchzuführen:

* Registrierung und Suche von IoT-Geräten.
* Herstellung von Konnektivität.
* Remotekonfiguration und -aktualisierung von Software auf Geräten. Beim Verwalten von Geräten in Fertigungsstätten oder auf Ölfeldern gibt es z. B. Richtlinien für die Remotekonfiguration und -aktualisierung von Geräten, bei denen Bewilligungsketten, behördliche Auditauflagen, das Vorhandensein von physischen Sicherheitsvorkehrungen und mehr berücksichtigt werden müssen.

Wenn Sie die IoT-Geräteverwaltung für Ihre IoT-Lösung aktivieren, sollten Sie die folgenden Funktionen berücksichtigen und deren Bedeutung im Hinblick auf Ihre Geschäftsziele festlegen:

* **[Gerätebereitstellung und -erkennung](#device-provisioning-and-discovery)**: Prozess, mit dem ein Gerät im System registriert wird.
* **[Geräteregistrierung und -modelle](#device-registry-and-device-models)**: Methode, wie die Gerätemodelle die strukturierte Verwendung von Metadaten für Gerätebeziehungen, Rollen im System und Überprüfungsmethoden abbilden.
* **[Gerätezugriffsverwaltung](#device-access-management)**: Methode, wie Geräte den Zugriff auf Geräteressourcen von Clouddiensten steuern.
* **[Remotesteuerung](#remote-control)**: Methode, wie Remotebenutzer Zugriff auf Geräte erhalten und Geräteänderungen anweisen.
* **[Remoteverwaltung](#remote-administration-and-monitoring)**: Prozess, mit dem ein Administrator die Integrität des Gerätebestands definiert.  
* **[Remotekonfiguration](#remote-configuration)**: Methode für Administratoren zum Ändern der Gerätekonfiguration.
* **[Remoteaktualisierung von Firmware und Software](#remote-firmware-and-software-update)**: Prozess, mit dem Administratoren die Gerätefirmware und -software aktualisieren können.

Die folgenden Abschnitte bieten einen tiefer reichenden Einblick in die einzelnen Geräteverwaltungsfunktionen sowie ein übergeordnetes Modell für die Implementierung dieser Funktionen mithilfe von Azure IoT Hub.

## Gerätebereitstellung und -suche

Sie stellen ein Gerät mit Azure IoT Hub über die Registrierungs-API bereit. Nachdem Sie Ihr Gerät registriert und einen Schlüssel bereitgestellt oder erhalten haben, können Sie Ihr Gerät aktivieren, damit mit diesem Schlüssel eine Verbindung mit IoT Hub hergestellt werden kann. Azure IoT Hub kommuniziert nur mit registrierten Geräten, die über autorisierte Anmeldeinformationen verfügen. Administratoren können den Gerätezugriff für Azure IoT Hub über das Geräteverwaltungsportal deaktivieren.

Je nachdem, wie die IoT-Geräte hergestellt, bereitgestellt und implementiert wurden, können Sie einen Bootstrap-Prozess verwenden. Sie können einen Bootstrapdienst als Teil Ihrer Lösung erstellen, um einfache Konnektivität zu unterstützen und den Prozess der Zuweisung eines Geräts zu einem bestimmten IoT Hub zu verzögern. Der Ort, an dem das Gerät eingesetzt wird, kann bei der Herstellung des Geräts unbekannt sein. Dies ist nur ein Beispiel von vielen potenziell komplexen Workflows, mit denen Azure IoT Hub auf ein Gerät hingewiesen werden kann. Darüber hinaus lässt es sich in bestehende Geschäftsprozesse integrieren.

Wenn Sie einen Bootstrapdienst verwenden, startet ein IoT-Gerät und gibt eine Anforderung aus, die letztendlich den Zugriff auf einen zugewiesenen Azure IoT Hub ermöglichen kann. Die Anforderung sollte Bootstrapanmeldeinformationen des Geräts und alle anderen erforderlichen Daten enthalten. Bei autorisierten Geräten sollte der Bootstrapdienst das Gerät bei einem zugewiesenen Azure IoT Hub registrieren und dem Gerät, das einen Bootstrap anfordert, Konnektivitätsdetails bereitstellen. IoT Hub stellt dem Gerät, das einen Bootstrap anfordert, Konnektivitätsdetails zur Verfügung.

## Geräteregistrierung und Gerätemodelle

Der Begriff *Gerätemodell* bezieht sich auf das Modell, das Eigenschaften enthält, die von einem Clouddienst gelesen oder geschrieben werden können. Es umfasst auch Gerätebefehle, die ein Clouddienst remote auf einem Gerät ausführen kann. In dem dienstorientierten Modell, das im nächsten Abschnitt beschrieben wird, ist dem Gerät das Modell nicht bekannt, allerdings können Clouddienste weiterhin Metadaten für Geräte nachverfolgen und verwenden.

Das Speichern von Geräteinformationen und zugehörigen Metadaten ist maßgeblich für das IoT-System und für die Rolle der Geräteregistrierung. Dies gilt besonders für ältere Geräte, die nicht geändert werden können, oder für Geräte, die kein Gerätemodell verwenden. Ein dienstorientiertes Modell kann einen IoT-Dienst für die Interaktion mit einem Gerätebestand aktivieren. Wenn Geräte ein definiertes Modell verwenden, dient die Geräteregistrierung als eine konsistente Ansicht der Gerätedaten, bei denen das Gerät als Master fungiert. In diesem Fall informiert der Dienst das Gerät über die gewünschten Änderungen, die erst gültig werden, nachdem das Gerät die Änderung bestätigt hat.

### Dienstorientiertes Modell

Wenn ein Gerät eine Verbindung mit Azure IoT Hub herstellt und kein Gerätemodell angibt, muss unbedingt eine Geräteregistrierung implementiert werden, mit der registrierte Geräte und die zugehörigen Metadaten nachverfolgt werden. In diesem Fall ist die Geräteregistrierung der einzige Speicherort für die Gerätemetadaten. Beispiele für die Gerätemetadaten, die Sie nachverfolgen sollten, sind die logische Topologie (z. B. 4. Etage, Gebäude 109), die Gerätefunktion (z. B. Türsensor) oder andere Taginformationen.

### Geräteorientiertes Modell

Um Ihrer IoT-Lösung zu ermöglichen, die Funktionen Ihres IoT-Geräts zu nutzen, müssen sich die Geräte selbst für die Cloud beschreiben, nachdem sie eine Verbindung mit IoT Hub hergestellt haben. Im Folgenden finden Sie zwei Typen von Gerätemodellen, die Sie in einer IoT-Lösung verwenden können:

#### Selbstdefiniertes Gerätemodell

Ein Geräteingenieur (oder Entwickler, der einen Gerätesimulator verwendet) nutzt das selbstdefinierte Gerätemodell, indem die Funktionen des Geräts bei dessen Herstellung durchlaufen werden. Ein Geräteentwickler kann zunächst ein Gerät mit wenigen Eigenschaften und unterstützten Befehlen erstellen und dann später weitere hinzufügen. Dieser Geräteingenieur könnte aber auch über viele Geräte verfügen, die jeweils einzigartige Funktionen aufweisen und das selbstdefinierte Modell verwenden. In diesem Fall muss der Geräteingenieur die Struktur des Gerätemodells nicht registrieren. Beträchtliche Unterschiede bei selbstdefinierten Modellen stellen möglicherweise bei der Skalierung auf viele Geräten eine Herausforderung dar.

#### Vordefiniertes Gerätemodell

Eine IoT-Produktionsbereitstellung, bei der Netzwerk- und Leistungs-/Verarbeitungseinschränkungen vorliegen, profitiert maßgeblich von einem vordefinierten Gerätemodell, bei dem das Gerät mit minimalen Verarbeitungs- und Leistungsressourcen auskommt. Auf ähnliche Weise ermöglicht ein minimaler Netzwerkdatenverkehr es den Geräten, Übertragungen über heterogene Netzwerke (WLAN, 2G/3G/4G, BLE, Sat usw.) durchzuführen, besonders bei einer eingeschränkten und teuren Infrastruktur (z. B. Satelliten). Bei der Implementierung eines vordefinierten Gerätemodells kann ein Geräteingenieur Geräteinformationen senden, die als ein oder zwei Bytes codiert sind und die als Schlüssel für das vordefinierte Gerätemodell fungieren. Die Übersichtlichkeit dieses Ansatzes führt im Vergleich zum selbstdefinierten Gerätemodell zu gesteigerter Effizienz von einer oder zwei Größenordnungen.

### Die vorkonfigurierte Lösung zur Remoteüberwachung und das Gerätemodell

Die vorkonfigurierte Azure IoT Suite-Lösung zur Remoteüberwachung implementiert ein selbstdefiniertes Gerätemodell. Mit diesem Modell ist bei der Definition und Entwicklung von Funktionen des Geräts ein schnelles Durchlaufen möglich.

Sie finden den Quellcode für diese vorkonfigurierte Lösung im GitHub-Repository [azure-iot-solution][lnk-azure-iot-solution].

Der Code, mit dem die vorkonfigurierte Lösung zur Remoteüberwachung ein Geräteobjekt erstellt und dann an den Dienst sendet, ist in der Datei **Simulator/Simulator.WorkerRole/SimulatorCore/Devices/DeviceBase.cs** enthalten. Bei den Methoden **SendDeviceInfo** und **GetDeviceInfo** können Sie sehen, wie das selbstbeschreibende Gerätemodell erstellt und an den Azure IoT Hub gesendet wird.

Der Code, mit dem der Clouddienst die mit dem Gerätemodell verbundenen Ereignisse verarbeitet, befindet sich in der Datei **EventProcessor/EventProcessor.WorkerRole/Processors/DeviceManagementProcessor.cs**. Die Hauptarbeit in Zusammenhang mit Reaktionen auf die mit dem Gerätemodell verbundenen Nachrichten, die ein Gerät an die Dienstseite der vorkonfigurierten Lösung sendet, erfolgt in der **ProcessJToken**-Methode.

Sobald eine mit dem Gerätemodell verbundene Nachricht empfangen wird, sind die Methoden **UpdateDeviceFromSimulatedDeviceInfoPacketAsync** und **UpdateDeviceFromRealDeviceInfoPacketAsync** in der Datei **DeviceManagement/Infrastructure/BusinessLogic/DeviceLogic.cs** für das Aktualisieren der Geräteregistrierung zuständig. Die Geräteregistrierungs-APIs in der vorkonfigurierten Lösung zur Remoteüberwachung finden Sie in der Datei **DeviceManagement/Web/WebApiControllers/DeviceApiController.cs**.

### Bereichsgateway-Gerätemodelle

Bereichsgateways werden häufig zur Aktivierung der Konnektivität und zur Protokollierung für Geräte verwendet, die sich nicht direkt mit dem Internet verbinden können oder sollen. Wenn das Gerät, das Sie erstellen, ein Bereichsgateway ist, kann es die Geräte darstellen, die bei allen Interaktionen über das Bereichsgateway mit Azure IoT Hub verbunden sind. Als Hersteller des Bereichsgateways ist es Ihre Aufgabe, die Übersetzung zwischen den Geräteprotokollen und den Protokollen, die vom IoT-Dienst unterstützt werden, zu implementieren. Wenn das Bereichsgateway eine Verbindung mit BLE-Geräten (Bluetooth Low Energy) ermöglichen soll, müssen Sie die BLE-Schnittstelle für Geräte und die Schnittstelle mit Azure IoT Hub implementieren.

## Gerätezugriffsverwaltung

Die vorkonfigurierte IoT Suite-Lösung steuert den Zugriff auf verschiedene Aspekte des Geräts, darunter Lese-/Schreibzugriffsrechte für Geräteeigenschaften und Ausführungsrechte für Gerätebefehle. In den vorkonfigurierten Lösungen wird dieser Zugriff mithilfe von Azure Active Directory (AAD) im Geräteverwaltungsportal gesteuert. Sie können rollenbasierte Zugriffssicherheit durch erweiterte Verwendung von AAD in der vorkonfigurierten Lösung aktivieren.

## Remotesteuerung

Remotesteuerung ist kein gültiges Szenario für vorkonfigurierte Azure IoT Suite-Lösungen. Im Folgenden finden Sie jedoch eine allgemeine Analyse der Optionen zum Aktivieren der Remotesteuerung in Ihrer IoT-Lösung.

In IT-Szenarien wird die Remotesteuerung häufig zur Unterstützung von Remotebenutzern oder zur Remotekonfiguration von Remoteservern verwendet. In IoT-Szenarien haben die meisten Geräte keine spezifischen Benutzer, daher wird die Remotesteuerung in Remotekonfigurations- und Diagnoseszenarien verwendet. Die Remotesteuerung kann mit zwei verschiedenen Modellen implementiert werden:

* **Direktverbindung**: Um die Remotesteuerung über eine Direktverbindung mit einem Gerät zu aktivieren (z. B. SSH unter Linux, Remotedesktop unter Windows oder über Tools zum Remotedebuggen), müssen Sie eine Verbindung mit dem Gerät herstellen können. Aufgrund des Sicherheitsrisikos für ein Gerät, das über das offene Internet erreichbar ist, wird empfohlen, einen Relaydienst (wie den Azure [Service Bus Relay-Dienst][service-bus-relay]) zu verwenden, um die Verbindung zu ermöglichen und Datenverkehr weiterzuleiten. Da eine Relayverbindung eine ausgehende Verbindung vom Gerät ist, lässt sich damit die Angriffsfläche von offenen TCP-Ports auf dem Gerät beschränken.

* **Gerätebefehl**: Die Remotesteuerung über Gerätebefehle nutzt die vorhandene Verbindung und den Kommunikationskanal zwischen dem Gerät und Azure IoT Hub. Um die Remotesteuerung basierend auf Gerätebefehlen zu aktivieren, müssen Sie die folgenden Anforderungen beachten:
  * Die Software, die auf dem Gerät ausgeführt wird, muss den IoT-Dienst darüber informieren, dass die Gerätebefehle auf dem Gerät verfügbar sind. Dies wird in der Regel als Teil des Gerätemodells definiert.
  * Die Software, die auf dem Gerät ausgeführt wird, muss die Remotesteuerungsbefehle implementieren. Diese Gerätebefehle sollten dem Anforderungsmuster (vom IoT-Dienst zum Gerät) und Antwortmuster (vom Gerät zum IoT-Dienst) folgen. Das Ausführen von Gerätebefehlen kann sich auf den Gerätestatus auswirken. Deshalb muss dieser neue Status in der Geräteregistrierung aktualisiert werden.

Wenn das Gerät der Hauptspeicher für Gerätekonfiguration und -status ist, verwendet die Geräteregistrierung ein letztendlich konsistentes Modell. Änderungen am Gerätestatus müssen mittels Push an die Geräteregistrierung übertragen werden. Die Aktualisierung des Geräteregistrierungsstatus kann durch eine Anforderung vom IoT-Dienst an das Gerät erzwungen werden. Alternativ kann das Gerät den Dienst automatisch aktualisieren, wenn eine Änderung des Systemstatus erkannt wird. Eine automatische Aktualisierung des Diensts vom Gerät generiert möglicherweise starken Netzwerkdatenverkehr und steigert womöglich den Einsatz des Geräteprozessors und der verfügbaren Leistung.

## Remoteverwaltung und -überwachung

Die meisten IoT-Geräte unterscheiden zwischen Benutzerrollen für Betrieb und Verwaltung. Administratoren können über die Remoteverwaltung den Status der Geräte überwachen, den Datenfluss von Geräten an Geschäftsprozesse und Anwendungen (z. B. CRM, ERP, Wartungssysteme usw.) konfigurieren und den Status oder die Konfiguration von Geräten mithilfe von Gerätebefehlen remote aktualisieren.

Die vorkonfigurierten Azure IoT Suite-Lösungen bieten eine Azure-Website, die einen Ausgangspunkt für die Geräteverwaltung darstellt und die Sie auf Szenarien in Ihrer vertikalen IoT-Lösung ausweiten können.

Administratoren können die Integrität von Geräten durch Untersuchen von Gerätebetriebsdaten (schnell) und Gerätemetadaten (langsam) bestimmen. Sie können zum Aktivieren eines Warnsystems zur Geräteintegrität Regeln verwenden, die im Gegensatz zu einer Abfragestrategie über ein Datenstromverarbeitungsmodul (z. B. [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)) implementiert werden.

## Remotekonfiguration

Es kann in verschiedenen Phasen des Lebenszyklus eines Geräts wichtig sein, die Gerätekonfiguration remote zu ändern: Bereitstellung, Diagnose und Integration in Geschäftsprozesse. Änderungen im Rahmen der Remotekonfiguration werden über eine Kombination aus Gerätemodell und der Fähigkeit des IoT-Diensts ermöglicht, den Status der Instanz eines Gerätemodells remote zu aktualisieren.

In der vorkonfigurierten Remoteüberwachungslösung beschreibt das Gerät, dass es die folgenden Befehle für die Remotekonfiguration unterstützt:

* ChangeKey
* ChangeConfig
* ChangeSystemProperties

Diese Gerätebefehle werden im Geräteverwaltungsportal für die vorkonfigurierte Lösung nicht als verfügbare Gerätebefehle angezeigt, da sie unter bestimmten Umständen vom Portal remote ausgeführt werden. Wenn ein Gerät einen Gerätebefehl empfängt, sendet es eine Bestätigungsantwort an den Dienst. Nach der Verarbeitung des Gerätebefehls sendet das Gerät ein Ergebnis, das den Dienst informiert, dass die Ausführung des Gerätebefehls erfolgreich war (oder mit einem Fehlercode nicht erfolgreich abgeschlossen wurde). Zu diesem Zeitpunkt wird der gewünschte Status für das Gerät auf dem Gerät bestätigt, und die Geräteregistrierung wird aktualisiert.

## Remoteaktualisierung von Firmware und Software

Die Remoteaktualisierung von Firmware und Software ist eine wichtige Funktion eines IoT-Systems. Durch Updates wird auf Geräten die aktuellste und sicherste Software ausgeführt. Die Remoteaktualisierung von Firmware und Software auf einem Gerät ist ein Beispiel für einen verteilten, lang andauernden Prozess, der in der Regel Interaktionen mit anderen Geschäftsprozessen umfasst. Für das Aktualisieren der Firmware auf einem Gerät, das eine Hochdruckbetankungspumpe steuert, sind möglicherweise zusätzliche Schritte in anderen Systemen erforderlich, um den Treibstoff umzuleiten, während das Update ausgeführt und überprüft wird.

### Eine Analyse von Firmwareupdates

Im folgenden Beispiel wird eine Möglichkeit zum Implementieren von Firmwareupdates beschrieben, die u. U. Ihren Geschäftsanforderungen entspricht. Ziel dieses Beispiels ist die Darstellung der verschiedenen Prozessaspekte. Es geht nicht darum, eine bestimmte Implementierung zu erzwingen. Der Entwurf Ihrer Updateanforderungen unter Berücksichtigung vieler geschäftlicher und technischer Faktoren würde den Rahmen dieses Artikels sprengen.

Geräteupdates werden im IoT-Dienst ausgelöst, wobei Geräte zu einem bestimmten Zeitpunkt über einen Gerätebefehl informiert werden. Wenn ein Gerät Remoteupdates der Firmware oder Software explizit unterstützt, sollte der IoT-Dienst die Updatebefehle basierend auf definierten Geschäftsprozessen und Richtlinien übermitteln. Nach Empfang der Gerätebefehls zur Aktualisierung muss das Gerät die folgenden Aktionen ausführen:

1. Das Updatepaket herunterladen und bereitstellen
2. Die neu bereitgestellte Konfiguration (bei einem Firmwareupdate) neu starten oder das neue Softwarepaket starten
3. Überprüfen, ob die neue Firmware oder Software wie erwartet ausgeführt wird

Beim Durchlaufen dieses mehrschrittigen Prozesses muss das Gerät den IoT-Dienst über den Status des Geräts informieren.

Ein Speicherdienst wie Azure Storage oder ein Content Delivery Network (CDN) kann das Updatepaket bereitstellen. Es ist wichtig, die Integrität des heruntergeladenen Pakets vor dem Anwenden des Firmware- oder Softwareupdates zu überprüfen, um sicherzustellen, dass das Paket aus der erwarteten Quelle stammt.

Nach Abschluss der Aktualisierung einer Firmware, muss das Gerät in der Lage sein, einen funktionierenden Zustand zu überprüfen und zu identifizieren. Wenn das Gerät nicht erfolgreich in den funktionierenden Zustand wechselt, sollte die Software auf dem Gerät ein Rollback auf einen als funktionierend bekannten Zustand initiieren. Der bekannte funktionierende Zustand könnte der letzte als funktionierend bekannte Zustand sein oder ein als *Golden State* bezeichnetes Gerätefirmware-Image, das in einer Speicherpartition gespeichert wird.

## Nächste Schritte

Weitere Informationen zu Azure IoT Hub finden Sie unter den folgenden Links:

* [Erste Schritte mit IoT Hub][]
* [Was ist Azure IoT Hub?][]
* [Verbinden Ihres Geräts][]

[Erste Schritte mit IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Was ist Azure IoT Hub?]: iot-hub-what-is-iot-hub.md
[service-bus-relay]: ../service-bus/service-bus-relay-overview.md
[Verbinden Ihres Geräts]: https://azure.microsoft.com/develop/iot/
[lnk-azure-iot-solution]: https://github.com/Azure/azure-iot-solution

<!---HONumber=AcomDC_0121_2016-->