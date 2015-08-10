<properties  pageTitle="Erstellen eine Oracle WebLogic Server 12c-Cluster in Azure" description="Durchlaufen Sie ein Beispiel zum Erstellen eines Oracle WebLogic Server 12c-Clusters in Microsoft Azure. schrittweise aus." services="virtual-machines" authors="bbenz" documentationCenter=""/>

<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />

#Erstellen eine Oracle WebLogic Server 12c-Cluster in Azure
Das folgende Beispiel zeigt, wie Sie ein Oracle WebLogic Server 12c-Cluster in Azure erstellen, der auf einem von Microsoft bereitgestellten Oracle WebLogic Server 12c-Image unter Windows Server 2012 basiert.

In jeder Instanz in einem Cluster WebLogic-Cluster muss die gleiche Version von Oracle WebLogic Server ausgeführt werden. In diesem Beispiel wird WebLogic Server 12c Enterprise Edition verwendet.

##Erstellen virtueller Computer zur Verwendung im Cluster
Erstellen Sie einen virtuellen Computer, der als Cluster-Verwaltungsserver verwendet werden soll, sowie zusätzliche virtuelle Server, die als Teil des Clusters verwendet werden sollen.

### Erstellen eines virtuellen Computers zur Verwendung als Verwaltungsserver

Erstellen Sie einen virtuellen Computer mit dem in Azure verfügbaren Image **Oracle WebLogic Server 12c Enterprise Edition auf Windows Server 2012**. Die Schritte zum Erstellen dieses virtuellen Computers finden Sie unter [Erstellen einen virtuellen Oracle WebLogic Server 12c-Computers in Azure](#creating-an-oracle-weblogic-server-12c-virtual-machine-in-azure-new-article). Rufen Sie für die Zwecke dieses Lernprogramms den virtuellen Computer **MYVM1-ADMIN** auf. Notieren Sie sich den virtuellen Netzwerknamen des virtuellen Computers \(VM\). Sie benötigen diesen Wert beim Erstellen zusätzlicher virtueller Computer, die zum Cluster hinzugefügt werden sollen. \(Der Name des virtuellen Computers und des Netzwerks kann beliebig gewählt werden, muss jedoch in Azure eindeutig sein.\)

### Erstellen verwalteter virtueller Computer zur Verwendung im Cluster

Erstellen Sie zusätzliche virtuelle Computer, die vom Verwaltungsserver verwaltet werden, mit dem in Azure verfügbaren Oracle WebLogic Server 12c-Image. Benennen Sie die zusätzlichen virtuellen Computer für die Zwecke dieses Lernprogramms in **MYVM2-MANAGED** und **MYVM3-MANAGED**. Die Schritte zum Erstellen dieser virtuellen Computer finden Sie unter [Erstellen einen virtuellen Oracle WebLogic Server 12c-Computers in Azure](#creating-an-oracle-weblogic-server-12c-virtual-machine-in-azure-new-article), *mit Ausnahme* folgender Änderung:

-  Wenn Sie die virtuellen Computer erstellen, erstellen Sie kein neues virtuelles Netzwerk. Wählen Sie im Dialogfeld **Optionale Konfiguration \> Virtuelles Netzwerk** anstelle der Standardeinstellung **Neues virtuelles Netzwerk erstellen** das virtuelle Netzwerk aus, das für den virtuellen Computer des Verwaltungsservers erstellt wurde. Wenn Sie beispielsweise beim Erstellen des Verwaltungsservers ein virtuelles Netzwerk mit dem Namen **BEISPIEL** erstellt haben, wählen Sie beim Erstellen der verwalteten Cluster-VMs **BEISPIEL** aus.

##Erstellen einer Domäne

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.

2. Klicken Sie auf **Virtuelle Computer**.

3. Klicken Sie auf den Namen des virtuellen Computers, den Sie als Cluster-Verwaltungsserver erstellt haben \(z. B. **MYVM1-ADMIN**\).

4. Klicken Sie auf **Verbinden**.

5. Befolgen Sie die Anweisungen, um eine Verbindung mit dem virtuellen Computer herzustellen. Wenn Sie zur Eingabe des Administratornamens und des Kennworts aufgefordert werden, verwenden Sie die Werte, die Sie beim Erstellen des virtuellen Computers bereitgestellt haben.

6. Klicken Sie auf **Seite 1** des **Fusion Middleware Configuration Assistant** auf **Neue Domäne erstellen**, und klicken Sie dann auf **Weiter**. \(Wenn das Dialogfeld **Fusion Middleware Configurations Assistant** nicht bereits geöffnet ist, öffnen Sie den Assistenten, indem Sie auf die Windows-Schaltfläche **Start** klicken, **Configuration Assistant** eingeben, und dann auf das Symbol **Configuration Assistant** klicken.\)

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image19.png)

7. Wählen Sie auf **Seite 2** des Dialogfelds **Fusion Middleware Configuration Assistant** die Vorlage **Basic WebLogic Server Domain** aus, und klicken Sie dann auf **Weiter**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image20.png)

