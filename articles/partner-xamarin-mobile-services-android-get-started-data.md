<properties 
	pageTitle="Erste Schritte mit Daten (Xamarin.Android) - Azure Mobile Services" 
	description="Erfahren Sie, wie Sie Daten von Ihrer Azure Mobile Services Xamarin.Android-App speichern und darauf zugreifen." 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="donnam"/>

# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]	

<p>Dieses Thema beschreibt den Einsatz von Azure Mobile Services für die Nutzung von Daten in Xamarin.Android-Apps. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.</p>

> [AZURE.NOTE] Dieses Lernprogramm vermittelt ein besseres Verständnis davon, wie Sie mit Mobile Services in Azure Daten aus einer Xamarin.Android-App speichern und abrufen können. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart für mobile Dienste abgenommen werden. Wenn dies Ihre erste Erfahrung mit mobilen Diensten ist, sollten Sie erwägen, zunächst das Lernprogramm [Erste Schritte mit Mobile Services](/de-de/develop/mobile/tutorials/get-started-xamarin-android) abzuschließen.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des Xamarin.Android-App-Projekts][GitHub] 
2. [Erstellen des mobilen Diensts]
3. [Hinzufügen einer Datentabelle als Speicher]
4. [Aktualisieren der App zur Verwendung von Mobile Services]
5. [Testen der App mit Mobile Services]

> [AZURE.IMPORTANT] Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5"%20target="_blank).

Für dieses Lernprogramm sind [Azure Mobile Services-Komponente], [Xamarin.Android], und Android SDK 4.2 oder eine höhere Version erforderlich. 

> [AZURE.NOTE] Das heruntergeladene GetStartedWithData-Projekt benötigt Android 4.2 oder eine neuere Version als Ziel. Das SDK für mobile Dienste benötigt dagegen nur Android 2.2 oder eine neuere Version.

<h2><a name="download-app"></a>Download des GetStartedWithData-Projekts</h2>

Dieses Lernprogramm basiert auf der [GetStartedWithData-App][GitHub], einer Xamarin.Android-App. Die GUI dieser App ist identisch mit der App aus dem Schnellstart für mobile Dienste in Android, nur werden die hinzugefügten Elemente bei dieser App lokal im Speicher abgelegt. 

1. Laden Sie die Beispiel-App `GetStartedWithData` herunter, und extrahieren Sie die Dateien auf Ihrem Computer. 

2. Klicken Sie in Xamarin Studio auf **Datei** und danach auf **Öffnen**. Navigieren Sie zum GetStartedWithData-Beispielprojekt und wählen Sie **XamarinTodoQuickStart.Android.sln** zum Öffnen aus.

3. Suchen und öffnen Sie die Klasse **TodoActivity**.

   	Beachten Sie die // TODO-Kommentare. Diese geben die von Ihnen durchzuführenden Schritte an, damit diese App mit dem mobilen Dienst funktionieren kann.

5. Klicken Sie im Menü **Ausführen** auf **Starten ohne Debugging**. Anschließend werden Sie aufgefordert, einen Emulator oder ein angeschlossenes USB-Android-Gerät auszuwählen.

	> [AZURE.IMPORTANT] Sie können dieses Projekt entweder auf einem Android-Telefon oder im Android-Emulator ausführen. Für die Ausführung auf einem Android-Telefon müssen Sie einen telefonspezifischen USB-Treiber herunterladen.
	> 
	> Um das Projekt im Android-Emulator auszuführen, müssen Sie mindestens ein virtuelles Android-Gerät (AVD) definieren. Verwenden Sie den AVD Manager, um diese Geräte zu erstellen und zu verwalten.

6. Geben Sie in der App einen sinnvollen Text ein, beispielsweise _Lernprogramm abschließen_, und klicken Sie dann auf **Hinzufügen**.

   	![][13]

   	Der gespeicherte Test wird in einer speicherinternen Sammlung gespeichert und in der folgenden Liste angezeigt.

