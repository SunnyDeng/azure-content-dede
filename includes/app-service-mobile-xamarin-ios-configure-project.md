####Konfigurieren des iOS-Projekts in Xamarin Studio

1. Öffnen Sie in Xamarin.Studio die Datei **Info.plist**, und aktualisieren Sie den **Bundle Identifier** mit der Bundle-ID, die Sie zuvor mit Ihrer neuen App-ID erstellt haben.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)

2. Scrollen Sie nach unten zu **Background Modes**, und aktivieren Sie **Enable Background Modes** und **Remote notifications**.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)

3. Doppelklicken Sie im Projektmappen-Bereich auf Ihr Projekt, um die **Projektoptionen** zu öffnen.

4.  Wählen Sie unter **Build** die Option **iOS Bundle Signing** aus, und wählen Sie die **Identität** und das **Bereitstellungsprofil**, die Sie gerade für dieses Projekt eingerichtet haben.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

    Hierdurch wird sichergestellt, dass das Projekt das neue Profil für Codesignierung verwendet. Die offizielle Dokumentation zur Xamarin-Gerätebereitstellung finden Sie unter [Xamarin Device Provisioning] (Xamarin – Gerätebereitstellung, in englischer Sprache).

####Konfigurieren des iOS-Projekts in Visual Studio

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **Eigenschaften**.

2. Klicken Sie auf den Eigenschaftenseiten auf die Registerkarte **iOS-Anwendung**, und aktualisieren Sie den **Bezeichner** mit der zuvor erstellten ID.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)

3. Wählen Sie auf der Registerkarte **iOS Bundle Signing** die **Identität** und das **Bereitstellungsprofil** aus, die Sie gerade für dieses Projekt eingerichtet haben.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Hierdurch wird sichergestellt, dass das Projekt das neue Profil für Codesignierung verwendet. Die offizielle Dokumentation zur Xamarin-Gerätebereitstellung finden Sie unter [Xamarin Device Provisioning] (Xamarin – Gerätebereitstellung, in englischer Sprache).

4. Öffnen Sie „Info.plist“ mit einem Doppelklick, und aktivieren Sie **RemoteNotifications** unter „Background Modes“.



[Xamarin Device Provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/

<!---HONumber=AcomDC_1203_2015-->