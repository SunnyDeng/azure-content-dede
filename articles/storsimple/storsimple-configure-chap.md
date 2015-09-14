<properties 
   pageTitle="Konfigurieren von CHAP für Ihr StorSimple-Gerät | Microsoft Azure"
	description="Beschreibt die Vorgehensweise zum Konfigurieren des Challenge Handshake Authentication-Protokolls (CHAP) auf einem StorSimple-Gerät."
	services="storsimple"
	documentationCenter=""
	authors="alkohli"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="storsimple"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="TBD"
	ms.date="09/01/2015"
	ms.author="alkohli"/>

# Konfigurieren von CHAP für Ihr StorSimple-Gerät

In diesem Tutorial erfahren Sie, wie Sie CHAP für Ihr StorSimple-Gerät konfigurieren. CHAP steht für Challenge Handshake Authentication-Protokoll. Dabei handelt es sich um ein Authentifizierungsschema, das von Servern zum Überprüfen der Identität von Remoteclients verwendet wird. Die Überprüfung basiert auf einem freigegebenen Kennwort oder geheimen Schlüssel.

CHAP kann unidirektional oder bidirektional verwendet werden. Bei unidirektionalem CHAP authentifiziert das Ziel einen Initiator. Bei bidirektionalem (oder umgekehrtem) CHAP muss dagegen das Ziel den Initiator und der Initiator wiederum das Ziel authentifizieren. Die Initiatorauthentifizierung kann ohne Zielauthentifizierung implementiert werden. Die Zielauthentifizierung kann dagegen nur implementiert werden, wenn auch die Initiatorauthentifizierung implementiert ist.

Es empfiehlt sich, CHAP zu verwenden, um die iSCSI-Sicherheit zu verbessern.

>[AZURE.NOTE]Bedenken Sie, dass IPsec auf StorSimple-Geräte derzeit nicht unterstützt wird.

Die CHAP-Einstellungen auf dem StorSimple-Gerät können wie folgt konfiguriert werden:

- Unidirektionale Authentifizierung

- Bidirektionale oder wechselseitige (umgekehrte) Authentifizierung

In beiden Fällen müssen das Verwaltungsportal des Geräts und die iSCSI-Initiator-Serversoftware konfiguriert werden. Die Schritte für diese Konfiguration werden im folgenden Tutorial ausführlich beschrieben.

## Unidirektionale Authentifizierung

Bei der unidirektionalen Authentifizierung authentifiziert das Ziel den Initiator. Für diese Authentifizierung müssen auf dem StorSimple-Gerät die CHAP-Initiator-Einstellungen und auf dem Host die iSCSI-Initiator-Software konfiguriert werden. Im Anschluss werden die Verfahren für das StorSimple-Gerät und den Windows-Host beschrieben.

#### So konfigurieren Sie das Gerät für die unidirektionale Authentifizierung

1. Klicken Sie im Verwaltungsportal auf der Seite **Geräte** auf die Registerkarte **Konfigurieren**.

    ![CHAP-Initiator](./media/storsimple-configure-chap/IC740943.png)

2. Navigieren Sie auf dieser Seite zum Abschnitt **CHAP-Initiator**, und führen Sie die folgenden Schritte aus:
													
	1. Geben Sie einen Benutzernamen für den CHAP-Initiator an.

	2. Geben Sie ein Kennwort für den CHAP-Initiator an.

    > [AZURE.IMPORTANT]Der CHAP-Benutzername darf maximal 233 Zeichen enthalten. Das CHAP-Kennwort muss zwischen 12 und 16 Zeichen umfassen. Längere Benutzernamen oder Kennwörtern haben einen Authentifizierungsfehler auf dem Windows-Host zur Folge.

#### So konfigurieren Sie die unidirektionale Authentifizierung auf dem Windows-Hostserver

1. Starten Sie auf dem Windows-Hostserver den iSCSI-Initiator.

