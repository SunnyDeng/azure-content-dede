<properties 
   pageTitle="Ändern der DATA 0-Netzwerkschnittstelleneinstellungen auf dem StorSimple-Gerät"
   description="Erfahren Sie, wie Sie die DATA 0-Netzwerkschnittstelle auf dem StorSimple-Gerät neu konfigurieren."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/30/2015"
   ms.author="alkohli" />

# Ändern der DATA 0-Netzwerkschnittstelleneinstellungen auf dem StorSimple-Gerät

## Übersicht
Ihr Microsoft Azure StorSimple-Gerät hat sechs Netzwerkschnittstellen \(DATA 0 bis DATA 5\). Die DATA 0-Schnittstelle wird immer über die Windows PowerShell-Schnittstelle oder die serielle Konsole konfiguriert und ist automatisch für die Cloud aktiviert. Die DATA 0-Schnittstelle wird zunächst über den Setup-Assistent während der ersten Bereitstellung des StorSimple-Geräts konfiguriert. Wenn sich das Gerät im Betriebsmodus befindet, müssen Sie die DATA 0-Einstellungen möglicherweise neu konfigurieren. In diesem Tutorial werden zwei Methoden zum Ändern der DATA 0-Netzwerkeinstellungen beschrieben. Beide Methoden werden über Windows PowerShell für StorSimple ausgeführt.

In diesem Tutorial lernen Sie Folgendes:

- Ändern von DATA 0-Netzwerkeinstellungen über den Setup-Assistenten
- Ändern von DATA 0-Netzwerkeinstellungen über das Cmdlet `Set-HcsNetInterface`


## Ändern von DATA 0-Netzwerkeinstellungen über den Setup-Assistenten
Sie können die DATA 0-Netzwerkeinstellungen neu konfigurieren, indem Sie eine Verbindung mit der Windows PowerShell-Schnittstelle Ihres StorSimple-Geräts herstellen und eine Sitzung des Setup-Assistenten starten. Führen Sie die folgenden Schritte aus, um DATA 0-Einstellungen zu ändern.

#### So ändern Sie die DATA 0-Netzwerkeinstellungen über den Setup-Assistenten

1. Wählen Sie im Menü der seriellen Konsole Option 1 aus, d. h. die** Anmeldung mit Vollzugriff**. Geben Sie bei entsprechender Aufforderung das **Geräteadministratorkennwort** ein. Das Standardkennwort lautet `Password1`.

1. Geben Sie an der Eingabeaufforderung Folgendes ein:


	`Invoke-HcsSetupWizard`

1. Ein Setup-Assistent wird angezeigt, mit dessen Hilfe sie die DATA 0-Schnittstelle Ihres Geräts konfigurieren können. Geben Sie neue Werte für die IP-Adresse, das Gateway und die Netzmaske an.

> [AZURE.NOTE]Die festen IP-Adressen des Controllers müssen auf der Seite „Konfigurieren“ des StorSimple-Geräts im Azure-Verwaltungsportal neu konfiguriert werden. Weitere Informationen finden Sie unter [Ändern der Netzwerkschnittstelle über die Konfigurationsseite des Geräts](storsimple-modify-device-config.md#modify-network-interfaces).


## Ändern der DATA 0-Netzwerkeinstellungen über das Cmdlet „Set-HcsNetInterface“
Sie können die DATA 0-Netzwerkschnittstelle auch mithilfe des Cmdlets `Set-HcsNetInterface` neu konfigurieren. Das Cmdlet wird über die Windows PowerShell-Schnittstelle Ihres StorSimple-Geräts ausgeführt. Führen Sie die folgenden Schritte aus, um die DATA 0-Einstellungen zu ändern.

#### So ändern Sie DATA 0-Netzwerkeinstellungen über das Cmdlet „Set-HcsNetInterface“

1. Wählen Sie im Menü der seriellen Konsole Option 1 aus, d. h. die** Anmeldung mit Vollzugriff**. Geben Sie bei entsprechender Aufforderung das **Geräteadministratorkennwort** ein. Das Standardkennwort lautet `Password1`.

1. Geben Sie an der Eingabeaufforderung Folgendes ein:

	`Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`

	Wenn Sie IPv6-Adressen verwenden, führen Sie den folgenden Befehl aus:

	`Set-HCSNetInterface -InterfaceAlias Data0 -IPv6Address <> -IPv6Netmask <> -IPv6Gateway <> -Controller0IPv6Address <> -Controller1IPv6Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`

1. Geben Sie Werte für DATA 0 in spitzen Klammern \(\< \>\) für Folgendes an:
											
	- IPv4-/IPv6-Adresse
	
	- IPv4-/IPv6-Gateway
	
	- IPv4-/IPv6-Subnetzmaske
	
	- Feste IPv4-/IPv6-Adresse für Controller 0

	- Feste IPv4-/IPv6-Adresse für Controller 1

## Nächste Schritte
Wenn beim Konfigurieren der Netzwerkschnittstellen Probleme auftreten, lesen Sie die Informationen unter [Beheben von Bereitstellungsproblemen](storsimple-troubleshoot-deployment.md).

<!---HONumber=July15_HO5-->