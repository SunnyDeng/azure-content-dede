### Erstellen eines TCP-Endpunkts für den virtuellen Computer

Für den Zugriff auf SQL Server über das Internet benötigt der virtuelle Computer einen Endpunkt, um nach eingehender TCP-Kommunikation zu lauschen. Dieser Azure-Konfigurationsschritt leitet den eingehenden Datenverkehr des TCP-Ports zu einem TCP-Port, auf den der virtuelle Computer zugreifen kann.

>[AZURE.NOTE]Wenn die Verbindung innerhalb desselben Clouddiensts oder innerhalb desselben virtuellen Netzwerks erfolgt, müssen Sie keinen Endpunkt mit öffentlichem Zugriff erstellen. In diesem Fall können Sie mit dem nächsten Schritt fortfahren. Weitere Informationen finden Sie unter [Verbindungsszenarien](../articles/virtual-machines/virtual-machines-sql-server-connectivity.md#connection-scenarios).

1. Klicken Sie im Azure-Verwaltungsportal auf **VIRTUELLE COMPUTER**.
	
2. Klicken Sie auf den neu erstellten virtuellen Computer. Informationen zu Ihrem virtuellen Computer werden angezeigt.
	
3. Wählen Sie oben auf der Seite die Seite **ENDPUNKTE** aus, und klicken Sie dann unten auf **HINZUFÜGEN**.
	
4. Klicken Sie auf der Seite **Einem virtuellen Computer einen Endpunkt hinzufügen** auf **Eigenständigen Endpunkt hinzufügen**, und klicken Sie dann auf „Weiter“, um fortzufahren.
	
5. Geben Sie auf der Seite zum Angeben der Details des Endpunkts folgende Informationen ein:

	- Geben Sie unter **NAME** einen Namen für den Endpunkt ein.
	- Wählen Sie im Feld **PROTOKOLL** den Eintrag **TCP**. Sie können zudem **57500** im Feld **ÖFFENTLICHER PORT** eingeben. Sie können den Standard-Überwachungsport **1433** von SQL Server im Feld **Privater Port** eingeben. Viele Organisationen verwenden unterschiedliche Portnummern, um böswillige Sicherheitsangriffe zu vermeiden. 

6. Klicken Sie auf das Häkchen, um fortzufahren. Der Endpunkt wurde erstellt.

<!---HONumber=AcomDC_0107_2016-->