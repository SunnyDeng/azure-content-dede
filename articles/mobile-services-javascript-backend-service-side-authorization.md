<properties
	pageTitle="Dienstseitige Autorisierung von Benutzern in Mobile Services mit JavaScript-Back-End | Mobile Dev Center"
	description="Erfahren Sie, wie Sie Benutzer im JavaScript-Back-End von Azure Mobile Services autorisieren."
	services="mobile-services"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.topic="article"
	ms.date="2/18/2015"
	ms.author="krisragh"/>

# Dienstseitige Autorisierung von Benutzern in Mobile Services

> [AZURE.SELECTOR-LIST (Plattform | Back-End)]
- [(Alle | .NET)](/de-de/documentation/articles/mobile-services-dotnet-backend-service-side-authorization/)
- [(Alle | JavaScript)](/de-de/documentation/articles/mobile-services-javascript-backend-service-side-authorization/)

In diesem Thema wird veranschaulicht, wie serverseitige Skripts verwendet werden können, um Benutzer zu autorisieren. In diesem Lernprogramm registrieren Sie Skripts für Azure Mobile Services, filtern Sie Abfragen anhand von Benutzer-IDs, und gewähren Sie Benutzern nur Zugriff auf deren eigene Daten.

Dieses Lernprogramm basiert auf Mobile Services-Schnellstart und setzt auf dem Lernprogramm [Hinzufügen von Authentifizierung zu einer vorhandenen Mobile Services-App] auf. Führen Sie zunächst [Hinzufügen von Authentifizierung zu einer vorhandenen Mobile Services-App] aus.

## <a name="register-scripts"></a>Registrieren von Skripts

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst. Klicken Sie auf die Registerkarte **Daten** und anschließend auf die Tabelle **TodoItem**.

2. Klicken Sie auf **Skript**, und wählen Sie dann den Vorgang **Einfügen** aus.

   	![][2]

3. Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Speichern**. Dieses Skript fügt dem Element vor dem Einfügen die Benutzer-ID des authentifizierten Benutzers hinzu.

        function insert(item, user, request) {
          item.userId = user.userId;
          request.execute();
        }


    > [AZURE.NOTE] [Das dynamische Schema muss aktiviert sein](https://msdn.microsoft.com/library/azure/jj193175.aspx), damit dies funktioniert. Diese Einstellung ist für neue mobile Dienste standardmäßig aktiviert.

5. Ersetzen Sie auf gleiche Weise den vorhandenen **Read**-Vorgang durch die folgende Funktion: Dieses Skript filtert die zurückgegebenen TodoItem-Objekte, sodass ein Benutzer nur die Elemente empfängt, die er selbst eingefügt hat.

        function read(query, user, request) {
           query.where({ userId: user.userId });
           request.execute();
        }




## <a name="test-app"></a>Testen der App

1. Wenn Sie nun Ihre clientseitige App ausführen, werden keine Elemente zurückgegeben, obwohl es aus früheren Lernprogrammen bereits Elemente in der _TodoItem_-Tabelle gibt. Dies liegt daran, dass die vorherigen Elemente ohne die Spalte für die Benutzer-ID eingefügt wurden und jetzt NULL-Werte haben. Vergewissern Sie sich, dass die neu hinzugefügten Elemente einen zugehörigen userId-Wert in der _TodoItem_-Tabelle haben.

2. Wenn Sie weitere Anmeldekonten haben, sollten Sie sicherstellen, dass Benutzer nur ihre eigenen Daten sehen können. Schließen und löschen Sie dazu die App, und führen Sie sie dann erneut aus. Wenn das Anmeldedialogfeld angezeigt wird, geben Sie andere Anmeldedaten ein, und vergewissern Sie sich, dass die unter den vorherigen Anmeldedaten eingegebenen Elemente nicht angezeigt werden.

<!-- Anchors. -->
[Registrieren von Serverskripts]: #register-scripts
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->

[Windows-Pushbenachrichtigungen und Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Meine Apps-Dashboard]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Hinzufügen von Mobile Services zu einer vorhandenen App]: /de-de/develop/mobile/tutorials/get-started-with-data-ios
[Hinzufügen von Authentifizierung zu einer vorhandenen Mobile Services-App]: /de-de/develop/mobile/tutorials/get-started-with-users-ios
[Hinzufügen von Pushbenachrichtigungen zu einer vorhandenen App]: /de-de/develop/mobile/tutorials/get-started-with-push-ios

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/

<!--HONumber=45--> 
