<properties pageTitle="Anmelden an einem virtuellen Computer unter Linux in Azure" description="Erfahren Sie, wie Sie sich an einem virtuellen Azure-Computer unter Linux mithilfe eines SSH-Clients (Secure Shell) anmelden." services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="kathydav"/>




#Anmelden bei einem mit Linux betriebenen virtuellen Computer #

Bei einem virtuellen Computer, auf dem das Betriebssystem Linux ausgeführt wird, verwenden Sie zur Anmeldung einen Secure Shell (SSH)-Client.

Sie müssen einen SSH-Client auf dem Computer installieren, über den Sie sich beim virtuellen Computer anmelden möchten. Es gibt eine Vielzahl an SSH-Client-Programmen, die Sie verwenden können. Hier einige mögliche Programme:

- Auf einem Computer, auf dem ein Windows-Betriebssystem ausgeführt wird, sollten Sie einen SSH-Client wie PuTTY verwenden. Weitere Informationen erhalten Sie unter [PuTTY Download Page](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) (in englischer Sprache).
- Auf einem Computer, auf dem ein Linux-Betriebssystem ausgeführt wird, sollten Sie einen SSH-Client wie OpenSSH verwenden. Weitere Informationen finden Sie unter [OpenSSH](http://www.openssh.org/).

>[AZURE.NOTE] Weitere Tipps zu den Voraussetzungen und zur Problembehandlung finden Sie unter [Verbinden mit einem virtuellen Azure-Computer mit RDP oder SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294) 

Bei diesem Verfahren wird aufgezeigt, wie Sie mithilfe des PuTTY-Programms auf den virtuellen Computer zugreifen.

1. Navigieren Sie im [Verwaltungsportal](http://manage.windowsazure.com) zum **Hostnamen** und den **Portinformationen**. Sie finden die erforderlichen Informationen im Dashboard des virtuellen Computers. Klicken Sie in der **Schnelleinsicht** des Dashboards auf den Namen des virtuellen Computers, und navigieren Sie zu den **SSH-Details**.

	![Obtain SSH details](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2. Öffnen Sie das PuTTY-Programm.

3. Geben Sie den Hostnamen und die Portinformationen aus dem Dashboard ein, und klicken Sie dann auf **Öffnen**

	![Open PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4. Melden Sie sich mit dem Konto an, das Sie beim Erstellen des virtuellen Computers angegeben haben. Nähere Informationen zum Erstellen eines virtuellen Computers mit Benutzername und Kennwort finden Sie unter [Erstellen eines virtuellen Linux-Computers](http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-linux-tutorial/). 

	![Log on to the virtual machine](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

>[AZURE.NOTE] Mit der VMAccess-Erweiterung können Sie den SSH-Schlüssel oder das SSH-Kennwort zurücksetzen, falls Sie diese vergessen sollten. Falls Sie den Benutzernamen vergessen, können Sie die Erweiterung verwenden, um einen neuen Benutzernamen mit sudo-Autorität zu erstellen. Eine Anleitung finden Sie unter [Zurücksetzen eines Kennworts oder einer SSH für virtuelle Linux-Computer]. 
	
Sie können jetzt mit dem virtuellen Computer wie mit jedem anderen Server arbeiten.

<!-- LINKS -->
[Zurücksetzen von Kennwort oder SSH für virtuelle Linux-Computer]: http://go.microsoft.com/fwlink/p/?LinkId=512138




<!--HONumber=42-->
