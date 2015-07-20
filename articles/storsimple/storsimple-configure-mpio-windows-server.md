<properties 
   pageTitle="Konfigurieren von MPIO für Ihr StorSimple-Gerät"
   description="Konfigurieren von MPIO für Ihr StorSimple-Gerät, das mit einem Host unter Windows Server 2012 R2 verbunden ist"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="adinah"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/07/2015"
   ms.author="alkohli" />

# Konfigurieren von MPIO für Ihr StorSimple-Gerät

Die Unterstützung für Multipfad-E/A (Multipath I/O, MPIO) in Windows Server soll Ihnen beim Erstellen hoch verfügbarer, fehlertoleranter SAN-Konfigurationen helfen. MPIO verwendet redundante physische Pfadkomponenten (Adapter, Kabel und Switches), um logische Pfade zwischen dem Server und dem Speichergerät zu erstellen. Wenn bei einer Komponente ein Fehler auftritt, durch den ein logischer Pfad fehlschlägt, verwendet die Multipfad-Logik einen anderen Pfad für E/A, sodass Anwendungen weiterhin auf ihre Daten zugreifen können. Darüber hinaus kann MPIO abhängig von Ihrer Konfiguration auch die Leistung durch ein Umverteilen der Lasten auf alle Pfade verbessern. Weitere Informationen finden Sie unter [Multipfad-E/A (Übersicht)](https://technet.microsoft.com/library/cc725907.aspx "MPIO – Übersicht und Features").

Für eine hohe Verfügbarkeit Ihrer StorSimple-Lösung sollte MPIO auf dem StorSimple-Gerät konfiguriert werden. Wenn MPIO auf den Hostservern unter Windows Server 2012 R2 installiert ist, können die Server den Ausfall einer Verknüpfung, des Netzwerks oder einer Schnittstelle tolerieren.

MPIO ist ein optionales Feature und wird unter Windows Server nicht standardmäßig installiert. Diese Funktion sollte als Feature über den Server-Manager installiert werden. In diesem Thema werden die Schritte zum Installieren und Verwenden von MPIO auf einem Host unter Windows Server 2012 R2 beschrieben, der mit einem physischen StorSimple-Gerät verbunden ist.

>[AZURE.NOTE]**MPIO wird auf virtuellen StorSimple-Geräten nicht unterstützt.**

Führen Sie die folgenden Schritte aus, um MPIO auf Ihrem StorSimple-Gerät zu konfigurieren:

- Schritt 1: Installieren von MPIO auf dem Windows Server-Host

- Schritt 2: Konfigurieren von MPIO für StorSimple-Volumes

- Schritt 3: Bereitstellen von StorSimple-Volumes auf dem Host

- Schritt 4: Konfigurieren von MPIO für hohe Verfügbarkeit und Lastenausgleich

Jeder der oben genannten Schritte wird in den folgenden Abschnitten erläutert.
## Schritt 1: Installieren von MPIO auf dem Windows Server-Host

Gehen Sie folgendermaßen vor, um dieses Feature auf Ihrem Windows Server-Host zu installieren.

### So installieren Sie MPIO auf dem Host

1. Öffnen Sie den Server-Manager auf Ihrem Windows Server-Host. Der Server-Manager wird standardmäßig gestartet, wenn sich ein Mitglied der Gruppe "Administratoren" an einem Computer unter Windows Server 2012 R2 oder Windows Server 2012 anmeldet. Wenn der Server-Manager nicht bereits geöffnet ist, klicken Sie auf **Start > Server-Manager**. ![Server-Manager](./media/storsimple-configure-mpio-windows-server/IC740997.png)
2. Klicken Sie auf **Server-Manager > Dashboard > Rollen und Features hinzufügen**. Dadurch wird der Assistent **Rollen und Features hinzufügen** gestartet. ![Hinzufügen von Rollen und Features – Assistent 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. Gehen Sie im Assistenten **Rollen und Features hinzufügen** folgendermaßen vor:

	- Klicken Sie auf der Seite **Vorbereitungen** auf **Weiter**.
	- Akzeptieren Sie auf der Seite **Auswählen des Installationstyps** die Standardeinstellung **Rollenbasierte oder featurebasierte Installation**. Klicken Sie auf **Weiter**.![Hinzufügen von Rollen und Features – Assistent 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
	- Wählen Sie auf der Seite **Zielserver auswählen** die Option **Einen Server aus Serverpool auswählen** aus. Ihr Hostserver sollte automatisch ermittelt werden. Klicken Sie auf **Weiter**.
	- Klicken Sie auf der Seite **Serverrollen auswählen** auf **Weiter**.
	- Wählen Sie auf der Seite **Auswählen von Features** die Option **Multipfad-E/A** aus, und klicken Sie dann auf **Weiter**.![Hinzufügen von Rollen und Features – Assistent 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
	- Bestätigen Sie auf der Seite **Installationsauswahl bestätigen** Ihre Auswahl, und wählen Sie dann wie unten gezeigt **Zielserver bei Bedarf automatisch neu starten aus**. Klicken Sie auf **Installieren**.![Hinzufügen von Rollen und Features – Assistent 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
	- Nachdem die Installation abgeschlossen wurde, erhalten Sie eine Benachrichtigung. Klicken Sie auf **Schließen**, um den Assistenten zu schließen.![Hinzufügen von Rollen und Features – Assistent 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## Schritt 2: Konfigurieren von MPIO für StorSimple-Volumes

MPIO muss konfiguriert werden, um StorSimple-Volumes zu erkennen. Führen Sie zum Konfigurieren von MPIO für die Erkennung von StorSimple-Geräten die folgenden Schritte aus.

### So konfigurieren Sie MPIO für StorSimple-Volumes

1. Öffnen Sie die **MPIO-Konfiguration**. Klicken Sie auf **Server-Manager > Dashboard > Tools > MPIO**.

2. Wählen Sie im Dialogfeld **MPIO-Eigenschaften** die Registerkarte **Multipfade suchen** aus.

3. Wählen Sie **Unterstützung für iSCSI-Geräte hinzufügen** aus, und klicken Sie dann auf **Hinzufügen**. ![MPIO-Eigenschaften – Multipfade ermitteln](./media/storsimple-configure-mpio-windows-server/IC741003.png)

4. Starten Sie den Server neu, wenn Sie dazu aufgefordert werden.
5. Klicken Sie im Dialogfeld **MPIO-Eigenschaften** auf die Registerkarte **MPIO-Geräte**. Klicken Sie auf **Hinzufügen**. </br>![MPIO-Eigenschaften – MPIO-Geräte](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. Geben Sie im Dialogfeld **MPIO-Unterstützung hinzufügen** unter **Gerätehardware-ID** die Seriennummer des Geräts ein. Sie können die Seriennummer des Geräts abrufen, indem Sie auf den StorSimple-Manager-Dienst zugreifen und dann zu **Geräte > Dashboard** navigieren. Die Seriennummer des Geräts wird im rechten Bereich **Auf einen Blick** des Gerätedashboards angezeigt. </br>![Hinzufügen von MPIO-Unterstützung](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Starten Sie den Server neu, wenn Sie dazu aufgefordert werden.

## Schritt 3: Bereitstellen von StorSimple-Volumes auf dem Host

Nachdem MPIO unter Windows Server konfiguriert wurde, können auf dem StorSimple-Gerät erstellte Volumes bereitgestellt werden und anschließend MPIO für Redundanz nutzen. Führen Sie die folgenden Schritte aus, um ein Volume bereitzustellen.

### So stellen Sie Volumes auf dem Host bereit

1. Öffnen Sie das Fenster **Eigenschaften des iSCSI-Initiators** auf dem Windows Server-Host. Klicken Sie auf **Server-Manager > Dashboard > Tools > iSCSI-Initiator**.
2. Klicken Sie im Dialogfeld **Eigenschaften des iSCSI-Initiators** auf die Registerkarte "Erkennung", und klicken Sie dann auf **Zielportal ermitteln**.
3. Gehen Sie im Dialogfeld **Zielportal ermitteln** folgendermaßen vor:
	
	- Geben Sie die IP-Adresse des DATA-Ports Ihres StorSimple-Geräts ein (Beispiel: DATA 0).
	- Klicken Sie auf **OK**, um zum Dialogfeld **Eigenschaften des iSCSI-Initiators** zurückzukehren.
	>[AZURE.IMPORTANT]**Wenn Sie ein privates Netzwerk für iSCSI-Verbindungen verwenden, geben Sie die IP-Adresse des DATA-Ports ein, der mit dem privaten Netzwerk verbunden ist.**

4. Wiederholen Sie die Schritte 2 bis 3 für eine zweite Netzwerkschnittstelle (z. B. DATA 1) auf Ihrem Gerät. Denken Sie daran, dass diese Schnittstellen für iSCSI aktiviert sein sollten. Weitere Informationen hierzu finden Sie unter [Konfigurieren von Netzwerkschnittstellen](https://msdn.microsoft.com/library/02f1412f-e196-4a88-8eda-2113247ea47c#sec05).
5. Wählen Sie die Registerkarte **Ziele** im Dialogfeld **Eigenschaften des iSCSI-Initiators** aus. Der Ziel-IQN des StorSimple-Geräts sollte unter **Ermittelte Ziele** angezeigt werden. ![Eigenschaften des iSCSI-Initiators – Registerkarte "Ziele"](./media/storsimple-configure-mpio-windows-server/IC741007.png)
6. Klicken Sie auf **Verbinden**, um eine iSCSI-Sitzung mit Ihrem StorSimple-Gerät einzurichten. Ein Dialogfeld **Mit Ziel verbinden** wird angezeigt.

7. Aktivieren Sie im Dialogfeld **Mit Ziel verbinden** das Kontrollkästchen **Multipfad aktivieren**. Klicken Sie auf **Erweitert**.

8. Gehen Sie im Dialogfeld **Erweiterte Eigenschaften** folgendermaßen vor:
	- 	 Wählen Sie **Microsoft iSCSI-Initiator** in der Dropdownliste **Lokaler Adapter** aus.
	- 	 Wählen Sie die IP-Adresse des Hosts in der Dropdownliste **Initiator-IP** aus.
	- 	 Wählen Sie die IP-Adresse der Geräteschnittstelle in der Dropdownliste **Zielportal-IP** aus.
	- 	 Klicken Sie auf **OK**, um zum Dialogfeld **Eigenschaften des iSCSI-Initiators** zurückzukehren.

9. Klicken Sie auf **Eigenschaften**. Klicken Sie im Dialogfeld **Eigenschaften** auf **Sitzung hinzufügen**.
10. Aktivieren Sie im Dialogfeld **Mit Ziel verbinden** das Kontrollkästchen **Multipfad aktivieren**. Klicken Sie auf **Erweitert**.
11. Gehen Sie im Dialogfeld **Erweiterte Einstellungen** folgendermaßen vor:										
	-  Wählen Sie "Microsoft iSCSI-Initiator" in der Dropdownliste **Lokaler Adapter** aus.
	-  Wählen Sie die IP-Adresse, die dem Host entspricht, in der Dropdownliste **Initiator-IP** aus. In diesem Fall werden zwei Netzwerkschnittstellen auf dem Gerät mit einer einzelnen Netzwerkschnittstelle auf dem Host verbunden. Aus diesem Grund handelt es sich bei dieser Schnittstelle um die gleiche Schnittstelle, die für die erste Sitzung bereitgestellt wurde.
	-  Wählen Sie die IP-Adresse für die zweite Datenschnittstelle, die auf dem Gerät aktiviert ist, in der Dropdownliste **Zielportal-IP** aus.
	-  Klicken Sie auf **OK**, um zum Dialogfeld "Eigenschaften des iSCSI-Initiators" zurückzukehren. Sie haben dem Ziel eine zweite Sitzung hinzugefügt.

12. Öffnen Sie die **Computerverwaltung**, indem Sie zu **Server-Manager > Dashboard > Computerverwaltung** navigieren. Klicken Sie im linken Bereich auf **Speicher > Datenträgerverwaltung**. Die auf dem StorSimple-Gerät erstellten und für diesen Host sichtbaren Volumes werden unter **Datenträgerverwaltung** als neue Datenträger angezeigt.

13. Initialisieren Sie den Datenträger, und erstellen Sie dann ein neues Volume. Wählen Sie während des Formatierungsvorgangs eine Blockgröße von 64 KB aus. ![Datenträgerverwaltung](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Klicken Sie unter **Datenträgerverwaltung** mit der rechten Maustaste auf den **Datenträger**, und wählen Sie dann **Eigenschaften** aus.
15. Klicken Sie im Dialogfeld **Eigenschaften von Multipfad-Datenträgergerät** der StorSimple-Modellnummer auf die Registerkarte **MPIO**. ![StorSimple 8100 – Eigenschaften von Multipfad-Datenträgergerät](./media/storsimple-configure-mpio-windows-server/IC741009.png)

16. Klicken Sie im Abschnitt **DSM-Name** auf **Details**, und vergewissern Sie sich dann, dass die Parameter auf die Standardparameter festgelegt sind. Die folgenden Standardparameter werden verwendet:

	- Pfadüberprüfungszeitraum = 30
	- Anzahl der Wiederholungsversuche = 3
	- Zeitraum für das Entfernen von PDO = 20
	- Wiederholungsintervall = 1
	- Pfadüberprüfung aktiviert = nicht aktiviert.


>[AZURE.NOTE]**Ändern Sie die Standardparameter nicht.**

## Schritt 4: Konfigurieren von MPIO für hohe Verfügbarkeit und Lastenausgleich

Für auf Multipfad basierende hohe Verfügbarkeit und Lastenausgleich müssen mehrere Sitzungen manuell hinzugefügt werden, um die verschiedenen verfügbaren Pfade zu deklarieren. Wenn beispielsweise der Host und das Gerät jeweils zwei Schnittstellen haben, die mit dem SAN verbunden sind, benötigen Sie vier Sitzungen, die mit den richtigen Pfadpermutationen konfiguriert sein müssen (wenn sich jede DATA- und Host-Schnittstelle in einem anderen IP-Subnetz befindet und nicht routingfähig ist, sind nur zwei Sitzungen erforderlich).

>[AZURE.IMPORTANT]**Es wird nicht empfohlen, 1-GbE- und 10-GbE-Netzwerkschnittstellen zu mischen. Bei Verwendung von zwei Netzwerkschnittstellen müssen beide denselben Typ haben.**

Das folgende Verfahren beschreibt, wie Sitzungen hinzugefügt werden, wenn ein StorSimple-Gerät mit zwei Netzwerkschnittstellen mit einem Host mit zwei Netzwerkschnittstellen verbunden ist.

### So konfigurieren Sie MPIO für hohe Verfügbarkeit und Lastenausgleich

1. Führen Sie eine Ermittlung des Ziels aus: Klicken Sie im Dialogfeld **Eigenschaften des iSCSI-Initiators** auf der Registerkarte **Erkennung** auf **Portal ermitteln**.
2. Geben Sie im Dialogfeld **Mit Ziel verbinden** die IP-Adresse einer Netzwerkschnittstelle des Geräts ein.
3. Klicken Sie auf **OK**, um zum Dialogfeld **Eigenschaften des iSCSI-Initiators** zurückzukehren.

4. Wählen Sie im Dialogfeld **Eigenschaften des iSCSI-Initiators** die Registerkarte **Ziele** aus, markieren Sie das erkannte Ziel, und klicken Sie dann auf **Verbinden**. Das Dialogfeld **Mit Ziel verbinden** wird angezeigt.

5. Gehen Sie im Dialogfeld **Mit Ziel verbinden** folgendermaßen vor:
	
	- Lassen Sie die Standardeinstellung für das ausgewählte Ziel für **Diese Verbindung der Liste der bevorzugten Ziele hinzufügen** unverändert. Dies veranlasst das Gerät, bei jedem Neustart dieses Computers automatisch einen Neustart der Verbindung zu versuchen.
	- Aktivieren Sie das Kontrollkästchen **Multipfad aktivieren**.
	- Klicken Sie auf **Erweitert**.

6. Gehen Sie im Dialogfeld **Erweiterte Einstellungen** folgendermaßen vor:
	- Wählen Sie **Microsoft iSCSI-Initiator** in der Dropdownliste **Lokaler Adapter** aus.
	- Wählen Sie die IP-Adresse des Hosts in der Dropdownliste **Initiator-IP** aus.
	- Wählen Sie die IP-Adresse für die Datenschnittstelle, die auf dem Gerät aktiviert ist, in der Dropdownliste **Zielportal-IP** aus.
	- Klicken Sie auf **OK**, um zum Dialogfeld "Eigenschaften des iSCSI-Initiators" zurückzukehren.

7. Klicken Sie auf **Eigenschaften** und dann im Dialogfeld **Eigenschaften** auf **Sitzung hinzufügen**.

8. Aktivieren Sie im Dialogfeld **Mit Ziel verbinden** das Kontrollkästchen **Multipfad aktivieren**, und klicken Sie dann auf **Erweitert**.

9. Gehen Sie im Dialogfeld **Erweiterte Einstellungen** folgendermaßen vor:
	1. Wählen Sie **Microsoft iSCSI-Initiator** in der Dropdownliste **Lokaler Adapter** aus.
	2. Wählen Sie die IP-Adresse, die der zweiten Datenschnittstelle auf dem Host entspricht, in der Dropdownliste **Initiator-IP** aus.
	3. Wählen Sie die IP-Adresse für die zweite Datenschnittstelle, die auf dem Gerät aktiviert ist, in der Dropdownliste **Zielportal-IP** aus.
	4. Klicken Sie auf **OK**, um zum Dialogfeld **Eigenschaften des iSCSI-Initiators** zurückzukehren. Sie haben dem Ziel damit eine zweite Sitzung hinzugefügt.

10. Wiederholen Sie die Schritte 8 bis 10, um dem Ziel weitere Sitzungen (Pfade) hinzuzufügen. Mit zwei Schnittstellen auf dem Host und zwei Schnittstellen auf dem Gerät können Sie insgesamt vier Sitzungen hinzufügen.

11. Nachdem Sie die gewünschten Sitzungen (Pfade) hinzugefügt haben, wählen Sie das Ziel im Dialogfeld **Eigenschaften des iSCSI-Initiators** aus, und klicken dann auf **Eigenschaften**. Beachten Sie auf der Registerkarte "Sitzungen" des Dialogfelds **Eigenschaften** die vier Sitzungsbezeichner, die den möglichen Pfadpermutationen entsprechen. Wenn Sie eine Sitzung abbrechen möchten, aktivieren Sie das Kontrollkästchen neben einem Sitzungsbezeichner, und klicken Sie dann auf **Trennen**.

12. Wenn Sie Geräte in den Sitzungen anzeigen möchten, wählen Sie die Registerkarte **Geräte** aus. Klicken Sie zum Konfigurieren der MPIO-Richtlinie für ein ausgewähltes Gerät auf **MPIO**. Das Dialogfeld **Gerätedetails** wird angezeigt. Auf der Registerkarte **MPIO** können Sie die entsprechenden Einstellungen für die **Lastenausgleichsrichtlinie** auswählen. Sie können auch den Pfadtyp **Aktiv** oder **Standby** anzeigen.

 

<!---HONumber=July15_HO2-->