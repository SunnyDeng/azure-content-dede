###In Xamarin Studio

1. Öffnen Sie in Xamarin.Studio die Datei **Info.plist**, und aktualisieren Sie den **Bundle Identifier** mit der zuvor erstellten ID.

    ![][88]

2. Scrollen Sie nach unten zu **Background Modes**, und aktivieren Sie **Enable Background Modes** und **Remote notifications**.

    ![][122]

3. Doppelklicken Sie im Projektmappen-Bereich auf Ihr Projekt, um die **Projektoptionen** zu öffnen.

4.  Wählen Sie unter **Build** die Option **iOS Bundle Signing** aus, und wählen Sie die **Identität** und das **Bereitstellungsprofil**, die Sie gerade für dieses Projekt eingerichtet haben.

    ![][120]

    Hierdurch wird sichergestellt, dass das Projekt das neue Profil für Codesignierung verwendet. Die offizielle Dokumentation zur Xamarin-Gerätebereitstellung finden Sie unter [Xamarin Device Provisioning] (Xamarin – Gerätebereitstellung, in englischer Sprache).

### In Visual Studio

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **Eigenschaften**.

3. Klicken Sie auf den Eigenschaftenseiten auf die Registerkarte **iOS-Anwendung**, und aktualisieren Sie den **Bezeichner** mit der zuvor erstellten ID.

    ![][123]

4. Wählen Sie auf der Registerkarte **iOS Bundle Signing** die **Identität** und das **Bereitstellungsprofil** aus, die Sie gerade für dieses Projekt eingerichtet haben.

    ![][124]

    Hierdurch wird sichergestellt, dass das Projekt das neue Profil für Codesignierung verwendet. Die offizielle Dokumentation zur Xamarin-Gerätebereitstellung finden Sie unter [Xamarin Device Provisioning] (Xamarin – Gerätebereitstellung, in englischer Sprache).


[Xamarin Device Provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/

<!---HONumber=Oct15_HO3-->