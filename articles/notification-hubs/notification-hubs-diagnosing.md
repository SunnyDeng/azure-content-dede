<properties 
	pageTitle="Azure Notification Hubs - Diagnoserichtlinien" 
	description="Richtlinien zum Analysieren allgemeiner Probleme mit Azure Notification Hubs." 
	services="notification-hubs" 
	documentationCenter="Mobile" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="wesmc"/>

#Azure Notification Hubs - Diagnoserichtlinien

##Übersicht

Eine der häufigsten Fragen, die wir von Azure Notification Hubs-Kunden hören, lautet, wie lässt sich herauszufinden, warum eine Benachrichtigung, die von ihrem Anwendungs-Back-End gesendet wurde, nicht auf dem Clientgerät zu sehen ist – wo und warum Benachrichtigungen verworfen wurden, und wie sich dieses Problem beheben lässt. In diesem Artikel werden wir die verschiedenen Gründe erläutern, warum Benachrichtigungen möglicherweise verworfen werden oder nicht bei den Geräten ankommen. Es werden auch Möglichkeiten vorgestellt, wie die eigentliche Ursache analysiert und ermittelt werden kann.

Zunächst einmal ist es wichtig, zu verstehen, wie Azure Notification Hubs Benachrichtigungen mithilfe von Push an die Geräte überträgt. ![][0]

In einem typischen Benachrichtigung senden-Fluss wird die Nachricht vom **Anwendungs-Back-End** zum **Azure Notification Hub (NH)** (Benachrichtigungshub) gesendet, der wiederum einige Verarbeitung für alle Registrierungen ausführt, wobei die konfigurierten Tags und Tagausdrücke berücksichtigt werden, um „Ziele“ (targets) zu ermitteln, d. h. alle Registrierungen, die die Pushbenachrichtigung empfangen müssen. Diese Registrierungen können sich über einige oder sämtliche unserer unterstützten Plattformen erstrecken - iOS, Google, Windows, Windows Phone, Kindle und Baidu für China Android. Nachdem die Ziele eingerichtet sind, überträgt NH dann mithilfe von Push auf mehrere Registrierungsbatches aufgeteilte Benachrichtigungen an den geräteplattformspezifischen **Pushbenachrichtigungsdienst (Push Notification Service, PNS)** – z. B. APNS für Apple, GCM für Google usw. NH authentifiziert sich bei dem entsprechenden PNS anhand der Anmeldeinformationen, die Sie im klassischen Azure-Portal auf der Seite „Notification Hub konfigurieren“ festgelegt haben. Danach leitet der PNS die Benachrichtigungen an die entsprechenden **Clientgeräte** weiter. Dies ist die für Plattformen empfohlene Methode zum Übermitteln von Pushbenachrichtigungen, und Sie sollten beachten, dass der letzte Abschnitt einer Benachrichtigungsübermittlung zwischen dem Plattform-PNS und dem Gerät erfolgt. Daher gibt es vier Hauptkomponenten – *Client*, *Anwendungs-Back-End*, *Azure Notification Hubs (NH)* und *Push Notification Services (PNS)*. Jede dieser Komponenten kann dafür verantwortlich sein, dass Benachrichtigungen verworfen werden. Weitere Informationen zu dieser Architektur finden Sie unter [Übersicht über Benachrichtigungshubs].

Fehler beim Übermitteln von Benachrichtigungen können während der ersten Test-/Stagingphase vorkommen, wodurch ein mögliches Konfigurationsproblem angezeigt wird, oder können während der Produktion vorkommen, wenn eventuell alle oder einige der Benachrichtigungen gelöscht werden, was ein Hinweis auf ein umfassenderes Anwendungs- oder Messagingmusterproblem sein kann. Im folgenden Abschnitt werden verschiedene Szenarien für gelöschte Benachrichtigungen betrachtet. Dies reicht von üblichen bis selteneren Szenarien, von denen Sie einige möglicherweise als offensichtlich und einige andere als nicht so offensichtlich ansehen.

##Fehlerhafte Konfiguration von Azure Notification Hubs 

