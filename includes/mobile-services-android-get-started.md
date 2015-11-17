Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

### Laden des Projekts in Android Studio laden und Synchronisieren von Gradle

1. Navigieren Sie zu dem Verzeichnis, in dem Sie die komprimierten Projektdateien gespeichert haben, und erweitern Sie die Dateien auf Ihrem Computer in das Android Studio-Projektverzeichnis.

2. Öffnen Sie Android Studio. Wenn Sie mit einem Projekt arbeiten und es angezeigt wird, schließen Sie das Projekt (Datei = > Projekt schließen).

3. Wählen Sie die **Option zum Öffnen eines vorhandenen Projekts für Android Studio** aus, navigieren Sie zum Projektspeicherort, und klicken Sie dann auf **OK**. Dadurch wird das Projekt geladen und die Synchronisierung mit Gradle gestartet.

 	![](./media/mobile-services-android-get-started/android-studio-import-project.png)

4. Warten Sie, bis die Gradle-Synchronisierungsaktivität abgeschlossen ist. Ein Fehler vom Typ "Ziel nicht gefunden" angezeigt wird, ist der Grund, dass die in Android Studio verwendete Version nicht der des Beispiels entspricht. Die einfachste Möglichkeit zur Behebung dieses Problems ist, auf den Link **Fehlende Plattformen installieren und Projekt synchronisieren** in der Fehlermeldung zu klicken. Ggf. werden weitere Versionsfehlermeldungen angezeigt. Wiederholen Sie dann einfach diesen Vorgang , bis keine Fehler mehr angezeigt werden.
    - Es gibt eine andere Möglichkeit, dieses Problem zu beheben, wenn Sie mit der "neuesten und besten" Android-Version arbeiten möchten. Sie können die **TargetSdkVersion** in der Datei *build.gradle* im Verzeichnis *app* entsprechend der Version aktualisieren, die bereits auf Ihrem Computer installiert ist. Diese können Sie herausfinden, indem Sie auf das Symbol **SDK-Manager** klicken und prüfen, welche Version aufgeführt ist. Als Nächstes klicken Sie auf das Symbol **Projekt mit Gradle-Dateien synchronisieren**. Möglicherweise erhalten Sie eine Fehlermeldung zur Build Tools-Version, und Sie auf die gleiche Weise beheben können.

### Ausführen der App

Sie können die Anwendung mit dem Emulator oder einem echten Gerät ausführen.

1. Um sie auf einem Gerät auszuführen, verbinden Sie es über ein USB-Kabel mit dem Computer. Sie müssen [das Gerät für die Entwicklung einrichten](https://developer.android.com/training/basics/firstapp/running-app.html). Wenn Sie auf einem Windows-Computer entwickeln, müssen Sie auch einen USB-Treiber herunterladen und installieren.

2. Um die App im Android-Emulator auszuführen, müssen Sie mindestens ein virtuelles Android-Gerät (AVD) definieren. Klicken Sie auf das AVD-Manager-Symbol, um diese Geräte zu erstellen und zu verwalten.

3. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um das Projekt zu starten. Wählen Sie im eingeblendeten Dialogfeld ein Gerät oder einen Emulator aus.

4. Wenn die App angezeigt wird, geben Sie einen sinnvollen Text ein, beispielsweise _Lernprogramm beenden_, und klicken Sie dann auf **Add**.

   	![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)

   	Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

	> [AZURE.NOTE]Sie können den Code überprüfen, der auf Ihren Mobile Service zugreift, um Daten abzufragen und einzufügen. Sie finden ihn in der ToDoActivity.java-Datei.

8. Zurück im Verwaltungsportal klicken Sie auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

   	![](./media/mobile-services-android-get-started/mobile-data-tab1.png)

   	Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

   	![](./media/mobile-services-android-get-started/mobile-data-browse.png)

<!---HONumber=Nov15_HO3-->