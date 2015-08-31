<properties 
   pageTitle="Verwalten von StorSimple-Gerätecontrollern | Microsoft Azure"
   description="Sie erfahren, wie Sie StorSimple-Gerätecontroller beenden, neu starten, herunterfahren oder zurücksetzen."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/13/2015"
   ms.author="alkohli" />

# Verwalten von StorSimple-Gerätecontrollern

## Übersicht
In diesem Tutorial werden die verschiedenen Vorgänge beschrieben, die auf den Gerätecontrollern von StorSimple durchgeführt werden können. Die Controller in Ihrem StorSimple-Gerät sind redundante Controller (Peer-Controller) in einer Aktiv/Passiv-Konfiguration. Es ist jeweils nur ein Controller aktiv, der alle Datenträger- und Netzwerkvorgänge verarbeitet. Der andere Controller befindet sich im passiven Modus. Wenn der aktive Controller ausfällt, wird automatisch der passive Controller aktiviert.

Dieses Tutorial enthält eine Schritt-für-Schritt-Anleitung zum Verwalten der Gerätecontroller mit folgenden Verfahren:

- Abschnitt **Controller** auf der Seite **Wartung** des StorSimple Manager-Diensts
- Windows PowerShell für StorSimple 

Es wird empfohlen, die Gerätecontroller über den StorSimple Manager-Dienst zu verwalten. Wenn eine Aktion nur mithilfe von Windows PowerShell für StorSimple durchgeführt werden kann, ist dies im Tutorial entsprechend angegeben.

In diesem Tutorial erfahren Sie Folgendes:

- Neustarten oder Herunterfahren eines StorSimple-Gerätecontrollers
- Herunterfahren eines StorSimple-Geräts
- Zurücksetzen des StorSimple-Geräts auf die Herstellerstandards


## Neustarten oder Herunterfahren eines einzelnen Controllers

Während des normalen Betriebs ist das Neustarten oder Herunterfahren normalerweise nicht erforderlich. Das Herunterfahren eines einzelnen Gerätecontrollers muss nur in Fällen durchgeführt werden, in denen eine ausgefallene Komponente der Gerätehardware ausgetauscht werden muss. Der Neustart eines Controllers kann auch erforderlich sein, wenn die Leistung durch übermäßige Arbeitsspeicherauslastung oder einen fehlerhaften Controller beeinträchtigt ist. Unter Umständen müssen Sie einen Controller auch nach einem erfolgreichen Controlleraustausch neu starten, um den neuen Controller zu aktivieren und zu testen.

Das Neustarten eines Geräts hat keine schädlichen Auswirkungen auf verbundene Initiatoren, sofern der passive Controller verfügbar ist. Wenn kein passiver Controller verfügbar ist oder wenn er ausgeschaltet ist, kann das Neustarten des aktiven Controllers zu Störungen des Diensts und zu Ausfällen führen.

> [AZURE.IMPORTANT]

> - **Ein aktiver Controller sollte nie physisch entfernt werden, da dies zu einem Verlust der Redundanz führen und das Ausfallrisiko erhöhen würde.**

> - Das folgende Verfahren gilt nur für das physische StorSimple-Gerät. Informationen zum Starten, Beenden und Neustarten des virtuellen Geräts finden Sie unter [Arbeiten mit dem virtuellen Gerät](storsimple-virtual-device-u1.md#work-with-the-storsimple-virtual-device).

Sie können einen einzelnen Gerätecontroller wie folgt neu starten oder herunterfahren:

- Verwaltungsportal des StorSimple Manager-Diensts
- Windows PowerShell für StorSimple 

Führen Sie die unten angegebenen Schritte aus, um Gerätecontroller über das Verwaltungsportal zu verwalten.

#### So starten Sie einen Controller im Verwaltungsportal neu oder fahren ihn herunter

1. Navigieren Sie zu **Geräte > Wartung**.

1. Wechseln Sie zu **Hardwarestatus**, und stellen Sie sicher, dass der Status beider Controller auf Ihrem Gerät **Fehlerfrei** lautet.

	![Überprüfen der Integrität von StorSimple-Gerätecontrollern](./media/storsimple-manage-device-controller/IC766017.png)

1. Klicken Sie unten auf der Seite **Wartung** auf **Controller verwalten**.

	![Verwalten von StorSimple-Gerätecontrollern](./media/storsimple-manage-device-controller/IC766018.png)</br>

	>[AZURE.NOTE]Wenn die Option **Controller verwalten** nicht angezeigt wird, müssen Sie Updates installieren. Weitere Informationen finden Sie unter [Aktualisieren von StorSimple-Geräten](storsimple-update-device.md).

1. Gehen Sie im Dialogfeld **Controller-Einstellungen ändern** wie folgt vor:


	- Wählen Sie in der Dropdownliste **Controller auswählen** den Controller aus, den Sie verwalten möchten. Die Optionen sind „Controller 0“ und „Controller 1“. Es wird auch angegeben, ob es sich bei diesen Controllern um aktive oder passive Controller handelt.

		>[AZURE.NOTE]Ein Controller kann nicht verwaltet werden, wenn er nicht verfügbar oder ausgeschaltet ist, und er wird dann auch nicht in der Dropdownliste angezeigt.
	


	- Wählen Sie in der Dropdownliste **Aktion auswählen** die Option **Controller neu starten** oder **Controller herunterfahren** aus.
	
		![Neustarten des passiven StorSimple-Gerätecontrollers](./media/storsimple-manage-device-controller/IC766020.png)
 

	- Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-manage-device-controller/IC740895.png).

