# Azure und Internet der Dinge

Willkommen bei Microsoft Azure und dem Internet der Dinge (IoT). In diesem Artikel wird eine typische IoT-Lösungsarchitektur vorgestellt, die die allgemeinen Merkmale einer IoT-Lösung beschreibt, die Sie mithilfe von Azure-Diensten bereitstellen können. Für eine typische IoT-Lösung ist eine sichere, bidirektionale Kommunikation zwischen – in einigen Fällen Millionen – Geräten und einem Lösungs-Back-End erforderlich, das beispielsweise automatisierte Predictive Analytics-Methoden verwendet, um Erkenntnisse aus dem D2C-Ereignisdatenstrom (Device-to-Cloud) zu gewinnen.

## Architektur einer IoT-Lösung

Das folgende Diagramm zeigt die Architektur einer typischen IoT-Lösung. Es enthält keine Namen von bestimmten Azure-Diensten, sondern beschreibt die wichtigsten Elemente in der Architektur einer generischen IoT-Lösung. In den folgenden Abschnitten enthalten Sie Informationen zu den Elementen in dieser Lösung.

![Architektur einer IoT-Lösung][img-solution-architecture]

### Gerätekonnektivität

In einem typischen IoT-Szenario senden Geräte Telemetriedaten wie Temperaturwerte an einen Cloudendpunkt, wo sie gespeichert und verarbeitet werden. Geräte können auch Cloud-zu-Gerät-Befehle (Cloud-to-Device, C2D) empfangen und darauf reagieren, indem Nachrichten von einem Cloudendpunkt gelesen werden. Ein Gerät kann z. B. einen Befehl mit der Anweisung erhalten, die Frequenz zu ändern, mit der Daten gemessen werden.

Eines der größten Probleme im Zusammenhang mit IoT-Projekten ist die sichere und zuverlässige Verbindung von Geräten mit dem Lösungs-Back-End. IoT-Geräte weisen gegenüber anderen Clients, z. B. Browsern und mobilen Apps, in der Regel andere Merkmale auf: IoT-Geräte:

- Es handelt sich häufig um eingebettete Systeme ohne menschlichen Bediener.
- Sie können sich an Remotestandorten befinden, an denen der physische Zugriff sehr teuer ist.
- Sie sind unter Umständen nur über das Back-End der Lösung erreichbar.
- Sie verfügen ggf. über eingeschränkte Stromversorgungs- und Verarbeitungsressourcen.
- Sie verfügen ggf. über intermittierende, langsame oder teure Netzwerkverbindungen.
- Sie müssen unter Umständen proprietäre, benutzerdefinierte oder branchenspezifische Anwendungsprotokolle verwenden.
- Sie können mit vielen gängigen Hardware- und Softwareplattformen erstellt werden.

Zusätzlich zu den obigen Anforderungen muss jede IoT-Lösung auch Skalierbarkeit, Sicherheit und Zuverlässigkeit bieten. Die sich ergebenden Verbindungsanforderungen sind schwierig und zeitaufwändig zu implementieren, wenn herkömmliche Technologie verwendet wird, z. B. Webcontainer und Nachrichtenbroker.

Ein Gerät kann direkt mit dem Endpunkt eines Cloud-Gateways kommunizieren. Falls das Gerät keines der vom Cloud-Gateway unterstützten Kommunikationsprotokolle nutzen kann, kann es eine Verbindung über ein Zwischengateway herstellen, das die Protokollübersetzung bereitstellt.

### Datenverarbeitung und Analysen

In der Cloud übernimmt ein IoT-Lösungs-Back-End folgende Funktionen:

- Es empfängt enorme Mengen an Telemetriedaten von Ihren Geräten und bestimmt, wie diese Daten verarbeitet und gespeichert werden sollen. 
- Es ermöglicht unter Umständen auch das Senden von Befehlen aus der Cloud an ein bestimmtes Gerät.
- Das Back-End bietet Registrierungsfunktionen, mit denen Sie Geräte bereitstellen und steuern können, welche Geräte eine Verbindung mit Ihrer Infrastruktur herstellen dürfen.
- Mit dem Back-End können Sie den Status Ihrer Geräte nachverfolgen und deren Aktivitäten überwachen.

IoT-Lösungen können automatische Feedback-Schleifen enthalten. Beispielsweise kann ein Machine Learning-Modul im Back-End aus den Telemetriedaten ermitteln, dass die Temperatur eines bestimmten Geräts über der normalen Betriebstemperatur liegt, und einen Befehl an das Gerät senden, damit korrigierende Maßnahmen ergriffen werden.

### Präsentation

Mit vielen IoT-Lösungen können die Benutzer die auf den Geräten erfassten Daten anzeigen und analysieren. Diese Darstellungen können in Form von Dashboards oder BI-Berichten erfolgen.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png

[lnk-machinelearning]: http://azure.microsoft.com/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot

<!---HONumber=AcomDC_0218_2016-->