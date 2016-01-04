<properties 
	pageTitle="Installieren von Aufträgen für die elastische Datenbank | Microsoft Azure" 
	description="Schrittweise Anleitung für die Installation der Funktion für elastische Aufträge" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="ddove" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/04/2015" 
	ms.author="ddove;sidneyh"/>

# Installieren von Aufträgen für die elastische Datenbank – Übersicht
[**Aufträge für die elastische Datenbank**](sql-database-elastic-jobs-overview.md) kann mithilfe von PowerShell oder über das klassische Azure-Portal installiert werden. Der Zugriff zum Erstellen und Verwalten von Aufträgen mithilfe der PowerShell-API ist jedoch nur mit installiertem PowerShell-Paket möglich. Darüber hinaus stellen die PowerShell-APIs zurzeit erheblich mehr Funktionen bereit als das Portal.

Wenn Sie **Aufträge für die elastische Datenbank** bereits aus einem vorhandenen **elastischen Datenbankpool**, über das Portal installiert haben, finden Sie in der neuesten Powershell-Vorschau Skripts, mit denen Sie ein Upgrade der vorhandenen Installation vornehmen können. Es wird dringend empfohlen, dass Sie ein Upgrade Ihrer Installation auf die neuesten Komponenten der **Aufträge für die elastische Datenbank** durchführen, um die über die PowerShell-APIs verfügbar gemachten neuen Funktionen nutzen zu können.

