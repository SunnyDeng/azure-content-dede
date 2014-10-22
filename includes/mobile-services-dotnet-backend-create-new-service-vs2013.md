Mit den folgenden Schritten wird ein neuer mobiler Dienst in Azure erstellt und Ihrem Projekt Code hinzugefügt, der Ihre App mit diesem neuen Dienst verbindet. Visual Studio 2013 stellt in Ihrem Namen eine Verbindung mit Azure her, um mit Ihren Anmeldedaten den neuen mobilen Dienst zu erstellen. Wenn Sie einen neuen mobilen Dienst erstellen, müssen Sie eine Azure-SQL-Datenbank angeben, die vom mobilen Dienst zum Speichern von Anwendungsdaten verwendet wird.

1.  Öffnen Sie in Visual Studio 2013 den Projektmappen-Explorer, klicken Sie mit der rechten Maustaste auf das Windows Store-App-Projekt, und klicken Sie auf **Hinzufügen** und dann auf **Verbundener Dienst...**.

2.  Klicken Sie im Dialogfeld "Dienst-Manager" auf **Dienst erstellen...**, und wählen Sie im Dialogfeld "Mobilen Dienst erstellen" unter **Abonnement** die Option **\<Verwalten...\>** aus.

    ![Dienst erstellen – Abonnements verwalten][Dienst erstellen – Abonnements verwalten]

3.  Klicken Sie unter "Microsoft Azure-Abonnements verwalten" auf **Anmelden...**, um sich mit Ihrem Azure-Konto anzumelden (falls erforderlich), wählen Sie ein verfügbares Abonnement aus, und klicken Sie dann auf **Schließen**.

    Wenn Ihr Abonnement bereits über mindestens einen mobilen Dienst verfügt, werden die Dienstnamen angezeigt.

4.  Wählen Sie im Dialogfeld **Mobilen Dienst erstellen** Ihr **Abonnement**, unter **Runtime** die Option **.NET Framework** als Backend und die gewünschte **Region** für Ihren mobilen Dienst aus. Geben Sie dann unter **Name** einen Namen für Ihren mobilen Dienst ein.

    > [WACOM.NOTE] Namen für mobile Dienste müssen eindeutig sein. Neben **Name** wird ein rotes X angezeigt, wenn der angegebene Name nicht verfügbar ist.

5.  Wählen Sie unter **Datenbank** die Option **Freie SQL-Datenbank erstellen** aus, geben Sie den **Serverbenutzernamen**, das **Serverkennwort** und die **Serverkennwortbestätigung** ein. Klicken Sie dann auf **Erstellen**.

    ![neuen mobilen Dienst in VS 2013 erstellen][neuen mobilen Dienst in VS 2013 erstellen]

    > [WACOM.NOTE]
    > Als Teil dieses Lernprogramms erstellen Sie eine neue kostenlose SQL-Datenbankinstanz und einen entsprechenden Server. Sie können diese neue Datenbank wiederverwenden und wie jede andere SQL-Datenbankinstanz verwalten. Sie können jeweils nur eine freie Datenbankinstanz haben. Wenn Sie bereits eine Datenbank in der Region des neuen mobilen Dienstes haben, können Sie stattdessen die vorhandene Datenbank auswählen. Wenn Sie eine vorhandene Datenbank auswählen, stellen Sie sicher, dass Sie die richtigen Anmeldeinformationen angeben. Falls Sie falsche Anmeldeinformationen angeben, wird der mobile Dienst in einem fehlerhaften Zustand erstellt.

6.  Wählen Sie den neu erstellten mobilen Dienst in der Liste im Dienst-Manager aus, nachdem er erstellt wurde, und klicken Sie auf **OK**.

    Nach Abschluss des Assistenten wird das mobile Dienstprojekt Ihrer Lösung hinzugefügt, die benötigten NuGet-Pakete werden installiert, und es wird ein Verweis auf die Clientbibliothek für mobile Dienste zum Projekt hinzugefügt. Außerdem wird der Projektquellcode aktualisiert.

  [Dienst erstellen – Abonnements verwalten]: ./media/mobile-services-dotnet-backend-create-new-service-vs2013/mobile-create-service-from-vs2013.png
  [neuen mobilen Dienst in VS 2013 erstellen]: ./media/mobile-services-dotnet-backend-create-new-service-vs2013/mobile-create-service-from-vs2013-2.png
