<properties urlDisplayName="Get Started with Data" pageTitle="Erste Schritte mit Daten (Xamarin.Android) - Azure Mobile Services" metaKeywords="Azure Xamarin.Android Daten, Azure mobile services Daten" description="Learn how to store and access data from your Azure Mobile Services Xamarin.Android app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services" documentationCenter="Mobile" authors="donnam" manager="dwrede" services="mobile-services" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="donnam" />

# Hinzufügen von Mobile Services zu einer vorhandenen App

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]	

<p>Dieses Thema beschreibt den Einsatz von Azure Mobile Services für die Nutzung von Daten in Xamarin.Android-Apps. In diesem Lernprogramm laden Sie eine App herunter, speichern die Daten im Speicher, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in die App, und melden sich am Azure-Verwaltungsportal an, um die beim Ausführen der App an den Daten vorgenommenen Änderungen anzuzeigen.</p>

<div class="dev-callout"><b>Hinweis</b>
<p>Dieses Lernprogramm vermittelt ein besseres Verständnis davon, wie Sie mit Mobile Services in Azure Daten aus einer Xamarin.Android-App speichern und abrufen können. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart für mobile Dienste abgenommen werden. Wenn dies Ihre erste Erfahrung mit mobilen Diensten ist, sollten Sie abwägen, zunächst das Lernprogramm <a href="/de-de/develop/mobile/tutorials/get-started-xamarin-android">Erste Schritte mit mobilen Diensten</a> durchzuführen.</p>
</div>

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des Xamarin.Android-App-Projekts][GitHub] 
2. [Erstellen des mobilen Dienstes]
3. [Erstellen einer Datentabelle als Datenspeicher]
4. [Aktualisieren der App zur Verwendung von Mobile Services]
5. [Testen der App mit Mobile Services]

<div class="dev-callout"><strong>Hinweis:</strong> <p>Sie benötigen ein Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A9C9624B5" target="_blank">Kostenlose Azure-Testversion</a>.</p></div> 

Dieses Lernprogramm benötigt [Azure Mobile Services Component], [Xamarin.Android] und das Android SDK 4.2 oder eine höhere Version. 

<div class="dev-callout"><b>Hinweis</b>
<p>Das heruntergeladene GetStartedWithData-Projekt benötigt Android 4.2 oder eine höhere Version als Ziel. Für das Mobile Services SDK ist Android 2.2 oder eine höhere Version erforderlich.</p>
</div>

<h2><a name="download-app"></a>Download des GetStartedWithData-Projekts</h2>

Dieses Lernprogramm verwendet die Xamarin.Android-App [GetStartedWithData][GitHub]. Die GUI dieser App ist identisch mit der App aus dem Schnellstart für mobile Dienste in Android, nur werden die hinzugefügten Elemente bei dieser App lokal im Speicher abgelegt. 

1. Laden Sie die `GetStartedWithData`-Beispielapp herunter und entpacken Sie die Dateien auf Ihrem Computer. 

2. Klicken Sie in Xamarin Studio auf **Datei** und danach auf **Öffnen**. Navigieren Sie zum GetStartedWithData-Beispielprojekt und wählen Sie **XamarinTodoQuickStart.Android.sln** zum Öffnen aus.

3. Suchen und öffnen Sie die Klasse **TodoActivity**

   	Es gibt `// TODO::` -Kommentare. Diese geben die von Ihnen durchzuführenden Schritte an, damit diese App mit dem mobilen Dienst funktionieren kann.

5. Klicken Sie im Menü **Run** auf **Start Without Debugging**Anschließend werden Sie aufgefordert, einen Emulator oder ein angeschlossenes Android-Gerät auszuwählen.

	<div class="dev-callout"><strong>Hinweis</strong> <p>Sie können dieses Projekt entweder auf einem Android-Telefon oder im Android-Emulator ausführen. Für die Ausführung auf einem Android-Telefon müssen Sie einen telefonspezifischen USB-Treiber herunterladen.</p> <p>Um das Projekt im Android-Emulator auszuführen, müssen Sie mindestens ein virtuelles Android-Gerät (AVD) definieren. Verwenden Sie den AVD Manager, um diese Geräte zu erstellen und zu verwalten.</p></div>

6. Geben Sie in der App einen sinnvollen Text ein, beispielsweise _Lernprogramm beenden_, und klicken Sie dann auf **Hinzufügen**.

   	![][13]

   	Der gespeicherte Test wird in einer speicherinternen Sammlung gespeichert und in der folgenden Liste angezeigt.

<h2><a name="create-service"></a>Erstellen eines neuen mobilen Diensts im Verwaltungsportal</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Hinzufügen einer neuen Tabelle zum mobilen Dienst</h2>

Um App-Daten im neuen mobilen Dienst speichern zu können, müssen Sie zuerst eine neue Tabelle erstellen.  

1. Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf die Registerkarte **Daten** und anschließend auf **+Erstellen**.

   	![][5]

   	Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3. Geben Sie in **Tabellenname** den Namen _TodoItem_ ein, und klicken Sie auf die Schaltfläche "Prüfen".

  	![][6]

  	Daraufhin wird eine neue Speichertabelle **TodoItem** mit den Standardberechtigungen erstellt, d. h. jeder Benutzer der App kann Daten in der Tabelle abrufen und ändern. 

    <div class="dev-callout"> 
	<b>Hinweis</b> 
	<p>Der gleiche Tabellenname wird in Mobile Services-Quickstart verwendet. Jede Tabelle wird jedoch in einem Schema erstellt, das für einen bestimmten mobilen Dienst gilt. Auf diese Weise werden Datenkollisionen verhindert, wenn mehrere mobile Dienste dieselbe Datenbank verwenden.</p> 
	</div>

4. Klicken Sie auf die neue **TodoItem**-Tabelle, und vergewissern Sie sich, dass sie keine Datenzeilen enthält.

5. Klicken Sie auf die Registerkarte **Columns** und vergewissern Sie sich, dass diese nur eine einzige **ID**-Spalte enthält, die automatisch erstellt wurde.

  	Dies ist die Mindestanforderung für eine Tabelle in Mobile Services. 

    <div class="dev-callout"><b>Hinweis</b>
	<p>Wenn für Ihren mobilen Dienst das dynamische Schema aktiviert ist, werden neue Spalten immer dann automatisch erstellt, wenn JSON-Objekte über einen Einfüge- oder Aktualisierungsvorgang an den mobilen Dienst gesendet werden.</p>
    </div>

Sie können den neuen mobilen Dienst nun als Datenspeicher für die App verwenden.

<h2><a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste</h2>

Ihr mobiler Dienst ist nun bereit, und Sie können die App aktualisieren, sodass Elemente in Mobile Services anstatt in einer lokalen Sammlung gespeichert werden. 

1. Falls Ihr Components-Ordner noch keinen **Azure Mobile Service** enthält, können Sie mit der rechten Maustaste auf **Components** klicken, **Get More Components** auswählen und nach **Azure Mobile Services** suchen.

  	Dadurch wird die Mobile Services SDK-Komponente zum Projekt hinzugefügt.

2. Öffnen Si die Datei **AndroidManifest.xml**, und stellen Sie sicher, dass die folgende Berechtigungszeile vorhanden ist:

		<uses-permission android:name="android.permission.INTERNET" />

  	Dieser Code ermöglicht den Zugriff auf Mobile Services in Azure für die App.

3. Öffnen Sie im **Solution**-Fenster die Klasse **TodoActivity** und entfernen Sie den Kommentar vor der folgenden Codezeile: 

		using Microsoft.WindowsAzure.MobileServices;
 
4. Wir werden die aktuell verwendete speicherinterne Liste entfernen und durch einen mobilen Dienst ersetzen. Kommentieren Sie in der Klasse **TodoActivity** die folgenden Codezeilen, in denen die **todoItemList**-Liste definiert wird.

		public List<TodoItem> todoItemList = new ArrayList<TodoItem>();

5. Nach Abschluss dieses Schritts zeigt das Projekt Buildfehler an. Suchen Sie nach den verbleibenden drei Orten, an denen die Variable `todoItemList` verwendet wird und kommentieren Sie die entsprechenden Abschnitte aus. 

6. Wir werden nun den mobilen Dienst hinzufügen. Entfernen Sie die Kommentare in den folgenden Codezeilen:

        private MobileServiceClient client; // Mobile Service Client references
        private IMobileServiceTable<TodoItem> todoTable; // Mobile Service Table used to access data   

7. Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

8. Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie die **Site-URL**. Klicken Sie anschließend auf **Schlüssel verwalten,**, und notieren Sie sich den **Anwendungsschlüssel**.

   	![][8]

  	Sie benötigen diese Werte beim Zugriff auf den mobilen Dienst von Ihrem App-Code aus.

9. Entfernen Sie die Kommentare der folgenden Mitgliedsvariablen in der Klasse **Constants**:

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";
        
10. Ersetzen Sie **AppUrl** und **AppKey** in den obigen Variablen durch die Werte aus dem Verwaltungsportal (siehe oben).

11. Entfernen Sie in der Methode **OnCreate** die Kommentare in den folgenden Codezeilen, in denen die **MobileServiceClient**-Variable definiert wird:

		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
		client = new MobileServiceClient(
			Constants.ApplicationURL,
			Constants.ApplicationKey).WithFilter(filter);

		// Get the Mobile Service Table instance to use
		todoTable = client.GetTable<TodoItem>();    

  	Dieser Code erstellt eine neue MobileServiceClient-Instanz, die für den Zugriff auf Ihren mobilen Dienst verwendet wird. Außerdem wird die MobileServiceTable-Instanz für die Proxy-Datenspeicherung im mobilen Dienst erstellt.

