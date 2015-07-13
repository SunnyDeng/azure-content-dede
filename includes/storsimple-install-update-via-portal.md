#### So installieren Sie Update 1 über das Verwaltungsportal

1. Wählen Sie auf der Seite des StorSimple-Diensts Ihr Gerät aus. Navigieren Sie zu **Geräte** > **Wartung**.

2. Klicken Sie unten auf der Seite auf **Updates suchen**. Für die Suche nach verfügbaren Updates wird ein Auftrag erstellt. Sie werden benachrichtigt, wenn der Auftrag erfolgreich abgeschlossen wurde.

3. Im Abschnitt **Softwareupdates** auf derselben Seite sehen Sie, dass neue Softwareupdates verfügbar sind. Es wird empfohlen, die Versionshinweise zu lesen, bevor Sie Update 1.0 auf Ihrem Gerät installieren.

    ![Installieren von Softwareupdates](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates1-include.png)

4. Klicken Sie unten auf der Seite auf **Updates installieren**.

5. Sie werden aufgefordert, diesen Schritt zu bestätigen. Klicken Sie auf **OK**.

6. Das Dialogfeld **Updates installieren** wird angezeigt. Stellen Sie sicher, dass Ihr Gerät die in diesem Dialogfeld aufgeführten Voraussetzungen erfüllt. Wählen Sie **Ich habe die oben aufgeführten Anforderungen gelesen und bin bereit, das Gerät zu aktualisieren**. Klicken Sie auf das Häkchensymbol.

    ![Bestätigungsmeldung](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates2-include.png)

7. Sie erhalten die Benachrichtigung, dass vor dem Update Überprüfungen ausgeführt werden.
  
    ![Benachrichtigung zur Vorabprüfung](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates3-include.png)

    Nachfolgend sehen Sie ein Beispiel, in dem die Überprüfung vor dem Upgrade nicht erfolgreich war. Sie müssen sicherstellen, dass beide Gerätecontroller fehlerfrei und online sind. Sie müssen außerdem den Zustand der Hardwarekomponenten überprüfen. In diesem Beispiel erfordern Controller 0- und Controller 1-Komponenten Ihre Aufmerksamkeit. Möglicherweise müssen Sie den Microsoft Support kontaktieren, wenn Sie diese Probleme nicht selbst beheben können.

    ![Fehler bei der Vorabprüfung](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

8. Wenn die Überprüfungen vor dem Upgrade erfolgreich abgeschlossen wurden, wird ein Updateauftrag erstellt. Sie werden benachrichtigt, wenn der Updateauftrag erfolgreich erstellt wurde.
 
    ![Erstellen eines Updateauftrags](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates4-include.png)

    Das Update wird dann auf Ihr Gerät angewendet.
 
9. Um den Status des Updateauftrags zu verfolgen, klicken Sie auf **Auftrag anzeigen**. Auf der Seite "Aufträge" sehen Sie den Updatestatus.

    ![Status des Updateauftrags](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates5-include.png)

10. Das Update kann einige Stunden dauern. Sie können die Details des Auftrags jederzeit anzeigen.

    ![Details zum Updateauftrag](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates6-include.png)

11. Navigieren Sie nach dem Abschließen des Auftrags zur Seite **Wartung**, und scrollen nach unten zu **Softwareupdates**.

12. Stellen Sie sicher, dass auf Ihrem Gerät **Update 1.0 der StorSimple 8000-Serie (6.3.9600.17491)** ausgeführt wird. Das **Datum der letzten Aktualisierung** hat sich ebenfalls geändert.

    ![Seite "Wartung"](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates7-include.png)

<!---HONumber=62-->