Azure Notification Hubs muss sich selbst im Kontext der Anwendung des Entwicklers authentifizieren, um in der Lage zu sein, Benachrichtigungen erfolgreich an den jeweiligen PNS zu senden. Dies wird ermöglicht, indem der Entwickler ein Entwicklerkonto für die jeweilige Plattform (Google, Apple, Windows usw.) erstellt und dann seine Anwendung registriert, wobei er Anmeldeinformationen erhält, die im Portal im Konfigurationsabschnitt für Notification Hubs konfiguriert werden müssen. Wenn keine Benachrichtigungen weitergeleitet werden, sollte im ersten Schritt sichergestellt werden, dass die richtigen Anmeldeinformationen im Benachrichtigungshub konfiguriert sind. Gleichen Sie dazu die Anmeldeinformationen mit der Anwendung ab, die unter dem jeweiligen plattformspezifischen Entwicklerkonto erstellt wurde. Dabei können unsere [Erste Schritte-Lernprogramme] nützlich sein, um diesen Prozess Schritt für Schritt zu durchlaufen. Dies sind einige übliche fehlerhafte Konfigurationen:

1. **Allgemein**
 
	a) Stellen Sie sicher, dass der Name des Benachrichtigungshubs (ohne Tippfehler) an folgenden Stellen identisch ist:

	- Dort, wo aus dem Client registriert wird 
	- Dort, wo Benachrichtigungen aus dem Back-End gesendet werden  
	- Dort, wo Sie die PNS-Anmeldeinformationen konfiguriert haben 
	- Für die Person, deren SAS-Anmeldeinformationen Sie im Client und im Back-End konfiguriert haben 
		
	b) Stellen Sie sicher, dass Sie die richtigen SAS-Konfigurationszeichenfolgen im Client und im Anwendungs-Back-End verwenden. Als Faustregel gilt, dass Sie **DefaultListenSharedAccessSignature** im Client und **DefaultFullSharedAccessSignature** im Anwendungs-Back-End verwenden müssen (wodurch die Berechtigung erteilt wird, Benachrichtigungen an den NH senden zu können).

2. **APNS-Konfiguration (Apple Push Notification Service)**
 
	Sie müssen zwei unterschiedliche Hubs verwalten – einen für die Produktion und einen anderen für Testzwecke. Dies bedeutet, dass Sie das Zertifikat, das Sie in einer Sandkastenumgebung verwenden möchten, auf einen einzelnen Hub und das Zertifikat, das Sie in einer Produktionsumgebung verwenden möchten, auf einen einzelnen Hub hochladen müssen. Sie sollten nicht versuchen, verschiedene Typen von Zertifikaten auf denselben Hub hochzuladen, weil dies später zu Benachrichtigungsfehlern führen kann. Sollten Sie versehentlich verschiedene Typen von Zertifikaten auf denselben Hub hochgeladen haben, empfiehlt es sich, dass Sie den Hub löschen und von vorne beginnen. Wenn Sie den Hub aus irgendeinem Grund nicht löschen können, müssen Sie zumindest alle vorhandenen Registrierungen aus dem Hub löschen.

3. **GCM-Konfiguration (Google Cloud Messaging)**

	a) Aktivieren Sie „Google Cloud Messaging for Android“ unter Ihrem Cloudprojekt.
	
	![][2]
	
	b) Erstellen Sie einen Serverschlüssel (Server key), wenn Sie die Anmeldeinformationen abrufen, mit denen der NH sich bei GCM authentifiziert.
	
	![][3]
	
	c) Konfigurieren Sie im Client eine Projektnummer (Project Number), die eine vollständig numerische Entität ist, die Sie vom Dashboard abrufen können:
	
	![][1]

##Anwendungsprobleme

1) **Tags oder Tagausdrücke**

Wenn Sie Tags oder Tagausdrücke verwenden, um Ihre Zielgruppe zu segmentieren, ist es bei einem Senden der Benachrichtigung immer möglich, dass kein Ziel anhand der Tags oder Tagausdrücke gefunden wird, die Sie in Ihrem Sendeaufruf angeben,. Es empfiehlt sich, Ihre Registrierungen zu überprüfen, um sicherzustellen, dass es passende Tags gibt, wenn Sie eine Benachrichtigung senden. Überprüfen Sie anschließend den Benachrichtigungsempfang nur von den Clients mit diesen Registrierungen. Nehmen Sie z. B. an, Ihre Registrierungen bei dem NH wurden alle mit dem Tag „Politik“ vorgenommen, und Sie senden eine Benachrichtigung mit dem Tag „Sport“. In diesem Fall wird die Benachrichtigung an kein Gerät gesendet. In einem komplexen Fall könnte es um Tagausdrücke gehen, die Sie nur für „Tag A“ oder „Tag B“ registriert haben, beim Senden von Benachrichtigungen wird aber „Tag A und Tag B“ als Ziel verwendet. Im Abschnitt „Tipps für eigene Diagnosen“ weiter unten sind Möglichkeiten aufgeführt, wie Sie Ihre Registrierungen zusammen mit den für sie angegebenen Tags überprüfen können.

