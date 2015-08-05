
<properties 
    pageTitle="Sichere Apps und Ressourcen in Azure RemoteApp"
    description="Erfahren Sie, wie Sie Apps und Ressourcen in Azure RemoteApp sperren können." 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/16/2015" 
    ms.author="elizapo" />



# Sichere Apps und Ressourcen in Azure RemoteApp

Azure RemoteApp bietet Benutzern Zugriff auf zentral verwaltete Windows-Apps, wodurch Sie steuern können, was Ihre Benutzer tun dürfen und was nicht. Dies ist besonders nützlich, wenn Benutzer über ein nicht verwaltetes Gerät eine Verbindung herstellen wollen (z. B. sein persönliches Macbook), und Sie den Benutzerzugriff und die -erfahrung steuern möchten.
 
Wenn Sie beispielsweise Active Directory für die Benutzerauthentifizierung verwenden und verhindern wollen, dass Benutzer Daten aus einer Anwendung kopieren, können Sie eine Remotedesktop-Gruppenrichtlinie so konfigurieren, dass das Kopieren von Daten für Benutzer blockiert wird.
 
Ein weiteres Beispiel ist das Blockieren des Internetzugriffs für eine bestimmte App in Ihrer Sammlung. Sie können eine Regel für die Windows-Firewall erstellen, die den Zugriff blockiert, wenn Sie das Image für die Sammlung erstellen.

## Optionen für die Implementierung

  Hier sind die wichtigsten Implementierungsoptionen, die je nach Bedarf einzeln oder zusammen verwendet werden können:

1.	Wenn Ihre RemoteApp-Sammlung mit einer Domäne verbunden ist, können Sie eine [Gruppenrichtlinie](https://technet.microsoft.com/library/cc725828.aspx) erzwingen (mit Ausnahme der Timeout-Richtlinien "Im Leerlauf" und "Trennen", die [hier](../azure-subscription-service-limits.md) beschrieben werden).
2.	Konfigurieren Sie als Alternative zur Gruppenrichtlinie (wenn Ihre Sammlung nicht mit einer Domäne verbunden ist oder Sie nicht die richtigen Berechtigungen in Active Directory haben) [Lokale Richtlinien](https://technet.microsoft.com/library/cc775702.aspx) in Ihrem Vorlagenimage. Beachten Sie, dass Gruppenrichtlinien Vorrang vor lokalen Richtlinien haben, wenn ein Konflikt vorliegt.
3.	Einige OS-/App-Einstellungen können nicht über Richtlinien konfiguriert werden. Dies funktioniert jedoch beim Konfigurieren des Vorlagenimages über den Registrierungsschlüssel mit dem [Programm "RegEdit"](./remoteapp-hybridtrouble.md).
4.	Sie können die [Windows-Firewall](http://windows.microsoft.com/en-US/windows-8/Windows-Firewall-from-start-to-finish) nutzen, um den Netzwerkzugriff auf und von dem Computer zu steuern, auf dem die Anwendung ausgeführt wird. Achten Sie darauf, dass Sie die hier definierten URLs und Ports nicht blockieren.
5.	Sie können [AppLocker](https://technet.microsoft.com/library/hh831440.aspx) verwenden, um zu steuern, welche Anwendungen und Dateien Benutzer ausführen können. Beispielsweise könnten gewiefte Benutzer herausfinden, wie nicht veröffentlichte Anwendungen ausgeführt werden, die aber im Image vorhanden sind, das Sie zum Erstellen der Sammlung erzeugt haben – AppLocker kann das blockieren.
 
## Ausführliche Informationen

- Die folgenden RDS-Richtlinien können besonders hilfreich sein: 
	- [Geräte- und Ressourcenumleitung](https://technet.microsoft.com/library/ee791794.aspx)
	- [Druckerumleitung](https://technet.microsoft.com/library/ee791784.aspx)
	- [Profile](https://technet.microsoft.com/library/ee791865.aspx).
- Beachten Sie, dass sich die Konfigurationsumleitungen über das RemoteApp-PowerShell-Modul (wie [hier](./remoteapp-redirection.md) beschrieben) auf den Clientcomputer stützen, um die Richtlinie zu erzwingen. Wenn Sicherheit das primäre Ziel ist, sollten Sie die Richtlinie über das Vorlagenimage für die lokale Richtlinie oder die Gruppenrichtlinie erzwingen.
- [Richtlinien für Windows Server 2012 R2](https://technet.microsoft.com/library/hh831791.aspx).
- [Office 2013-Richtlinien](https://technet.microsoft.com/library/cc178969.aspx) (einschließlich[Informationen zum Anpassen der Office-Symbolleiste](https://technet.microsoft.com/library/cc179143.aspx)).
 

<!---HONumber=July15_HO4-->