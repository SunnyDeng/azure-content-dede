

1. Melden Sie sich beim [Azure-Verwaltungsportal] an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

	![](./media/mobile-services-create-custom-api/mobile-services-selection.png)

2. Klicken Sie auf die Registerkarte **API** und dann auf **Eine benutzerdefinierte API erstellen**.

	![](./media/mobile-services-create-custom-api/mobile-custom-api-create.png)

	Das Dialogfeld **Eine benutzerdefinierte API erstellen** wird angezeigt.

3. Geben Sie _completeall_ in das Feld **API-Name** ein, und klicken Sie auf das Häkchensymbol.

	![](./media/mobile-services-create-custom-api/mobile-custom-api-create-dialog2.png)

	Dadurch wird die neue API erstellt.

	> [AZURE.NOTE] Die Standardberechtigungen werden festgelegt, und das heißt, dass jeder Benutzer der App die benutzerdefinierte API aufrufen kann. Der Anwendungsschlüssel wird jedoch nicht verteilt bzw. sicher gespeichert und gilt daher nicht als sichere Anmeldeinformation. Deshalb sollten Sie in Erwägung ziehen, den Zugriff für Vorgänge, durch die Daten geändert werden oder die sich auf den mobilen Service auswirken, auf authentifizierte Benutzer zu beschränken.

4. Klicken Sie auf den neuen Eintrag **completeall** in der API-Tabelle.

	![](./media/mobile-services-create-custom-api/mobile-custom-api-select2.png)

5. Klicken Sie auf die Registerkarte **Skript**, ersetzen Sie den vorhandenen Code durch folgenden Code, und klicken Sie anschließend auf **Speichern**:

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


	Dieser Code verwendet das [mssql]-Objekt, um direkt auf die Tabelle **todoitem** zuzugreifen und für alle Elemente das Erledigt-Kennzeichen zu setzen. Da die Funktion **exports.post** verwendet wird, senden die Clients eine POST-Anforderung zur Durchführung des Vorgangs. Die Anzahl der geänderten Zeilen wird an den Client als Ganzzahl zurückgegeben.

> [AZURE.NOTE]
> Das <a href="http://msdn.microsoft.com/de-de/library/windowsazure/jj554218.aspx" target="_blank">request</a>-Objekt und das <a href="http://msdn.microsoft.com/de-de/library/windowsazure/dn303373.aspx" target="_blank">response</a>-Objekt, die für die Funktionen der benutzerdefinierten API bereitgestellt werden, werden durch die <a href="http://go.microsoft.com/fwlink/p/?LinkId=309046" target="_blank">Express.js-Bibliothek</a> implementiert. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/dn280974.aspx" target="_blank">Benutzerdefinierte API</a>. 

Als Nächstes ändern Sie die Quickstart-App so, dass eine neue Schaltfläche und neuer Code hinzugefügt wird, der die neue benutzerdefinierte API asynchron aufruft.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[mssql-Objekt]: http://msdn.microsoft.com/de-de/library/windowsazure/jj554212.aspx
<!--HONumber=42-->
