<!--author=alkohli last changed: 03/17/16-->

#### So installieren Sie ein Update über das Azure-Portal

1. Wählen Sie auf der Seite des StorSimple-Diensts Ihr Gerät aus. Navigieren Sie zu **Geräte** > **Wartung**.

2. Klicken Sie unten auf der Seite auf **Updates suchen**. Für die Suche nach verfügbaren Updates wird ein Auftrag erstellt. Sie werden benachrichtigt, wenn der Auftrag erfolgreich abgeschlossen wurde.

3. Im Abschnitt **Softwareupdates** auf derselben Seite sehen Sie, dass neue Softwareupdates verfügbar sind. Es empfiehlt sich, die Versionshinweise zu lesen, bevor Sie ein Update auf Ihr Gerät anwenden.

4. Klicken Sie unten auf der Seite auf **Updates installieren** und anschließend auf **OK**.

5. Vergewissern Sie sich im Dialogfeld **Updates installieren**, dass Sie sich an die Empfehlungen gehalten haben. Wählen Sie anschließend **Ich bin mir der oben genannten Voraussetzung bewusst und zum Upgrade meines Geräts bereit.** aus, und klicken Sie dann auf die Überprüfungsschaltfläche.

    ![Bestätigungsmeldung](./media/storsimple-install-update2-via-portal/InstallUpdate12_2M.png)

7. Eine Reihe von Voraussetzungsüberprüfungen wird gestartet. Diese umfassen:

	- **Controller Integritätsprüfungen**, um sicherzustellen, dass die beiden Gerätecontroller fehlerfrei und online sind.

	- **Integritätsprüfungen für Hardware-Komponente**, um zu überprüfen, ob alle Hardwarekomponenten auf dem StorSimple-Gerät fehlerfrei sind.

	- **DATA 0-Prüfungen**, um sicherzustellen, dass DATA 0 auf Ihrem Gerät aktiviert ist. Wenn diese Schnittstelle nicht aktiviert ist, müssen Sie sie aktivieren, und es dann erneut versuchen.

	- **DATA 2- und DATA 3-Prüfungen**, um sicherzustellen, dass DATA 2- und DATA 3-Netzwerkschnittstellen nicht aktiviert sind. Wenn diese Schnittstellen aktiviert sind, müssen Sie deaktiviert werden, bevor Sie Ihr Gerät aktualisieren. Diese Prüfung wird nur bei der Aktualisierung auf einem Gerät durchgeführt, auf dem GA-Software ausgeführt wird. Bei Geräten, auf denen Versionen von 0.1, 0.2 oder 0.3 ausgeführt werden, ist diese Prüfung nicht erforderlich.

	- **Gateway-Prüfung** auf jedem Gerät, auf dem eine Version vor Update 1 ausgeführt wird. Diese Prüfung wird auf Geräten durchgeführt, auf denen Software zur Vorabprüfung 1 ausgeführt wird, aber auf Geräten fehlschlägt, auf denen ein Gateway für eine andere Netzwerkschnittstelle als DATA 0 konfiguriert ist.

	Das Update wird angewendet, wenn alle Überprüfungen erfolgreich abgeschlossen wurden. Sie werden davon in Kenntnis gesetzt, dass Überprüfungen ausgeführt werden.

    ![Benachrichtigung zur Vorabprüfung](./media/storsimple-install-update2-via-portal/InstallUpdate12_3M.png)

    Nachfolgend sehen Sie ein Beispiel, in dem die Überprüfungen nicht erfolgreich waren. Sie müssen sicherstellen, dass beide Gerätecontroller fehlerfrei und online sind. Sie müssen außerdem den Zustand der Hardwarekomponenten überprüfen. In diesem Beispiel erfordern Controller 0- und Controller 1-Komponenten Ihre Aufmerksamkeit. Möglicherweise müssen Sie den Microsoft Support kontaktieren, wenn Sie diese Probleme nicht selbst beheben können.

   	 ![Überprüfungsfehler](./media/storsimple-install-update2-via-portal/HCS_PreUpgradeChecksFailed-include.png)

8. Wenn die Überprüfungen erfolgreich abgeschlossen wurden, wird ein Updateauftrag erstellt. Sie werden benachrichtigt, wenn der Updateauftrag erfolgreich erstellt wurde.

    ![Erstellen eines Updateauftrags](./media/storsimple-install-update2-via-portal/InstallUpdate12_44M.png)

    Das Update wird dann auf Ihr Gerät angewendet.

9. Um den Status des Updateauftrags zu verfolgen, klicken Sie auf **Auftrag anzeigen**. Auf der Seite **Aufträge** sehen Sie den Updatestatus.

10. Das Update kann einige Stunden dauern. Durch Auswählen des Updateauftrags und Klicken auf **Details** können Sie jederzeit die Details des Auftrags anzeigen.

11. Navigieren Sie nach dem Abschließen des Auftrags zur Seite **Wartung**, und scrollen nach unten zu **Softwareupdates**.

<!---HONumber=AcomDC_0323_2016-->