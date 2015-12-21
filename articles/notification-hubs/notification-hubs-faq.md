<properties
	pageTitle="Azure Notification Hubs - häufig gestellte Fragen (FAQs)"
	description="Häufig gestellte Fragen zum Entwerfen/Implementieren von Lösungen in Notification Hubs"
	services="notification-hubs"
	documentationCenter="mobile"
	authors="wesmc7777"
	manager="dwrede"
	editor="" />

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="11/25/2015" 
	ms.author="wesmc" />

#Azure Notification Hubs - häufig gestellte Fragen (FAQs)

##Allgemein
###1\. Welche Preise gelten für Notification Hubs?
Notification Hubs wird in drei Stufen angeboten: *Free*, *Basic* und *Standard*. Weitere Informationen finden Sie hier – [Notification Hubs – Preise]. Die Kosten werden auf Abonnementebene berechnet und basieren auf der Anzahl von Pushvorgängen, sodass es keine Rolle spielt, über wie viele Namespaces oder Notification Hubs Sie verfügen. Der Free-Tarif wird zu Entwicklungszwecken ohne SLA-Garantie angeboten. Die Stufen "Basic" und "Standard" werden zur Verwendung in der Produktion angeboten, wobei die folgenden Funktionen nur für die Stufe "Standard" aktiviert werden:

- *Broadcast* – Stufe "Basic" schränkt die Anzahl der Notification Hubs-Tags auf 3K ein (gilt für > 5-Geräte). Wenn Ihre Zielgruppe über 3K groß ist, müssen Sie zur Stufe "Standard" wechseln.
- *Umfangreiche Telemetriedaten* – Stufe "Basic" ermöglicht keinen Export Ihrer Telemetrie- oder Registrierungsdaten. Wenn Sie Ihre Telemetriedaten zur Offlineanzeige oder -analyse exportieren möchten, müssen Sie zur Stufe "Standard" wechseln.
- *Mehrinstanzenfähigkeit* – Wenn Sie eine mobile App mit Notification Hubs zur Unterstützung mehrerer Mandanten erstellen, müssen Sie einen Wechsel auf die Stufe "Standard" erwägen. Dadurch können Sie die App PNS-Anmeldeinformationen (Push Notification Services) auf Notification Hubs-Namespaceebene festlegen und anschließend die Mandanten trennen, indem Sie ihnen individuelle Hubs in diesem gemeinsamen Namespace bereitstellen. Dies vereinfacht die Wartung und ermöglicht es Ihnen gleichzeitig, die SAS-Schlüssel zum Senden und Empfangen von Benachrichtigungen von den Notification Hubs für jeden Mandanten getrennt zu halten und Überschneidungen zwischen Mandanten zu vermeiden.

###2\. Was ist eine Vereinbarung zum Servicelevel (SLA)?
Für die Notification Hubs-Stufen "Basic" und "Standard" wird garantiert, dass von ordnungsgemäß konfigurierten Anwendungen zu mindestens 99,9 % der Zeit Benachrichtigungen gesendet oder Registrierungsverwaltungsvorgänge für Notification Hubs durchgeführt werden können, die im Rahmen der Notification Hubs-Stufen "Basic" oder "Standard" bereitgestellt werden. Weitere Informationen über die Vereinbarung zum Servicelevel finden Sie hier auf der SLA-Seite – [Notification Hubs-SLA]. Beachten Sie, dass keine SLA-Garantien für die Verbindungen zwischen PNS (Platform Notification Service) und dem Gerät gelten, da Notification Hubs von externen Plattformanbietern zum Übermitteln der Benachrichtigung an das Gerät abhängig sind.

###3\. Welche Kunden verwenden Notification Hubs?
Es gibt zahlreiche Kunden, die Notification Hubs verwenden, darunter beispielsweise:

* Sochi 2014 – Hunderte von Interessengruppen, über 3 Millionen Geräte, mehr als 150 Millionen Benachrichtigungen in 2 Wochen gesendet. [Fallstudie - Sochi]
* Skanska – [Fallstudie – Skanska]
* Seattle Times – [Fallstudie – Seattle Times]
* Mural.ly – [Fallstudie – Mural.ly]
* 7Digital – [Fallstudie – 7Digital]
* Bing Apps – mehrere Millionen von Geräten, 3 Millionen Benachrichtigungen pro Tag gesendet

