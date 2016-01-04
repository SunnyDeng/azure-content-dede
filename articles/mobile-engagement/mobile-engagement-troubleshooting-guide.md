<properties 
   pageTitle="Azure Mobile Engagement – Anweisungen zur Problembehandlung" 
   description="Handbuch zur Problembehandlung für Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="10/22/2015"
   ms.author="piyushjo"/>

# Azure Mobile Engagement – Handbuch zur Problembehandlung

## Einführung
Mithilfe dieses Handbuchs zur Problembehandlung können Sie die Ursachen einiger häufig auftretender Probleme ermitteln und die Problembehandlung selbst durchführen.

## Allgemein

Im Allgemeinen sollten Sie immer Folgendes sicherstellen:

1. Stellen Sie sicher, dass Sie alle in den [Erste-Schritte-Lernprogrammen](mobile-engagement-windows-store-dotnet-get-started.md) beschriebenen Schritte ausgeführt haben, die für die Integration erforderlich sind.
2. Sie verwenden die neueste Version der Plattform-SDKs. 
3. Führen Sie Tests auf einem Originalgerät und einem Emulator durch, da bestimmte Probleme nur beim Emulator auftreten. 
4. Es treten keine der Beschränkungen/Drosselungen für Mobile Engagement auf, die [hier](azure-subscription-service-limits.md) dokumentiert sind.
5. Wenn Sie keine Verbindung mit dem Engagement Mobile Service Back-End herstellen können oder festzustellen, dass Daten nicht kontinuierlich geladen werden, vergewissern Sie sich [hier](https://azure.microsoft.com/status/), dass keine aktiven Servicevorfälle vorliegen.

## Probleme beim „Überwachen“

### Mein Gerät wird auf der Registerkarte „Überwachen“ nicht angezeigt
Auf der Registerkarte „Überwachen“ werden die Geräte in Echtzeit angezeigt, die mit der Mobile Engagement-Plattform verbunden sind. Beim Debuggen auf einem Emulator und einem Gerät sollte hier mindestens eine Sitzung angezeigt werden. Wenn die Anwendung verteilt wurde, werden im Indikator „Aktive Sitzungen“ die Geräte in Echtzeit angezeigt, die mit der Plattform verbunden sind.

Wenn Ihr Gerät nicht auf der Registerkarte „Überwachen“ angezeigt wird, ist die Ursache wahrscheinlich ein Problem bei der SDK-Integration. Einige allgemeine Schritte zur Problembehandlung:

1. Stellen Sie sicher, dass Sie die richtige Verbindungszeichenfolge aus dem Abschnitt für die SDK-Schlüssel und nicht die API-Schlüssel in der mobilen App verwenden. Unter Verwendung der Verbindungszeichenfolge wird die Verbindung der mobilen App mit der Instanz der Mobile Engagement-App hergestellt, in der Ihr Gerät auf der Registerkarte „Überwachen“ angezeigt wird. 
2. Windows-Plattform: Wenn die `OnNavigatedTo`-Methode von Ihrer Seite überschrieben wird, vergewissern Sie sich, dass `base.OnNavigatedTo(e)` aufgerufen wird.
3. Wenn Mobile Engagement in eine vorhandene mobile App integriert wird, können Sie sich auch vergewissern, dass Sie keine Schritte ausgelassen haben, indem Sie die [hier](mobile-engagement-windows-store-integrate-engagement.md) aufgeführten erweiterten Integrationsschritte durchgehen.
4. Stellen Sie sicher, dass Sie mindestens einen Bildschirm oder eine Aktivität senden, indem Sie je nach verwendeter Plattform entsprechend der Beschreibung in den [Erste-Schritte-Lernprogrammen](mobile-engagement-windows-store-dotnet-get-started.md) die Seite mit EngagementActivity überschreiben.

### Auf der Registerkarte „Überwachen“ wird eine Sitzung angezeigt, obwohl die Verbindung getrennt wurde oder die App bzw. der Emulator geschlossen wurde. 
Wenn nur Sie derzeit eine Verbindung mit der Plattform hergestellt haben und zum Öffnen der App einen Emulator verwenden, ist die Ursache wahrscheinlich ein Emulatorfehler. Im Allgemeinen müssen Sie sicherstellen, dass Sie zur Startseite auf dem Emulator für die App-Sitzung zurückkehren, damit die Verbindung erfolgreich getrennt wird. Außerdem müssen Sie auf der Windows-Plattform beim Debuggen mit Visual Studio möglicherweise sicherstellen, dass Sie in Visual Studio die Menüleiste **Ereignisse des Lebenszyklus** aufrufen und auf **Anhalten ** klicken, damit die Sitzung wirklich geschlossen wird. Weitere Einzelheiten finden Sie im [Windows-Lernprogramm](mobile-engagement-windows-store-dotnet-get-started.md).

## Probleme bei „Analyse“

### Auf der Registerkarte „Analyse“ werden keine oder veraltete Daten angezeigt 
Analysedaten werden in regelmäßigen Abständen neu berechnet. Diese Aktualisierung kann bis zu 24 Stunden dauern. Es handelt sich nicht um Echtzeitdaten, und die Aktualisierung erfolgt innerhalb eines Zeitraums von 24 Stunden. Stellen Sie jedoch sicher, dass Sie mindestens einen Bildschirm oder eine Aktivität an das Plattform-Back-End senden, indem Sie mindestens eine Seite mit `EngagementActivity` oder durch einen expliziten Aufruf von `SendActivity` überschreiben.

### Auf der Registerkarte „Analyse“ werden für ein Gerät falsch erfasste Datums-/Uhrzeitangaben angezeigt
Der Zeitraum für die Analyse beruht auf dem Datum auf dem Gerät des Benutzers. Daher müssen Sie sich vergewissern, dass das Datum korrekt eingestellt ist.

## Probleme bei „Segment“

### Ich habe ein Segment erstellt, das nun abgeblendet ist oder für das keine Daten angezeigt werden
Die Segmenterstellung erfolgt derzeit nicht in Echtzeit. Sie wird gleichzeitig mit der Zusammenstellung der Analysedaten berechnet und kann daher bis zu 24 Stunden dauern. Überprüfen Sie die Segmentierung später erneut. In der Zwischenzeit sollten Sie jedoch auch sicherstellen, dass Ihre mobilen Apps wirklich die Daten senden, auf denen die Segmentierung beruht. Wenn beispielsweise das Ereignis „foo“ von keinem der mobilen Geräte gesendet wird, stehen keine Segmentdaten für ein Segment zur Verfügung, das mit dem Kriterium „EventName = foo“ erstellt wurde. Außerdem sollten Sie die SDK-Integration überprüfen, um sicherzustellen, dass Ihre mobile App die richtigen Daten sendet.

## Probleme bei „Reach“ oder Pushbenachrichtigungen

### Pushmeldungen werden nicht übermittelt 

1. Versuchen Sie zunächst, Benachrichtigungen an ein Testgerät zu senden, um sicherzustellen, dass alle Komponenten – die mobile App, das SDK und der Dienst – korrekt verbunden sind und Pushbenachrichtigungen übermitteln können. 
2. Senden Sie zunächst immer die einfachste Out-of-App-Benachrichtigung über eine Kampagne, die nicht geplant und für die kein Benutzergruppenkriterium festgelegt wurde. Damit soll wiederum überprüft werden, ob die Benachrichtigungsverbindungen ordnungsgemäß funktionieren. 
3. Wenn bei der Übermittlung von In-App-Benachrichtigungen Probleme auftreten, empfiehlt es sich, als ersten Schritt zu versuchen, zunächst eine Out-of-App-Benachrichtigung zu senden. 
4. Vergewissern Sie sich, dass der „systemeigene Push“ für Ihre mobile App korrekt konfiguriert ist. Je nach Plattform werden dabei entweder Schlüssel (Android, Windows) oder Zertifikate (iOS) verwendet. Weitere Informationen finden Sie unter [Benutzeroberfläche – Einstellungen](mobile-engagement-user-interface-settings.md).
5. Außerdem ist es möglich, dass Out-of-App-Benachrichtigungen vom Benutzer über das mobile Betriebssystem blockiert wurden. Vergewissern Sie sich also, dass dies nicht der Fall ist. 
6. Vergewissern Sie sich außerdem, dass Sie im Bereich **Kampagne** einer Reach-Kampagne nicht die Option *Zielgruppe ignorieren, Push wird über die API an Benutzer gesendet* festgelegt haben, da dies dazu führt, dass Pushbenachrichtigungen nur über APIs gesendet werden können. 
7. Sie müssen Ihre Pushkampagne sowohl mit einem Gerät, das über WLAN verbunden ist, als auch mit einem Gerät testen, das über ein Mobilfunknetz verbunden ist, um die Netzwerkverbindung als mögliche Problemquelle auszuschließen.
8. Stellen Sie sicher, dass Systemdatum und -uhrzeit für den Geräteemulator korrekt eingestellt sind, da ein nicht synchronisiertes Gerät ebenfalls die Übermittlung von Benachrichtigungen durch den Pushbenachrichtigungsdienst beeinträchtigt. 

Im Folgenden finden Sie weitere plattformspezifische Anweisungen zur Problembehandlung:

1. **iOS** 

	- Stellen Sie sicher, dass die Zertifikate für iOS-Pushbenachrichtigungen gültig und nicht abgelaufen sind. 
	- Vergewissern Sie sich, dass Sie ein *Produktionszertifikat* in Ihrer Mobile Engagement-App korrekt konfiguriert haben. 
	- Stellen Sie sicher, dass Sie den Test auf eine *realen, physischen Gerät* durchführen. Der iOS-Simulator kann keine Pushnachrichten verarbeiten.
	- Stellen Sie sicher, dass die Bündel-ID in der mobilen App ordnungsgemäß konfiguriert ist. Anweisungen finden Sie [hier](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6).
	- Verwenden Sie beim Testen die „Ad Hoc“-Verteilung in Ihrem mobile Bereitstellungsprofil. Sie werden nicht benachrichtigt, wenn Ihre App mithilfe von „Debug“ kompiliert wird.

2. **Android**

	- Vergewissern Sie sich, dass Sie in der Datei „AndroidManifest.xml“ Ihrer mobilen App die korrekte Projektnummer gefolgt vom Zeichen „\\n“ angegeben haben. 
	
	    	<meta-data android:name="engagement:gcm:sender" android:value="************\n" />
	    
	- Stellen Sie sicher, dass in der Android-Manifestdatei keine Berechtigungen fehlen oder falsch konfiguriert sind.
	- Wenn Sie Systembenachrichtigungen empfangen, aber keine In-App-Benachrichtigungen, überprüfen Sie den Abschnitt [Angeben eines Symbol für Benachrichtigungen](mobile-engagement-android-get-started.md), da in der Android-Manifestdatei wahrscheinlich nicht das richtige Symbol angegeben ist. 
	- Wenn Sie eine BigPicture-Benachrichtigung senden und über externe Bildserver verfügen, müssen diese "GET" und "HEAD" für HTTP unterstützen.

3. **Windows**
	
	- Stellen Sie sicher, dass Sie die App einer gültigen Windows Store-App zugeordnet ist. In Visual Studio müssen Sie dazu mit der rechten Maustaste auf das Projekt klicken, die Option "Verknüpfen der App mit dem Store" und die im Windows Store erstellte App auswählen. Bei dieser Windows Store-App muss es sich um die gleiche App handeln, von der die systemeigenen Pushanmeldeinformationen stammen, die Sie im Mobile Engagement-Portal konfiguriert haben.
	- Wenn Sie Out-of-App-Pushbenachrichtigungen empfangen, aber keine In-App-Benachrichtigungen über die `EngagementOverlay`-Integration, vergewissern Sie sich, dass ein Stammelement für das Raster auf der Seite vorhanden ist. Für EngagementOverlay wird das erste Grid-Element verwendet, das in der XAML-Datei gefunden wird, um zwei Webansichten auf Ihrer Seite hinzuzufügen. Wenn Sie festlegen möchten, wo Webansichten festgelegt werden, können Sie wie folgt ein Raster mit dem Namen „EngagementGrid“ definieren. Sie müssen dann jedoch sicherstellen, dass die Höhe und Breite für die beiden folgenden Webansichten ausreicht, in denen die Benachrichtigung und die folgende Ankündigung als In-App-Benachrichtigung angezeigt werden:
		
			<Grid x:Name="EngagementGrid"></Grid>

### Ich habe eine Pushbenachrichtigung/Ankündigung/Kampagne erstellt. Nachdem die Benachrichtigung an mich gesendet wurde, wird immer noch „Aktiv“ angezeigt. Was bedeutet das? 
Die **Kampagne**, die Sie in Mobile Engagement erstellt haben, trägt diesen Namen, da es sich um eine auf Dauer ausgeführte Pushbenachrichtigung handelt. Das heißt, wenn neue Geräte eine Verbindung mit der Mobile Engagement-Plattform herstellen, wird die Benachrichtigung, die Sie hier konfigurieren, automatisch an diese gesendet, sofern das in der Kampagne festgelegte Kriterium erfüllt ist. Es handelt sich nicht um eine Konfiguration für eine einmalige Benachrichtigung. Sie müssen manuell auf die Schaltfläche **Fertig stellen** klicken, um die Kampagne zu beenden, damit keine weiteren Benachrichtigungen gesendet werden.

### Ich habe eine Pushkampagne erstellt und erhalte Benachrichtigungen. Bei jedem Öffnen der App erhalte ich jedoch die gleiche Benachrichtigung, obwohl ich bereits auf sie reagiert habe. 
Dieser Fall tritt wahrscheinlich beim Testen auf, wenn Sie Emulatoren oder ein Testframework wie TestFlight verwenden. Bei jeder Ausführung einer App-Instanz wird eine neue DeviceID abgerufen und an das Back-End gesendet. Dies führt dazu, dass sie von der Mobile Engagement-Plattform als neues Gerät behandelt und die Benachrichtigung gesendet wird.

## Support anfordern

Wenn Sie das Problem nicht selbst beheben können, haben Sie folgende Möglichkeiten

1. Suchen Sie in den vorhandenen Threads im StackOverflow-Forum und [MSDN-Forum](https://social.msdn.microsoft.com/Forums/windows/de-DE/home?forum=azuremobileengagement) nach dem Problem. Stellen Sie dort ggf. eine Frage. 
2. Wenn Sie der Meinung sind, dass ein Feature fehlt, stellen Sie im [UserVoice-Forum](http://feedback.azure.com/forums/285737-mobile-engagement) eine Anfrage, oder stimmen Sie für eine Anfrage ab.
3. Wenn Sie zu Microsoft-Support berechtigt sind, stellen Sie eine Supportanfrage mit folgenden Angaben: 
	- Azure-Abonnement-ID
	- Plattform (iOS, Android usw.)
	- App-ID
	- Kampagnen-ID (für Probleme bei Pushbenachrichtigungen)
	- Geräte-ID
	- Mobile Engagement SDK-Version (z. B. „Android SDK v2.1.0“)
	- Fehlerdetails mit der genauen Fehlermeldung und dem Fehlerszenario

<!---HONumber=AcomDC_1217_2015-->