###In Xamarin Studio

1. Öffnen Sie in Xamarin.Studio die Datei **Info.plist**, und aktualisieren Sie den **Bundle Identifier** mit der zuvor erstellten ID.

    ![][121]

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

[120]: ./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png
[121]: ./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png
[122]: ./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png
[123]: ./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png
[124]: ./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png

[Xamarin Device Provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/

<!---HONumber=August15_HO8-->