2) **Vorlagenprobleme**

Wenn Sie Vorlagen verwenden, müssen Sie den Richtlinien folgen, die in der [Anleitung für Vorlagen] beschrieben sind.

3) **Ungültige Registrierungen**

Unter der Voraussetzung, dass der Benachrichtigungshub richtig konfiguriert ist und alle Tags oder Tagausdrücke richtig verwendet wurden, sodass gültige Ziele gefunden werden, an die die Benachrichtigungen gesendet werden müssen, führt der Hub (NH) mehrere Batches parallel aus - in jedem Batch werden Benachrichtigungen an eine Gruppe von Registrierungen gesendet.

> [AZURE.NOTE] Da die Verarbeitung parallel erfolgt, gibt es keine Garantie hinsichtlich der Reihenfolge, in der die Benachrichtigungen übermittelt werden.

Azure Notifications Hub ist mittlerweile für ein „maximal einmal“-Nachrichtenübermittlungsmodell optimiert. Dies bedeutet, dass eine Deduplizierung versucht wird, sodass keine Benachrichtigung häufiger als einmal an ein Gerät übermittelt wird. Damit dies sichergestellt ist, werden die Registrierungen durchsucht und wird darauf geachtet, dass nur eine Nachricht pro Geräte-ID gesendet wird, bevor die Nachricht tatsächlich an den PNS gesendet wird. Da jeder Batch an den PNS gesendet wird, der wiederum die Registrierungen annimmt und überprüft, ist es möglich, dass der PNS einen Fehler für eine oder mehrere Registrierungen in einem Batch erkennt, einen Fehler an Azure NH zurückgibt und die Verarbeitung beendet, wodurch dieser Batch vollständig verworfen wird. Dies gilt insbesondere für APNS, für den ein TCP-Streamprotokoll verwendet wird. Da es eine Optimierung hinsichtlich maximal einer Übermittlung gibt, ist zu beachten, dass kein Wiederholungsversuch für einen fehlgeschlagenen Batch unternommen wird, weil nicht sicher bekannt ist, ob der PNS den Batch vollständig oder teilweise verworfen hat. Der PNS teilt Azure NH jedoch mit, welche Registrierung den Fehler verursacht hat, und anhand dieses Feedbacks wird diese Registrierung aus der Datenbank entfernt. Dies bedeutet, dass es möglich ist, dass ein Registrierungsbatch oder eine Teilmenge dieses Batches möglicherweise keine Benachrichtigung erhält. Da die fehlerhafte Registrierung aber bereinigt wurde, gibt es, wenn das nächste Mal ein Sendeversuch unternommen wird, eine größere Chance für ein erfolgreiches Senden. Wenn die Menge der Zielgeräte wächst (einige unserer Kunden senden Benachrichtigungen an Millionen Geräte), bedeutet ein gelegentliches Verwerfen eines fehlerhaften Batches keinen großen Unterschied im Gesamtprozentsatz der Geräte, die Benachrichtigungen empfangen. Wenn Sie aber wenige Benachrichtigungen senden, und es gibt einige PNS-Fehler, werden möglicherweise alle oder die meisten Benachrichtigungen nicht empfangen. Tritt dieses Verhalten wiederholt auf, müssen Sie die fehlerhaften Registrierungen ermitteln und löschen. Auf jeden Fall müssen Sie alle manuell konfigurierten Registrierungen löschen, weil diese die häufigste Ursache für verworfene Benachrichtigungen sind. Ist dies eine Testumgebung, sollten Sie auch sofort alle Registrierungen löschen, weil die Apps, wenn sie auf den Geräten geöffnet werden, versuchen, sich erneut bei den Benachrichtigungshubs zu registrieren. So lässt sich erreichen, dass alle dadurch erstellten Registrierungen gültig sind.

##PNS-Probleme