Der Controller wird neu gestartet bzw. heruntergefahren. In der folgenden Tabelle ist zusammengefasst, was je nach den Einstellungen passiert, die Sie im Dialogfeld **Controller-Einstellungen ändern** gewählt haben.
													

|Auswahlnr.|Gewählte Einstellung|Ausgeführte Aktion|
|---|---|---|
|1\.|Neustarten des passiven Controllers|Es wird ein Auftrag zum Neustarten des Controllers erstellt, und Sie werden benachrichtigt, nachdem die Erstellung erfolgreich abgeschlossen wurde. Hiermit wird der Neustart des Controllers initiiert. Sie können den Neustartprozess überwachen, indem Sie auf **Dienst > Dashboard > Vorgangsprotokolle anzeigen** zugreifen und dann nach den Parametern für Ihren Dienst filtern.|
|2\.|Neustarten des aktiven Controllers|Die folgende Warnung wird angezeigt: „Wenn Sie den aktiven Controller neu starten, erfolgt ein Failover des Geräts auf den passiven Controller. Möchten Sie fortfahren?“ </br>Wenn Sie sich für das Fortsetzen des Vorgangs entscheiden, sind die nächsten Schritte mit den Schritten zum Neustarten des passiven Controllers identisch (siehe Auswahl 1).|
|3\.|Herunterfahren des passiven Controllers|Die folgende Meldung wird angezeigt: „Nach dem Herunterfahren müssen Sie den Netzschalter Ihres Controllers betätigen, um ihn zu aktivieren. Sind Sie sicher, dass Sie diesen Controller herunterfahren möchten?“ </br>Wenn Sie sich für das Fortsetzen des Vorgangs entscheiden, sind die nächsten Schritte mit den Schritten zum Neustarten des passiven Controllers identisch (siehe Auswahl 1).|
|4\.|Herunterfahren des aktiven Controllers|Die folgende Meldung wird angezeigt: „Nach dem Herunterfahren müssen Sie den Netzschalter Ihres Controllers betätigen, um ihn zu aktivieren. Sind Sie sicher, dass Sie diesen Controller herunterfahren möchten?“ </br>Wenn Sie sich für das Fortsetzen des Vorgangs entscheiden, sind die nächsten Schritte mit den Schritten zum Neustarten des passiven Controllers identisch (siehe Auswahl 1).|


#### So starten Sie einen Controller in Windows PowerShell für StorSimple neu oder fahren ihn herunter
Führen Sie die folgenden Schritte aus, um einen einzelnen Controller auf dem StorSimple-Gerät über das Verwaltungsportal herunterzufahren oder neu zu starten.


1. Greifen Sie auf das Gerät zu, indem Sie die serielle Konsole oder eine Telnet-Sitzung von einem Remotecomputer verwenden. Stellen Sie eine Verbindung mit Controller 0 oder Controller 1 her, indem Sie die Schritte unter [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console) ausführen.

1. Wählen Sie im Menü der seriellen Konsole Option 1 aus, d. h. die** Anmeldung mit Vollzugriff**.

1. Notieren Sie sich in der Bannermeldung den Controller, mit dem Sie verbunden sind (Controller 0 oder Controller 1), und ob es sich um den aktiven oder den passiven Controller (Standby) handelt.
	

	- Geben Sie zum Herunterfahren eines einzelnen Controllers an der Eingabeaufforderung Folgendes ein:

		`Stop-HcsController`

		Der Controller, mit dem Sie verbunden sind, wird heruntergefahren. Wenn Sie den aktiven Controller beenden, wird vor dem Herunterfahren ein Failover zum passiven Controller durchgeführt.


	- Geben Sie an der Eingabeaufforderung Folgendes ein, um einen Controller neu zu starten:

		`Restart-HcsController`

		Der Controller, mit dem Sie verbunden sind, wird neu gestartet. Wenn Sie den aktiven Controller neu starten, wird vor dem Neustart ein Failover zum passiven Controller durchgeführt.


