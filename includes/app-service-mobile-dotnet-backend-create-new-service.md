1. Melden Sie sich beim [Azure-Portal] an.

2. Klicken Sie oben links im Fenster auf **+NEU** > **Web und mobil** > **Mobile App**, und geben Sie dann einen Namen für Ihr mobiles App-Back-End an.

3. Wählen Sie im Feld **Ressourcengruppe** eine vorhandene Ressourcengruppe aus. Wenn Sie keine Ressourcengruppen besitzen, geben Sie den gleichen Namen wie für Ihre App ein.
 
	An dieser Stelle wird der standardmäßige App Service-Plan ausgewählt, bei dem es sich um den [Standard-Tarif](https://azure.microsoft.com/pricing/details/app-service/) handelt. Die Einstellungen für den App Service-Plan bestimmen den Standort, die Funktionen und die Computeressourcen Ihrer App. Sie können einen anderen vorhandenen App Service-Plan auswählen oder einen neuen Plan erstellen. Weitere Informationen zu App Service-Plänen sowie zum Erstellen eines neuen Plans in einem anderen Tarif und am gewünschten Standort finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Verwenden Sie den standardmäßigen App Service-Plan, wählen Sie einen anderen Plan aus, oder [erstellen Sie einen neuen Plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan), und klicken Sie auf **Erstellen**.
	
	Dadurch wird das Back-End für mobile Apps erstellt. Später werden Sie Ihr Serverprojekt in diesem Back-End bereitstellen. Die Bereitstellung eines Back-Ends für mobile Apps kann mehrere Minuten dauern. Nach Abschluss wird das Blatt **Einstellungen** für das mobile App-Back-End angezeigt. Bevor Sie das Back-End für mobile Apps verwenden können, müssen Sie auch eine Verbindung zu einem Datenspeicher definieren.

    > [AZURE.NOTE] Im Rahmen dieses Tutorials erstellen Sie eine neue SQL-Datenbankinstanz und einen neuen SQL-Datenbankserver. Sie können diese neue Datenbank wiederverwenden und wie jede andere SQL-Datenbankinstanz verwalten. Wenn Sie bereits über eine Datenbank an demselben Standort wie dem des neuen mobilen App-Back-Ends verfügen, können Sie stattdessen **Vorhandene Datenbank verwenden** wählen und dann diese Datenbank auswählen. Die Verwendung einer Datenbank an einem anderen Standort wird nicht empfohlen, da zusätzliche Kosten für Bandbreite anfallen können und es zu höherer Latenz kommen kann. Es stehen weitere Datenspeicheroptionen zur Verfügung.

6. Klicken Sie auf dem Blatt **Einstellungen** für das neue Back-End für mobile Apps auf **Schnellstart** > Ihre Clientanwendungsplattform > **Verbindung mit Datenbank herstellen**.

	![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. Klicken Sie auf dem Blatt **Datenverbindung hinzufügen** auf **SQL-Datenbank** > **Neue Datenbank erstellen**, geben Sie den **Namen** für die Datenbank ein, wählen Sie einen Tarif, und klicken Sie dann auf **Server**.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. Geben Sie auf dem Blatt **Neuer Server** im Feld **Servername** einen eindeutigen Servernamen ein, stellen Sie eine sichere **Serveradministratoranmeldung** und ein sicheres **Kennwort** bereit, vergewissern Sie sich, dass das Kontrollkästchen **Azure-Diensten Zugriff auf den Server erlauben** aktiviert ist, und klicken Sie dann zweimal auf **OK**. Dadurch werden die neue Datenbank und der neue Server erstellt.

10. Klicken Sie auf dem Blatt **Datenverbindung hinzufügen** auf **Verbindungszeichenfolge**, geben Sie den Anmeldenamen und das Kennwort für Ihre Datenbank ein, und klicken Sie dann zweimal auf **OK**.

	Das Erstellen der Datenbank kann einige Minuten dauern. Im Bereich **Benachrichtigungen** können Sie den Fortschritt der Bereitstellung überwachen. Sie können erst fortfahren, wenn die Datenbank erfolgreich bereitgestellt wurde.


<!-- URLs. -->
[Azure-Portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0224_2016-->