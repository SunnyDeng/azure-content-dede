<properties 
   pageTitle="Erstellen und Verwalten eines Unterstützungspakets | Microsoft Azure"
   description="Hier erfahren Sie, wie Sie ein Unterstützungspaket für Ihr StorSimple-Gerät erstellen, entschlüsseln und bearbeiten."
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
   ms.date="07/24/2015"
   ms.author="alkohli" />


# Erstellen und Verwalten eines Unterstützungspakets

## Übersicht
Dieses Tutorial beschreibt die verschiedenen Aufgaben im Zusammenhang mit der Erstellung und Verwaltung eines Unterstützungspakets. Ein Unterstützungspaket enthält alle relevanten Protokolle in einem verschlüsselten, komprimierten Format und unterstützt das Supportteam von Microsoft beim Behandeln von Problemen mit StorSimple-Geräten.

Dieses Tutorial enthält schrittweise Anleitungen für die Erstellung und Verwaltung des Unterstützungspakets mithilfe von:

- Abschnitt **Unterstützungspaket** auf der Seite **Wartung** des StorSimple Manager-Diensts
- Windows PowerShell für StorSimple

In diesem Tutorial lernen Sie Folgendes:

- Erstellen eines Unterstützungspakets
- Entschlüsseln und Bearbeiten eines Unterstützungspakets


## Erstellen eines Unterstützungspakets im Verwaltungsportal
Zur Behandlung möglicher Probleme mit dem StorSimple Manager-Dienst können Sie im Verwaltungsportal über die Wartungsseite des Diensts ein Unterstützungspaket erstellen und an die Microsoft Support-Website hochladen. Für den Upload wird ein Unterstützungshauptschlüssel benötigt. Den Unterstützungshauptschlüssel erhalten Sie per E-Mail vom zuständigen Supporttechniker. Ein unverschlüsseltes, komprimiertes Unterstützungspaket (CAB-Datei) wird erstellt. Dieses Paket kann dann vom Supporttechniker über die Support-Website abgerufen werden, wenn er den Hauptschlüssel bereitstellt.

Führen Sie im Verwaltungsportal die folgenden Schritte aus, um ein Unterstützungspaket zu erstellen:

#### So erstellen Sie ein Unterstützungspaket im Verwaltungsportal

1. Navigieren Sie zu **Geräte > Wartung**.

1. Klicken Sie im Abschnitt **Unterstützungspaket** auf **Unterstützungspaket erstellen und hochladen**.

	

1. Führen Sie im Dialogfeld **Unterstützungspaket erstellen und hochladen** die folgenden Schritte aus:

	![Erstellen eines Unterstützungspakets](./media/storsimple-create-manage-support-package/IC740923.png)
											
	- Geben Sie den Unterstützungshauptschlüssel an. Diesen Schlüssel erhalten Sie per E-Mail vom zuständigen Microsoft-Supporttechniker.
 	
	- Aktivieren Sie das Kombinationsfeld **Ich stimme zu, das Supportpaket automatisch auf die Microsoft Support-Website hochzuladen**.
 	
	- Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-create-manage-support-package/IC740895.png).


## Erstellen eines Unterstützungspakets in Windows PowerShell für StorSimple
Wenn Sie Ihre Protokolldateien vor der Paketerstellung bearbeiten möchten, müssen Sie das Paket mithilfe von Windows PowerShell für StorSimple erstellen.

Führen Sie die folgenden Schritte aus, um ein Unterstützungspaket in Windows PowerShell für StorSimple zu erstellen:


#### So erstellen Sie ein Unterstützungspaket in Windows PowerShell für StorSimple

1. Geben Sie den folgenden Befehl ein, um auf dem Remotecomputer, über den eine Verbindung mit dem StorSimple-Gerät hergestellt wird, eine Windows PowerShell-Sitzung als Administrator zu starten:

	`Start PowerShell`