Sobald eine Benachrichtigung vom entsprechenden PNS empfangen wurde, ist dieser dafür verantwortlich, die Benachrichtigung an das Gerät zu senden. Azure Notification Hubs ist hier nicht mehr beteiligt und hat keine Kontrolle darüber, wann oder ob die Benachrichtigung an das Gerät gesendet wird. Da die Plattformbenachrichtigungsdienste ziemlich robust sind, dauert die Übertragung von Benachrichtigungen an die Geräte üblicherweise nur wenige Sekunden. Wenn der PNS jedoch drosselt, wird von Azure Notification Hubs eine exponentielle Backoff-Strategie angewendet, und wenn das PNS 30 Minuten lang nicht erreichbar bleibt, wird eine Richtlinie wirksam, um diese Nachrichten dauerhaft ungültig werden zu lassen und zu verwerfen.

Wenn ein PNS versucht, eine Benachrichtigung zu übermitteln, das Gerät aber offline ist, wird die Benachrichtigung vom PNS für einen begrenzten Zeitraum gespeichert und an das Gerät gesendet, wenn es wieder verfügbar ist. Nur eine letzte Benachrichtigung für eine bestimmte App wird gespeichert. Werden mehrere Benachrichtigungen gesendet, während das Gerät offline ist, bewirkt jede neue Benachrichtigung, dass die vorherige Benachrichtigung verworfen wird. Dieses Verhalten, bei dem nur die neueste Benachrichtigung beibehalten wird, wird in APNS als „coalescing notifications“ (zusammenwachsende Benachrichtigungen) und in GCM (in dem ein reduzierbarer Schlüssel verwendet wird) als „collapsing“ (reduzierend) bezeichnet. Wenn das Gerät über einen längeren Zeitraum offline bleibt, werden alle für das Gerät gespeicherten Benachrichtigungen verworfen. Quellen: [APNS-Richtlinien] und [GCM-Richtlinien]

Mit Azure Notification Hubs können Sie einen zusammengeführten Schlüssel über einen HTTP-Header mit der generischen `SendNotification`-API (z. B. für .NET SDK – `SendNotificationAsync`) übergeben, die auch HTTP-Header akzeptiert, die unverändert an den entsprechenden PNS übergeben werden.

##Tipps für eigene Diagnosen

Im Folgenden werden die verschiedenen Möglichkeiten vorgestellt, wie Benachrichtigungshubprobleme diagnostiziert und deren Hauptursachen ermittelt werden können:

###Überprüfen von Anmeldeinformationen

1. **PNS-Entwicklerportal**

	Überprüfen Sie die Anmeldeinformationen auf dem jeweiligen PNS-Entwicklerportal (APNS, GCM, WNS usw.) über unsere [Erste-Schritte-Lernprogramme].

2. **Klassisches Azure-Portal**

	Navigieren Sie zur Registerkarte „Konfigurieren“, um die Anmeldeinformationen zu überprüfen und mit denjenigen zu vergleichen, die Sie aus dem PNS-Entwicklerportal übernommen haben.

	![][4]

###Überprüfen von Registrierungen

