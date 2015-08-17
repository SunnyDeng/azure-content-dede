<properties title="Creating an Oracle Database Virtual Machine in Azure" pageTitle="Erstellen eines virtuellen Oracle Database-Computers in Azure" description="Erstellen Sie mithilfe eines Beispiels einen virtuellen Oracle-Computer in Microsoft Azure und anschließend eine Oracle-Datenbank auf dem Computer." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Erstellen eines virtuellen Oracle Database-Computers in Azure
Das folgende Beispiel zeigt, wie Sie einen virtuellen Computer basierend auf einem von Microsoft bereitgestellten Oracle Database-Image unter Windows Server 2012 in Azure erstellen. Dies umfasst zwei Schritte: Erstellen des virtuellen Computers und Erstellen der Oracle-Datenbank auf dem virtuellen Computer. In diesem Beispiel wird Oracle Database Version 12c verwendet, die Schritte sind jedoch für Version 11g nahezu identisch.

##Erstellen eines virtuellen Oracle Database-Computers in Azure

1.	Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.

2.	Klicken Sie auf **Marketplace**, dann auf **Compute**, und geben Sie **Oracle** in das Suchfeld ein.

3.	Wählen Sie eines der verfügbaren Oracle Database-Images aus:** Version 11g, Version 12c, Standard Edition, Enterprise Edition oder ein Popular Options- oder Advanced Options-Bündel**. Überprüfen Sie die Informationen zum Image, das Sie auswählen möchten (z. B. empfohlene Mindestgröße), und klicken Sie dann auf **Weiter**.

4.	Geben Sie einen **Hostnamen** für den virtuellen Computer an.

5.	Geben Sie einen **Benutzernamen** für den virtuellen Computer an. Beachten Sie, dass dieser Benutzer für die Remote-Anmeldung auf dem virtuellen Computer verwendet wird. Dies ist nicht der Oracle-Datenbank-Benutzername.

6.	Geben Sie ein Kennwort für den virtuellen Computer ein, und bestätigen Sie es, oder geben Sie einen öffentlichen SSH-Schlüssel an.

7.	Wählen Sie einen **Tarif** aus. Beachten Sie, dass die empfohlenen Preiskategorien standardmäßig angezeigt werden. Um alle Konfigurationsoptionen anzuzeigen, klicken Sie oben rechts auf **Alle anzeigen**.

8.	Legen Sie [Optionale Konfiguration](https://msdn.microsoft.com/library/azure/dn763935.aspx) nach Bedarf fest. Beachten Sie dabei folgende Überlegungen:

	1. Lassen Sie **Speicherkonto** unverändert, um ein neues Speicherkonto mit dem Namen des virtuellen Computers zu erstellen.

	2. Legen Sie für **Verfügbarkeitsgruppe** "Nicht konfiguriert" fest.

	3. Fügen Sie zu diesem Zeitpunkt keine **Endpunkte** hinzu.

9.	Auswählen oder Erstellen einer Ressourcengruppe

10. Wählen Sie ein **Abonnement** aus.

11. Wählen Sie einen **Standort** aus.

12. Klicken Sie auf **Erstellen**. Der Vorgang zum Erstellen eines virtuellen Computers wird gestartet. Wenn der Status des virtuellen Computers **Wird ausgeführt** ist, fahren Sie mit dem nächsten Schritt fort.


##Erstellen einer Datenbank mit dem virtuellen Oracle Database-Computer in Azure

1.	Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.

2.	Klicken Sie auf **Virtuelle Computer**.

3.	Klicken Sie auf den Namen des virtuellen Computers, an dem Sie sich anmelden möchten.

4.	Klicken Sie auf **Verbinden**.

5.	Befolgen Sie die Anweisungen, um eine Verbindung mit dem virtuellen Computer herzustellen. Wenn Sie zur Eingabe des Administratornamens und des Kennworts aufgefordert werden, verwenden Sie die Werte, die Sie beim Erstellen des virtuellen Computers bereitgestellt haben.

6.	Erstellen Sie eine Umgebungsvariable mit dem Namen **ORACLE\_HOSTNAME** und legen Sie als Wert den Computername des virtuellen Computers fest. Sie können eine Umgebungsvariable anhand der folgenden Schritte erstellen:

	1.	Klicken Sie auf das **Windows-Startmenü**, geben Sie **Systemsteuerung** ein, klicken Sie auf das Symbol **Systemsteuerung** und dann auf **System und Sicherheit**. Klicken Sie auf **System** und dann auf **Erweiterte Systemeinstellungen**.

	2.	Klicken Sie auf die Registerkarte **Erweitert** und dann auf **Umgebungsvariablen**.

	3.	Klicken Sie im Bereich **Systemvariablen** auf **Neu**, um die Variable zu erstellen.

	4.	Geben Sie im Dialogfeld **Neue Systemvariable** den Variablennamen **ORACLE\_HOSTNAME** und den Computernamen des virtuellen Computers als Wert ein. Um den Computernamen zu ermitteln, öffnen Sie eine Eingabeaufforderung, und führen Sie **SET COMPUTERNAME** aus (die Ausgabe dieses Befehls enthält den Computernamen).
	
	5.	Klicken Sie auf **OK**, um die neue Umgebungsvariable zu speichern und das Dialogfeld **Neue Systemvariable** zu schließen.

	6.	Schließen Sie die übrigen von der Systemsteuerung geöffneten Dialogfelder.

7.	Klicken Sie auf das **Windows-Startmenü**, und geben Sie dann **Datenbank-Konfigurationsassistent** ein. Klicken Sie auf das Symbol **Datenbank-Konfigurationsassistent**.

8.	Geben Sie im **Datenbank-Konfigurationsassistenten** die erforderlichen Werte in jedem Dialogfeld an:

	1.	**Schritt 1:** klicken Sie auf **Datenbank erstellen** und dann auf **Weiter**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image5.png)

	2. **Schritt 2:** Geben Sie einen Wert für **Globaler Datenbankname** ein. Geben Sie ein**Administratorkennwort** ein und bestätigen Sie es; dieses Kennwort wird für den **SYSTEM**-Benutzer der Oracle-Datenbank verwendet. Deaktivieren Sie die Option **Als Container-Datenbank erstellen,**. Klicken Sie auf **Weiter**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image6.png)

	3. **Schritt 3:** Die Überprüfung der erforderlichen Komponenten wird automatisch gestartet. Fahren Sie mit **Schritt 4** fort.
	
	4. **Schritt 4:** Überprüfen Sie die Optionen **Datenbank erstellen – Zusammenfassung**, und klicken Sie dann auf **Fertig stellen**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image7.png)
	5. **Schritt 5:** Auf der **Statusseite** wird der Status der Datenbankerstellung angezeigt.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image8.png)
	6. Nachdem die Datenbank erstellt wurde, können Sie das Dialogfeld **Kennwortverwaltung** bearbeiten. Ändern Sie die Kennworteinstellungen bei Bedarf, und schließen Sie dann die Dialogfelder, um den **Datenbank-Konfigurationsassistenten** zu beenden.

