<properties 
	pageTitle="Branchenanwendung, Phase 5 | Microsoft Azure"
	description="In Phase 5 der Branchenanwendung erstellen Sie in Azure eine Verfügbarkeitsgruppe, und fügen Sie dieser Ihre Anwendungsdatenbanken hinzu."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="josephd"/>

# Branchenanwendungs-Workload, Phase 5: Erstellen der Verfügbarkeitsgruppe und Hinzufügen der Anwendungsdatenbanken

In dieser letzten Phase der Bereitstellung einer hochverfügbaren Branchenanwendung in den Azure-Infrastrukturdiensten erstellen Sie eine neue SQL Server AlwaysOn-Verfügbarkeitsgruppe und fügen die Datenbanken der Anwendung hinzu.

Eine Übersicht über alle Phasen finden Sie unter [Bereitstellen einer hochverfügbaren Branchenanwendung in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md).

## Erstellen der Verfügbarkeitsgruppe und Hinzufügen der Datenbanken

Für jede Datenbank der Branchenanwendung:

1.	Erstellen Sie von der Datenbank auf dem primären virtuellen SQL Server-Computer eine vollständige Sicherung und eine Sicherung der Transaktionsprotokolle.
2.	Stellen Sie die vollständige Sicherung und die Protokollsicherung auf dem virtuellen SQL Server-Sicherungscomputer wieder her.

Nachdem die Datenbanken gesichert und wiederhergestellt wurden, können sie der Verfügbarkeitsgruppe hinzugefügt werden. SQL Server nimmt nur Datenbanken in die Gruppe auf, die mindestens einmal gesichert und auf einem anderen Computer wiederhergestellt wurden.

### Freigeben der Sicherungsordner

Damit die Datenbanken gesichert und wiederhergestellt werden können, müssen die Sicherungsdateien (BAK-Dateien) vom virtuellen Computer des sekundären Datenbankservers aus zugänglich sein. Gehen Sie dazu wie folgt vor:

1.	Melden Sie sich beim primären Datenbankserver als **[Domäne]\\sqladmin** an. 
2.	Navigieren Sie zu Laufwerk „F:\\“. 
3.	Klicken Sie mit der rechten Maustaste auf den Ordner **Backup**, und klicken Sie auf **Freigeben für** und dann auf **Bestimmte Personen**.
4.	Geben Sie im Dialogfeld **Dateifreigabe** **[Domäne]\\sqlservice** ein, und klicken Sie dann auf **Hinzufügen**.
5.	Klicken Sie für den Kontonamen **sqlservice** auf die Spalte **Berechtigungsebene**, und klicken Sie dann auf **Lesen/Schreiben**. 
6.	Klicken Sie auf **Freigeben** und dann auf **Fertig**.

Führen Sie die oben genannten Schritte auf dem sekundären Datenbankserver aus, erteilen Sie dem Konto "sqlservice" in Schritt 5 für den Ordner "F:\\Backup" allerdings nur die Berechtigung **Lesen**.

### Sichern und Wiederherstellen einer Datenbank

Die folgenden Verfahren müssen für jede Datenbank ausgeführt werden, die der Verfügbarkeitsgruppe hinzugefügt werden soll.

Führen Sie zum Sichern einer Datenbank die folgenden Schritte aus.

1.	Geben Sie auf dem Startbildschirm des primären Datenbankservers **SQL Studio** ein, und klicken Sie dann auf **SQL Server Management Studio**.
2.	Klicken Sie auf **Verbinden**.
3.	Erweitern Sie im linken Bereich den Knoten **Datenbanken**.
4.	Klicken Sie mit der rechten Maustaste auf eine Datenbank, die Sie sichern möchten, zeigen Sie auf **Aufgaben**, und klicken Sie dann auf **Sichern**.
5.	Klicken Sie im Abschnitt **Ziel** auf **Entfernen**, um den Standarddateipfad der Sicherungsdatei zu entfernen.
6.	Klicken Sie auf **Hinzufügen**. Geben Sie unter **Dateiname** **\[Computername]\\backup[Datenbankname].bak** ein, wobei **Computername** für den Namen des primären SQL-**Servercomputers** und **Datenbankname** für den Namen der Datenbank steht. Klicken Sie auf **OK** und nach der Meldung über die erfolgreiche Sicherung nochmals auf **OK**.
7.	Klicken Sie im linken Bereich mit der rechten Maustaste auf **[Datenbankname]**, zeigen Sie auf **Aufgaben**, und klicken Sie dann auf **Sichern**.
8.	Wählen Sie unter **Sicherungstyp** die Option **Transaktionsprotokoll** aus, und klicken Sie dann zweimal hintereinander auf **OK**.
9.	Lassen Sie diese Remotedesktopsitzung geöffnet.

