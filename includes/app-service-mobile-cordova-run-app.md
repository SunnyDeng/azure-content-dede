
1. Besuchen Sie das [Azure-Portal]. Klicken Sie auf **Alle durchsuchen** > **Mobile Apps** > Back-End, das Sie gerade erstellt haben. Klicken Sie in den Einstellungen der mobilen App auf **Schnellstart** > **Cordova**. Klicken Sie unter **Clientanwendung konfigurieren**, auf **Neue App erstellen**, und klicken Sie dann auf **Download**. Damit laden Sie ein vollständiges Cordova-Projekt für eine App herunter, das für eine Verbindung mit dem Back-End vorkonfiguriert ist.

2. Entpacken Sie die heruntergeladene ZIP-Datei in ein Verzeichnis auf Ihrer Festplatte.

3. Öffnen Sie das Projekt in **Visual Studio**. Klicken Sie auf **Öffnen** > **Projekt/Projektmappe**.

4. Suchen Sie die Datei „_websitename_.sln“, und klicken Sie auf **Öffnen**.

5. Der Standardemulator ist **Ripple – Nexus (Galaxy)**. Klicken Sie auf den Dropdownpfeil neben dem Emulator, und wählen Sie **Google Android Emulator** aus.

6. Klicken Sie auf **Google Android Emulator**. Das Projekt wird erstellt und anschließend ausgeführt. Es wird möglicherweise ein Netzwerksicherheitshinweis angezeigt, da der Google Android Emulator Zugriff auf das Netzwerk anfordert. Der Google Android Emulator wird angezeigt, und die Anwendung wird ausgeführt.

7. Geben Sie in der App einen sinnvollen Text, wie z. B. _Tutorial fertigstellen_ ein, und klicken Sie dann auf die Schaltfläche „Hinzufügen“. Damit wird eine POST-Anforderung an das Azure-Back-End gesendet, das Sie zuvor bereitgestellt haben. Die Back-End fügt Daten aus der Anforderung in die TodoItem-SQL-Tabelle ein und gibt Informationen über die neu gespeicherten Elemente an die mobile App zurück. Die mobile App zeigt diese Daten in der Liste an.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)

[Azure-Portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0218_2016-->