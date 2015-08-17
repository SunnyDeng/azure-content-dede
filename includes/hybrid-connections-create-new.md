
1. Melden Sie sich auf dem lokalen Computer beim [Azure-Verwaltungsportal](http://manager.windowsazure.com) an (dies ist das ältere Portal).

2. Klicken Sie unten im Navigationsbereich auf **+NEU** > **App-Dienste** > **BizTalk Service** > **Benutzerdefinierte Erstellung**.

3. Geben Sie einen Namen für den **BizTalk Service** ein und wählen Sie eine **Edition** aus.

	Dieses Lernprogramm verwendet **mobile1**. Sie müssen einen eindeutigen Namen für Ihren neuen BizTalk Service angeben.

4. Nach der Erstellung des BizTalk Service wählen Sie die Registerkarte **Hybridverbindungen** aus und klicken Sie auf **Hinzufügen**.

	![Hybridverbindung hinzufügen](./media/hybrid-connections-create-new/3.png)

	Daraufhin wird eine neue Hybridverbindung erstellt.

5. Geben Sie einen **Namen** und **Hostnamen** für die Hybridverbindung ein, und konfigurieren Sie den **Port** auf mit `1433`.
  
	![Hybridverbindung konfigurieren](./media/hybrid-connections-create-new/4.png)

	Der Hostname ist der Name des lokalen Servers. Konfigurieren Sie die Hybridverbindung für den Zugriff auf SQL Server unter dem Port 1433. Wenn Sie eine benannte SQL-Server-Instanz nutzen, verwenden Sie stattdessen den zuvor definierten statischen Port.

6. Nachdem die neue Verbindung erstellt wurde, wird der Status der neuen Verbindung als **Lokale Einrichtung unvollständig** angezeigt.

7. Navigieren Sie zurück zu Ihrem mobilen Dienst, und klicken Sie auf **Konfigurieren**. Scrollen Sie nach unten zu **Hybridverbindungen** und klicken Sie auf **Hinzufügen von Hybridverbindungen**. Wählen Sie die eben erstellte Hybridverbindung und klicken Sie auf **OK**.

    Jetzt kann Ihr mobiler Dienst die neue Hybridverbindung verwenden.

Nun müssen Sie den Hybrid Connection Manager auf dem lokalen Computer installieren.

<!---HONumber=August15_HO6-->