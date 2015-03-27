<properties 
	pageTitle="Erste Schritte mit Azure Mobile Engagement" 
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Engagement mit Analysen und Push-Benachrichtigungen."
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# Erste Schritte mit Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store"  class="current">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

In diesem Thema wird veranschaulicht, wie Sie Azure Mobile Engagement zur richtigen Verwendung der App benutzen und Push-Benachrichtigungen an segmentierte Benutzer einer Windows Store-Anwendung senden. 
In diesem Lernprogramm wird ein einfaches Übertragungsszenario mit Mobile Engagement dargestellt. Sie erstellen eine leere Windows Store-App, die Basisdaten erfasst und Push-Benachrichtigungen mit Windows Notification Service (WNS) empfängt. Nach Abschluss dieses Lernprogramms können Sie Pushbenachrichtigungen an alle Geräte oder zielspezifische Benutzer basierend auf ihren Geräteeigenschaften übertragen. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Mobile Engagement zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren.


Für dieses Lernprogramm ist Folgendes erforderlich:

+ Visual Studio 2013
+ Die [Windows Store-SDK für Mobile Engagement]

> [AZURE.IMPORTANT] Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Engagement-Lernprogramme für Windows Store-Apps. Um es abzuschließen, benötigen Sie ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.

## <a id="setup-azme"></a>Einrichten von Mobile Engagement für Ihre App

1. Melden Sie sich im Azure-Verwaltungsportal an und klicken Sie im unteren Teil des Bildschirms auf **+NEU**.

2. Klicken Sie auf **App-Dienste**, dann auf **Mobile Engagement** und anschließend auf **Erstellen**.

   	![][7]

3. Geben Sie in das Popupfenster, das angezeigt wird, die folgenden Informationen ein:
 
   	![][8]

	1. **Name der Anwendung**: Sie können den Namen der Anwendung eingeben. Sie können jedes beliebige Zeichen verwenden.
	2. **Plattform**: Wählen Sie die Zielplattform für die App (Wenn Ihre App auf mehrere Plattformen ausgerichtet ist, wiederholen Sie dieses Lernprogramm für jede Plattform).
	3. **Ressourcenname der Anwendung**: Dies ist der Name, mit dem Sie über APIs und URLs auf diese Anwendung zugreifen können. Wir empfehlen die ausschließliche Verwendung von konventionellen URL-Zeichen: Der automatisch generierte Name sollte eine sichere Basis bereitstellen. Wir empfehlen auch das Anfügen des Plattformnamens, um einen Namenskonflikt zu vermeiden, da dieser Name eindeutig sein muss.
	4. **Speicherort**: Wählen Sie das Rechenzentrum, in denen diese App (und vor allem die Sammlung - siehe unten) gehostet werden soll.
	5. **Sammlung**: Wenn Sie bereits eine Anwendung erstellt haben, wählen Sie eine zuvor erstellte Sammlung; wählen Sie andernfalls "Neue Sammlung".
	6. **Name der Sammlung**: Dieser steht für die Anwendungsgruppe. Dadurch wird außerdem sichergestellt, dass sich alle Ihre Apps in einer Gruppe befinden, wodurch aggregierte Berechnungen möglich sind. Es wird dringend empfohlen, den Firmennamen oder die Abteilung zu verwenden.


	Wenn Sie fertig sind, klicken Sie auf das Kontrollkästchen, um die Erstellung Ihrer App zu beenden.

4. Klicken Sie auf/wählen Sie die Anwendung aus, die Sie soeben in der Registerkarte **Anwendung** erstellt haben.
 
   	![][9]

5. Klicken Sie dann auf **Verbindungsinformationen**, um die Verbindungseinstellungen für die SDK-Integration anzuzeigen.
 
   	![][10]

6. Notieren Sie abschließend die **Verbindungszeichenfolge**, die Sie benötigen, um diese Anwendung aus dem Anwendungscode zu identifizieren.

   	![][11]

	>[AZURE.TIP] Sie können auch das Symbol "Kopieren" auf der rechten Seite der Verbindungszeichenfolge verwenden, um diese der Einfachheit halber in die Zwischenablage zu kopieren.

## <a id="connecting-app"></a>Verbinden Ihrer App mit dem Mobile Engagement-Back-End