Führen Sie zum Wiederherstellen einer Datenbank die folgenden Schritte aus.

1.	Melden Sie sich beim sekundären Datenbankserver mit dem Konto "[Domänenname]\\sp\_farm\_db" an.
2.	Geben Sie auf dem Startbildschirm **SQL Studio** ein, und klicken Sie dann auf **SQL Server Management Studio**.
3.	Klicken Sie auf **Verbinden**.
4.	Klicken Sie im linken Bereich mit der rechten Maustaste auf **Datenbanken**, und klicken Sie dann auf **Datenbank wiederherstellen**.
5.	Wählen Sie im Abschnitt **Quelle** die Option **Gerät** aus, und klicken Sie dann auf die Schaltfläche mit den Auslassungspunkten (...).
6.	Klicken Sie unter **Sicherungsmedien auswählen** auf **Hinzufügen**.
7.	Geben Sie unter **Speicherort der Sicherungsdatei** **\[Computername]\\backup** ein, drücken Sie die **Eingabetaste**, wählen Sie **[Datenbankname].bak** aus, und klicken Sie dann zweimal auf **OK**. Nun sollten im Abschnitt **Wiederherzustellende Sicherungssätze** die vollständige Sicherung und die Protokollsicherung angezeigt werden.
8.	Klicken Sie unter **Seite auswählen** auf **Optionen**. Wählen Sie im Abschnitt **Wiederherstellungsoptionen** unter **Wiederherstellungsstatus** die Option **OHNE RECOVERY WIEDERHERSTELLEN** aus, und klicken Sie dann auf **OK**. 
9.	Klicken Sie auf Aufforderung auf **OK**.

### Erstellen einer Verfügbarkeitsgruppe

Nachdem Sie mindestens eine Datenbank durch Sicherung und Wiederherstellung vorbereitet haben, erstellen Sie eine Verfügbarkeitsgruppe.

1.	Kehren Sie zur Remotedesktopsitzung für den primären Datenbankserver zurück.
2.	Klicken Sie in **SQL Server Management Studio** im linken Bereich mit der rechten Maustaste auf **Hohe Verfügbarkeit mit AlwaysOn**, und klicken Sie dann auf **Assistent für neue Verfügbarkeitsgruppen**.
3.	Klicken Sie auf der Einführungsseite auf **Weiter**. 
4.	Geben Sie auf der Seite **Namen der Verfügbarkeitsgruppe angeben** unter **Name der Verfügbarkeitsgruppe** den Namen Ihrer Verfügbarkeitsgruppe (z. B. AG1) ein, und klicken Sie dann auf **Weiter**.
5.	Wählen Sie auf der Seite **Datenbanken auswählen** die gesicherten Datenbanken für die Anwendung aus, und klicken Sie auf **Weiter**. Diese Datenbanken erfüllen die Voraussetzungen für eine Verfügbarkeitsgruppe, da Sie mindestens eine vollständige Sicherung auf dem vorgesehenen primären Replikat erstellt haben.
6.	Klicken Sie auf der Seite **Replikate angeben** auf **Replikat hinzufügen**.
7.	Geben Sie unter **Mit Server verbinden** den Namen des sekundären Datenbankservers ein, und klicken Sie dann auf **Verbinden**. 
8.	Der sekundäre Datenbankserver wird auf der Seite **Replikate angeben** in der Liste **Verfügbarkeitsreplikate** aufgeführt. Legen Sie für beide Instanzen für die jeweiligen Optionen die folgenden Werte fest: 