## Voraussetzungen
* Ein Azure-Abonnement. Eine kostenlose Testversion finden Sie unter [Kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/).
* Azure PowerShell, Version >= 0.8.16. Installieren Sie die neueste Version (0.9.5) mithilfe des [Webplattform-Installationsprogramms](http://go.microsoft.com/fwlink/p/?linkid=320376). Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).
* Zum Installieren des Pakets der Aufträge für die elastische Datenbank wird das [NuGet-Befehlszeilenhilfsprogramm](https://nuget.org/nuget.exe) verwendet. Weitere Informationen finden Sie unter http://docs.nuget.org/docs/start-here/installing-nuget.

## Herunterladen und Importieren des PowerShell-Pakets der Aufträge für die elastische Datenbank
1. Öffnen Sie das Microsoft Azure PowerShell-Befehlsfenster, und navigieren Sie zu dem Verzeichnis, in das Sie das NuGet-Befehlszeilenhilfsprogramm („nuget.exe“) heruntergeladen haben.

2. Mit dem folgenden Befehl laden Sie das Paket der **Aufträge für die elastische Datenbank** in das aktuelle Verzeichnis herunter und importieren es:

		PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease

    Die Dateien der **Aufträge für die elastische Datenbank** werden im lokalen Verzeichnis in einem Ordner mit dem Namen **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x** platziert, wobei *x.x.xxxx.x* für die Versionsnummer steht. Die PowerShell-Cmdlets (einschließlich der erforderlichen Client-DLLs) befinden sich im Unterverzeichnis **tools\\ElasticDatabaseJobs**, und die PowerShell-Skripts für Installation, Upgrade und Deinstallation befinden sich ebenfalls im Unterverzeichnis **tools**.

3. Navigieren Sie zum Unterverzeichnis „tools“ unter dem Ordner „Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x“, indem Sie „cd tools“ eingeben, z. B. in dieser Weise:

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4.	Führen Sie das Skript „.\\InstallElasticDatabaseJobsCmdlets.ps1“ aus, um das Verzeichnis „ElasticDatabaseJobs“ in „$home\\Documents\\WindowsPowerShell\\Modules“ zu kopieren. Dadurch wird das Modul außerdem automatisch zur Verwendung importiert, beispielsweise:

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1 
		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## Installieren der Komponenten der Aufträge für die elastische Datenbank mithilfe von PowerShell
1.	Öffnen Sie ein Microsoft Azure PowerShell-Befehlsfenster, und navigieren Sie zum Unterverzeichnis „\\tools“ unter dem Ordner „Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x“: Geben „cd \\tools“ ein.

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2.	Führen Sie das Skript „.\\InstallElasticDatabaseJobs.ps1“ aus, und geben Sie Werte für die erforderlichen Variablen an. Dieses Skript erstellt die in [Aufträge für die elastische Datenbank – Komponenten und Preise](sql-database-elastic-jobs-overview/#components-and-pricing) beschriebenen Komponenten und konfiguriert den Azure Cloud Service passend für die Verwendung der abhängigen Komponenten.

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1 
		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

Wenn Sie den Befehl ausführen, wird ein Fenster geöffnet, das Sie zur Eingabe von **Benutzername** und **Kennwort** auffordert. Dabei handelt es sich nicht um Ihre Azure-Anmeldeinformationen. Geben Sie den Benutzernamen und das Kennwort ein, die Sie als Anmeldeinformationen für das Administratorkonto des neuen Servers erstellen möchten.

Die für diesen Beispielaufruf angegebenen Parameter können nach Ihren Wünschen angepasst werden. Im folgenden finden Sie weitere Informationen zum Verhalten der einzelnen Parameter:

<table style="width:100%">
  <tr>
    <th>Parameter</th>
    <th>Beschreibung</th>
  </tr>

<tr>
	<td>ResourceGroupName</td>
	<td>Gibt den Azure-Ressourcengruppennamen an, der für die Aufnahme der neu erstellten Azure-Komponenten erstellt wird. Dieser Parameter ist standardmäßig auf den Wert „__ElasticDatabaseJob“ festgelegt. Das Ändern dieses Werts wird nicht empfohlen.</td>
	</tr>

</tr>

	<tr>
	<td>ResourceGroupLocation</td>
	<td>Gibt den Azure-Standort an, der für die neu erstellten Azure-Komponenten verwendet werden soll. Dieser Parameter ist standardmäßig auf den Central US-Standort festgelegt.</td>
</tr>

<tr>
	<td>ServiceWorkerCount</td>
	<td>Gibt die Anzahl der zu installierenden Arbeitsprozesse an. Dieser Parameter weist den Standardwert „1“ auf. Es kann eine höhere Anzahl Arbeitsprozesse verwendet werden, um den Dienst zu skalieren und höhere Verfügbarkeit bereitzustellen. Für Bereitstellungen, die eine hohe Verfügbarkeit des Diensts benötigen, wird der Wert „2“ empfohlen.</td>
	</tr>

</tr>
	<tr>
	<td>ServiceVmSize</td>
	<td>Gibt die Größe der VM für die Verwendung innerhalb des Clouddiensts an. Dieser Parameter ist standardmäßig auf A0 festgelegt. Die möglichen Parameterwerte sind „A0/A1/A2/A3“ und legen die Größe „Extraklein/Klein/Mittel/Groß“ für die Workerrolle fest. Weitere Informationen zur Größe der Workerrolle finden Sie unter [Aufträge für die elastische Datenbank – Komponenten und Preise](sql-database-elastic-jobs-overview/#components-and-pricing).</td>
</tr>

</tr>
	<tr>
	<td>SqlServerDatabaseSlo</td>
	<td>Gibt das Service Level-Ziel für die Standard-Edition an. Dieser Parameter ist standardmäßig „S0“. Die Parameterwerte „S0/S1/S2/S3“ sind zulässig und bewirken die Verwendung des entsprechenden SLOs für die Azure SQL-Datenbank. Weitere Informationen zu den SLOs der SQL-Datenbank finden Sie unter [Aufträge für die elastische Datenbank – Komponenten und Preise](sql-database-elastic-jobs-overview/#components-and-pricing).</td>
</tr>

</tr>
	<tr>
	<td>SqlServerAdministratorUserName</td>
	<td>Gibt den Namen des Administratorbenutzers für den neu erstellten Azure SQL-Datenbankserver an. Wenn die Angabe nicht erfolgt, wird ein PowerShell-Anmeldeinformationsfenster geöffnet und fordert zur Eingabe der Anmeldeinformationen auf.</td>
</tr>

</tr>
	<tr>
	<td>SqlServerAdministratorPassword</td>
	<td>Gibt das Administratorkennwort für den neu erstellten Azure SQL-Datenbankserver an. Wenn keine Angabe erfolgt, wird ein PowerShell-Anmeldeinformationsfenster geöffnet und fordert zur Eingabe der Anmeldeinformationen auf.</td>
</tr>
</table>

Für Systeme, die viele Aufträge parallel auf einer großen Anzahl von Datenbanken ausführen sollen, werden Angaben in dieser Art empfohlen: -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2.

    PS C:*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## Aktualisieren einer vorhandenen Installation von Komponenten der Aufträge für die elastische Datenbank mithilfe von PowerShell

**Aufträge für die elastische Datenbank** kann innerhalb einer vorhandenen Installation im Hinblick auf Skalierbarkeit und Hochverfügbarkeit aktualisiert werden. Dieser Prozess ermöglicht zukünftige Upgrade des Dienstcodes, ohne dass die Steuerdatenbank verworfen und neu erstellt werden muss. Dieser Prozess kann auch innerhalb der gleichen Version verwendet werden, um die Größe der Dienst-VM oder die Workeranzahl auf dem Server zu ändern.

Zum Aktualisieren der Größe der VM einer Installation führen Sie das folgende Skript mit Parameterwerten Ihrer Wahl aus.

    PS C:*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>Parameter</th>
  <th>Beschreibung</th>
</tr>

  <tr>
	<td>ResourceGroupName</td>
	<td>Bezeichnet den Namen der Azure-Ressourcengruppe, die bei der ursprünglichen Installation der Komponenten der Aufträge für die elastische Datenbank verwendet wurde. Dieser Parameter ist standardmäßig auf den Wert „__ElasticDatabaseJob“ festgelegt. Da eine Änderung dieses Werts nicht empfohlen wird, sollten Sie diesen Parameter nicht anzugeben brauchen.</td>
	</tr>
</tr>

</tr>

  <tr>
	<td>ServiceWorkerCount</td>
	<td>Gibt die Anzahl der zu installierenden Arbeitsprozesse an. Dieser Parameter weist den Standardwert „1“ auf. Es kann eine höhere Anzahl Arbeitsprozesse verwendet werden, um den Dienst zu skalieren und höhere Verfügbarkeit bereitzustellen. Für Bereitstellungen, die eine hohe Verfügbarkeit des Diensts benötigen, wird der Wert „2“ empfohlen.</td>
</tr>

</tr>

	<tr>
	<td>ServiceVmSize</td>
	<td>Gibt die Größe der VM für die Verwendung innerhalb des Clouddiensts an. Dieser Parameter ist standardmäßig auf A0 festgelegt. Die möglichen Parameterwerte sind „A0/A1/A2/A3“ und legen die Größe „Extraklein/Klein/Mittel/Groß“ für die Workerrolle fest. Weitere Informationen zur Größe der Workerrolle finden Sie unter [Aufträge für die elastische Datenbank – Komponenten und Preise](sql-database-elastic-jobs-overview/#components-and-pricing).</td>
</tr>

</table>

## Installieren der Komponenten der Aufträge für die elastische Datenbank mithilfe des Portals

Nachdem Sie einen [elastischen Datenbankpool erstellt](sql-database-elastic-pool-portal.md) haben, können Sie die Komponenten der **Aufträge für die elastische Datenbank** installieren, um die Ausführung von Verwaltungsaufgaben für jede der Datenbanken im elastischen Datenbankpool zu ermöglichen. Anders als bei der Verwendung der PowerShell-APIs für die **Aufträge für die elastische Datenbank**, ist die Portalschnittstelle aktuell auf die Ausführung auf einem vorhandenen Pool beschränkt.


**Geschätzter Zeitaufwand:** 10 Minuten.

1. Klicken Sie in der Dashboardansicht des elastischen Datenbankpools im [Azure-Portal](https://ms.portal.azure.com/#) auf **Auftrag erstellen**.
2. Wenn Sie zum ersten Mal einen Auftrag erstellen, müssen Sie **Aufträge für die elastische Datenbank** installieren, indem Sie auf **PREVIEW TERMS** klicken. 
3. Akzeptieren Sie die Bedingungen, indem Sie das Kontrollkästchen aktivieren.
4. Klicken Sie in der Ansicht "Dienste installieren" auf **JOB CREDENTIALS**.

	![Installieren der Dienste][1]

5. Geben Sie einen Benutzernamen und ein Kennwort für einen Datenbankadministrator ein. Im Rahmen der Installation wird ein neuer Azure SQL-Datenbank-Server erstellt. Auf diesem neuen Server wird eine neue Datenbank erstellt, die als Steuerdatenbank bezeichnet wird und die Metadaten für Aufträge für die elastische Datenbank enthält. Der Benutzername und das Kennwort, die hier erstellt werden, werden für die Anmeldung bei der Steuerdatenbank verwendet. Für die Skriptausführung für die Datenbanken innerhalb des Pools werden getrennte Anmeldeinformationen verwendet.

	![Erstellen von Benutzername und Kennwort][2]

6. Klicken Sie auf die Schaltfläche "OK". Die Komponenten werden in wenigen Minuten in einer neuen [Ressourcengruppe](../resource-group-portal.md) erstellt. Die neue Ressourcengruppe wird an Start angeheftet, wie unten dargestellt. Nach der Erstellung werden alle Aufträge für die elastische Datenbank (Cloud Service, SQL-Datenbank, Service Bus und Storage) in der Gruppe erstellt.

	![Ressourcengruppe unter Start][3]

7. Wenn Sie versuchen, einen Auftrag zu erstellen oder zu verwalten, während die Aufträge für die elastische Datenbank installiert werden, wird bei der Eingabe der **Anmeldeinformationen** die folgende Meldung angezeigt.

	![Bereitstellung noch nicht abgeschlossen][4]

Wenn die Deinstallation erforderlich ist, löschen Sie die Ressourcengruppe. Weitere Informationen finden Sie unter [Deinstallieren der Komponenten der Aufträge für die elastische Datenbank](sql-database-elastic-jobs-uninstall.md).

## Nächste Schritte

Stellen Sie sicher, dass Anmeldeinformationen mit den passenden Rechten für die Skriptausführung für jede Datenbank in der Gruppe erstellt werden. Weitere Informationen dazu finden Sie unter [Hinzufügen von Benutzern zu allen Datenbanken in einer Gruppe von Datenbanken](sql-database-elastic-jobs-add-logins-to-dbs.md). Informationen zum Einstieg finden Sie unter [Erstellen und Verwalten von Aufträgen für die elastische Datenbank](sql-database-elastic-jobs-create-and-manage.md).

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/incomplete.png
 

<!---HONumber=AcomDC_1203_2015-->