In diesem Lernprogramm wird eine "einfache Integration" dargestellt. Dabei handelt es sich um den minimalen erforderlichen Satz zur Sammlung von Daten und zum Senden einer Pushbenachrichtigung. Die vollständige Dokumentation zur Integration finden Sie in der [Dokumentation Windows Store-SDK für Mobile Engagement].

Wir erstellen eine einfache App mit Visual Studio, um die Integration zu veranschaulichen.

### Erstellen eines neuen Windows Store-Projekts

Wenn Sie bereits über eine App verfügen und mit Windows-Entwicklung vertraut sind, können Sie diesen Schritt überspringen.

1. Starten Sie Visual Studio und wählen Sie im Startbildschirm **Neues Projekt...**.

   	![][12]

2. Wählen Sie in der Popup-Meldung (1)  `Windows Store` und wählen Sie dann (2)  `Leere App (XAML)`.  (3) Geben Sie auch den  `Namen` der App und (4) den `Lösungsnamen` an und klicken Sie dann auf **OK**.

   	![][13]

Das Projekt wird jetzt mit der Demo-App erstellt, in die Mobile Engagement integriert wird.

### Schließen Sie die SDK-Bibliothek in Ihr Projekt ein

Herunterladen und Integrieren der SDK-Bibliothek

1. Laden Sie [Windows Store-SDK für Mobile Engagement] herunter.
2. Extrahieren Sie die ZIP-Datei in einen Ordner auf Ihrem Computer.
3. Gehen Sie zu `PROJECT`, dann zu `NuGet-Pakete verwalten...`
4. Klicken Sie im Popup-Menü auf "Einstellungen"
5. Klicken Sie dann auf die Schaltfläche `+`, um eine neue Datenquelle zu erstellen.

	![][17]

6. Klicken Sie auf die Schaltfläche  `...` unten, um die Quelle auszuwählen und navigieren Sie zum Lib-Ordner des extrahierten SDK-Downloads (siehe Schritt 2), klicken Sie dann auf `Auswählen`

	![][18]

7. Das SDK wird als Quelle hinzugefügt. Klicken Sie auf `OK`. Wählen Sie anschließend das Paket in der Paketliste "Online" und klicken Sie auf "Installieren"

	![][19]


### Verbinden der App mit Mobile Engagement-Back-End mithilfe der Verbindungszeichenfolge

1. Öffnen Sie `Package.appxmanifest` und stellen Sie sicher, dass folgende Deklarationen vorliegen (fügen Sie sie hinzu, falls dies nicht der Fall ist):
		
		Internet (Client)

	![][20]

2. Öffnen Sie in derselben Datei die Registerkarte `Deklarationen`:
	1. Fügen Sie unter `Verfügbare Deklarationen`  `Dateitypzuordnungen` hinzu
	2. Legen Sie dann im Abschnitt `Eigenschaften` auf der rechten Seite den `Namen` auf `engagement_log_file` fest
	3. Legen Sie ebenfalls im Abschnitt `Eigenschaften` auf der rechten Seite den `Dateityp` auf `.set` fest
	4. Fügen Sie abschließend unter `Verfügbare Deklarationen` `Aktualisierung zwischengespeicherter Dateien` hinzu

		![][21]
 
3. Wechseln Sie zurück zum Azure-Portal auf der Seite **Verbindungsinformationen** Ihrer App und kopieren Sie die **Verbindungszeichenfolge**.

	![][11]

4. Fügen Sie sie in die  `Resources\EngagementConfiguration.xml`-Datei zwischen den Tags `<connectionString>` und `</connectionString>` ein:

	![][22]

5. In der  `App.xaml.cs`-Datei:
	1. Fügen Sie die `using`-Anweisung hinzu

			using Microsoft.Azure.Engagement;

	2. Initialisieren Sie das SDK in der  `OnLaunched`-Methode:
			
			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  //... rest of the code
			}

	3. Fügen Sie Folgendes in  `Application_Activated` hinzu (Sie müssen diesen Vorgang hinzufügen, wenn er nicht bereits vorhanden ist.)

			protected override void OnActivated(IActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  //... rest of the code
			}

### Senden eines Bildschirms an Mobile Engagement

Um mit dem Senden von Daten zu beginnen und sicherzustellen, dass die Benutzer aktiv sind, müssen Sie mindestens einen Bildschirm (Aktivität) an das Mobile Engagement-Back-End schicken. Wir erreichen dies durch die Erstellung von Unterklassen unserer  `MainPage` mit der  `EngagementPage`, die durch das Mobile Engagement-SDK bereitgestellt wird.

