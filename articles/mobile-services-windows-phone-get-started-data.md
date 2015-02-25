<properties pageTitle="Erste Schritte mit Daten (WP8) - Azure Mobile Services" description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Daten aus Ihrer Azure Mobile Services Windows Phone 8-App." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/19/2014" ms.author="glenga"/>


# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer Windows Phone 8-App nutzen können. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="dev-onpage-video"><span class="icon">Video wiedergeben</span></a> <span class="time">12:54</span></div>
</div>

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des Windows Phone 8-App-Projekts]
2. [Erstellen des mobilen Diensts]
3. [Hinzufügen einer Datentabelle als Speicher]
4. [Aktualisieren der App zur Verwendung von Mobile Services]
5. [Testen der App mit Mobile Services]

Dieses Lernprogramm erfordert Visual Studio 2012 Express für Windows Phone 8 und das [Windows Phone 8 SDK] unter Windows 8. Um dieses Lernprogramm zum Erstellen einer Windows Phone 8.1-App abzuschließen, müssen Sie Visual Studio 2013 Update 2 oder eine höhere Version verwenden.

>[AZURE.NOTE]Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Detaillierte Informationen finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-wp8%2F" target="_blank">Kostenlose Azure-Testversion</a>.

##<a name="download-app"></a>Download des GetStartedWithData-Projekts

Dieses Lernprogramm baut auf der Website [GetStartedWithData-App][Codebeispiele für Entwickler]auf, bei der es sich um ein Windows Phone Silverlight 8-App-Projekt handelt.  

1. Laden Sie das GetStartedWithData-App-Beispielprojekt von der [Website mit Codebeispielen für Entwickler] herunter. 

	>[AZURE.NOTE]Zum Erstellen einer Windows Phone Silverlight 8.1-App ändern Sie einfach das Zielbetriebssystem im heruntergeladenen Windows Phone Silverlight 8-App-Projekt in Windows Phone 8.1. Zum Erstellen einer Windows Phone Store-App laden Sie die [Windows Phone Store-App-Version](http://go.microsoft.com/fwlink/p/?LinkId=397372) des App-Beispielprojekts "GetStartedWithData" herunter. 

2. Öffnen Sie in Visual Studio das heruntergeladene Projekt, und sehen Sie sich die Datei "MainPage.xaml.cs" an.

   	Beachten Sie, dass die hinzugefügten **TodoItem**-Objekte in einem speicherinternen **ObservableCollection&lt;TodoItem&gt;**-Element gespeichert werden.

3. Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

4. Geben Sie in der App einen Text in das Textfeld ein, und klicken Sie anschließend auf die Schaltfläche **Speichern**.

   	![][0]  

   	Beachten Sie, dass der gespeicherte Text in der unteren Liste angezeigt wird.

<h2><a name="create-service"></a>Erstellen eines neuen mobilen Diensts im Verwaltungsportal</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Hinzufügen einer neuen Tabelle zum mobilen Dienst</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste</h2>

Ihr mobiler Dienst ist nun bereit und Sie können die App aktualisieren, sodass dieser Elemente im mobilen Dienst anstatt in einer lokalen Sammlung speichert. 

1. Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2. Wählen Sie im linken Bereich die Kategorie **Online**, suchen Sie nach `WindowsAzure.MobileServices`, klicken Sie auf **Installieren** für das **Azure Mobile Services**-Paket, und akzeptieren Sie die Lizenzbedingungen.

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

6. Fügen Sie in der Datei "MainPage.xaml.cs" die folgenden  `using`-Anweisungen hinzu, oder heben Sie deren Auskommentierung auf:

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

7. Kommentieren Sie die Zeile aus, welche die bestehende **items**-Sammlung definiert. Heben Sie anschließend die Auskommentierung für die folgenden Zeilen auf:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
			App.MobileService.GetTable<TodoItem>();

   	Dieser Code erstellt eine mobile dienstunterstützende Bindungssammlung (**items**) und eine Proxyklasse für die SQL-Datenbanktabelle **TodoItem** (**todoTable**). 

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

<h2><a name="test-app"></a>Testen der App mit dem neuen mobilen Dienst</h2>

1. Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2. Geben Sie wie zuvor Text im Textfeld ein, und klicken Sie danach auf die Schaltfläche **Speichern**.

   	Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3. Klicken Sie im [Verwaltungsportal] auf **Mobile Services** und dann auf Ihren mobilen Dienst.

4. Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

   	![][9]
  
   	Beachten Sie, dass die **TodoItem**-Tabelle nun Daten mit von Mobile Services generierten ID-Werten enthält, und dass der Tabelle automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt wurden.

Damit ist das Lernprogramm **Erste Schritte mit Daten** für Windows Phone 8 beendet.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Phone 8-App für die Arbeit mit Daten in Mobile Services aufgezeigt. Als Nächstes können Sie das folgende Lernprogramm ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

* [Überprüfen und Ändern von Daten mit Skripts]
  <br/>Erfahren Sie mehr über die Verwendung von Serverskripts in Mobile Services, um die von Ihrer App gesendeten Daten zu prüfen und zu ändern.

* [Optimieren von Abfragen mit Paging]
  <br/>Erfahren Sie, wie Sie Paging in Abfragen zum Steuern der in einer einzelnen Anforderung verarbeiteten Datenmengen verwenden können.

Sobald Sie die Datenreihe abgeschlossen haben, können Sie sich auch an einem der folgenden Windows Phone 8-Lernprogramme versuchen:

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie, wie Benutzer in Ihrer App authentifiziert werden.

* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Erfahren Sie, wie Sie eine ganz einfache Pushbenachrichtigung mit Mobile Services an Ihre App senden können.
 
<!-- Anchors. -->
[Herunterladen des Windows Phone 8-App-Projekts]: #download-app
[Erstellen des mobilen Diensts]: #create-service
[Hinzufügen einer Datentabelle als Speicher]: #add-table
[Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
[Testen der App mit Mobile Services]: #test-app
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png






[7]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
[8]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-wp8
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-wp8
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-wp8
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-wp8
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-wp8

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Website mit Codebeispielen für Entwickler]:  http://go.microsoft.com/fwlink/p/?LinkId=271146


<!--HONumber=42-->
