Um einen neuen Notification Hub zur Verwendung für Pushbenachrichtigungen zu erstellen, gehen Sie wie folgt vor. Wenn Sie bereits über einen Notification Hub verfügen, können Sie ihn auch mit dem Back-End Ihrer Mobile App verbinden.

1. Klicken Sie im [Azure-Portal] auf **Durchsuchen** > **App-Dienste**, suchen Sie Ihr Mobile App-Back-End > **Alle Einstellungen** und klicken Sie darauf. Wählen Sie anschließend **Mobil** aus und klicken Sie auf **Push** > **Notification Hub**.

2. Klicken Sie auf **+Notification Hub**, geben Sie einen neuen Namen für den **Notification Hub** ein, der derselbe sein darf wie für Ihr Mobile App-Back-End, geben Sie einen neuen Namespacenamen ein oder verwenden Sie einen vorhandenen, und klicken Sie dann auf **OK** und schließlich auf **Erstellen**.

	![](./media/app-service-mobile-create-notification-hub/create-new-hub-flow.png)

	Dadurch wird ein neuer Notification Hub erstellt und verbindet ihn mit Ihrer mobilen App. Wenn Sie über einen vorhandenen Notification Hub verfügen, können Sie diesen mit Ihrem Mobile App-Back-End verbinden, statt einen neuen zu erstellen.

Jetzt haben Sie einen Nofitication Hub mit Ihrem Mobile App-Back-End verbunden. Später konfigurieren Sie diesen Notification Hub so, dass eine Verbindung mit einem Platform Notification Service (PNS) hergestellt wird, der an das systemeigene Gerät Pushbenachrichtigungen sendet.

[Azure-Portal]: https://portal.azure.com/

<!---HONumber=AcomDC_1203_2015-->