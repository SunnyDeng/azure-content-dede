<properties title="How to install and configure Symantec Endpoint Protection on an Azure VM" pageTitle="How to install and configure Symantec Endpoint Protection on an Azure VM" description="Describes installing and configuring Symantec Endpoint Protection on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Installieren und Konfigurieren von Symantec Endpoint Protection auf einem virtuellen Azure-Computer

In diesem Artikel wird beschrieben, wie der Symantec Endpoint Protection-Client auf einem neuen oder vorhandenen virtuellen Windows Server-Computer installiert und konfiguriert wird. Dies ist der vollständige Client, der Dienste wie den Viren- und Spywareschutz, die Firewall und den Eindringschutz umfasst.

Der Client wird als Sicherheitserweiterung durch die Verwendung des VM Agent installiert. Auf einen neuen virtuellen Computer installieren Sie den VM Agent zusammen mit dem Endpunktclient. Auf einem vorhandenen virtuellen Computer ohne den VM Agent müssen Sie den Agent zunächst herunterladen und installieren. In diesem Artikel werden beide Situationen behandelt.

Wenn Sie über ein vorhandenes Abonnement von Symantec für eine lokale Lösung verfügen, können Sie damit Ihre virtuellen Azure-Computer schützen(<for an on-premises solution, you can use it to protect your azure virtual machines>). Wenn Sie noch kein Kunde sind, können Sie sich für ein Probeabonnement registrieren. Weitere Informationen zu dieser Lösung finden Sie auf der Seite zu [Symantec Endpoint Protection auf der Microsoft Azure-Plattform][Symantec Endpoint Protection auf der Microsoft Azure-Plattform]. Diese Seite enthält auch Links zu Lizenzierungsinformationen und alternative Anweisungen zum Installieren des Clients, wenn Sie bereits Kunde bei Symantec sind.

## Installieren von Symantec Endpoint Protection auf einem neuen virtuellen Computer

Über das [Azure-Verwaltungsportal][Azure-Verwaltungsportal] können Sie den VM Agent und die Sicherheitserweiterung von Symantec installieren, wenn Sie die Option **Aus Katalog** verwenden, um den virtuellen Computer zu erstellen. Auf diese Weise können Sie den Schutz von Symantec einfach hinzufügen, wenn Sie einen einzelnen virtuellen Computer erstellen.

Die Option **Aus Katalog** öffnet ein Assistenten, der Sie beim Einrichten des virtuellen Computers unterstützt. Sie verwenden die letzte Seite des Assistenten, um den VM Agent und die Sicherheitserweiterung von Symantec zu installieren.

Allgemeine Anweisungen finden Sie unter [Erstellen eines virtuellen Windows Server-Computers][Erstellen eines virtuellen Windows Server-Computers]. Gehen Sie wie folgt vor, wenn Sie zur letzten Seite des Assistenten gelangen:

1.  Aktivieren Sie unter VM Agent die Option **VM Agent installieren**.

2.  Aktivieren Sie unter den Sicherheitserweiterungen die Option **Symantec Endpoint Protection**.

    ![Installieren des VM Agent und des Endpoint Protection-Clients][Installieren des VM Agent und des Endpoint Protection-Clients]

3.  Klicken Sie auf das Häkchen am unteren Seitenrand, um den virtuellen Computer zu erstellen.

## Installieren von Symantec Endpoint Protection auf einem vorhandenen virtuellen Computer

Folgendes ist für die Installation des Deep Security Agent auf einem vorhandenen virtuellen Computer erforderlich:

-   Das Azure PowerShell-Modul, Version 0.8.2 oder höher. Anweisungen und einen Link zur neuesten Version finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].

-   Den VM Agent. Anweisungen und einen Link zum Download finden Sie im Blogbeitrag zum [VM Agent und Erweiterungen – Teil 2][VM Agent und Erweiterungen – Teil 2].

Öffnen Sie eine Azure PowerShell-Sitzung, und führen Sie die folgenden Befehle aus. Achten Sie darauf, dass Sie Ihre eigenen Werte für die Platzhalter wie „MyServiceName“ einsetzen.

1.  Rufen Sie den Clouddienstnamen, den Namen des virtuellen Computers ab, und speichern Sie jeden dieser Werte in Variablen, damit diese in den nächsten Befehlen verwendet werden können:

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] Wenn Ihnen die Namen für den Clouddienst und den virtuellen Computer nicht bekannt sind, führen Sie „Get-AzureVM“ aus, um diese Informationen für alle virtuellen Computer des aktuellen Abonnements anzuzeigen.

2.  Fügen Sie den Symantec Endpoint Protection-Agent zum virtuellen Computer hinzu. Führen Sie folgenden Befehl aus, um die neueste Version zu installieren:

    `Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -VM $vm.VM`

    > [WACOM.NOTE] Wenn Sie eine bestimmte Version installieren möchten, führen Sie den folgenden Befehl aus, um eine Liste der verfügbaren Versionen abzurufen: `Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`.
    > Dann beziehen Sie den Versionsparameter mit ein, wenn Sie „Set-AzureVMExtension“ ausführen.

3.  Aktualisieren Sie den virtuellen Computer, der den Symantec Endpoint Protection-Agent installiert:

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## Zusätzliche Ressourcen

[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]

[Verwalten von Erweiterungen][Verwalten von Erweiterungen]

<!--Link references-->

  [Symantec Endpoint Protection auf der Microsoft Azure-Plattform]: http://go.microsoft.com/fwlink/p/?LinkId=403942
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Erstellen eines virtuellen Windows Server-Computers]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [Installieren des VM Agent und des Endpoint Protection-Clients]: ./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png
  [Installieren und Konfigurieren von Azure PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [VM Agent und Erweiterungen – Teil 2]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: ../virtual-machines-log-on-windows-server/
  [Verwalten von Erweiterungen]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
