<properties pageTitle="Get started with Mobile Services with PhoneGap | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for PhoneGap development for iOS, Android, and Windows Phone." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

[Windows Store](/en-us/documentation/articles/mobile-services-windows-store-get-started "Windows Store") [Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started "Windows Phone") [iOS](/en-us/documentation/articles/mobile-services-ios-get-started "iOS") [Android](/en-us/documentation/articles/mobile-services-android-get-started "Android") [HTML](/en-us/documentation/articles/mobile-services-html-get-started "HTML") [Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started "Xamarin.iOS") [Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started "Xamarin.Android") [Sencha](/en-us/documentation/articles/partner-sencha-mobile-services-get-started/ "Sencha") [PhoneGap](/en-us/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/ "PhoneGap")

Erste Schritte mit Mobile Services
==================================

In diesem Lernprogramm erfahren Sie, wie Sie einen cloudbasierten Backend-Dienst mithilfe von Azure Mobile Services zu einer App hinzufügen. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App, die App-Daten im neuen mobilen Dienst speichert.

Unten sehen Sie einen Screenshot aus der fertigen App:

![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png)

### Zusätzliche Anforderungen:

Dieses Lernprogramm erfordert die PhoneGap-Tools (v3.2+ für Windows Phone 8-Projekte erforderlich).

PhoneGap unterstützt die Entwicklung für verschiedene Plattformen. Zusätzlich zu den eigentlichen PhoneGap-Tools müssen Sie die Tools für jede geplante Plattform installieren:

-   Windows Phone: Installieren Sie [Visual Studio 2012 Express für Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374)
-   iOS: Installieren Sie [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) (v4.4+ erforderlich)
-   Android: Installieren Sie die [Android Developer Tools](https://go.microsoft.com/fwLink/p/?LinkID=280125)
    (Das Mobile Services SDK für Android unterstützt Apps für Android 2.2 oder höhere Versionen. Android 4.2 oder höher ist zum Ausführen der Schnellstart-App erforderlich.)

Erstellen eines neuen mobilen Diensts
-------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

Erstellen einer neuen AppErstellen einer neuen PhoneGap-App
-----------------------------------------------------------

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern.

In diesem Abschnitt erstellen Sie eine neue PhoneGap-App, die mit Ihrem Mobile Service verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf der Schnellstartregisterkarte auf **PhoneGap** unter **Plattform auswählen**, und erweitern Sie die Option **Neue PhoneGap-App erstellen**.

      ![][0]

      Dadurch werden drei einfache Schritte zum Erstellen einer PhoneGap-App angezeigt, die mit dem mobilen Dienst verbunden wird.

      ![](./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png)

1.  Falls Sie es nicht bereits getan haben, laden Sie PhoneGap sowie mindestens eines der Plattform-Entwicklungstools (Windows Phone, iOS oder Android) herunter und installieren Sie sie.

2.  Klicken Sie auf **TodoItems-Tabelle erstellen**, um eine Tabelle zum Speichern der App-Daten zu erstellen.

3.  Klicken Sie unter **Ihre App herunterladen und ausführen** auf **Download**.

    Dadurch wird das Projekt für die Beispielanwendung der *To-do-Liste*, die mit dem mobilen Dienst verbunden ist, zusammen mit dem Mobile Services JavaScript SDK heruntergeladen. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

Ausführen der neuen PhoneGap-App
--------------------------------

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1.  Navigieren Sie zum Speicherort der komprimierten Projektdateien, und entpacken Sie die Dateien auf Ihrem Computer.

2.  Öffnen Sie das Projekt und führen Sie es entsprechend den unten angegebenen Anweisungen für jede Plattform aus.

    -   **Windows Phone 8**

    1.  Windows Phone 8: Öffnen Sie die SLN-Datei im Ordner **platforms\\wp8** in Visual Studio 2012 Express für Windows Phone.

    2.  Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

      ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png)

    -   **iOS**

    1.  Öffnen Sie das Projekt im Ordner **platforms/ios** in Xcode.

    2.  Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und die App im iPhone-Emulator zu starten, was die Voreinstellung bei diesem Projekt darstellt.

      ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png)

    -   **Android**

        1.  Klicken Sie in Eclipse auf **Datei**, dann auf **Importieren**, und erweitern Sie dann **Android**. Klicken Sie auf **Existing Android Code into Workspace** und dann auf **Weiter.**

        2.  Klicken Sie auf **Durchsuchen**, navigieren Sie zum Speicherort der entpackten Projektdateien, klicken Sie auf **OK**, stellen Sie sicher, dass das TodoActivity-Projekt ausgewählt ist, und klicken Sie dann auf **Fertig stellen**. <p\>Daraufhin werden die Projektdateien in den aktuellen Arbeitsbereich importiert.<p\>

        3.  Klicken Sie im Menü **Ausführen** auf **Ausführen**, um das Projekt im Android-Emulator zu starten.

            ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png)

        > [WACOM.NOTE]Sie müssen mindestens ein Android Virtual Device (AVD) definieren, um das Projekt im Android-Emulator auszuführen. Verwenden Sie den AVD Manager, um diese Geräte zu erstellen und zu verwalten.

