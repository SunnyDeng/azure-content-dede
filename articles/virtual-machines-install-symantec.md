<properties title="Gewusst wie: Installieren und Konfigurieren von Symantec Endpoint Protection auf einem virtuellen Azure-Computer" pageTitle="Gewusst wie: Installieren und Konfigurieren von Symantec Endpoint Protection auf einem virtuellen Azure-Computer" description="Beschreibt die Installation und Konfiguration von Symantec Endpoint Protection auf einem virtuellen Computer in Azure." metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="7/16/2014" ms.author="kathydav" />

# Gewusst wie: Installieren und Konfigurieren von Symantec Endpoint Protection auf einem virtuellen Azure-Computer

Dieser Artikel zeigt Ihnen das Installieren und Konfigurieren des Symantec Endpoint Protection-Clients auf einem neuen oder vorhandenen virtuellen Computer unter Windows Server. Dies ist der vollständige Client, der Dienste wie Viren- und Spywareschutz, Firewall und Eindringschutz enthält.

Der Client wird als eine Sicherheitserweiterung über den VM-Agent installiert. Auf einem neuen virtuellen Computer installieren Sie den Agent zusammen mit dem Endpunktclient. Auf einem vorhandenen virtuellen Computer ohne den Agent müssen Sie den Agent zunächst herunterladen und installieren. Dieser Artikel deckt beide Situationen ab.

Wenn Sie über ein vorhandenes Abonnement von Symantec für eine lokale Lösung verfügen, können Sie es zum Schützen Ihrer virtuellen Azure-Computer verwenden. Wenn Sie noch kein Kunde sind, können Sie sich für ein Testabonnement registrieren. Weitere Informationen über diese Lösung finden Sie unter [Symantec Endpoint Protection on Microsoft's Azure platform][Symantec Endpoint Protection on Microsoft's Azure platform] (Symantec Endpoint Protection auf der Azure-Plattform von Microsoft) (in englischer Sprache). Diese Seite enthält auch Links zu Lizenzierungsinformationen und alternative Anweisungen für das Installieren des Clients, wenn Sie bereits ein Symantec-Kunde sind.

## Installieren von Symantec Endpoint Protection auf einem neuen virtuellen Computer

Mit dem [Azure-Verwaltungsportal][Azure-Verwaltungsportal] können Sie den VM-Agent und die Symantec-Sicherheitserweiterung installieren, wenn Sie die Option **Aus Katalog** zum Erstellen des virtuellen Computers verwenden. Die Verwendung dieses Ansatzes ist eine einfache Möglichkeit, einen Schutz von Symantec hinzuzufügen, wenn Sie einen einzelnen virtuellen Computer erstellen.

Die Option **Aus Katalog** öffnet einen Assistenten, der Ihnen beim Einrichten des virtuellen Computers hilft. Sie verwenden die letzte Seite des Assistenten zum Installieren des VM-Agents und der Symantec-Sicherheitserweiterung.

Allgemeine Anweisungen dazu finden Sie unter [Erstellen eines virtuellen Windows Server-Computers][Erstellen eines virtuellen Windows Server-Computers]. Führen Sie auf der letzten Seite des Assistenten Folgendes aus:

1.  Aktivieren Sie **VM-Agent installieren** unter „VM-Agent“.

2.  Aktivieren Sie **Symantec Endpoint Protection** unter „Sicherheitserweiterungen“.

    ![Installieren des VM-Agents und des Endpoint Protection-Clients][Installieren des VM-Agents und des Endpoint Protection-Clients]

3.  Klicken Sie auf das Häkchen unten auf der Seite, um den virtuellen Computer zu erstellen.

## Installieren von Symantec Endpoint Protection auf einem vorhandenen virtuellen Computer

Zum Installieren von Deep Security Agent auf einem vorhandenen virtuellen Computer benötigen Sie Folgendes:

-   Das Azure PowerShell-Modul Version 0.8.2 oder neuer. Anweisungen und einen Link zur neuesten Version finden Sie unter [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell][Gewusst wie: Installieren und Konfigurieren von Azure PowerShell].

-   Der VM-Agent. Anweisungen und einen Downloadlink finden Sie im Blogbeitrag [VM Agent and Extensions - Part 2][VM Agent and Extensions - Part 2] (VM-Agent und Erweiterungen – Teil 2) (in englischer Sprache).

Öffnen Sie eine Azure PowerShell-Sitzung, und führen Sie die folgenden Befehle aus. Ersetzen Sie die Platzhalter wie MyServiceName durch Ihre eigenen Werte.

1.  Rufen Sie den Namen des Cloud-Diensts, den Namen des virtuellen Computers und den virtuellen Computer ab, und speichern Sie die eben genannten in Variablen, sodass sie durch die nächsten Befehle verwendet werden können:

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] Wenn Sie den Namen des Cloud-Diensts und des virtuellen Computers nicht kennen, führen Sie „Get-AzureVM“ aus, um diese Informationen für alle virtuellen Computer im aktuellen Abonnement anzuzeigen.

2.  Fügen Sie dem virtuellen Computer den Symantec Endpoint Protection-Agent hinzu. So installieren Sie die neueste Version

    `Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -VM $vm.VM`

    > [WACOM.NOTE] Wenn Sie eine bestimmte Version installieren möchten, führen Sie den folgenden Befehl aus, um eine Liste der verfügbaren Versionen abzurufen. `Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`.
    > Nehmen Sie anschließend den Parameter „Version“ beim Ausführen von „Set-AzureVMExtension“ auf.

3.  Aktualisieren Sie den virtuellen Computer, wodurch der Symantec Endpoint Protection-Agent installiert wird:

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## Zusätzliche Ressourcen

[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]

[Verwalten von Erweiterungen][Verwalten von Erweiterungen]

<!--Link references-->

  [Symantec Endpoint Protection on Microsoft's Azure platform]: http://go.microsoft.com/fwlink/p/?LinkId=403942
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Erstellen eines virtuellen Windows Server-Computers]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [Installieren des VM-Agents und des Endpoint Protection-Clients]: ./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png
  [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [VM Agent and Extensions - Part 2]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: ../virtual-machines-log-on-windows-server/
  [Verwalten von Erweiterungen]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
