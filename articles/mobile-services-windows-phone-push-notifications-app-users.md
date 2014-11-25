<properties linkid="develop-net-tutorials-push-notifications-to-users-wp8" urlDisplayName="Push Notifications to Users (WP8)" pageTitle="Push notifications to users (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Windows Phone app." metaCanonical="" services="" documentationCenter="" title="Push notifications to users by using Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Pushbenachrichtigungen an Benutzer mithilfe von Mobile Services

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/de-de/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/de-de/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/de-de/develop/mobile/tutorials/push-notifications-to-users-android" title="Android" class="current">Android</a>
</div>

In diesem Thema wird das [vorherige Lernprogramm zu Pushbenachrichtigungen][vorherige Lernprogramm zu Pushbenachrichtigungen] erweitert, indem eine neue Tabelle hinzugefügt wird, die Channel-URIs des Microsoft-Pushbenachrichtigungsdiensts (MPNS) speichert. Diese Channels können anschließend zum Versenden von Pushbenachrichtigungen an Benutzer der Windows Phone 8-App verwendet werden.

Dieses Lernprogramm zeigt Ihnen die entsprechenden Schritte zur Aktualisierung von Pushbenachrichtigungen in Ihrer App:

1.  [Erstellen der Channel-Tabelle][Erstellen der Channel-Tabelle]
2.  [Aktualisieren der App][Aktualisieren der App]
3.  [Aktualisieren von Serverskripts][Aktualisieren von Serverskripts]
4.  [Verifizieren des Pushbenachrichtigungs-Verhaltens][Verifizieren des Pushbenachrichtigungs-Verhaltens]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf und führt das vorherige Lernprogram [Erste Schritte mit Pushbenachrichtigungen][vorherige Lernprogramm zu Pushbenachrichtigungen] weiter fort. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Pushbenachrichtigungen][vorherige Lernprogramm zu Pushbenachrichtigungen] abschließen.

## <a name="create-table"></a>Erstellen einer neuen Tabelle

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

    ![][0]

2.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Erstellen**.

    ![][1]

    Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3.  Behalten Sie die Standardeinstellung **Anybody with the application key** für alle Berechtigungen bei, geben Sie *Channel* in das Feld **Tabellenname** ein, und klicken Sie dann auf den Häkchenknopf.

    ![][2]

    Dadurch wird die Tabelle **Channel** erstellt, in der die Channel-URIs gespeichert werden, über die Pushbenachrichtigungen getrennt von Elementdaten gesendet werden.

Als Nächstes modifizieren Sie die Pushbenachrichtigungs-App zur Speicherung von Daten in dieser neuen Tabelle anstatt in der Tabelle **TodoItem**.

## <a name="update-app"></a>Aktualisieren Ihrer App

1.  Öffnen Sie in Visual Studio 2012 Express für Windows Phone das Projekt aus dem Lernprogramm [Erste Schritte mit Pushbenachrichtigungen][vorherige Lernprogramm zu Pushbenachrichtigungen], öffnen Sie die Datei "MainPage.xaml.cs", und entfernen Sie die Eigenschaft **Channel** aus der Klasse **TodoItem**. Dies sollte folgendermaßen aussehen:

        public class TodoItem
        {
            public int Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

2.  Ersetzen Sie die Ereignishandlermethode **ButtonSave\_Click** mit der Originalversion dieser Methode wie folgt:

        private void ButtonSave_Click(object sender, RoutedEventArgs e)
        {
            var todoItem = new TodoItem { Text = TextInput.Text };
            InsertTodoItem(todoItem);
        }

3.  Fügen Sie folgenden Code hinzu, um eine neue **Channel**-Klasse zu erstellen:

        public class Channel
        {
            public int Id { get; set; }

            [JsonProperty(PropertyName = "uri")]
            public string Uri { get; set; }
        }

4.  Öffnen Sie die Datei "App.xaml.cs", und ersetzen Sie die Methode **AcquirePushChannel** mit folgendem Code:

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }

           IMobileServiceTable<Channel> channelTable = App.MobileService.GetTable<Channel>();
           var channel = new Channel { Uri = CurrentChannel.ChannelUri.ToString() };
           channelTable.InsertAsync(channel);
        }

    Mit diesem Code wird ein Channel in die Channel-Tabelle eingefügt.

