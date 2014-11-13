<properties urlDisplayName="Log on to a VM" pageTitle="Anmelden an einem virtuellen Computer unter Windows Server" metaKeywords="Azure logging on vm, vm portal" description="Erfahren Sie, wie Sie das Azure-Verwaltungsportal verwenden, um sich an einem virtuellen Computer unter Windows Server anzumelden." metaCanonical="" services="virtual-machines" documentationCenter="" title="Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgef&uuml;hrt wird" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="08/05/2014" ms.author="kathydav" />

# Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird

Verwenden Sie bei einem virtuellen Computer, auf dem das Betriebssystem Windows Server ausgeführt wird, die Schaltfläche „Verbinden“ im Verwaltungsportal, um eine Remotedesktopverbindung zu starten.

> [WACOM.NOTE] Wenn Sie den Benutzernamen oder das Kennwort zurücksetzen oder RDP auf dem virtuellen Computer aktivieren müssen, können Sie dazu die Erweiterung [VMAccess][VMAccess] verwenden. Tipps zu den Voraussetzungen und zur Problembehandlung finden Sie unter [Verbinden mit einem virtuellen Azure-Computer mit RDP oder SSH][Verbinden mit einem virtuellen Azure-Computer mit RDP oder SSH].

1.  Melden Sie sich am [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, falls noch nicht geschehen.

2.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den betreffenden virtuellen Computer aus.

3.  Klicken Sie in der Befehlsleiste auf **Verbinden**.

    ![Melden Sie sich beim virtuellen Computer an.][Melden Sie sich beim virtuellen Computer an.]

4.  Klicken Sie auf **Öffnen**, um die Remotedesktop-Protokolldatei zu verwenden, die automatisch für den virtuellen Computer erstellt wurde.

5.  Klicken Sie auf **Verbinden**, um den Vorgang fortzusetzen.

    ![Stellen Sie eine Verbindung her.][Stellen Sie eine Verbindung her.]

6.  Geben Sie den Benutzernamen und das Kennwort des Administratorkontos auf dem virtuellen Computer ein, und klicken Sie anschließend auf **OK**. Das ist der Benutzername und das Kennwort, die Sie bei der Erstellung des virtuellen Computers angegeben haben, sofern der virtuelle Computer jetzt kein Domänencontroller ist. Geben Sie in diesem Fall den Benutzernamen und das Kennwort eines Domänenadministratorkontos für die Domäne ein.

7.  Klicken Sie auf **Ja**, um die Identität des virtuellen Computers zu bestätigen.

    ![Bestätigen Sie die Identität des Computers.][Bestätigen Sie die Identität des Computers.]

    Sie können jetzt mit dem virtuellen Computer wie mit jedem anderen Server arbeiten.

  [VMAccess]: http://go.microsoft.com/fwlink/p/?LinkId=396856
  [Verbinden mit einem virtuellen Azure-Computer mit RDP oder SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Melden Sie sich beim virtuellen Computer an.]: ./media/virtual-machines-log-on-windows-server/connectwindows.png
  [Stellen Sie eine Verbindung her.]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [Bestätigen Sie die Identität des Computers.]: ./media/virtual-machines-log-on-windows-server/connectverify.png
