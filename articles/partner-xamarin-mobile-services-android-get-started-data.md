<properties linkid="develop-mobile-tutorials-get-started-with-data-xamarin-android" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Xamarin.Android) - Azure Mobile Services" metaKeywords="Azure Xamarin.Android data, Azure mobile services data" description="Learn how to store and access data from your Azure Mobile Services Xamarin.Android app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services" documentationCenter="Mobile" authors="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Erste Schritte mit Daten in Mobile Services

<div class="dev-center-tutorial-selector sublanding">    
    <a href="/en-us/develop/mobile/tutorials/get-started-with-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

Dieses Thema beschreibt den Einsatz von Azure Mobile Services für die Nutzung von Daten in Xamarin.Android-Apps. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.

<div class="dev-callout"><b>Hinweis</b>
<p>Dieses Lernprogramm vermittelt ein besseres Verst&auml;ndnis davon, wie Sie mit Mobile Services in Azure Daten aus einer Xamarin.Android-App speichern und abrufen k&ouml;nnen. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart f&uuml;r mobile Dienste abgenommen werden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie eventuell zuerst das Lernprogramm <a href="/en-us/develop/mobile/tutorials/get-started-xamarin-android">Erste Schritte mit Mobile Services</a> abschlie&szlig;en.</p>
</div>

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Herunterladen des Xamarin.Android-App-Projekts][]
2.  [Erstellen des mobilen Dienstes][]
3.  [Erstellen einer Datentabelle als Datenspeicher][]
4.  [Aktualisieren der App zur Verwendung von Mobile Services][]
5.  [Testen der App mit Mobile Services][]

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie ben&ouml;tigen ein Azure-Konto, um dieses Lernprogramm auszuf&uuml;hren. Wenn Sie noch kein Konto haben, k&ouml;nnen Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A9C9624B5" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

Dieses Lernprogramm benötigt [Azure Mobile Services Component][], [Xamarin.Android] und das Android SDK 4.2 oder eine neuere Version.

<div class="dev-callout"><b>Hinweis</b>
<p>Das heruntergeladene GetStartedWithData-Projekt ben&ouml;tigt Android 4.2 oder eine neuere Version als Ziel. Das SDK f&uuml;r mobile Dienste ben&ouml;tigt dagegen nur Android 2.2 oder eine neuere Version.</p>
</div>

## <a name="download-app"></a><span class="short-header">Herunterladen des Projekts</span>Herunterladen des GetStartedWithData-Projekts

Dieses Lernprogramm verwendet die Xamarin.Android-App [GetStartedWithData][Herunterladen des Xamarin.Android-App-Projekts]. Die GUI dieser App ist identisch mit der App aus dem Schnellstart für mobile Dienste in Android, nur werden die hinzugefügten Elemente bei dieser App lokal im Speicher abgelegt.

1.  Laden Sie die `GetStartedWithData`-Beispielapp herunter, und entpacken Sie die Dateien auf Ihrem Computer.

2.  Klicken Sie in Xamarin Studio auf **Datei** und danach auf **Öffnen**. Navigieren Sie zum GetStartedWithData-Beispielprojekt und wählen Sie **XamarinTodoQuickStart.Android.sln** zum Öffnen aus.

3.  Suchen und öffnen Sie die Klasse **TodoActivity**

    Beachten Sie die `// TODO::`-Kommentare. Diese geben die von Ihnen durchzuführenden Schritte an, damit diese App mit dem mobilen Dienst funktionieren kann.

4.  Klicken Sie im Menü **Run** auf **Start Without Debugging**. Anschließend werden Sie aufgefordert, einen Emulator oder ein angeschlossenes Android-Gerät auszuwählen.

    <div class="dev-callout"><strong>Hinweis</strong> <p>Sie k&ouml;nnen dieses Projekt entweder auf einem Android-Telefon oder im Android-Emulator ausf&uuml;hren. F&uuml;r die Ausf&uuml;hrung auf einem Android-Telefon m&uuml;ssen Sie einen telefonspezifischen USB-Treiber herunterladen.</p> <p>Um das Projekt im Android-Emulator auszuf&uuml;hren, m&uuml;ssen Sie mindestens ein virtuelles Android-Ger&auml;t (AVD) definieren. Verwenden Sie den AVD Manager, um diese Ger&auml;te zu erstellen und zu verwalten.</p></div>

