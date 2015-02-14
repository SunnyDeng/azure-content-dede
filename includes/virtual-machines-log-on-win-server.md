<properties services="virtual-machines" title="Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird" authors="KBDAzure" solutions="" manager="timlt" editor="tysonn" />

>[AZURE.NOTE] Wenn Sie den Benutzernamen oder das Kennwort zurücksetzen oder RDP auf dem virtuellen Computer aktivieren müssen, können Sie dazu die Erweiterung [VMAccess](http://go.microsoft.com/fwlink/p/?LinkId=396856) verwenden. Anforderungen und Tipps zur Problembehandlung bei der Anmeldung finden Sie unter [Verbindung mit einem virtuellen Azure-Computer mit RDP oder SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294).

1. Melden Sie sich auf dem [Azure-Verwaltungsportal](http://manage.windowsazure.com) an, falls noch nicht geschehen.

2. Klicken Sie auf **Virtuelle Computer** und wählen Sie dann den betreffenden virtuellen Computer aus.

3. Klicken Sie in der Befehlsleiste auf **Verbinden**.

	![Log on to the virtual machine](./media/virtual-machines-log-on-win-server/connectwindows.png)

4. Klicken Sie auf **Öffnen**, um die Remotedesktop-Protokolldatei zu verwenden, die automatisch für den virtuellen Computer erstellt wurde.
	
5. Klicken Sie auf **Verbinden**, um den Vorgang fortzusetzen.

	![Continue with connecting](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Geben Sie den Benutzernamen und das Kennwort des Administratorkontos auf dem virtuellen Computer ein, und klicken Sie anschließend auf **OK**. 

 >[AZURE.TIP] In den meisten Fällen verwenden Sie den Benutzernamen und das Kennwort, die angegeben wurden, als der virtuelle Computer erstellt wurde. Überprüfen Sie den Benutzernamen, um sicherzustellen, dass die korrekten Domäneninformationen verfügbar sind:

>- Wenn der virtuelle Computer zu einer Domäne in Ihrer Organisation gehört, stellen Sie sicher, dass der Benutzername den Namen der Domäne enthält.
- Wenn der virtuelle Computer keiner Domäne angehört, entfernen Sie entweder alle Domäneninformationen durch Starten der Zeile mit '\' oder verwenden den VM-Namen als Namen der Domäne. Beispiel: `\MyUserName` oder `MyTestVM\MyUserName`. 
- Wenn der virtuelle Computer ein Domänencontroller ist, geben Sie den Benutzernamen und das Kennwort eines Domänenadministratorkontos für diese Domäne an.

Klicken Sie auf **Ja**, um die Identität des virtuellen Computers zu bestätigen.

![Verify the identity of the machine](./media/virtual-machines-log-on-win-server/connectverify.png)

Sie können jetzt über eine Remoteverbindung mit dem virtuellen Computer arbeiten.


<!--HONumber=42-->
