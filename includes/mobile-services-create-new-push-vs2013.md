Zunächst verwenden Sie den Assistenten zum Hinzufügen von Pushbenachrichtigungen in Visual Studio 2013, um Ihre App im Windows Store zu registrieren, Ihren mobilen Dienst zur Unterstützung von Pushbenachrichtigungen zu konfigurieren und Ihrer App Code zum Registrieren eines Gerätekanals hinzuzufügen.

1.  Falls noch nicht geschehen, befolgen Sie die Schritte unter [Importieren der publishsettings-Datei in Visual Studio 2013](/de-de/documentation/articles/mobile-services-windows-how-to-import-publishsettings/), um Ihre publisher.settings-Datei in Visual Studio zu importieren.
    
    Sie müssen diesen Schritt nicht durchführen, wenn Sie Visual Studio bereits verwendet haben, um mobile Dienste im Rahmen Ihres Azure-Abonnements zu erstellen oder zu verwalten.

2.  Öffnen Sie in Visual Studio 2013 den Projektmappen-Explorer, klicken Sie mit der rechten Maustaste auf das Projekt, klicken Sie auf **Hinzufügen** und dann auf **Pushbenachrichtigung...**.
    
    ![mobile-add-push-notifications-vs2013](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png)
    
    Dadurch wird der Assistent zum Hinzufügen von Pushbenachrichtigungen gestartet.

3.  Klicken Sie auf **Weiter**, melden Sie sich bei Ihrem Windows Store-Konto an, geben Sie einen Namen unter **Reserve a new name** ein, und klicken Sie dann auf **Reservieren**.
    
    ![mobile-add-push-notifications-vs2013-2](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-2.png)
    
    Dadurch wird eine neue App-Registrierung erstellt.

4.  Klicken Sie in der Liste **App-Name** auf die neue Registrierung, und klicken Sie dann auf **Weiter**.
    
    ![mobile-add-push-notifications-vs2013-3](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-3.png)

5.  Klicken Sie im Dialogfeld **Dienst auswählen** auf den Namen des mobilen Diensts, den Sie erstellt haben, als Sie entweder [Erste Schritte mit Mobile Services](/de-de/develop/mobile/tutorials/get-started/) oder [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-dotnet/) abgeschlossen haben, und klicken Sie dann auf **Weiter** und **Fertigstellen**.
    
    Der mobile Dienst wird aktualisiert, um Ihre Anwendungspaket-SID und das Clientgeheimnis zu registrieren. Es wird eine neue Tabelle namens **channels** erstellt. Mobile Services ist nun so konfiguriert, dass es mit Windows Push Notification Services (WNS) funktioniert, um Benachrichtigungen an Ihre App senden zu können.
<div class="dev-callout">

<b>Hinweis</b>
<p>Wenn Ihre App noch nicht für die Verbindung mit dem mobilen Dienst konfiguriert ist, führt der Assistent die gleichen Konfigurationsaufgaben durch, die unter <b>Erste Schritte mit Daten</b> beschrieben sind.</p>
</div>


<!-- URLs. -->