
1. Klicken Sie mit der rechten Maustaste in des Windows Store-Projekts, klicken Sie auf **Als Startprojekt festlegen**, und drücken Sie dann auf die Taste F5, um die Windows Store-App ausführen.
	
	Nach dem Start der App ist das Gerät für Pushbenachrichtigungen registriert.

2. Stoppen Sie die Windows Store-App, und führen Sie den obigen Schritt erneut aus, um die Windows Phone Store-App zu starten.

	Nun sind beide Geräte für den Empfang von Pushbenachrichtigungen registriert.

3. Führen Sie die Windows Store-App erneut aus, tippen Sie einen Text in **Ein TodoItem einfügen** ein, und klicken Sie anschließend auf **Speichern**.

   	![](./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push1.png)

   	Bitte beachten Sie, dass nach dem Einfügen sowohl die Windows Store-App als auch die Windows Phone-App eine Pushbenachrichtigung von WNS erhält.

   	![](./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push2.png)

	Die Benachrichtigung wird unter Windows Phone angezeigt, selbst wenn die App nicht ausgeführt wird.

   	![](./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push5-wp8.png)


<!--HONumber=42-->
