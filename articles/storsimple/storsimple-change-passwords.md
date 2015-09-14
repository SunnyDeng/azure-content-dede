<properties 
   pageTitle="Ändern Ihres StorSimple-Kennworts | Microsoft Azure"
	description="Beschreibt, wie Sie den StorSimple Manager-Dienst verwenden, um Ihr StorSimple Snapshot Manager- und Ihr Geräteadministratorkennwort zu ändern."
	services="storsimple"
	documentationCenter="NA"
	authors="SharS"
	manager="carolz"
	editor=""/>

<tags
   ms.service="storsimple"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="TBD"
	ms.date="08/31/2015"
	ms.author="v-sharos@microsoft.com"/>

# Verwenden des StorSimple Manager-Diensts, um StorSimple-Kennwörter zu ändern

## Übersicht 

Die Verwaltungsportalseite **Konfigurieren** enthält alle Geräteparameter, die Sie für ein StorSimple-Gerät neu konfigurieren können, das über einen StorSimple Manager-Dienst verwaltet wird. In diesem Tutorial wird erläutert, wie Sie die Seite **Konfigurieren** verwenden können, um Ihr StorSimple Snapshot Manager- oder Ihr Geräteadministratorkennwort zu ändern.

## Ändern des StorSimple Snapshot Manager-Kennworts

Der StorSimple-Momentaufnahme-Manager befindet sich auf dem Windows-Host und ermöglicht Administratoren die Verwaltung der Sicherungen Ihres StorSimple-Geräts in Form von lokalen und Cloudmomentaufnahmen.

Beim Konfigurieren eines Geräts im StorSimple Snapshot Manager werden Sie aufgefordert, zur Authentifizierung des Speichergeräts die IP-Adresse und das Kennwort des Geräts anzugeben. Dieses Kennwort wird zunächst über die Windows PowerShell-Benutzeroberfläche konfiguriert. Weitere Informationen finden Sie unter [Schritt 3: Konfigurieren und Registrieren des Geräts über Windows PowerShell für StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) in [Bereitstellen lokaler StorSimple-Geräte](storsimple-deployment-walkthrough.md).

Das Kennwort, das zuerst über die Windows PowerShell-Schnittstelle während der Registrierung festgelegt wurde, kann dann über das Verwaltungsportal geändert werden. Führen Sie die folgenden Schritte aus, um das Kennwort für StorSimple Snapshot Manager ändern.

#### So ändern Sie das Kennwort für StorSimple Snapshot Manager

1. Klicken Sie im Portal auf **Geräte** > **Konfigurieren** für Ihr Gerät.

2. Führen Sie einen Bildlauf nach unten bis zum Abschnitt **StorSimple Snapshot Manager** aus. Geben Sie ein Kennwort mit einer Länge von 14 oder 15 Zeichen ein. Stellen Sie sicher, dass das Kennwort eine Kombination aus Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen enthält.

3. Bestätigen Sie das Kennwort.

4. Klicken Sie unten auf der Seite auf **Speichern**.

Das Kennwort für StorSimple Snapshot Manager sollte jetzt aktualisiert sein.
 
## Ändern des Geräteadministratorkennworts

Wenn Sie über die Windows PowerShell-Benutzeroberfläche auf das StorSimple-Gerät zugreifen, müssen Sie ein Geräteadministratorkennwort eingeben. Wenn das erste StorSimple-Gerät mit einem Dienst registriert ist, ist das Standardkennwort für diese Schnittstelle gleich *Kennwort1*. Für die Sicherheit Ihrer Daten müssen Sie dieses Kennwort am Ende des Registrierungsprozesses ändern. Sie können den Registrierungsprozess nicht beenden, ohne dieses Kennwort geändert zu haben. Weitere Informationen finden Sie unter [Schritt 3: Konfigurieren und Registrieren des Geräts über Windows PowerShell für StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) in [Bereitstellen lokaler StorSimple-Geräte](storsimple-deployment-walkthrough.md).

Das Kennwort, das zuerst über die Windows PowerShell-Schnittstelle während der Registrierung festgelegt wurde, kann dann über das Verwaltungsportal geändert werden. Führen Sie die folgenden Schritte aus, um das Geräteadministratorkennwort zu ändern.

#### So ändern Sie das Geräteadministratorkennwort

1. Klicken Sie im Portal auf **Geräte** > **Konfigurieren** für Ihr Gerät.

2. Scrollen Sie bis zum Abschnitt **Geräteadministratorkennwort** nach unten. Geben Sie ein Administratorkennwort ein, das zwischen 8 und 15 Zeichen lang ist. Beim Kennwort muss es sich um eine Kombination aus Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen handeln.

3. Bestätigen Sie das Kennwort.

4. Klicken Sie unten auf der Seite auf **Speichern**.

Das Geräteadministratorkennwort wurde jetzt aktualisiert. Sie können dieses geänderte Kennwort verwenden, um auf die Windows PowerShell-Schnittstelle zugreifen.

## Nächste Schritte

[Erfahren Sie mehr über StorSimple-Sicherheit](storsimple-security.md)

[Erfahren Sie mehr über das Ändern Ihrer Gerätekonfiguration](storsimple-modify-device-config.md).

<!---HONumber=September15_HO1-->