##Bestätigen, dass die Datenbank installiert ist

1.	Bleiben Sie auf dem virtuellen Computer angemeldet, und starten Sie eine SQL Plus-Eingabeaufforderung: Klicken Sie auf das **Windows-Startmenü**, und geben Sie **SQL Plus** ein. Klicken Sie auf das Symbol **SQL Plus**.

2.	Wenn Sie dazu aufgefordert werden, geben Sie den Namen des **SYSTEM**-Benutzers und das Kennwort ein, den bzw. das Sie beim Erstellen der Oracle-Datenbank angegeben haben.

3.	Geben Sie folgenden Befehl in der SQL Plus-Eingabeaufforderung ein, und führen Sie ihn aus:

		select * from GLOBAL\_NAME;

	Das Ergebnis enthält den globalen Namen der Datenbank, die Sie erstellt haben.

	![](media/virtual-machines-creating-oracle-database-virtual-machine/image9.png)

##Zulassen von Remoteverbindungen zur Datenbank
Um Remoteverbindungen zur Ihrer Datenbank zuzulassen (z. B. für Clientcomputer, auf dem Java-Code ausgeführt wird), müssen Sie den Datenbank-Listener starten, Port 1521 in der Firewall des virtuellen Computers öffnen und einen öffentlichen Endpunkt für Port 1521 erstellen.

### Starten des Datenbank-Listeners
1.	Melden Sie sich am virtuellen Computer an.

2.	Öffnen Sie eine Eingabeaufforderung.

3.	Führen Sie in der Eingabeaufforderung Folgendes aus:

		lsnrctl start

(Um den Status des Listeners zu überprüfen, führen Sie `lsnrctl status` aus. Um den Listener zu beenden, führen Sie `lsnrctl stop` aus.)

### Öffnen Sie Port 1521 in der Firewall des virtuellen Computers.

1.	Bleiben Sie auf dem virtuellen Computer angemeldet. Klicken Sie auf das **Windows-Startmenü**, geben Sie **Windows-Firewall mit erweiterter Sicherheit** ein, und klicken Sie dann auf das Symbol **Windows-Firewall mit erweiterter Sicherheit**. Die Verwaltungskonsole für **Windows-Firewall mit erweiterter Sicherheit** wird geöffnet.

2.	Klicken Sie in der Firewall-Verwaltungskonsole im linken Bereich auf **Eingehende Regeln** (wenn **Eingehende Regeln** nicht angezeigt wird, erweitern Sie den obersten Knoten im linken Bereich), und klicken Sie dann auf **Neue Regel** im rechten Bereich.

3.	Wählen Sie für **Regeltyp** die Option **Port** aus, und klicken Sie auf **Weiter**.

4.	Legen Sie für **Protokoll und Port** die Option **TCP** fest, wählen Sie **Bestimmte lokale Ports** aus, geben Sie **1521** für den Port ein, und klicken Sie dann auf "Weiter".