1. Ersetzen Sie dazu wie unten dargestellt die übergeordnete Klasse der  `MainPage`, also vor  `Page`, mit EngagementPage:

	![][23]

	>[AZURE.NOTE] Vergessen Sie nicht, die Klasse aufzulösen, wenn sie rot unterstrichen angezeigt wird, indem Sie  `using Microsoft.Azure.Engagement;` zu den `using`-Klauseln hinzufügen.

2. In der  `MainPage.xaml`-Datei:
	1. Fügen Sie Ihre Namespace-Deklarationen hinzu:

			xmlns:engagement="using:Microsoft.Azure.Engagement"
	2. Ersetzen Sie die  `-Seite` im XML-Tagname mit `engagement:EngagementPage`

## <a id="monitor"></a>So überprüfen Sie, ob Ihre App mit der Echtzeitüberwachung verbunden ist

In diesem Abschnitt erfahren Sie, wie Sie sicherstellen, dass Ihre App eine Verbindung mit dem Mobile Engagement-Back-End mithilfe der Echtzeitüberwachungsfunktion von Mobile Engagement herstellt.

1. Navigieren Sie zum Mobile Engagement-Portal.

	Stellen Sie im Azure-Portal sicher, dass Sie sich in der App befinden, die für dieses Projekt verwendet wird, und klicken Sie dann unten auf die Schaltfläche**Beginnen**.

	![][26]

2. Sie gelangen zu der Seite "Einstellungen" des Engagement-Portals Ihrer App. Klicken Sie dort auf die Registerkarte **Überwachen**, wie unten dargestellt.
![][30]

3. Die Überwachung ist bereit, in Echtzeit jedes Gerät anzuzeigen, auf dem Ihre App gestartet wird.
![][31]

4. Starten Sie Ihre App in Visual Studio entweder im Emulator oder auf einem verbundenen Gerät, indem Sie auf das grüne Dreieck klicken und dann Ihr Gerät auswählen.

5. Wenn es funktioniert hat, sollte jetzt eine Sitzung in der Überwachung angezeigt werden! 
![][33]

**Herzlichen Glückwunsch!** Sie haben den ersten Schritt dieses Lernprogramms mit einer App, die eine Verbindung mit dem Mobile Engagement-Back-End herstellt, das bereits Daten sendet, erfolgreich abgeschlossen.


## <a id="integrate-push"></a>Aktivieren von Push-Benachrichtigungen

Mit Mobile Engagement können Sie mit Ihren Benutzern interagieren und diese mit Push-Benachrichtigungen und In-App-Nachrichten im Rahmen von Kampagnen ERREICHEN. Dieses Modul nennt sich REACH im Mobile Engagement-Portal.
In den folgenden Abschnitten wird Ihre App für den Empfang eingerichtet.

### Aktivieren Sie Ihre App für den Empfang von WNS-Push-Benachrichtigungen

1. Hinzufügen einer neuen `Dateitypzuordnung` zu Ihrer  `Package.appxmanifest`-Datei im Bereich "Deklarationen":

		Name: engagement_reach_content
		File type: .txt

	![][34]

2. Wählen Sie in der  `Package.appxmanifest`-Datei in der `Application` Registerkarte `Benachrichtigungen`  `Ja` für `toastfähig:`:

	![][35]

### Initialisieren des REICHWEITEN-SDK

1. Rufen Sie in `App.xaml.cs`  `EngagementReach.Instance.Init(args);` in der  `OnLaunched`-Funktion unmittelbar nach der Agent-Initialisierung auf:

		protected override void OnLaunched(LaunchActivatedEventArgs args)
		{
		   EngagementAgent.Instance.Init(args);
		   EngagementReach.Instance.Init(args);
		}

2. Rufen Sie in `App.xaml.cs`  `EngagementReach.Instance.OnActivated(args);` in der  `Application_Activated`-Funktion unmittelbar nach der Agent-Initialisierung auf:

		protected override void OnActivated(IActivatedEventArgs args)
		{
		   EngagementAgent.Instance.OnActivated(args);
		   EngagementReach.Instance.OnActivated(args);
		}

3. Führen Sie die Anwendung aus. 
Sie sind für das Senden eines Toasts bereit. Nun stellen wir sicher, ob Sie diese Basisintegration richtig abgeschlossen haben.

