<properties title="Creating an Oracle WebLogic Server 12c Virtual Machine in Azure" pageTitle="Erstellen eines Erstellen eines virtuellen Oracle WebLogic Server 12c-Computers in Azure" description="Durchlaufen Sie ein Beispiel zum Erstellen eines virtuellen Oracle WebLogic Server 12c-Computers unter Windows Server 2012 in Microsoft Azure." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Erstellen eines Erstellen eines virtuellen Oracle WebLogic Server 12c-Computers in Azure
Das folgende Beispiel zeigt, wie Sie einen virtuellen Computer basierend auf einem von Microsoft bereitgestellten Oracle WebLogic Server 12c-Image unter Windows Server 2012 in Azure erstellen.

##Erstellen eines virtuellen Oracle WebLogic Server 12c-Computers in Azure

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.

2. Klicken Sie auf **Marketplace**, dann auf **Compute**, und geben Sie **Oracle** in das Suchfeld ein.

3.	Wählen Sie das Image **WebLogic Server 12c Standard Edition on Windows Server 2012** oder **Oracle WebLogic Server 12c Enterprise Edition on Windows Server 2012** aus. Überprüfen Sie die Informationen zum Image, das Sie auswählen möchten (z. B. empfohlene Mindestgröße), und klicken Sie dann auf **Weiter**.

4.	Geben Sie einen **Hostnamen** für den virtuellen Computer an.

5.	Geben Sie einen **Benutzernamen** für den virtuellen Computer an. Beachten Sie, dass dieser Benutzer für die Remote-Anmeldung auf dem virtuellen Computer verwendet wird. Dies ist nicht der Oracle-Datenbank-Benutzername.

6.	Geben Sie ein Kennwort für den virtuellen Computer ein, und bestätigen Sie es, oder geben Sie einen öffentlichen SSH-Schlüssel an.

7.	Wählen Sie einen **Tarif** aus. Beachten Sie, dass die empfohlenen Preiskategorien standardmäßig angezeigt werden. Um alle Konfigurationsoptionen anzuzeigen, klicken Sie oben rechts auf **Alle anzeigen**.

8.	Legen Sie [Optionale Konfiguration](https://msdn.microsoft.com/library/azure/dn763935.aspx) nach Bedarf fest. Beachten Sie dabei folgende Überlegungen:
	1. Lassen Sie **Speicherkonto** unverändert, um ein neues Speicherkonto mit dem Namen des virtuellen Computers zu erstellen.
	2. Legen Sie für **Verfügbarkeitsgruppe** "Nicht konfiguriert" fest.
	3. Fügen Sie zu diesem Zeitpunkt keine **Endpunkte** hinzu.

9.	Wählen Sie eine [Ressourcengruppe](resource-group-portal.md) aus oder erstellen Sie eine Ressourcengruppe

10. Wählen Sie ein **Abonnement** aus.

11. Wählen Sie einen **Standort** aus.


##Konfigurieren eines virtuellen Oracle WebLogic Server 12c-Computers in Azure

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.

2.	Klicken Sie auf **Virtuelle Computer**.

3.	Klicken Sie auf den Namen des virtuellen Computers, an dem Sie sich anmelden möchten.

4.	Klicken Sie auf **Verbinden**.

5.	Befolgen Sie die Anweisungen, um eine Verbindung mit dem virtuellen Computer herzustellen. Wenn Sie zur Eingabe des Administratornamens und des Kennworts aufgefordert werden, verwenden Sie die Werte, die Sie beim Erstellen des virtuellen Computers bereitgestellt haben.

6.	Klicken Sie im Dialogfeld **WebLogic-Plattform Schnellstart** auf **Erste Schritte mit WebLogic-Server**. (Wenn das Dialogfeld **WebLogic-Plattform Schnellstart** nicht bereits geöffnet ist, öffnen Sie es, indem Sie auf die **Windows-Schaltfläche Start** klicken, **Admin-Server für WebLogic Server-Domäne starten** eingeben und dann auf das Symbol **Admin-Server für WebLogic Server-Domäne starten** klicken.)

7.	Wählen Sie im Dialogfeld **Willkommen** die Option **Neue WebLogic-Domäne erstellen** aus, und klicken Sie dann auf **Weiter**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image10.png)

8.	Übernehmen Sie im Dialogfeld **Domänenquelle auswählen** die Standardwerte, und klicken Sie dann auf **Weiter**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image11.png)

9.	Übernehmen Sie im Dialogfeld **Domänenname und -standort angeben** die Standardwerte, und klicken Sie dann auf **Weiter**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image12.png)

10.	Im Dialogfeld **Benutzername und Kennwort für Administrator konfigurieren**:

	1.	[Optional] Ändern Sie den Namen von **weblogic** in einen beliebigen Namen Ihrer Wahl.

	2.	Geben Sie ein Kennwort für den WebLogic Server-Administrator ein und bestätigen Sie es.

	3.	Klicken Sie auf **Weiter**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image13.png)

11.	Wählen Sie im Dialogfeld **Server-Startmodus und JDK konfigurieren** die Option **Produktionsmodus** aus, wählen Sie das verfügbare JDK aus (oder navigieren Sie ggf. zum gewünschten JDK), und klicken Sie dann auf **Weiter**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image14.png)

12.	Wählen Sie im Dialogfeld **Optionale Konfiguration auswählen** keine weiteren Optionen aus, und klicken Sie auf **Weiter**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image15.png)

13.	Klicken Sie im Dialogfeld **Konfigurationszusammenfassung** auf **Erstellen**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image16.png)

