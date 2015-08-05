
## <a id="register"></a>Registrieren der App für Pushbenachrichtigungen

* [Registrieren Sie eine App-ID für Ihre App](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingProfiles/MaintainingProfiles.html#//apple_ref/doc/uid/TP40012582-CH30-SW991). Aktivieren Sie das optionale Kontrollkästchen **Pushbenachrichtigungen** in **App Services**, wenn Sie die App registrieren.

> [AZURE.NOTE]Erstellen Sie eine explizite App-ID (keine Platzhalter-App-ID), und verwenden Sie für **Bundle-ID** die genaue **Bundle-ID**, die sich in Ihrem Xcode-Schnellstartprojekt befindet. Es ist auch wichtig, dass Sie die Option **Pushbenachrichtigung** in **App-Dienste** beim Registrieren der App-ID aktivieren. Pushbenachrichtigungen funktionieren nur, wenn Sie dieses Kontrollkästchen aktivieren.

* [Aktivieren Sie danach Pushbenachrichtigungen für die lokale App](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW6). Sie können ein "Entwicklung"- oder ein "Vertrieb"-SSL-Zertifikat erstellen (denken Sie daran, später im Mobile Services-Portal die entsprechende Option auszuwählen, nämlich "Sandbox" oder "Produktion").

* [Stellen Sie danach sicher, dass die App-ID Pushbenachrichtigungen ermöglicht](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW8).

* Schließlich [aktualisieren Sie die Bereitstellungsprofile im Xcode-Schnellstartprojekt](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW10), und [überprüfen Sie dann, ob das Bereitstellungsprofil erstellt oder neu generiert wurde, um Pushbenachrichtigungen zu ermöglichen](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW12).

<!---HONumber=July15_HO4-->