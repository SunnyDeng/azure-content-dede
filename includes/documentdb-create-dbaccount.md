1.	Melden Sie sich beim [Microsoft Azure-Vorschauportal](https://portal.azure.com/) an.
2.	Klicken Sie in der Indexleiste auf **Neu**, wählen Sie **Daten und Speicher** aus, und klicken Sie anschließend auf **DocumentDB**. 

	![Screenshot des Azure-Vorschauportals mit den Schaltflächen "Neu", "Daten und Speicher" auf dem Blatt "Erstellen" sowie "DocumentDB" auf dem Blatt "Daten und Speicher"][1]

	<!-- Alternatively, from the Startboard, you can browse the Azure Marketplace, select **Data + storage**, choose **DocumentDB**, and then click **Create**.  --><!-- ![Screen shot of the Azure Preview portal, showing the Marketplace blade with the DocumentDB tile highlighted, and the DocumentDB blade with the Create button highlighted][2]    -->
   

3. Geben Sie im Blatt **Neue DocumentDB** die gewünschte Konfiguration für das DocumentDB-Konto an.
 
	![Screenshot des Blatts "Neue DocumentDB"][3]


	- Geben Sie im Feld **Id** einen Namen zur Identifizierung des DocumentDB-Kontos ein. Dieser Wert wird zum Hostnamen innerhalb des URI. Die **ID** darf nur Kleinbuchstaben, Ziffern und den Bindestrich "-" enthalten und muss zwischen 3 und 50 Zeichen lang sein. Beachten Sie, dass *documents.azure.com* an den gewählten Namen des Endpunkts angehängt wird, um den Endpunkt des DocumentDB-Kontos zu erstellen.

	- Der Fokus **Tarif für Konto** ist gesperrt, da DocumentDB nur einen einzigen Standardtarif für Konten unterstützt. Weitere Informationen finden Sie unter [DocumentDB-Preise](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- Unter **Ressourcengruppe** können Sie eine Ressourcengruppe für Ihr DocumentDB-Konto auswählen oder erstellen. Standardmäßig wird eine neue Ressourcengruppe erstellt. Sie können jedoch eine vorhandene Ressourcengruppe auswählen, zu der Sie Ihr DocumentDB-Konto hinzufügen möchten. Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](resource-group-portal.md).

	- Wählen Sie unter **Abonnement** das Azure-Abonnement aus, das Sie mit dem DocumentDB-Konto verwenden möchten. Wenn Ihr Konto nur über ein Abonnement verfügt, wird dieses Konto automatisch ausgewählt.
 
	- Unter **Standort** können Sie einen geografischen Standort angeben, an dem Ihr DocumentDB-Konto gehostet werden soll.

4.	Klicken Sie nach der Konfiguration der Optionen für das neue DocumentDB-Konto auf **Erstellen**. Die Erstellung des DocumentDB-Kontos kann einige Minuten dauern. Sie können den Fortschritt im Startmenü überwachen, um den Status zu überprüfen.![Screenshot der Kachel "Erstellen" im Startmenü][4]
  
	Sie können den Status auch über den Hub "Benachrichtigungen" überwachen.

	![Screenshot des Notification Hubs, in dem gerade das DocumentDB-Konto erstellt wird][5]

	![Screenshot des Notification Hubs, in dem das DocumentDB-Konto erfolgreich erstellt und in einer Ressourcengruppe bereitgestellt wurde][6]

5.	Nachdem das DocumentDB-Konto erstellt wurde, kann es mit den Standardeinstellungen verwendet werden. Beachten Sie, dass die Standardkonsistenz des DocumentDB-Kontos auf "Sitzung" eingestellt ist. Sie können die Standardkonsistenzeinstellung im [Azure-Vorschauportal](https://portal.azure.com/#gallery/Microsoft.DocumentDB) ändern.

    ![Screenshot des Blatts "Ressourcengruppe"][7]

<!--Image references-->
[1]: media/documentdb-create-dbaccount/ca1.png
[2]: media/documentdb-create-dbaccount/ca2.png
[3]: media/documentdb-create-dbaccount/ca3.png
[4]: media/documentdb-create-dbaccount/ca4.png
[5]: media/documentdb-create-dbaccount/ca5.png
[6]: media/documentdb-create-dbaccount/ca6.png
[7]: media/documentdb-create-dbaccount/ca7.png

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]: ../articles/documentdb/documentdb-manage.md

<!---HONumber=62-->