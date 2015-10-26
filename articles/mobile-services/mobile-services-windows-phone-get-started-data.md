<properties 
	pageTitle="Hinzufügen von Mobile Services zu einer vorhandenen App (WP8) | Microsoft Azure" 
	description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Daten aus Ihrer Azure Mobile Services Windows Phone 8-App." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/25/2015" 
	ms.author="glenga"/>


# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##Übersicht

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer Windows Phone 8-App nutzen können. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.

Sie können auch eine Demonstration von Nick Harris im folgenden Video anzeigen:
>[AZURE.VIDEO mobile-get-started-with-data-windows-phone]

##Voraussetzungen 

+ Visual Studio 2012 Express für Windows Phone 8 und [Windows Phone 8 SDK] unter Windows 8. Um dieses Lernprogramm zum Erstellen einer Windows Phone 8.1-App abzuschließen, müssen Sie Visual Studio 2013 Update 2 oder eine höhere Version verwenden. 

+ Ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Farticles%2Fdocumentation%2Fmobile-services-windows-phone-get-started-data%2F).

##<a name="download-app"></a>Herunterladen des GetStartedWithData-Projekts

Dieses Lernprogramm baut auf der [GetStartedWithData-App][Developer Code Samples site] auf, bei der es sich um ein Windows Phone Silverlight 8-App-Projekt handelt.

