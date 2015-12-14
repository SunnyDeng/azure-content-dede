<properties
	pageTitle="Erstellen von virtuellen Oracle Database-Computern im klassischen Azure-Portal | Microsoft Azure"
	description="Erfahren Sie, wie Sie virtuelle Computer mit einer Oracle-Datenbank mit dem klassischen Bereitstellungsmodell und dem Azure-Portal erstellen."
	services="virtual-machines"
	authors="bbenz"
	documentationCenter=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="Windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Erstellen eines virtuellen Oracle Database-Computers in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Das folgende Beispiel zeigt, wie Sie einen virtuellen Computer (Virtual Machine, VM) auf der Grundlage eines von Microsoft bereitgestellten Oracle Database-Image unter Windows Server 2012 in Azure erstellen. Der Vorgang umfasst zwei Schritte: die Erstellung des virtuellen Computers und die Erstellung der Oracle-Datenbank auf dem virtuellen Computer. In diesem Beispiel wird Oracle Database Version 12c verwendet, die Schritte sind jedoch für Version 11g nahezu identisch.

##So erstellen Sie einen virtuellen Oracle Database-Computer in Azure

1.	Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.

2.	Klicken Sie auf **Marketplace**, auf **Compute**, und geben Sie **Oracle** in das Suchfeld ein.

3.	Wählen Sie eines der verfügbaren Oracle Database-Images aus: **Version 11g, Version 12c, Standard Edition, Enterprise Edition oder ein Popular Options- oder Advanced Options-Bündel**. Überprüfen Sie die Informationen zum Image, das Sie auswählen möchten (z. B. empfohlene Mindestgröße), und klicken Sie dann auf **Weiter**.

4.	Geben Sie einen **Hostnamen** für den virtuellen Computer an.

5.	Geben Sie einen **Benutzernamen** für den virtuellen Computer an. Beachten Sie, dass dieser Benutzer für die Remoteanmeldung auf dem virtuellen Computer verwendet wird. Dies ist nicht der Benutzername für die Oracle-Datenbank.

6.	Geben Sie ein Kennwort für den virtuellen Computer ein, und bestätigen Sie es, oder geben Sie einen öffentlichen SSH-Schlüssel an.

7.	Wählen Sie einen **Tarif** aus. Beachten Sie, dass die empfohlenen Tarife standardmäßig angezeigt werden. Um alle Konfigurationsoptionen anzuzeigen, klicken Sie oben rechts auf **Alle anzeigen**.

8.	Legen Sie die optionale Konfiguration nach Bedarf fest. Beachten Sie dabei folgende Überlegungen:

	a. Lassen Sie **Speicherkonto** unverändert, um ein neues Speicherkonto mit dem Namen des virtuellen Computers zu erstellen.

	b. Lassen Sie die **Verfügbarkeitsgruppe** auf "Nicht konfiguriert" festgelegt.

	c. Fügen Sie zu diesem Zeitpunkt keine **Endpunkte** hinzu.

9.	Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine Ressourcengruppe

10. Wählen Sie ein **Abonnement** aus.

11. Wählen Sie einen **Standort** aus.

12. Klicken Sie auf **Erstellen**. Der Vorgang zum Erstellen eines virtuellen Computers wird gestartet. Wenn der Status des virtuellen Computers **Wird ausgeführt** lautet, fahren Sie mit dem nächsten Schritt fort.


##So erstellen Sie Ihre Datenbank mit dem virtuellen Oracle Database-Computer in Azure

1.	Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.

2.	Klicken Sie auf **Virtuelle Computer**.

3.	Klicken Sie auf den Namen des virtuellen Computers, bei dem Sie sich anmelden möchten.

4.	Klicken Sie auf **Verbinden**.

5.	Befolgen Sie die Anweisungen, um eine Verbindung mit dem virtuellen Computer herzustellen. Wenn Sie zur Eingabe des Administratornamens und des Kennworts aufgefordert werden, verwenden Sie die Werte, die Sie beim Erstellen des virtuellen Computers angegeben haben.

6.	Erstellen Sie eine Umgebungsvariable mit dem Namen **ORACLE\_HOSTNAME**, und legen Sie als Wert den Computername des virtuellen Computers fest. Sie können eine Umgebungsvariable anhand der folgenden Schritte erstellen:

	a. Klicken Sie in Windows auf **Start**, geben Sie **Systemsteuerung** ein, und klicken Sie auf das Symbol **Systemsteuerung**. Klicken Sie auf **System und Sicherheit**, anschließend auf **System** und dann auf **Erweiterte Systemeinstellungen**.

	b. Klicken Sie auf die Registerkarte **Erweitert** und dann auf **Umgebungsvariablen**.

	c. Klicken Sie im Bereich **Systemvariablen** auf **Neu**, um die Variable zu erstellen.

	d. Geben Sie im Dialogfeld **Neue Systemvariable** den Variablennamen **ORACLE\_HOSTNAME** und als Wert den Computernamen des virtuellen Computers ein. Öffnen Sie zum Ermitteln des Computernamens eine Eingabeaufforderung, und führen Sie **SET COMPUTERNAME** aus. Die Ausgabe dieses Befehls enthält den Computernamen.

	e. Klicken Sie auf **OK**, um die neue Umgebungsvariable zu speichern und das Dialogfeld **Neue Systemvariable** zu schließen.

	f. Schließen Sie die übrigen von der Systemsteuerung geöffneten Dialogfelder.