1. **Visual Studio**

	Wenn Sie mit Visual Studio entwickeln, können Sie eine Verbindung mit Microsoft Azure herstellen und in „Server-Explorer“ eine Reihe von Azure-Diensten anzeigen und verwalten, einschließlich Notification Hubs (Benachrichtigungs-Hubs). Dies ist in erster Linie für Ihre Test-/Entwicklungsumgebung nützlich.

	![][9]

	Sie können alle Registrierungen in Ihrem Hub anzeigen und verwalten. Die Registrierungen sind geeignet nach Plattform, systemeigener oder Vorlagenregistrierung, jeglichen Tags, PNS-Bezeichner, Registrierungs-ID und Ablaufdatum kategorisiert. Sie können eine Registrierung auch direkt bearbeiten – dies ist z. B. hilfreich, wenn Sie Tags bearbeiten möchten.

	![][8]
 
	> [AZURE.NOTE] Die Visual Studio-Funktionen zum Bearbeiten von Registrierungen sollten nur beim Entwickeln/Testen mit begrenzter Anzahl von Registrierungen verwendet werden. Wenn es erforderlich wird, Ihre Registrierungen in einem Sammelvorgang zu korrigieren, bietet es sich an, die Export/Import-Registrierungsfunktionalität zu verwenden, die unter [Exportieren und Ändern von Registrierungen](https://msdn.microsoft.com/library/dn790624.aspx) beschrieben ist.

2. **Service Bus-Explorer**

	Viele Kunden nutzen den unter Service Bus-Explorer beschriebenen [Service Bus-Explorer], um ihre Benachrichtigungshubs anzuzeigen und zu verwalten. Dieser Explorer ist ein Open Source-Projekt, das über „code.microsoft.com“ verfügbar ist - [Service Bus Explorer-Code].

###Überprüfen von Benachrichtigungen

1. **Klassisches Azure-Portal**

	Sie können zur Registerkarte „Debuggen“ navigieren, um Testbenachrichtigungen an Ihre Clients zu senden, ohne dass ein Dienst-Back-End eingerichtet sein und ausgeführt werden muss.

	![][7]

2. **Visual Studio**

	Sie können Testbenachrichtigungen auch aus der komfortablen Umgebung von Visual Studio senden:

	![][10]

	Weitere Informationen zu den Funktionen von Visual Studio Notification Hub Azure Explorer finden Sie hier:
	
	- [Übersicht über Visual Studio Server-Explorer]
	- [Visual Studio Server Explorer-Blogbeitrag - 1]
	- [Visual Studio Server Explorer-Blogbeitrag - 2]

###Debuggen von fehlerhaften Benachrichtigungen / Überprüfen von Benachrichtigungsergebnissesn

**EnableTestSend-Eigenschaft**

Wenn Sie eine Benachrichtigung über einen Benachrichtigungshub senden, wird diese zunächst zur Verarbeitung in die Warteschlange des Hubs eingereiht, um alle Ziele der Benachrichtigung zu ermitteln, und schließlich vom Benachrichtigungshub an den PNS gesendet. Das heißt, dass die erfolgreiche Rückgabe eines Sendeaufrufs, wenn Sie REST-API oder irgendeine Client-SDK verwenden, nur bedeutet, dass die Nachricht erfolgreich in die Warteschlange des Benachrichtigungshubs eingereiht wurde. Sie haben keinen Einblick in das, was geschehen ist, als der Benachrichtigungshub schließlich versucht hat, die Nachricht an den PNS zu senden. Wenn die Benachrichtigung nicht auf dem Clientgerät eintrifft, ist möglicherweise ein Fehler aufgetreten, als der Benachrichtigungshub versucht hat, die Nachricht zu senden, beispielsweise weil die Nutzlastgröße die vom PNS maximal zugelassene Größe überschritten hat oder die im Benachrichtigungshub konfigurierten Anmeldeinformationen ungültig sind usw. Um Informationen zu den PNS-Fehler zu erhalten, haben wir eine Eigenschaft namens [EnableTestSend] eingeführt. Diese Eigenschaft wird automatisch aktiviert, wenn Sie Testnachrichten aus dem Portal oder aus dem Visual Studio-Client senden, und ermöglicht Ihnen daher, detaillierte Debuginformationen anzuzeigen. Sie können dies über APIs nutzen, wenn Sie das Beispiel für das .NET SDK verwenden, in dem die Eigenschaft jetzt verfügbar ist. Außerdem wird sie letztlich zu allen Client-SDKs hinzugefügt. Um dies mit dem REST-Aufruf zu verwenden, fügen Sie einfach einen Abfragezeichenfolgenparameter namens „test“ an das Ende des Sendeaufrufs an.

	https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*Beispiel (.NET SDK)*
 
Angenommen, Sie verwenden .NET SDK, um eine systemeigene Popupbenachrichtigung zu senden:

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
 
`result.State` setzt einfach `Enqueued` an das Ende der Ausführung, ohne jeglichen Einblick in die Art und Weise, wie der Pushvorgang verarbeitet wurde. Nun können Sie die boolesche Eigenschaft `EnableTestSend` beim Initialisieren von `NotificationHubClient` verwenden, um ausführliche Statusinformationen zu den PNS-Fehlern zu erhalten, die beim Senden der Benachrichtigung aufgetreten sind. Für diesen Sendeaufruf ist zusätzliche Zeit für die Rückgabe erforderlich, weil diese erst erfolgt, nachdem der Benachrichtigungshaub die Benachrichtigung an den PNS gesendet hat, um das Ergebnis zu ermitteln.
 
	bool enableTestSend = true;
	NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);
	
	var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
	Console.WriteLine(outcome.State);
	
	foreach (RegistrationResult result in outcome.Results)
	{
	    Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
	}

