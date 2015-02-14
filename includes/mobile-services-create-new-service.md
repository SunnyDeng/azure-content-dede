

Führen Sie die folgenden Schritte aus, um einen neuen mobilen Dienst zu erstellen.

1.	Melden Sie sich beim [Verwaltungsportal] an. 

2.	Klicken Sie am unteren Rand des Navigationsbereichs auf **+ NEU**.

	![](./media/mobile-services-create-new-service/plus-new.png)

3.	Erweitern Sie **Berechnen** und **Mobiler Service**, und klicken Sie dann auf **Erstellen**.

	![](./media/mobile-services-create-new-service/mobile-create.png)

	Daraufhin wird das Dialogfeld **Neuer mobiler Service** angezeigt.

4.	Wählen Sie auf der Seite **Einen mobilen Service erstellen** die Option **Eine freie 20 MB-SQL-Datenbank erstellen** und die Laufzeit **Node.js** aus. Geben Sie dann einen Unterdomänennamen für den neuen mobilen Service im Textfeld **URL** ein, und warten Sie, bis der Name überprüft wurde. Nachdem der Name fertig überprüft wurde, klicken Sie auf den Pfeil nach rechts, um die nächste Seite aufzurufen.	

	![](./media/mobile-services-create-new-service/mobile-create-page1.png)

   	Daraufhin wird die Seite **Datenbankeinstellungen angeben** angezeigt.

	> [AZURE.NOTE] Im Rahmen dieses Lernprogramms erstellen Sie eine neue SQL-Datenbankinstanz und einen neuen SQL-Datenbankserver. Sie können diese neue Datenbank wiederverwenden und wie jede andere SQL-Datenbankinstanz verwalten. Wenn Sie bereits über eine Datenbank in derselben Region wie der des neuen mobilen Service verfügen, können Sie stattdessen **Vorhandene Datenbank verwenden** wählen und dann diese Datenbank auswählen. Die Verwendung einer Datenbank in einer anderen Region wird nicht empfohlen, da zusätzliche Kosten für Bandbreite anfallen können und es zu höherer Latenz kommen kann.	

6.	Geben Sie unter **Name** den Namen der neuen Datenbank ein. Geben Sie dann den **Anmeldenamen** ein. Dies ist der Administratoranmeldename für den neuen SQL-Datenbankserver. Geben Sie das Kennwort ein, und bestätigen Sie es, und klicken Sie dann auf die Schaltfläche 'Prüfen', um den Vorgang abzuschließen.

	![](./media/mobile-services-create-new-service/mobile-create-page2.png)

	> [AZURE.NOTE] Wenn das eingegebene Kennwort nicht den Mindestanforderungen entspricht oder es keine Übereinstimmung gibt, wird eine Warnung angezeigt. 
	>
	> Es wird empfohlen, sich den Anmeldenamen und das Kennwort des Administrators zu notieren. Sie benötigen diese Informationen, um die SQL-Datenbankinstanz oder den Server in der Zukunft wiederzuverwenden.

Sie haben nun einen neuen mobilen Service erstellt, den Sie mit Ihren mobilen Apps verwenden können.



<!-- URLs. -->
[Verwaltungsportal]: https://manage.windowsazure.com/
<!--HONumber=42-->