7.	Klicken Sie in Windows auf **Start**, und geben Sie **Datenbank-Konfigurationsassistent** ein. Klicken Sie auf das Symbol **Datenbank-Konfigurationsassistent**.

8.	Geben Sie in den Dialogfeldern des **Datenbank-Konfigurationsassistenten** jeweils die erforderlichen Werte an:

	a. **Schritt 1**: Klicken Sie auf **Datenbank erstellen** und dann auf **Weiter**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image5.png)

	b. **Schritt 2**: Geben Sie einen Wert für **Globaler Datenbankname** ein. Geben Sie einen Wert für **Administratorkennwort** ein, und bestätigen Sie ihn. Dieses Kennwort gilt für den **SYSTEM**-Benutzer Ihrer Oracle-Datenbank. Deaktivieren Sie das Kontrollkästchen **Create As Container Database**. Klicken Sie auf **Weiter**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image6.png)

	c. **Schritt 3**: Die Überprüfung der erforderlichen Komponenten wird automatisch gestartet. Fahren Sie mit **Schritt 4** fort.

	d. **Schritt 4**: Überprüfen Sie die Optionen unter **Datenbank erstellen – Zusammenfassung**, und klicken Sie dann auf **Fertig stellen**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image7.png)
	e. **Schritt 5**: Auf der **Statusseite** wird der Status der Datenbankerstellung angezeigt.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image8.png)
	f. Nachdem die Datenbank erstellt wurde, können Sie das Dialogfeld **Kennwortverwaltung** verwenden. Ändern Sie ggf. die Kennworteinstellungen, und schließen Sie dann die Dialogfelder, um den **Datenbank-Konfigurationsassistenten** zu beenden.

##Bestätigen, dass die Datenbank installiert ist

1.	Starten Sie eine SQL Plus-Eingabeaufforderung, während Sie noch bei Ihrem virtuellen Computer angemeldet sind. Klicken Sie in Windows auf *Start**, und geben Sie **SQL Plus** ein. Klicken Sie auf das Symbol **SQL Plus**.

2.	Geben Sie den Namen des **SYSTEM**-Benutzers und das Kennwort ein, den bzw. das Sie beim Erstellen der Oracle-Datenbank angegeben haben.

3.	Führen Sie den folgenden Befehl an der SQL Plus-Eingabeaufforderung aus.

		**select * from GLOBAL\_NAME;**

	Das Ergebnis enthält den globalen Namen der Datenbank, die Sie erstellt haben.

	![](media/virtual-machines-creating-oracle-database-virtual-machine/image9.png)

##Zulassen von Remoteverbindungen zur Datenbank
Um Remoteverbindungen mit Ihrer Datenbank zuzulassen (z. B. für einen Clientcomputer, auf dem Java-Code ausgeführt wird), müssen Sie den Datenbanklistener starten, Port 1521 in der Firewall des virtuellen Computers öffnen und einen öffentlichen Endpunkt für Port 1521 erstellen.

### Starten des Datenbanklisteners
1.	Melden Sie sich bei Ihrem virtuellen Computer an.

2.	Öffnen Sie eine Eingabeaufforderung.

3.	Führen Sie an der Eingabeaufforderung den folgenden Befehl aus.

		**lsnrctl start**

> [AZURE.NOTE]Mit **lsnrctl status** können Sie den Status des Listeners ermitteln. Führen Sie **lsnrctl stop** aus, wenn Sie den Listener beenden möchten.

### Öffnen Sie in der Firewall des virtuellen Computers Port 1521.

1.	Klicken Sie in Windows auf **Start**, geben Sie **Windows-Firewall mit erweiterter Sicherheit** ein, und klicken Sie dann auf das Symbol **Windows-Firewall mit erweiterter Sicherheit**, während Sie bei Ihrem virtuellen Computer angemeldet sind. Die Verwaltungskonsole für die **Windows-Firewall mit erweiterter Sicherheit** wird geöffnet.

2.	Klicken Sie in der Firewall-Verwaltungskonsole im linken Bereich auf **Eingehende Regeln**. (Falls **Eingehende Regeln** nicht angezeigt wird, erweitern Sie den obersten Knoten im linken Bereich.) Klicken Sie dann im rechten Bereich auf **Neue Regel**.

3.	Wählen Sie für **Regeltyp** die Option **Port** aus, und klicken Sie dann auf **Weiter**.

4.	Legen Sie für **Protokoll und Port** die Option **TCP** fest, wählen Sie **Bestimmte lokale Ports** aus, geben Sie **1521** für den Port ein, und klicken Sie dann auf **Weiter**.

