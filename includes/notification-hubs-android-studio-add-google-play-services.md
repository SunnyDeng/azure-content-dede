1. Öffnen Sie Android SDK Manager, indem Sie in der Symbolleiste auf das Symbol von Android Studio klicken oder indem Sie im Menü auf **Tools** -> **Android** -> **SDK Manager** klicken. Suchen Sie die Zielversion des Android SDKs, das in diesem Projekt verwendet wird, öffnen Sie es, und wählen Sie **Google APIs**, falls es noch nicht installiert ist.

2. Klicken Sie auf die Registerkarte **SDK-Tools**. Wenn Sie Google Play Services noch nicht installiert haben, klicken Sie, wie unten dargestellt, auf **Google Play Services**. Klicken Sie dann auf **Übernehmen**, um die Installation auszuführen.
 
	Notieren Sie den SDK-Pfad, den Sie in einem späteren Schritt angeben müssen.

   	![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)


3. Öffnen Sie die Datei **build.gradle** im App-Verzeichnis.

	![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)

4. Fügen Sie die folgende Zeile unter *android* hinzu:

		useLibrary 'org.apache.http.legacy'

5. Fügen Sie diese Zeile unter *dependencies* hinzu:

   		compile 'com.google.android.gms:play-services-base:6.5.87'

7. Ändern Sie unter *defaultConfig* den Wert von *minSdkVersion* in 9.
 
8. Klicken Sie auf der Symbolleiste auf das Symbol **Sync Project with Gradle Files**.

9. Öffnen Sie die Datei **AndroidManifest.xml**, und fügen Sie dem *application*-Tag das folgende Tag hinzu.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=Nov15_HO1-->