1. Laden Sie das GetStartedWithData-App-Beispielprojekt von der Website mit [Codebeispielen für Entwickler] herunter. 

	>[AZURE.NOTE]Zum Erstellen einer Windows Phone Silverlight 8.1-App ändern Sie einfach das Zielbetriebssystem im heruntergeladenen Windows Phone Silverlight 8-App-Projekt in Windows Phone 8.1. Zum Erstellen einer Windows Phone Store-App laden Sie die [Windows Phone Store-App-Version](http://go.microsoft.com/fwlink/p/?LinkId=397372) des App-Beispielprojekts GetStartedWithData herunter.

2. Öffnen Sie in Visual Studio das heruntergeladene Projekt, und sehen Sie sich die Datei "MainPage.xaml.cs" an.

   	Beachten Sie, dass die hinzugefügten **TodoItem**-Objekte in einem speicherinternen **ObservableCollection&lt;TodoItem&gt;**-Objekt gespeichert werden.

3. Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

4. Geben Sie in der App einen Text in das Textfeld ein. Klicken Sie anschließend auf die Schaltfläche **Speichern**.

   	![][0]

   	Beachten Sie, dass der gespeicherte Text in der unteren Liste angezeigt wird.

##<a name="create-service"></a>Erstellen neuer mobiler Dienste im Verwaltungsportal

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Hinzufügen einer neuen Tabelle zum mobilen Dienst

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste

Ihr mobiler Dienst ist nun bereit, und Sie können die App aktualisieren, sodass Elemente in Mobile Services anstatt in einer lokalen Sammlung gespeichert werden.

1. Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2. Wählen Sie im linken Bereich die Kategorie **Online**, suchen Sie nach `WindowsAzure.MobileServices`, klicken Sie auf **Installieren** für das **Paket Azure Mobile Services**, und akzeptieren Sie die Lizenzbedingungen.

  	![][7]

  	Dadurch wird die Mobile Services-Clientbibliothek zum Projekt hinzugefügt.

3. Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

4. Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Site URL**. Klicken Sie anschließend auf **Schlüssel verwalten**, und notieren Sie sich den **Anwendungsschlüssel**.

   	![][8]

  	Sie benötigen diese Werte beim Zugriff auf den mobilen Dienst von Ihrem App-Code aus.

5. Öffnen Sie in Visual Studio die Datei "App.xaml.cs". Fügen Sie die folgende `using`-Anweisung hinzu, oder heben Sie deren Auskommentierung auf:

       	using Microsoft.WindowsAzure.MobileServices;

6. Heben Sie in derselben Datei die Auskommentierung für den Code auf, der die Variable **MobileService** festlegt, und stellen Sie die URL und den Anwendungsschlüssel aus dem mobilen Dienst im **MobileServiceClient**-Konstruktor bereit. Beachten Sie hierbei die angegebene Reihenfolge.

		//public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

  	Dieser Code erstellt eine neue **MobileServiceClient**-Instanz, die für den Zugriff auf Ihren mobilen Dienst verwendet wird.

6. Fügen Sie in der Datei "MainPage.xaml.cs" die folgenden `using`-Anweisungen hinzu, oder heben Sie deren Auskommentierung auf:

       	using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json;

7. Ersetzen Sie in derselben Datei die **TodoItem**-Klassendefinition durch den folgenden Code:

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

7. Kommentieren Sie die Zeile aus, welche die bestehende **items**-Sammlung festlegt. Heben Sie anschließend die Auskommentierung für die folgenden Zeilen auf:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
			App.MobileService.GetTable<TodoItem>();

   	Dieser Code erstellt eine Bindungssammlung (**items**), die Mobile Services unterstützt, und eine Proxyklasse für die SQL-Datenbanktabelle **TodoItem** (**todoTable**).

7. Entfernen Sie aus der **InsertTodoItem**-Methode die Codezeile, in der die **TodoItem**.**Id**-Eigenschaft festgelegt wird. Fügen Sie der Methode den **async**-Modifizierer hinzu, und heben Sie die Auskommentierung der folgenden Codezeile auf:

        await todoTable.InsertAsync(todoItem);

  	Mit diesem Code wird ein neuer Eintrag in die Tabelle eingefügt.

8. Fügen Sie der **RefreshTodoItems**-Methode den **async**-Modifizierer hinzu, und heben Sie dann die Auskommentierung der folgenden Codezeile auf:

        items = await todoTable.ToCollectionAsync();

   	Damit wird die Bindung an die Sammlung der Einträge in der todoTable festgelegt, die alle vom mobilen Dienst zurückgegebenen TodoItem-Objekte enthält.

9. Fügen Sie der **UpdateCheckedTodoItem**-Methode den **async**-Modifizierer hinzu, und heben Sie die Auskommentierung der folgenden Codezeile auf:

         await todoTable.UpdateAsync(item);

   	Damit wird eine Eintragsaktualisierung an den mobilen Dienst gesendet.

Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.

##<a name="test-app"></a>Testen der App mit dem neuen mobilen Dienst

1. Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2. Geben Sie wie zuvor Text im Textfeld ein, und klicken Sie danach auf die Schaltfläche **Speichern**.

   	Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3. Klicken Sie im [Verwaltungsportal] auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4. Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

   	![][9]
  
   	Die **TodoItem**-Tabelle enthält nun Daten mit von Mobile Services generierten ID-Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

Damit ist das Lernprogramm beendet.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Phone 8-App für die Arbeit mit Daten in Mobile Services aufgezeigt. Lesen Sie danach eines dieser anderen Themen:

* [Hinzufügen von Authentifizierung zur App](mobile-services-windows-phone-get-started-users.md) <br/>Erfahren Sie, wie Sie Benutzer für Ihre App authentifizieren.

* [Hinzufügen von Pushbenachrichtigungen zur App](mobile-services-javascript-backend-windows-phone-get-started-push.md) <br/>Erfahren Sie, wie Sie mit Mobile Services eine einfache Pushbenachrichtigung an Ihre App senden können.

* [Mobile Services #C-Anleitungen: Konzeptionelle Referenz](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.
 
<!-- Anchors. -->
[Download the Windows Phone 8 app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png
[7]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
[8]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. -->

[Azure Management Portal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=271146
[Codebeispielen für Entwickler]: http://go.microsoft.com/fwlink/p/?LinkId=271146
 

<!---HONumber=Oct15_HO3-->