5.  Geben Sie in der App einen sinnvollen Text ein, beispielsweise *Lernprogramm beenden*, und klicken Sie dann auf **Hinzufügen**.

    ![][]

    Der gespeicherte Test wird in einer speicherinternen Sammlung gespeichert und in der folgenden Liste angezeigt.

## <a name="create-service"></a><span class="short-header">Erstellen eines mobilen Dienstes</span>Erstellen eines neuen mobilen Dienstes im Verwaltungsportal

[WACOM.INCLUDE [mobile-services-create-new-service-data][]]

## <a name="add-table"></a><span class="short-header">Erstellen einer neuen Tabelle</span>Hinzufügen einer neuen Tabelle zum mobilen Dienst

Um App-Daten im neuen mobilen Dienst speichern zu können, müssen Sie zuerst eine neue Tabelle erstellen.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf die Registerkarte **Daten** und anschließend auf **+Erstellen**.

    ![][1]

    Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3.  Geben Sie unter **Tabellenname** den Namen *TodoItem* ein und klicken Sie auf das Kontrollkästchen.

    ![][2]

    Daraufhin wird eine neue Speichertabelle **TodoItem** mit den Standardberechtigungen erstellt, d. h. jeder Benutzer der App kann Daten in der Tabelle abrufen und ändern.

    <div class="dev-callout"> 
<b>Hinweis</b> 
<p>Der gleiche Tabellenname wird in Mobile Services-Quickstart verwendet. Jede Tabelle wird jedoch in einem Schema erstellt, das f&uuml;r einen bestimmten mobilen Dienst gilt. Dadurch soll verhindert werden, dass es zu Datenkollisionen kommt, wenn mehrere mobile Dienste dieselbe Datenbank verwenden.</p> 
</div>

4.  Klicken Sie auf die neue Tabelle **TodoItem** und überprüfen Sie, dass keine Datenzeilen vorhanden sind.

5.  Klicken Sie auf die Registerkarte **Columns** und vergewissern Sie sich, dass diese nur eine einzige **id**-Spalte enthält, die automatisch erstellt wurde.

    Dies ist die Mindestanforderung für eine Tabelle in Mobile Services.

    <div class="dev-callout"><b>Hinweis</b>
<p>Wenn f&uuml;r Ihren mobilen Dienst das dynamische Schema aktiviert ist, werden neue Spalten immer dann automatisch erstellt, wenn JSON-Objekte &uuml;ber einen Einf&uuml;ge- oder Aktualisierungsvorgang an den mobilen Dienst gesendet werden.</p>
</div>

Sie können den neuen mobilen Dienst nun als Datenspeicher für die App verwenden.

## <a name="update-app"></a><span class="short-header">Aktualisieren der App</span>Aktualisieren der App für den Datenzugriff über mobile Dienste

Ihr mobiler Dienst ist nun bereit und Sie können die App aktualisieren, sodass dieser Elemente im mobilen Dienst anstatt in einer lokalen Sammlung speichert.

1.  Falls Ihr Components-Ordner noch keinen **Azure Mobile Service** enthält, können Sie mit der rechten Maustaste auf **Components** klicken, **Get More Components** auswählen und nach **Azure Mobile Services** suchen.

    Dadurch wird die Mobile Services SDK-Komponente zum Projekt hinzugefügt.

2.  Öffnen Sie die Datei **AndroidManifest.xml** und stellen Sie sicher, dass die folgende Berechtigungszeile vorhanden ist:

        <uses-permission android:name="android.permission.INTERNET" />

    Dieser Code ermöglicht den Zugriff auf Mobile Services in Azure für die App.

3.  Öffnen Sie im **Solution**-Fenster die Klasse **TodoActivity** und entfernen Sie den Kommentar vor der folgenden Codezeile:

        using Microsoft.WindowsAzure.MobileServices;

4.  Wir werden die aktuell verwendete speicherinterne Liste entfernen und durch einen mobilen Dienst ersetzen. Kommentieren Sie in der Klasse **TodoActivity** die folgenden Codezeilen, in denen die **todoItemList**-Liste definiert wird.

        public List<TodoItem> todoItemList = new ArrayList<TodoItem>();