###4\. Wie führe ich für meine Notification Hubs zum Ändern meiner Dienstebene ein Upgrade oder Downgrade durch?
Klicken Sie im [klassischen Azure-Portal] auf „Service Bus“, dann auf Ihren Namespace und schließlich auf Ihren Notification Hub. Unter der Registerkarte „Skalierung“ können Sie die Dienstebene Ihrer Notification Hubs ändern.

##Entwurf und Entwicklung
###1\. Welche Serviceplattformen werden unterstützt?
Es werden SDKs und Beispiele für .NET, Java, PHP, Python und Node.js bereitgestellt, damit ein App-Back-End für die Kommunikation mit Notification Hubs über eine beliebige dieser Plattformen eingerichtet werden kann. Notification Hubs-APIs basieren auf REST-Schnittstellen, sodass Sie sich für eine direkte Kommunikation entscheiden können. Weitere Informationen finden Sie hier: [NH – REST-APIs]

###2\. Welche Geräteplattformen werden unterstützt?
Benachrichtigungen können an die Plattformen Apple iOS, Android, Windows Universal und Windows Phone, Kindle, Android China (über Baidu), Xamarin (iOS & Android) und Chrome-Apps gesendet werden. Lernprogramme mit Schrittanleitungen für den Einstieg für diese Plattformen finden Sie hier: [NH – Lernprogramme für den Einstieg]

###3\. Werden Benachrichtigungen per SMS/E-Mail/Web unterstützt?
Notification Hubs wurde primär zum Senden von Benachrichtigungen an mobile Apps mithilfe der oben aufgeführten Plattformen entwickelt. Es werden keine Funktionen zum Senden von E-Mails oder SMS bereitgestellt, es können jedoch zusätzlich zu Notification Hubs Drittanbieterplattformen mit dieser Funktionalität integriert werden, um systemeigene Pushbenachrichtigungen unter Verwendung von Azure Mobile Services zu senden. Es werden ebenfalls keine sofort einsatzfähigen Browserpushbenachrichtigungen bereitgestellt. Kunden können diese Funktionalität unter Verwendung von SignalR implementieren. Es steht ein Lernprogramm zum Senden von Pushbenachrichtigungen an Chrome-Apps über den Google Chrome-Browser zur Verfügung. Siehe hier – [Chrome-Apps-Lernprogramm]

###4\. Welche Beziehung besteht zwischen Azure Mobile Services und Azure Notification Hubs, und wann verwende ich was?
Wenn Sie über ein vorhandenes Back-End für mobile Apps verfügen und nur die Funktionalität zum Senden von Pushbenachrichtigungen hinzufügen möchten, müssen Sie Azure Notification Hubs verwenden. Wenn Sie ein Back-End für mobile Apps von Grund auf erstellen möchten, sollten Sie den Einsatz von Azure Mobile Services erwägen. Azure Mobile Services stellt automatisch einen Notification Hub für Sie bereit, damit Sie über das Back-End der mobilen App problemlos Pushbenachrichtigungen senden können. Die Preise für Azure Mobile Services schließen die Grundgebühren für Notification Hubs ein, und Kosten fallen nur dann an, wenn Sie die eingeschlossene Zahl an Pushvorgängen überschreiten. Weitere Einzelheiten finden Sie hier: [Mobile Services-Preise].

###5\. Wie viele Geräte werden unterstützt?
Für die Stufen "Basic" und "Standard" gelten keine Beschränkungen für die Anzahl von aktiven Geräten, die Benachrichtigungen empfangen können. Weitere Informationen finden Sie hier: [Notification Hubs – Preise].

###6\. Wie viele Pushbenachrichtigungen kann ich senden?
Kunden nutzen Azure Notification Hubs, um Millionen von Pushbenachrichtigungen pro Tag zu senden. Es sind keine zusätzlichen Schritte zur Skalierung von Notification Hubs erforderlich. Die Skalierung erfolgt basierend auf der Anzahl von Benachrichtigungen, die über das System verarbeitet werden, automatisch. Beachten Sie, dass die Preisgestaltung durch die Anzahl von verarbeiteten Pushbenachrichtigungen beeinflusst wird.

