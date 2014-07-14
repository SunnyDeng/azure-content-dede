Die folgenden Schritte erstellen einen neuen mobilen Dienst in Azure und fügen Ihrem Projekt Code hinzu, der den Zugriff auf diesen neuen Dienst ermöglicht. Bevor Sie den mobilen Dienst erstellen können, müssen Sie die ".publishsettings"-Datei aus Ihrem Azure-Abonnement in Visual Studio importieren. Auf diese Weise kann Visual Studio in Ihrem Auftrag eine Verbindung mit Azure herstellen. Wenn Sie einen neuen mobilen Dienst erstellen, müssen Sie eine Azure-SQL-Datenbank angeben, die vom mobilen Dienst zum Speichern von Anwendungsdaten verwendet wird.

1.  Öffnen Sie den Projektmappen-Explorer in Visual Studio 2013, klicken Sie mit der rechten Maustaste auf das Projekt, klicken Sie auf **Hinzufügen** und dann auf **Verbundener Dienst**.
    
    ![verbundenen Dienst
    hinzufügen](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2.  Klicken Sie im Dienste-Manager-Dialogfeld auf **Dienst erstellen** und wählen Sie **Importieren** aus **Abonnement** im Dialogfeld "Mobilen Dienst erstellen" aus.
    
    ![neuen mobilen Dienst aus VS 2013
    erstellen](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3.  Klicken Sie unter "Azure-Abonnements abonnieren" auf **Abonnementdatei herunterladen**, melden Sie sich ggf. mit Ihrem Azure-Konto an, klicken Sie auf **Speichern**, wenn der Browser Sie zum Speichern der Datei auffordert.
    
    ![Abonnementdatei in VS
    herunterladen](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)
	<div class="dev-callout"><b>Hinweis</b> <p>Das Anmeldefenster wird im Browser angezeigt, der sich hinter dem Visual Studio-Fenster befinden kann. Denken Sie daran, sich zu notieren, wo Sie die heruntergeladene ".publishsettings"-Datei gespeichert haben. Wenn das Projekt bereits mit Ihrem Azure-Abonnement verbunden ist, können Sie diesen Schritt überspringen.</p> </div>


4.  Klicken Sie auf **Durchsuchen**, navigieren Sie zum Speicherort der ".publishsettings"-Datei, wählen Sie die Datei aus, klicken Sie auf **Öffnen** und dann auf **Importieren**.
    
    ![Abonnementdatei in VS
    importieren](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)
    
    Visual Studio importiert die Daten, die für die Verbindung mit Ihrem Azure-Abonnement erforderlich sind. Wenn Ihr Abonnement bereits über mindestens einen mobilen Dienst verfügt, werden die Dienstnamen angezeigt.

	<div class="dev-callout"><b>Sicherheitshinweis</b><p>Nach dem Importieren der Veröffentlichungseinstellungen sollten Sie die heruntergeladene ".publishsettings"-Datei löschen, da sie Informationen enthält, die von anderen Personen für den Zugriff auf Ihr Konto verwendet werden können. Sichern Sie die Datei, wenn Siesie in anderen Projekten mit verbundenen Anwendungen verwenden möchten.</p></div>


5.  Wählen Sie im Dialogfeld **Mobilen Dienst erstellen** Ihr **Abonnement** und die gewünschte **Region** für Ihren mobilen Dienst aus. Geben Sie dann unter **Name** einen Namen für Ihren mobilen Dienst ein.

	<div class="dev-callout"><b>Hinweis</b>
	<p>Namen für mobile Dienste müssen eindeutig sein. Neben **Name** wird ein rotes X angezeigt, wenn der angegebene Name nicht verfügbar ist.</p></div>


6.  Wählen Sie unter **Datenbank** die Option **Freie SQL-Datenbank erstellen** aus, geben Sie den **Serverbenutzernamen**, das **Serverkennwort** und die **Serverkennwortbestätigung** ein.Klicken Sie dann auf **Erstellen**.

 	![Dienst aus VS 2013 erstellen, Teil 2](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013-2.png)

    

	> [WACOM.NOTE]
    > Als Teil dieses Lernprogramms erstellen Sie eine neue freie SQL-Datenbankinstanz und einen entsprechenden Server. Sie können diese neue Datenbank wiederverwenden und wie jede andere SQL-Datenbankinstanz verwalten. Sie können jeweils nur eine freie Datenbankinstanz haben. Wenn Sie bereits eine Datenbank in der Region des neuen mobilen Dienstes haben, können Sie stattdessen die vorhandene Datenbank auswählen. Wenn Sie eine vorhandene Datenbank auswählen, stellen Sie sicher, dass Sie die richtigen Anmeldeinformationen angeben. Falls Sie falsche Anmeldeinformationen angeben, wird der mobile Dienst in einem fehlerhaften Zustand erstellt.

 Nach dem Erstellen des mobilen Dienstes wird ein Verweis auf die Clientbibliothek für mobile Dienste zum Projekt hinzugefügt. Außerdem wird der Projektquellcode aktualisiert. 