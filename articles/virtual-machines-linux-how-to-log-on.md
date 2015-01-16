<properties urlDisplayName="Log on to a VM" pageTitle="Anmelden an einem virtuellen Computer unter Linux in Azure" metaKeywords="Azure Linux vm, Linux SSH" description="Erfahren Sie, wie Sie sich an einem virtuellen Azure-Computer unter Linux mithilfe eines SSH-Clients (Secure Shell) anmelden." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="kathydav" />




#Anmelden bei einem mit Linux betriebenen virtuellen Computer #

Bei einem virtuellen Computer, auf dem das Betriebssystem Linux ausgeführt wird, verwenden Sie zur Anmeldung einen Secure Shell (SSH)-Client.

Sie müssen einen SSH-Client auf dem Computer installieren, über den Sie sich beim virtuellen Computer anmelden möchten. Es gibt eine Vielzahl an SSH-Client-Programmen, die Sie verwenden können. Hier einige mögliche Programme:

- Auf einem Computer, auf dem ein Windows-Betriebssystem ausgeführt wird, sollten Sie einen SSH-Client wie PuTTY verwenden. Weitere Informationen erhalten Sie unter [PuTTY Download Page] (in englischer Sprache)(http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
- Auf einem Computer, auf dem ein Linux-Betriebssystem ausgeführt wird, sollten Sie einen SSH-Client wie OpenSSH verwenden. Weitere Informationen finden Sie unter [OpenSSH](http://www.openssh.org/).

>[WACOM.NOTE] Weitere Tipps zu den Voraussetzungen und zur Problembehandlung finden Sie unter [Verbinden mit einem virtuellen Azure-Computer mit RDP oder SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294). 

Bei diesem Verfahren wird aufgezeigt, wie Sie mithilfe des PuTTY-Programms auf den virtuellen Computer zugreifen.

1. Navigieren Sie zum **Hostnamen** und den **Portinformationen** im [Verwaltungsportal](http://manage.windowsazure.com). Sie finden die erforderlichen Informationen im Dashboard des virtuellen Computers. Klicken Sie auf den Namen des virtuellen Computers und navigieren Sie zu den **SSH-Details** in der**Schnelleinsicht** des Dashboards.

	![Obtain SSH details](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2. Öffnen Sie das PuTTY-Programm.

3. Geben Sie den Hostnamen und die Portinformationen aus dem Dashboard ein, und klicken Sie dann auf **Öffnen**.

	![Open PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4. Melden Sie sich mit dem Konto an, das Sie beim Erstellen des virtuellen Computers angegeben haben. Der Benutzername lautet standardmäßig "azureuser".

	![Log on to the virtual machine](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

>[WACOM.NOTE] Mit der VMAccess-Erweiterung können Sie den SSH-Schlüssel oder das SSH-Kennwort zurücksetzen, falls Sie diese vergessen sollten. Falls Sie den Benutzernamen vergessen, können Sie die Erweiterung verwenden, um einen neuen Benutzernamen mit sudo-Autorität zu erstellen. Eine Anleitung finden Sie unter [Zurücksetzen von Kennwort oder SSH für virtuelle Linux-Computer]. 
	
Sie können jetzt mit dem virtuellen Computer wie mit jedem anderen Server arbeiten.

<!-- LINKS -->
[Zurücksetzen von Kennwort oder SSH für virtuelle Linux-Computer]: http://go.microsoft.com/fwlink/p/?LinkId=512138

<!--HONumber=35.1-->
