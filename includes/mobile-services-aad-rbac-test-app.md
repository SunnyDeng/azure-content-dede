1.  Führen Sie die Clientanwendung in Visual Studio aus, und versuchen Sie, sich mit dem Benutzerkonto namens Dave zu authentifizieren.

    ![][0]

2.  Dave ist nicht Mitglied der Gruppe "Sales". Die rollenbasierte Zugriffsprüfung verweigert daher den Zugriff auf Tabellenvorgänge. Schlie´ßen Sie die Clientanwendung.

    ![][1]

3.  Führen Sie die Clientanwendung in Visual Studio erneut aus. Dieses Mal authentifizieren Sie sich mit dem Benutzerkonto namens Bob, das wir zuvor erstellt haben.

    ![][2]

4.  Bob ist Mitglied der Gruppe "Sales". Die rollenbasierte Zugriffsprüfung lässt daher den Zugriff auf Tabellenvorgänge zu.

    ![][3]

  [0]: ./media/mobile-services-aad-rbac-test-app/dave-login.png
  [1]: ./media/mobile-services-aad-rbac-test-app/unauthorized.png
  [2]: ./media/mobile-services-aad-rbac-test-app/bob-login.png
  [3]: ./media/mobile-services-aad-rbac-test-app/success.png
