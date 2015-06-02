<properties
	pageTitle="Dienstseitige Autorisierung von Benutzern in Mobile Services mit .NET-Back-End | Mobile Dev Center"
	description="Erfahren Sie, wie Sie Benutzer in .NET-Back-End von Azure Mobile Services autorisieren."
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

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-service-side-authorization.md)
- [(Any | Javascript)](mobile-services-javascript-backend-service-side-authorization.md)

In diesem Thema wird veranschaulicht, wie serverseitige Logik verwendet werden kann, um Benutzer zu autorisieren. In diesem Lernprogramm ändern Sie Datenzugriffsmethoden in .NET, filtern Sie Abfragen anhand von Benutzer-IDs, und gewähren Sie Benutzern nur Zugriff auf deren eigene Daten.

Dieses Lernprogramm basiert auf Mobile Services-Schnellstart und setzt auf dem Lernprogramm [Hinzufügen von Authentifizierung zu einer vorhandenen Mobile Services-App] auf. Bearbeiten Sie zunächst [Hinzufügen von Authentifizierung zu einer vorhandenen Mobile Services-App].

## <a name="register-scripts"></a>Ändern von Datenzugriffsmethoden

1. Öffnen Sie in Visual Studio Ihr Projekt für mobile Geräte, erweitern Sie den Ordner „DataObjects“, und öffnen Sie **TodoItem.cs**. Die **TodoItem**-Klasse definiert das Datenobjekt, und Sie müssen eine **UserId**-Eigenschaft für Filterzwecke hinzufügen. Fügen Sie die folgende neue UserId-Eigenschaft zur **TodoItem**-Klasse hinzu:

		public string UserId { get; set; }

	>[AZURE.NOTE]Um eine Datenmodelländerung durchzuführen und bestehende Daten in der Datenbank beizubehalten, müssen Sie [Code First-Migrationen](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md) verwenden.

2. Erweitern Sie in Visual Studio den Ordner „Controllers“, und öffnen Sie **TodoItemController.cs**. Suchen Sie die Methode **PostTodoItem**, und fügen Sie am Anfang der Methode den folgenden Code hinzu: Dieser Code fügt dem Element die Benutzer-ID des authentifizierten Benutzers hinzu, bevor das Element in die TodoItem-Tabelle eingefügt wird.

			// Get the logged in user
			var currentUser = User as ServiceUser;

			// Set the user ID on the item
			item.UserId = currentUser.Id;

3. Suchen Sie die **GetAllTodoItems**-Methode und ersetzen Sie die vorhandene **return**-Anweisung durch die folgende Codezeile: Diese Abfrage filtert die zurückgegebenen TodoItem-Objekte, sodass jeder Benutzer nur die eingefügten Elemente erhält.

				// Get the logged in user
				var currentUser = User as ServiceUser;

				return Query().Where(todo => todo.UserId == currentUser.Id);

4. das Projekt für den mobilen Service erneut auf Azure veröffentlichen.


## <a name="test-app"></a>Testen der App

1. Wenn Sie nun Ihre clientseitige App ausführen, werden keine Elemente zurückgegeben, obwohl es bereits Elemente in der Datenbank gibt. Dies liegt daran, dass die vorherigen Elemente ohne die Spalte für die Benutzer-ID eingefügt wurden und jetzt NULL-Werte haben.

2. Wenn Sie weitere Anmeldekonten haben, sollten Sie sicherstellen, dass Benutzer nur ihre eigenen Daten sehen können. Schließen und löschen Sie dazu die App, und führen Sie sie dann erneut aus. Wenn das Anmeldedialogfeld angezeigt wird, geben Sie andere Anmeldedaten ein, und vergewissern Sie sich, dass die unter den vorherigen Anmeldedaten eingegebenen Elemente nicht angezeigt werden.



<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]: #next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Get started with Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Get started with data]: mobile-services-dotnet-backend-ios-get-started-data.md
[Hinzufügen von Authentifizierung zu einer vorhandenen Mobile Services-App]: mobile-services-dotnet-backend-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-ios-get-started-push.md

[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md

<!--HONumber=54-->