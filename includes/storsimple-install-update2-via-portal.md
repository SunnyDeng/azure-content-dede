<!--author=alkohli last changed: 01/26/16-->

#### So installieren Sie Update 2 über das Azure-Portal

1. Wählen Sie auf der Seite des StorSimple-Diensts Ihr Gerät aus. Navigieren Sie zu **Geräte** > **Wartung**.

2. Klicken Sie unten auf der Seite auf **Updates suchen**. Für die Suche nach verfügbaren Updates wird ein Auftrag erstellt. Sie werden benachrichtigt, wenn der Auftrag erfolgreich abgeschlossen wurde.

3. Im Abschnitt **Softwareupdates** auf derselben Seite sehen Sie, dass neue Softwareupdates verfügbar sind. Es wird empfohlen, die Versionshinweise zu lesen, bevor Sie Update 2 auf Ihrem Gerät installieren.

    ![Softwareupdates installieren](./media/storsimple-install-update2-via-portal/scanupdate1.png)

4. Klicken Sie unten auf der Seite auf **Updates installieren**.

5. Sie werden aufgefordert, diesen Schritt zu bestätigen. Klicken Sie auf **OK**.

6. Das Dialogfeld **Updates installieren** wird angezeigt. Ihr Gerät sollte die in diesem Dialogfeld aufgeführten Voraussetzungen erfüllen. Diese Schritte wurden vor dem Update ausgeführt. Wählen Sie **Ich habe die oben aufgeführten Anforderungen gelesen und bin bereit, das Gerät zu aktualisieren**. Klicken Sie auf das Häkchensymbol.

    ![Bestätigungsmeldung](./media/storsimple-install-update2-via-portal/InstallUpdate12_2M.png)

7. Eine Reihe von automatischen Vorabüberprüfungen wird jetzt gestartet. Diese umfassen:

	- **Controller Integritätsprüfungen**, um sicherzustellen, dass die beiden Gerätecontroller fehlerfrei und online sind.
	
	- **Integritätsprüfungen für Hardware-Komponente**, um zu überprüfen, ob alle Hardwarekomponenten auf dem StorSimple-Gerät fehlerfrei sind.
	
	- **DATA 0-Prüfungen**, um sicherzustellen, dass DATA 0 auf Ihrem Gerät aktiviert ist. Wenn diese Schnittstelle nicht aktiviert ist, müssen Sie sie aktivieren, und es dann erneut versuchen.
	
	- **DATA 2- und DATA 3-Prüfungen**, um sicherzustellen, dass DATA 2- und DATA 3-Netzwerkschnittstellen nicht aktiviert sind. Wenn diese Schnittstellen aktiviert sind, müssen Sie deaktiviert werden, bevor Sie Ihr Gerät aktualisieren. Diese Prüfung wird nur bei der Aktualisierung auf einem Gerät durchgeführt, auf dem GA-Software ausgeführt wird. Bei Geräten, auf denen Versionen von 0.1, 0.2 oder 0.3 ausgeführt werden, ist diese Prüfung nicht erforderlich.
	
	- **Gateway-Prüfung** auf jedem Gerät, auf dem eine Version vor Update 1 ausgeführt wird. Diese Prüfung wird auf Geräten durchgeführt, auf denen Software zur Vorabprüfung 1 ausgeführt wird, aber auf Geräten fehlschlägt, auf denen ein Gateway für eine andere Netzwerkschnittstelle als DATA 0 konfiguriert ist.
 
	Update 2 wird nur angewendet, wenn alle obigen vor dem Update durchgeführten Prüfungen erfolgreich abgeschlossen wurden. Sie erhalten die Benachrichtigung, dass vor dem Update Überprüfungen ausgeführt werden.
  
    ![Benachrichtigung zur Vorabprüfung](./media/storsimple-install-update2-via-portal/InstallUpdate12_3M.png)

    Nachfolgend sehen Sie ein Beispiel, in dem die Überprüfung vor dem Upgrade nicht erfolgreich war. Sie müssen sicherstellen, dass beide Gerätecontroller fehlerfrei und online sind. Sie müssen außerdem den Zustand der Hardwarekomponenten überprüfen. In diesem Beispiel erfordern Controller 0- und Controller 1-Komponenten Ihre Aufmerksamkeit. Möglicherweise müssen Sie den Microsoft Support kontaktieren, wenn Sie diese Probleme nicht selbst beheben können.

   	 ![Fehler bei der Vorabprüfung](./media/storsimple-install-update2-via-portal/HCS_PreUpgradeChecksFailed-include.png)

	
	> [AZURE.NOTE] Wenn Sie ein Update für eine Softwareversion vor Update 1 ausführen, sind die DATA 2- und DATA 3-Prüfung sowie die Gateway-Prüfung bei zukünftigen Updates nicht mehr erforderlich, nachdem Sie Update 2 auf dem StorSimple-Gerät angewendet haben. Die anderen Vorabprüfungen sind noch immer erforderlich. Bei einer Aktualisierung von Update 1 oder einer höheren Version werden die DATA 2-, DATA 3- und Gateway-Vorabprüfungen nicht ausgeführt.


8. Wenn die Überprüfungen vor dem Upgrade erfolgreich abgeschlossen wurden, wird ein Updateauftrag erstellt. Sie werden benachrichtigt, wenn der Updateauftrag erfolgreich erstellt wurde.
 
    ![Erstellen eines Updateauftrags](./media/storsimple-install-update2-via-portal/InstallUpdate12_44M.png)

    Das Update wird dann auf Ihr Gerät angewendet.
 
9. Um den Status des Updateauftrags zu verfolgen, klicken Sie auf **Auftrag anzeigen**. Auf der Seite **Aufträge** sehen Sie den Updatestatus.
    
10. Das Update kann einige Stunden dauern. Durch Auswählen des Updateauftrags und Klicken auf **Details** können Sie jederzeit die Details des Auftrags anzeigen.
  
11. Navigieren Sie nach dem Abschließen des Auftrags zur Seite **Wartung**, und scrollen nach unten zu **Softwareupdates**.

12. Stellen Sie sicher, dass auf Ihrem Gerät **Update 2 der StorSimple 8000-Serie (6.3.9600.17673)** ausgeführt wird. Das **Datum der letzten Aktualisierung** hat sich ebenfalls geändert.


13. Sie sehen nun, dass Wartungsmodus-Updates verfügbar sind. In einigen Fällen könnte Ihre Datenträgerfirmware bereits auf dem aktuellen Stand sein, wenn Sie Update 1.2 ausführen. In diesen Fällen wird das Portal dies automatisch erkennen und Sie nicht dazu auffordern, Wartungsmodus-Updates durchzuführen.

	Die Wartungsmodus-Updates sind beeinträchtigende Updates, die zu Ausfallzeiten beim Gerät führen und nur über die Windows PowerShell-Schnittstelle Ihres Geräts angewendet werden können. Führen Sie die Schritte unter [So installieren und überprüfen Sie den Hotfix für den Wartungsmodus](#to-install-and-verify-maintenance-mode-hotfix) aus, um diese Wartungsmodus-Updates zu installieren.

> [AZURE.NOTE] In bestimmten Fällen werden Meldungen, die anzeigen, dass Updates des Wartungsmodus verfügbar sind, bis zu 24 Stunden nach erfolgreichem Anwenden dieser Updates auf dem Gerät angezeigt.

<!---HONumber=AcomDC_0128_2016-->