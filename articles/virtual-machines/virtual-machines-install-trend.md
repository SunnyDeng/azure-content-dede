<properties 
	pageTitle="Installieren und Konfigurieren von Trend Micro Deep Security als Dienst auf einem virtuellen Azure-Computer" 
	description="Beschreibt die Installation und Konfiguration von der Trend Micro-Sicherheitslösung auf einem virtuellen Computer in Azure." 
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
	ms.date="02/17/2015" 
	ms.author="kathydav"/>


# Installieren und Konfigurieren von Trend Micro Deep Security als Dienst auf einem virtuellen Azure-Computer

Dieser Artikel zeigt Ihnen das Installieren und Konfigurieren von Trend Micro Deep Security als einen Dienst auf einem neuen oder vorhandenen virtuellen Computer unter Windows Server. Der durch Deep Security-als-ein-Dienst bereitgestellte Schutz umfasst den Antischadsoftwareschutz, die Firewall, das Eindringschutzsystem und die Integritätsüberwachung.

Der Client wird als eine Sicherheitserweiterung über den VM-Agent installiert. Auf einem neuen virtuellen Computer installieren Sie den VM-Agent zusammen mit Deep Security Agent. Auf einem vorhandenen virtuellen Computer, der nicht über den VM-Agent verfügt, müssen Sie diesen zunächst herunterladen und installieren. Dieser Artikel deckt beide Situationen ab.

Wenn Sie über ein vorhandenes Abonnement von Trend Micro für eine lokale Lösung verfügen, können Sie es zum Schützen Ihrer virtuellen Azure-Computer verwenden. Wenn Sie noch kein Kunde sind, können Sie sich für ein Testabonnement registrieren. Weitere Informationen zu dieser Lösung finden Sie im Trend Micro-Blogbeitrag [Microsoft Azure VM Agent Extension For Deep Security](http://go.microsoft.com/fwlink/p/?LinkId=403945) (Microsoft Azure-VM-Agent-Erweiterung für Deep Security, in englischer Sprache).

## Installieren von Deep Security Agent auf einem neuen virtuellen Computer

Mit dem [Azure-Verwaltungsportal](http://manage.windowsazure.com) können Sie den VM-Agent und die Trend Micro-Sicherheitserweiterung installieren, wenn Sie die Option **Aus Katalog** zum Erstellen des virtuellen Computers verwenden. Die Verwendung dieses Ansatzes ist eine einfache Möglichkeit, einen Schutz von Trend Micro hinzuzufügen, wenn Sie einen einzelnen virtuellen Computer erstellen.

Die Option **Aus Katalog** öffnet einen Assistenten, der Ihnen beim Einrichten des virtuellen Computers hilft. Sie verwenden die letzte Seite des Assistenten zum Installieren des VM-Agents und der Trend Micro-Sicherheitserweiterung. Allgemeine Anweisungen dazu finden Sie unter [Erstellen eines virtuellen Windows Server-Computers](virtual-machines-windows-tutorial.md). Führen Sie auf der letzten Seite des Assistenten Folgendes aus:

1.	Aktivieren Sie **VM-Agent installieren** unter „VM-Agent“.

2.	Aktivieren Sie **Trend Micro Deep Security Agent** unter „Sicherheitserweiterungen“.

	![Installieren des VM-Agents und von Deep Security Agent](./media/virtual-machines-install-trend/InstallVMAgentandTrend.png)

3.	Aktivieren Sie das Kontrollkästchen, um den virtuellen Computer zu erstellen.

## Installieren von Deep Security Agent auf einem vorhandenen virtuellen Computer

Dafür benötigen Sie Folgendes:

- Das Azure PowerShell-Modul, Version 0.8.2 oder höher, muss auf Ihrem lokalen Computer installiert sein. Sie können die installierte Version von Azure PowerShell mit dem Befehl **Get-Module azure | format-table version** überprüfen. Anweisungen und einen Link zur neuesten Version finden Sie unter [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md). 

- Der VM-Agent muss auf dem virtuellen Zielcomputer installiert sein.

Überprüfen Sie zunächst, ob der VM-Agent bereits installiert ist. Füllen Sie den Cloud-Dienstnamen und den Namen des virtuellen Computers aus, und führen Sie dann die folgenden Befehle an einer Azure PowerShell-Eingabeaufforderung mit Administratorrechten aus. Ersetzen Sie alles innerhalb der Anführungszeichen, einschließlich der Zeichen < and >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName 
	write-host $vm.VM.ProvisionGuestAgent

Wenn Sie den Namen des Clouddiensts und des virtuellen Computers nicht kennen, führen Sie **Get-AzureVM** aus, um diese Informationen für alle virtuellen Computer im aktuellen Abonnement anzuzeigen.

Der Befehl **write-host** gibt **True** zurück, wenn der VM-Agent installiert ist. Wenn **False** zurückgegeben wird, nutzen Sie die Anweisungen und den Link zum Download im Azure-Blogbeitrag [VM Agent and Extensions - Part 2](http://go.microsoft.com/fwlink/p/?LinkId=403947) (VM-Agent und Erweiterungen, Teil 2, in englischer Sprache).

Wenn der VM-Agent installiert ist, führen Sie diese Befehle aus.

	$Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA
	Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## Nächste Schritte

Es dauert einige Minuten, bis der Agent gestartet wird, wenn er installiert ist. Danach müssen Sie Deep Security auf dem virtuellen Computer aktivieren, sodass es durch einen Deep Security Manager verwaltet werden kann. Weitere Anweisungen finden Sie in folgenden Ressourcen:

- Der Artikel von Trend in Bezug auf diese Lösung [Instant-On Cloud Security for Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101) (Instant-On Cloud Security für Microsoft Azure) (in englischer Sprache).
- Ein [Windows PowerShell-Beispielskript](http://go.microsoft.com/fwlink/?LinkId=404100) zum Konfigurieren des virtuellen Computers.
- [Anweisungen](http://go.microsoft.com/fwlink/?LinkId=404099) für das Beispiel.

## Zusätzliche Ressourcen

[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]

[Azure-VM-Erweiterungen und Features]


<!--Link references-->
[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: virtual-machines-log-on-windows-server.md
[Azure-VM-Erweiterungen und Features]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

 

<!---HONumber=July15_HO2-->