*Beispielausgabe*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong
 
Diese Meldung gibt entweder an, dass im Benachrichtigungshub ungültige Anmeldeinformationen konfiguriert sind, oder, dass es ein Problem mit den Registrierungen im Hub gibt. Die empfohlene Vorgehensweise besteht darin, diese Registrierungen zu löschen und vom Client erneut erstellen zu lassen, bevor die Nachricht gesendet wird.
 
> [AZURE.NOTE] Beachten Sie, dass die Verwendung dieser Eigenschaft stark eingeschränkt ist und Sie diese daher nur in einer Test-/Entwicklungsumgebung mit begrenzter Anzahl von Registrierungen verwenden sollten. Debugbenachrichtigungen werden nur an 10 Geräte gesendet. Außerdem ist die Verarbeitung von Debugsendevorgängen auf 10 pro Minute beschränkt.

###Überprüfen der Telemetrie 

1. **Verwenden des klassischen Azure-Portals**

	Das Portal bietet Ihnen einen schnellen Überblick über alle Aktivitäten in Ihrem Notification Hub.
	
	a) Auf der Registerkarte "Dashboard" können Sie eine aggregierte Ansicht der Registrierungen, Benachrichtigungen sowie Fehler pro Plattform anzeigen.
	
	![][5]
	
	b) Sie können auch viele weitere plattformspezifische Metriken von der Registerkarte „Überwachen“ hinzufügen, um eine genauere Betrachtung besonders aller PNS-spezifischen Fehler zu erhalten, die zurückgegeben werden, wenn der Notification Hub versucht, die Benachrichtigung an den PNS zu senden.
	
	![][6]
	
	c) Sie sollten zunächst **Eingehende Nachrichten**, **Registrierungsvorgänge**, **Erfolgreiche Benachrichtigungen** überprüfen und dann die jeweilige plattformspezifische Registerkarte anzeigen, um die PNS-spezifischen Fehler zu überprüfen.
	
	d) Wenn Sie den Benachrichtigungshub mit fehlerhaften Authentifizierungseinstellungen konfiguriert haben, wird der PNS-Authentifizierungsfehler angezeigt. Dies ist ein guter Indikator, um die PNS-Anmeldeinformationen zu überprüfen.

2) **Programmgesteuerter Zugriff**

Ausführlichere Informationen finden Sie hier:

- [Programmgesteuerter Telemetriezugriff]
- [Telemetriezugriff über APIs – Beispiel] 

> [AZURE.NOTE] Mehrere telemetriebezogene Funktionen wie **Registrierungen exportieren/importieren**, **Telemetriezugriff über APIs** usw. sind nur auf der Stufe „Standard“ verfügbar. Wenn Sie versuchen, diese Funktionen zu verwenden, wenn Sie sich auf der Stufe „Free“ oder „Basic“ befinden, erhalten Sie hierzu eine Ausnahmemeldung, sofern Sie das SDK verwenden, und einen HTTP-Fehler 403 (Verboten), sofern Sie die Funktionen direkt über die REST-APIs verwenden. Vergewissern Sie sich, dass Sie über das klassische Azure-Portal in den Tarif „Standard“ gewechselt sind.

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png
 
<!-- LINKS -->
[Übersicht über Benachrichtigungshubs]: notification-hubs-overview.md
[Erste Schritte-Lernprogramme]: notification-hubs-windows-store-dotnet-get-started.md
[Erste-Schritte-Lernprogramme]: notification-hubs-windows-store-dotnet-get-started.md
[Anleitung für Vorlagen]: https://msdn.microsoft.com/library/dn530748.aspx
[APNS-Richtlinien]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[GCM-Richtlinien]: http://developer.android.com/google/gcm/adv.html
[Export/Import Registrations]: http://msdn.microsoft.com/library/dn790624.aspx
[Service Bus-Explorer]: http://msdn.microsoft.com/library/dn530751.aspx
[Service Bus Explorer-Code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Übersicht über Visual Studio Server-Explorer]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[Visual Studio Server Explorer-Blogbeitrag - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Visual Studio Server Explorer-Blogbeitrag - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[Programmgesteuerter Telemetriezugriff]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Telemetriezugriff über APIs – Beispiel]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

 

<!---HONumber=AcomDC_0302_2016-->