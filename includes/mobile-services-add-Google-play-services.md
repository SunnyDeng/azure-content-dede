1. Öffnen Sie Android SDK Manager, indem Sie in der Symbolleiste auf das Symbol von Android Studio klicken oder indem Sie im Menü auf **Tools** -> **Android** -> **SDK Manager** klicken. Suchen Sie die Zielversion des Android SDKs, das in diesem Projekt verwendet wird, öffnen Sie es, und wählen Sie **Google APIs**, falls es noch nicht installiert ist.

2. Scrollen Sie nach unten zu **Extras**, erweitern Sie diese, und wählen Sie **Google Play Services**, wie unten gezeigt. Klicken Sie auf **Install Packages**. Beachten Sie den SDK-Pfad für den folgenden Schritt.

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. Öffnen Sie die Datei **build.gradle** im App-Verzeichnis.

	![](./media/mobile-services-android-get-started-push/android-studio-push-build-gradle.png)

4. Fügen Sie diese Zeile unter *dependencies* hinzu:

   		compile 'com.google.android.gms:play-services-base:6.5.87'

5. Ändern Sie unter *defaultConfig* den Wert von *minSdkVersion* in 9.
 
6. Klicken Sie in der Symbolleiste auf die Schaltfläche **Sync Project with Gradle Files**.

7. Öffnen Sie die Datei **AndroidManifest.xml**, und fügen Sie dem *application*-Tag das folgende Tag hinzu.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 





<!--HONumber=54-->