5.  Nach Abschluss dieses Schritts zeigt das Projekt Buildfehler an. Suchen Sie nach den verbleibenden drei Orten, an denen die Variable `todoItemList` verwendet wird, und kommentieren Sie die entsprechenden Abschnitte aus.

6.  Wir werden nun den mobilen Dienst hinzufügen. Entfernen Sie die Kommentare in den folgenden Codezeilen:

        private MobileServiceClient client; // Mobile Service Client references
        private IMobileServiceTable<TodoItem> todoTable; // Mobile Service Table used to access data   

7.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

8.  Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Site URL**. Klicken Sie anschließend auf **Schlüssel verwalten**, und notieren Sie sich den **Anwendungsschlüssel**.

    ![][3]

    Sie benötigen diese Werte beim Zugriff auf den mobilen Dienst von Ihrem App-Code aus.

9.  Entfernen Sie die Kommentare der folgenden drei Mitgliedsvariablen in der Klasse **Constants**:

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

15. Entfernen Sie die Kommentare in diesen Zeilen in der **RefreshItemsFromTable**-Methode:

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

## <a name="test-app"></a><span class="short-header">Testen der App</span>Testen der App mit Ihrem neuen mobilen Dienst

1.  Klicken Sie im Menü **Run** auf **Start Without Debugging**, um das Projekt zu starten. Sie werden aufgefordert, ein existierendes Emulator-Image oder ein angeschlossenes Android-Gerät auszuwählen.

    Dadurch wird Ihre mit dem Xamarin.Android erstellte App ausgeführt, die mithilfe der Clientbibliothek Elemente aus Ihrem mobilen Dienst abfragt.

2.  Geben Sie wie zuvor einen lesbaren Text ein und klicken Sie auf **Hinzufügen**.

    Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3.  Klicken Sie im [Verwaltungsportal][] auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

    ![][4]

    Die **TodoItem**-Tabelle enthält nun Daten mit von Mobile Services generierten ID-Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** für Xamarin.Android beendet.

## Abgeschlossenes Beispiel abrufen

Laden Sie das [abgeschlossene Beispielprojekt][Herunterladen des Xamarin.Android-App-Projekts] herunter. Stellen Sie sicher, dass Sie die Variablen **applicationURL** und **applicationKey** mit Ihren eigenen Azure-Einstellungen aktualisieren.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen für die Integration von Daten in Xamarin.Android-Apps in Mobile Services behandelt.

Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Prüfen und Ändern von Daten mit Skripten][]
    Informationen zur Verwendung von Serverskripten in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging][]
    Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie die folgenden weiteren Xamarin.Android-Lernprogramme ausprobieren:

-   [Erste Schritte mit der Authentifizierung][]
    Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen][]
    Verschicken Sie mit Mobile Services eine einfache Pushbenachrichtigung an Ihre App.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->

  [Windows Store C\#]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet "Windows Store C#"
  [Windows Store JavaScript]: /en-us/develop/mobile/tutorials/get-started-with-data-js "Windows Store JavaScript"
  [Windows Phone]: /en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone"
  [iOS]: /en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS"
  [Android]: /en-us/develop/mobile/tutorials/get-started-with-data-android "Android"
  [HTML]: /en-us/develop/mobile/tutorials/get-started-with-data-html "HTML"
  [Xamarin.iOS]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android"
  [Erste Schritte mit Mobile Services]: /en-us/develop/mobile/tutorials/get-started-xamarin-android
  [Herunterladen des Xamarin.Android-App-Projekts]: http://go.microsoft.com/fwlink/p/?LinkId=331302
  [Erstellen des mobilen Dienstes]: #create-service
  [Erstellen einer Datentabelle als Datenspeicher]: #add-table
  [Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
  [Testen der App mit Mobile Services]: #test-app
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A9C9624B5
  [Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
  []: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [1]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-data-tab-empty.png
  [2]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png
  [3]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-dashboard-tab.png
  [Verwaltungsportal]: https://manage.windowsazure.com/
  [4]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
  [Prüfen und Ändern von Daten mit Skripten]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
  [Optimieren von Abfragen mittels Paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android
  [Erste Schritte mit der Authentifizierung]: /en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [Erste Schritte mit Pushbenachrichtigungen]: /en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android
