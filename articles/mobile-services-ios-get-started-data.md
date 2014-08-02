<properties linkid="develop-mobile-tutorials-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Erste Schritte mit Daten in Mobile Services
===========================================

[Windows Store C#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer iOS-App nutzen können. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.

**Hinweis**

Anhand dieses Lernprogramms soll Ihnen ein besseres Verständnis über die Mobile Services dahingehend vermittelt werden, wie Sie mit deren Hilfe Azure verwenden können, um Daten von einer iOS-App abzurufen und zu speichern. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart für mobile Dienste abgenommen werden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie eventuell zuerst das Lernprogramm [Erste Schritte mit Mobile Services](/en-us/develop/mobile/tutorials/get-started-ios) abschließen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte behandelt:

1.  [Herunterladen des iOS-App-Projekts](#download-app)
2.  [Erstellen des mobilen Dienstes](#create-service)
3.  [Erstellen einer Datentabelle als Datenspeicher](#add-table)
4.  [Aktualisieren der App zur Verwendung von Mobile Services](#update-app)
5.  [Testen der App mit Mobile Services](#test-app)

Dieses Lernprogramm erfordert das [Mobile Services iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533) und [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) sowie iOS 5.0 oder neuere Versionen.

**Hinweis**

Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenloses Azure-Testkonto](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F).

Herunterladen des ProjektsHerunterladen des GetStartedWithData-Projekts
-----------------------------------------------------------------------

Dieses Lernprogramm baut auf der [GetStartedWithData-App](http://go.microsoft.com/fwlink/p/?LinkId=268622) auf, bei der es sich um eine iOS-App handelt. Die Benutzeroberfläche dieser App und die vom Mobile Services-iOS-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1.  Laden Sie die GetStartedWithData-[Beispiel-App](http://go.microsoft.com/fwlink/p/?LinkId=268622) herunter.

2.  Öffnen Sie in Xcode das heruntergeladene Projekt, und prüfen Sie die Datei "QSTodoService.m".

   	Beachten Sie, dass acht **// TODO**-Kommentare vorhanden sind. Diese geben die von Ihnen durchzuführenden Schritte an, damit diese App mit dem mobilen Dienst funktionieren kann.

3.  Klicken Sie auf die Schaltfläche **Ausführen** (oder drücken Sie die Taste Command+R), um das Projekt neu zu erstellen und die App zu starten.

4.  Geben Sie in der App einen Text in das Textfeld ein. Klicken Sie anschließend auf die Schaltfläche **+**.

   	![](./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png)

   	Beachten Sie, dass der gespeicherte Text in der unteren Liste angezeigt wird.

Erstellen eines mobilen DienstesErstellen eines neuen mobilen Dienstes im Verwaltungsportal
-------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Erstellen einer neuen TabelleHinzufügen einer neuen Tabelle zum mobilen Dienst
------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

Aktualisieren der AppAktualisieren der App für den Datenzugriff über mobile Dienste
-----------------------------------------------------------------------------------

Ihr mobiler Dienst ist nun bereit und Sie können die App aktualisieren, sodass dieser Elemente im mobilen Dienst anstatt in einer lokalen Sammlung speichert.

1.  Wenn Sie das [Mobile Services iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533) noch nicht installiert haben, können Sie es nun installieren.

2.  Öffnen Sie im Project Navigator unter Xcode sowohl die Datei "TodoService.m" als auch die Datei "TodoService.h", die sich im Schnellstart-Ordner befinden. Fügen Sie außerdem den folgenden Import-Ausdruck hinzu:

         #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3.  Ermitteln Sie in der Datei "ToDoService.h" die folgende kommentierte Codezeile:

        // Create an MSClient property comment in the #interface declaration for the TodoService. 

   	Fügen Sie nach diesem Kommentar die folgende Codezeile hinzu:

        @property (nonatomic, strong)   MSClient *client;

   	Dadurch wird eine Eigenschaft erstellt, die den MSClient repräsentiert, welcher eine Verbindung zu dem Dienst herstellt.

4.  Ermitteln Sie in der Datei "TodoService.m" die folgende kommentierte Codezeile:

        // Create an MSTable property for your items.  

   	Fügen Sie nach diesem Kommentar die folgende Codezeile innerhalb der @interface-Deklaration hinzu:

        @property (nonatomic, strong)   MSTable *table;

   	Dadurch wird eine Eigenschaftsdarstellung für die Tabelle der mobilen Dienste erstellt.

5.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

6.  Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Site URL**. Klicken Sie anschließend auf **Schlüssel verwalten**, und notieren Sie sich den **Anwendungsschlüssel**.

   	![](./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png)

  	Sie benötigen diese Werte beim Zugriff auf den mobilen Dienst von Ihrem App-Code aus.

1.  Öffnen Sie erneut unter Xcode die Datei "TodoService.m", und ermitteln Sie die folgende kommentierte Codezeile:

         // Initialize the Mobile Service client with your URL and key.

    Fügen Sie nach diesem Kommentar die folgende Codezeile hinzu:

         self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    Hierdurch wird eine Instanz des Mobile Services-Client erstellt.

2.  Ersetzen Sie die Werte von **APPURL** und **APPKEY** in diesem Code durch die URL und den Anwendungsschlüssel aus dem mobilen Dienst, den Sie in Schritt 6 erworben haben.

3.  Ermitteln Sie die folgende kommentierte Codezeile:

         // Create an MSTable instance to allow us to work with the TodoItem table.

    Fügen Sie nach diesem Kommentar die folgende Codezeile hinzu:

         self.table = [self.client tableWithName:@"TodoItem"];

    Hierdurch wird die TodoItem-Tabelleninstanz erstellt.

4.  Ermitteln Sie die folgende kommentierte Codezeile:

 	    // Erstellen Sie ein Prädikat, welches Einträge mit "complete is false"-Kommentar (Abschluss ist falsch) in der Methode "refreshDataOnSuccess" ermitteln kann.

    Fügen Sie nach diesem Kommentar die folgende Codezeile hinzu:

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    Hierdurch wird eine Abfrage zur Rückgabe sämtlicher Aufgaben gestellt, die noch nicht abgeschlossen wurden.

1.  Ermitteln Sie die folgende kommentierte Codezeile:

        // Query the TodoItem table and update the items property with the results from the service.

   	Ersetzen Sie diesen Kommentar und den nachfolgenden **Fertigstellung**-Blockaufruf durch den folgenden Code:

        // Query the TodoItem table and update the items property with the results from the service
        [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error) 
        {
           self.items = [results mutableCopy];
           completion();
        }]; 

1.  Ermitteln Sie die Methode **addItem**, und ersetzen Sie den Text der Methode durch den folgenden Code:

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];
         
            // Let the caller know that we finished
            completion(index);
        }];

    Dieser Code sendet eine Einfügeanforderung an den mobilen Dienst.

2.  Ermitteln Sie die Methode **completeItem**, und ersetzen Sie den Text der Methode durch den folgenden Code:

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {
    
            // TODO
            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];
    
            [mutableItems removeObjectAtIndex:index];
    
            // Let the caller know that we have finished
            completion(index);
	    }]; 

   	Durch diesen Code werden die TodoItems entfernt, nachdem diese als abgeschlossen markiert sind.

Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.