### Gewähren von Zugriff auf Mobile Engagement zum Senden von Benachrichtigungen

1. Sie müssen Ihre App dann dem Windows Store zuweisen, um Ihren `Package security identifier (SID)` und den  `geheimen Schlüssel` (*Client Secret*) zu erhalten.

2. Navigieren Sie zu den Einstellungen Ihres Mobile Engagement-Portals und klicken Sie auf den Abschnitt  `Systemeigener Push` auf der linken Seite

3. Klicken Sie auf die Schaltfläche  `Bearbeiten`, um Ihren  `Paket-Sicherheits-ID (SID)` und Ihren  `geheimen Schlüssel` wie unten dargestellt einzugeben:

	![][36]

## <a id="send"></a>Senden einer Benachrichtigung an Ihre App

Wir erstellen nun eine einfache Push-Benachrichtigungskampagne, die eine Push-Benachrichtigung an die App sendet.

1. Navigieren Sie zu der Registerkarte **REACH** in Ihrem Mobile Engagement-Portal.
2. Klicken Sie auf **Neue Ankündigung**, um die Push-Kampagne zu erstellen.
![][37]

3. Richten Sie das erste Feld der Kampagne mit den folgenden Schritten ein:
![][38]

	1. Wählen Sie einen beliebigen Namen für die Kampagne.
	2. Wählen Sie für die **Lieferzeit**  *Jederzeit*, um eine Benachrichtigung darüber zu erhalten, ob die App gestartet wurde oder nicht.
	3. Geben Sie in den Benachrichtigungstext den Titel ein, der im Push-Vorgang fett gedruckt erscheint
	4. Geben Sie dann Ihre Nachricht ein.

4. Scrollen Sie nach unten und wählen Sie im Inhaltsabschnitt **Nur Benachrichtigung**.
![][39]

5. Sie haben das Festlegen der Basiskampagne abgeschlossen, scrollen Sie nun nach unten und erstellen Sie Ihre Kampagne, um sie zu speichern!
![][40]

6. Letzter Schritt, aktivieren Sie die Kampagne.
![][41]

Auf Ihrem Gerät sollte die Benachrichtigung **Herzlichen Glückwunsch!** angezeigt werden:



<!-- URLs. -->
[Dokumentation Windows Store-SDK für Mobile Engagement]: ../mobile-engagement-windows-store-integrate-engagement/
[Windows Store-SDK für Mobile Engagement]: http://go.microsoft.com/?linkid=9864592

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-store-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-store-get-started/aux-create-app.png
[9]: ./media/mobile-engagement-windows-store-get-started/aux-select-app.png
[10]: ./media/mobile-engagement-windows-store-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-store-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-windows-phone-get-started/new-project.png
[13]: ./media/mobile-engagement-windows-store-get-started/project-properties.png
[17]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source.png
[18]: ./media/mobile-engagement-windows-store-get-started/manage-nuget-settings-new-source-add-lib.png
[19]: ./media/mobile-engagement-windows-store-get-started/manage-nuget-settings-new-source-added.png
[20]: ./media/mobile-engagement-windows-store-get-started/manifest-capabilities.png
[21]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations.png
[22]: ./media/mobile-engagement-windows-store-get-started/add-connection-info.png
[23]: ./media/mobile-engagement-windows-store-get-started/subclass-page.png
[26]: ./media/mobile-engagement-windows-store-get-started/engage-button.png
[27]: ./media/mobile-engagement-windows-store-get-started/engagement-portal.png

[28]: ./media/mobile-engagement-windows-store-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-windows-store-get-started/api-key.png
[30]: ./media/mobile-engagement-windows-store-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-windows-store-get-started/monitor.png
[32]: ./media/mobile-engagement-windows-store-get-started/launch.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations-reach.png
[35]: ./media/mobile-engagement-windows-store-get-started/manifest-toast.png
[36]: ./media/mobile-engagement-windows-store-get-started/enter-credentials.png
[37]: ./media/mobile-engagement-windows-store-get-started/new-announcement.png
[38]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[39]: ./media/mobile-engagement-windows-store-get-started/campaign-content.png
[40]: ./media/mobile-engagement-windows-store-get-started/campaign-create.png
[41]: ./media/mobile-engagement-windows-store-get-started/campaign-activate.png

<!--HONumber=47-->
