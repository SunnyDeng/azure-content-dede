<properties urlDisplayName="Optimistic concurrency" pageTitle="Behandeln von Datenbank-Schreibkonflikten mit vollständiger Parallelität (Windows Store) | Mobile Dev Center" metaKeywords="" writer="wesmc" description="Erfahren Sie, wie Sie Datenbankschreibkonflikte auf dem Server und in Ihrer Windows Store-Anwendung behandeln." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Behandeln von Schreibkonflikten in Datenbanken

<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#">Windows Store C#</a>
<a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a>
<a href="/de-de/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone">Windows Phone</a></div>	

Dieses Lernprogramm beschreibt die Behandlung von Konflikten, die auftreten, wenn zwei oder mehr Clients in denselben Datenbankeintrag in einer Windows Store-App schreiben. In manchen Szenarien können zwei oder mehr Clients gleichzeitig versuchen, dasselbe Element zu bearbeiten. Ohne Konflikterkennung würde der letzte Schreibvorgang alle vorherigen Aktualisierungen überschreiben, selbst wenn dies nicht so gewollt wäre. Azure Mobile Services bietet Unterstützung für die Erkennung und Auflösung dieser Konflikte. In diesem Artikel werden die Schritte beschrieben, mit denen Sie Datenbank-Schreibkonflikte sowohl auf dem Server als auch in Ihrer Anwendung behandeln können.

In diesem Lernprogramm fügen Sie eine Funktion zur Schnellstart-App hinzu, die Konflikte bei Änderungen in der TodoItem-Datenbank behandelt. In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Aktualisieren der Anwendung, um Änderungen zu ermöglichen]
2. [Aktivieren der Konflikterkennung in Ihrer Anwendung]
3. [Testen auf Datenbank-Schreibkonflikte in Ihrer Anwendung]
4. [Automatische Konfliktauflösung in Serverskripts]


Für dieses Lernprogramm ist Folgendes erforderlich:

+ Microsoft Visual Studio 2013 Express für Windows oder eine höhere Version.
+ Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services] abschließen und die JavaScript-Sprachversion des Startprojekts herunterladen. 
+ [Azure-Konto]
+ Windows Azure Mobile Services NuGet-Paket 1.1.5 oder neuer. Führen Sie die folgenden Schritte aus, um die neueste Version herunterzuladen:
	1. Öffnen Sie das Projekt in Visual Studio, klicken Sie mit der rechten Maustaste im Projektmappen-Explorer auf das Projekt, und klicken Sie auf **NuGet-Pakete verwalten**. 


	2. Erweitern Sie **Online**, und klicken Sie auf **Microsoft und .NET**. Geben Sie in das Textfeld für die Suche **WindowsAzure.MobileServices.WinJS** ein. Klicken Sie auf **Install** (Installieren) für das **Windows Azure Mobile Services for WinJS** NuGet-Paket.

		![][20]


 

<h2><a name="uiupdate"></a>Aktualisieren der Anwendung, um Änderungen zu ermöglichen</h2>

In diesem Abschnitt aktualisieren Sie die Benutzeroberfläche, um Änderungen an den Textelementen zu ermöglichen. Die Bindungsvorlage enthält ein Kontrollkästchen und ein Textklassen-Steuerelement für jeden Eintrag in der Datenbanktabelle. Sie können das Textfeld des TodoItems ändern. Die Anwendung bearbeitet das `keydown`-Ereignis, sodass das Element durch Drücken der **Eingabetaste** geändert wird.


1. Öffnen Sie in Visual Studio die JavaScript-Sprachversion des TodoList-Projekts, das Sie im Lernprogramm [Erste Schritte mit Mobile Services] heruntergeladen haben.
2. Öffnen Sie die Datei "default.html" im Projektmappen-Explorer in Visual Studio, ersetzen Sie die `TemplateItem`-Div-Tag-Definition durch das folgende Div-Tag, und speichern Sie Ihre Änderungen. Daraufhin wird ein Textfeldsteuerelement hinzugefügt, sodass Sie den Text eines TodoItem bearbeiten können.

        <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
          <div style="display: -ms-grid; -ms-grid-columns: auto 1fr">
            <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; dataContext: this" />
              <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px">
                <input id="modifytextbox" class="text win-interactive" data-win-bind="value: text; dataContext: this" />
              </div>
          </div>
        </div>


3. Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **js**. Öffnen Sie die Datei "default.js", und ersetzen Sie die `updateTodoItem`-Funktion durch die folgende Definition, sodass geänderte Elemente nicht von der Benutzeroberfläche entfernt werden.

        var updateTodoItem = function (todoItem) {
          // This code takes a freshly completed TodoItem and updates the database. 
          todoTable.update(todoItem);
          };


