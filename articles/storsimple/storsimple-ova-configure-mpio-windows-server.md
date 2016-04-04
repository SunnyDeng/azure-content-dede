<properties 
   pageTitle="Konfigurieren von MPIO auf Ihrem virtuellen StorSimple-Arrayhost | Microsoft Azure"
   description="Beschreibt, wie Sie Multipfad-E/A (MPIO) für Ihr virtuelles StorSimple-Array konfigurieren, das mit einem Host unter Windows Server 2012 R2 verbunden ist."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/22/2016"
   ms.author="alkohli" />

# Konfigurieren von Multipfad-E/A auf Windows Server-Host für das virtuelle StorSimple-Array

## Übersicht

Dieser Artikel beschreibt die Installation der Funktion für Multipfad-E/A (MPIO, Multipath I/O) auf dem Windows Server-Host, die Anwendung bestimmter Konfigurationseinstellungen für reine StorSimple-Volumes und die MPIO-Überprüfung für StorSimple-Volumes. Das Verfahren setzt voraus, dass Ihr virtuelles StorSimple 1200-Array mit zwei Netzwerkschnittstellen mit einem Windows Server-Host mit zwei Netzwerkschnittstellen verbunden ist. Die in diesem Artikel enthaltenen Informationen gelten nur für das virtuelle Array. Informationen zu Geräten der StorSimple 8000-Serie finden Sie unter [Konfigurieren von Multipfad-E/A für Ihr StorSimple-Gerät](storsimple-configure-mpio-windows-server.md).

