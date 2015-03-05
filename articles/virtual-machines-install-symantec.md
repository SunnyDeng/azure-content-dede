<properties 
	pageTitle="Gewusst wie: Installieren und Konfigurieren von Symantec Endpoint Protection auf einem virtuellen Azure-Computer" 
	description="Beschreibt die Installation und Konfiguration von Symantec Endpoint Protection auf einem virtuellen Computer in Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/26/2015" 
	ms.author="kathydav"/>

#Gewusst wie: Installieren und Konfigurieren von Symantec Endpoint Protection auf einem virtuellen Azure-Computer

Dieser Artikel zeigt Ihnen das Installieren und Konfigurieren des Symantec Endpoint Protection-Clients auf einem neuen oder vorhandenen virtuellen Computer unter Windows Server. Dies ist der vollständige Client, der Dienste wie Viren- und Spywareschutz, Firewall und Eindringschutz enthält. 

Der Client wird als eine Sicherheitserweiterung über den VM-Agent installiert. Auf einem neuen virtuellen Computer installieren Sie den Agent zusammen mit dem Endpunktclient. Auf einem vorhandenen virtuellen Computer ohne den Agent müssen Sie den Agent zunächst herunterladen und installieren. Dieser Artikel deckt beide Situationen ab.

Wenn Sie über ein vorhandenes Abonnement von Symantec für eine lokale Lösung verfügen, können Sie es zum Schützen Ihrer virtuellen Azure-Computer verwenden. Wenn Sie noch kein Kunde sind, können Sie sich für ein Testabonnement registrieren. Weitere Informationen über diese Lösung finden Sie unter [Symantec Endpoint Protection on Microsoft's Azure platform](http://go.microsoft.com/fwlink/p/?LinkId=403942) (Symantec Endpoint Protection auf der Azure-Plattform von Microsoft, in englischer Sprache). Diese Seite enthält auch Links zu Lizenzierungsinformationen und alternative Anweisungen für das Installieren des Clients, wenn Sie bereits ein Symantec-Kunde sind.

##Installieren von Symantec Endpoint Protection auf einem neuen virtuellen Computer

Mit dem [Azure-Verwaltungsportal](http://manage.windowsazure.com) können Sie den VM-Agent und die Symantec-Sicherheitserweiterung installieren, wenn Sie die Option **Aus Katalog** zum Erstellen des virtuellen Computers verwenden. Die Verwendung dieses Ansatzes ist eine einfache Möglichkeit, einen Schutz von Symantec hinzuzufügen, wenn Sie einen einzelnen virtuellen Computer erstellen. 

Die Option **Aus Katalog** öffnet einen Assistenten, der Ihnen beim Einrichten des virtuellen Computers hilft. Sie verwenden die letzte Seite des Assistenten zum Installieren des VM-Agents und der Symantec-Sicherheitserweiterung. 

Allgemeine Anweisungen dazu finden Sie unter [Erstellen eines virtuellen Windows Server-Computers](http://go.microsoft.com/fwlink/p/?LinkId=403943). Führen Sie auf der letzten Seite des Assistenten Folgendes aus:

1.	Unter VM-Agent müsste **VM-Agent installieren** bereits aktiviert sein.

2.	Aktivieren Sie **Symantec Endpoint Protection** unter "Sicherheitserweiterungen".


	![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	Klicken Sie auf das Häkchen unten auf der Seite, um den virtuellen Computer zu erstellen.

## Installieren von Symantec Endpoint Protection auf einem vorhandenen virtuellen Computer

Dafür benötigen Sie Folgendes:

- Das Azure PowerShell-Modul Version 0.8.2 oder neuer. Anweisungen und einen Link zur neuesten Version finden Sie unter [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320552).  

- Der VM-Agent. Anweisungen und einen Downloadlink finden Sie im Blogbeitrag [VM Agent and Extensions - Part 2](http://go.microsoft.com/fwlink/p/?LinkId=403947) (VM-Agent und Erweiterungen, Teil 2, in englischer Sprache).

So installieren Sie die Symantec-Sicherheitserweiterung auf einem vorhandenen virtuellen Computer:

1.	Rufen Sie den Namen des Cloud-Diensts und den Namen des virtuellen Computers ab. Wenn Sie den Namen des Cloud-Diensts und des virtuellen Computers nicht kennen, führen Sie den Befehl **Get-AzureVM** aus, um diese Informationen für alle virtuellen Computer im aktuellen Abonnement anzuzeigen. Ersetzen Sie dann alles zwischen den Anführungszeichen, einschließlich der spitzen Klammern (< und >), und führen Sie die folgenden Befehle aus:

	<p>`$servicename = "<YourServiceName>"`
<p>`$name = "<YourVmName>"`
<p>`$vm = Get-AzureVM -ServiceName $servicename -Name $name`
<p>`Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`

2.	Notieren Sie aus der Anzeige des Befehls Get-AzureVMAvailableExtension die Versionsnummer für die Versionseigenschaft. Führen Sie dann die folgenden Befehle aus:

	<p>`$ver=<version number from the Version property>`
<p>`Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -Version $ver -VM $vm.VM`
<p>`Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

So überprüfen Sie, dass die Symantec-Sicherheitserweiterung installiert wurde und auf dem neuesten Stand ist:

1.	Melden Sie sich beim virtuellen Computer an.
2.	Klicken Sie für Windows Server 2008 R2 auf **Start > Alle Programme > Symantec Endpoint Protection**. Geben Sie für Windows Server 2012 über den Startbildschirm **Symantec** ein und klicken Sie dann auf **Symantec Endpoint Protection**.
3.	Wenden Sie bei Bedarf die im Statusfenster angegebenen Updates an.

## Zusätzliche Ressourcen
[Anmelden bei einem virtuellen Computer unter Windows Server]

[Verwalten von Erweiterungen]

<!--Link references-->
[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: ../virtual-machines-log-on-windows-server/

[Verwalten von Erweiterungen]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409


<!--HONumber=45--> 
