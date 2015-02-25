<properties pageTitle="Erstellen eines virtuellen Computers unter MySQL in Azure" description="Erstellen Sie einen virtuellen Azure-Computer unter Windows Server 2008 R2, und installieren und konfigurieren Sie dann eine MySQL-Datenbank auf dem virtuellen Computer." services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor="tysonn"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="kathydav"/>


#Installieren von MySQL auf einem virtuellen Computer mit Windows Server 2008 R2 in Azure

[MySQL](http://www.mysql.com) ist eine beliebte Open-Source-SQL-Datenbank. Mit dem [Azure-Verwaltungsportal][AzurePreviewPortal] können Sie über den Image-Katalog einen virtuellen Computer mit Windows Server 2008 R2 erstellen. Anschließend können Sie auf dem virtuellen Computer eine MySQL-Datenbank installieren und konfigurieren.

Dieses Lernprogramm beschreibt Folgendes:

- Verwenden des Verwaltungsportals zum Erstellen eines virtuellen Computers mit Windows Server 2008 R2

- Installieren und Ausführen von MySQL Community Server auf dem virtuellen Computer

##Erstellen eines virtuellen Computers unter Windows Server

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

##Datenträger anfügen

Nachdem der virtuelle Computer erstellt wurde, fügen Sie einen Datenträger an. Dieser Datenträger stellt den Datenspeicher bereit, der für die Installation von MySQL benötigt wird. Beachten Sie den Artikel [Gewusst wie: Anfügen eines Datenträgers auf einem Windows-Computer](http://azure.microsoft.com/de-de/documentation/articles/storage-windows-attach-disk/) und befolgen Sie die Anweisungen für das Anfügen eines leeren Datenträgers.

##Melden Sie sich beim virtuellen Computer an.
Melden Sie sich nun am virtuellen Computer an, sodass Sie MySQL installieren können.

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

##Installieren und Ausführen von MySQL Community Server auf dem virtuellen Computer
Gehen Sie wie folgt vor, um MySQL Community Server zu installieren, konfigurieren und auszuführen:

1. Wenn Sie über Remote Desktop eine Verbindung zum virtuellen Computer aufgebaut haben, öffnen Sie im Menü **Start** den **Internet Explorer**. 

2. Klicken Sie in der oberen rechten Ecke auf **Extras**. Öffnen Sie unter **Internetoptionen** die Registerkarte **Sicherheit**, wählen Sie anschließend das Symbol **Vertrauenswürdige Sites**, und klicken Sie anschließend auf die Schaltfläche **Sites**. Fügen Sie  *http://\*.mysql.com* zur Liste vertrauenswürdiger Sites hinzu.

3. Wechseln Sie zu [Download MySQL Community Server] (MySQL Community Server herunterladen, in englischer Sprache)[MySQLDownloads].

4. Wählen Sie im Dropdownmenü **Platform** die Option **Microsoft Windows**, und klicken Sie auf **Select**.

5. Suchen Sie die neueste Version von **Windows (x86, 64-bit), MSI Installer**, und klicken Sie auf **Download**. 

6. Wählen Sie **No thanks, just start my download!** (oder registrieren Sie sich für ein Konto).  Wenn Sie dazu aufgefordert werden, wählen Sie eine gespiegelte Site zum Herunterladen des MySQL-Installers aus, und speichern Sie den Installer auf dem Desktop.

7. Doppelklicken Sie auf die Installerdatei auf dem Desktop, um mit der Installation zu beginnen.

8. Klicken Sie auf **Weiter**.

9. Akzeptieren Sie die Lizenzbedingungen, und klicken Sie auf **Next**.

10. Klicken Sie auf **Typical**, um häufig verwendete Features zu installieren.

11. Klicken Sie auf **Installieren**.

12. Starten Sie den MySQL-Konfigurationsassistenten, und klicken Sie auf **Next**.

13. Wählen Sie **Detailed Configuration**, und klicken Sie auf "Next".

14. Wählen Sie **Server Machine**, wenn Sie MySQL zusammen mit anderen Anwendungen auf dem Server ausführen möchten, oder wählen Sie die für Ihre Zwecke am besten geeignete Option.  Klicken Sie auf **Weiter**.

15. Wählen Sie **Multifunctional Database** oder die für Ihre Zwecke am besten geeignete Option.  Klicken Sie auf **Weiter**.

16. Wählen Sie das Datenlaufwerk aus, das Sie im vorherigen Abschnitt angeschlossen haben.

	![Configure MySQL][MySQLConfig5]

17. Wählen Sie **Decision Support (DSS)/OLAP** oder die für Ihre Zwecke am besten geeignete Option.  Klicken Sie auf **Weiter**.

18. Wählen Sie **Enable TCP/IP Networking** und **Add firewall exception for this port**. (Hierdurch wird eine eingehende Regel in der Windows-Firewall mit dem Namen **MySQL Server** erstellt.)

	![Configure MySQL][MySQLConfig7]

19. Wenn Sie Text in vielen unterschiedlichen Sprachen speichern möchten, wählen Sie **Best Support For Multilingualism**. Andernfalls können Sie eine der anderen Optionen wählen.  Klicken Sie auf **Weiter**.

	![Configure MySQL][MySQLConfig8]

20. Wählen Sie **Install As Windows Service** und **Launch the MySQL Server automatically**.  Wählen Sie außerdem **Include Bin Directory in Windows PATH**. Klicken Sie auf **Weiter**.

	![Configure MySQL][MySQLConfig9]

21. Geben Sie das Stammkennwort ein. Aktivieren Sie nicht das Kontrollkästchen **Enable root access from remote machines** oder **Create An Anonymous Account**.  Klicken Sie auf **Weiter**.

	![Configure MySQL][MySQLConfig10]

22. Klicken Sie auf **Execute**, und warten Sie, bis die Konfiguration abgeschlossen ist.

23. Klicken Sie auf **Fertig stellen**.

24. Klicken Sie auf **Start**, und wählen Sie **MySQL 5.x Command Line Client**, um den Befehlszeilenclient zu starten.

25.  Geben Sie das Stammkennwort an der Eingabeaufforderung ein, das Sie im vorherigen Schritt geändert haben. Daraufhin wird eine Eingabeaufforderung angezeigt, über die Sie SQL-Anweisungen starten können, um mit der Datenbank zu interagieren.

26. Führen Sie zum Erstellen eines neuen MySQL-Benutzers bei der **mysql>**-Eingabeaufforderung den folgenden Befehl aus:

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Das Semikolon (;) am Ende der Zeilen wird für den Abschluss der Befehle benötigt.

27. Führen Sie die folgenden Befehle aus, um eine Datenbank zu erstellen und um dafür die  `mysqluser`-Benutzerberechtigungen zu gewähren:

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Beachten Sie, dass die Benutzernamen und Kennwörter der Datenbank nur von Skripten verwendet werden, die eine Verbindung zur Datenbank herstellen.  Benutzernamen für Datenbankkonten sind nicht notwendigerweise tatsächliche Benutzerkonten auf dem Computer.

28. Um sich von einem anderen Computer aus anzumelden, führen Sie folgenden Befehl aus:

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	wobei 'ip-address' die IP-Adresse des Computers ist, von dem aus Sie eine Verbindung mit MySQL herstellen möchten.
	
29. Um den MySQL-Befehlszeilenclient zu beenden, führen Sie folgenden Befehl aus:

		quit

30. Nachdem MySQL installiert wurde, konfigurieren einen Endpunkt, damit ein Remotezugriff auf MySQL möglich ist. Melden Sie sich beim [Azure-Verwaltungsportal][AzurePreviewPortal] an. Klicken Sie im Azure-Portal auf **Virtuelle Computer** und dann auf den Namen des neuen virtuellen Computers, dann auf **Endpunkte** und dann auf **Endpunkt hinzufügen**.

31. Wählen Sie **Endpunkt hinzufügen**, und klicken Sie zum Fortfahren auf den Pfeil.
	

32. Fügen Sie einen Endpunkt mit dem Namen "MySQL" hinzu, und wählen Sie als Protokoll **TCP** aus. Setzen Sie die beiden Ports **Öffentlich** und **Privat** auf "3306". Aktivieren Sie das Kontrollkästchen. Damit können Sie per Remoteausführung auf MySQL zugreifen.
	

33. Testen Sie die Fernverbindung zu MySQL.  Führen Sie von einem lokalen Computer mit MySQL einen Befehl wie den folgenden aus, um sich als Benutzer **mysqluser** anzumelden:

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	Wenn Sie den virtuellen Computer "testwinvm" genannt haben, führen Sie folgenden Befehl aus:

		mysql -u mysqluser -p -h testwinvm.cloudapp.net

##Ressourcen
Informationen zu MySQL finden Sie in der [MySQL-Dokumentation](http://dev.mysql.com/doc/).

[AzurePortal]: http://manage.windowsazure.com
[MySQLDownloads]: http://www.mysql.com/downloads/mysql/


[MySQLConfig5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
[MySQLConfig7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
[MySQLConfig8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
[MySQLConfig9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
[MySQLConfig10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png





<!--HONumber=42-->