4. Fügen Sie in der Datei "default.js" den folgenden Ereignishandler für das `keydown`-Ereignis hinzu, sodass das Element durch Drücken der **Eingabetaste** geändert wird.

        listItems.onkeydown = function (eventArgs) {
          if (eventArgs.key == "Enter") {
            var todoItem = eventArgs.target.dataContext.backingData;
            todoItem.text = eventArgs.target.value;
            updateTodoItem(todoItem);
            }
          };

Die Anwendung schreibt nun die Textänderungen der einzelnen Elemente in die Datenbank, wenn die **Eingabetaste** gedrückt wird.

<h2><a name="enableOC"></a>Aktivieren der Konflikterkennung in Ihrer Anwendung</h2>

Azure Mobile Services unterstützt optimistische Parallelität, indem Änderungen an Elementen in der `__version`-Systemeigenschaftenspalte registriert werden, die jeder Tabelle hinzugefügt wird. In diesem Abschnitt erweitern wir die Anwendung um die Erkennung dieser Schreibkonflikte anhand der `__version`-Systemeigenschaft. Sobald diese Systemeigenschaft in der Datenbanktabelle "todoTable" aktiviert ist, wird die Anwendung durch eine `MobileServicePreconditionFailedException` bei Änderungsversuchen benachrichtigt, falls der Eintrag seit der letzten Abfrage geändert wurde. Anschließend kann die Anwendung entscheiden, ob die Änderung in die Datenbank geschrieben oder die letzte Änderung in der Datenbank erhalten bleiben soll. Weitere Informationen zu Systemeigenschaften für Mobile Services finden Sie unter [Systemeigenschaften].

1. Fügen Sie in der Datei "default.js" unter der Deklaration der `todoTable` -Variable den Code hinzu, um die Unterstützung für die Erkennung von Schreibkonflikten anhand der **__version**-Systemeigenschaft hinzuzufügen.

        var todoTable = client.getTable('TodoItem');
        todoTable.systemProperties |= WindowsAzure.MobileServiceTable.SystemProperties.Version;


2. Wenn Sie die `Version`-Systemeigenschaft zu den Systemeigenschaften der Tabelle hinzufügen, wird die Anwendung durch eine `MobileServicePreconditionFailedException` bei Änderungsversuchen benachrichtigt, falls der Eintrag seit der letzten Abfrage geändert wurde. Diese Ausnahme wird in JavaScript durch eine Fehlerfunktion abgefangen. Der Fehler enthält die neueste Version des Elements auf dem Server, der zum Lösen von Konflikten verwendet wird. Aktualisieren Sie in der Datei "default.js" die `updateTodoItem`-Funktion, um den Fehler abzufangen. Rufen Sie anschließend eine `resolveDatabaseConflict`-Funktion auf.

        var updateTodoItem = function (todoItem) {
          // This code takes a freshly completed TodoItem and updates the database. 
          // If the server version of the record has been updated, we get the updated
          // record from the Precondition Failed error in order to resolve the conflict.
          var serverItem = null;
          todoTable.update(todoItem).then(null, function (error) {
            if (error.message == "Precondition Failed") {
              serverItem = error.serverInstance;
            }
            else {
              var msgDialog =
                new Windows.UI.Popups.MessageDialog(error.request.responseText,"Update Failed");
                msgDialog.showAsync();
            }
          }).done(function () {
            if (serverItem != null)
              resolveDatabaseConflict(todoItem, serverItem);
          });
        };


3. Fügen Sie in der Datei "default.js" die Definition für die `resolveDatabaseConflict()`-Funktion hinzu, auf die in der `updateTodoItem`-Funktion verwiesen wird. Um den Konflikt zu lösen, setzen Sie die Version des lokalen Elements auf die aktualisierte Version vom Server, bevor Sie das Element in der Datenbank ändern. Andernfalls wird der Konflikt weiterhin auftreten.


        var resolveDatabaseConflict = function (localItem, serverItem) {
          var content = "This record has been changed as follows on the server already..\n\n" +
              "id : " + serverItem.id + "\n" +
              "text : " + serverItem.text + "\n" +
              "complete : " + serverItem.complete + "\n\n" +
              "Do you want to overwrite the server instance with your data?";
          var msgDialog = new Windows.UI.Popups.MessageDialog(content, "Resolve Database Conflict");
          msgDialog.commands.append(new Windows.UI.Popups.UICommand("Yes"));
          msgDialog.commands.append(new Windows.UI.Popups.UICommand("No"));
          msgDialog.showAsync().done(function (command) {
              if (command.label == "Yes") {
                  localItem.__version = serverItem.__version;
                  updateTodoItem(localItem);
              }
          });
        }


<h2><a name="test-app"></a>Testen auf Datenbank-Schreibkonflikte in Ihrer Anwendung</h2>

