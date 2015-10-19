# Microsoft Azure und das Internet der Dinge (IoT)

Für eine typische IoT-Lösung ist eine sichere, bidirektionale Kommunikation zwischen – in einigen Fällen Millionen – Geräten und einem Anwendungs-Back-End erforderlich. Ein Beispiel für die Funktionen von Anwendungs-Back-Ends ist die Verarbeitung von Gerät-zu-Cloud-Ereignissen (Device-to-Cloud, D2C), um mithilfe von automatisierten und vorhersehenden Analysen Informationen zu erhalten.

Microsoft stellt eine Reihe von Bibliotheken bereit (mit Unterstützung für mehrere Sprachen und Hardware-Plattformen), mit denen Sie Clientanwendungen zum Ausführen auf einem IoT-Gerät entwickeln können. Zur Implementierung der IoT-Back-End-Anwendung können Sie mehrere Azure-Dienste kombinieren oder eine der vorkonfigurierten Lösungen verwenden, die über die [Azure IoT Suite][] erhältlich sind. Um besser zu verstehen, wie diese IoT-Infrastruktur von Azure unterstützt wird, sollten Sie die Architektur einer typischen IoT-Lösung betrachten.

## Architektur einer IoT-Lösung

Das folgende Diagramm zeigt die Architektur einer typischen IoT-Lösung. Es enthält keine Namen von bestimmten Azure-Diensten, sondern beschreibt die wichtigsten Elemente in der Architektur einer generischen IoT-Lösung. In den folgenden Abschnitten enthalten Sie Informationen zu den Elementen in dieser Lösung.

![Architektur einer IoT-Lösung][img-solution-architecture]

### Gerätekonnektivität

In einem typischen IoT-Szenario senden Geräte Gerät-zu-Cloud-Telemetriedaten (Device-to-Cloud, D2C) wie Temperaturwerte an einen Cloudendpunkt, wo sie gespeichert und verarbeitet werden. Geräte können auch Cloud-zu-Gerät-Befehle (Cloud-to-Device, C2D) empfangen und darauf reagieren, indem Nachrichten von einem Cloudendpunkt gelesen werden. Ein Gerät kann z. B. einen Befehl mit der Anweisung erhalten, die Frequenz zu ändern, mit der Daten gemessen werden.

Ein Gerät oder eine Datenquelle in einer IoT-Lösung kann aus einem einfachen Sensor mit Netzwerkverbindung oder aus einem leistungsfähigen, eigenständigen Computer bestehen. Verarbeitungskapazitäten, Arbeitsspeicher, Kommunikationsbandbreite und Unterstützung für Kommunikationsprotokolle können bei dem Gerät eingeschränkt sein.

Ein Gerät kann über ein Kommunikationsprotokoll wie AMQP oder HTTP direkt mit einem Endpunkt eines Cloudgateways kommunizieren oder über einen Vermittler, z. B. ein Bereichsgateway, das einen Dienst wie die Protokollübersetzung bereitstellt.

### Datenverarbeitung und Analysen

In der Cloud empfängt der Prozessor für Datenstrom-Ereignisse D2C-Nachrichten in großem Maßstab von den Geräten und bestimmt, wie diese Nachrichten verarbeitet und gespeichert werden sollen. Mit einer Lösung für verbundene Geräte können Sie C2D-Daten in Form von Befehlen an bestimmte Geräte senden. Durch die Registrierung von Geräten bei der IoT-Lösung können Sie Geräte bereitstellen und steuern, welche Geräte eine Verbindung mit Ihrer Infrastruktur herstellen dürfen. Mit dem Back-End können Sie den Status Ihrer Geräte nachverfolgen und deren Aktivitäten überwachen.

IoT-Lösungen können automatische Feedback-Schleifen enthalten. Beispielsweise kann ein Machine Learning-Modul aus den D2C-Telemetriedaten eines Geräts ermitteln, dass die Temperatur eines bestimmten Geräts über der normalen Betriebstemperatur liegt, und einen Befehl an das Gerät senden, damit korrigierende Maßnahmen ergriffen werden.

### Präsentation

Mit vielen IoT-Lösungen können die Benutzer die auf den Geräten erfassten Daten anzeigen und analysieren. Diese Darstellungen können in Form von Dashboards oder BI-Berichten erfolgen.

[img-solution-architecture]: media/iot-azure-and-iot/iot-reference-architecture.png

[lnk-machinelearning]: http://azure.microsoft.com/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot

<!---HONumber=Oct15_HO2-->