2. Führen Sie im Fenster **Eigenschaften von iSCSI-Initiator** die folgenden Schritte aus:
													
	1. Klicken Sie auf die Registerkarte **Ermittlung**.

		![Eigenschaften von iSCSI-Initiator](./media/storsimple-configure-chap/IC740944.png)

	2. Klicken Sie auf **Portal ermitteln**.

3. Gehen Sie im Dialogfeld **Zielportal ermitteln** wie folgt vor:
													
	1. Geben Sie die IP-Adresse Ihres Geräts an.

	3. Klicken Sie auf **Erweitert**.

		![Zielportal erkennen](./media/storsimple-configure-chap/IC740945.png)

4. Gehen Sie im Dialogfeld **Erweiterte Einstellungen** folgendermaßen vor:
													
	1. Aktivieren Sie das Kontrollkästchen **CHAP-Anmeldung aktivieren**.

	2. Geben Sie im Feld **Name** den Benutzernamen an, den Sie im Verwaltungsportal für den CHAP-Initiator angegeben haben.

	3. Geben Sie im Feld **Zielschlüssel** das Kennwort an, das Sie im Verwaltungsportal für den CHAP-Initiator angegeben haben.

	4. Klicken Sie auf **OK**.

		![Erweiterte Einstellungen (allgemein)](./media/storsimple-configure-chap/IC740946.png)

5. Im Fenster **Eigenschaften von iSCSI-Initiator** wird auf der Registerkarte **Ziele** der Gerätestatus **Verbunden** angezeigt.

    > [AZURE.IMPORTANT]Wenn Sie den iSCSI-Namen ändern, wird für neue iSCSI-Sitzungen der neue Name verwendet. Neue Einstellungen werden erst dann für vorhandene Sitzungen verwendet, wenn Sie sich ab- und wieder anmelden.

