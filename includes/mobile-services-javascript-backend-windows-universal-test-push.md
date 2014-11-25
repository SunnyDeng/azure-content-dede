1.  Klicken Sie in Visual Studio mit der rechten Maustaste auf das Windows Store-Projekt, klicken Sie auf **Als Startprojekt festlegen**, und drücken Sie dann F5, um die Windows Store-App auszuführen.

    Nach dem Start der App ist das Gerät für Pushbenachrichtigungen registriert.

2.  Stoppen Sie die Windows Store-App, und führen Sie den obigen Schritt erneut aus, um die Windows Phone Store-App zu starten.

    Nun sind beide Geräte für den Empfang von Pushbenachrichtigungen registriert.

3.  Starten Sie die Windows Store-App erneut, geben Sie unter **Insert a TodoItem** Text ein, und klicken Sie dann auf **Save**.

    ![][0]

    Bitte beachten Sie, dass nach dem Einfügen sowohl die Windows Store-App als auch die Windows Phone-App eine Pushbenachrichtigung von WNS erhält.

    ![][1]

    Die Benachrichtigung wird unter Windows Phone angezeigt, selbst wenn die App nicht ausgeführt wird.

    ![][2]

  [0]: ./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push1.png
  [1]: ./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push2.png
  [2]: ./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push5-wp8.png
