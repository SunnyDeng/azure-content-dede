<properties
	pageTitle="Einrichten eines virtuellen SQL Server-Computers als IPython Notebook-Server | Microsoft Azure"
	description="Einrichten eines virtuellen Data Science-Computers mit SQL-Server und IPython-Server."
	services="machine-learning"
	solutions="" documentationCenter=""
	authors="msolhab" 
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/29/2015"
	ms.author="mohabib;xibingao;bradsev" />

# Einrichten eines virtuellen Azure SQL Server-Computers als IPython Notebook-Server für die erweiterte Analyse

In diesem Thema wird das Bereitstellen und Konfigurieren eines virtuellen SQL Server-Computers als Teil einer cloudbasierten Data Science-Umgebung beschrieben. Der virtuelle Windows-Computer wird mit Unterstützung von Tools wie IPython Notebook, Azure-Speicher-Explorer und AzCopy sowie anderer Hilfsprogramme, die für Data Science-Projekte hilfreich sind, konfiguriert. Azure-Speicher-Explorer und AzCopy stellen z. B. hilfreiche Möglichkeiten zum Hochladen von Daten von Ihrem lokalen Computer in den Azure-Blob-Speicher oder das Herunterladen aus dem Blob-Speicher auf Ihren lokalen Computer bereit.

In der Galerie der virtuellen Computer von Azure sind verschiedene Images zu finden, die Microsoft SQL Server enthalten. Wählen Sie ein VM-Image mit SQL Server aus, das Ihren Datenanforderungen entspricht. Folgende Images werden empfohlen:

- SQL Server 2012 SP2 Enterprise für kleine bis mittlere Datenvolumen
- SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads für große bis sehr große Datenvolumen

 >[AZURE.NOTE]Das SQL Server 2012 SP2 Enterprise-Image **enthält keinen Datenträger**. Sie müssen einen oder mehrere virtuelle Festplatten zum Speichern von Daten hinzufügen und/oder anfügen. Wenn Sie einen virtuellen Azure-Computer erstellen, hat dieser einen Datenträger für das Betriebssystem, der Laufwerk C zugeordnet ist, und einen temporären Datenträger, der Laufwerk D zugeordnet ist. Verwenden Sie Laufwerk D nicht zum Speichern von Daten. Wie der Name schon sagt, bietet dieser Datenträger nur temporäre Speicherung. Er ermöglicht keine Redundanz oder Sicherung, da er sich nicht im Azure-Speicher befindet.


##<a name="Provision"></a>Verbinden mit dem Azure-Verwaltungsportal und Bereitstellen eines virtuellen Computers mit SQL Server