12. Suchen Sie die ProgressFilter-Klasse am Ende der Datei und entfernen Sie deren Kommentare ebenfalls. Diese Klasse zeigt einen Hinweis an, wenn der MobileServiceClient Netzwerkoperationen ausführt.

13. Entfernen Sie die Kommentare in diesen Zeilen in der **CheckItem**-Methode:

		try {
			await todoTable.UpdateAsync(item);
			if (item.Complete)
				adapter.Remove(item);
		} catch (Exception e) {
			CreateAndShowDialog(e, "Error");
		}

   	Dieser Code schickt eine Elementaktualisierung an den mobilen Dienst und entfernt markierte Elemente aus dem Adapter.
    
14. Entfernen Sie die Kommentare in diesen Zeilen in der **AddItem**-Methode:
	
		try 
		{
			// Insert the new item
			await todoTable.InsertAsync(item);

			if (!item.Complete) 
				adapter.Add(item);			
		} 
		catch (Exception e) 
		{
			CreateAndShowDialog(e, "Error");
		}   		

  	Dieser Code erstellt ein neues Element und fügt es in die Tabelle im mobilen Dienst ein.

15. 	Entfernen Sie die Kommentare in diesen Zeilen in der **RefreshItemsFromTableAsync**-Methode:

		try {
			// Get the items that weren't marked as completed and add them in the adapter
			var list = await todoTable.Where(item => item.Complete == false).ToListAsync ();

			adapter.Clear();

			foreach (TodoItem current in list)
				adapter.Add(current);
		} 
        catch (Exception e) 
        {
			CreateAndShowDialog(e, "Error");
		}

	Dieser Code fragt den mobilen Dienst ab und gibt alle Elemente zurück, die nicht als abgeschlossen markiert sind. Die Elemente werden für die Bindung zum Adapter hinzugefügt.
		

Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.

<h2><a name="test-app"></a>Testen der App mit dem neuen mobilen Dienst</h2>

1. Klicken Sie im Menü **Run** auf **Start Without Debugging**, um das Projekt zu starten. Sie werden aufgefordert, ein existierendes Emulator-Image oder ein angeschlossenes Android-Gerät auszuwählen.

	Dadurch wird Ihre mit dem Xamarin.Android erstellte App ausgeführt, die mithilfe der Clientbibliothek Elemente aus Ihrem mobilen Dienst abfragt.

5. Geben Sie wie zuvor aussagekräftigen Text ein, und klicken Sie dann auf **Hinzufügen**.

   	Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3. Klicken Sie im [Verwaltungsportal] auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4. Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

   	![][9]
  
   	Die **TodoItem**-Tabelle enthält nun Daten mit von Mobile Services generierten ID-Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** für Xamarin.Android beendet.

## Abgeschlossenes Beispiel abrufen
Laden Sie das [abgeschlossene Beispielprojekt] herunter. Stellen Sie sicher, dass Sie die Variablen **applicationURL** und **applicationKey** mit Ihren eigenen Azure-Einstellungen aktualisieren. 

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen für die Integration von Daten in Xamarin.Android-Apps in Mobile Services behandelt. 

Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

* [Überprüfen und Ändern von Daten mit Skripts]
  <br/>Erfahren Sie mehr über die Verwendung von Serverskripts in Mobile Services, um die von Ihrer App gesendeten Daten zu prüfen und zu ändern.

* [Optimieren von Abfragen mit Paging]
  <br/>Erfahren Sie, wie Sie Paging in Abfragen zum Steuern der in einer einzelnen Anforderung verarbeiteten Datenmengen verwenden können.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie die folgenden weiteren Xamarin.Android-Lernprogramme ausprobieren:

* [Erste Schritte mit der Authentifizierung] 
	<br/>Erfahren Sie, wie Benutzer in Ihrer App authentifiziert werden.

* [Erste Schritte mit Pushbenachrichtigungen] 
  <br/>Erfahren Sie, wie Sie eine ganz einfache Pushbenachrichtigung mit Mobile Services an Ihre App senden können.

<!-- Anchors. -->

[Abrufen der Windows Store-App]: #download-app
[Erstellen des mobilen Dienstes]: #create-service
[Erstellen einer Datentabelle als Datenspeicher]: #add-table
[Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
[Testen der App mit Mobile Services]: #test-app
[Nächste Schritte]:#next-steps

<!-- Images. -->





[5]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-quickstart-startup-android.png

<!-- URLs. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-xamarin-android
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Azure Mobile Services-Komponente]: http://components.xamarin.com/view/azure-mobile-services/
[Herunterladen des Android-App-Projekts]: http://www.google.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[Android-SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

[Abgeschlossenes Beispielprojekt]: http://go.microsoft.com/fwlink/p/?LinkId=331302
