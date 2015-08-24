<properties 
   pageTitle="Microsoft-Support kontaktieren | Microsoft Azure"
   description="Hier erfahren Sie, wie Sie eine Supportanfrage erstellen und eine Supportsitzung auf Ihrem StorSimple-Gerät initiieren."
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
   ms.date="07/24/2015"
   ms.author="alkohli" />

# Microsoft-Support kontaktieren

Wenn Probleme mit Ihrer Microsoft Azure StorSimple-Lösung auftreten, können Sie eine Serviceanfrage für den technischen Support erstellen. In einer Onlinesitzung mit dem Supporttechniker müssen Sie darüber hinaus eine Supportsitzung auf Ihrem StorSimple-Gerät starten. In diesem Artikel werden die Schritte zum Erstellen einer Supportanfrage sowie zum Starten einer Supportsitzung über die Windows PowerShell-Schnittstelle Ihres StorSimple-Geräts erläutert.

## Erstellen einer Supportanfrage

Führen Sie die folgenden Schritte aus, um eine Supportanfrage zu erstellen.

#### So erstellen Sie eine Supportanfrage

1. Eine Supportanfrage kann über das [Verwaltungsportal](http://manage.windowsazure.com/) erstellt werden. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com/) auf Ihren **Kontonamen** und anschließend auf **Microsoft-Support kontaktieren**.

	![Kontaktieren des Supports von MS über das Verwaltungsportal](./media/storsimple-contact-microsoft-support/IC777286.png)

2. Führen Sie im Dialogfenster **Microsoft-Support kontaktieren** die folgenden Schritte aus:

	1. Wählen Sie in der Dropdownliste das **Abonnement** für Ihren StorSimple Manager-Dienst aus. Geben Sie unter **Supporttyp** die Option **Technisch** an. Sie benötigen einen kostenpflichtigen Supportplan, um technischen Support zu erhalten.

	2. Aktivieren Sie mit dem Häkchen ![Häkchensymbol](./media/storsimple-contact-microsoft-support/IC740895.png) die Option **Ticket erstellen**.

3. Wählen Sie im Fenster **Microsoft-Support** in der Dropdownliste **Produkt** die Option **StorSimple**.

	![Microsoft-Support kontaktieren – Produkt](./media/storsimple-contact-microsoft-support/IC777288.png)

4. Folgen Sie den Anweisungen auf dem Bildschirm, um Ihre Anfrage ordnungsgemäß zu klassifizieren, und geben Sie eine eindeutige Beschreibung Ihres Problems ein.

Nach dem Übermitteln Ihrer Anfrage setzt sich baldmöglichst ein Supporttechniker mit Ihnen in Verbindung, um Ihre Anfrage zu bearbeiten.

## Starten einer Supportsitzung in Windows PowerShell für StorSimple

Um Probleme zu beheben, die auf Ihrem StorSimple-Gerät unter Umständen auftreten, müssen Sie sich mit dem Supportteam von Microsoft in Verbindung setzen. Der Microsoft-Support muss sich möglicherweise über eine Supportsitzung bei Ihrem Gerät anmelden.

Führen Sie zum Starten einer Supportsitzung die folgenden Schritte aus:

#### So starten Sie eine Supportsitzung

1. Greifen Sie auf das Gerät direkt über die serielle Konsole oder von einem Remotecomputer über eine Telnet-Sitzung zu. Führen Sie hierzu die Schritte unter [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console) aus.

2. Drücken Sie in der Sitzung, die geöffnet wird, die **EINGABETASTE**, um eine Eingabeaufforderung zu öffnen.

3. Wählen Sie im Menü der seriellen Konsole Option 1 aus, d. h. die **Anmeldung mit Vollzugriff**.

4. Geben Sie an der Eingabeaufforderung das folgende Kennwort ein:

	`Password1`

5. Geben Sie an der Eingabeaufforderung den folgenden Befehl ein:

	`Enable-HcsSupportAccess`

6. Eine verschlüsselte Zeichenfolge wird angezeigt. Kopieren Sie diese Zeichenfolge in einen Text-Editor wie beispielsweise Notepad.

7. Speichern Sie diese Zeichenfolge, und senden Sie sie in einer E-Mail-Nachricht an den Microsoft-Support.

> [AZURE.IMPORTANT]Sie können den Supportzugriff deaktivieren, indem Sie `Disable-HcsSupportAccess` deaktivieren. Das StorSimple-Gerät versucht acht Stunden nach dem Initiieren der Sitzung ebenfalls, den Supportzugriff zu deaktivieren. Es wird empfohlen, die Anmeldeinformationen für das StorSimple-Gerät nach dem Initiieren einer Supportsitzung zu ändern.

<!---HONumber=August15_HO7-->