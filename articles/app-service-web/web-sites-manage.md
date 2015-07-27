<properties 
	pageTitle="Verwalten von Web-Apps über das Azure-Portal" 
	description="Übersicht über die Web-App-Verwaltungsaufgaben in Microsoft Azure mithilfe des Azure-Portals." 
	services="app-service\web" 
	documentationCenter="" 
	authors="MikeWasson" 
	writer="mwasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mwasson"/>

# Verwalten von Web-Apps über das Azure-Portal

Sie verwalten Ihre Web-Apps im [Azure-Portal](https://portal.azure.com) über eine Reihe von *Blättern*, die Container für die unterschiedlichen Komponenten darstellen. (Einen Überblick über das Azure-Portal finden Sie im Änderungshandbuch zum [Portal](http://go.microsoft.com/fwlink/?LinkId=529715).)

Um das Blatt für Ihre Web-App anzuzeigen, klicken Sie auf **Home** und dann auf den Namen der App, oder Sie klicken auf **Durchsuchen**, um alle Ihre Azure-Ressourcen anzuzeigen.

![](./media/web-sites-manage/manage01.png)

Im oberen Teil des Blatts werden Steuerelemente für einige häufig verwendete Aktionen angezeigt:

- **Einstellungen** zeigt eine Liste aller Verwaltungseinstellungen an.

- **Durchsuchen** öffnet die Web-App im Browserfenster.

- Andere Schaltflächen können die App starten oder beenden, Bereitstellungsslots wechseln, die App löschen usw.

**Essentials** zeigt grundlegende Informationen über die App an, wie z. B. URL, Speicherort,[ App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Plan und aktuellem Status (ausgeführt, beendet usw.).

Unter **Essentials** können Sie in verschiedenen Bereichen die App überwachen, verwalten und bereitstellen. Um das Layout des Blatts anzupassen, klicken Sie mit der rechten Maustaste und wählen **Anpassen** oder **Teile hinzufügen** aus. Im weiteren Verlauf dieses Themas werden die Teile im Standardlayout beschrieben.

## Überwachung

Unter **Überwachung** wird ein Diagramm mit Kennzahlen für Ihre Web-App angezeigt. Um das Diagramm zu konfigurieren, klicken Sie auf **Bearbeiten**. Sie können den Zeitraum sowie, die Metriken, die angezeigt werden sollen, auswählen. Verfügbare Metriken sind: Anzahl der Anforderungen, durchschnittliche Reaktionszeit, Serverfehler und CPU-Zeit.

![](./media/web-sites-manage/manage02.png)

Um eine Warnungsregel hinzuzufügen, klicken Sie auf das Diagramm und dann auf **Warnungsregel hinzufügen**. Eine Warnungsregel benachrichtigt Sie, wenn eine bestimmte Metrik einen Schwellenwert erreicht. Beispielsweise können Sie gewarnt werden, wenn die Anzahl der Serverfehler einen bestimmten Wert über einen Zeitraum von 5 Minuten überschreitet.

**Überwachung** enthält auch Bereiche zur Konfiguration von Analysen, zum Einrichten der Anwendungsüberwachung und zum Erstellen von Verfügbarkeitstests. Weitere Informationen finden Sie unter [Grundlagen der Überwachung von Web-Apps in Azure App Service](web-sites-monitor.md).

## Verwendung

![](./media/web-sites-manage/manage03.png)

- **Dateisystemspeicher** zeigt an, wie viel Dateispeicher Ihre App verbraucht.
- **Kontingente** zeigt, wie Ihre App das Ressourcenverwendungskontingent nutzt. Klicken Sie auf diesen Bereich, um Details anzuzeigen.
- **Skalierung** ermöglicht das Skalieren der Anzahl der Instanzen und das Einrichten der automatischen Skalierung. Weitere Informationen finden Sie unter [Skalieren einer Web-App in Azure App Service](../how-to-scale-websites.md).  
- **Geschätzte Ausgaben** zeigt eine Kostenabschätzung für die App an.
- **Preisstufe** ermöglicht das Ändern der Preisstufe.

## Vorgänge

![](./media/web-sites-manage/manage04.png)

- **Ereignisse**. Klicken Sie auf diesen Bereich, um Ereignisprotokolle anzuzeigen.  
- **Warnungsregeln**. Klicken Sie auf diesen Bereich, um Warnregeln anzuzeigen und neue Warnungen hinzuzufügen.
- **Streamingprotokolle** (A). Klicken Sie auf diesen Bereich, um Anwendungsprotokolle anzuzeigen. Um Protokolle zu aktivieren, wechseln Sie zu **Einstellungen** und öffnen das Blatt **Diagnoseprotokolle**. 
- **Konsole**. Klicken Sie auf diesen Bereich, um eine Befehlszeile zu öffnen, die im Portal ausgeführt wird. Sie können damit Befehle wie `mkdir` und `dir` ausführen.  
- **Process Explorer** (B). Klicken Sie auf diesen Bereich, um die in der App ausgeführten Prozesse anzuzeigen, einschließlich Workingset und der Anzahl der Threads.

## Bereitstellung
 
![](./media/web-sites-manage/manage05.png)

- Legen Sie die kontinuierliche Bereitstellung fest. Weitere Informationen finden Sie unter [Bereitstellen von Web-Apps in Azure App Service mit Git](web-sites-publish-source-control.md).
- Bereitstellungsslots. Weitere Informationen finden Sie unter [Bereitstellen von Web-Apps in Azure App Service in Stagingumgebungen](web-sites-staged-publishing.md).
- Legen Sie die Anmeldeinformationen für die Bereitstellung für Git oder FTP fest. 

## Netzwerk

- Verbinden der App mit einem virtuellen Netzwerk
- Hinzufügen von Hybridverbindungen

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Nächste Schritte

- [Skalieren einer Web-App in Azure App Service](../how-to-scale-websites.md)
- [Ausführen von Hintergrundaufgaben mit Webaufträgen](web-sites-create-web-jobs.md)
- [Azure Web-Apps-Sicherungen](web-sites-backup.md) und [Wiederherstellungen](web-sites-restore.md)
- [Grundlagen der Überwachung von Web-Apps in Azure App Service](web-sites-monitor.md)

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=July15_HO3-->