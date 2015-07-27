
Die nachfolgenden Anweisungen und Screenshots gelten für das Testen eines Windows Store-Clients, aber Sie können dies auch mit beliebigen anderen Plattformen testen, die von Azure Mobile Services unterstützt werden.

1. Führen Sie die Client-App in Visual Studio aus, und versuchen Sie, sich mit dem Benutzerkonto namens "Dave" zu authentifizieren. 

    ![](./media/mobile-services-aad-rbac-test-app/dave-login.png)

2. Dave ist nicht Mitglied der Gruppe "Sales". Die rollenbasierte Zugriffsprüfung verweigert daher den Zugriff auf Tabellenvorgänge. Schließen Sie die Clientanwendung.

    ![](./media/mobile-services-aad-rbac-test-app/unauthorized.png)

3. Führen Sie die Client-App in Visual Studio erneut aus. Dieses Mal authentifizieren Sie sich mit dem Benutzerkonto namens Bob, das wir zuvor erstellt haben.

    ![](./media/mobile-services-aad-rbac-test-app/bob-login.png)

4. Bob ist Mitglied der Gruppe "Sales". Die rollenbasierte Zugriffsprüfung lässt daher den Zugriff auf Tabellenvorgänge zu.

    ![](./media/mobile-services-aad-rbac-test-app/success.png)

<!---HONumber=July15_HO3-->