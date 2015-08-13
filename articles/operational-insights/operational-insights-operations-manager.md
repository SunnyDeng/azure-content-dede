<properties
   pageTitle="Überlegungen zu Operations Manager mit Operational Insights"
   description="Wenn Sie Microsoft Azure Operational Insights mit Operations Manager verwenden, basiert Ihre Konfiguration auf einer Verteilung von Operations Manager-Agents und Verwaltungsgruppen, um Daten zu sammeln und zur Analyse an den Operational Insights-Dienst zu senden"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# Überlegungen zu Operations Manager mit Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Wenn Sie Microsoft Azure Operational Insights mit Operations Manager verwenden, basiert Ihre Konfiguration auf einer Verteilung von Operations Manager-Agents und Verwaltungsgruppen, um Daten zu sammeln und zur Analyse an den Operational Insights-Dienst zu senden. Wenn Sie jedoch Agents verwenden, die sich direkt mit dem Webdienst verbinden, ist Operations Manager nicht notwendig. Berücksichtigen Sie die folgenden Probleme bei Ihrer Verwendung von Operational Insights mit Operations Manager.

Darüber hinaus müssen Sie die Anmeldeinformationen für die Ausführung der Workloads angeben, die von Operations Manager an Operational Insights überwacht werden.

## Funktionen und Anforderungen der Operational Insights-Software

Operative Insights besteht aus einem Webdienst in der Cloud und entweder Agents, die direkt mit dem Webdienst verbunden sind, oder Operations Manager-Agents und Operations Manager-Verwaltungsgruppen, die Computer in Ihrer Umgebung verwalten.

Bevor Sie Operations Manager-Agents \(zum Sammeln von Daten\) und Verwaltungsgruppen \(zum Verwalten von Agents und Senden von Daten an den Operational Insights-Dienst\) auswählen, sollten Sie Folgendes wissen:

- Der Operations Manager-Agent wird auf jedem Server installiert, auf dem Sie Daten sammeln und analysieren möchten.

- Die Operations Manager-Verwaltungsgruppe überträgt Daten von Agents an den Operational Insights-Webdienst. Sie analysiert keine Daten.

- Die Operations Manager-Verwaltungsgruppe benötigt Zugriff auf das Internet, um Daten an den Webdienst hochzuladen.

- Für ein optimales Ergebnis installieren Sie den Operations Manager-Verwaltungsserver nicht auf einem Computer, der auch als Domänencontroller dient.

- Ein Operations Manager-Agent muss über eine Netzwerkverbindung mit der Operations Manager-Verwaltungsgruppe verfügen, um Daten übertragen zu können.

Operational Insights kann den Integritätsdienst von System Center zum Sammeln und Analysieren von Daten verwenden. Operations Manager ist vom Integritätsdienst von System Center abhängig. Wenn Sie die Programme anzeigen, die auf dem Server installiert sind, sehen Sie die System Center Operations Manager-Agent-Software, insbesondere unter "Software". Entfernen Sie diese nicht, da Operational Insights davon abhängig ist. Wenn Sie die Operations Manager-Agent-Software entfernen, funktioniert Operational Insights nicht mehr.

## Koexistenz mit Operations Manager

Bei Verwendung von Operations Manager wird Operational Insights nur mit dem Operations Manager-Agent in System Center Operations Manager 2012 R2 oder System Center Operations Manager 2012 SP1 unterstützt. Es wird nicht mit früheren Versionen von System Center Operations Manager unterstützt. Da der Operations Manager-Agent zum Sammeln von Daten eingesetzt wird, verwendet er bestimmte Anmeldeinformationen \(Aktionskonten oder ausführende Konten\) zur Unterstützung einiger der analysierten Workloads, z. B. SharePoint 2012.

## Operational Insights und SQL Server 2012

Wenn Sie Operations Manager verwenden, wird der Integritätsdienst von System Center unter dem lokalen Systemkonto ausgeführt. In SQL Server-Versionen vor SQL Server 2008 R2 war das lokale Systemkonto standardmäßig aktiviert und Mitglied der Systemadministrator-Serverrolle. In SQL Server 2012 ist die lokale Systemanmeldung nicht Teil der Systemadministrator-Serverrolle. Daher kann bei Verwendung von Operational Insights die SQL Server 2012-Instanz nicht vollständig überwacht werden, und nicht alle Regeln können Warnungen generieren.

## Internet und interne Netzwerkverbindungen

Bei Verwendung von Agents, die direkt mit dem Webdienst verbunden sind, müssen die Agents auf das Internet zugreifen können, um Daten an den Webdienst zu senden und aktualisierte Konfigurationsinformationen aus dem Webdienst zu erhalten.