Testen der AppTesten der App mit Ihrem neuen mobilen Dienst
-----------------------------------------------------------

1.  Wählen Sie unter Xcode einen Emulator, den Sie bereitstellen (entweder iPhone oder iPad). Klicken Sie auf die Schaltfläche **Ausführen** (oder drücken Sie die Taste Command+R), um das Projekt neu zu erstellen und die App zu starten.

   	Hierdurch wird der Azure Mobile Services-Client ausgeführt und mit dem iOS SDK erstellt, welches Einträge vom mobilen Dienst abfragt.

2.  Geben Sie wie zuvor Text im Textfeld ein, und klicken Sie danach auf die Schaltfläche **+**.

   	Dieser Code schickt ein neues einzufügendes Element an den mobilen Dienst.

3.  Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

   	![](./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png)
  
   	Die **TodoItem**-Tabelle enthält nun Daten, mit von Mobile Services generierten ID-Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** für iOS beendet.

Nächste Schritte
----------------

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer iOS-App für die Arbeit mit Daten in Mobile Services aufgezeigt.

Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Prüfen und Ändern von Daten mit Skripten](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    Informationen zur Verwendung von Serverskripten in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging](/en-us/develop/mobile/tutorials/add-paging-to-data-ios)
    Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie die folgenden weiteren iOS-Lernprogramme ausprobieren:

-   [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-ios)
    Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen](/en-us/develop/mobile/tutorials/get-started-with-push-ios)
    Verschicken Sie mit Mobile Services eine einfache Pushbenachrichtigung an Ihre App.


