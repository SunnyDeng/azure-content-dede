1.  Melden Sie sich beim [Azure-Verwaltungsportal][] an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

    ![][]

2.  Klicken Sie auf die Registerkarte **API** und dann auf **Create a custom API**.

    ![][1]

    Das Dialogfeld **Create a new custom API** wird angezeigt.

3.  Geben Sie *completeall* in das Feld **API-Name** ein, und klicken Sie auf den Häkchenknopf.

    ![][2]

    Dadurch wird die neue API erstellt.

    > [WACOM.NOTE] Es werden Standardberechtigungen eingerichtet, das heißt, jeder Benutzer der App kann die benutzerdefinierte API aufrufen. Der Anwendungsschlüssel wird jedoch nicht verteilt bzw. sicher gespeichert und gilt daher nicht als sichere Anmeldeinformation. Deshalb sollten Sie in Erwägung ziehen, den Zugriff für Vorgänge, durch die Daten geändert werden oder die sich auf den mobilen Service auswirken, auf authentifizierte Benutzer zu beschränken.

4.  Klicken Sie auf den neuen Eintrag **completeall** in der API-Tabelle.

    ![][3]

5.  Klicken Sie auf die Registerkarte **Skript**, ersetzen Sie den vorhandenen Code durch folgenden Code, und klicken Sie anschließend auf **Speichern**:

        exports.post = function(request, response) {
            var mssql = request.service.mssql;
            var sql = "UPDATE todoitem SET complete = 1 " + 
                "WHERE complete = 0; SELECT @@ROWCOUNT as count";
            mssql.query(sql, {
                success: function(results) {            
                    if(results.length == 1)                         
                        response.send(200, results[0]);         
                }
            })
        };

    Dieser Code verwendet das [mssql-Objekt][], um direkt auf die Tabelle **todoitem** zuzugreifen und für alle Elemente das Erledigt-Kennzeichen zu setzen. Da die Funktion **exports.post** verwendet wird, senden die Clients eine POST-Anforderung zur Durchführung des Vorgangs. Die Anzahl der geänderten Zeilen wird an den Client als Ganzzahl zurückgegeben.

> [WACOM.NOTE]
> Das [request][]-Objekt und das [response][]-Objekt, die für die Funktionen der benutzerdefinierten API bereitgestellt werden, werden durch die [Express.js-Bibliothek][] implementiert. Weitere Informationen finden Sie unter [Benutzerdefinierte API][].

Als Nächstes ändern Sie die Quickstart-App so, dass eine neue Schaltfläche und neuer Code hinzugefügt wird, der die neue benutzerdefinierte API asynchron aufruft.

<!-- Anchors. --> 

<!-- Images. --> 

<!-- URLs. -->

  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  []: ./media/mobile-services-create-custom-api/mobile-services-selection.png
  [1]: ./media/mobile-services-create-custom-api/mobile-custom-api-create.png
  [2]: ./media/mobile-services-create-custom-api/mobile-custom-api-create-dialog2.png
  [3]: ./media/mobile-services-create-custom-api/mobile-custom-api-select2.png
  [mssql-Objekt]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554212.aspx
  [request]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554218.aspx
  [response]: http://msdn.microsoft.com/en-us/library/windowsazure/dn303373.aspx
  [Express.js-Bibliothek]: http://go.microsoft.com/fwlink/p/?LinkId=309046
  [Benutzerdefinierte API]: http://msdn.microsoft.com/en-us/library/windowsazure/dn280974.aspx
