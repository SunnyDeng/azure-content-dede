Um App-Daten im neuen mobilen Dienst speichern zu können, müssen Sie zuerst eine neue Tabelle in der verknüpften SQL-Datenbankinstanz erstellen.

1. Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf die Registerkarte **Daten** und anschließend auf **+Erstellen**.

   	Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3. Geben Sie unter **Tabellenname** den Namen _TodoItem_ ein und klicken Sie auf das Kontrollkästchen.

  Dadurch wird die neue Speichertabelle **TodoItem** mit Standardberechtigungen erstellt. Dies bedeutet, dass jeder mit dem Anwendungsschlüssel (der mit Ihrer App verteilt wird) auf die Daten in der Tabelle zugreifen und diese ändern kann.

	>[AZURE.NOTE] The same table name is used in Mobile Services quickstart. However, each table is created in a schema that is specific to a given mobile service. This is to prevent data collisions when multiple mobile services use the same database.

4. Klicken Sie auf die neue Tabelle **TodoItem**, und überprüfen Sie, dass keine Datenzeilen vorhanden sind.

5. Klicken Sie auf die Registerkarte **Spalten**. Überprüfen Sie, dass folgende Standardspalten automatisch erstellt werden:
	
	<table border="1" cellpadding="10">
<tr>
<th>Spaltenname</th>
<th>Typ</th>
<th>Index</th>
</tr>
<tr>
<td>ID</td>
<td>string</td>
<td>Indiziert</td>
</tr>
<tr>
<td>__createdAt</td>
<td>date</td>
<td>Indiziert</td>
</tr>
<tr>
<td>__updatedAt</td>
<td>date</td>
<td><font color="transparent">-</font></td>
</tr>
<tr>
<td>__version</td>
<td>timestamp (MSSQL)</td>
<td><font color="transparent">-</font></td>
</tr> 	
</table>Dies ist die Mindestanforderung für eine Tabelle in Mobile Services.

    > [AZURE.NOTE]Wenn für Ihren mobilen Dienst das dynamische Schema aktiviert ist, werden neue Spalten immer dann automatisch erstellt, wenn JSON-Objekte über einen Einfüge- oder Aktualisierungsvorgang an den mobilen Dienst gesendet werden.

Sie können den neuen mobilen Dienst nun als Datenspeicher für die App verwenden.

<!---HONumber=July15_HO3-->