8. Führen Sie auf **Seite 3** des Dialogfelds **Fusion Middleware Configuration Wizard** Folgendes vor:

	1. \[Optional\] Ändern Sie den Namen von **weblogic** in einen beliebigen Namen Ihrer Wahl.
	
	2. Geben Sie ein Kennwort für den WebLogic Server-Administrator ein und bestätigen Sie es.
	
	3. Klicken Sie auf **Weiter**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image21.png)

9. Wählen Sie auf **Seite 4** des Dialogfelds **Fusion Middleware Configuration Wizard** den Domänenmodus **Produktion** und das verfügbare JDK aus \(bzw. suchen Sie ggf. nach einem JDK\), und klicken Sie dann auf **Weiter**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image22.png)

10.  Wählen Sie auf **Seite 5** des Dialogfelds **Fusion Middleware Configuration Wizard** keine weiteren Optionen aus. Klicken Sie auf **Weiter**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image23.png)

11.  Klicken Sie auf **Seite 6** des Dialogfelds **Fusion Middleware Configuration Wizard** auf **Erstellen**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image24.png)

12.  Nachdem die Domäne erstellt wurde, klicken Sie auf **Seite 7** des Dialogfelds **Fusion Middleware Configuration Assistant** auf **Weiter**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image25.png)

13.  Aktivieren Sie auf **Seite 8** des **Fusion Middleware Configuration Assistant** die Option "Admin-Server starten", und klicken Sie dann auf **Weiter**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image26.png)

14.  Eine Eingabeaufforderung für **startWebLogic.cmd** wird gestartet. Geben Sie bei Aufforderung Ihren WebLogic-Benutzernamen und das Kennwort ein.

##Einrichten des Clusters

1. Bleiben Sie am virtuellen Verwaltungscomputer angemeldet, und führen Sie die **WebLogic Server-Verwaltungskonsole**, <http://localhost:7001/console>, aus. Geben Sie bei Aufforderung Ihren WebLogic Server-Benutzernamen und das Kennwort ein.

2. Klicken Sie in der **WebLogic Server-Verwaltungskonsole** auf **Sperren und bearbeiten**.

3. Erweitern Sie im Bereich **Domänenstruktur** den Eintrag **Umgebung**, und klicken Sie dann auf **Cluster**.

4. Klicken Sie im Dialogfeld **Cluster-Zusammenfassung** auf **Neu**, und klicken Sie dann auf **Cluster**.

5. Im Dialogfeld **Cluster-Eigenschaft**:

	1. Geben Sie einen Namen für den Cluster ein.

	2. Wählen Sie **Unicast** als **Nachrichtenmodus**.

		![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image001.png)

	
	3. Klicken Sie auf **OK**.

6. Erweitern Sie im Bereich **Domänenstruktur** den Eintrag **Umgebung**, und klicken Sie dann auf **Server**.