1.  Melden Sie sich auf dem [Azure-Verwaltungsportal](http://manage.windowsazure.com/) bei Ihrem Konto an. Wenn Sie kein Azure-Konto haben, sollten Sie die Seite [Kostenlose einmonatige Testversion](http://www.windowsazure.com/pricing/free-trial/) besuchen.

2.  Klicken Sie im Azure-Verwaltungsportal unten links auf der Webseite auf **+NEU**, klicken Sie auf **COMPUTE**, klicken Sie auf **VIRTUELLER COMPUTER** und dann auf **AUS GALERIE**.

3.  Wählen Sie auf der Seite **Virtuellen Computer erstellen** ein Image eines virtuellen Computers aus, das SQL Server enthält und Ihren Datenanforderungen entspricht, und klicken Sie dann unten rechts auf den Pfeil für die nächste Seite. Die aktuellsten Informationen zu den unterstützen SQL Server-Images auf Azure finden Sie unter [Erste Schritte mit SQL Server auf Windows Azure-Virtuellen Computern](http://go.microsoft.com/fwlink/p/?LinkId=294720) in der Dokumentation [SQL Server auf Windows Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294719).

	![Auswählen des virtuellen SQL Server-Computer][1]

4.  Geben Sie zunächst auf der Seite **Konfiguration des virtuellen Computers** die folgenden Informationen ein:

    -   Geben Sie einen **NAMEN DES VIRTUELLEN COMPUTERS** ein.
    -   Geben Sie im Feld **NEUER BENUTZERNAME** einen eindeutigen Benutzernamen für das lokale Administratorkonto für den virtuellen Computer ein.
    -   Geben Sie in das Feld **NEUES KENNWORT** ein sicheres Kennwort ein. Weitere Informationen finden Sie unter [Sichere Kennwörter](http://msdn.microsoft.com/library/ms161962.aspx).
    -   Geben Sie das Kennwort im Feld **KENNWORT BESTÄTIGEN** nochmals ein.
    -   Wählen Sie in der Dropdownliste die geeignete **GRÖSSE** aus.

     >[AZURE.NOTE]Die Größe des virtuellen Computers wird während der Bereitstellung angegeben: A2 ist die kleinste empfohlene Größe für Produktionsarbeitsauslastungen. Die empfohlene Minimalgröße für einen virtuellen Computer ist A3, wenn SQL Server Enterprise Edition verwendet wird. Wählen Sie A3 oder höher aus, wenn Sie SQL Server Enterprise Edition verwenden. Wählen Sie A4 aus, wenn Sie SQL Server 2012 oder 2014 Enterprise verwenden, das für Images für transaktionale Arbeitslasten optimiert ist. Wählen Sie A7 aus, wenn Sie SQL Server 2012 oder 2014 Enterprise verwenden, das für Images für Data Warehousing-Arbeitslasten optimiert ist. Die ausgewählte Größe begrenzt die Anzahl der Datenträger, die Sie konfigurieren können. Die aktuellsten Informationen zu verfügbaren Größen virtueller Computer und der Anzahl an Datenträgern, die Sie zu einem virtuellen Computer hinzufügen können, finden Sie unter [Größen virtueller Computer für Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Preisinformationen finden Sie unter [Preise für virtuelle Computer](http://azure.microsoft.com/pricing/details/virtual-machines/).

    Klicken Sie unten rechts auf den Pfeil für die nächste Seite, um fortzufahren.

    ![Konfiguration des virtuellen Computers][2]

5.  Konfigurieren Sie auf der zweiten Seite **Konfiguration des virtuellen Computers** die Ressourcen für Netzwerk, Speicher und Verfügbarkeit:

    -   Wählen Sie im Feld **Cloud-Dienst** die Option **Einen neuen Cloud-Dienst erstellen** aus.
    -   Geben Sie im Feld **DNS-Name des Cloud-Diensts** den ersten Teil eines DNS-Namens Ihrer Wahl ein, sodass insgesamt ein Name des Formats **TESTNAME.cloudapp.net** entsteht.
    -   Wählen Sie im Feld **REGION/AFFINITY GROUP/VIRTUAL NETWORK** eine Region aus, in der dieses virtuelle Image gehostet wird.
    -   Wählen Sie unter **Speicherkonto** ein vorhandenes Speicherkonto aus, oder wählen Sie ein automatisch generiertes aus.
    -   Wählen Sie im Feld **AVAILABILITY SET** den Eintrag **(none)**.
    -   Lesen Sie sich die Preisinformationen durch, und bestätigen Sie diese.

6.	Klicken Sie im Abschnitt **ENDPUNKTE** in die leere Dropdownliste unter **NAME**, wählen Sie **MSSQL** aus, und geben Sie dann die Portnummer der Instanz des Datenbankmoduls ein (**1433** für die Standardinstanz).

7.  Die SQL Server-VM kann auch als IPython Notebook-Server dienen, der in einem späteren Schritt konfiguriert wird. Fügen Sie einen neuen Endpunkt hinzu, um den Port für den IPython Notebook-Server anzugeben. Geben Sie einen Namen in der Spalte **NAME** ein, wählen Sie eine Portnummer für den öffentlichen Port und "9999" für den privaten Port aus.

	Klicken Sie unten rechts auf den Pfeil für die nächste Seite, um fortzufahren.

	![Auswählen der MSSQL- und IPython-Ports][3]

8.  Lassen Sie die Option **VM-Agent installieren** aktiviert, und klicken Sie auf das Häkchen in der unteren rechten Ecke des Assistenten, um die VM-Bereitstellung abzuschließen.

	`![Letzte Optionen für virtuelle Computer][4]

9.  Warten Sie, während Azure den virtuellen Computer vorbereitet. Der virtuelle Computer wird folgende Phasen durchlaufen:

    -   Wird gestartet (Bereitstellung)
    -   Beendet
    -   Wird gestartet (Bereitstellung)
    -   Wird ausgeführt (Bereitstellung)
    -   Wird ausgeführt

##<a name="RemoteDesktop"></a>Öffnen des virtuellen Computers mithilfe von Remotedesktop und vollständige Einrichtung

1.  Wenn die Bereitstellung abgeschlossen ist, klicken Sie auf den Namen des virtuellen Computers, um zur Seite "DASHBOARD" zu gelangen. Klicken Sie unten auf der Seite auf **Verbinden**.

2.  Öffnen Sie die RPD-Datei mit dem Windows-Remotedesktop-Programm (`%windir%\system32\mstsc.exe`).

3.  Geben Sie im Dialogfeld **Windows-Sicherheit** das Kennwort für das lokale Administratorkonto an, das Sie zuvor festgelegt haben. (Sie werden möglicherweise dazu aufgefordert, die Anmeldedaten des virtuellen Computers zu überprüfen.)

4.  Beim ersten Anmelden auf dem virtuellen Computer müssen eventuell einige Prozesse ausgeführt werden, darunter die Einrichtung Ihres Desktops, Windows-Updates und die Fertigstellung der ersten Windows-Konfigurationsaufgaben (sysprep). Nachdem Windows-sysprep abgeschlossen ist, stellt die SQL Server-Einrichtung die Konfigurationsaufgaben fertig. Diese Tasks können während der Ausführung eine Verzögerung von wenigen Minuten verursachen. `SELECT @@SERVERNAME` gibt den richtigen Namen möglicherweise erst zurück, wenn die Einrichtung von SQL Server abgeschlossen ist, und SQL Server Management Studio wird auf der Startseite möglicherweise nicht angezeigt.

Nachdem Sie über Windows Remotedesktop mit dem virtuellen Computer verbunden sind, funktioniert der virtuelle Computer wie jeder andere Computer. Stellen Sie ganz normal eine Verbindung zur Standardinstanz von SQL Server mit SQL Server Management Studio, das auf dem virtuellen Computer ausgeführt wird, her.

##<a name="InstallIPython"></a>Installieren von IPython Notebook und anderen Tools

Zum Konfigurieren der neuen SQL Server-VM als IPython Notebook-Server und zum Installieren zusätzlicher unterstützender Tools wie AzCopy, Azure-Speicher-Explorer, hilfreicher Python-Pakete für Data Science und anderer steht ein spezielles Anpassungsskript bereit. So führen Sie die Installation durch:

- Klicken Sie mit der rechten Maustaste auf das Windows-Startmenü, und klicken dann Sie auf **Eingabeaufforderung (Administrator)**.
- Kopieren Sie die folgenden Befehle, und fügen Sie sie an der Eingabeaufforderung ein.

    	set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
    	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

- Geben Sie, wenn Sie dazu aufgefordert werden, ein Kennwort Ihrer Wahl für den IPython Notebook-Server ein.
- Das Anpassungsskript automatisiert mehrere Nachinstallationsprozeduren, z. B.:
	+ Installation und Einrichtung des IPython Notebook-Servers
	+ Öffnen der TCP-Ports in der Windows-Firewall für die zuvor erstellten Endpunkte:
	+ Für die SQL Server-Remotekonnektivität
	+ Für die IPython Notebook-Server-Remotekonnektivität
	+ Abrufen von Beispiel-IPython Notebooks und -SQL-Skripts
	+ Herunterladen und Installieren von nützlichen Python-Paketen für Data Science
	+ Herunterladen und Installieren von Azure-Tools wie AzCopy und Azure-Speicher-Explorer <br>
- Sie können über einen beliebigen Browser lokal oder remote auf IPython Notebook zugreifen und es ausführen. Verwenden Sie dazu eine URL im Format `https://<virtual_machine_DNS_name>:<port>`, wobei der Port der öffentliche IPython-Port ist, den Sie beim Bereitstellen des virtuellen Computers ausgewählt haben.
- Der IPython Notebook-Server wird als Hintergrunddienst ausgeführt und bei einem Neustart des virtuellen Computers ebenfalls automatisch neu gestartet.

##<a name="Optional"></a>Anfügen eines Datenträgers

Wenn Ihr VM-Image keine Datenträger enthält, d. h. keine anderen Datenträger als die Laufwerke C (Datenträger für das Betriebssystem) und D (temporärer Datenträger), müssen Sie mindestens einen Datenträger zum Speichern der Daten hinzufügen. Das VM-Image für SQL Server 2012 SP2 Enterprise Optimized für DataWarehousing Workloads ist bereits mit zusätzlichen Datenträgern für die Daten- und Protokolldateien von SQL Server vorkonfiguriert.

 >[AZURE.NOTE]Verwenden Sie Laufwerk D nicht zum Speichern von Daten. Wie der Name schon sagt, bietet dieser Datenträger nur temporäre Speicherung. Er ermöglicht keine Redundanz oder Sicherung, da er sich nicht im Azure-Speicher befindet.

Um zusätzliche Datenträger anzufügen, führen Sie die Schritte unter [Anfügen eines Datenträgers an einen virtuellen Windows-Computer](storage-windows-attach-disk.md) durch:

1. Anfügen leerer Datenträger an den in den vorherigen Schritten bereitgestellten virtuellen Computer
2. Initialisieren der neuen Datenträger auf dem virtuellen Computer


##<a name="SSMS"></a>Herstellen einer Verbindung mit SQL Server Management Studio und Aktivieren der Authentifizierung im gemischten Modus

Das SQL Server-Datenbankmodul kann ohne Domänenumgebung keine Windows-Authentifizierung nutzen. Um eine Verbindung zum Datenbankmodul von einem anderen Computer aus herzustellen, konfigurieren Sie SQL Server für die Authentifizierung mit gemischtem Modus. Die Authentifizierung mit gemischtem Modus ermöglicht sowohl die SQL Server-Authentifizierung als auch die Windows-Authentifizierung. Der SQL-Authentifizierungsmodus ist erforderlich, um Daten direkt aus den Datenbanken des virtuellen SQL Server-Computers in [Azure Machine Learning Studio](https://studio.azureml.net) mithilfe des Reader-Moduls zu erfassen.

1.  Verwenden Sie, während der virtuelle Computer über Remotedesktop verbunden ist, den Windows-Bereich **Suche**, und geben Sie dort **SQL Server Management Studio** (SSMS) ein. Klicken Sie auf diese Option, um SQL Server Management Studio (SSMS) zu starten. Sie können für die zukünftige Verwendung eine Verknüpfung mit SSMS auf Ihrem Desktop hinzufügen.

    ![SSMS starten][5]

    Wenn Sie Management Studio erstmalig öffnen, muss dieses eine Management Studio-Benutzerumgebung erstellen. Dies kann einige Zeit dauern.

2.  Beim Öffnen wird in Management Studio das Dialogfeld **Verbindung mit Server herstellen** angezeigt. Geben Sie im Feld **Servername** den Namen des virtuellen Computers ein, um das Datenbankmodul mit dem Objekt-Explorer zu verbinden. (Anstelle des Namen des virtuellen Computers können Sie auch **(lokal)** oder einen einzigen Punkt als **Servername** verwenden. Wählen Sie **Windows-Authentifizierung**, und lassen Sie ***your_VM_name*\\your_local_administrator** im Feld **Benutzername**. Klicken Sie auf **Verbinden**.

    ![Mit Server verbinden][6]

	<br>

	 >[AZURE.TIP]Sie können den SQL Server-Authentifizierungsmodus über einen Windows-Registrierungsschlüssel oder in SQL Server Management Studio ändern. Starten Sie zum Ändern des Authentifizierungsmodus über einen Registrierungsschlüssel eine **Neue Abfrage**, und führen Sie das folgende Skript aus:

		USE master
    	go

    	EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
    	go


	So ändern Sie den Authentifizierungsmodus mit SQL Server Management Studio:

3.  Klicken Sie im Objekt-Explorer von SQL Server Management Studio mit der rechten Maustaste auf die Instanz von SQL Server (den Namen des virtuellen Computers), und klicken Sie dann auf **Eigenschaften**.

    ![Servereigenschaften][7]

4.  Wählen Sie auf der Seite **Sicherheit**, unter **Serverauthentifizierung**, die Option **SQL Server- und Windows-Authentifizierungsmodus**, und klicken Sie dann auf **OK**.

    ![Authentifizierungsmodus auswählen][8]

5.  Klicken Sie im Dialogfeld von SQL Server Management Studio auf **OK**, um zu bestätigen, dass es erforderlich ist, SQL Server neu zu starten.

6.  Klicken Sie im Objekt-Explorer mit der rechten Maustaste auf den Server, und klicken Sie dann auf **Neu starten**. (Wenn SQL Server-Agent ausgeführt wird, muss dieser ebenfalls neu gestartet werden.)

    ![Neu starten][9]

7.  Klicken Sie im Dialogfeld von SQL Server Management Studio auf **Ja**, um zu bestätigen, dass SQL Server neu gestartet werden soll.

##<a name="Logins"></a>Erstellen von Anmeldenamen für die SQL Server-Authentifizierung

Um von einem anderen Computer aus eine Verbindung zum Datenbankmodul herzustellen, müssen Sie mindestens einen Anmeldenamen für die SQL Server-Authentifizierung erstellen.

> [AZURE.TIP]Sie können programmgesteuert oder mithilfe von SQL Server Management Studio neue SQL Server-Anmeldungen erstellen. Um programmgesteuert einen neuen sysadmin-Benutzer mit SQL-Authentifizierung zu erstellen, starten Sie eine **Neue Abfrage** und führen das folgende Skript aus. Ersetzen Sie <new user name> und <new password> durch den gewünschten Benutzernamen und das gewünschte Kennwort. Passen Sie die Kennwortrichtlinie ggf. an (der Beispielcode deaktiviert die Richtlinienprüfung und den Ablauf von Kennwörtern). Weitere Informationen zu Anmeldenamen für SQL Server finden Sie unter [Erstellen eines Anmeldenamens](http://msdn.microsoft.com/library/aa337562.aspx).

    USE master
    go

    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
    	CHECK_POLICY = OFF,
    	CHECK_EXPIRATION = OFF;

    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';

So erstellen Sie mit SQL Server Management Studio neue SQL Server-Anmeldungen:

1.  Erweitern Sie in SQL Server Management Studio-Objekt-Explorer den Ordner der Serverinstanz, in der der neue Anmeldename erstellt werden soll.

2.  Klicken Sie mit der rechten Maustaste auf den Ordner **Sicherheit**, zeigen Sie auf **Neu**, und wählen Sie **Anmeldung** aus.

    ![Neue Anmeldung][10]

3.  Geben Sie im Dialogfeld **Anmeldung – Neu** auf der Seite **Allgemein** im Feld **Anmeldename** den Namen des neuen Benutzers ein.

4.  Wählen Sie **SQL Server-Authentifizierung**.

5.  Geben Sie im Feld **Kennwort** ein Kennwort für den neuen Benutzer ein. Geben Sie dieses Kennwort erneut im Feld **Kennwort bestätigen** ein.

6.  Wählen Sie **Kennwortrichtlinie erzwingen** (empfohlen) aus, um die Kennwortrichtlinienoptionen bezüglich Komplexität und Durchführung durchzusetzen. Dies ist eine Standardoption, wenn die SQL Server-Authentifizierung ausgewählt wurde.

7.  Wählen Sie **Ablauf des Kennwortes erzwingen** (empfohlen) aus, um die Kennwortrichtlinienoptionen bezüglich des Ablaufs durchzusetzen. Kennwortrichtlinie erzwingen muss ausgewählt werden, um dieses Kontrollkästchen zu aktivieren. Dies ist eine Standardoption, wenn die SQL Server-Authentifizierung ausgewählt wurde.

8.  Wählen Sie **Benutzer muss das Kennwort bei der nächsten Anmeldung ändern**, damit der Benutzer gezwungen wird, ein neues Kennwort nach der erstmaligen Verwendung des Anmeldenamens zu erstellen (Wird empfohlen, falls dieser Anmeldenamen von einer anderen Person verwendet wird. Wenn Sie den Anmeldenamen selbst verwenden werden, sollten Sie diese Option nicht auswählen). Ablauf des Kennwortes erzwingen muss ausgewählt werden, um dieses Kontrollkästchen zu aktivieren. Dies ist eine Standardoption, wenn die SQL Server-Authentifizierung ausgewählt wurde.

9.  Wählen Sie aus der Liste **Standarddatenbank** eine Standarddatenbank für den Anmeldenamen aus. **master** ist die Standardoption. Falls Sie noch keine Benutzerdatenbank erstellt haben, lassen Sie diese Einstellung auf **master**.

10. Belassen Sie in der Liste **Standardsprache** den Wert **Standard**.

    ![Anmeldenameneigenschaften][11]

11. Falls es sich um den ersten Anmeldenamen handelt, den Sie erstellen, werden Sie diesen Anmeldenamen wahrscheinlich als SQL Server-Administrator bezeichnen. Prüfen Sie in diesem Fall **sysadmin** auf der Seite **Serverrollen**.

    **Sicherheitshinweis:** Mitglieder der festen Serverrolle sysadmin haben vollständige Kontrolle über das Datenbankmodul. Sie sollten die Mitgliedschaft zu dieser Rolle sorgfältig beschränken.

    ![sysadmin][12]

12. Klicken Sie auf OK.

##<a name="DNS"></a>Bestimmen des DNS-Namens des virtuellen Computers

Um sich von einem anderen Computer aus mit dem SQL Server-Datenbankmodul zu verbinden, müssen Sie den DNS-Namen (Domain Name System) des virtuellen Computers kennen. (Dies ist der Name, den das Internet verwendet, um den virtuellen Computer zu identifizieren. Sie können die IP-Adresse verwenden, aber die IP-Adresse ändert sich möglicherweise, wenn Azure aufgrund von Redundanz oder Wartungsarbeiten Ressourcen verschiebt. Der DNS-Name bleibt bestehen, da er zu einer neuen IP-Adresse weitergeleitet werden kann.)

1.  Wählen Sie im Azure-Verwaltungsportal (oder aus dem vorherigen Schritt) **VIRTUELLE COMPUTER**.

2.  Kopieren Sie auf der Seite **INSTANZEN VIRTUELLER COMPUTER** in der Spalte **DNS-NAME** den DNS-Namen für den virtuellen Computer, der auf **http://** folgt. (In der Benutzeroberfläche wird möglicherweise nicht der gesamte Name angezeigt, aber Sie können mit der rechten Maustaste darauf klicken und diese kopieren.)

##<a name="cde"></a>Verbinden mit dem Datenbankmodul von einem anderen Computer aus

1.  Öffnen Sie auf einem mit dem Internet verbundenen Computer SQL Server Management Studio.

2.  Geben Sie im Dialogfeld **Verbindung mit Server herstellen** oder **Verbindung mit Datenbankmodul herstellen** im Feld **Servername** den DNS-Namen des virtuellen Computers ein (in vorherigen Aufgabe festgelegt) sowie die Portnummer eines öffentlichen Endpunkts im Format *DNSName,Portnummer* wie etwa **tutorialtestVM.cloudapp.net,57500**.

3.  Wählen Sie im Feld **Authentifizierung** den Eintrag **SQL Server-Authentifizierung**.

4.  Geben Sie im Feld **Anmeldename** den Anmeldenamen ein, den Sie vorher erstellt haben.

5.  Geben Sie im Feld **Kennwort** das Kennwort des Anmeldenamens ein, den Sie vorher erstellt haben.

6.  Klicken Sie auf **Verbinden**.

##<a name="amlconnect"></a>Herstellen einer Verbindung von Azure Machine Learning zum Datenbankmodul

In späteren Phasen des Advanced Analytics Process and Technology (ADAPT) verwenden Sie [Azure Machine Learning Studio](https://studio.azureml.net) zum Erstellen und Bereitstellen der Machine Learning-Modelle. Verwenden Sie zum Erfassen von Daten aus den SQL Server-VM-Datenbanken direkt in Azure Machine Learning für das Training oder die Bewertung das Reader-Modul in einem neuen [Azure Machine Learning Studio](https://studio.azureml.net)-Experiment. Weitere Informationen finden Sie über die Anleitungslinks des Advanced Analytics Process and Technology (ADAPT). Eine Einführung finden Sie unter [Was ist Azure Machine Learning Studio?](machine-learning-what-is-ml-studio.md).

2.	Wählen Sie im Bereich **Properties** des [Reader-Moduls](https://msdn.microsoft.com/library/azure/dn905997.aspx) die Option **Azure SQL Database** aus der Dropdownliste **Data Source** aus.

3.	Geben Sie im Textfeld **Database server name** die Zeichenfolge `tcp:<DNS name of your virtual machine>,1433` ein.

4.	Geben Sie den SQL-Benutzernamen im Textfeld **Server user account name** ein.

5.	Geben Sie das Kennwort für den SQL-Benutzer im Textfeld **Server user account password** ein.

	![Azure ML-Reader][13]

##<a name="shutdown"></a>Herunterfahren und Freigeben von nicht genutzten virtuellen Computern

Virtuelle Azure-Computer werden **nach Nutzung abgerechnet**. Damit Sie nicht für ungenutzte virtuelle Computer bezahlen müssen, sollten Sie diese in den Status **Angehalten (Freigegeben)** versetzen.

> [AZURE.NOTE]Beim Herunterfahren virtueller Computer aus der VM heraus (mithilfe der Windows-Energieoptionen) wird der virtuelle Computer beendet, er bleibt jedoch weiterhin zugeordnet. Um sicherzustellen, dass virtuelle Computer nicht abgerechnet werden, beenden Sie sie immer im [Azure-Verwaltungsportal](http://manage.windowsazure.com/). Sie können die VM auch über PowerShell beenden, indem Sie ShutdownRoleOperation mit der "PostShutdownAction" "StoppedDeallocated" aufrufen.

So fahren Sie den virtuellen Computer herunter und heben die Zuordnung auf:

1. Melden Sie sich auf dem [Azure-Verwaltungsportal](http://manage.windowsazure.com/) bei Ihrem Konto an.  

2. Wählen Sie in der Navigationsleiste links **VIRTUAL MACHINES** aus.

3. Klicken Sie in der Liste der virtuellen Computer auf den Namen des virtuellen Computers, und wechseln Sie zur Seite **DASHBOARD**.

4. Klicken Sie unten auf der Seite auf **SHUTDOWN**.

![Herunterfahren virtueller Computer][15]

Der virtuelle Computer wird freigegeben, aber nicht gelöscht. Sie können den virtuellen Computer jederzeit im Azure-Verwaltungsportal neu starten.

## Ihre Azure SQL Server-VM ist damit einsatzbereit. Wie lauten die nächsten Schritte?

Sie können Ihren virtuellen Computer jetzt in Ihren Data Science-Übungen verwenden. Der virtuelle Computer kann auch als IPython Notebook-Server für das Durchsuchen und Verarbeiten von Daten und für andere Aufgaben in Verbindung mit Azure Machine Learning und dem Advanced Analytics Process and Technology (ADAPT) verwendet werden.

Die nächsten Schritte des Datenanalyseprozesses sind unter [Leitfaden: Erweiterte Datenverarbeitung in Azure](machine-learning-data-science-advanced-data-processing.md) aufgeführt. Dazu gehören auch das Verschieben von Daten nach HDInsight und das Verarbeiten und Extrahieren von Stichproben für den Erhalt von Informationen anhand der Daten mit Azure Machine Learning.


[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png
 

<!---HONumber=July15_HO3-->