In diesem Abschnitt erstellen Sie ein Windows Store-App-Paket, um die App auf einem zweiten Computer bzw. virtuellen Computer zu installieren. Anschließend werden Sie die App auf beiden Computern ausführen, um den Code mit einem Schreibkonflikt zu testen. Beide Instanzen der App werden versuchen, die `text` -Eigenschaft desselben Elements zu aktualisieren, woraufhin der Benutzer den Konflikt auflösen muss.


1. Erstellen Sie ein Windows Store-App-Paket, um die App auf einem zweiten Computer bzw. virtuellen Computer zu installieren. Klicken Sie dazu auf **Projekt**->**Speichern**->**App-Pakete erstellen** in Visual Studio.

	![][0]

2. Klicken Sie im Bildschirm "Ihre Pakete erstellen" auf **Nein**, da Sie dieses Paket nicht im Windows Store hochladen werden. Klicken Sie auf **Next**.

	![][1]

3. Akzeptieren Sie die Standardwerte im Bildschirm "Pakete auswählen und konfigurieren", und klicken Sie auf **Erstellen**.

	![][10]

4. Klicken Sie im Bildschirm "Paketerstellung abgeschlossen" auf den Link **Ausgabeverzeichnis**, um den Speicherort des Pakets zu öffnen.

   	![][11]

5. Kopieren Sie den Paketordner "todolist_1.0.0.0_AnyCPU_Debug_Test" auf den zweiten Computer. Öffnen Sie auf diesem Computer den Paketordner, und klicken Sie mit der rechten Maustaste auf das **Add-AppDevPackage.ps1** PowerShell-Skript, und klicken Sie auf **Run with PowerShell**, wie unten gezeigt. Folgen Sie den Anweisungen, um die App zu installieren.

	![][12]
  
6. Führen Sie die Instanz 1 der App in Visual Studio aus, indem Sie auf **Debuggen**->**Debuggen starten** klicken. Klicken Sie im Startbildschirm des zweiten Computers auf den Pfeil nach unten, um "Apps by Name" anzuzeigen. Klicken Sie anschließend auf die **todolist**-App, um Instanz 2 der App auszuführen. 

	App-Instanz 1	
	![][2]

	App-Instanz 2	
	![][2]


7. Ändern Sie in Instanz 1 der App den Text des letzten Elements in **Test Write 1**, und drücken Sie anschließend die **Eingabetaste**, um die Datenbank zu aktualisieren. Der folgende Screenshot zeigt ein Beispiel.
	
	App-Instanz 1	
	![][3]

	App-Instanz 2	
	![][2]

8. Der Wert des letzten Elements in Instanz 2 der App ist nun veraltet. Geben Sie in dieser Instanz der App **Test Write 2** für die `text`-Eigenschaft des letzten Elements ein, und drücken Sie die **Eingabetaste**, um die Datenbank mit einer alten `_version`-Eigenschaft zu aktualisieren.

	App-Instanz 1	
	![][4]

	App-Instanz 2	
	![][5]

9. Da der beim Aktualisierungsversuch verwendete `__version` -Wert nicht mit dem `__version`-Wert des Servers übereinstimmt, löst das Mobile Services SDK eine `MobileServicePreconditionFailedException` als Fehler in der `updateTodoItem`-Funktion aus und gibt der App die Möglichkeit, den Konflikt zu lösen. Zum Lösen des Konflikts können Sie auf **Ja** klicken, sodass die Werte von Instanz 2 übernommen werden. Alternativ können Sie auf **Nein** klicken, um die Werte in Instanz 2 zu verwerfen und die Werte aus Instanz 1 in der Datenbank zu behalten. 

	App-Instanz 1	
	![][4]

	App-Instanz 2	
	![][6]



<h2><a name="scriptsexample"></a>Automatische Konfliktauflösung in Serverskripts</h2>

Sie können Schreibkonflikte auch in Serverskripts erkennen und behandeln. Dies macht Sinn, wenn Sie geskriptete Logik anstelle einer Benutzereingabe für die Konfliktauflösung verwenden möchten. In diesem Abschnitt erstellen Sie ein Serverskript für die TodoItem-Tabelle der Anwendung. Die Logik in diesem Skript wird Konflikte wie folgt auflösen:

+  Falls das ` complete`-Feld der TodoItem-Tabelle den Wert "true" hat, gilt das Element als abgeschlossen und `text` kann nicht mehr geändert werden.
+  Falls das ` complete`-Feld der TodoItem-Tabelle den Wert "false" hat, werden Änderungen an `text` in die Datenbank geschrieben.

Führen Sie die folgenden Schritte aus, um ein Serverskript zu erstellen und zu testen.

