

Als Nächstes erstellen Sie einen mobilen Dienst, um die In-Memory-Liste für die Datenspeicherung zu ersetzen. Führen Sie die folgenden Schritte aus, um einen neuen mobilen Dienst zu erstellen.

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an. 
2.	Klicken Sie unten im Navigationsbereich auf **+NEW**.

	![plus-new](./media/mobile-services-create-new-service-data/plus-new.png)

3.	Erweitern Sie **Compute** und **Mobile Service**, und klicken Sie dann auf **Erstellen**.

	![mobile-create](./media/mobile-services-create-new-service-data/mobile-create.png)

    Das Dialogfeld **New Mobile Service** wird angezeigt.

4.	Wählen Sie auf der Seite **Create a mobile service** die Option **Create a free 20 MB SQL Database** aus. Geben Sie dann einen Unterdomänennamen für den neuen mobilen Dienst im Textfeld **URL** ein, und warten Sie, bis der Name überprüft wurde. Nachdem der Name fertig überprüft wurde, klicken Sie auf den Pfeil nach rechts, um die nächste Seite aufzurufen.

	![mobile-create-page1](./media/mobile-services-create-new-service-data/mobile-create-page1.png)

    Dadurch wird die Seite **Specify database settings** angezeigt.

    
	> [AZURE.NOTE]Im Rahmen dieses Lernprogramms erstellen Sie eine neue SQL-Datenbankinstanz und einen neuen SQL-Datenbankserver. Sie können diese neue Datenbank wiederverwenden und wie jede andere SQL-Datenbankinstanz verwalten. Wenn Sie bereits über eine Datenbank in derselben Region wie der neue mobile Dienst verfügen, können Sie stattdessen **Vorhandene Datenbank verwenden** und dann diese Datenbank auswählen. Die Verwendung einer Datenbank in einer anderen Region wird nicht empfohlen, da zusätzliche Kosten für Bandbreite anfallen können und es zu höherer Latenz kommen kann.

5.	Geben Sie unter **Name** den Namen der neuen Datenbank ein. Geben Sie dann den **Anmeldenamen** ein. Dies ist der Administratoranmeldename für den neuen SQL-Datenbankserver. Geben Sie das Kennwort ein, und bestätigen Sie es, und klicken Sie dann auf den Häkchenknopf, um den Vorgang abzuschließen.

	![mobile-create-page2](./media/mobile-services-create-new-service-data/mobile-create-page2.png)

    
	> [AZURE.NOTE]Wenn das eingegebene Kennwort nicht den Mindestanforderungen entspricht oder es keine Übereinstimmung gibt, wird eine Warnung angezeigt.
	>
	> Es wird empfohlen, sich den Anmeldenamen und das Kennwort des Administrators zu notieren. Sie benötigen diese Informationen, um die SQL-Datenbankinstanz oder den Server in der Zukunft wiederzuverwenden.

Sie haben nun einen neuen mobilen Dienst erstellt, der von Ihren mobilen Apps verwendet werden kann. Als Nächstes fügen Sie eine neue Tabelle zum Speichern von App-Daten hinzu. Diese Tabelle wird von der App anstelle der In-Memory-Sammlung verwendet.

<!---HONumber=July15_HO3-->