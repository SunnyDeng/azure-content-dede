<properties 
   pageTitle="Ausführen eines Failovers für das StorSimple-Gerät"
   description="Erfahren Sie, wie Sie ein Failover des StorSimple-Geräts auf sich selbst, auf ein anderes physisches Gerät oder auf ein virtuelles Gerät durchführen."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/17/2015"
   ms.author="alkohli" />

#Ausführen eines Failovers und einer Notfallwiederherstellung für das StorSimple-Gerät

##Übersicht

In diesem Lernprogramm werden die erforderlichen Schritte für das Ausführen eines Failovers bei einem Notfall auf einem StorSimple-Gerät beschrieben. Ein Failover ermöglicht das Migrieren Ihrer Daten von einem Quellgerät im Datencenter auf ein anderes physisches oder sogar ein virtuelles Gerät an dem gleichen oder einem anderen geografischen Standort. Ein Gerätefailover wird über das Feature der Notfallwiederherstellung koordiniert und auf der Seite "Geräte" initiiert. Auf dieser Seite werden alle StorSimple-Geräte aufgeführt, die mit dem StorSimple-Manager-Dienst verbunden sind. Für jedes Gerät werden Anzeigename, Status, bereitgestellte und maximale Kapazität, Typ und Modell angezeigt.

![Seite "Geräte"](./media/storsimple-device-failover-disaster-recovery/IC740972.png)

##Notfallwiederherstellung und Gerätefailover
Bei einer Notfallwiederherstellung funktioniert das primäre Gerät nicht mehr. In diesem Fall können Sie die dem ausgefallenen Gerät zugeordneten Clouddaten auf ein anderes Gerät verschieben und dabei das primäre Gerät als *Quelle* und ein anderes Gerät als *Ziel* angeben. Sie können einen oder mehrere Volumecontainer für das Migrieren auf das Zielgerät auswählen. Dieser Vorgang wird als *Failover* bezeichnet. Während des Failovers wechseln die Volumecontainer vom Quellgerät den Eigentümer und werden auf das Zielgerät übertragen.

##Überlegungen zum Gerätefailover
Bei einem Notfall können Sie als Failoverziel für Ihr StorSimple-Gerät Folgendes auswählen:

- Ein physisches Gerät 
- Das Gerät selbst
- Ein virtuelles Gerät

Bedenken Sie bei jedem Gerätefailover Folgendes:

- Als Voraussetzung für die Notfallwiederherstellung müssen alle Volumes innerhalb der Volumecontainer offline sein, und den Volumecontainern muss eine Cloud-Momentaufnahme zugeordnet sein. 
- Die verfügbaren Zielgeräte für die Notfallwiederherstellung sind Geräte, die über ausreichenden Speicherplatz zur Aufnahme der ausgewählten Volumecontainer verfügen. 
- Geräte, die mit dem Dienst verbunden sind, aber das Speicherplatzkriterium nicht erfüllen, stehen als Zielgeräte nicht zur Verfügung.

##Failover auf ein anderes physisches Gerät

Führen Sie die folgenden Schritte aus, um Ihr Gerät auf einem physischen Zielgerät wiederherzustellen.

1. Stellen Sie sicher, dass der Volumecontainer für das Failover über zugeordnete Cloud-Momentaufnahmen verfügt.

1. Klicken Sie auf der Seite **Geräte** auf die Registerkarte **Volumecontainer**.

1. Wählen Sie einen Volumecontainer für das Failover auf einem anderen Gerät aus. Klicken Sie auf den Volumecontainer, um die Liste der Volumes innerhalb dieses Containers anzuzeigen. Wählen Sie ein Volume aus, und klicken Sie auf **Offline schalten**, um das Volume offline zu schalten. Wiederholen Sie diesen Vorgang für alle Volumes im Volumecontainer.

1. Wiederholen Sie den vorherigen Schritt für alle Volumecontainer, für die auf dem anderen Gerät ein Failover durchgeführt werden soll.

1. Klicken Sie auf der Seite "Geräte" auf **Failover**.

1. Führen Sie im angezeigten Assistenten unter **Choose volume container to failover** folgende Schritte durch:

	1. Wählen Sie in der Liste der Volumecontainer die Volumecontainer aus, für die ein Failover durchgeführt werden soll.

		>[AZURE.NOTE] **Es werden nur Volumecontainer mit zugehörigen Cloud-Momentaufnahmen und Offline-Volumes angezeigt.**

	1. Wählen Sie unter **Choose a target device for the volumes in the selected containers** ein Zielgerät aus der Dropdownliste der verfügbaren Geräte aus. Nur die Geräte, die über die verfügbare Kapazität verfügen, werden in der Dropdownliste angezeigt.

	1. Überprüfen Sie abschließend die Failover-Einstellungen unter **Failover bestätigen**. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-device-failover-disaster-recovery/IC740895.png).

1. Wechseln Sie nach Abschluss des Failovers zur Seite **Geräte**.

	1. Wählen Sie das Gerät aus, das Sie als Zielgerät für den Failovervorgang verwendet haben.

	1. Wechseln Sie zur Seite **Volumecontainer**. Es sollten alle Volumecontainer und die Volumes des alten Geräts aufgeführt werden.

##Failover mit einem einzelnen Gerät

