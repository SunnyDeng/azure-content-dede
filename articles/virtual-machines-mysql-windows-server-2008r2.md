<properties urlDisplayName="Install MySQL" pageTitle="Erstellen eines virtuellen Computers unter MySQL in Azure " metaKeywords="Azure virtual machines, Azure Windows Server, Azure installing MySQL, Azure configuring MySQL, Azure databases" description="Create an Azure virtual machine running Windows Server 2008 R2, and then install and configure a MySQL database on the virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running Windows Server 2008 R2 in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="12/15/2013" ms.author="kathydav" />


#Installieren von MySQL auf einem virtuellen Computer mit Windows Server 2008 R2 in Azure

[MySQL](http://www.mysql.com) ist eine beliebte Open-Source-SQL-Datenbank. Mit dem [Azure-Verwaltungsportal][AzurePreviewPortal], können Sie über die Bildergalerie einen virtuellen Computer mit Windows Server 2008 R2 erstellen.  Anschließend können Sie auf dem virtuellen Computer eine MySQL-Datenbank installieren und konfigurieren.

In diesem Lernprogramm lernen Sie Folgendes:

- Verwenden des Verwaltungsportals zum Erstellen eines virtuellen Computers mit Windows Server 2008 R2

- Installieren und Ausführen von MySQL Community Server auf dem virtuellen Computer

##Erstellen eines virtuellen Computers mit Windows Server 2008 R2

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

##Datenträger anfügen

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

##Installieren und Ausführen von MySQL Community Server auf dem virtuellen Computer
Gehen Sie wie folgt vor, um MySQL Community Server zu installieren, konfigurieren und auszuführen:

1. Wenn Sie über Remotedesktop eine Verbindung zum virtuellen Computer hergestellt haben, öffnen Sie im Menü **Start** den **Internet Explorer**. 

2. Klicken Sie in der oberen rechten Ecke auf **Extras**. Öffnen Sie unter **Internetoptionen** die Registerkarte **Sicherheit**, wählen Sie anschließend das Symbol **Vertrauenswürdige Sites**, und klicken Sie anschließend auf die Schaltfläche **Sites**. Fügen Sie der Liste vertrauenswürdiger Websites *http://\*.mysql.com* hinzu.

3. Wechseln Sie zu [Download MySQL Community Server][MySQLDownloads] (MySQL Community Server herunterladen, in englischer Sprache).

4. Wählen Sie im Dropdownmenü **Platform** die Option **Microsoft Windows**, und klicken Sie auf **Select**.

5. Suchen Sie die neueste Version von **Windows (x86, 64-bit), MSI Installer**, und klicken Sie auf **Download**. 

6. Wählen Sie **No thanks, just start my download!** (oder registrieren Sie sich für ein Konto).  Wenn Sie dazu aufgefordert werden, wählen Sie eine gespiegelte Site zum Herunterladen des MySQL-Installers aus, und speichern Sie den Installer auf dem Desktop.

7. Doppelklicken Sie auf die Installerdatei auf dem Desktop, um mit der Installation zu beginnen.

8. Klicken Sie auf **Weiter**.

	![MySQL Setup][MySQLInstall1]

9. Akzeptieren Sie die Lizenzbedingungen, und klicken Sie auf **Next**.

	![MySQL Setup][MySQLInstall2]

10. Klicken Sie auf **Typical**, um häufig verwendete Features zu installieren.

	![MySQL Setup][MySQLInstall3]

11. Klicken Sie auf **Installieren**.

	![MySQL Setup][MySQLInstall4]

12. Starten Sie den MySQL-Konfigurationsassistenten, und klicken Sie auf **Next**.

	![Configure MySQL][MySQLConfig1]

13. Wählen Sie **Detailed Configuration**, und klicken Sie auf "Next".

	![Configure MySQL][MySQLConfig2]

14. Wählen Sie **Server Machine**, wenn Sie MySQL zusammen mit anderen Anwendungen auf dem Server ausführen möchten, oder wählen Sie die für Ihre Zwecke am besten geeignete Option.  Klicken Sie auf **Weiter**.

	![Configure MySQL][MySQLConfig3]

15. Wählen Sie **Multifunctional Database**, oder die für Ihre Zwecke am besten geeignete Option.  Klicken Sie auf **Next**.

	![Configure MySQL][MySQLConfig4]

16. Wählen Sie das Datenlaufwerk aus, das Sie in den Schritten oben angeschlossen haben.

	![Configure MySQL][MySQLConfig5]

17. Wählen Sie **Decision Support (DSS)/OLAP**, oder die für Ihre Zwecke am besten geeignete Option.  Klicken Sie auf **Weiter**.

	![Configure MySQL][MySQLConfig6]

18. Wählen Sie **Enable TCP/IP Networking** und **Add firewall exception for this port**. (Hierdurch wird eine eingehende Regel in der Windows-Firewall mit dem Namen **MySQL Server**erstellt).

	![Configure MySQL][MySQLConfig7]

19. Wählen Sie **Best Support For Multilingualism**, wenn Sie Text in vielen verschiedenen Sprachen speichern müssen, oder wählen Sie die für Ihre Zwecke am besten geeignete Option.  Klicken Sie auf **Weiter**.

	![Configure MySQL][MySQLConfig8]

20. Wählen Sie **Install As Windows Service** und **Launch the MySQL Server automatically**.  Wählen Sie außerdem **Include Bin Directory in Windows PATH**. Klicken Sie auf **Weiter**.

	![Configure MySQL][MySQLConfig9]

21. Geben Sie das Stammkennwort ein. Aktivieren Sie nicht das Kontrollkästchen **Enable root access from remote machines** oder **Create An Anonymous Account**.  Klicken Sie auf **Weiter**.

	![Configure MySQL][MySQLConfig10]

22. Klicken Sie auf **Execute**, und warten Sie, bis die Konfiguration abgeschlossen ist.

	![Configure MySQL][MySQLConfig11]

23. Klicken Sie auf **Fertig stellen**.

	![Configure MySQL][MySQLConfig12]

24. Klicken Sie auf **Start**, und wählen Sie **MySQL 5.x Command Line Client**, um den Befehlszeilenclient zu starten.

25.  Geben Sie das Stammkennwort an der Eingabeaufforderung ein, das Sie im vorherigen Schritt geändert haben. Daraufhin wird eine Eingabeaufforderung angezeigt, über die Sie SQL-Anweisungen starten können, um mit der Datenbank zu interagieren.

26. Führen Sie zum Erstellen eines neuen MySQL-Benutzers bei der **mysql>**-Eingabeaufforderung den folgenden Befehl aus:

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Beachten Sie das Semikolon (;) am Ende der Zeilen, die entscheidend für den Abschluss der Befehle sind.

27. Starten Sie die folgenden Befehle, um eine Datenbank zu erstellen und um dafür die `mysqluser`-Benutzerberechtigungen zu gewähren:

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Beachten Sie, dass die Benutzernamen und Kennwörter der Datenbank nur von Skripten verwendet werden, die eine Verbindung zur Datenbank herstellen.  Benutzernamen für Datenbankkonten sind nicht notwendigerweise tatsächliche Benutzerkonten auf dem Computer.

28. Führen Sie den folgenden Befehl aus, um sich von einem anderen Computer aus anzumelden:

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	wobei `ip-address` die IP-Adresse des Computers ist, von dem aus Sie eine Verbindung mit MySQL herstellen möchten.
	
29. Um den MySQL-Befehlszeilenclient zu beenden, führen Sie den folgenden Befehl aus:

		quit

30. Nachdem MySQL installiert wurde, müssen Sie einen Endpunkt konfigurieren, damit ein Remotezugriff auf MySQL möglich ist. Melden Sie sich beim [Azure Management Portal][Azure-Verwaltungsportal] an. Klicken Sie im Azure-Portal auf **Virtuelle Computer** und dann auf den Namen des neuen virtuellen Computers, dann auf **Endpunkte** und dann auf   **Endpunkt hinzufügen**.

	![Endpoints][AddEndPoint]

31. Wählen Sie **Endpunkt hinzufügen**, und klicken Sie zum Fortfahren auf den Pfeil.
	
	![Endpoints][AddEndPoint2]

32. Fügen Sie einen Endpunkt mit dem Namen "MySQL" hinzu, und wählen Sie als Protokoll **TCP** aus. Setzen Sie die beiden Ports **Öffentlich** und **Privat** auf "3306". Aktivieren Sie das Kontrollkästchen. Dadurch ist ein Remotezugriff auf MySQL möglich.
	
	![Endpoints][AddEndPoint3]

33. Sie können in Azure eine Remoteverbindung zu MySQL herstellen, das auf dem virtuellen Computer ausgeführt wird.  Führen Sie auf einem lokalen Computer, auf dem MySQL ausgeführt wird, den folgenden Befehl aus, um sich als **mysqluser**-Benutzer, den Sie in den Schritten weiter oben erstellt haben, anzumelden:

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	Wenn Sie den in diesem Lernprogramm erstellten virtuellen Computer verwenden, lautet der Befehl folgendermaßen:

		mysql -u mysqluser -p -h testwinvm.cloudapp.net

##Zusammenfassung

In diesem Lernprogramm haben Sie erfahren, wie Sie einen virtuellen Computer mit Windows 2008 R2 erstellen und eine Remoteverbindung dazu herstellen. Sie haben außerdem gelernt, wie Sie MySQL auf dem virtuellen Computer installieren und konfigurieren, eine Datenbank erstellen und einen neuen MySQL-Benutzer anlegen. Weitere Informationen zu MySQL finden Sie in der [MySQL-Dokumentation](http://dev.mysql.com/doc/).

[AzurePreviewPortal]: http://manage.windowsazure.com
[MySQLDownloads]: http://www.mysql.com/downloads/mysql/


[MySQLInstall1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall1.png
[MySQLInstall2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall2.png
[MySQLInstall3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall3.png
[MySQLInstall4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall4.png
[MySQLConfig1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig1.png
[MySQLConfig2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig2.png
[MySQLConfig3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig3.png
[MySQLConfig4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig4.png
[MySQLConfig5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
[MySQLConfig6]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig6.png
[MySQLConfig7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
[MySQLConfig8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
[MySQLConfig9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
[MySQLConfig10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png
[MySQLConfig11]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig11.png
[MySQLConfig12]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig12.png
[AddEndPoint]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL0.png
[AddEndPoint2]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL1.png
[AddEndPoint3]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL.png