1. Melden Sie sich beim [Azure-Verwaltungsportal] an. Klicken Sie auf **Mobile Services** und dann auf Ihre App. 

   	![][7]

2. Klicken Sie auf die Registerkarte **Daten**, und klicken Sie dann auf die Tabelle **TodoItem**.

   	![][8]

3. Klicken Sie auf **Skript**, und wählen Sie den Vorgang **Aktualisieren** aus.

   	![][9]

4. Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Speichern**.

		function update(item, user, request) { 
			request.execute({ 
				conflict: function (serverRecord) {
					// Only committing changes if the item is not completed.
					if (serverRecord.complete === false) {
						//write the updated item to the table
						request.execute();
					}
					else
					{
						request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
					}
				}
			}); 
		}   
5. Führen Sie die **todolist**-App auf beiden Computern aus. Ändern Sie den TodoItem-`text` des letzten Elements in Instanz 2, und drücken Sie die **Eingabetaste**, sodass die Datenbank von der App aktualisiert wird.

	App-Instanz 1	
	![][4]

	App-Instanz 2	
	![][5]

6. Geben Sie in Instanz 1 der App einen anderen Wert für die letzte text-Eigenschaft ein, und drücken Sie anschließend die **Eingabetaste**. Die App versucht, die Datenbank mit einer falschen `__version`-Eigenschaft zu aktualisieren.

	App-Instanz 1	
	![][13]

	App-Instanz 2	
	![][14]

7. Beachten Sie, dass keine Ausnahme in der App aufgetreten ist, da der Konflikt vom Serverskript behandelt und die Änderung durchgeführt wurde, da das Element nicht abgeschlossen ist. Klicken Sie in Instanz 2 auf **Aktualisieren**, um die Datenbank erneut abzufragen und sich zu vergewissern, dass die Änderung tatsächlich erfolgreich war.

	App-Instanz 1	
	![][15]

	App-Instanz 2	
	![][15]

8. Klicken Sie in Instanz 1 auf das Kontrollkästchen, um das letzte Todo-Element als abgeschlossen zu markieren.

	App-Instanz 1	
	![][16]

	App-Instanz 2	
	![][15]

9. Versuchen Sie in Instanz 2, den Text des letzten TodoItems zu ändern, und drücken Sie die **Eingabetaste**. Daraufhin wird ein Konflikt verursacht, da eine Änderung vorgenommen wurde und im Feld "Complete" der Wert "true" festgelegt wurde. Das Skript löst den Konflikt, indem die Änderung abgelehnt wird, da das Element bereits abgeschlossen war. Mithilfe des Skripts wurde in der Antwort eine Nachricht zur Verfügung gestellt.  

	App-Instanz 1	
	![][17]

	App-Instanz 2	
	![][18]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Behandlung von Schreibkonflikten in Windows Store-Apps bei der Arbeit mit Daten in Mobile Services gezeigt. Anschließend könnten Sie eines der folgenden Lernprogramme in unserer Datenreihe ausführen:

* [Überprüfen und Ändern von Daten mit Skripts]
  <br/>Erfahren Sie mehr über die Verwendung von Serverskripts in Mobile Services, um die von Ihrer App gesendeten Daten zu prüfen und zu ändern.

* [Optimieren von Abfragen mit Paging]
  <br/>Erfahren Sie, wie Sie Paging in Abfragen zum Steuern der in einer einzelnen Anforderung verarbeiteten Datenmengen verwenden können.

Sobald Sie die Datenreihe abgeschlossen haben, können Sie sich auch an einem der folgenden Windows Store-Lernprogramme versuchen:

* [Erste Schritte mit der Authentifizierung] 
  <br/>Erfahren Sie, wie Benutzer in Ihrer App authentifiziert werden.

* [Erste Schritte mit Pushbenachrichtigungen] 
  <br/>Erfahren Sie, wie Sie eine ganz einfache Pushbenachrichtigung mit Mobile Services an Ihre App senden können.
 
<!-- Anchors. -->
[Aktualisieren der Anwendung, um Änderungen zu ermöglichen]: #uiupdate
[Aktivieren der Konflikterkennung in Ihrer Anwendung]: #enableOC
[Testen auf Datenbank-Schreibkonflikte in Ihrer Anwendung]: #test-app
[Automatische Konfliktauflösung in Serverskripts]: #scriptsexample
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png 
[3]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png

<!-- URLs. -->
[Steuerung für optimistische Parallelität]: http://go.microsoft.com/fwlink/?LinkId=330935
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Azure-Konto]: http://www.windowsazure.com/de-de/pricing/free-trial/
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Optimieren von Abfragen mit Paging]: /de-de/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Website mit Codebeispielen für Entwickler]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[Systemeigenschaften]: http://go.microsoft.com/fwlink/?LinkId=331143