Führen Sie die folgenden Schritte aus, wenn Sie nur über ein einzelnes Gerät verfügen und ein Failover durchführen müssen.

1. Erstellen Sie Cloud-Momentaufnahmen aller Volumes auf Ihrem Gerät.

1. Setzen Sie das Gerät auf die Werkseinstellungen zurück. Befolgen Sie die ausführlichen Anleitungen unter [Zurücksetzen des Geräts auf die Standardwerkseinstellungen](https://msdn.microsoft.com/library/dn772373.aspx).

1. Konfigurieren Sie das Gerät, und registrieren Sie es erneut im StorSimple-Manager-Dienst.

1. Auf der Seite **Geräte** sollte das alte Gerät als **Offline** angezeigt werden. Das neu registrierte Gerät sollte als **Online** angezeigt werden.

1. Führen Sie für das neue Gerät zunächst die Mindestkonfiguration durch.
												
	>[AZURE.IMPORTANT]**Wenn nicht zuerst die Mindestkonfiguration abgeschlossen wird, schlägt die Notfallwiederherstellung aufgrund eines Fehlers in der aktuellen Implementierung fehl. Dieses Verhalten wird in einer späteren Version behoben werden.**

1. Wählen Sie das alte Gerät (Status "Offline") aus, und klicken Sie auf **Failover**. Führen Sie im daraufhin angezeigten Assistenten ein Failover für dieses Gerät durch, und geben Sie das Zielgerät als neu registriertes Gerät an. Ausführliche Anweisungen finden Sie unter [Failover auf ein anderes physisches Gerät](#fail-over-to-another-physical-device).

1. Es wird ein Gerätewiederherstellungsauftrag erstellt, den Sie auf der Seite **Aufträge** überwachen können.

1. Greifen Sie, nachdem der Auftrag erfolgreich abgeschlossen wurde, auf das neue Gerät zu, und navigieren Sie zur Seite **Volumecontainer**. Alle Volumecontainer vom alten Gerät sollten auf das neue Gerät migriert worden sein.

##Failover auf ein virtuelles StorSimple-Gerät

Sie müssen zunächst ein virtuelles StorSimple-Gerät erstellen und konfigurieren, bevor Sie dieses Verfahren ausführen können.
 
>[AZURE.NOTE]**In dieser Version beträgt der auf dem virtuellen StorSimple-Gerät unterstützte Speicherplatz 30 TB.**

Führen Sie die folgenden Schritte aus, um Ihr Gerät auf einem virtuellen StorSimple-Zielgerät wiederherzustellen.

1. Stellen Sie sicher, dass der Volumecontainer für das Failover über zugeordnete Cloud-Momentaufnahmen verfügt.

1. Klicken Sie auf der Seite **Geräte** auf die Registerkarte **Volumecontainer**.

1. Wählen Sie einen Volumecontainer für das Failover auf einem anderen Gerät aus. Klicken Sie auf den Volumecontainer, um die Liste der Volumes innerhalb dieses Containers anzuzeigen. Wählen Sie ein Volume aus, und klicken Sie auf **Offline schalten**, um das Volume offline zu schalten. Wiederholen Sie diesen Vorgang für alle Volumes im Volumecontainer.

1. Wiederholen Sie den vorherigen Schritt für alle Volumecontainer, für die auf dem anderen Gerät ein Failover durchgeführt werden soll.

1. Klicken Sie auf der Seite **Geräte** auf **Failover**.

1. Führen Sie im angezeigten Assistenten unter **Choose volume container to failover** folgende Schritte durch:
													
	1. Wählen Sie in der Liste der Volumecontainer die Volumecontainer aus, für die ein Failover durchgeführt werden soll.

		>[AZURE.NOTE] **Es werden nur Volumecontainer mit zugehörigen Cloud-Momentaufnahmen und Offline-Volumes angezeigt.**

1. Wählen Sie unter **Choose a target device for the volumes in the selected containers** das virtuelle StorSimple-Gerät aus der Dropdownliste der verfügbaren Geräte aus. Nur die Geräte, die über ausreichende Kapazität verfügen, werden in der Dropdownliste angezeigt.

1. Überprüfen Sie abschließend die Failover-Einstellungen unter "Failover bestätigen". Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-device-failover-disaster-recovery/IC740895.png).

1. Wechseln Sie nach Abschluss des Failovers zur Seite **Geräte**.
													
	a. Wählen Sie das virtuelle StorSimple-Gerät aus, das Sie als Zielgerät für das Failover verwendet haben.
	
	b. Wechseln Sie zur Seite **Volumecontainer**. Es sollten jetzt alle Volumecontainer und die Volumes des alten Geräts aufgeführt werden.


##Weitere Informationen
Nachdem Sie das Failover ausgeführt haben, müssen Sie möglicherweise noch Folgendes durchführen:

- [Deaktivieren des StorSimple-Geräts](https://msdn.microsoft.com/library/azure/dn772379.aspx#deactivate)
- [Löschen des StorSimple-Geräts](https://msdn.microsoft.com/library/azure/dn772379.aspx#delete)

Informationen zum Verwalten Ihres Geräts mit dem StorSimple-Manager-Dienst finden Sie unter:

- [Administratorhandbuch](https://msdn.microsoft.com/library/dn772401.aspx)


<!--HONumber=52-->
