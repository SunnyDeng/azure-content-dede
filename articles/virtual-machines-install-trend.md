<properties title="How to install and configure Trend on an Azure VM" pageTitle="How to install and configure Trend Micro Deep Security as a Service on an Azure VM" description="Describes installing and configuring Trend Micro security on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Installieren und Konfigurieren von Trend Micro Deep Security als Dienst auf einem virtuellen Azure-Computer

In diesem Artikel wird beschrieben, wie der Trend Micro Deep Security als Dienst auf einem neuen oder vorhandenen virtuellen Windows Server-Computer installiert und konfiguriert wird. Der von Deep Security als Dienst bereitgestellte Schutz umfasst den Schadsoftwareschutz, eine Firewall, ein Eindringschutzsystem und die Integritätsüberwachung.

Der Client wird als Sicherheitserweiterung mithilfe des VM Agent installiert. Auf einen neuen virtuellen Computer installieren Sie den VM Agent zusammen mit dem Deep Security Agent. Auf einem vorhandenen virtuellen Computer ohne VM Agent müssen Sie den Agent zunächst herunterladen und installieren. In diesem Artikel werden beide Situationen behandelt.

Wenn Sie über ein vorhandenes Abonnement von Trend für eine lokale Lösung verfügen, können Sie damit Ihre virtuellen Azure-Computer schützen. Wenn Sie noch kein Kunde sind, können Sie sich für ein Probeabonnement registrieren. Weitere Informationen zu dieser Lösung finden Sie in dem Blogbeitrag zur [Microsoft Azure VM Agent-Erweiterung für Deep Security][Microsoft Azure VM Agent-Erweiterung für Deep Security].

## Installieren des Deep Security Agent auf einem neuen virtuellen Computer

Über das [Azure-Verwaltungsportal][Azure-Verwaltungsportal] können Sie den VM Agent und die Sicherheitserweiterung von Trend installieren, wenn Sie die Option **Aus Katalog** verwenden, um den virtuellen Computer zu erstellen. Auf diese Weise können Sie den Schutz von Trend einfach hinzufügen, wenn Sie einen einzelnen virtuellen Computer erstellen.

Die Option **Aus Katalog** öffnet ein Assistenten, der Sie beim Einrichten des virtuellen Computers unterstützt. Sie verwenden die letzte Seite des Assistenten, um den VM Agent und die Sicherheitserweiterung von Trend zu installieren. Allgemeine Anweisungen finden Sie unter [Erstellen eines virtuellen Windows Server-Computers][Erstellen eines virtuellen Windows Server-Computers]. Gehen Sie wie folgt vor, wenn Sie zur letzten Seite des Assistenten gelangen:

1.  Aktivieren Sie unter VM Agent die Option **VM Agent installieren**.

2.  Aktivieren Sie unter den Sicherheitserweiterungen die Option **Trend Micro Deep Security Agent**.

3.  Aktivieren Sie das Kontrollkästchen, um den virtuellen Computer zu erstellen.

    ![Installieren von VM Agent und Deep Security Agent][Installieren von VM Agent und Deep Security Agent]

## Installieren des Deep Security Agent auf einem vorhandenen virtuellen Computer

Dazu benötigen Sie Folgendes:

-   Das Azure PowerShell-Modul, Version 0.8.2 oder höher. Anweisungen und einen Link zur neuesten Version finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].

-   Den VM Agent. Anweisungen und einen Link zum Download finden Sie im Blogbeitrag zum [VM Agent und Erweiterungen – Teil 2][VM Agent und Erweiterungen – Teil 2].

Öffnen Sie eine Azure PowerShell-Sitzung, und führen Sie die folgenden Befehle aus. Achten Sie darauf, dass Sie Ihre eigenen Werte für die Platzhalter wie „MyServiceName“ einsetzen.

1.  Rufen Sie den Clouddienstnamen, den Namen des virtuellen Computers ab, und speichern Sie jeden dieser Werte in Variablen, damit diese in den nächsten Befehlen verwendet werden können:

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] Wenn Ihnen die Namen für den Clouddienst und den virtuellen Computer nicht bekannt sind, führen Sie „Get-AzureVM“ aus, um diese Informationen für alle virtuellen Computer des aktuellen Abonnements anzuzeigen.

2.  Fügen Sie den Deep Security Agent zum virtuellen Computer hinzu:

    `Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA -VM $vm.VM`

    > [WACOM.NOTE] Wenn Sie eine bestimmte Version installieren möchten, führen Sie den folgenden Befehl aus, um eine Liste der verfügbaren Versionen abzurufen: `Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA`. Dann beziehen Sie den Versionsparameter mit ein, wenn Sie „Set-AzureVMExtension“ ausführen.

3.  Aktualisieren Sie den virtuellen Computer, der den Deep Security Agent installiert:

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## Nächste Schritte

Nachdem der Agent installiert wurde, dauert es einige Minuten, bis dieser gestartet ist. Anschließend müssen Sie Deep Security auf dem virtuellen Computer aktivieren, damit es vom Deep Security Manager verwaltet werden kann. Weitere Informationen finden Sie unter:

-   Artikel von Trend zu dieser Lösung, [Sofort aktive Cloudsicherheit für Microsoft Azure][Sofort aktive Cloudsicherheit für Microsoft Azure].
-   Ein [Windows PowerShell-Beispielskript][Windows PowerShell-Beispielskript] zum Konfigurieren des virtuellen Computers.
-   [Anweisungen][Anweisungen] zum Beispiel.

## Zusätzliche Ressourcen

[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]

[Verwalten von Erweiterungen][Verwalten von Erweiterungen]

<!--Link references-->

  [Microsoft Azure VM Agent-Erweiterung für Deep Security]: http://go.microsoft.com/fwlink/p/?LinkId=403945
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Erstellen eines virtuellen Windows Server-Computers]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [Installieren von VM Agent und Deep Security Agent]: ./media/virtual-machines-install-trend/InstallVMAgentandTrend.png
  [Installieren und Konfigurieren von Azure PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [VM Agent und Erweiterungen – Teil 2]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [Sofort aktive Cloudsicherheit für Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=404101
  [Windows PowerShell-Beispielskript]: http://go.microsoft.com/fwlink/?LinkId=404100
  [Anweisungen]: http://go.microsoft.com/fwlink/?LinkId=404099
  [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: ../virtual-machines-log-on-windows-server/
  [Verwalten von Erweiterungen]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
