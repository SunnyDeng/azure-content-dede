1. Öffnen Sie Android SDK Manager, indem Sie in der Symbolleiste auf das Symbol von Android Studio klicken oder indem Sie im Menü auf **Tools** -> **Android** -> **SDK Manager** klicken. Suchen Sie die Zielversion des Android SDKs, das in diesem Projekt verwendet wird, öffnen Sie es, und wählen Sie **Google APIs**, falls es noch nicht installiert ist.

2. Navigieren Sie zu **Datei**, **Projektstruktur**. Aktivieren Sie dann unter **Benachrichtigungen** die Pushbenachrichtigungen.

3. Öffnen Sie die Datei **AndroidManifest.xml**, und fügen Sie dem *application*-Tag das folgende Tag hinzu.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=AcomDC_1203_2015-->