7. Fügen Sie den ersten verwalteten Server zum Cluster hinzu.

	1. Klicken Sie auf **Neu**.

	2. Im Dialogfeld**Neuen Server erstellen**:

		1. Geben Sie unter **Servername** den Namen des ersten verwalteten Servers ein. Zum Beispiel \*\*MYVM2-MANAGED\*\*.

		2. Geben Sie unter **Server-Abhöradresse** den Namen erneut ein.

		3. Geben Sie unter **Überwachungsport** **7008** ein.

		4. Aktivieren Sie **Ja, diesen Server als Mitglied zu einem vorhandenen Cluster hinzufügen**.

		5. Wählen Sie in der Dropdownliste **Cluster auswählen** den zuvor erstellten Cluster aus.

			34d27e82-bb2e-4f9c-aaad-ca3e28c0f5fc

		6. Klicken Sie auf **Weiter**.

		7. Klicken Sie auf **Fertig stellen**.

8. Führen Sie die obigen Schritte erneut aus, um den zweiten verwalteten Server zum Cluster hinzuzufügen. Geben Sie unter **Servername** und **Server-Abhöradresse** den Namen des zweiten verwalteten Computers ein. Geben Sie unter **Überwachungsport** **7008** ein.

9. Bleiben Sie in der WebLogic Server-Verwaltungskonsole angemeldet, und klicken Sie auf **Änderungen aktivieren**.

10. Erstellen Sie auf dem virtuellen Verwaltungscomputer eine Umgebungsvariable mit dem Namen **SERVER\_HOME**, und legen Sie ihren Wert auf **C:\\Oracle\\Middleware\\Oracle\_Home\\wlserver** fest. Sie können eine Umgebungsvariable anhand der folgenden Schritte erstellen:

	1. Klicken Sie auf die **Windows-Schaltfläche Start**, geben Sie **Systemsteuerung** ein, und klicken Sie auf das Symbol **Systemsteuerung**. Klicken Sie auf **System und Sicherheit**, dann auf **System**, und klicken Sie dann auf **Erweiterte Systemeinstellungen**.

	2. Klicken Sie auf die Registerkarte **Erweitert** und dann auf **Umgebungsvariablen**.

	3. Klicken Sie im Bereich **Systemvariablen** auf **Neu**, um die Variable zu erstellen.

	4. Geben Sie im Dialogfeld **Neue Systemvariable** **SERVER\_HOME** als Namen für die Variable ein, und legen Sie den Wert **C:\\Oracle\\Middleware\\Oracle\_Home\\wlserver** fest.

	5. Klicken Sie auf **OK**, um die neue Umgebungsvariable zu speichern und das Dialogfeld **Neue Systemvariable** zu schließen.

	6. Schließen Sie die übrigen von der Systemsteuerung geöffneten Dialogfelder.

11. Öffnen Sie eine neue Eingabeaufforderung \(zum Aktivieren der **SERVER\_HOME**-Umgebungsvariable\).

	>[AZURE.NOTE]Einige Schritte erfordern der Verwendung einer Eingabeaufforderung, nachdem Sie sich bei den virtuellen Computern angemeldet haben. Eine einfache Möglichkeit, den Überblick darüber zu behalten, bei welchem Computer Sie angemeldet sind, öffnen Sie die Eingabeaufforderung und geben Sie **title %COMPUTERNAME%** ein.
	>
	>**\( %COMPUTERNAME%** ist eine systemdefinierte Umgebungsvariable, für die automatisch der Computername festgelegt wird. Mit dem Befehl **title** **%COMPUTERNAME%** wird der Name des Computers in der Titelleiste der Eingabeaufforderung angezeigt.\)

12. Führen Sie den folgenden Befehl aus:

		%SERVER\_HOME%\\common\\bin\\pack.cmd -managed=true -domain=C:\\Oracle\\Middleware\\Oracle\_Home\\user\_projects\\domains\\base\_domain -template=c:\\mytestdomain.jar -template\_name="mytestdomain" 

	Dieser Befehl erstellt eine JAR-Datei mit dem Namen **c:\\mytestdomain.jar.** Diese JAR-Datei kopieren Sie später auf die verwalteten virtuellen Computer im Cluster.