Weitere Informationen zum Konfigurieren von CHAP auf dem Windows-Hostserver finden Sie unter [Zusätzliche Überlegungen](#additional-considerations).


## Bidirektionale oder wechselseitige Authentifizierung

Bei der bidirektionalen Authentifizierung muss das Ziel den Initiator und der Initiator wiederum das Ziel authentifizieren. Hierzu muss der Benutzer auf dem Gerät die CHAP-Initiator-Einstellungen und die umgekehrten CHAP-Einstellungen und auf dem Host die iSCSI-Initiator-Software konfigurieren. In den folgenden Verfahren werden die Schritte beschrieben, die zum Konfigurieren der wechselseitigen Authentifizierung auf dem Gerät und auf dem Windows-Host ausgeführt werden müssen.

#### So konfigurieren Sie das Gerät für die wechselseitige Authentifizierung

1. Klicken Sie im Verwaltungsportal auf der Seite **Geräte** auf die Registerkarte **Konfigurieren**.

    ![CHAP-Ziel](./media/storsimple-configure-chap/IC740948.png)

2. Navigieren Sie auf dieser Seite zum Abschnitt **CHAP-Ziel**, und führen Sie die folgenden Schritte aus:
													
	1. Geben Sie für Ihr Gerät einen Benutzernamen für umgekehrtes CHAP an.

	2. Geben Sie für Ihr Gerät ein Kennwort für umgekehrtes CHAP an.

	3. Bestätigen Sie das Kennwort.

3. Führen Sie im Abschnitt **CHAP-Initiator** folgende Schritte aus:
												
	1. Geben Sie einen Benutzernamen für Ihr Gerät an.

	1. Geben Sie ein Kennwort für Ihr Gerät an.

	3. Bestätigen Sie das Kennwort.

4. Klicken Sie auf **Speichern**. Eine Bestätigungsmeldung wird angezeigt. Klicken Sie zum Speichern der Änderungen auf **OK**.

#### So konfigurieren Sie die bidirektionale Authentifizierung auf dem Windows-Hostserver

1. Starten Sie auf dem Windows-Hostserver den iSCSI-Initiator.

2. Klicken Sie im Fenster **Eigenschaften von iSCSI-Initiator** auf die Registerkarte **Konfiguration**.

3. Klicken Sie auf **CHAP**.

4. Gehen Sie im Dialogfeld **iSCSI-Initiator: Geheimer Schlüssel für wechselseitige CHAP-Authentifizierung** wie folgt vor:
													
	1. Geben Sie das Kennwort für umgekehrtes CHAP ein, das Sie im Verwaltungsportal konfiguriert haben.

	2. Klicken Sie auf **OK**.

		![iSCSI-Initiator: Geheimer Schlüssel für wechselseitige CHAP-Authentifizierung](./media/storsimple-configure-chap/IC740949.png)

5. Klicken Sie auf die Registerkarte **Ziele**.

6. Klicken Sie auf die Schaltfläche **Verbinden**.

7. Klicken Sie im Dialogfeld **Mit Ziel verbinden** auf **Erweitert**.

8. Gehen Sie im Dialogfeld **Erweiterte Eigenschaften** wie folgt vor:
													
	1. Aktivieren Sie das Kontrollkästchen **CHAP-Anmeldung aktivieren**.

	2. Geben Sie im Feld **Name** den Benutzernamen an, den Sie im Verwaltungsportal für den CHAP-Initiator angegeben haben.

	3. Geben Sie im Feld **Zielschlüssel** das Kennwort an, das Sie im Verwaltungsportal für den CHAP-Initiator angegeben haben.

	4. Aktivieren Sie das Kontrollkästchen **Wechselseitige Authentifizierung ausführen**.

		![Erweiterte Einstellungen (wechselseitige Authentifizierung)](./media/storsimple-configure-chap/IC740950.png)

	5. Klicken Sie auf **OK**, um die CHAP-Konfiguration abzuschließen.

Weitere Informationen zum Konfigurieren von CHAP auf dem Windows-Hostserver finden Sie unter [Zusätzliche Überlegungen](#additional-considerations).

## Zusätzliche Überlegungen

Das Schnellverbindungsfeature unterstützt keine Verbindungen mit aktiviertem CHAP. Verwenden Sie bei aktiviertem CHAP die Schaltfläche **Verbinden** auf der Registerkarte **Ziele**, um die Verbindung mit einem Ziel herzustellen.

![Mit Ziel verbinden](./media/storsimple-configure-chap/IC740947.png)

Aktivieren Sie im angezeigten Dialogfeld **Mit Ziel verbinden** das Kontrollkästchen **Diese Verbindung der Liste der bevorzugten Ziele hinzufügen**. Dadurch wird sichergestellt, dass bei jedem Neustart des Computer versucht wird, die Verbindung mit den bevorzugten iSCSI-Zielen wiederherzustellen.

## Fehler bei der Konfiguration

Bei nicht ordnungsgemäßer CHAP-Konfiguration wird in der Regel ein Authentifizierungsfehler angezeigt.

## Überprüfen der CHAP-Konfiguration

Mit den folgenden Schritten können Sie überprüfen, ob CHAP verwendet wird.

#### So überprüfen Sie die CHAP-Konfiguration

1. Klicken Sie auf **Bevorzugte Ziele**.

2. Wählen Sie das Ziel aus, für das Sie die Authentifizierung aktiviert haben.

3. Klicken Sie auf **Details**.

    ![Eigenschaften von iSCSI-Initiator – bevorzugte Ziele](./media/storsimple-configure-chap/IC740951.png)

4. Sehen Sie sich im Dialogfeld **Details zum bevorzugten Ziel** das Feld **Authentifizierung** an. War die Konfiguration erfolgreich, enthält das Feld den Wert **CHAP**.

    ![Details zum bevorzugten Ziel](./media/storsimple-configure-chap/IC740952.png)

## Nächste Schritte

Weitere Informationen zur [StorSimple-Sicherheit](storsimple-security.md).

<!---HONumber=September15_HO1-->