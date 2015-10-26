

1. Melden Sie sich beim Azure-Verwaltungsportal an, klicken Sie auf **Mobile Services**, und wählen Sie dann Ihren mobilen Dienst aus.

2. Klicken Sie auf die Registerkarte **API** und dann auf **Erstellen**. Das Dialogfeld **Create a new custom API** wird angezeigt.

3. Geben Sie _completeall_ in das Feld **API-Name** ein, und klicken Sie auf die Schaltfläche mit dem Häkchen, um die neue API zu erstellen.

	> [AZURE.TIP]Mit den Standardberechtigungen kann jeder Benutzer mit dem App-Schlüssel die benutzerdefinierte API aufrufen. Der Anwendungsschlüssel wird jedoch nicht verteilt bzw. sicher gespeichert und gilt daher nicht als sichere Anmeldeinformation. Erwägen Sie, den Zugriff nur authentifizierten Benutzern zu gestatten, um die Sicherheit zu erhöhen.

4. Klicken Sie in der API-Tabelle auf **completeall**.

5. Klicken Sie auf die Registerkarte **Skript**, ersetzen Sie den vorhandenen Code durch folgenden Code, und klicken Sie anschließend auf **Speichern**. Dieser Code verwendet das [mssql-Objekt], um direkt auf die Tabelle **todoitem** zuzugreifen und für alle Elemente das `complete`-Kennzeichen zu setzen. Da die Funktion **exports.post** verwendet wird, senden die Clients eine POST-Anforderung zur Durchführung des Vorgangs. Die Anzahl der geänderten Zeilen wird an den Client als Ganzzahl zurückgegeben.


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


> [AZURE.NOTE]Das [request](http://msdn.microsoft.com/library/windowsazure/jj554218.aspx)-Objekt und das [response](http://msdn.microsoft.com/library/windowsazure/dn303373.aspx)-Objekt, die für die Funktionen der benutzerdefinierten API bereitgestellt werden, werden durch die [Express.js-Bibliothek](http://go.microsoft.com/fwlink/p/?LinkId=309046) implementiert.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[mssql-Objekt]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx

<!---HONumber=Oct15_HO3-->