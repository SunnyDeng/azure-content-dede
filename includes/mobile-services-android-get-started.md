Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1. Navigieren Sie zu dem Verzeichnis, in dem Sie die komprimierten Projektdateien gespeichert haben, und erweitern Sie die Dateien auf Ihrem Computer in das Android Studio-Projektverzeichnis.

2. Öffnen Sie Android Studio. Wenn Sie mit einem Projekt arbeiten und es angezeigt wird, schließen Sie das Projekt (Datei = > Projekt schließen).

3. Wählen Sie die Option zum **Öffnen eines vorhandenen Projekts für Android Studio** aus, navigieren Sie zum Projektspeicherort, und klicken Sie dann auf **OK**. 

 	![](./media/mobile-services-android-get-started/android-studio-import-project.png)

4. Stellen Sie im linken **Projektexplorer**-Fenster sicher, dass die Registerkarte *Project* ausgewählt ist, öffnen Sie dann **app**, **src**, **java**, und doppelklicken Sie anschließend auf **ToDoactivity**.

   	![](./media/mobile-services-android-get-started/Android-Studio-quickstart.png)


5. Wenn Sie Version 2.0 des SDK heruntergeladen haben, müssen Sie den Code mit der URL und dem Schlüssel des mobilen Diensts aktualisieren:
	- 	Suchen Sie die **OnCreate**-Methode in **TodoActivity.java**, und suchen Sie den Code, der den Mobile Services-Client instanziiert. Der Code ist in der Abbildung oben dargestellt.
	- 	Ersetzen Sie "MobileServiceUrl" durch den tatsächlichen URL des mobilen Diensts.
	- 	Ersetzen Sie "AppKey" durch den Schlüssel des mobilen Diensts.
	- 	Weitere Informationen finden Sie im Lernprogramm [Hinzufügen von Mobile Services zu einer vorhandenen App](../articles/mobile-services-android-get-started-data.md). 

6. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um das Projekt im Android-Emulator zu starten.

	> [AZURE.IMPORTANT] Sie müssen mindestens ein Android Virtual Device (AVD) definieren, um das Projekt im Android-Emulator auszuführen. Verwenden Sie den AVD Manager, um diese Geräte zu erstellen und zu verwalten.

7. Geben Sie in der App einen sinnvollen Text ein, beispielsweise _Lernprogramm abschließen_, und klicken Sie dann auf **Hinzufügen**.

   	![][10]

   	Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

	> [AZURE.NOTE] Sie können den Code überprüfen, der auf Ihren mobilen Dienst zugreift, um Daten abzufragen und einzufügen. Der Code befindet sich in der Datei "ToDoActivity.java".

8. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItems**.

   	![](./media/mobile-services-android-get-started/mobile-data-tab1.png)

   	Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

   	![](./media/mobile-services-android-get-started/mobile-data-browse.png)


<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-android-get-started/Android-Studio-quickstart.png
[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png
[14]: ./media/mobile-services-android-get-started/android-studio-import-project.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Hinzufügen von Mobile Services zu einer vorhandenen App]: ../articles/mobile-services-android-get-started-data.md
[Erste Schritte mit der Authentifizierung]: ../articles/mobile-services-android-get-started-users.md
[Erste Schritte mit Pushbenachrichtigungen]: ../articles/mobile-services-javascript-backend-android-get-started-push.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Verwaltungsportal]: https://manage.windowsazure.com/

<!--HONumber=52-->