5.	Klicken Sie auf **Verbindung zulassen** und anschließend auf **Weiter**.

6.	Übernehmen Sie die Standardeinstellungen für die Profile, für die die Regel gilt, und klicken Sie auf **Weiter**.

7.	Geben Sie einen Namen für die Regel und optional eine Beschreibung ein, und klicken Sie dann auf **Fertig stellen**.

### Erstellen Sie einen öffentlichen Endpunkt für Port 1521:

1.	Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.

2.	Klicken Sie auf **Durchsuchen**.

3.  Klicken Sie auf **Virtuelle Computer**.

4.	Wählen Sie den virtuellen Computer aus.

5.	Klicken Sie auf **Einstellungen**.

6.	Klicken Sie auf **Endpunkte**.

7.	Klicken Sie auf **Hinzufügen**.

8.	Geben Sie einen Namen für den Endpunkt an:

	a. Verwenden Sie **TCP** als Protokoll.

	b. Verwenden Sie Port **1521** als öffentlichen Port.

	c. Verwenden Sie Port **1521** als privaten Port.

9.	Lassen Sie die übrigen Optionen unverändert.

10. Klicken Sie auf **OK**.

##Aktivieren des Remotezugriffs auf Oracle Database Enterprise Manager
Wenn Sie den Remotezugriff für Oracle Database Enterprise Manager aktivieren möchten, öffnen Sie Port 5500 in der Firewall, und erstellen Sie im klassischen Azure-Portal einen VM-Endpunkt für 5500. (Verwenden Sie dazu die weiter oben angegebenen Schritte für das Öffnen von Port 1521 und das Erstellen eines Endpunkts für 1521.) Öffnen Sie dann einen Browser mit der URL im Format `http://<<unique_domain_name>>:5500/em`, um Oracle Enterprise Manager auf dem Remotecomputer auszuführen.

> [AZURE.NOTE]Sie können den Wert für *<<unique\_domain\_name>>* im [klassischen Azure-Portal](https://ms.portal.azure.com/) festlegen, indem Sie auf **Virtuelle Computer** klicken und dann den virtuellen Computer auswählen, den Sie zum Ausführen von Oracle Database verwenden.

##Konfigurieren von Popular Options- und Advanced Options-Bündeln
Wenn Sie das Bündel **Oracle Database with Popular Options** oder **Oracle Database with Advanced Options** verwenden, müssen Sie als Nächstes die Add-On-Funktionen in der Oracle-Installation konfigurieren. Anweisungen zum Einrichten dieser Funktionen unter Windows finden Sie in der Oracle-Dokumentation. Die Konfigurationen können je nach Anforderungen der einzelnen Komponenten stark variieren.

Das Bündel **Oracle Database with Popular Options** enthält Oracle Database Enterprise Edition und umfasst in der Lizenz enthaltende Instanzen von [Partitioning](http://www.oracle.com/us/products/database/options/partitioning/overview/index.html), [Active Data Guard](http://www.oracle.com/us/products/database/options/active-data-guard/overview/index.html), [Oracle Tuning Pack for Database](http://docs.oracle.com/html/A86647_01/tun_ovw.htm), [Oracle Diagnostics Pack for Database](http://docs.oracle.com/cd/B28359_01/license.111/b28287/options.htm#CIHIHDDJ) und [Oracle Lifecycle Management Pack for Database](http://www.oracle.com/technetwork/oem/lifecycle-mgmt-495331.html).

Das Bündel **Oracle Database with Advanced Options** umfasst in der Lizenz enthaltene Instanzen aller Optionen des Popular Options-Bündels sowie [Advanced Compression](http://www.oracle.com/us/products/database/options/advanced-compression/overview/index.html), [Advanced Security](http://www.oracle.com/us/products/database/options/advanced-security/overview/index.html), [Label Security](http://www.oracle.com/us/products/database/options/label-security/overview/index.html), [Database Vault](http://www.oracle.com/us/products/database/options/database-vault/overview/index.html), [Advanced Analytics](http://www.oracle.com/us/products/database/options/advanced-analytics/overview/index.html), [OLAP](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [Spatial and Graph](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [In-Memory Database Cache](http://www.oracle.com/technetwork/products/timesten/overview/timesten-imdb-cache-101293.html), [Data Masking Pack](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CHDGEEBB) und das Oracle Test Data Management Pack (als Bestandteil des Data Masking Pack).

##Zusätzliche Ressourcen
Nachdem Sie den virtuellen Computer eingerichtet und die Datenbank erstellt haben, lesen Sie die folgenden Themen, um weitere Informationen zu erhalten.

-	[Images virtueller Oracle-Computer – verschiedene Überlegungen](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-	[Oracle Database 12c-Dokumentationsbibliothek](http://www.oracle.com/pls/db1211/homepage)

-	[Herstellen einer Verbindung mit der Oracle-Datenbank in einer Java-Anwendung](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

-	[Images des virtuellen Oracle-Computers für Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

-	[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=AcomDC_1203_2015-->