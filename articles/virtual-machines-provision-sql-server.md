<properties 
	pageTitle="Bereitstellen eines virtuellen Computers mit SQL Server in Azure" 
	description="In diesem Lernprogramm erfahren Sie, wie Sie einen virtuellen Computer mit SQL Server in Azure erstellen und konfigurieren." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor="monicar"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows-sql-server" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="jeffreyg"/>

# Bereitstellen eines virtuellen Computers mit SQL Server auf Azure #

In der Galerie der virtuellen Computer von Azure sind verschiedene Images zu finden, die Microsoft SQL Server enthalten. Sie können eines der Images virtueller Computer aus der Galerie auswählen, und mit wenigen Klicks können Sie den virtuellen Computer auf Ihrer Azure-Umgebung bereitstellen.

In diesem Lernprogramm lernen Sie Folgendes:

* [Verbinden mit dem Azure-Verwaltungsportal und Bereitstellen eines virtuellen Computers aus dem Katalog](#Provision)
* [Öffnen des virtuellen Computers mithilfe von Remotedesktop und vollständige Einrichtung](#RemoteDesktop)
* [Fertigstellen der Konfigurationsschritte, um mithilfe von SQL Server Management Studio auf einem anderen Computer eine Verbindung zum virtuellen Computer herzustellen](#SSMS)
* [Nächste Schritte](#Optional)

##<a id="Provision">Verbinden mit dem Azure-Verwaltungsportal und Bereitstellen eines virtuellen Computers aus dem Katalog</a>

1. Melden Sie sich auf dem [Azure-Verwaltungsportal](http://manage.windowsazure.com) bei Ihrem Konto an. Wenn Sie kein Azure-Konto haben, sollten Sie die Seite [Kostenlose einmonatige Testversion](http://www.windowsazure.com/pricing/free-trial/) besuchen.

2. Klicken Sie im Azure-Verwaltungsportal unten links auf der Webseite auf **+NEU**, klicken Sie auf **COMPUTE**, klicken Sie auf **VIRTUELLER COMPUTER** und dann auf **AUS GALERIE**.

3. Klicken Sie auf der Seite **Image auswählen** auf **SQL SERVER**. Wählen Sie dann ein SQL Server-Image aus. Klicken Sie am unteren rechten Rand der Seite auf den Pfeil "Weiter". ![Image auswählen][Image34]


Die aktuellsten Informationen zu den unterstützen SQL Server-Images auf Azure finden Sie unter [Erste Schritte mit SQL Server auf Windows Azure-Virtuellen Computern](http://go.microsoft.com/fwlink/p/?LinkId=294720) in der Dokumentation [SQL Server auf Windows Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294719).

   
>[AZURE.NOTE]Wenn Sie über einen virtuellen Computer verfügen, der von einem Plattformimage mit der SQL Server Evaluation-Edition erstellt wurde, können Sie kein Upgrade auf ein minütlich bezahltes Editionsimage in der Galerie durchführen. Sie haben zwei Möglichkeiten: Sie können einen neuen virtuellen Computer mithilfe der minütlich bezahlten Edition von SQL Server aus dem Katalog erstellen und die Datenbankdateien auf diesen neuen virtuellen Computer migrieren. Befolgen Sie dazu die Schritte unter [Migrieren von SQL Server-Datenbankdateien und -Schema zwischen virtuellen Azure-Computern mit Datenträgern](http://go.microsoft.com/fwlink/p/?LinkId=294738). **Oder** aktualisieren Sie unter dem Vertrag [Lizenzmobilität durch Software Assurance für Azure](http://www.windowsazure.com/pricing/license-mobility/) eine vorhandene Instanz von SQL Server Evaluation Edition auf eine andere Edition von SQL Server. Befolgen Sie dazu die Schritte unter [Aktualisieren auf eine andere Edition von SQL Server 2014](http://go.microsoft.com/fwlink/?LinkId=396915). Informationen zum Erwerb der lizenzierten Kopie von SQL Server finden Sie unter [So kaufen Sie SQL Server](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx).


4. Geben Sie zunächst auf der Seite **Konfiguration des virtuellen Computers** die folgenden Informationen ein:
	- EIN **VERÖFFENTLICHUNGSDATUM DER VERSION** Wenn mehrere Images verfügbar sind, wählen Sie das neueste aus.
	- Ein eindeutiger **NAME FÜR DEN VIRTUELLEN COMPUTER**
	- Geben Sie im Feld **NEUER BENUTZERNAME** einen eindeutigen Benutzernamen für das lokale Administratorkonto des Computers ein.
	- Geben Sie in das Feld **NEUES KENNWORT** ein sicheres Kennwort ein. 
	- Geben Sie das Kennwort im Feld **KENNWORT BESTÄTIGEN** nochmals ein.
	- Wählen Sie in der Dropdownliste die geeignete **GRÖSSE** aus. 

	>[AZURE.NOTE]Die Größe des virtuellen Computers wird während der Bereitstellung angegeben: A2 ist die kleinste empfohlene Größe für Produktionsarbeitsauslastungen. Die empfohlene Minimalgröße für einen virtuellen Computer ist A3, wenn SQL Server Enterprise Edition verwendet wird. Wählen Sie A3 oder höher aus, wenn Sie SQL Server Enterprise Edition verwenden. Wählen Sie A4 aus, wenn Sie SQL Server 2012 oder 2014 Enterprise verwenden, das für Images für transaktionale Arbeitslasten optimiert ist. Wählen Sie A7 aus, wenn Sie SQL Server 2012 oder 2014 Enterprise verwenden, das für Images für Data Warehousing-Arbeitslasten optimiert ist. Die ausgewählte Größe begrenzt die Anzahl der Datenträger, die Sie konfigurieren können. Die aktuellsten Informationen zu verfügbaren Größen virtueller Computer und der Anzahl an Datenträgern, die Sie zu einem virtuellen Computer hinzufügen können, finden Sie unter [Größen virtueller Computer für Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).

	Klicken Sie unten rechts auf den Pfeil für die nächste Seite, um fortzufahren.

	![Konfiguration des virtuellen Computers](./media/virtual-machines-provision-sql-server/4VM-Config.png)


5. Konfigurieren Sie auf der zweiten Seite **Konfiguration des virtuellen Computers** die Ressourcen für Netzwerk, Speicher und Verfügbarkeit:
	- Wählen Sie im Feld **Cloud-Dienst** die Option **Einen neuen Cloud-Dienst erstellen** aus.
	- Geben Sie im Feld **DNS-Name des Cloud-Diensts** den ersten Teil eines DNS-Namens Ihrer Wahl ein, sodass insgesamt ein Name des Formats **TESTNAME.cloudapp.net** entsteht. 
	- Wählen Sie ein **ABONNEMENT** aus, wenn Sie über mehrere Abonnements verfügen. Die Auswahl bestimmt, welche **Speicherkonten **verfügbar sind. - Wählen Sie im Feld **REGION/AFFINITY GROUP/VIRTUAL NETWORK** eine Region aus, in der dieses virtuelle Image gehostet wird.
	- Erstellen Sie in **Speicherkonto** automatisch ein Konto, oder wählen Sie ein Konto aus der Liste aus. Ändern Sie das **ABONNEMENT**, um weitere Konten anzuzeigen. 
	- Wählen Sie im Feld **AVAILABILITY SET** den Eintrag **(none)**.
	- Lesen Sie sich die Bedingungen durch, und bestätigen Sie diese.
	

6. Klicken Sie auf den Pfeil zum Aufrufen der nächsten Seite, um fortzufahren.


7. Klicken Sie auf das Häkchen unten rechts, um fortzufahren.

8. Warten Sie, während Azure den virtuellen Computer vorbereitet. Der virtuelle Computer wird folgende Phasen durchlaufen:

	- Wird gestartet (Bereitstellung)
	- Beendet
	- Wird gestartet (Bereitstellung)
	- Wird ausgeführt (Bereitstellung)
	- Wird ausgeführt
	

##<a id="RemoteDesktop">Öffnen des virtuellen Computers mithilfe von Remotedesktop und vollständige Einrichtung</a>

1. Wenn die Bereitstellung abgeschlossen ist, klicken Sie auf den Namen des virtuellen Computers, um zur Seite "DASHBOARD" zu gelangen. Klicken Sie unten auf der Seite auf **Verbinden**.
2. Klicken Sie auf die Schaltfläche **Öffnen**. ![Klicken Sie auf die Schaltfläche "Öffnen"][Image37].

3. Klicken Sie im Dialogfeld **Windows-Sicherheit** auf **Anderes Konto verwenden**. ![Klicken Sie auf "Anderes Konto verwenden"][Image38]
4. Verwenden Sie den Namen des Computers als Domänennamen gefolgt von Ihrem Administratornamen im folgenden Format: `machinename\username`. Geben Sie Ihr Kennwort ein, und stellen Sie eine Verbindung mit dem Computer her.

4. Beim ersten Anmelden werden einige Prozesse ausgeführt, darunter die Einrichtung Ihres Desktops, Windows-Updates und die Fertigstellung der ersten Windows-Konfigurationsaufgaben (sysprep). Nachdem Windows-sysprep abgeschlossen ist, stellt die SQL Server-Einrichtung die Konfigurationsaufgaben fertig. Diese Tasks können während der Ausführung eine Verzögerung von wenigen Minuten verursachen. `SELECT @@SERVERNAME` gibt den richtigen Namen möglicherweise erst zurück, wenn die Einrichtung von SQL Server abgeschlossen ist. Zudem wird SQL Server Management Studio möglicherweise nicht auf der Startseite angezeigt.

Nachdem Sie über Windows Remotedesktop mit dem virtuellen Computer verbunden sind, funktioniert der virtuelle Computer wie jeder andere Computer. Stellen Sie ganz normal eine Verbindung zur Standardinstanz von SQL Server mit SQL Server Management Studio, das auf dem virtuellen Computer ausgeführt wird, her.

##<a id="SSMS">Fertigstellen der Konfigurationsschritte, um mithilfe von SQL Server Management Studio auf einem anderen Computer eine Verbindung zum virtuellen Computer herzustellen</a>

Bevor Sie eine Verbindung mit der Instanz von SQL Server über das Internet herstellen können, müssen Sie folgende Aufgaben abschließen, die in den nächsten Abschnitten beschrieben werden:

- [Erstellen eines TCP-Endpunkts für den virtuellen Computer](#Endpoint)
- [Öffnen der TCP-Ports in der Windows-Firewall](#FW)
- [Konfigurieren von SQL Server für das Abhören des TCP-Protokolls](#TCP)
- [Konfigurieren von SQL Server für die Authentifizierung mit gemischtem Modus](#Mixed)
- [Erstellen von Anmeldenamen für die SQL Server-Authentifizierung](#Logins)
- [Bestimmen des DNS-Namens des virtuellen Computers](#DNS)
- [Verbinden mit dem Datenbankmodul von einem anderen Computer aus](#cde)
- [Verbinden mit dem Datenbankmodul von Ihrer Anwendung aus](#cdea)

Der Verbindungspfad wird von folgendem Diagramm zusammengefasst:

![Verbinden mit einem virtuellen SQL Server-Computer][Image8b]

##<a id="Endpoint">Erstellen eines TCP-Endpunkts für den virtuellen Computer</a>

Der virtuelle Computer benötigt einen Endpunkt, um nach eingehender TCP-Kommunikation zu lauschen. Dieser Azure-Konfigurationsschritt leitet den eingehenden Datenverkehr des TCP-Ports zu einem TCP-Port, auf den der virtuelle Computer zugreifen kann.

1. Klicken Sie im Azure-Verwaltungsportal auf **VIRTUELLE COMPUTER**.
	
2. Klicken Sie auf den neu erstellten virtuellen Computer. Informationen zu Ihrem virtuellen Computer werden angezeigt.
	
3. Wählen Sie oben auf der Seite die Seite **ENDPUNKTE** aus, und klicken Sie dann unten auf **HINZUFÜGEN**.
	
4. Klicken Sie auf der Seite **Einem virtuellen Computer einen Endpunkt hinzufügen** auf **Eigenständigen Endpunkt hinzufügen**, und klicken Sie dann auf „Weiter“, um fortzufahren.
	
5. Geben Sie auf der Seite zum Angeben der Details des Endpunkts folgende Informationen ein:

	- Geben Sie unter **NAME** einen Namen für den Endpunkt ein.
	- Wählen Sie im Feld **PROTOKOLL** den Eintrag **TCP**. Sie können zudem **57500** im Feld **ÖFFENTLICHER PORT** eingeben. Sie können den Standard-Überwachungsport **1433** von SQL Server im Feld **Privater Port** eingeben. Viele Organisationen verwenden unterschiedliche Portnummern, um böswillige Sicherheitsangriffe zu vermeiden. 

6. Klicken Sie auf das Häkchen, um fortzufahren. Der Endpunkt wurde erstellt.

##<a id="FW">Öffnen von TCP-Ports in der Windows-Firewall für die Standardinstanz des Datenbankmoduls</a>

1. Stellen Sie mit Windows Remotedesktop eine Verbindung zum virtuellen Computer her. Geben Sie nach der Anmeldung auf dem Startbildschirm **WF.msc** ein, und drücken Sie dann die EINGABETASTE. 

	![Starten des Firewall-Programms][Image12]
2. Klicken Sie in **Windows-Firewall mit erweiterter Sicherheit** im linken Bereich mit der rechten Maustaste auf **Eingehende Regeln**, und klicken Sie dann im Aktionsbereich auf **Neue Regel**.

	![Neue Regel][Image13]

3. Wählen Sie im Dialogfeld **Assistent für neue eingehende Regel ** unter **Regeltyp** die Option **Port** aus, und klicken Sie dann auf **Weiter**.

4. Verwenden Sie im Dialogfeld **Protokolle und Ports** die Standardeinstellung **TCP**. Wählen Sie im Feld **Bestimmte lokale Ports** die gewünschten Ports aus, und geben Sie dann die Portnummer der Instanz des Datenbankmoduls ein (**1433** für die Standardinstanz oder die von Ihnen ausgewählte Nummer für den privaten Port im Endpunktschritt).

	![TCP-Port 1433][Image14]

5. Klicken Sie auf **Weiter**.

6. Klicken Sie im Dialogfeld **Aktion** auf **Verbindung zulassen** und anschließend auf **Weiter**.

	**Sicherheitshinweis:** Die Auswahl von **Verbindung zulassen, wenn sie sicher ist** kann für zusätzliche Sicherheit sorgen. Wählen Sie diese Option, wenn Sie zusätzliche Sicherheitsoptionen in Ihrer Umgebung konfigurieren möchten.

	![Verbindungen zulassen][Image15]

7. Klicken Sie im Dialogfeld **Profil** auf **Öffentlich** und anschließend auf **Weiter**.

    **Sicherheitshinweis:** Die Auswahl von **Öffentlich** ermöglicht den Zugriff über das Internet. Sie sollten möglichst ein restriktiveres Profil auswählen.

	![Öffentliches Profil][Image16]

8. Geben Sie im Dialogfeld **Name** einen Namen und eine Beschreibung für diese Regel ein, und klicken Sie dann auf **Fertig stellen**.

	![Regelname][Image17]

Öffnen Sie gegebenenfalls zusätzliche Ports für andere Komponenten. Weitere Informationen finden Sie unter [Konfigurieren der Windows-Firewall für den SQL Server-Zugriff](http://msdn.microsoft.com/library/cc646023.aspx).


###<a id="TCP">Konfigurieren von SQL Server für das Abhören des TCP-Protokolls</a>

1. Geben Sie während der Verbindung mit dem virtuellen Computer auf der Startseite **SQL Server-Konfigurations-Manager ** ein, und drücken Sie die EINGABETASTE.
	
	![SSCM öffnen][Image9]

2. Erweitern Sie im SQL Server-Konfigurationsmanager im Konsolenbereich **SQL Server-Netzwerkkonfiguration**.

3. Klicken Sie im Konsolenbereich auf **Protokolle für MSSQLSERVER** (der Standardinstanzenname). Klicken Sie im Detailbereich mit der rechten Maustaste auf TCP, dies sollte standardmäßig für Galerie-Images aktiviert sein. Klicken Sie für eigene Images auf **Aktivieren**, falls der Status Deaktiviert ist.

	![TCP aktivieren][Image10]

5. Klicken Sie im linken Bereich auf **SQL Server-Dienste**. Klicken Sie im Detailbereich mit der rechten Maustaste auf **SQL Server (_Instanzenname_)** (die Standardinstanz ist **SQL Server (MSSQLSERVER)**). Klicken Sie dann auf **Neu starten**, um die Instanz von SQL Server zu beenden und neu zu starten.

	![Datenbankmodul neu starten][Image11]

7. Schließen Sie den SQL Server-Konfigurations-Manager.

Weitere Informationen zur Aktivierung von Protokollen für SQL Server-Datenbankmodule finden Sie unter [Aktivieren oder Deaktivieren eines Servernetzwerkprotokolls](http://msdn.microsoft.com/library/ms191294.aspx).

###<a id="Mixed">Konfigurieren von SQL Server für die Authentifizierung im gemischten Modus</a>

Das SQL Server-Datenbankmodul kann ohne Domänenumgebung keine Windows-Authentifizierung nutzen. Um eine Verbindung zum Datenbankmodul von einem anderen Computer aus herzustellen, konfigurieren Sie SQL Server für die Authentifizierung mit gemischtem Modus. Die Authentifizierung mit gemischtem Modus ermöglicht sowohl die SQL Server-Authentifizierung als auch die Windows-Authentifizierung. (Die Konfiguration der Authentifizierung mit gemischtem Modus ist möglicherweise nicht notwendig, wenn Sie ein Azure Virtual Network konfiguriert haben. Weitere Informationen erhalten Sie unter [Connectivity Considerations for SQL Server in Azure Virtual Machines](http://go.microsoft.com/fwlink/?LinkId=294723) (Konnektivitätsüberlegungen für SQL Server in virtuellen Azure-Computern, in englischer Sprache) in der Dokumentation [SQL Server in Azure Virtual Machines](http://go.microsoft.com/fwlink/?LinkId=294719).

1. Geben Sie während der Verbindung mit dem virtuellen Computer auf der Startseite **SQL Server 2014 Management Studio** ein, und klicken Sie auf das ausgewählte Symbol.

	![SSMS starten][Image18]

	Wenn Sie Management Studio erstmalig öffnen, muss dieses eine Management Studio-Benutzerumgebung erstellen. Dies kann einige Zeit dauern.

2. Management Studio zeigt das Dialogfeld **Verbindung mit Server herstellen** an. Geben Sie im Feld **Servername** den Namen des virtuellen Computers ein, um das Datenbankmodul mit dem Objekt-Explorer zu verbinden. (Anstelle des Namen des virtuellen Computers können Sie auch **(lokal)** oder einen einzigen Punkt als **Servername** verwenden. Wählen Sie **Windows-Authentifizierung**, und lassen Sie **_your_VM_name_\your_local_administrator** im Feld **Benutzername**. Klicken Sie auf **Verbinden**.

	![Mit Server verbinden][Image19]

3. Klicken Sie im Objekt-Explorer von SQL Server Management Studio mit der rechten Maustaste auf die Instanz von SQL Server (den Namen des virtuellen Computers), und klicken Sie dann auf **Eigenschaften**.

	![Servereigenschaften][Image20]

4. Wählen Sie auf der Seite **Sicherheit**, unter **Serverauthentifizierung**, die Option **SQL Server- und Windows-Authentifizierungsmodus**, und klicken Sie dann auf **OK**.

	![Authentifizierungsmodus auswählen][Image21]

5. Klicken Sie im Dialogfeld von SQL Server Management Studio auf **OK**, um zu bestätigen, dass es erforderlich ist, SQL Server neu zu starten.

6. Klicken Sie im Objekt-Explorer mit der rechten Maustaste auf den Server, und klicken Sie dann auf **Neu starten**. (Wenn SQL Server-Agent ausgeführt wird, muss dieser ebenfalls neu gestartet werden.)

	![Neu starten][Image22]

7. Klicken Sie im Dialogfeld von SQL Server Management Studio auf **Ja**, um zu bestätigen, dass SQL Server neu gestartet werden soll.

###<a id="Logins">Erstellen von Anmeldenamen für die SQL Server-Authentifizierung</a>

Um von einem anderen Computer aus eine Verbindung zum Datenbankmodul herzustellen, müssen Sie mindestens einen Anmeldenamen für die SQL Server-Authentifizierung erstellen.

1. Erweitern Sie in SQL Server Management Studio-Objekt-Explorer den Ordner der Serverinstanz, in der der neue Anmeldename erstellt werden soll.

2. Klicken Sie mit der rechten Maustaste auf den Ordner **Sicherheit**, zeigen Sie auf **Neu**, und wählen Sie **Anmeldung...** aus.

	![Neue Anmeldung][Image23]

3. Geben Sie im Dialogfeld **Anmeldung – Neu** auf der Seite **Allgemein** im Feld **Anmeldename** den Namen des neuen Benutzers ein.

4. Wählen Sie **SQL Server-Authentifizierung**.

5. Geben Sie im Feld **Kennwort** ein Kennwort für den neuen Benutzer ein. Geben Sie dieses Kennwort erneut im Feld **Kennwort bestätigen** ein.

6. Wählen Sie **Kennwortrichtlinie erzwingen** (empfohlen) aus, um die Kennwortrichtlinienoptionen bezüglich Komplexität und Durchführung durchzusetzen. Dies ist eine Standardoption, wenn die SQL Server-Authentifizierung ausgewählt wurde.

7. Wählen Sie **Ablauf des Kennwortes erzwingen** (empfohlen) aus, um die Kennwortrichtlinienoptionen bezüglich des Ablaufs durchzusetzen. Kennwortrichtlinie erzwingen muss ausgewählt werden, um dieses Kontrollkästchen zu aktivieren. Dies ist eine Standardoption, wenn die SQL Server-Authentifizierung ausgewählt wurde.

8. Wählen Sie **Benutzer muss das Kennwort bei der nächsten Anmeldung ändern**, damit der Benutzer gezwungen wird, ein neues Kennwort nach der erstmaligen Verwendung des Anmeldenamens zu erstellen (Wird empfohlen, falls dieser Anmeldenamen von einer anderen Person verwendet wird. Wenn Sie den Anmeldenamen selbst verwenden werden, sollten Sie diese Option nicht auswählen). Ablauf des Kennwortes erzwingen muss ausgewählt werden, um dieses Kontrollkästchen zu aktivieren. Dies ist eine Standardoption, wenn die SQL Server-Authentifizierung ausgewählt wurde.

9. Wählen Sie aus der Liste **Standarddatenbank** eine Standarddatenbank für den Anmeldenamen aus. **master** ist die Standardoption. Falls Sie noch keine Benutzerdatenbank erstellt haben, lassen Sie diese Einstellung auf **master**.

10. Belassen Sie in der Liste **Standardsprache** den Wert **Standard**.
    
	![Anmeldenameneigenschaften][Image24]

11. Falls es sich um den ersten Anmeldenamen handelt, den Sie erstellen, werden Sie diesen Anmeldenamen wahrscheinlich als SQL Server-Administrator bezeichnen. Prüfen Sie in diesem Fall **sysadmin** auf der Seite **Serverrollen**.

	**Sicherheitshinweis:** Mitglieder der festen Serverrolle sysadmin haben vollständige Kontrolle über das Datenbankmodul. Sie sollten die Mitgliedschaft zu dieser Rolle sorgfältig beschränken.

	![sysadmin][Image25]

12. Klicken Sie auf OK.

Weitere Informationen zu Anmeldenamen für SQL Server finden Sie unter [Erstellen eines Anmeldenamens](http://msdn.microsoft.com/library/aa337562.aspx).



###<a id="DNS">Bestimmen des DNS-Namens des virtuellen Computers</a>

Um sich von einem anderen Computer aus mit dem SQL Server-Datenbankmodul zu verbinden, müssen Sie den DNS-Namen (Domain Name System) des virtuellen Computers kennen. (Dies ist der Name, den das Internet verwendet, um den virtuellen Computer zu identifizieren. Sie können die IP-Adresse verwenden, aber die IP-Adresse ändert sich möglicherweise, wenn Azure aufgrund von Redundanz oder Wartungsarbeiten Ressourcen verschiebt. Der DNS-Name bleibt bestehen, da er zu einer neuen IP-Adresse weitergeleitet werden kann.)

1. Wählen Sie im Azure-Verwaltungsportal (oder aus dem vorherigen Schritt) **VIRTUELLE COMPUTER**. 

2. Suchen und kopieren Sie auf der Seite **INSTANZEN VIRTUELLER COMPUTER** in der Spalte **Schnelleinsicht** den DNS-Namen für den virtuellen Computer.

![DNS-Name][Image35]
	

### <a id="cde">Verbinden mit dem Datenbankmodul von einem anderen Computer aus</a>
 
1. Öffnen Sie auf einem mit dem Internet verbundenen Computer SQL Server Management Studio.
2. Geben Sie im Dialogfeld **Verbindung mit Server herstellen ** oder **Verbindung mit Datenbankmodul herstellen** im Feld **Servername** den DNS-Namen des virtuellen Computers ein (in der vorherigen Aufgabe festgelegt) sowie die Portnummer eines öffentlichen Endpunkts im Format *DNSName, Portnummer* wie etwa **tutorialtestVM.cloudapp.net,57500**. Um die Portnummer zu erhalten, melden Sie sich beim Azure-Verwaltungsportal an und suchen den virtuellen Computer. Klicken Sie auf dem Dashboard auf **ENDPUNKTE**, und verwenden Sie den **ÖFFENTLICHEN PORT**, der **MSSQL** zugewiesen ist.![Öffentlicher Port][Image36]
3. Wählen Sie im Feld **Authentifizierung** den Eintrag **SQL Server-Authentifizierung**.
5. Geben Sie im Feld **Anmeldename** den Anmeldenamen ein, den Sie vorher erstellt haben.
6. Geben Sie im Feld **Kennwort** das Kennwort des Anmeldenamens ein, den Sie vorher erstellt haben.
7. Klicken Sie auf **Verbinden**.

	![Verbinden mit SSMS][Image33]

### <a id="cdea">Verbinden mit dem Datenbankmodul von Ihrer Anwendung aus</a>

Wenn Sie eine Verbindung zu einer Instanz von SQL Server, die auf einem virtuellen Azure-Computer ausgeführt wird, mithilfe von Management Studio herstellen können, sollten Sie in der Lage sein, eine Verbindung mithilfe einer Verbindungszeichenfolge wie unten herzustellen.

	connectionString="Server=<DNS_Name>;Integrated Security=false;User ID=<login_name>;Password=<your_password>;"providerName="System.Data.SqlClient"

Weitere Informationen finden Sie unter [How to Troubleshoot Connecting to the SQL Server Database Engine](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) (So beheben Sie Fehler bei der Verbindung zum SQL Server-Datenbankmodul, in englischer Sprache).

##<a id="Optional">Nächste Schritte</a>
Sie haben jetzt erfahren, wie Sie einen SQL Server auf einem virtuellen Azure-Computer mithilfe des Plattform-Image erstellen und konfigurieren. Wenn Sie SQL Server auf Azure Virtual Machines verwenden, empfehlen wir, der detaillierten Anleitung zu folgen, die Sie in der Dokumentation zu [SQL Server auf Windows Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294719) in der Bibliothek finden. Diese Dokumentation enthält zahlreiche Artikel und Lernprogramme mit detaillierten Anleitungen. Diese behandeln die folgenden Themen:

[SQL Server auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/p/?LinkId=294719)

[Erste Schritte mit SQL Server auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/p/?LinkId=294720)

[Bereit für die Migration zu SQL Server auf Azure-Virtuellen Computern](http://go.microsoft.com/fwlink/p/?LinkId=294721)

- Migrieren von SQL Server-Datenbankdateien und -Schema zwischen virtuellen Azure-Computern mit Datenträgern

[Implementierung von SQL Server auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/p/?LinkId=294722)

- Gewusst wie: Kopieren von SQL Server-Daten- und Setupdateien auf einem Datenträger mit dem CSUpload-Tool von einem lokalen Speicherort nach Azure
- Erstellen eines lokalen virtuellen Basiscomputers mit Hyper-V
- Erstellen eines virtuellen SQL Server-Computers in Azure mithilfe des vorhandenen lokalen SQL Server-Datenträgers
- Erstellen eines virtuellen SQL Server-Computers in Azure mithilfe des vorhandenen lokalen SQL Server-Computers 
- Verwenden von PowerShell zum Einrichten eines virtuellen SQL Server-Computers in Azure 
- Gewusst wie: Verwenden eines angefügten Datenträgers zum Speichern von Datenbankdateien

[Konnektivitätsüberlegungen für SQL Server auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/p/?LinkId=294723)

- Lernprogramm: Verbinden mit SQL Server im selben Clouddienst 
- Lernprogramm: Verbinden mit SQL Server in einem anderen Clouddienst 
- Lernprogramm: Herstellen einer Verbindung zwischen ASP.NET-Anwendung und SQL Server in Azure über das virtuelle Netzwerk 

[Leistungsüberlegungen zu SQL Server auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/?LinkId=294724)

[Sicherheitsüberlegungen für SQL Server auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/p/?LinkId=294725)

[Problembehandlung und Überwachung für SQL Server auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/p/?LinkId=294726)

[Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/p/?LinkId=294727)

- Lernprogramm: AlwaysOn-Verfügbarkeitsgruppen in Azure (GUI)
- Lernprogramm: AlwaysOn-Verfügbarkeitsgruppen in Azure (PowerShell)
- Lernprogramm: Listenerkonfiguration für AlwaysOn-Verfügbarkeitsgruppen
- Lernprogramm: Assistent zum Hinzufügen von Azure-Replikaten
- Lernprogramm: Datenbankspiegelung für die Notfallwiederherstellung in Azure
- Lernprogramm: Datenbankspiegelung für die Notfallwiederherstellung in Hybrid-IT 
- Lernprogramm: Datenbankspiegelung für hohe Verfügbarkeit in Azure
- Lernprogramm: Datenbankspiegelung für die Notfallwiederherstellung in Hybrid-IT 
- Beheben von Fehlern bei Verfügbarkeitsgruppenlistenern in Azure

[Sicherung und Wiederherstellung für SQL Server auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/p/?LinkId=294728)

[SQL Server-Business Intelligence auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/p/?LinkId=294729)

- Verwenden von PowerShell zum Erstellen einer Azure-VM mit SQL Server BI und SharePoint 2010
- Verwenden von PowerShell zum Erstellen einer Azure-VM mit SQL Server BI und SharePoint 2013
- Verwenden von PowerShell zum Erstellen einer Azure-VM mit einem Berichtsserver im einheitlichen Modus

[SQL Server Data Warehousing auf virtuellen Azure-Computern](http://msdn.microsoft.com/library/windowsazure/dn387396.aspx)

[Technische Artikel zu SQL Server auf virtuellen Azure-Computern](http://msdn.microsoft.com/library/azure/dn248435.aspx)

- [Whitepaper: Grundlegendes zur Azure SQL-Datenbank und SQL Server in Azure Virtual Machines](http://azure.microsoft.com/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas/)

- [Whitepaper: Anwendungsmuster und Entwicklungsstrategien für SQL Server auf Azure Virtual Machines](http://msdn.microsoft.com/library/azure/dn574746.aspx)

- [Whitepaper: Bereitstellen von SQL Server Business Intelligence auf virtuellen Azure-Computern](http://msdn.microsoft.com/library/windowsazure/dn321998.aspx)

- [Whitepaper: Leitfaden zur Leistung für SQL Server auf virtuellen Azure-Computern](http://msdn.microsoft.com/library/windowsazure/dn248436.aspx)

- [Whitepaper: ReportViewer-Steuerelement von Reporting Services und Berichtsserver, die auf virtuellen Microsoft Azure-Computern basieren](http://msdn.microsoft.com/library/azure/dn753698.aspx)

[Image5]: ./media/virtual-machines-provision-sql-server/5VM-Mode.png
[Image5b]: ./media/virtual-machines-provision-sql-server/5VM-Connect.png
[Image6]: ./media/virtual-machines-provision-sql-server/6VM-Options.png
[Image8b]: ./media/virtual-machines-provision-sql-server/SQLServerinVMConnectionMap.png
[Image9]: ./media/virtual-machines-provision-sql-server/9Click-SSCM.png
[Image10]: ./media/virtual-machines-provision-sql-server/10Enable-TCP.png
[Image11]: ./media/virtual-machines-provision-sql-server/11Restart.png
[Image12]: ./media/virtual-machines-provision-sql-server/12Open-WF.png
[Image13]: ./media/virtual-machines-provision-sql-server/13New-FW-Rule.png
[Image14]: ./media/virtual-machines-provision-sql-server/14Port-1433.png
[Image15]: ./media/virtual-machines-provision-sql-server/15Allow-Connection.png
[Image16]: ./media/virtual-machines-provision-sql-server/16Public-Profile.png
[Image17]: ./media/virtual-machines-provision-sql-server/17Rule-Name.png
[Image18]: ./media/virtual-machines-provision-sql-server/18Start-SSMS.png
[Image19]: ./media/virtual-machines-provision-sql-server/19Connect-to-Server.png
[Image20]: ./media/virtual-machines-provision-sql-server/20Server-Properties.png
[Image21]: ./media/virtual-machines-provision-sql-server/21Mixed-Mode.png
[Image22]: ./media/virtual-machines-provision-sql-server/22Restart2.png
[Image23]: ./media/virtual-machines-provision-sql-server/23New-Login.png
[Image24]: ./media/virtual-machines-provision-sql-server/24Test-Login.png
[Image25]: ./media/virtual-machines-provision-sql-server/25sysadmin.png
[Image28]: ./media/virtual-machines-provision-sql-server/28Add-Endpoint.png
[Image29]: ./media/virtual-machines-provision-sql-server/29Add-Endpoint-to-VM.png
[Image30]: ./media/virtual-machines-provision-sql-server/30Endpoint-Details.png
[Image31]: ./media/virtual-machines-provision-sql-server/31VM-Connect.png
[Image32]: ./media/virtual-machines-provision-sql-server/32DNS-Name.png
[Image33]: ./media/virtual-machines-provision-sql-server/33Connect-SSMS.png
[Image34]: ./media/virtual-machines-provision-sql-server/choose-sql-vm.png
[Image35]: ./media/virtual-machines-provision-sql-server/sql-vm-dns-name.png
[Image36]: ./media/virtual-machines-provision-sql-server/sql-vm-port-number.png
[Image37]: ./media/virtual-machines-provision-sql-server/click-open-to-connect.png
[Image38]: ./media/virtual-machines-provision-sql-server/credentials.png

<!---HONumber=58-->