14.	Aktivieren Sie im Dialogfeld **Domäne erstellen** das Kontrollkästchen **Admin-Server starten**, und klicken Sie dann auf **Fertig**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image17.png)

15.	Eine Eingabeaufforderung für **startWebLogic.cmd** wird gestartet. Geben Sie bei Aufforderung Ihren WebLogic-Benutzernamen und das Kennwort ein.

##Installieren einer Anwendung auf einem virtuellen Oracle WebLogic Server 12c-Computer in Azure
1.	Bleiben Sie am virtuellen Computer angemeldet, und speichern Sie das shoppingcart.war-Beispiel unter http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war lokal. Erstellen Sie z. B. einen Ordner mit dem Namen **c:\\mywar** und speichern Sie die WAR-Datei unter http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war in **c:\\mywar**.

2.	Öffnen Sie die **WebLogic Server-Verwaltungskonsole** (http://localhost:7001/console). Geben Sie bei Aufforderung Ihren WebLogic-Benutzernamen und das Kennwort ein.

3.	Klicken Sie in der **WebLogic Server-Verwaltungskonsole** auf **Sperren und bearbeiten**, klicken Sie auf **Bereitstellungen**, und dann auf **Installieren**.

4.	Geben Sie den **Pfad** **c:\\myway\\shoppingcart.war** ein.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image18.png)

	Klicken Sie auf **Weiter**.

5.	Wählen Sie "Diese Bereitstellung als Anwendung installieren", und klicken Sie dann auf **Weiter**.

6.	Klicken Sie auf **Fertig stellen**.

7.	Klicken Sie in der **WebLogic Server-Verwaltungskonsole** auf **Speichern** und dann auf **Änderungen aktivieren**.

8.	Klicken Sie auf **Bereitstellungen**, wählen Sie **shoppingcart** aus, klicken Sie auf **Start** und dann auf **Alle Anforderungen bearbeiten**. Wenn Sie aufgefordert werden, die Auswahl zu bestätigen, klicken Sie auf **Ja**.

9.	Um die lokal ausgeführte Warenkorbanwendung zu sehen, öffnen Sie einen Browser mit der URL <http://localhost:7001/shoppingcart>.

10.	Erstellen Sie einen Endpunkt für den virtuellen Computer:

	1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.

	2.	Klicken Sie auf **Durchsuchen**.

	3.	Klicken Sie auf **Virtuelle Computer**.

	4.	Wählen Sie den virtuellen Computer aus.

	5.	Klicken Sie auf **Einstellungen**.

	6.	Klicken Sie auf **Endpunkte**.

	7.	Klicken Sie auf **Hinzufügen**.

	8.	Geben Sie einen Namen für den Endpunkt an:

		1. Verwenden Sie **TCP** als Protokoll.

		2. Verwenden Sie Port **80** als öffentlichen Port.

		3. Verwenden Sie Port **7001** als privaten Port.

	9.	Lassen Sie die übrigen Optionen unverändert.

	10. Klicken Sie auf **OK**.

11.	Lassen Sie eine eingehende Verbindung über die Firewall für Port 7001 zu.

	1.	Melden Sie sich am virtuellen Computer an.

	2.	Klicken Sie auf die **Windows-Schaltfläche Start**, geben Sie **Windows-Firewall mit erweiterter Sicherheit** ein, und klicken Sie dann auf das Symbol **Windows-Firewall mit erweiterter Sicherheit**. Die Verwaltungskonsole für **Windows-Firewall mit erweiterter Sicherheit** wird geöffnet.

	3.	Klicken Sie in der Firewall-Verwaltungskonsole im linken Bereich auf **Eingehende Regeln** (wenn **Eingehende Regeln** nicht angezeigt wird, erweitern Sie den obersten Knoten im linken Bereich), und klicken Sie dann auf "Neue Regel" im rechten Bereich.

	4.	Wählen Sie für **Regeltyp** die Option **Port** aus, und klicken Sie auf **Weiter**.

	5.	Legen Sie für **Protokoll und Port** die Option **TCP** fest, wählen Sie **Bestimmte lokale Ports** aus, geben Sie **7001** für den Port ein, und klicken Sie dann auf **Weiter**.

	6.	Klicken Sie auf **Verbindung zulassen** und anschließend auf **Weiter**.

	7.	Übernehmen Sie die Standardeinstellungen für die Profile, für die die Regel gilt, und klicken Sie auf **Weiter**.

	8.	Geben Sie einen Namen für die Regel und optional eine Beschreibung ein, und klicken Sie dann auf **Fertig stellen**.

12.	Um die ausgeführte Warenkorbanwendung im Internet anzuzeigen, öffnen Sie einen Browser mit der URL in Form von `http://<<unique_domain_name>>/shoppingcart`. (Sie können den Wert für <<*unique_domain_name*>> im **Azure-Portal** festlegen, indem Sie auf [Virtuelle Computer](https://ms.portal.azure.com/) klicken und dann den virtuellen Computer auswählen, den Sie zum Ausführen von Oracle WebLogic Server verwenden.)


##Zusätzliche Ressourcen
Nachdem Sie den virtuellen Computer mit Oracle WebLogic Server eingerichtet haben, lesen Sie die folgenden Themen, um weitere Informationen zu erhalten.

-	[Images virtueller Oracle-Computer – verschiedene Überlegungen](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-	[Oracle WebLogic Server-Produktdokumentation](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

-	[Verwendung von Oracle WebLogic Server 12c mit Linux in Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

-	[Oracle Virtual Machine images for Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md) (Images von virtuellen Oracle-Computern für Azure; in englischer Sprache)

<!---HONumber=July15_HO2-->