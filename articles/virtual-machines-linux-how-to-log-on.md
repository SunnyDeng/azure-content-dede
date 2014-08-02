<properties linkid="manage-linux-howto-logon-linux-vm" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Linux in Azure" metaKeywords="Azure Linux vm, Linux SSH" description="Learn how to log on to an Azure virtual machine running Linux by using a Secure Shell (SSH) client." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Linux" authors="" solutions="" manager="" editor="" />

Anmelden bei einem mit Linux betriebenen virtuellen Computer
============================================================

Bei einem virtuellen Computer, der mit dem Linux-Betriebssystem betrieben wird, verwenden Sie zur Anmeldung einen Secure Shell (SSH)-Client.

Sie müssen einen SSH-Client auf dem Computer installieren, den Sie zum Anmelden an einem virtuellen Computer verwenden möchten. Es gibt eine Vielzahl an SSH-Client-Programmen, die Sie verwenden können. Hier einige mögliche Programme:

-   Wenn Sie einen Computer mit einem Windows-Betriebssystem verwenden, können Sie beispielsweise einen SSH-Client wie PuTTY verwenden. Weitere Informationen erhalten Sie unter [PuTTY Download Page](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) (in englischer Sprache).
-   Wenn Sie einen Computer mit einem Linux-Betriebssystem verwenden, können Sie beispielsweise einen SSH-Client wie OpenSSH verwenden. Weitere Informationen finden Sie unter [OpenSSH](http://www.openssh.org/).

Bei diesem Verfahren wird aufgezeigt, wie Sie mithilfe des PuTTY-Programms auf den virtuellen Computer zugreifen.

1.  Sie können **Hostname** und **Portinformationen** über das [Verwaltungsportal](http://manage.windowsazure.com) aufrufen. Sie finden die erforderlichen Informationen im Dashboard des virtuellen Computers. Klicken Sie in der Schnelleinsicht des Dashboards auf den Namen des virtuellen Computers, und navigieren Sie zu den SSH-Details.

    ![SSH-Details abrufen](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2.  Öffnen Sie das PuTTY-Programm.

3.  Geben Sie den Hostnamen und die Portinformationen aus dem Dashboard ein, und klicken Sie dann auf **Öffnen**.

    ![PuTTY öffnen](./media/virtual-machines-linux-how-to-log-on/putty.png)

4.  Melden Sie sich mit dem Konto an, das Sie beim Erstellen des virtuellen Computers angegeben haben.

    ![Beim virtuellen Computer anmelden](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

    Sie können jetzt mit dem virtuellen Computer wie mit jedem anderen Server arbeiten.