3.  Nachdem Sie die App in einem der oben angegebenen mobilen Emulatoren gestartet haben, geben Sie Text in das Textfeld ein und klicken auf **Hinzufügen**.

    Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die **TodoItem**-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

    **Wichtig**

    Änderungen an diesem Plattformprojekt werden überschrieben, falls das Hauptprojekt mit den PhoneGap-Tools neu erstellt wird. Nehmen Sie die Änderungen stattdessen im www-Stammverzeichnis des Projekts vor, wie im Abschnitt unten dargestellt.

4.  Zurück im Verwaltungsportal klicken Sie auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

    ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png)

    Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

    ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png)

Erstellen von App-Aktualisierungen und Neuerstellung von Projekten für jede Plattform
-------------------------------------------------------------------------------------

1.  Nehmen Sie Änderungen an den Codedateien im Verzeichnis "www" vor, in diesem Fall "todolist/www".

2.  Stellen Sie sicher, dass alle Tools der Zielplattform im Systempfad verfügbar sind.

3.  Öffnen Sie im Stammverzeichnis des Projekts eine Eingabeaufforderung, und führen Sie einen der folgenden plattformspezifischen Befehle aus:

    -   **Windows Phone**

        Geben Sie an der Eingabeaufforderung von Visual Studio Developer Folgendes ein:

              phonegap local build wp8

    -   **iOS**

        Öffnen Sie das Terminal, und führen Sie den folgenden Befehl aus:

             phonegap local build ios

    -   **Android**

        Öffnen Sie eine Eingabeaufforderung oder ein Terminalfenster, und führen Sie den folgenden Befehl aus:

              phonegap local build android

4.  Öffnen Sie jedes Projekt in der entsprechenden Entwicklungsumgebung, wie im vorhergehenden Abschnitt beschrieben.

> [WACOM.NOTE]Sie können den Code überprüfen, der auf Ihren mobilen Dienst zum Abfragen und Einfügen von Daten zugreift. Der Code befindet sich in der Datei "js/index.js".

Nächste Schritte
----------------

Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

-   [Erste Schritte mit Daten](/en-us/documentation/articles/mobile-services-html-get-started-data)
    <br/>Weitere Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

-   [Erste Schritte mit der Authentifizierung](/en-us/documentation/articles/mobile-services-html-get-started-users)
    <br/>Informationen zur Authentifizierung von Benutzern Ihrer App bei einem Identitätsanbieter.

<!-- Images. -->
[0]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
[1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
[2]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
[3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
[4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png

<!-- URLs. -->
[Get started with data]: /en-us/documentation/articles/mobile-services-html-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-html-get-started-users
[Get started with push notifications]: /en-us/develop/mobile/tutorials/mobile-services-html-get-started-push
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Management Portal]: https://manage.windowsazure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374