## Herunterfahren eines StorSimple-Geräts

In diesem Abschnitt wird erklärt, wie Sie ein aktives oder ausgefallenes StorSimple-Gerät über einen Remotecomputer herunterfahren. Ein Gerät wird ausgeschaltet, nachdem beide Gerätecontroller heruntergefahren wurden. Das Herunterfahren eines Geräts erfolgt, wenn das Gerät physisch bewegt oder außer Dienst gestellt wird.

> [AZURE.IMPORTANT]Überprüfen Sie vor dem Herunterfahren des Geräts die Integrität der Gerätekomponenten. Navigieren Sie zu **Geräte > Wartung > Hardwarestatus**, und stellen Sie sicher, dass der LED-Status aller Komponenten grün ist. Nur ein fehlerfreies Gerät verfügt über einen grünen Status. Wenn Ihr Gerät heruntergefahren wird, um eine fehlerhafte Komponente auszutauschen, wird für die jeweilige Komponente der Fehlerstatus (rot) oder der heruntergestufte Status (gelb) angezeigt.

#### So fahren Sie ein StorSimple-Gerät herunter

1. Verwenden Sie das Verfahren [Neustarten oder Herunterfahren eines Controllers](#restart-or-shut-down-a-single-controller), um den passiven Controller auf Ihrem Gerät zu identifizieren und herunterzufahren. Sie können diesen Vorgang im Verwaltungsportal oder in Windows PowerShell für StorSimple durchführen.
2. Wiederholen Sie den obigen Schritt, um den aktiven Controller herunterzufahren.
3. Achten Sie hierbei auf die Rückwandplatine des Geräts. Nachdem die beiden Controller vollständig heruntergefahren wurden, sollten die Status-LEDs für beide Controller rot blinken. Wenn Sie das Gerät nun vollständig ausschalten müssen, bewegen Sie die Schalter an beiden Modulen (Power and Cooling Modules, PCMs) in die Position AUS. Das Gerät sollte hierdurch ausgeschaltet werden.


<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

	`Stop-HCSController`

	This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## Zurücksetzen des Geräts auf die werkseitigen Standardeinstellungen

Dieses Verfahren enthält die ausführlichen Schritte zum Zurücksetzen Ihres Microsoft Azure StorSimple-Geräts auf die werkseitigen Standardeinstellungen mit Windows PowerShell für StorSimple.

Führen Sie die folgenden Schritte aus, um das Microsoft Azure StorSimple-Gerät auf die werkseitigen Standardeinstellungen zurückzusetzen:

### So setzen Sie das Gerät in Windows PowerShell für StorSimple auf die Standardeinstellungen zurück

1. Greifen Sie über die serielle Konsole auf das Gerät zu. Überprüfen Sie die Bannermeldung, um sicherzustellen, dass Sie mit dem aktiven Controller verbunden sind.

1. Wählen Sie im Menü der seriellen Konsole Option 1 aus, d. h. die** Anmeldung mit Vollzugriff**.

1. Geben Sie an der Eingabeaufforderung den folgenden Befehl ein:

	`Reset-HcsFactoryDefault`

	Das System wird mehrmals neu gestartet. Sie werden benachrichtigt, wenn das Zurücksetzen erfolgreich abgeschlossen wurde. Je nach Systemmodell kann es 45 bis 60 Minuten dauern, bis dieser Prozess für ein Gerät der Serie 8100 beendet ist. Für ein Gerät der Serie 8600 kann dieser Prozess 60 bis 90 Minuten dauern.

	> [AZURE.TIP]
	
	> - Verwenden Sie den Befehl `Reset-HcsFactoryDefault –SkipFirmwareVersionCheck`, um die Prüfung der Firmwareversion zu überspringen, wenn das Cmdlet zum Zurücksetzen auf die werksseitigen Einstellungen (wie oben verwendet) einen Fehler aufgrund eines Firmwarekonflikts meldet: Die Zurücksetzung auf die werksseitigen Einstellungen kann aufgrund eines Konflikts bei den Firmwareversionen nicht fortgesetzt werden. Sie müssen die Firmwareprüfung überspringen (mithilfe der Option `–SkipFirmwareCheck`), wenn Sie die Zurücksetzung auf die werksseitigen Einstellungen für ein Gerät durchführen, das zuvor per Microsoft Update oder Hotfixmechanismus aktualisiert wurde.
	
	> - Weitere Informationen zur Verwendung dieses Cmdlets finden Sie in der [Cmdlet-Referenz für Windows PowerShell für StorSimple](https://technet.microsoft.com/library/dn688168.aspx).


## Fragen und Antworten zur Verwaltung von Gerätecontrollern

In diesem Abschnitt haben wir einige der häufig gestellten Fragen zur Verwaltung von StorSimple-Gerätecontrollern zusammengefasst.

**F.** Was passiert, wenn beide Controller auf meinem Gerät fehlerfrei und eingeschaltet sind und ich den aktiven Controller neu starte oder herunterfahre?

**A.** Wenn beide Controller auf Ihrem Gerät fehlerfrei und eingeschaltet sind, werden Sie zur Bestätigung aufgefordert. Sie können wie folgt vorgehen:

- **Aktiven Controller neu starten** – Sie werden benachrichtigt, dass der Neustart eines aktiven Controllers bewirkt, dass das Gerät ein Failover auf den passiven Controller durchführt. Der Controller wird neu gestartet.

- **Aktiven Controller herunterfahren** – Sie werden benachrichtigt, dass das Herunterfahren eines aktiven Controllers zu Ausfallzeiten führt. Außerdem müssen Sie den Ein/Aus-Schalter am Gerät betätigen, um den Controller einzuschalten.

**F.** Was passiert, wenn der passive Controller auf meinem Gerät nicht verfügbar oder ausgeschaltet ist und ich den aktiven Controller neu starte oder herunterfahre?

**A.** Wenn der passive Controller auf dem Gerät nicht verfügbar oder ausgeschaltet ist, ist der Ablauf in den unten angegebenen Fällen wie folgt:

- **Aktiven Controller neu starten** – Sie werden benachrichtigt, dass das Fortsetzen des Vorgangs zu einer vorübergehenden Störung des Diensts führt, und Sie werden zur Bestätigung aufgefordert.

- **Aktiven Controller herunterfahren** – Sie werden benachrichtigt, dass das Fortsetzen des Vorgangs zu Ausfallzeiten führt und dass Sie den Ein/Aus-Schalter an einem oder beiden Controllern betätigen müssen, um das Gerät einzuschalten. Sie werden aufgefordert, diesen Schritt zu bestätigen.

**F.** In welchen Fällen kann das Neustarten oder Herunterfahren des Controllers nicht fortgesetzt werden?

**A.** Beim Neustarten oder Herunterfahren eines Controllers kann in den folgenden Fällen ein Fehler auftreten:

- Es wird ein Update des Geräts durchgeführt.

- Es wird bereits ein Neustart des Controllers durchgeführt.

- Das Herunterfahren des Controllers wird bereits durchgeführt.

**F.** Wie kann ermittelt werden, ob ein Controller neu gestartet oder heruntergefahren wurde?

**A.** Sie können den Controllerstatus auf der Seite „Wartung“ überprüfen. Mit dem Controllerstatus wird angegeben, ob ein Controller neu gestartet oder heruntergefahren wurde. Außerdem enthält die Seite „Warnungen“ eine Warnung vom Typ „Information“, wenn der Controller neu gestartet oder heruntergefahren wurde. Die Vorgänge des Neustartens und Herunterfahrens von Controllern werden zudem in den Vorgangsprotokollen aufgezeichnet. Weitere Informationen zu Vorgangsprotokollen finden Sie unter [Anzeigen der Vorgangsprotokolle](storsimple-service-dashboard.md#view-the-operations-logs).

**F.** Wirkt sich ein Failover des Controllers auf die Ein- und Ausgabe aus?

**A.** Die TCP-Verbindungen zwischen Initiatoren und aktivem Controller werden bei einem Failover des Controllers zurückgesetzt, aber wiederhergestellt, wenn der passive Controller den Betrieb übernimmt. Im Verlauf dieses Vorgangs kann es zu einer vorübergehenden Pause (weniger als 30 Sekunden) bei der E/A-Aktivität zwischen Initiatoren und dem Gerät kommen.

**F.** Wie kann ich den Controller wieder in Betrieb nehmen, nachdem er heruntergefahren und entfernt wurde?

**A.** Wenn Sie einen Controller wieder in Betrieb nehmen möchten, müssen Sie ihn wie unter [Ersetzen des Controllermoduls auf dem StorSimple-Gerät](storsimple-controller-replacement.md) beschrieben wieder in das Gehäuse einsetzen.

## Nächste Schritte

[Wenden Sie sich an den Microsoft Support](storsimple-contact-microsoft-support.md), falls ein Problem mit Ihren StorSimple-Gerätecontrollern auftritt, das Sie mit den Verfahren in diesem Tutorial nicht beheben können.

<!---HONumber=August15_HO8-->