###7\. Wie lange dauert die Übertragung der Benachrichtigungen auf mein Gerät?
Azure Notification Hubs kann in einem normalen Benutzerszenario mit konsistenter Eingangslast ohne nennenswerte Spitzen mindestens 1 Millionen Sendevorgänge innerhalb von 1 Minute verarbeiten. Diese Rate kann abhängig von der Anzahl von Tags, der Art der eingehenden Sendevorgänge usw. variieren. In diesem Zeitraum kann die Berechnung von Zielen pro Plattform und die Weiterleitung von Nachrichten an die entsprechenden PNS (Push Notification Services) basierend auf den registrierten Tags/Tagausdrücken erfolgen. Ab dort liegt die Verantwortung zur Übermittlung der Benachrichtigung an das Gerät beim PNS (Push Notification Service). Push Notification Services garantieren keine SLAs für die Übermittlung von Benachrichtigungen, der größte Teil der Nachrichten wird jedoch innerhalb von wenigen Minuten (< 10 Minuten) nach dem Senden an die Plattform zugestellt. Es kann wenige Ausnahmen geben, bei denen die Übermittlung mehr Zeit in Anspruch nimmt. Azure Notification Hubs verfügt über eine Richtlinie, nach der Benachrichtigungen verworfen werden, wenn sie nicht innerhalb von 30 Minuten an den PNS übermittelt werden können. Diese Verzögerung kann aus verschiedenen Gründen auftreten, meistens jedoch, weil der PNS Ihre Anwendung drosselt.

###8\. Gibt es garantierte Latenzzeiten?
Aufgrund der Tatsache, dass Pushbenachrichtigungen von einem externen, plattformspezifischen PNS übermittelt werden, können keine Latenzzeiten garantiert werden. Die Mehrzahl der Benachrichtigungen wird jedoch innerhalb weniger Minuten zugestellt.

###9\. Welche Aspekte müssen beim Entwurf einer Lösung mit Namespaces und Notification Hubs berücksichtigt werden?
*Mobile App/Umgebung:* Es sollte ein Notification Hub pro Umgebung für mobile Apps verfügbar sein. In einem mehrinstanzenfähigen Szenario sollte jeder Mandant über einen separaten Hub verfügen. Ein Notification Hub darf niemals von einer Test- und einer Produktionsumgebung gemeinsam genutzt werden, da dies zu Problemen beim Senden von Benachrichtigungen führen kann. Apple bietet beispielsweise Pushendpunkte für Sandbox und Produktion an, die jeweils über separate Anmeldeinformationen verfügen. Wenn der Hub ursprünglich mit einem Apple-Sandbox-Zertifikat konfiguriert wurde und anschließend zur Verwendung eines Apple-Produktionszertifikats neu konfiguriert wird, verfällt das vorherige Gerätetoken durch das neue Zertifikat, und es kommt zu Fehlern bei Pushvorgängen. Sie sollten Ihre Produktions- und Testumgebung immer getrennt voneinander verwalten und unterschiedliche Hubs für unterschiedliche Umgebungen verwenden.

*PNS-Anmeldeinformationen:* Wenn eine mobile App für das Entwicklerportal einer Plattform registriert wird (z. B. Apple oder Google usw.), erhalten Sie eine App-ID und Sicherheitstoken, die ein App-Back-End für den PNS der Plattform benötigen, um Pushbenachrichtigungen an die Geräte zu senden. Diese Sicherheitstoken, die in Form von Zertifikaten (beispielsweise für Apple iOS oder Windows Phone) oder als Sicherheitsschlüssel (Google Android, Windows usw.) ausgestellt werden, müssen in Notification Hubs konfiguriert werden. Die Konfiguration erfolgt typischerweise auf Notification Hub-Ebene, kann jedoch in einem Szenario mit Mehrinstanzenfähigkeit auch auf Namespaceebene durchgeführt werden.

*Namespaces:* Namespaces können auch für eine Bereitstellungsgruppierung eingesetzt werden. Sie können alle Notification Hubs für alle Mandanten derselben App in einem Szenario mit Mehrinstanzenfähigkeit repräsentieren.

