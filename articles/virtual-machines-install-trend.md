<properties title="Gewusst wie: Installieren und Konfigurieren von Trend auf einem virtuellen Azure-Computer" pageTitle="Gewusst wie: Installieren und Konfigurieren von Trend Micro Deep Security-as-a-Service auf einem virtuellen Azure-Computer" description="Beschreibt die Installation und Konfiguration von der Trend Micro-Sicherheitsl&ouml;sung auf einem virtuellen Computer in Azure." metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Gewusst wie: Installieren und Konfigurieren von Trend Micro Deep Security-as-a-Service auf einem virtuellen Azure-Computer

Dieser Artikel zeigt Ihnen das Installieren und Konfigurieren von Trend Micro Deep Security als einen Dienst auf einem neuen oder vorhandenen virtuellen Computer unter Windows Server. Der durch Deep Security-als-ein-Dienst bereitgestellte Schutz umfasst den Antischadsoftwareschutz, die Firewall, das Eindringschutzsystem und die Integritätsüberwachung.

Der Client wird als eine Sicherheitserweiterung über den VM-Agent installiert. Auf einem neuen virtuellen Computer installieren Sie den VM-Agent zusammen mit Deep Security Agent. Auf einem vorhandenen virtuellen Computer, der nicht über den VM-Agent verfügt, müssen Sie diesen zunächst herunterladen und installieren. Dieser Artikel deckt beide Situationen ab.

Wenn Sie über ein vorhandenes Abonnement von Trend für eine lokale Lösung verfügen, können Sie es zum Schützen Ihrer virtuellen Azure-Computer verwenden. Wenn Sie noch kein Kunde sind, können Sie sich für ein Testabonnement registrieren. Weitere Informationen über diese Lösung finden Sie im Blogbeitrag [Microsoft Azure VM Agent Extension For Deep Security][Microsoft Azure VM Agent Extension For Deep Security] (Microsoft Azure-VM-Agent-Erweiterung für Deep Security) (in englischer Sprache).

## Installieren von Deep Security Agent auf einem neuen virtuellen Computer

Mit dem [Azure-Verwaltungsportal][Azure-Verwaltungsportal] können Sie den VM-Agent und die Trend-Sicherheitserweiterung installieren, wenn Sie die Option **Aus Katalog** zum Erstellen des virtuellen Computers verwenden. Die Verwendung dieses Ansatzes ist eine einfache Möglichkeit, einen Schutz von Trend hinzuzufügen, wenn Sie einen einzelnen virtuellen Computer erstellen.

Die Option **Aus Katalog** öffnet einen Assistenten, der Ihnen beim Einrichten des virtuellen Computers hilft. Sie verwenden die letzte Seite des Assistenten zum Installieren des VM-Agents und der Trend-Sicherheitserweiterung. Allgemeine Anweisungen dazu finden Sie unter [Erstellen eines virtuellen Windows Server-Computers][Erstellen eines virtuellen Windows Server-Computers]. Führen Sie auf der letzten Seite des Assistenten Folgendes aus:

1.  Aktivieren Sie **VM-Agent installieren** unter „VM-Agent“.

2.  Aktivieren Sie **Trend Micro Deep Security Agent** unter „Sicherheitserweiterungen“.

3.  Aktivieren Sie das Kontrollkästchen, um den virtuellen Computer zu erstellen.

    ![Installieren des VM-Agents und von Deep Security Agent][Installieren des VM-Agents und von Deep Security Agent]

## Installieren von Deep Security Agent auf einem vorhandenen virtuellen Computer

Dafür benötigen Sie Folgendes:

-   Das Azure PowerShell-Modul Version 0.8.2 oder neuer. Anweisungen und einen Link zur neuesten Version finden Sie unter [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell][Gewusst wie: Installieren und Konfigurieren von Azure PowerShell].

-   Der VM-Agent. Anweisungen und einen Downloadlink finden Sie im Blogbeitrag [VM Agent and Extensions - Part 2][VM Agent and Extensions - Part 2] (VM-Agent und Erweiterungen – Teil 2) (in englischer Sprache).

Öffnen Sie eine Azure PowerShell-Sitzung, und führen Sie die folgenden Befehle aus. Ersetzen Sie die Platzhalter wie MyServiceName durch Ihre eigenen Werte.

1.  Rufen Sie den Namen des Cloud-Diensts, den Namen des virtuellen Computers und den virtuellen Computer ab, und speichern Sie die eben genannten in Variablen, sodass sie durch die nächsten Befehle verwendet werden können:

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] Wenn Sie den Namen des Cloud-Diensts und des virtuellen Computers nicht kennen, führen Sie „Get-AzureVM“ aus, um diese Informationen für alle virtuellen Computer im aktuellen Abonnement anzuzeigen.

2.  Fügen Sie dem virtuellen Computer den Deep Security Agent hinzu:

    `Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA -VM $vm.VM`

    > [WACOM.NOTE] Wenn Sie eine bestimmte Version installieren möchten, führen Sie den folgenden Befehl aus, um eine Liste der verfügbaren Versionen abzurufen. `Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA`. Nehmen Sie anschließend den Parameter „Version“ beim Ausführen von „Set-AzureVMExtension“ auf.

3.  Aktualisieren Sie den virtuellen Computer, wodurch Deep Security Agent installiert wird:

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## Nächste Schritte

Nach der Installation des Agents nimmt der Start der Ausführung ein paar Minuten in Anspruch. Danach müssen Sie Deep Security auf dem virtuellen Computer aktivieren, sodass es durch einen Deep Security Manager verwaltet werden kann. Siehe Folgendes:

-   Der Artikel von Trend in Bezug auf diese Lösung [Instant-On Cloud Security for Microsoft Azure][Instant-On Cloud Security for Microsoft Azure] (Instant-On Cloud Security für Microsoft Azure) (in englischer Sprache).
-   Ein [Windows PowerShell-Beispielskript][Windows PowerShell-Beispielskript] zum Konfigurieren des virtuellen Computers.
-   [Anweisungen][Anweisungen] für das Beispiel.

## Zusätzliche Ressourcen

[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]

[Verwalten von Erweiterungen][Verwalten von Erweiterungen]

<!--Link references-->

  [Microsoft Azure VM Agent Extension For Deep Security]: http://go.microsoft.com/fwlink/p/?LinkId=403945
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Erstellen eines virtuellen Windows Server-Computers]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [Installieren des VM-Agents und von Deep Security Agent]: ./media/virtual-machines-install-trend/InstallVMAgentandTrend.png
  [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [VM Agent and Extensions - Part 2]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [Instant-On Cloud Security for Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=404101
  [Windows PowerShell-Beispielskript]: http://go.microsoft.com/fwlink/?LinkId=404100
  [Anweisungen]: http://go.microsoft.com/fwlink/?LinkId=404099
  [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: ../virtual-machines-log-on-windows-server/
  [Verwalten von Erweiterungen]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