Bei Verwendung von Operations Manager muss der Verwaltungsserver auf das Internet zugreifen können, um Daten an den Webdienst zu senden und aktualisierte Konfigurationsinformationen aus dem Webdienst zu erhalten. Allerdings müssen die Agents nicht auf das Internet zugreifen. Wenn Sie Operations Manager-Agents auf Servern verwenden, die nicht mit dem Internet verbunden sind, können Sie den Webdienst verwenden, wenn sie mit einem Verwaltungsserver mit Internetverbindung kommunizieren können.

## Unterstützung für Clustering

Der Operations Manager-Agent wird auf Computern unter Windows Server 2012, Windows Server 2008 R2 oder Windows Server 2008 unterstützt, die als Teil eines Windows-Failoverclusters konfiguriert sind. Die Cluster können im Operational Insights-Portal angezeigt werden. Auf der Seite "Server" werden Cluster als TYPE=CLUSTER identifiziert \(im Gegensatz zu TYPE=AGENT, womit physische Computer gekennzeichnet werden\).

Die Ermittlungs- und Konfigurationsregeln werden auf den aktiven und passiven Knoten des Clusters ausgeführt, die auf den passiven Knoten generierten Warnungen werden jedoch ignoriert. Wenn ein Knoten von passiv in aktiv geändert wird, werden Warnungen für den Knoten automatisch angezeigt, ohne dass ein Benutzereingriff erforderlich ist.

Einige Warnungen werden möglicherweise zweimal erstellt, abhängig von der Regel, die die Warnung generiert. Eine Regel, die durch Untersuchen des Betriebssystems einen fehlerhaften Treiber erkennt, generiert z. B. Warnungen für den physischen Server und für den Cluster.

Die Konfigurationsanalyse der passiven Knoten wird nicht unterstützt.

Operational Insights unterstützt nicht die Gruppierung oder Verknüpfung von Computern unter Windows Server, die demselben Failovercluster angehören.

## Skalieren Ihrer Operational Insights-Umgebung

Beachten Sie bei der Planung Ihrer Operational Insights-Bereitstellung die Kapazität des Servers im Hinblick auf dem Speicherplatz \(insbesondere dann, wenn Sie die Anzahl der Operations Manager-Agents analysieren, die zum Übertragen von Daten über eine einzelne Verwaltungsgruppe verwendet werden sollen\).

Berücksichtigen Sie die folgenden Variablen:

- Anzahl der Agents pro Verwaltungsgruppe

- Die durchschnittliche Größe der Daten, die pro Tag vom Agent an die Verwaltungsgruppe übertragen werden. Standardmäßig lädt jeder Agent zweimal pro Tag CAB-Dateien in die Verwaltungsgruppe hoch. Die Größe der CAB-Dateien hängt von der Konfiguration des Servers \(z. B. von der Anzahl der SQL Server-Module und der Anzahl der Datenbanken\) und von der Integrität des Servers ab \(z. B. von der Anzahl der generierten Warnungen\). In den meisten Fällen beträgt die tägliche Uploadgröße in der Regel weniger als 100 KB.

- Der Archivierungszeitraum für die Aufbewahrung von Daten in der Verwaltungsgruppe \(der Standardwert ist 5 Tage\)

Wenn Sie beispielsweise von einer täglichen Uploadgröße von 100 KB pro Agent und vom Standardarchivierungszeitraum ausgehen, benötigen Sie für die Verwaltungsgruppe den folgenden Speicherplatz:

Anzahl der Agents|Geschätzter Speicherplatzbedarf für die Verwaltungsgruppe
---|---
5|\~2,5 MB \(5 Agents x 100 KB Daten/Tag x 5 Tage = 2.500 KB\)
50|\~25 MB \(50 Agents x 100 KB Daten/Tag x 5 Tage = 25.000 KB\)

## Ausführende Operations Manager-Konten für Operational Insights

Operational Insights verwendet den Operations Manager-Agent und die Verwaltungsgruppe zum Erfassen und Senden von Daten an den Operational Insights-Dienst. Operational Insights beruht auf Management Packs für Arbeitsauslastungen, um Dienste bereitzustellen, die einen Mehrwert schaffen. Jede Arbeitsauslastung erfordert spezifische Berechtigungen zum Ausführen von Management Packs in einem anderen Sicherheitskontext, z. B. ein Domänenkonto. Sie müssen Anmeldeinformationen angeben, indem Sie ein ausführendes Operations Manager-Konto konfigurieren.

In den folgenden Abschnitten wird beschrieben, wie ausführende Operations Manager-Konten für die folgenden Arbeitsauslastungen festgelegt werden:

- SQL Assessment
- Virtual Machine Manager
- Lync Server
- SharePoint

### Festlegen des ausführenden Kontos für SQL Assessment

 Wenn Sie bereits das SQL Server Management Pack verwenden, sollten Sie das entsprechende ausführende Konto verwenden.

#### So konfigurieren Sie das ausführende SQL-Konto in der Betriebskonsole

1. Öffnen Sie in Operations Manager die Betriebskonsole, und klicken Sie dann auf **Verwaltung**.

