1.	Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com/) an.
2.	Klicken Sie in der Indexleiste auf **Neu**, wählen Sie dann **Daten und Speicher** und klicken Sie anschließend auf **DocumentDB**. 

	![Screenshot des Azure-Vorschauportals unter Hervorhebung der Schaltflächen **Neu**, **Daten und Speicher** auf dem Blatt "Erstellen" sowie **DocumentDB** auf dem Blatt "Daten und Speicher"][1]   

	Durchsuchen Sie alternativ über das Startmenü den Azure Marketplace, wählen Sie **Daten und Analysen** aus, klicken Sie auf **DocumentDB** und dann auf **Erstellen**.  
	
	![Screenshot des Azure-Vorschauportals mit dem Blatt "Marketplace" mit hervorgehobener Kachel "DocumentDB" sowie dem Blatt "DocumentDB" mit hervorgehobener Schaltfläche "Erstellen"][2]   
   

3. Geben Sie im Blade **Neue DocumentDB (Vorschau)** die gewünschte Konfiguration für das DocumentDB-Konto an. 
 
	![Screenshot des Blatts "New DocumentDB (Preview)"][3] 


	- Geben Sie in das Feld **Id** einen Namen zur Identifizierung des DocumentDB-Kontos ein. Dieser Wert wird zum Hostnamen innerhalb des URI. Die ID darf nur Kleinbuchstaben, Ziffern und den Bindestrich "-" enthalten und muss zwischen 3 und 50 Zeichen lang sein. Beachten Sie, dass "documents.azure.com" an den gewählten Endpunktnamen angehängt wird, um den DocumentDB-Konto-Endpunkt zu erstellen.

	- Der Bereich **Preisebene** ist gesperrt, da die DocumentDB-Vorschau nur eine Standardpreisebene unterstützt. Weitere Informationen finden Sie unter [DocumentDB-Preise](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- Der Bereich **Optionale Konfiguration** dient zur Angabe der Anfangskapazität, die Ihrem DocumentDB-Konto zugewiesen wird.  DocumentDB verwendet Kapazitätseinheiten, um Ihnen die Skalierung Ihres DocumentDB-Kontos zu ermöglichen, wobei jede Kapazitätseinheit reservierten Datenbankspeicher und Durchsatz umfasst.  Standardmäßig wird 1 Kapazitätseinheit bereitgestellt.  Sie können die Anzahl der Kapazitätseinheiten, die Ihrem DocumentDB-Konto zur Verfügung steht, jederzeit im [Vorschauverwaltungsportal](https://portal.azure.com/#gallery/Microsoft.DocumentDB) anpassen. Ausführliche Informationen zur DocumentDB-Kontokapazität und zum Durchsatz finden Sie im Artikel [Verwalten der Kapazität und Leistung von DocumentDB][documentdb-manage].

	- Unter **Ressourcengruppe** können Sie eine Ressourcengruppe für Ihr DocumentDB-Konto auswählen oder erstellen.  Standardmäßig wird eine neue Ressourcengruppe erstellt.  Sie können jedoch eine vorhandene Ressourcengruppe auswählen, zu der Sie Ihr DocumentDB-Konto hinzufügen möchten. Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](azure-preview-portal-using-resource-groups.md)..

	- Wählen Sie unter **Abonnement** das Azure-Abonnement aus, das Sie mit dem DocumentDB-Konto verwenden möchten. Wenn Ihr Konto nur über ein Abonnement verfügt, wird dieses Konto automatisch ausgewählt.*
 
	- Unter **Standort** können Sie einen geografischen Standort angeben, an dem Ihr DocumentDB-Konto gehostet werden soll.   

3.	Klicken Sie nach der Konfiguration der Optionen für das neue DocumentDB-Konto auf **Erstellen**.  Die Erstellung des DocumentDB-Kontos kann einige Minuten dauern.  Sie können den Fortschritt über das Startmenü überwachen, um den Status zu überprüfen.  
	![Screenshot der Kachel "Erstellen" im Startmenü][4]  
  
	Sie können den Status auch über den Hub "Benachrichtigungen" überwachen.  

	![Screenshot des Hubs "Benachrichtigungen", in dem gerade das DocumentDB-Konto erstellt wird][5]  

	![Screenshot des Hubs "Benachrichtigungen", in dem das DocumentDB-Konto erstellt und in einer Ressourcengruppe bereitgestellt wurde][6]

4.	Nachdem das DocumentDB-Konto erstellt wurde, kann es mit den Standardeinstellungen verwendet werden.

	*Die Standardkonsistenz des DocumentDB-Kontos wird auf "Session" eingestellt.  Sie können die Standardkonsistenzeinstellung über das [Vorschauverwaltungsportal](https://portal.azure.com/#gallery/Microsoft.DocumentDB) ändern.*  
	![Screenshot des Blatts "Ressourcengruppe"][7]  


<!--Image references-->
[1]: ./media/documentdb-create-dbaccount/ca1.png
[2]: ./media/documentdb-create-dbaccount/ca2.png
[3]: ./media/documentdb-create-dbaccount/ca3.png
[4]: ./media/documentdb-create-dbaccount/ca4.png
[5]: ./media/documentdb-create-dbaccount/ca5.png
[6]: ./media/documentdb-create-dbaccount/ca6.png
[7]: ./media/documentdb-create-dbaccount/ca7.png

[Gewusst wie: Erstellen eines DocumentDB-Kontos]: #Howto
[Nächste Schritte]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md

<!--HONumber=49-->