> [AZURE.IMPORTANT]Um Pushbenachrichtigungen von Mobile Engagement zu erhalten, müssen Sie `Silent Remote Notifications` in Ihrer Anwendung aktivieren. Sie müssen dem "UIBackgroundModes"-Array in der Datei "Info.plist" den Remote-Benachrichtigungswert hinzufügen.

1. Öffnen Sie die Datei `info.plist` im Projekt.
2. Klicken Sie mit der rechten Maustaste auf das oberste Element in der Liste (`Information Property List`), und fügen Sie eine neue Zeile hinzu.

	![][1]

3. Geben Sie in der neuen Zeile `Required background modes` ein.

	![][2]

4. Klicken Sie auf den Pfeil nach links, um die Zeile zu erweitern.
5. Fügen Sie dem Element 0 den folgenden Wert hinzu: `App downloads content in response to push notifications`.

	![][3]

Sobald Sie die Änderung vornehmen, sollte die XML-Datei "Info.plist" den folgenden Schlüssel und Wert enthalten:

    <key>UIBackgroundModes</key>
        <array>
            <string>remote-notification</string>
        </array>
    ...
    
6. Wenn Sie Xcode 7 und iOS9 nutzen, müssen Sie die folgenden zusätzlichen Schritte ausführen: • Legen Sie **Bitcode aktivieren** unter „Ziele > Buildeinstellungen > „Bitcode auf ‚Ja‘ oder ‚Nein‘ festlegen“ auf **Nein** fest. (Stellen Sie sicher, dass Sie auf der oberen Leiste „ALLE“ auswählen.) • Aktivieren Sie unter „Ziele > Ihr Zielname > Funktionen“ **Pushbenachrichtigungen**.

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png
[2]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png
[3]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png

<!---HONumber=Sept15_HO3-->