1. Stellen Sie in der Windows PowerShell-Sitzung eine Verbindung mit dem SSAdminConsole-Runspace des Geräts her:


	- Geben Sie an der Eingabeaufforderung Folgendes ein: 
			
		`$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
		
		
	1. Geben Sie im angezeigten Dialogfeld Ihr Administratorkennwort ein. Standardkennwort:
	 
		`Password1`

		![PowerShell-Sitzung an SSAdminConsole-Runspace](./media/storsimple-create-manage-support-package/IC740962.png)

	2. Klicken Sie auf **OK**.
	1. Geben Sie an der Eingabeaufforderung Folgendes ein: 
		
		`Enter-PSSession $MS`


1. Geben Sie in der geöffneten Sitzung den entsprechenden Befehl ein.


	- Geben Sie für kennwortgeschützte Netzwerkfreigaben Folgendes ein:

		`Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`

		Sie werden aufgefordert, ein Kennwort, einen Pfad zum freigegebenen Netzwerkordner und eine Verschlüsselungspassphrase (da das Unterstützungspaket verschlüsselt ist) einzugeben. Anschließend wird im angegebenen Ordner ein Unterstützungspaket erstellt.
											

	- Bei offenen freigegebenen Netzwerkordnern (ohne Kennwortschutz) wird der Parameter „-Credential“ nicht benötigt. Geben Sie Folgendes ein:

		`Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`

		Das Unterstützungspaket wird für beide Controller im freigegebenen Netzwerkordner erstellt. Hierbei handelt es sich um eine verschlüsselte, komprimierte Datei, die zur Problembehandlung an den Support von Microsoft gesendet werden kann. Weitere Informationen finden Sie unter [Kontaktaufnahme mit dem Microsoft-Support](https://msdn.microsoft.com/library/dn757750.aspx).


### Weitere Informationen zum Cmdlet „Export-HcsSupportPackage“
Die folgende Tabelle enthält die verschiedenen Parameter, die für das Cmdlet „Export-HcsSupportPackage“ verwendet werden können:

| S. Nr. | Parameter | Erforderlich/Optional | Beschreibung |
|--------|----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1 | Path | Erforderlich | Dient zum Angeben des Orts des freigegebenen Netzwerkordners für das Unterstützungspaket. |
| 2 | EncryptionPassphrase | Erforderlich | Dient zum Angeben einer Passphrase für die Verschlüsselung des Unterstützungspakets. |
| 3 | Credential | Optional | Dient zum Angeben von Anmeldeinformationen für den Zugriff auf den freigegebenen Netzwerkordner. |
| 4 | Force | Optional | Dient zum Überspringen des Bestätigungsschritts für die Verschlüsselungspassphrase. |
| 5 | PackageTag | Optional | Dient zum Angeben eines Verzeichnisses unter „Path“, in dem das Unterstützungspaket platziert werden soll. Standardmäßig wird Folgendes verwendet: [Gerätename]-[ aktuelles Datum und aktuelle Uhrzeit:JJJJ-MM-TT-HH-mm-ss]. |
| 6 | Scope | Optional | Geben Sie **Cluster** (Standardeinstellung) an, um ein Unterstützungspaket für beide Controller zu erstellen. Wenn Sie nur ein Paket für den aktuellen Controller erstellen möchten, geben Sie **Controller** an. |


## Bearbeiten eines Unterstützungspakets

Unter Umständen muss ein Unterstützungspaket nach der Generierung bearbeitet werden, um kundenspezifische Informationen wie Volumenamen, Geräte-IP-Adressen und Sicherungsnamen aus den Protokolldateien zu entfernen.

> [AZURE.IMPORTANT]Unterstützungspakete können nur bearbeitet werden, wenn sie mit Windows PowerShell für StorSimple generiert wurden. Pakete, die im Verwaltungsportal mit dem StorSimple Manager-Dienst erstellt wurden, können nicht bearbeitet werden.

Wenn Sie ein Unterstützungspaket vor dem Hochladen an die Microsoft Support-Website bearbeiten möchten, müssen Sie es entschlüsseln, die Dateien bearbeiten und es anschließend wieder verschlüsseln. Führen Sie zum Bearbeiten eines Unterstützungspakets die folgenden Schritte aus:

#### So bearbeiten Sie ein Unterstützungspaket in Windows PowerShell für StorSimple

1. Generieren Sie mithilfe von Windows PowerShell für StorSimple ein Unterstützungspaket, wie unter [Erstellen eines Unterstützungspakets](#Create-a-Support-package-in-Windows-PowerShell-for-StorSimple) beschrieben.

1. Laden Sie das [Skript](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokal auf den Client herunter.


1. Importieren Sie das Windows PowerShell-Modul. Geben Sie den Pfad des lokalen Ordners an, in den Sie das Skript heruntergeladen haben. Geben Sie zum Importieren des Moduls Folgendes ein:
 
	`Import-module <Path to the folder that contains the Windows PowerShell script>`

1. Öffnen Sie den Ordner mit dem Unterstützungspaket. Beachten Sie, dass es sich bei allen Dateien um komprimierte, verschlüsselte AES-Dateien handelt. Öffnen Sie die Dateien. Geben Sie zum Öffnen von Dateien Folgendes ein:

	`Open-HcsSupportPackage <Path to the folder that contains support package files>`

	Dadurch werden die Dateien dekomprimiert und entschlüsselt. Außerdem werden die Dateien nun mit ihrer tatsächlichen Dateierweiterung angezeigt.
	
	![Bearbeiten des Unterstützungspakets 3](./media/storsimple-create-manage-support-package/IC750706.png)


1. Geben Sie die bei der Paketerstellung verwendete Verschlüsselungspassphrase ein, wenn Sie dazu aufgefordert werden.

    	cmdlet Open-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:EncryptionPassphrase: ****
	

1. Navigieren Sie zum Ordner mit den Protokolldateien. Da die Protokolldateien nun dekomprimiert und entschlüsselt sind, werden sie mit ihrer ursprünglichen Dateierweiterung angezeigt. Entfernen Sie alle kundenspezifischen Informationen wie Volumenamen und Geräte-IP-Adressen aus den Dateien, und speichern Sie sie.

1. Schließen Sie die Dateien. Beim Schließen werden die Dateien mit Gzip komprimiert mit AES-256 verschlüsselt. Dies macht die Übertragung des Unterstützungspakets über ein Netzwerk sicherer und schneller. Geben Sie zum Schließen von Dateien Folgendes ein:

	`Close-HcsSupportPackage <Path to the folder that contains support package files>`

	![Bearbeiten des Unterstützungspakets 2](./media/storsimple-create-manage-support-package/IC750707.png)

1. Geben Sie eine Verschlüsselungspassphrase für das geänderte Unterstützungspaket ein, wenn Sie dazu aufgefordert werden.

	    cmdlet Close-HcsSupportPackage at command pipeline position 1
    	Supply values for the following parameters:EncryptionPassphrase: ****

1. Notieren Sie sich die Passphrase, um Sie auf Anfrage dem Support von Microsoft mitteilen zu können.


### Beispiel: Bearbeiten von Dateien in einem Unterstützungspaket auf einer kennwortgeschützten Freigabe

Das folgende Beispiel veranschaulicht das Entschlüsseln, Bearbeiten und erneute Verschlüsseln eines Unterstützungspakets:

![Bearbeiten des Unterstützungspakets 1](./media/storsimple-create-manage-support-package/IC750708.png)

    	PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1
    
    	PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw
    
    	cmdlet Open-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:
    
    	EncryptionPassphrase: ****
    
    	PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw
    
    	cmdlet Close-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:
    
    	EncryptionPassphrase: ****
    
    	PS C:\WINDOWS\system32>

## Nächste Schritte

Informieren Sie sich darüber, wie Sie [mithilfe von Unterstützungspaketen und Geräteprotokollen Probleme bei der Gerätebereitstellung behandeln](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).

<!---HONumber=August15_HO6-->