*Geografische Verteilung:* Die geografische Verteilung ist bei Pushbenachrichtigungen nicht immer ein wichtiger Aspekt. Die verschiedenen Push Notification Services (beispielsweise APNS, GCM usw.), die für die Zustellung der Pushbenachrichtigungen an die Geräte sorgen, sind häufig selbst nicht gleichmäßig verteilt. Wenn Sie jedoch über eine Anwendung verfügen, die weltweit eingesetzt wird, können Sie mehrere Hubs in verschiedenen Namespaces erstellen, um die Verfügbarkeit des Notification Hubs-Diensts in verschiedenen Azure-Regionen rund um den Globus zu nutzen. Beachten Sie, dass dies die Verwaltungskosten erhöht, insbesondere im Hinblick auf die Registrierungen. Deshalb wird diese Konfiguration nicht ausdrücklich empfohlen und sollte nur verwendet werden, wenn dies unbedingt erforderlich ist.

###10\. Sollten Registrierungen vom App-Back-End oder direkt über das Gerät erfolgen?
Registrierungen über das App-Back-End sind sinnvoll, wenn Sie eine Clientauthentifizierung durchführen müssen, bevor Sie die Registrierung erstellen, oder wenn Sie über Tags verfügen, die vom App-Back-End basierend auf einer bestimmten Applogik durch die App erstellt oder geändert werden müssen. Weitere Anweisungen finden Sie hier: [Leitfaden zur Back-End-Registrierung] und [Leitfaden zur Back-End-Registrierung – 2].

###11\. Welches Sicherheitsmodell wird verwendet?
Azure Notification Hubs verwendet ein SAS-basiertes (Shared Access Signature) Sicherheitsmodell. Sie können die SAS-Token auf Namespacestammebene oder auf der granularen Notification Hubs-Ebene verwenden. Diese SAS-Token können mit verschiedenen Authentifizierungsregeln festgelegt werden, z. B. Berechtigungen zum Senden von Nachrichten oder Berechtigungen zum Überwachen auf Benachrichtigungen usw.. Weitere Einzelheiten finden Sie hier: [NH Sicherheitsmodell].

###12\. Wie werden sensible Daten in Benachrichtigungen behandelt?
Alle Benachrichtigungen werden über den Push Notification Service (PNS) der Plattform an die Geräte übermittelt. Wenn ein Absender eine Benachrichtigung an Azure Notification Hubs sendet, werden diese Benachrichtigungen verarbeitet und an den jeweiligen PNS weitergeleitet. Alle Verbindungen zwischen Absender und Azure Notification Hubs und zum PNS verwenden HTTPS. Azure Notification Hubs führt keine Protokollierung der Nachrichteninhalte durch. Für das Senden sensibler Daten wird jedoch der Einsatz eines sicheren Pushmusters empfohlen, bei dem der Absender eine "Ping"-Benachrichtigung ohne die vertraulichen Daten mit einer Nachrichten-ID an das Gerät sendet. Wenn die App diese Daten empfängt, erfolgt ein direkter Aufruf einer sicheren App-Back-End-API, mit der die Nachrichtendetails angefordert werden. Ein Lernprogramm zur Implementierung dieses Musters finden Sie hier: [NH – Lernprogramm für sichere Pushvorgänge].

##Vorgänge
###1\. Wie wird eine Notfallwiederherstellung gewährleistet?
Es wird eine Notfallwiederherstellung für Metadaten bereitgestellt (Notification Hub-Name, Verbindungszeichenfolge usw.). Im Falle einer Notfallwiederherstellung gehen die Registrierungsdaten verloren, deshalb müssen Sie diese Daten über eine eigene Lösung wieder in Ihren neuen Hub einspeisen.

- *Schritt 1* – Erstellen eines sekundären Notification Hubs auf einem anderen Domänencontroller. Sie können den sekundären Notification Hub im Falle einer Notfallwiederherstellung bei Bedarf oder bereits im Vorfeld erstellen. Dies spielt keine große Rolle, da die NH-Bereitstellung sehr schnell erfolgt und innerhalb weniger Sekunden durchgeführt werden kann. Es wird jedoch empfohlen, bereits im Vorfeld einen sekundären Notification Hub zu erstellen, da so Auswirkungen der Notfallwiederherstellung auf Verwaltungsfunktionen ausgeschlossen werden.

