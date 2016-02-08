<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="cynthn" solutions="" manager="timlt" editor="tysonn" />

4. Nach Klicken auf **Verbinden** wird eine Remotedesktopprotokolldatei (RDP-Datei) erstellt und heruntergeladen. Klicken Sie auf **Öffnen**, damit diese Datei verwendet wird.

5. Klicken Sie im Fenster "Remotedesktop" auf **Verbinden**, um den Vorgang fortzusetzen.

	![Stellen Sie eine Verbindung her.](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Geben Sie im Fenster "Windows-Sicherheit" den Benutzernamen und das Kennwort eines Kontos auf dem virtuellen Computer ein, und klicken Sie anschließend auf **OK**.

 	In den meisten Fällen sind die Anmeldeinformationen der Benutzername und das Kennwort des lokalen Kontos, das Sie beim Erstellen des virtuellen Computers angegeben haben. In diesem Fall ist die Domäne der Name des virtuellen Computers. Das Eingabeformat lautet *VM-Name*&#92;*Benutzername*.
	
	Wenn der virtuelle Computer zu einer Domäne in Ihrer Organisation gehört, stellen Sie sicher, dass der Benutzername den Namen der Domäne im Format *Domäne*&#92;*Benutzername* enthält. Das Konto muss außerdem in der Administratorgruppe sein oder über Remotezugriffsrechte auf den virtuellen Computer verfügen.
	
	Wenn der virtuelle Computer ein Domänencontroller ist, geben Sie den Benutzernamen und das Kennwort eines Domänenadministratorkontos für diese Domäne an.

7.	Klicken Sie auf **Ja**, um die Identität des virtuellen Computers zu bestätigen und das Anmelden zu beenden.

	![Bestätigen Sie die Identität des Computers.](./media/virtual-machines-log-on-win-server/connectverify.png)

<!---HONumber=AcomDC_0128_2016-->