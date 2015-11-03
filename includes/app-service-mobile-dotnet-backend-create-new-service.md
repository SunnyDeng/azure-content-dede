

Führen Sie die folgenden Schritte aus, um ein neues mobiles App-Back-End zu erstellen.

1. Melden Sie sich beim [Azure-Portal] an.

2. Klicken Sie oben links im Fenster auf **+NEU** > **Web und mobil** > **Mobile App**, und geben Sie dann einen Namen für Ihr mobiles App-Back-End an.

3. Geben Sie im Feld **Ressourcengruppe** den gleichen Namen wie für Ihre App ein.

4. Es wird der App Service-Tarif "Standard" ausgewählt. Wenn Sie Ihren App Service-Tarif ändern möchten, klicken Sie dazu auf App Services-Plan > **+ Neu erstellen**. Geben Sie einen Namen für den neuen App Service-Tarif ein, und wählen Sie einen geeigneten Speicherort. Klicken Sie auf den Tarif, und wählen Sie einen geeigneten Tarif für den Dienst. Wählen Sie **Alle anzeigen** aus, um mehr Tarifoptionen anzuzeigen, z. B. **Free** und **Shared**. Nachdem Sie den Tarif ausgewählt haben, klicken Sie auf die Schaltfläche **Auswählen**. Klicken Sie wieder auf dem Blatt **App Service-Plan** auf **OK**.

5. Klicken Sie auf **Erstellen**. Damit erstellen Sie ein mobiles App-Back-End, auf dem Sie später das Serverprojekt bereitstellen. Das Bereitstellen eines mobilen App-Back-Ends kann einige Minuten in Anspruch nehmen. Nachdem das mobile App-Back-End bereitgestellt wurde, wird im Portal das Blatt **Einstellungen** für das mobile App-Back-End geöffnet. Im nächsten Schritt erstellen Sie eine neue SQL-Datenbank.

    > [AZURE.NOTE]Im Rahmen dieses Lernprogramms erstellen Sie eine neue SQL-Datenbankinstanz und einen neuen SQL-Datenbankserver. Sie können diese neue Datenbank wiederverwenden und wie jede andere SQL-Datenbankinstanz verwalten. Wenn Sie bereits über eine Datenbank an demselben Standort wie dem des neuen mobilen App-Back-Ends verfügen, können Sie stattdessen **Vorhandene Datenbank verwenden** wählen und dann diese Datenbank auswählen. Die Verwendung einer Datenbank an einem anderen Standort wird nicht empfohlen, da zusätzliche Kosten für Bandbreite anfallen können und es zu höherer Latenz kommen kann.

6. Klicken Sie im neuen mobilen App-Back-End auf **Einstellungen** > **Mobile App** > **Daten** > **+Hinzufügen**.

7. Klicken Sie auf dem Blatt **Add data connection** auf **SQL-Datenbank – Erforderliche Einstellungen konfigurieren** > **Neue Datenbank erstellen**. Geben Sie den Namen der neuen Datenbank in das Feld **Name** ein.

8. Klicken Sie auf **Server**. Geben Sie auf dem Blatt **Neuer Server** in **Servername** einen eindeutigen Namen ein, und legen Sie einen geeigneten **Server admin login** mit einem **Kennwort** an. Stellen Sie sicher, dass **Allow azure services to access server** aktiviert ist. Klicken Sie auf **OK**.

    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

9. Klicken Sie auf dem Blatt **Neue Datenbank** auf **OK**.

10. Wählen Sie auf dem Blatt **Add data connection** die **Verbindungszeichenfolge** aus, und geben Sie den Anmeldenamen und das Kennwort ein, das Sie gerade beim Erstellen der Datenbank angegeben haben. Wenn Sie eine vorhandene Datenbank verwenden, geben Sie die Anmeldeinformationen für diese Datenbank an. Klicken Sie nach der Eingabe auf **OK**.

11. Klicken Sie auf dem Blatt **Add data connection** auf **OK**, um die Datenbank zu erstellen.

Das Erstellen der Datenbank kann einige Minuten dauern. Im Bereich **Benachrichtigungen** können Sie den Fortschritt der Bereitstellung überwachen. Fahren Sie erst fort, wenn die Datenbank erfolgreich bereitgestellt wurde.

Sie haben nun ein Azure-Back-End für mobile Apps bereitgestellt, das von Ihren mobilen Clientanwendungen verwendet werden kann. Als Nächstes laden Sie ein Serverprojekt für ein einfaches "Aufgabenlisten"-Back-End herunter und veröffentlichen es in Azure.

<!-- URLs. -->
[Azure-Portal]: https://portal.azure.com/

<!---HONumber=Nov15_HO1-->