## <a name="update-scripts"></a>Aktualisieren von Serverskripts

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **Channel**.

    ![][3]

2.  Klicken Sie unter **Channel** auf die Registerkarte **Skript** und wählen Sie **Einfügen** aus.

    ![][4]

    Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **Channel** eingefügt wird.

3.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

            function insert(item, user, request) {
                var channelTable = tables.getTable('Channel');
                channelTable
                    .where({ uri: item.uri })
                    .read({ success: insertChannelIfNotFound });
                function insertChannelIfNotFound(existingChannels) {
                    if (existingChannels.length > 0) {
                        request.respond(200, existingChannels[0]);
                    } else {
                        request.execute();
                    }
                }
            }

    Dieses Skript überprüft die Tabelle **Channel** auf einen bereits vorhandenen Kanal mit demselben URI. Die Einfügung wird nur vorgenommen, wenn kein übereinstimmender Channel gefunden wurde. Dadurch werden doppelte Channel-Datensätze verhindert.

4.  Klicken Sie auf **TodoItem**, dann auf **Script**, und wählen Sie **Insert** aus.

    ![][5]

5.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });

            function sendNotifications() {
                var channelTable = tables.getTable('Channel');
                channelTable.read({
                    success: function(channels) {
                        channels.forEach(function(channel) {
                            push.mpns.sendFlipTile(channel.uri, {
                                title: item.text
                            }, {
                                success: function(pushResponse) {
                                    console.log("Sent push:", pushResponse);
                                }
                            });
                        });
                    }
                });
            }
        }

    Dieses Einfügeskript sendet eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags) an alle Kanäle, die in der **Channel**-Tabelle gespeichert sind.

## <a name="test-app"></a>Testen der App

1.  Wählen Sie in Visual Studio im Menü **Build** den Befehl **Projektmappe bereitstellen**.

2.  Tippen Sie auf die Kachel namens **TodoList** oder **hello push**, um die App zu starten.

    ![][6]

3.  Geben Sie in der App in das Textfeld den Text "hello push again" ein, und klicken Sie dann auf **Save**.

    ![][7]

    Damit wird eine Einfügeanforderung zum Speichern des hinzugefügten Eintrags an den mobilen Dienst gesendet.

4.  Drücken Sie auf die **Start**-Schaltfläche, um zum Startmenü zurückzukehren.

    ![][8]

    Die Anwendung hat die Pushbenachrichtigung empfangen, und der Titel der Kachel lautet nun **hello push**.

5.  (Optional) Führen Sie die App gleichzeitig auf zwei Geräten aus, und wiederholen Sie den vorherigen Schritt.

    Die Benachrichtigung wird an alle laufenden App-Instanzen gesendet.

## Nächste Schritte

Dies bildet den Abschluss der Lernprogramme, welche die Grundlagen der Arbeit mit Pushbenachrichtigungen veranschaulichen. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Mobile Services: Serverskriptreferenz][Mobile Services: Serverskriptreferenz]
    Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

<!-- anchors -->  

  [vorherige Lernprogramm zu Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-wp8
  [Erstellen der Channel-Tabelle]: #create-table
  [Aktualisieren der App]: #update-app
  [Aktualisieren von Serverskripts]: #update-scripts
  [Verifizieren des Pushbenachrichtigungs-Verhaltens]: #test-app
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-channel-table.png
  [3]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-portal-data-tables-channel.png
  [4]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-channel.png
  [5]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-push2.png
  [6]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push4-wp8.png
  [7]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push5-wp8.png
  [8]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push6-wp8.png
  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-wp8
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-wp8
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/p/?LinkId=262293