5.	Klicken Sie auf **Verbindung zulassen** und anschließend auf **Weiter**.

6.	Übernehmen Sie die Standardeinstellungen für die Profile, für die die Regel gilt, und klicken Sie auf **Weiter**.

7.	Geben Sie einen Namen für die Regel und optional eine Beschreibung ein, und klicken Sie dann auf **Fertig stellen**.

### Erstellen Sie einen öffentlichen Endpunkt für Port 1521:

1.	Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.

2.	Klicken Sie auf **Durchsuchen**.

3.    Klicken Sie auf **Virtuelle Computer**.

4.	Wählen Sie den virtuellen Computer aus.

5.	Klicken Sie auf **Einstellungen**.

6.	Klicken Sie auf **Endpunkte**.

7.	Klicken Sie auf **Hinzufügen**.

8.	Geben Sie einen Namen für den Endpunkt an:

	1. Verwenden Sie **TCP** als Protokoll.
	
	2. Verwenden Sie als öffentlichen Port **1521**.
	
	3. Verwenden Sie als privaten Port **1521**.

9.	Lassen Sie die restlichen Optionen unverändert.

10. Klicken Sie auf **OK**.

##Aktivieren des Remotezugriffs auf Oracle Database Enterprise Manager
Wenn Sie den Remotezugriff auf Oracle Database Enterprise Manager aktivieren möchten, öffnen Sie Port 5500 in der Firewall, und erstellen Sie einen Endpunkt des virtuellen Computers für 5500 im Azure-Portal (folgen Sie den Schritten oben für das Öffnen von Port 1521 und Erstellen eines Endpunkts für 1521). Öffnen Sie dann zum Ausführen von Oracle Enterprise Manager auf dem Remotecomputer einen Browser mit der URL in Form von `http://<<unique_domain_name>>:5500/em`. (Sie können den Wert für *<< Unique\_domain\_name >>* im [Azure-Portal](https://ms.portal.azure.com/) festlegen, indem Sie auf **Virtuelle Computer** klicken und dann den virtuellen Computer auswählen, den Sie zum Ausführen von Oracle Database verwenden.)

##Konfigurieren von Popular Options- und Advanced Options-Bündeln
Wenn Sie das Bündel **Oracle Database with Popular Options** oder **Oracle Database with Advanced Options** verwenden, müssen Sie als Nächstes die Add-On-Funktionen in der Oracle-Installation konfigurieren. Anweisungen zum Einrichten dieser Funktionen unter Windows finden Sie in der Oracle-Dokumentation. Die Konfigurationen können je nach Anforderungen der einzelnen Komponenten stark variieren.

Das Bündel **Oracle Database with Popular Options** enthält Oracle Database Enterprise Edition und umfasst in der Lizenz enthaltende Instanzen von [Partitioning](http://www.oracle.com/us/products/database/options/partitioning/overview/index.html), [Active Data Guard](http://www.oracle.com/us/products/database/options/active-data-guard/overview/index.html), [Oracle Tuning Pack for Database](http://docs.oracle.com/html/A86647_01/tun_ovw.htm), [Oracle Diagnostics Pack for Database](http://docs.oracle.com/cd/B28359_01/license.111/b28287/options.htm#CIHIHDDJ) und [Oracle Lifecycle Management Pack for Database](http://www.oracle.com/technetwork/oem/lifecycle-mgmt-495331.html).

Das Bündel**Oracle Database with Advanced Options** umfasst in der Lizenz enthaltene Instanzen aller Optionen des Popular Options-Bündels sowie [Advanced Compression](http://www.oracle.com/us/products/database/options/advanced-compression/overview/index.html), [Advanced Security](http://www.oracle.com/us/products/database/options/advanced-security/overview/index.html), [Label Security](http://www.oracle.com/us/products/database/options/label-security/overview/index.html), [Database Vault](http://www.oracle.com/us/products/database/options/database-vault/overview/index.html), [Advanced Analytics](http://www.oracle.com/us/products/database/options/advanced-analytics/overview/index.html), [OLAP](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [Spatial and Graph](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [In-Memory Database Cache](http://www.oracle.com/technetwork/products/timesten/overview/timesten-imdb-cache-101293.html), [Data Masking Pack](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CHDGEEBB) und das Oracle Test Data Management Pack (als Bestandteil des Data Masking Pack).

##Zusätzliche Ressourcen
Nachdem Sie den virtuellen Computer eingerichtet und die Datenbank erstellt haben, lesen Sie die folgenden Themen, um weitere Informationen zu erhalten.

-	[Images virtueller Oracle-Computer – verschiedene Überlegungen](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-	[Oracle Database 12c-Dokumentationsbibliothek](http://www.oracle.com/pls/db1211/homepage)

-	[Connecting to Oracle Database from a Java Application](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136) (Herstellen einer Verbindung mit Oracle Database in einer Java-Anwendung; in englischer Sprache)

-	[Bilder des virtuellen Oracle-Computers für Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

-	[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=August15_HO6-->