Anfangsrolle | Option | Wert 
--- | --- | ---
Primär | Automatisches Failover (max. 2) | Aktiviert
Sekundär | Automatisches Failover (max. 2) | Aktiviert
Primär | Synchroner Commit (max. 3) | Aktiviert
Sekundär | Synchroner Commit (max. 3) | Aktiviert
Primär | Lesbare sekundäre Rolle | Ja
Sekundär | Lesbare sekundäre Rolle | Ja
		
9.	Klicken Sie auf **Weiter**.
10.	Klicken Sie auf der Seite **Anfängliche Datensynchronisierung auswählen** auf **Nur verknüpfen**, und klicken Sie dann auf **Weiter**. Die Datensynchronisierung erfolgt manuell durch Durchführung der vollständigen Sicherung und der Sicherung der Transaktionsprotokolle auf dem primären Server und die Wiederherstellung der Sicherungen auf dem Sicherungsserver. Alternativ können Sie aber auch **Vollständig** auswählen. In diesem Fall führt der Assistent für neue Verfügbarkeitsgruppen die Datensynchronisierung automatisch aus. Von der automatischen Synchronisierung wird bei großen Datenbanken, wie sie in manchen Unternehmen vorhanden sind, jedoch abgeraten.
11.	Klicken Sie auf der **Überprüfungsseite** auf **Weiter**. Sie erhalten eine Warnung zu einer fehlenden Listenerkonfiguration, da für die Verfügbarkeitsgruppe noch kein Listener konfiguriert ist. 
12.	Klicken Sie auf der Seite **Zusammenfassung** auf **Fertig stellen**. Überprüfen Sie nach Abschluss des Assistenten die Seite **Ergebnisse**, um sich zu vergewissern, dass die Verfügbarkeitsgruppe erfolgreich erstellt wurde. Klicken Sie in diesem Fall auf **Schließen**, um den Assistenten zu beenden. 
13.	Geben Sie auf dem Startbildschirm **Failover** ein, und klicken Sie dann auf **Failovercluster-Manager**. Öffnen Sie im linken Bereich den Namen Ihres Clusters, und klicken Sie dann auf **Rollen**. Es sollte nun eine neue Rolle mit dem Namen Ihrer Verfügbarkeitsgruppe vorhanden sein.

Damit haben Sie erfolgreich eine SQL Server AlwaysOn-Verfügbarkeitsgruppe für Ihre Anwendungsdatenbanken konfiguriert.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase5/workload-lobapp-phase4.png)

Sie können jetzt damit beginnen, die neue Anwendung für Ihre Intranetbenutzer bereitzustellen.

## Konfigurieren eines Listeners für die AlwaysOn-Verfügbarkeitsgruppe

Für die AlwaysOn-Verfügbarkeitsgruppe können Sie optional eine Listenerkonfiguration erstellen. Die hierzu erforderlichen Schritte werden in [Lernprogramm: Listenerkonfiguration für AlwaysOn-Verfügbarkeitsgruppen](https://msdn.microsoft.com/library/dn425027.aspx) beschrieben. In der vorliegenden Schrittanleitung wird nur ein einziger Listener (empfohlen) erstellt und eine statische IP-Adresse einer internen Lastenausgleichsinstanz verwendet.

Nach der Erstellung des Listeners müssen Sie alle virtuellen Webservercomputer so konfigurieren, dass sie anstelle des Namens des ersten SQL Server-Computers im Cluster den Listener verwenden. Statt einen neuen DNS-Namen und -Datensatz zu verwenden, der eine Zuordnung zur virtuellen IP-Adresse der internen Lastenausgleichsinstanz durchführt, konfigurieren Sie die virtuellen Computer, die als Webserver fungieren, zur Verwendung eines SQL-Alias. Nähere Informationen und Anweisungen finden Sie unter [SQL Alias for SharePoint](http://blogs.msdn.com/b/priyo/archive/2013/09/13/sql-alias-for-sharepoint.aspx) (SQL-Alias für SharePoint).

## Zusätzliche Ressourcen

[Bereitstellen einer hochverfügbaren Branchenanwendung in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md)

[Architekturblaupause für Branchenanwendungen](http://msdn.microsoft.com/dn630664)

[Einrichten einer webbasierten Branchenanwendung in einer Hybrid Cloud zu Testzwecken](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure-Infrastrukturdienste-Workload: SharePoint Server 2013-Farm](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=August15_HO9-->