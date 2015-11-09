
1. Klicken Sie mit der rechten Maustaste auf das Windows Store-Projekt, klicken Sie auf **Als Startprojekt festlegen**, und drücken Sie dann F5, um die Windows Store-App auszuführen.
	
	Nach dem Start der App ist das Gerät für Pushbenachrichtigungen registriert.

2. Beenden Sie die Windows Store-App, und führen Sie den obigen Schritt erneut für die Windows Phone Store-App aus.

	Nun sind beide Geräte für den Empfang von Pushbenachrichtigungen registriert.

3. Starten Sie die Windows Store-App erneut, geben Sie unter **Insert a TodoItem** Text ein, und klicken Sie dann auf **Save**.

   	Bitte beachten Sie, dass nach dem Einfügen sowohl die Windows Store-App als auch die Windows Phone-App eine Pushbenachrichtigung von WNS erhält. Die Benachrichtigung wird unter Windows Phone angezeigt, selbst wenn die App nicht ausgeführt wird.

   	![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)

<!---HONumber=Nov15_HO1-->