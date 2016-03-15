# Azure und Internet der Dinge

Willkommen bei Microsoft Azure und dem Internet der Dinge (IoT). In diesem Artikel wird eine IoT-Lösungsarchitektur vorgestellt, die die allgemeinen Merkmale einer IoT-Lösung beschreibt, die Sie mithilfe von Azure-Diensten bereitstellen können. Für IoT-Lösungen ist eine sichere, bidirektionale Kommunikation zwischen Geräten – in einigen Fällen Millionen – und einem Lösungs-Back-End erforderlich, das beispielsweise automatisierte Predictive Analytics-Methoden verwendet, um Erkenntnisse aus dem D2C-Ereignisdatenstrom (Device-to-Cloud) zu gewinnen.

Azure IoT Hub ist ein wichtiger Baustein, wenn Sie diese IoT-Lösungsarchitektur mithilfe von Azure-Diensten implementieren. IoT Suite stellt vollständige und umfassende Implementierungen dieser Architektur für bestimmte IoT-Szenarios bereit, wie z. B. *Remoteüberwachung* und *vorbeugende Wartung*.

## Architektur einer IoT-Lösung

Das folgende Diagramm zeigt die Architektur einer typischen IoT-Lösung. Es enthält keine Namen von bestimmten Azure-Diensten, sondern beschreibt die wichtigsten Elemente in der Architektur einer generischen IoT-Lösung. In dieser Architektur erfassen IoT-Geräte Daten, die sie an ein Cloud-Gateway senden. Das Cloud-Gateway macht die zur Verarbeitung verfügbaren Daten von anderen Back-End-Diensten verfügbar, aus denen Daten an andere Line-of-Business-Anwendung oder über ein Dashboard oder ein anderes Präsentationsgerät an Bedienpersonal übermittelt werden.

![Architektur einer IoT-Lösung][img-solution-architecture]

### Gerätekonnektivität

In dieser IoT-Lösungsarchitektur senden Geräte Telemetriedaten wie Temperaturwerte an einen Cloudendpunkt, wo sie gespeichert und verarbeitet werden. Geräte können auch Cloud-zu-Gerät-Befehle (Cloud-to-Device, C2D) empfangen und darauf reagieren, indem Nachrichten von einem Cloudendpunkt gelesen werden. Ein Gerät kann z. B. einen Befehl mit der Anweisung erhalten, die Frequenz zu ändern, mit der Daten gemessen werden.

Eines der größten Probleme im Zusammenhang mit IoT-Projekten ist die sichere und zuverlässige Verbindung von Geräten mit dem Lösungs-Back-End. IoT-Geräte weisen andere Merkmale als andere Clients wie Browser und mobile Apps auf. IoT-Geräte:

- Es handelt sich häufig um eingebettete Systeme ohne menschlichen Bediener.
- Sie können sich an Remotestandorten befinden, an denen der physische Zugriff sehr teuer ist.
- Sie sind unter Umständen nur über das Back-End der Lösung erreichbar. Es gibt keine andere Möglichkeit zur Interaktion mit dem Gerät.
- Sie verfügen ggf. über eingeschränkte Stromversorgungs- und Verarbeitungsressourcen.
- Sie verfügen ggf. über intermittierende, langsame oder teure Netzwerkverbindungen.
- Sie müssen unter Umständen proprietäre, benutzerdefinierte oder branchenspezifische Anwendungsprotokolle verwenden.
- Sie können mit vielen gängigen Hardware- und Softwareplattformen erstellt werden.

Zusätzlich zu den obigen Anforderungen muss jede IoT-Lösung auch Skalierbarkeit, Sicherheit und Zuverlässigkeit bieten. Die sich ergebenden Verbindungsanforderungen sind schwierig und zeitaufwändig zu implementieren, wenn herkömmliche Technologie verwendet wird, z. B. Webcontainer und Nachrichtenbroker. Azure IoT Hub und die IoT-Geräte-SDKs vereinfachen die Implementierung von Lösungen, die diese Anforderungen erfüllen.

Ein Gerät kann direkt mit dem Endpunkt eines Cloudgateways kommunizieren. Falls das Gerät keines der vom Cloudgateway unterstützten Kommunikationsprotokolle nutzen kann, kann es eine Verbindung über ein Zwischengateway herstellen, das die Protokollübersetzung bereitstellt, z. B. das [IoT Hub-Protokollgateway][lnk-protocol-gateway]. Die

### Datenverarbeitung und Analysen

In der Cloud erfolgt auf dem Back-End der IoT-Lösung ein Großteil der Datenverarbeitung in der Lösung. Dies betrifft insbesondere das Filtern und Aggregieren von Telemetriedaten und deren Weiterleitung an andere Dienste. Das IoT-Lösungs-Back-End:

- Es empfängt enorme Mengen an Telemetriedaten von Ihren Geräten und bestimmt, wie diese Daten verarbeitet und gespeichert werden sollen. 
- Es ermöglicht unter Umständen das Senden von Befehlen aus der Cloud an ein bestimmtes Gerät.
- Das Back-End bietet Registrierungsfunktionen, mit denen Sie Geräte bereitstellen und steuern können, welche Geräte eine Verbindung mit Ihrer Infrastruktur herstellen dürfen.
- Mit dem Back-End können Sie den Status Ihrer Geräte nachverfolgen und deren Aktivitäten überwachen.

IoT-Lösungen können automatische Feedback-Schleifen enthalten. Beispielsweise kann ein Analysemodul im Back-End aus den Telemetriedaten ermitteln, dass die Temperatur eines bestimmten Geräts über der normalen Betriebstemperatur liegt, und einen Befehl an das Gerät senden, damit korrigierende Maßnahmen ergriffen werden.

### Präsentations- und Geschäftskonnektivität

Die Präsentations- und Geschäftskonnektivitätsebene ermöglicht Endbenutzern die Interaktion mit der IoT-Lösung und den Geräten. Damit können die Benutzer die auf den Geräten erfassten Daten anzeigen und analysieren. Diese Darstellungen können in Form von Dashboards oder BI-Berichten erfolgen. Ein Bediener kann z. B. den Status eines bestimmten Zustell-Lkws überprüfen und alle Warnungen anzeigen, die vom System ausgelöst wurden. Diese Ebene ermöglicht auch die Integration des IoT-Lösungs-Back-Ends in vorhandene Line-of-Business-Anwendungen, um es in Geschäftsprozesse oder Workflows im Unternehmen einzubinden.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png

[lnk-machinelearning]: http://azure.microsoft.com/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]: iot-hub-protocol-gateway.md

<!---HONumber=AcomDC_0309_2016-->