- *Schritt 2* – Stellen Sie die Registrierungen aus dem primären Notification Hub im sekundären Notification Hub bereit. Es wird nicht empfohlen, Registrierungen auf beiden Hubs zu verwalten und diese zu synchronisieren, wenn Registrierungen eingehen. Dies funktioniert in der Regel nicht, da Registrierungen vom PNS mit einem Ablaufdatum versehen werden. Notification Hubs bereinigt Registrierungen, wenn PNS-Informationen zu abgelaufenen oder ungültigen Registrierungen empfangen werden.

Es wird empfohlen, ein App-Back-End zu verwenden, das folgende Aufgaben erfüllt:

- Verwaltung der Registrierungen, sodass bei einer Notfallwiederherstellung ein Masseneinfügevorgang in den sekundären Notification Hub durchgeführt werden kann, ODER

- Regelmäßiger Abruf einer Sicherungskopie der Registrierungen aus dem primären Hub als Datensicherung und anschließendes Einfügen der Registrierungen auf dem sekundären Notification Hub (per Massenvorgang).

(Informationen zur Funktion für den Export/Import von Registrierungen in der Stufe "Standard" finden Sie hier: [Exportieren/Importieren von Registrierungen].)

Wenn Sie über kein Back-End verfügen und die App auf den Geräten gestartet wird, erfolgt eine Neuregistrierung im sekundären Notification Hub. Dies führt letztlich dazu, dass der sekundäre Notification Hub über alle Registrierungen für aktive Geräte verfügt. Der Nachteil hierbei ist, dass Geräte, auf denen die App nicht geöffnet wird, eine Zeit lang keine Benachrichtigungen erhalten.

###2\. Steht ein Überwachungsprotokoll zur Verfügung?
Alle Notification Hubs-Verwaltungsvorgänge werden in Vorgangsprotokollen aufgezeichnet, die im [klassischen Azure-Portal] zur Verfügung stehen.

##Überwachung und Problembehandlung
###1\. Welche Funktionen für die Problembehandlung stehen zur Verfügung?
Azure Notification Hubs bietet verschiedene Funktionen für die gängige Problembehandlung, insbesondere für häufigsten Szenarien im Zusammenhang mit verworfenen Benachrichtigungen. Weitere Informationen finden Sie in diesem Whitepaper zur Problembehandlung: [NH – Problembehandlung].

###2\. Welche Telemetriefunktionen stehen zur Verfügung?
Azure Notification Hubs ermöglicht die Anzeige von Telemetriedaten im [klassischen Azure-Portal]. Detailinformationen zu den verfügbaren Metriken finden Sie hier: [NH – Metriken]. Beachten Sie, dass erfolgreiche Benachrichtigungen nur bedeuten, dass die Benachrichtigungen an den externen PNS übermittelt wurden (z. B. APNS für Apple, GCM für Google usw.). Anschließend liegt es in der Verantwortung des PNS, die Benachrichtigung an die Geräte zuzustellen. Die PNS-Anbieter stellen zu diesem Vorgang keine näheren Angaben zur Verfügung. Es gibt außerdem die Möglichkeit, Telemetriedaten programmatisch zu exportieren (verfügbar in Stufe "Standard"). Ein Beispiel mit näheren Informationen finden Sie hier: [NH – Beispiel zu Metriken].

[klassischen Azure-Portal]: https://manage.windowsazure.com
[Notification Hubs – Preise]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs-SLA]: http://azure.microsoft.com/support/legal/sla/
[Fallstudie - Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[Fallstudie – Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[Fallstudie – Seattle Times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[Fallstudie – Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[Fallstudie – 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH – REST-APIs]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH – Lernprogramme für den Einstieg]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Chrome-Apps-Lernprogramm]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services-Preise]: http://azure.microsoft.com/pricing/details/mobile-services/
[Leitfaden zur Back-End-Registrierung]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Leitfaden zur Back-End-Registrierung – 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[NH Sicherheitsmodell]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH – Lernprogramm für sichere Pushvorgänge]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH – Problembehandlung]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH – Metriken]: https://msdn.microsoft.com/library/dn458822.aspx
[NH – Beispiel zu Metriken]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Exportieren/Importieren von Registrierungen]: https://msdn.microsoft.com/library/dn790624.aspx

<!---HONumber=AcomDC_1210_2015-->