13. Lassen Sie eine eingehende Verbindung über die Firewall für Port 7001 zu.

	1. Klicken Sie auf die **Windows-Schaltfläche Start**, geben Sie **Windows-Firewall mit erweiterter Sicherheit** ein, und klicken Sie dann auf das Symbol **Windows-Firewall mit erweiterter Sicherheit**. Die Verwaltungskonsole für **Windows-Firewall mit erweiterter Sicherheit** wird geöffnet.

	2. Klicken Sie in der Firewall-Verwaltungskonsole im linken Bereich auf **Eingehende Regeln** \(wenn **Eingehende Regeln** nicht angezeigt wird, erweitern Sie den obersten Knoten im linken Bereich\), und klicken Sie dann auf **Neue Regel** im rechten Bereich.

	3. Wählen Sie für **Regeltyp** die Option **Port** aus, und klicken Sie auf **Weiter**.

	4. Legen Sie für **Protokoll und Port** die Option **TCP** fest, wählen Sie **Bestimmte lokale Ports** aus, geben Sie **7001** für den Port ein, und klicken Sie dann auf **Weiter**.

	5. Klicken Sie auf **Verbindung zulassen** und anschließend auf **Weiter**.

	6. Übernehmen Sie die Standardeinstellungen für die Profile, für die die Regel gilt, und klicken Sie auf **Weiter**.

	7. Geben Sie einen Namen für die Regel und optional eine Beschreibung ein, und klicken Sie dann auf **Fertig stellen**.

14. Für jeden verwalteten virtuellen Computer:

	1. Melden Sie sich beim virtuellen Computer an.

	2. Erstellen Sie eine Umgebungsvariable mit dem Namen **SERVER\_HOME**, und legen Sie ihren Wert auf **C:\\Oracle\\Middleware\\Oracle\_Home\\wlserver** fest.

	3. Kopieren Sie c:\\mytestdomain.jar vom virtuellen Verwaltungscomputer in c:\\mytestdomain.jar auf dem verwalteten virtuellen Computer.

	4. Öffnen Sie eine Eingabeaufforderung \(denken Sie daran, den Befehl **title %COMPUTERNAME%** in der Eingabeaufforderung auszuführen, um zu sehen, auf welchen Computer Sie zugreifen\).

	5. Führen Sie den folgenden Befehl aus:

			%SERVER\_HOME%\\common\\bin\\unpack.cmd -domain=C:\\Oracle\\Middleware\\Oracle\_Home\\user\_projects\\domains\\base\_domain -template=c:\\mytestdomain.jar

	6. Ändern Sie das aktuelle Verzeichnis der Eingabeaufforderung in **C:\\Oracle\\Middleware\\Oracle\_Home\\user\_projects\\domains\\base\_domain\\bin**.

	7. Führen Sie Start \<\<\*COMPUTERNAME\*\>\>.cmd aus, wobei \<\<\*COMPUTERNAME\*\>\> der Name des verwalteten Computers ist. Zum Beispiel **startMYVM2-MANAGED**.

	8. Geben Sie bei Aufforderung Ihren WebLogic Server-Benutzernamen und das Kennwort ein.

	9. Lassen Sie eine eingehende Verbindung über die Firewall für Port 7008 zu. \(Führen Sie die Schritte zum Öffnen von Port 7001 auf dem Verwaltungsserver durch, verwenden Sie jedoch stattdessen 7008 für den verwalteten Server\).

