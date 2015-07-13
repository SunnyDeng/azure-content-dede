1. Melden Sie sich auf dem [Azure-Verwaltungsportal ](http://manage.windowsazure.com) an, falls Sie dies noch nicht getan haben.

2. Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den betreffenden virtuellen Computer aus.

3. Klicken Sie in der Befehlsleiste auf **Verbinden**.

	![Anmelden beim virtuellen Computer](./media/virtual-machines-log-on-win-server/connectwindows.png)

4. Klicken Sie auf **Öffnen**, um die Remotedesktop-Protokolldatei zu verwenden, die automatisch für den virtuellen Computer erstellt wurde.
	
5. Klicken Sie auf **Verbinden**, um den Vorgang fortzusetzen.

	![Stellen Sie eine Verbindung her.](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Geben Sie den Benutzernamen und das Kennwort des Administratorkontos auf dem virtuellen Computer ein, und klicken Sie anschließend auf **OK**.

 >[AZURE.TIP]In den meisten Fällen verwenden Sie den Benutzernamen und das Kennwort, die beim Erstellen des virtuellen Computers angegeben wurden. Überprüfen Sie den Benutzernamen, um sicherzustellen, dass die korrekten Domäneninformationen verfügbar sind:

>- Wenn der virtuelle Computer zu einer Domäne in Ihrer Organisation gehört, stellen Sie sicher, dass der Benutzername den Namen der Domäne enthält.
- Wenn der virtuelle Computer keiner Domäne angehört, entfernen Sie entweder alle Domäneninformationen, indem Sie die Zeile mit '' beginnen, oder verwenden Sie den VM-Namen als Domänennamen. Beispiel: `\MyUserName` oder `MyTestVM\MyUserName`. 
- Wenn der virtuelle Computer ein Domänencontroller ist, geben Sie den Benutzernamen und das Kennwort eines Domänenadministratorkontos für diese Domäne an.

Klicken Sie auf **Ja**, um die Identität des virtuellen Computers zu bestätigen.

![Bestätigen Sie die Identität des Computers.](./media/virtual-machines-log-on-win-server/connectverify.png)

Sie können jetzt über eine Remoteverbindung mit dem virtuellen Computer arbeiten.

<!---HONumber=62-->