<h2><a name="create-service"></a>Erstellen eines neuen mobilen Diensts im Verwaltungsportal</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Hinzufügen einer neuen Tabelle zum mobilen Dienst</h2>

Um App-Daten im neuen mobilen Dienst speichern zu können, müssen Sie zuerst eine neue Tabelle erstellen.  

1. Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf die Registerkarte **Daten** und anschließend auf **+Erstellen**.

   	![][5]

   	Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3. Geben Sie unter **Tabellenname** "_TodoItem_" ein, und klicken Sie auf das Kontrollkästchen.

  	![][6]

  	Daraufhin wird eine neue Speichertabelle **TodoItem** mit den Standardberechtigungen erstellt, d. h. jeder Benutzer der App kann auf die Daten in der Tabelle zugreifen und diese ändern. 

    > [AZURE.NOTE] Der gleiche Tabellenname wird in Mobile Services-Quickstart verwendet. Jede Tabelle wird jedoch in einem Schema erstellt, das für einen bestimmten mobilen Dienst gilt. Dadurch soll verhindert werden, dass es zu Datenkollisionen kommt, wenn mehrere mobile Dienste dieselbe Datenbank verwenden.

4. Klicken Sie auf die neue Tabelle **TodoItem**, und überprüfen Sie, dass keine Datenzeilen vorhanden sind.

5. Klicken Sie auf die Registerkarte **Spalten**, und vergewissern Sie sich, dass diese nur eine einzige **id**-Spalte enthält, die automatisch erstellt wurde.

  	Dies ist die Mindestanforderung für eine Tabelle in Mobile Services. 

    > [AZURE.NOTE] Wenn für Ihren mobilen Dienst das dynamische Schema aktiviert ist, werden neue Spalten immer dann automatisch erstellt, wenn JSON-Objekte über einen Einfüge- oder Aktualisierungsvorgang an den mobilen Dienst gesendet werden.

Sie können den neuen mobilen Dienst nun als Datenspeicher für die App verwenden.

<h2><a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste</h2>

Ihr mobiler Dienst ist nun bereit und Sie können die App aktualisieren, sodass dieser Elemente im mobilen Dienst anstatt in einer lokalen Sammlung speichert. 

1. Falls Ihr Components-Ordner noch keinen Eintrag **Azure Mobile Services** enthält, können Sie mit der rechten Maustaste auf **Components** klicken, **Weitere Komponenten abrufen** auswählen und nach **Azure Mobile Services** suchen.

  	Dadurch wird die Mobile Services SDK-Komponente zum Projekt hinzugefügt.

2. Öffnen Sie die Datei **AndroidManifest.xml**, und stellen Sie sicher, dass die folgende Berechtigungszeile vorhanden ist:

		<uses-permission android:name="android.permission.INTERNET" />

  	Dieser Code ermöglicht den Zugriff auf Mobile Services in Azure für die App.

3. Öffnen Sie im **Solution**-Fenster die Klasse **TodoActivity**, und heben Sie die Auskommentierung der folgenden Codezeile auf: 

		using Microsoft.WindowsAzure.MobileServices;
 
4. Wir werden die aktuell verwendete speicherinterne Liste entfernen und durch einen mobilen Dienst ersetzen. Kommentieren Sie in der Klasse **TodoActivity** die folgenden Codezeile aus, in der die **todoItemList**-Liste definiert wird.

		public List<TodoItem> todoItemList = new ArrayList<TodoItem>();

5. Nach Abschluss dieses Schritts zeigt das Projekt Buildfehler an. Suchen Sie nach den verbleibenden drei Stellen, an denen die Variable  `todoItemList` verwendet wird, und kommentieren Sie die entsprechenden Abschnitte aus. 

6. Wir werden nun den mobilen Dienst hinzufügen. Entfernen Sie die Kommentare in den folgenden Codezeilen:

        private MobileServiceClient client; // Mobile Service Client references
        private IMobileServiceTable<TodoItem> todoTable; // Mobile Service Table used to access data   

7. Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

8. Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Site URL**. Klicken Sie anschließend auf **Schlüssel verwalten**, und notieren Sie sich den **Anwendungsschlüssel**.

   	![][8]

  	Sie benötigen diese Werte beim Zugriff auf den mobilen Dienst von Ihrem App-Code aus.

9. Heben Sie die Auskommentierung der folgenden Mitgliedsvariablen in der Klasse **Constants** auf:

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";
        
10. Ersetzen Sie **AppUrl** und **AppKey** in den obigen Variablen durch die Werte aus dem Verwaltungsportal (siehe oben).

11. Heben Sie in der Methode **OnCreate** die Auskommentierung in den folgenden Codezeilen auf, in denen die **MobileServiceClient**-Variable definiert wird:

		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
		client = new MobileServiceClient(
			Constants.ApplicationURL,
			Constants.ApplicationKey).WithFilter(filter);

		// Get the Mobile Service Table instance to use
		todoTable = client.GetTable<TodoItem>();    

  	Dieser Code erstellt eine neue MobileServiceClient-Instanz, die für den Zugriff auf Ihren mobilen Dienst verwendet wird. Außerdem wird die MobileServiceTable-Instanz für die Proxy-Datenspeicherung im mobilen Dienst erstellt.

12. Suchen Sie die ProgressFilter-Klasse am Ende der Datei und entfernen Sie deren Kommentare ebenfalls. Diese Klasse zeigt einen Hinweis  'loading' an, wenn der MobileServiceClient Netzwerkoperationen ausführt.

13. Heben Sie die Auskommentierung dieser Zeilen der **CheckItem**-Methode auf:

		try {
			await todoTable.UpdateAsync(item);
			if (item.Complete)
				adapter.Remove(item);
		} catch (Exception e) {
			CreateAndShowDialog(e, "Error");
		}

   	Dieser Code schickt eine Elementaktualisierung an den mobilen Dienst und entfernt markierte Elemente aus dem Adapter.
    
14. Heben Sie die Auskommentierung dieser Zeilen der **AddItem**-Methode auf:
	
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

15. Heben Sie die Auskommentierung dieser Zeilen der **RefreshItemsFromTableAsync**-Methode auf:

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

1. Klicken Sie im Menü **Ausführen** auf **Starten ohne Debugging**, um das Projekt zu starten. Sie werden aufgefordert, ein existierendes Emulator-Image oder ein angeschlossenes Android-Gerät auszuwählen.

	Dadurch wird Ihre mit dem Xamarin.Android erstellte App ausgeführt, die mithilfe der Clientbibliothek Elemente aus Ihrem mobilen Dienst abfragt.

5. Geben Sie wie zuvor einen sinnvollen Text ein, und klicken Sie auf **Hinzufügen**.

   	Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3. Klicken Sie im [Verwaltungsportal] auf **Mobile Services** und dann auf Ihren mobilen Dienst.

4. Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

   	![][9]
  
   	Beachten Sie, dass die **TodoItem**-Tabelle nun Daten mit von Mobile Services generierten ID-Werten enthält, und dass der Tabelle automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt wurden.

Damit ist das Lernprogramm **Erste Schritte mit Daten** für Xamarin.Android abgeschlossen.

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
[Erstellen des mobilen Diensts]: #create-service
[Hinzufügen einer Datentabelle als Speicher]: #add-table
[Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
[Testen der App mit Mobile Services]: #test-app
[Nächste Schritte]:#next-steps

<!-- Images. -->





[5]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-quickstart-startup-android.png

<!-- URLs. TODO: 'Download the Android app project'-Download-Link aktualisieren, 'GitHub', abgeschlossenes Projekt usw. -->
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
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

[Abgeschlossenes Beispielprojekt]: http://go.microsoft.com/fwlink/p/?LinkId=331302




<!--HONumber=42-->