15. Öffnen Sie auf dem virtuellen Verwaltungscomputer die **WebLogic Server-Verwaltungskonsole** \(<http://localhost:7001/console>\) aus, um zu bestätigen, dass die Server ausgeführt werden.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image003.png)

16. Erstellen Sie eine Gruppe von Endpunkten mit Lastenausgleich für die verwalteten virtuellen Computer.

	1. Wählen im [Azure-Portal](https://ms.portal.azure.com/) im Abschnitt **Virtuelle Computer** des ersten verwalteten virtuellen Computer aus \(z. B. **MYVM2-MANAGED\)**. 
	2. Klicken Sie auf **Einstellungen**, dann auf **Endpunkte** und dann auf **Hinzufügen**

	3. Geben Sie einen Namen für den Endpunkt ein, legen Sie **TCP** als Protokoll fest, geben Sie den öffentlichen Port **80** und den privaten Port **7008** an. Lassen Sie die übrigen Optionen unverändert.

	4. Aktivieren Sie **Gruppe mit Lastenausgleich erstellen**, und klicken Sie dann auf **Abgeschlossen**.

	5. Geben Sie einen Namen für die Gruppe mit Lastenausgleich an, übernehmen Sie die Standardwerte für die anderen Parameter, und klicken Sie dann auf **Abgeschlossen.

17. Erstellen Sie einen Endpunkt für den virtuellen Computer:

	1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.

	2. Klicken Sie auf **Durchsuchen**.

	3. Klicken Sie auf **Virtuelle Computer**.

	4. Wählen Sie den virtuellen Computer aus.

	5. Klicken Sie auf **Einstellungen**.

	6. Klicken Sie auf **Gruppen mit Lastenausgleich**.

	7. Klicken Sie auf **Verknüpfen**.

	8. Legen Sie für die Gruppe mit Lastenausgleich den Typ **Intern** fest.

	9. Geben Sie einen Namen für den Endpunkt an:

		1. Verwenden Sie **TCP** als Protokoll.

		2. Verwenden Sie Port **80** als öffentlichen Port.

		3. Verwenden Sie Port **7008** als Testport.

	10. Lassen Sie die übrigen Optionen unverändert.

	11. Klicken Sie auf **OK**.

	12. Warten Sie, bis der virtuelle Computer mit der Gruppe mit Lastenausgleich verknüpft wurde, bevor Sie mit dem nächsten Schritt fortfahren.

18. Wählen im [Azure-Portal](https://ms.portal.azure.com/) im Abschnitt **Virtuelle Computer** den zweiten verwalteten virtuellen Computer aus \(z. B. **MYVM3-MANAGED**\). Führen Sie die oben beschriebenen Schritte aus, um den virtuellen Computer zur Gruppe mit Lastenausgleich hinzuzufügen, die Sie für den ersten verwalteten virtuellen Computer erstellt haben.

##Bereitstellen einer Anwendung für den Cluster

An diesem Punkt können Sie Ihre Anwendung mit den folgenden Schritten bereitstellen. Angenommen, Sie stellen die shoppingcart-Anwendung von Oracle bereit, die unter <http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war> heruntergeladen werden kann.

1. Melden Sie sich bei dem virtuellen Computer an, der zum Verwalten des WebLogic Server-Cluster verwendet wird \(z. B. **MYVM1-ADMIN**\). 

2. Speichern Sie die Datei "shoppingcart.war" lokal. Erstellen Sie z. B. einen Ordner mit dem Namen **c:\\mywar** und speichern Sie die WAR-Datei unter <http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war> in **c:\\mywar**.

3. Öffnen Sie die **WebLogic Server-Verwaltungskonsole** \(<http://localhost:7001/console>\). Geben Sie bei Aufforderung Ihren WebLogic-Benutzernamen und das Kennwort ein.

4. Klicken Sie in der **WebLogic Server-Verwaltungskonsole** auf **Sperren und bearbeiten**, klicken Sie auf **Bereitstellungen**, und dann auf **Installieren**.

5. Geben Sie den **Pfad** **c:\\myway\\shoppingcart.war** ein.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-cluster/image004.png)

	Klicken Sie auf **Weiter**.

6. Wählen Sie **Diese Bereitstellung als Anwendung installieren**, und klicken Sie dann auf **Weiter**.

7. Klicken Sie auf **Fertig stellen**.

8. Wählen Sie für **Verfügbare Ziele** den zuvor erstellten Cluster aus, und stellen Sie sicher, dass **Alle Server im Cluster** ausgewählt ist, und klicken Sie dann auf **Weiter**.

9. Wählen Sie unter **Zugriff auf Quelle** die Option **Diese Anwendung auf alle meine Ziele kopieren**, und klicken Sie dann auf **Fertig stellen**.

10.  Klicken Sie in der **WebLogic Server-Verwaltungskonsole** auf **Speichern** und dann auf **Änderungen aktivieren**.

11.  Klicken Sie auf **Bereitstellungen**, wählen Sie **shoppingcart** aus, klicken Sie auf **Start** und dann auf **Alle Anforderungen bearbeiten**. Wenn Sie aufgefordert werden, die Auswahl zu bestätigen, klicken Sie auf **Ja**.

12.  Um die ausgeführte Warenkorbanwendung im Internet anzuzeigen, öffnen Sie einen Browser und geben Sie die URL in Form von `http://<<unique_domain_name>>/shoppingcart` ein. \(Sie können den Wert für `<<unique_domain_name>>` im [Azure-Portal](https://ms.portal.azure.com/) festlegen, indem Sie auf "Virtuelle Computer" klicken und dann den virtuellen Computer auswählen, den Sie zum Ausführen von Oracle WebLogic Server verwenden.\)

## Nächste Schritte

Sie können überprüfen, ob der Cluster wie erwartet ausgeführt wird, indem Sie das shoppingcart.war-Projekt ändern, um beispielsweise den Namen des Computers, der die Browsersitzung verarbeitet, anzuzeigen, eine Browsersitzung zu starten, den Computer, der die Browsersitzung bearbeitet, zu beenden und die Browsersitzung zu aktualisieren, um festzustellen, ob die Verarbeitung der Browsersitzung von einem anderen Computer fortgesetzt wird.

Beispiel:

1. Ändern Sie die Datei **DWRHeader1.jspf**, sodass die Datei den folgenden Code am Anfang der Datei enthält:

		<table>
		
		<tr><td><CENTER><b><h3><% out.println("Your request is served from " + System.getenv("computername") ); %></h3></b></CENTER></td></tr>
		
		</table>             

2. Ändern Sie die Datei **weblogic.xml**, sodass die Datei den folgenden Code nach der Kommentarzeile `Insert session descriptor element here` enthält.

		<session-descriptor>
			<persistent-store-type>replicated_if_clustered</persistent-store-type>
		</session-descriptor>


3. Kompilieren Sie shoppingcart.war neu und stellen Sie die WAR-Datei erneut bereit.

4. Öffnen Sie eine Browsersitzung, und führen Sie die Warenkorbanwendung aus. Fügen Sie einige Artikel in den Warenkorb, und beobachten Sie, welcher Computer die Browsersitzung verarbeitet.

5. Wählen Sie im Azure-Portal im Bereich **Virtuelle Computer** den virtuellen Computer aus, der die Browsersitzung verarbeitet hat, und klicken Sie auf **Herunterfahren**. Warten Sie, bis der VM-Status **Beendet \(Zuordnung aufgehoben\)** lautet, bevor Sie fortfahren.

6. Aktualisieren Sie die Browsersitzung, in der die Warenkorbanwendung ausgeführt wird. Sie können sehen, dass die Browsersitzung von einem anderen Computer verarbeitet wird.

7. Klicken Sie auf den Warenkorb-Link. Sie können sehen, dass die zuvor hinzugefügten Artikel nach wie vor im Warenkorb enthalten sind.

## Zusätzliche Ressourcen

Nachdem Sie den Cluster mit Oracle WebLogic Server eingerichtet haben, lesen Sie die folgenden Themen, um weitere Informationen zu erhalten.

- [Images virtueller Oracle-Computer – verschiedene Überlegungen](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

- [Oracle WebLogic Server-Produktdokumentation](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

- [Verwendung von Oracle WebLogic Server 12c mit Linux in Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

<!---HONumber=July15_HO5-->