2. Klicken Sie unter **Ausführen als-Konfiguration** auf **Profile**, und öffnen Sie das **ausführende Profil für Operational Insights SQL Assessment**.

3. Klicken Sie auf der Seite **Ausführende Konten** auf **Hinzufügen**.

4. Wählen Sie ein ausführendes Windows-Konto aus, das die Anmeldeinformationen für SQL Server enthält, oder klicken Sie auf **Neu**, um eines zu erstellen.
	>[AZURE.NOTE]Der Typ des ausführenden Kontos muss "Windows" sein. Das ausführende Konto muss auch Teil der lokalen Administratorengruppe auf allen Windows-Servern sein, auf denen SQL Server-Instanzen gehostet werden.

5. Klicken Sie auf **Speichern**.

6. Ändern und führen Sie dann das folgende T-SQL-Beispiel auf jeder SQL Server-Instanz aus, um dem ausführenden Konto die erforderlichen Mindestberechtigungen zum Ausführen von SQL Assessment zu erteilen. Dies ist jedoch nicht erforderlich, wenn ein ausführendes Konto bereits Teil der Serverrolle "sysadmin" auf SQL Server-Instanzen ist.

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### To configure the SQL Run As account using Windows PowerShell

Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```


### Festlegen des ausführenden Kontos für Virtual Machine Manager

Stellen Sie sicher, dass das ausführende Konto Berechtigungen für die folgenden Aktionen besitzt:

- Verwenden des VMM Windows PowerShell-Moduls

- Abfragen der VMM-Datenbank

- Remoteverwaltung der VMM-Agents, die auf Virtualisierungshosts ausgeführt werden

Führen Sie die folgenden Schritte aus, um das Konto festzulegen, wenn Sie Operational Insights mit Operations Manager verbinden.

#### So legen Sie Anmeldeinformationen für VMM fest

1. Öffnen Sie in Operations Manager die Betriebskonsole, und klicken Sie dann auf **Verwaltung**.

2. Klicken Sie unter **Ausführen als-Konfiguration** auf **Profile**, und öffnen Sie das **ausführende Konto für Operational Insights VMM**.

3. Klicken Sie auf der Seite **Ausführende Konten** auf **Hinzufügen**.

4. Wählen Sie ein ausführendes Windows-Konto aus, das die Anmeldeinformationen für VMM enthält, oder klicken Sie auf **Neu**, um eines zu erstellen.
	>[AZURE.NOTE]Der Typ des ausführenden Kontos muss "Windows" sein.

5. Klicken Sie auf **Speichern**.


### Festlegen des ausführenden Kontos für Lync Server

 Das ausführende Konto muss ein Mitglied sowohl der lokalen Administratorengruppe als auch der Lync-Sicherheitsgruppe RTCUniversalUserAdmins sein.

#### So legen Sie die Anmeldeinformationen für ein Lync-Konto fest

1. Öffnen Sie in Operations Manager die Betriebskonsole, und klicken Sie dann auf **Verwaltung**.

2. Klicken Sie unter **Ausführen als-Konfiguration** auf **Profile**, und öffnen Sie das **ausführende Konto für Operational Insights Lync**.

3. Klicken Sie auf der Seite **Ausführende Konten** auf **Hinzufügen**.

4. Wählen Sie ein ausführendes Konto aus, das sowohl ein Mitglied der lokalen Administratorengruppe als auch der Lync-Sicherheitsgruppe RTCUniversalUserAdmins ist.
	>[AZURE.NOTE]Der Typ des ausführenden Kontos muss "Windows" sein.

5. Klicken Sie auf **Speichern**.


### Festlegen des ausführenden Kontos für SharePoint


#### So legen Sie die Anmeldeinformationen für ein SharePoint-Konto fest

1. Öffnen Sie in Operations Manager die Betriebskonsole, und klicken Sie dann auf **Verwaltung**.

2. Klicken Sie unter **Ausführen als-Konfiguration** auf **Profile**, und öffnen Sie das **ausführende Konto für Operational Insights SharePoint**.

3. Klicken Sie auf der Seite **Ausführende Konten** auf **Hinzufügen**.

4. Wählen Sie ein ausführendes Windows-Konto aus, das die Anmeldeinformationen für SharePoint enthält, oder klicken Sie auf **Neu**, um eines zu erstellen.
	>[AZURE.NOTE]Der Typ des ausführenden Kontos muss "Windows" sein.

5. Klicken Sie auf **Speichern**.



## Geografische Standorte

Wenn Sie Daten von Servern an unterschiedlichen geografischen Standorten analysieren möchten, sollten Sie die Verwendung einer Verwaltungsgruppe pro Standort in Betracht ziehen. Dadurch können Sie die Leistung der Datenübertragung vom Agent an die Verwaltungsgruppe verbessern.

<!--------HONumber=July15_HO5-->