Die MPIO-Funktion in Windows Server unterstützt das Erstellen hoch verfügbarer, fehlertoleranter Speicherkonfigurationen. MPIO verwendet redundante physische Pfadkomponenten (Adapter, Kabel und Switches), um logische Pfade zwischen dem Server und dem Speichergerät zu erstellen. Wenn bei einer Komponente ein Fehler auftritt, durch den ein logischer Pfad fehlschlägt, verwendet die Multipfad-Logik einen anderen Pfad für E/A, sodass Anwendungen weiterhin auf ihre Daten zugreifen können. Darüber hinaus kann MPIO abhängig von Ihrer Konfiguration auch die Leistung durch ein Umverteilen der Lasten auf alle Pfade verbessern. Weitere Informationen finden Sie unter [Multipfad-E/A (Übersicht)](https://technet.microsoft.com/library/cc725907.aspx "MPIO – Übersicht und Features").

Konfigurieren Sie MPIO für die hohe Verfügbarkeit Ihrer StorSimple-Lösung auf den Windows Server-Hosts, die mit Ihrem virtuellen StorSimple-1200-Array (auch als lokales virtuelles Gerät bezeichnet) verbunden sind. Die Hostserver können dann einen Verknüpfungs-, Netzwerk oder Schnittstellenfehler tolerieren.

Führen Sie die folgenden Schritte aus, um MPIO zu konfigurieren:

- Konfigurationsvoraussetzungen

- Schritt 1: Installieren von MPIO auf dem Windows Server-Host

- Schritt 2: Konfigurieren von MPIO für StorSimple-Volumes

- Schritt 3: Bereitstellen von StorSimple-Volumes auf dem Host

Jeder der oben genannten Schritte wird in den folgenden Abschnitten erläutert.


## Voraussetzungen

In diesem Abschnitt werden die Konfigurationsvoraussetzungen für den Windows Server-Host und Ihr virtuelles Array im Detail beschrieben.

### Auf dem Windows Server-Host

-  Stellen Sie sicher, dass Ihr Windows Server-Host über zwei aktivierte Netzwerkschnittstellen verfügt.


### Auf dem virtuellen StorSimple-Array

- Das virtuelle Array sollte als iSCSI-Server konfiguriert werden. Weitere Informationen finden Sie unter [Bereitstellen von StorSimple Virtual Array – Einrichten des virtuellen Geräts als iSCSI-Server](storsimple-ova-deploy3-iscsi-setup.md). Mindestens eine Netzwerkschnittstelle sollte auf dem Array aktiviert sein.   

- Die Netzwerkschnittstellen auf Ihrem virtuellen Array sollten vom Windows Server-Host aus erreichbar sein.

- Mindestens ein Volume sollte auf Ihrem virtuellen StorSimple-Array erstellt werden. Weitere Informationen zum Hinzufügen eines Volumes auf Ihrem virtuellen StorSimple-1200-Array finden Sie unter [Hinzufügen eines Volumes](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume). In diesem Verfahren haben wir 3 Volumes (2 lokal angeheftete und 1 mehrstufiges Volume, wie unten gezeigt) auf dem virtuellen Array erstellt.
	
	![mpio0](./media/storsimple-ova-configure-mpio-windows-server/mpio0.png)

### Hardwarekonfiguration für virtuelles StorSimple-Array

Die nachstehende Abbildung zeigt die Hardwarekonfiguration für hohe Verfügbarkeit und Lastenausgleich mit Multipfad für Ihren Windows Server-Host und das in diesem Verfahren verwendete virtuelle StorSimple-Array.

![MPIO-Hardwarekonfiguration](./media/storsimple-ova-configure-mpio-windows-server/1200hardwareconfig.png)

Die Abbildung oben zeigt Folgendes:

- Das auf Hyper-V bereitgestellte virtuelle StorSimple-Array ist ein aktives Einzelknotengerät, das als iSCSI-Server konfiguriert ist.

- Auf Ihrem Array sind zwei virtuelle Netzwerkschnittstellen aktiviert. Überprüfen Sie in der lokalen Webbenutzeroberfläche Ihres virtuellen 1200-Arrays, ob zwei Netzwerkschnittstellen aktiviert sind, indem Sie wie unten dargestellt zu den **Netzwerkeinstellungen** wechseln:

	![Auf 1200 aktivierte Netzwerkschnittstellen](./media/storsimple-ova-configure-mpio-windows-server/mpio9.png)
	
	Notieren Sie sich die IPv4-Adressen der aktivierten Netzwerkschnittstellen (Ethernet, Ethernet 2 in der Standardeinstellung), und legen Sie die Notiz für die spätere Verwendung auf dem Host zurück.

- Auf Ihrem Windows Server-Host sind zwei Netzwerkschnittstellen aktiviert. Wenn die verbundenen Schnittstellen für Host und Array sich im gleichen Subnetz befinden, dann sind 4 Pfade verfügbar. Dies ist in diesem Verfahren der Fall. Wenn sich jedoch jede Netzwerkschnittstelle des Arrays und die Hostschnittstelle in einem unterschiedlichen IP-Subnetz befinden (und nicht routingfähig sind), stehen nur 2 Pfade zur Verfügung.

## Schritt 1: Installieren von MPIO auf dem Windows Server-Host

MPIO ist ein optionales Feature und wird unter Windows Server nicht standardmäßig installiert. Diese Funktion sollte als Feature über den Server-Manager installiert werden. Gehen Sie folgendermaßen vor, um dieses Feature auf Ihrem Windows Server-Host zu installieren.

[AZURE.INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]


## Schritt 2: Konfigurieren von MPIO für StorSimple-Volumes

MPIO muss konfiguriert werden, um StorSimple-Volumes zu erkennen. Führen Sie zum Konfigurieren von MPIO für die Erkennung von StorSimple-Geräten die folgenden Schritte aus.

[AZURE.INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## Schritt 3: Bereitstellen von StorSimple-Volumes auf dem Host

Nachdem MPIO unter Windows Server konfiguriert wurde, können auf dem StorSimple-Array erstellte Volumes bereitgestellt werden und anschließend MPIO für Redundanz nutzen. Führen Sie die folgenden Schritte aus, um ein Volume bereitzustellen.

#### So stellen Sie Volumes auf dem Host bereit

1. Öffnen Sie das Fenster **Eigenschaften des iSCSI-Initiators** auf dem Windows Server-Host. Klicken Sie auf **Server-Manager > Dashboard > Tools > iSCSI-Initiator**.
2. Klicken Sie im Dialogfeld **Eigenschaften des iSCSI-Initiators** auf die Registerkarte "Erkennung", und klicken Sie dann auf **Zielportal ermitteln**.
3. Gehen Sie im Dialogfeld **Zielportal ermitteln** folgendermaßen vor:
	
	- Geben Sie die IP-Adresse der ersten aktivierten Netzwerkschnittstelle auf Ihrem virtuellen StorSimple-Array ein. Standardmäßig wäre dies die **Ethernet**-Adresse. 
	- Klicken Sie auf **OK**, um zum Dialogfeld **Eigenschaften des iSCSI-Initiators** zurückzukehren.

	>[AZURE.IMPORTANT] **Wenn Sie ein privates Netzwerk für iSCSI-Verbindungen verwenden, geben Sie die IP-Adresse des DATA-Ports ein, der mit dem privaten Netzwerk verbunden ist.**

4. Wiederholen Sie die Schritte 2 bis 3 für eine zweite Netzwerkschnittstelle (z. B. Ethernet 2) auf Ihrem Array.

5. Wählen Sie die Registerkarte **Ziele** im Dialogfeld **Eigenschaften des iSCSI-Initiators** aus. Für das virtuelle Array sollten Sie jede Volumeoberfläche als Ziel unter **Erkannte Ziele** sehen. In diesem Fall würden drei Ziele (entspricht drei Volumes) erkannt werden.

	![mpio1](./media/storsimple-ova-configure-mpio-windows-server/mpio1.png)

6. Klicken Sie auf **Verbinden**, um eine iSCSI-Sitzung mit Ihrem StorSimple-Array einzurichten. Ein Dialogfeld **Mit Ziel verbinden** wird angezeigt. Aktivieren Sie das Kontrollkästchen **Multipfad aktivieren**. Klicken Sie auf **Erweitert**.

	![mpio2](./media/storsimple-ova-configure-mpio-windows-server/mpio2.png)

8. Gehen Sie im Dialogfeld **Erweiterte Eigenschaften** folgendermaßen vor:
	- 	 Wählen Sie **Microsoft iSCSI-Initiator** in der Dropdownliste **Lokaler Adapter** aus.
	- 	 Wählen Sie die IP-Adresse des Hosts in der Dropdownliste **Initiator-IP** aus.
	- 	 Wählen Sie die IP-Adresse der Arrayschnittstelle in der Dropdownliste **Zielportal-IP** aus.
	- 	 Klicken Sie auf **OK**, um zum Dialogfeld **Eigenschaften des iSCSI-Initiators** zurückzukehren.

	![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)

9. Klicken Sie auf **Eigenschaften**.

	![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)
10. Klicken Sie im Dialogfeld **Eigenschaften** auf **Sitzung hinzufügen**.

	![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)

10. Aktivieren Sie im Dialogfeld **Mit Ziel verbinden** das Kontrollkästchen **Multipfad aktivieren**. Klicken Sie auf **Erweitert**.
11. Gehen Sie im Dialogfeld **Erweiterte Einstellungen** folgendermaßen vor:										
	-  Wählen Sie "Microsoft iSCSI-Initiator" in der Dropdownliste **Lokaler Adapter** aus.
	-  Wählen Sie die IP-Adresse, die dem Host entspricht, in der Dropdownliste **Initiator-IP** aus. In diesem Fall werden zwei Netzwerkschnittstellen auf dem Array mit einer einzelnen Netzwerkschnittstelle auf dem Host verbunden. Aus diesem Grund handelt es sich bei dieser Schnittstelle um die gleiche Schnittstelle, die für die erste Sitzung bereitgestellt wurde.
	-  Wählen Sie die IP-Adresse für die zweite Datenschnittstelle, die auf dem Array aktiviert ist, in der Dropdownliste **Zielportal-IP** aus.
	-  Klicken Sie auf **OK**, um zum Dialogfeld "Eigenschaften des iSCSI-Initiators" zurückzukehren. Sie haben dem Ziel eine zweite Sitzung hinzugefügt.

		![mpio11](./media/storsimple-ova-configure-mpio-windows-server/mpio11.png)

	- Nachdem Sie die gewünschten Sitzungen (Pfade) hinzugefügt haben, wählen Sie das Ziel im Dialogfeld **Eigenschaften des iSCSI-Initiators** aus, und klicken dann auf **Eigenschaften**. Beachten Sie auf der Registerkarte "Sitzungen" des Dialogfelds **Eigenschaften** die vier Sitzungsbezeichner, die den möglichen Pfadpermutationen entsprechen. Wenn Sie eine Sitzung abbrechen möchten, aktivieren Sie das Kontrollkästchen neben einem Sitzungsbezeichner, und klicken Sie dann auf **Trennen**.
 
	- Wenn Sie Geräte in den Sitzungen anzeigen möchten, wählen Sie die Registerkarte **Geräte** aus. Klicken Sie zum Konfigurieren der MPIO-Richtlinie für ein ausgewähltes Gerät auf **MPIO**. Das Dialogfeld
	-  **Details** wird angezeigt. Auf der Registerkarte **MPIO** können Sie die entsprechenden Einstellungen für die **Lastenausgleichsrichtlinie** auswählen. Sie können auch den Pfadtyp **Aktiv** oder **Standby** anzeigen.

10. Wiederholen Sie die Schritte 8 bis 11, um dem Ziel weitere Sitzungen (Pfade) hinzuzufügen. Mit zwei Schnittstellen auf dem Host und zwei Schnittstellen auf dem virtuellen Array können Sie insgesamt vier Sitzungen für jedes Ziel hinzufügen.

	![mpio14](./media/storsimple-ova-configure-mpio-windows-server/mpio14.png)

11. Sie müssen diese Schritte für jedes Volume (Oberflächen als Ziel) wiederholen.

	![mpio15](./media/storsimple-ova-configure-mpio-windows-server/mpio15.png)

12. Öffnen Sie die **Computerverwaltung**, indem Sie zu **Server-Manager > Dashboard > Computerverwaltung** navigieren. Klicken Sie im linken Bereich auf **Speicher > Datenträgerverwaltung**. Die auf dem virtuellen StorSimple-Array erstellten und für diesen Host sichtbaren Volumes werden unter **Datenträgerverwaltung** als neue Datenträger angezeigt.

13. Initialisieren Sie den Datenträger, und erstellen Sie dann ein neues Volume. Wählen Sie während des Formatierungsvorgangs eine Zuordnungseinheitengröße (AUS, Allocation Unit Size) von 64 KB. Wiederholen Sie den Vorgang für alle verfügbaren Volumes.

	![Datenträgerverwaltung](./media/storsimple-ova-configure-mpio-windows-server/mpio20.png)

14. Klicken Sie unter **Datenträgerverwaltung** mit der rechten Maustaste auf den **Datenträger**, und wählen Sie dann **Eigenschaften** aus.

15. Klicken Sie im Dialogfeld **Eigenschaften von Multipfad-Datenträgergerät** auf die Registerkarte **MPIO**.

	![Datenträgereigenschaften](./media/storsimple-ova-configure-mpio-windows-server/mpio21.png)

16. Klicken Sie im Abschnitt **DSM-Name** auf **Details**, und vergewissern Sie sich, dass die Parameter auf die Standardparameter festgelegt sind. Die Standardparameter lauten:

	- Pfadüberprüfungszeitraum = 30
	- Anzahl der Wiederholungsversuche = 3
	- Zeitraum für das Entfernen von PDO = 20
	- Wiederholungsintervall = 1
	- Pfadüberprüfung aktiviert = nicht aktiviert.

	>[AZURE.NOTE] **Ändern Sie die Standardparameter nicht.**


## Nächste Schritte

Weitere Informationen finden Sie unter [Verwalten des StorSimple Virtual Array mithilfe des StorSimple Manager-Diensts](storsimple-ova-manager-service-administration.md).
 

<!---HONumber=AcomDC_0323_2016-->