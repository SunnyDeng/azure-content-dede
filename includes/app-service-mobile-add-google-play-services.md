1. Öffnen Sie Android SDK Manager, indem Sie in der Symbolleiste auf das Symbol von Android Studio klicken oder indem Sie im Menü auf **Tools** -> **Android** -> **SDK Manager** klicken. Wählen Sie den Link **Launch Standalone SDK Manager** aus.

2. Suchen Sie die Zielversion des Android SDK, das in Ihrem Projekt verwendet wird, und öffnen Sie es. Wenn **Google-APIs** noch nicht installiert sind, dann markieren Sie den Eintrag, und wählen Sie **Install packages...** aus.

3. Wählen Sie in Android Studio **File** > **Project Structure**. Markieren Sie **Notifications**, wählen Sie **Google Cloud Messaging** und dann **OK** aus.

<!--
3. Open **AndroidManifest.xml** and add this tag to the *application* tag.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
-->

<!---HONumber=AcomDC_0309_2016-->