<properties 
	pageTitle="Überlegungen zur Preisgestaltung und zur Leistung eines elastischen Datenbankpools | Azure SQL-Datenbank – Elastische Datenbankpools" 
	description="Ein elastischer Datenbankpool stellt eine Sammlung der verfügbaren Ressourcen dar, die von einer Gruppe von elastischen Datenbanken gemeinsam verwendet werden. Dieses Dokument enthält Anleitungen, die Ihnen helfen sollen, die Eignung eines elastischen Datenbankpools für eine Gruppe von Datenbanken einzuschätzen." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/24/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>



# Überlegungen zum Preis und zur Leistung eines elastischen Datenbankpools


Dieses Dokument enthält Anleitungen, die Ihnen bei der Einschätzung helfen, ob ein elastischer Datenbankpool für eine Datenbankgruppe im Hinblick auf die Datenbankverwendungsmuster und die Preisunterschiede zwischen einem elastischen Pool und einzelnen Datenbanken für Sie kostengünstiger ist. Darüber hinaus sind weitere Anleitungen enthalten, die Sie bei der Ermittlung der aktuellen Poolgröße für eine vorhandene Reihe von SQL-Datenbanken unterstützen.

- Eine Übersicht über elastische Datenbankpools finden Sie unter [Elastische Datenbankpools für SQL-Datenbanken](sql-database-elastic-pool.md).
- Detaillierte Informationen zu elastischen Datenbankpools finden Sie unter [Referenz: Elastische Datenbankpools für SQL-Datenbanken](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE]Elastische Pools sind derzeit als Vorschauversion ausschließlich für Server mit SQL-Datenbank V12 verfügbar.

## Elastische Datenbankpools

SaaS-ISVs (Internetdienstanbieter, die „Software as a Service“ anbieten) entwickeln Anwendungen, die auf der obersten von umfangreichen Datenebenen aufsetzen, die wiederum aus zahlreichen Datenbanken bestehen können. Es kommt häufig vor, dass es für diese Datenbanken unterschiedliche Kunden mit eindeutig voneinander abweichenden und nicht vorhersehbaren Verwendungsmustern gibt. Daher kann es für den ISV schwierig werden, die Ressourcenanforderungen für jede einzelne Datenbank vorherzusehen. Unter diesen Umständen kann es vorkommen, dass der ISV zu viele Ressourcen bereitstellt, um den bestmöglichen Datendurchsatz und die optimale Antwortzeit für alle Datenbanken zu gewährleisten, was jedoch mit erheblichen Kosten verbunden ist. Auf der anderen Seite riskiert der ISV eine schlechtere Leistung bei geringeren Kosten für seine Kunden.

Mit elastischen Datenbankpools in Azure SQL-Datenbanken sind SaaS-ISVs in der Lage, das Preis-/Leistungs-Verhältnis für eine Gruppen von Datenbanken im Rahmen eines vorgegebenen Budgets zu optimieren und können dennoch eine flexible Leistung für jede Datenbank sicherstellen. Mit elastischen Pools kann der ISV Datenbankdurchsatzeinheiten (Database Throughput Units, DTUs) für einen elastischen Pool erwerben, der von mehreren Datenbanken gemeinsam genutzt wird, um unvorhersehbare Nutzungszeiten von einzelnen Datenbanken abzufedern. Die DTU-Anforderungen eines elastischen Pools werden anhand der zusammengefassten Auslastung der hierin befindlichen Datenbanken ermittelt. Die Anzahl der für den elastischen Pool verfügbaren DTUs wird vom Budget des ISVs gesteuert. Elastische Pools vereinfachen es dem ISV, Argumente für die Auswirkungen des Budgets auf die Leistung und umgekehrt auf den Pool zu finden. Der ISV fügt dem elastischen Pool einfach Datenbanken hinzu, legt alle erforderlichen DTU-Garantien oder -Beschränkungen für die Datenbanken fest und berechnet dann die DTUs des Pools basierend auf dem vorhandenen Budget. Mithilfe von elastischen Pools können ISVs ihre Dienste nahtlos von einem schlanken Einstieg für Startups bis zu einem Angebot für gut eingeführte Unternehmen mit ständig wachsenden Anforderungen skalieren.
  


## Wann ein elastischer Datenbankpool in Frage kommt

Elastische Pools sind hervorragend geeignet, wenn es eine große Anzahl an Datenbanken mit spezifischen Nutzungsmustern gibt. Im Hinblick auf eine einzelne Datenbank wird dieses Muster durch eine geringe durchschnittliche Auslastung mit relativ wenigen Nutzungslastspitzen gekennzeichnet.

Je mehr Datenbanken Sie einem Pool hinzufügen können, desto höher werden die Einsparungen, aber je nach Anwendungsauslastungsmuster kann es sein, dass sich diese Einsparungen schon im Bereich von vier S3-Datenbanken einstellen.

In den folgenden Abschnitten wird erläutert, wie Sie einschätzen können, ob Ihre spezielle Sammlung an Datenbanken von elastischen Pools profitieren kann.

### Bewerten der Datenbankauslastungsmuster

In der folgenden Abbildung finden Sie ein Beispiel für eine Datenbank, die sich in der Regel im Leerlauf befindet, bei der es allerdings auch regelmäßig zu Auslastungsspitzen kommt. Dieses Auslastungsmuster eignet sich hervorragend für einen elastischen Pool:
 
   ![Eine Datenbank][1]

In dem vorstehend dargestellten Zeitraum von einer Stunde werden bei DB1 bis zu 90 DTUs abgerufen, die mittlere Auslastung liegt jedoch bei unter 5 DTUs. Zum Ausführen dieses Workloads in einer einzelnen Datenbank ist eine S3-Leistungsstufe erforderlich. Dies bedeutet jedoch auch, dass ein Großteil der Ressourcen in Zeiten mit geringer Aktivität ungenutzt bleibt.

Bei einem elastischen Pool können diese ungenutzten DTUs auf mehrere Datenbanken verteilt werden, wodurch sich die Gesamtmenge der erforderlichen DTUs und damit auch die Gesamtkosten verringern.

Ausgehend vom vorherigen Beispiel nehmen wir einmal an, dass es weitere Datenbanken mit ähnlichen Auslastungsmustern wie DB1 gibt. In den beiden folgenden Abbildungen unten befindet sich die Auslastung von vier Datenbank und von weiteren 20 Datenbanken in Ebenen in der gleichen Grafik, um die sich nicht überlappende Natur der Auslastung im Zeitverlauf zu veranschaulichen:

   ![Vier Datenbanken][2]

   ![Zwanzig Datenbanken][3]

Die zusammengefasste DTU-Auslastung aller 20 Datenbanken wird in der vorstehenden Abbildung durch die schwarze Linie dargestellt. Diese zeigt, dass die zusammengefasste DTU-Auslastung niemals 100 DTUs übersteigt, und gibt an, dass die 20 Datenbanken in diesem Zeitraum 100 DTUs gemeinsam nutzen können. Dies führt zu einer 20-fachen Verringerung im Hinblick auf die DTUs und einer sechsfachen Preisreduzierung im Vergleich zur Positionierung jeder Datenbank in S3-Leistungsstufen für Einzeldatenbanken.


Dieses Beispiel ist aus den folgenden Gründen ideal:

- Es gibt große Unterschiede zwischen der Spitzenauslastung und der mittleren Auslastung pro Datenbank.  
- Die Spitzenauslastung jeder Datenbank ergibt sich zu jeweils unterschiedlichen Zeitpunkten. 
- Die DTUs werden von einer großen Anzahl Datenbanken gemeinsam genutzt.


Der Preis für einen elastischen Pool ist eine Funktion aus den Pool-DTUs und der Anzahl der im Pool befindlichen Datenbanken. Während der Preis pro DTU für einen Pool bei den allgemein verfügbaren Preisen (General Availability, GA) drei Mal höher als der Preis pro DTU für eine einzelne Datenbank ist, können hierbei Pool-DTUs von vielen Datenbanken gemeinsam genutzt werden, daher liegt der Preis in vielen Fällen unter der Gesamtanzahl der benötigten DTUs. Die Unterschiede bei der Preisgestaltung und die gemeinsame Nutzung von DTUs sind die Basis des Einsparungspotenzials, das von Pools geboten wird.

<br>

Im Hinblick auf die Datenbankanzahl und die Datenbankauslastung sollen Ihnen die folgenden Faustregeln bei der Sicherstellung helfen, dass sich mit einem elastischer Pool im Vergleich zu Leistungsstufen für einzelne Datenbanken die Kosten reduzieren lassen. Die Richtlinien basieren auf allgemein verfügbaren Preisen (GA). Beachten Sie, dass die allgemein verfügbaren Preise während der Vorschau um 50 % rabattiert sind, sodass diese Faustregel als relativ konservativ angesehen werden sollte.


### Minimale Anzahl Datenbanken

In Anbetracht der allgemein verfügbaren Preise ist ein elastischer Pool kosteneffizienter, wenn eine DTU von mehr als drei Datenbanken gemeinsam genutzt werden kann. Dies bedeutet, dass die Summe der DTUs für die Leistungsstufen für einzelne Datenbanken mehr als das Dreifache der DTUs des Pools beträgt. Informationen zu den verfügbaren Größen finden Sie unter [DTUs und Speicherbeschränkungen für elastische Pools und elastische Datenbanken](sql-database-elastic-pool-reference.md#dtu-and-storage-limits-for-elastic-pools-and-elastic-databases).


***Beispiel***<br> Es werden mindestens vier S3-Datenbanken oder mindestens 36 S0-Datenbanken benötigt, damit ein elastischer Pool mit 100 DTUs kosteneffizienter als die Leistungsstufen für einzelne Datenbanken ist. (Beachten Sie, dass bei den prognostizierten Preisen die Rentabilitätsgrenze auf einer Datenbankanzahl von zwei oder weniger S3-Datenbanken oder 17 S0-Datenbanken basiert.)



### Maximale Anzahl von gleichzeitig unter Spitzenlast laufenden Datenbanken

Bei der gemeinsamen Nutzung von DTUs können nicht alle Datenbanken in einem Pool gleichzeitig DTUs bis zum verfügbaren Grenzwert verwenden, wenn Leistungsstufen für einzelne Datenbanken verwendet werden. Je weniger Datenbanken gleichzeitig auf Spitzenlast genutzt werden, desto niedriger kann der Pool-DTU festgelegt werden und desto mehr Kosten können eingespart werden. Im Allgemeinen sollte nicht mehr als ein Drittel der Datenbanken im Pool gleichzeitig den DTU-Grenzwert erreichen.

***Beispiel***<br> Um die Kosten für vier S3-Datenbanken in einem Pool mit 200 DTUs zu senken, können höchstens zwei dieser Datenbanken gleichzeitig mit Spitzenauslastung ausgeführt werden. Andernfalls gilt, dass der Pool auf mehr als 200 DTUs ausgelegt werden muss, wenn zwei dieser vier S3-Datenbanken gleichzeitig mit Spitzenlast ausgeführt werden. Wenn die Größe des Pools auf mehr als 200 DTUs geändert wird, müssten weitere S3-Datenbanken zum Pool hinzugefügt werden, damit die Kosten unterhalb der Leistungsstufe für einzelne Datenbanken bleiben.


Beachten Sie, dass in diesem Beispiel die Auslastung anderer Datenbanken im Pool nicht berücksichtigt wird. Wenn alle Datenbanken zu einem gegebenen Zeitpunkt eine Spitzenauslastung aufweisen, können weniger als ein Drittel der Datenbanken gleichzeitig eine Spitzenauslastung aufweisen.


### DTU-Auslastung pro Datenbank

Ein großer Unterschied zwischen der mittleren und der Spitzenauslastung einer Datenbank weist darauf hin, dass es längere Zeiträume mit geringer Auslastung und kurze Zeiträume mit hoher Auslastung gibt. Dieses Auslastungsmuster eignet sich ideal für die gemeinsame, datenbankübergreifende Nutzung von Ressourcen. Eine Datenbank eignet sich für die Aufnahme in einen Pool, wenn die Spitzenauslastung etwa um das Dreifache höher als die mittlere Auslastung ist.

    
***Beispiel***<br> Eine S3-Datenbank, die zu Spitzenzeiten 100 DTUs und im Durchschnitt 30 DTUs oder weniger benötigt, ist ein guter Kandidat für die gemeinsame Nutzung von DTUs in einem elastischen Pool. Alternativ ist auch eine S1-Datenbank, die zu Spitzenzeiten 20 DTUs und im Durchschnitt 7 DTUs oder weniger benötigt, ist ein guter Kandidat für einen elastischen Pool.
    

## Heuristik zum Vergleich des Preisunterschieds zwischen einem elastischen Pool und einzelnen Datenbanken 

Die folgende Heuristik kann bei der Einschätzung helfen, ob ein elastischer Pool kostengünstiger als die Verwendung einzelner Datenbanken ist.

1. Wir schätzen die für den Pool benötigten DTUs wie folgt:
    
    MAX(*Gesamtanzahl Datenbanken* \* *durchschnittliche DTU-Auslastung pro Datenbank*, *Anzahl von gleichzeitig unter Spitzenlast ausgeführten Datenbanken* \* *Spitzen-DTU-Auslastung pro Datenbank*)

2. Wählen Sie den kleinsten verfügbaren DTU-Wert für den Pool aus, der größer als die Schätzung aus Schritt 1 ist. Die in Frage kommenden DTU-Auswahlmöglichkeiten finden Sie hier unter den gültigen Werten für DTUs: [DTUs und Speicherbeschränkungen für elastische Pools und elastische Datenbanken](sql-database-elastic-pool-reference.md#dtu-and-storage-limits-for-elastic-pools-and-elastic-databases).

    


3. Wir berechnen den Preis für den Pool wie folgt:

    Preis für den Pool = (*Pool-DTUs* \* *Preis pro Einheit per Pool-DTU*) + (*Gesamtanzahl Datenbanken* \* *Preis pro Einheit per Pool-DTU*)

    Preisinformationen finden Sie unter [SQL-Datenbank – Preisgestaltung](http://azure.microsoft.com/pricing/details/sql-database/).


4. Wir vergleichen den Pool-Preis aus Schritt 3 mit dem Preis für die Verwendung der geeigneten Leistungsstufen für einzelne Datenbanken.



## Ermitteln der besten Pool-DTU-Größe für vorhandene SQL-Datenbanken 

Die beste Größe für einen elastischen Pool ist von den zusammengefassten DTUs und den Speicherressourcen abhängig, die für alle Datenbanken im Pool benötigt werden. Dies setzt voraus, dass Sie die größere der beiden folgenden Mengen ermitteln:

* Maximale Anzahl DTUs, die von allen Datenbanken im Pool verwendet werden.
* Maximale Speicherbytes, die von allen Datenbanken im Pool verwendet werden. 

Beachten Sie, dass auf der Standarddienstebene 1 GB Speicher für jede für den Pool konfigurierte DTU bereitgestellt wird. Wenn der Pool beispielsweise für 200 DTUs konfiguriert ist, beträgt die Speicherkapazität 200 GB.

### Verwenden von Service Tier Advisor (STA) und Dynamic Management Views (DMVs) für Größenempfehlungen   

STA und DMVs bieten unterschiedliche Tools und Optionen für die Größenauslegung eines elastischen Pools. Ungeachtet der verwendeten Tools sollte die Größeneinschätzung nur für die Erstbewertung und -erstellung von elastischen Pools zugrunde gelegt werden. Nachdem ein elastischer Pool erstellt wurde, muss die Ressourcennutzung akkurat überwacht werden, wobei die Leistungseinstellungen des Pools je nach Bedarf nach oben oder unten korrigiert werden können.

**STA**<br>STA ist ein in das Azure-Portal integriertes Tool, mit dem sich automatisch der Verlauf der Ressourcennutzung von Datenbanken auf einem vorhandenen SQL-Server ermitteln lässt und das auf dieser Basis eine geeignete Konfiguration für den elastischen Pool vorschlägt.

**DMV-Tool für die Größenfestlegung**<br>Das DMV-Tool für die Größenfestlegung wird als PowerShell-Skript bereitgestellt und ermöglicht die Anpassung von geschätzten Größen eines elastischen Pools an vorhandene Datenbanken auf einem Server.

### Auswählen zwischen STA und DMV 

Wählen Sie das Tool aus, das für die Analyse Ihrer speziellen Anwendung am besten geeignet ist. In der folgenden Tabelle werden die Unterschiede zwischen den beiden Tools erläutert:

| Funktion | STA | DMVs |
| :--- | :--- | :--- |
| Granularität von für die Analyse verwendeten Datenmustern | 15 Sekunden | 15 Sekunden |
| Berücksichtigt Preisunterschiede zwischen einem Pool und Leistungsstufen für einzelne Datenbanken | Ja | Nein |
| Ermöglicht die Anpassung der Liste der Datenbanken, die auf einem Server analysiert wurden | Nein | Ja |
| Ermöglicht die Anpassung des Zeitraums der Analyse | Nein | Ja |


### Schätzen der Größe des elastischen Pools mit STA  

STA bewertet den Verlauf der Datenbanknutzung und empfiehlt einen elastischen Pool, wenn dies kostengünstiger als die Verwendung von Leistungsstufen für einzelne Datenbanken ist. Wenn ein elastischer Pool empfohlen wird, stellt das Tool eine Liste der empfohlenen Datenbanken bereit und empfiehlt zudem die Anzahl der Pool-DTUs sowie Höchst- und Tiefstwerte für DTU-Einstellungen für jede elastische Datenbank. Damit eine Datenbank als Kandidat für einen elastischen Pool berücksichtigt wird, muss sie seit mindestens 14 Tagen existieren. Die öffentliche Vorschau auf den elastischen Datenbankpool ist auf die Standardansicht beschränkt, sodass Premiumdatenbanken noch nicht als Kandidaten für einen elastischen Pool berücksichtigt werden.

STA steht im Azure-Portal zur Verfügung, wenn Sie einem vorhandenen Server einen elastischen Pool hinzufügen. Wenn für diesen Server Empfehlungen für einen elastischen Pool verfügbar sind, werden sie auf der Erstellungsseite „Elastischer Datenbankpool“ angezeigt. Kunden können die empfohlenen Konfigurationen jederzeit ändern, um eigene Gruppen aus elastischen Pools zu erstellen.

### Schätzen der Größe von elastischen Pools mit DMVs (Dynamic Management Views) 

Mit den von [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) generierten Messwerten kann die Ressourcenverwendung einer einzelnen Datenbank gemessen werden. Diese DMV enthält Informationen zur CPU, zur E/A, zur Protokollierung und zur Protokollauslastung einer Datenbank als Prozentsatz der Leistungsbegrenzung der Datenbank. Diese Daten können zum Berechnen der DTU-Auslastung einer Datenbank in beliebigen Intervallen von jeweils 15 Sekunden verwendet werden.

Die zusammengefasste DTU-Auslastung eines elastischen Pools in einem 15 Sekunden-Intervall kann geschätzt werden, indem die DTU-Auslastung aller Datenbankkandidaten während dieser Zeit aggregiert wird. Abhängig von den jeweiligen Leistungszielen ist es sinnvoll, einen geringen Prozentsatz der Beispieldaten zu verwerfen. So kann der 99-zigste Perzentilwert der zusammengefassten DTUs über alle Zeitintervalle beispielsweise angewendet werden, um Ausreißer auszuschließen und einen DTU für den elastischen Pool bereitzustellen, der für 99 % der geprüften Zeitintervalle zutrifft.

## PowerShell-Skript zum Schätzen der zusammengefassten DTU-Auslastung Ihrer Datenbanken

Sie finden hier ein PowerShell-Skript zum Schätzen der zusammengefassten DTU-Werte für Benutzerdatenbanken auf einem SQL-Datenbankserver.

Mit dem Skript werden nur zur Laufzeit Daten gesammelt. Bei einem typischen Produktionsworkload sollten Sie das Skript einen Tag lang ausführen, obwohl Sie über eine Woche oder einen noch längeren Zeitraum zu exakteren Schätzungen kommen. Führen Sie das Skript für die Zeitdauer aus, die den typischen Workload Ihrer Datenbanken repräsentiert.

> [AZURE.IMPORTANT]Beim Ausführen des Skripts muss das PowerShell-Fenster geöffnet sein. Schließen Sie das PowerShell-Fenster erst, wenn das Skript ausreichend lange ausgeführt wurde und wenn genügend Daten gesammelt wurden, um den Workload zu normalen Zweiten und zu Spitzenauslastungszeiten zu repräsentieren.

### Voraussetzungen für das Skript 

Installieren Sie Folgendes, bevor Sie das Skript ausführen:

- Die neuesten [Powershell-Befehlszeilentools](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
- Das [SQL Server 2014-Featurepack](https://www.microsoft.com/download/details.aspx?id=42295).


### Details zum Skript


Sie können das Skript auf Ihrem lokalen Computer oder in einem virtuellen Computer in der Cloud ausführen. Wenn Sie es auf Ihrem lokalen Computer ausführen, kann dies Gebühren für die Datenübergabe verursachen, da das Skript Daten aus den Zieldatenbanken herunterladen muss. Nachstehend sind die Schätzungen für das Datenvolumen basierend auf der Anzahl von Zieldatenbanken und der Ausführungsdauer des Skripts angegeben. Informationen zu Azure-Datenübertragungskosten finden Sie unter [Details zu den Datenübertragungskosten](http://azure.microsoft.com/pricing/details/data-transfers/).
       
 -     1 Datenbank pro Stunde = 38 KB
 -     1 Datenbank pro Tag = 900 KB
 -     1 Datenbank pro Woche = 6 MB
 -     100 Datenbanken pro Tag = 90 MB
 -     500 Datenbanken pro Woche = 3 GB

Das Skript schließt bestimmte Datenbanken aus, die keine guten Kandidaten für die aktuelle öffentliche Vorschau auf der Ebene „Standardmäßiger elastischer Pool“ sind. Wenn Sie auf dem Zielserver weitere Datenbanken ausschließen müssen, können Sie das Skript Ihren Anforderungen entsprechend ändern. Standardmäßig kompiliert das Skript keine Informationen für Folgendes:

* Alle Premiumdatenbanken auf V12-Servern
* P2- und P3-Datenbanken auf V11-Servern
* Elastische Datenbanken (Datenbanken, die sich bereits in einem elastischen Pool befinden)
* Die Masterdatenbank auf dem Server

Für das Skript ist eine Ausgabedatenbank zum Speichern von Zwischendaten zwecks Analyse erforderlich. Sie können eine neue Datenbank anlegen oder eine vorhandene verwenden. Obwohl es für die Ausführung des Tools technisch nicht erforderlich ist, empfiehlt es sich, die Ausgabedatenbank auf einem anderen Server zu speichern, um Auswirkungen auf das Analyseergebnis zu vermeiden. Gehen Sie bei der Leistungsstufe der Ausgabedatenbank von mindestens S0 oder höher aus. Beim Sammeln von Daten über einen längeren Zeitraum für eine große Anzahl von Datenbanken sollten Sie überlegen, ob die Ausgabedatenbank auf eine höhere Leistungsstufe aktualisiert werden sollte.

Für das Skript müssen Sie Anmeldeinformationen angeben, um die Verbindung zum Zielserver herzustellen (dem Kandidaten für den elastischen Datenbankpool), und zwar mit einem vollständigen Servernamen wie „abcdef.database.windows.net“. Zurzeit unterstützt das Skript nicht die gleichzeitige Analyse mehrerer Server.



Nach der Übermittlung von Werten für den anfänglichen Parametersatz werden Sie aufgefordert, sich bei Ihrem Azure-Konto anzumelden. Hiermit melden Sie sich beim Zielserver und nicht beim Datenbankausgabeserver an.
	
Wenn beim Ausführen des Skripts die folgenden Warnungen ausgegeben werden, können Sie diese ignorieren:

- WARNUNG: Das Cmdlet „Switch-AzureMode“ ist veraltet.
- WARNUNG: Es konnten keine SQL Server-Dienstinformationen abgerufen werden. Fehler bei einem Versuch, eine Verbindung zu WMI auf 'Microsoft.Azure.Commands.Sql.dll' herzustellen: Der RPC-Server ist nicht verfügbar.

Nach Abschluss des Skripts wird die geschätzte Anzahl DTUs ausgegeben, die für einen elastischen Pool erforderlich sind, der alle Datenbankkandidaten auf dem Zielserver enthält. Diese geschätzten DTUs können zum Erstellen und Konfigurieren eines elastischen Pools für diese Datenbanken verwendet werden. Nachdem der Pool erstellt und Datenbanken in diesen Pool verschoben wurden, sollte er für einige Tage genauestens überwacht werden, um im Bedarfsfall Anpassungen an der Konfiguration der Pool-DTUs vornehmen zu können.


Wenn Sie das vollständige Skript kopieren möchten, klicken Sie drei Mal auf einen beliebigen Text im Skript (Dreifach-Klick).

    
    param (
    [Parameter(Mandatory=$true)][string]$AzureSubscriptionName, # Azure Subscription name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$ResourceGroupName, # Resource Group name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$servername, # full server name like "abcdefg.database.windows.net"
    [Parameter(Mandatory=$true)][string]$username, # user name
    [Parameter(Mandatory=$true)][string]$serverPassword, # password
    [Parameter(Mandatory=$true)][string]$outputServerName, # metrics collection database for analysis. full server name like "zyxwvu.database.windows.net"
    [Parameter(Mandatory=$true)][string]$outputdatabaseName,
    [Parameter(Mandatory=$true)][string]$outputDBUsername,
    [Parameter(Mandatory=$true)][string]$outputDBpassword,
    [Parameter(Mandatory=$true)][int]$duration_minutes # How long to run. Recommend to run for the period of time when your typical workload is running. At least 10 mins.
    )
    
    Add-AzureAccount 
    Select-AzureSubscription $AzureSubscriptionName
    Switch-AzureMode AzureResourceManager
    
    $server = Get-AzureSqlServer -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    
    # Check version/upgrade status of the server
    $upgradestatus = Get-AzureSqlServerUpgrade -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    $version = ""
    if ([string]::IsNullOrWhiteSpace($server.ServerVersion)) 
    {
    $version = $upgradestatus.Status
    }
    else
    {
    $version = $server.ServerVersion
    }
    
    # For Elastic database pool candidates, we exclude master, Premium SKU (P1 in previous version still qualifies), and any databases that are already in a pool. You may add more to the excluded list below as needed
    if ($version -in ("12.0", "Completed")) # This is on Azure Database V12. 
    {
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceLevelObjectiveName -notlike ("P*") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notlike ("P*")}
    }
    else # This is previous versions of Azure Database
    {
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool", "P2", "P3") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool", "P2", "P3")}
    }
    
    # Due to pricing model difference, we don't recommend to put less than 3 databases in a pool. 
    if ($ListOfDBs.Count -lt 3)
    {
    Write-Host "There are less than 3 qualified standalone databases in this server. Not a good candidate for Elastic Database Pool!" -ForegroundColor Red
    }
    
    $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
    $destinationTableName = "resource_stats_output"
    
    # Create a table in output database for metrics collection
    $sql = "
    IF  NOT EXISTS (SELECT * FROM sys.objects 
    WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))
    
    BEGIN
    Create Table $($destinationTableName) (database_name varchar(128), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
    Create Clustered Index ci_endtime ON $($destinationTableName) (end_time);
    END
    TRUNCATE TABLE $($destinationTableName);
    "
    Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 120 
    
    # waittime (minutes) is interval between data collection queries in the loop below.
    $Waittime = 10
    $end_Time = [DateTime]::UtcNow
    $start_time = $end_time.AddMinutes(-$Waittime)
    $finish_time = $end_Time.AddMinutes($duration_minutes)
    
    While ($end_time -lt $finish_time)
    {
     foreach ($db in $ListOfDBs)
     {
    if ($version -in ("12.0", "Completed")) # for V12 databases 
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'S3' THEN 100
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
    SELECT @dbname as database_name, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    else
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'P1' THEN 100 -- for old version of SQL database, P1 has same performance capacity as S3.
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
    SELECT @dbname as database_name, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    #write-host $sql
    
    $result = Invoke-Sqlcmd -ServerInstance $servername -Database $db.DatabaseName -Username $username -Password $serverPassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 3600 
    #bulk copy the metrics to output database
    $bulkCopy = new-object ("Data.SqlClient.SqlBulkCopy") $outputConnectionString 
    $bulkCopy.BulkCopyTimeout = 600
    $bulkCopy.DestinationTableName = "$destinationTableName";
    $bulkCopy.WriteToServer($result);
    
     }
    
     $start_time = $start_time.AddMinutes($Waittime)
     $end_time = $end_time.AddMinutes($Waittime)
     Write-Host $start_time
     Write-Host $end_time
     do {
    Start-Sleep 1
    }
    until (([DateTime]::UtcNow) -ge $end_time)
    }
    
    
    # Analysis query that does aggregation of the resource metrics to calculate pool size.
    $sql = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
    WITH group_stats AS
    (
    SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
    FROM resource_stats_output
    GROUP BY end_time
    )
    -- calculate aggregate storage and DTUs for all DBs in the group
    , group_DTU AS
    (
    SELECT end_time, avg_group_Storage, 
    (SELECT Max(v)
       FROM (VALUES (avg_group_cpu), (avg_group_log), (avg_group_io)) AS value(v)) AS avg_group_DTU
    FROM group_stats
    )
    -- Get top 1 percent of the storage and DTU utilization samples.
    , top1_percent AS (
    SELECT TOP 1 PERCENT avg_group_Storage, avg_group_dtu FROM group_dtu ORDER BY [avg_group_DTU] DESC
    )
    
    -- Max and 99th percentile DTU for the given list of databases if converted into an elastic pool. Storage is increased by factor of 1.25 to accommodate for future growth. Currently storage limit of the pool is determined by the amount of DTUs based on 1GB/DTU.
    --SELECT MAX(avg_group_Storage)*1.25/1024.0 AS Group_Storage_DTU, MAX(avg_group_dtu) AS Group_Performance_DTU, MIN(avg_group_dtu) AS Group_Performance_DTU_99th_percentile FROM top1_percent;
    SELECT @DTU_Storage = MAX(avg_group_Storage)*1.25/1024.0, @DTU_Perf_99 = MIN(avg_group_dtu) FROM top1_percent;
    IF @DTU_Storage > @DTU_Perf_99 
    SELECT @DTU_Storage AS "Pool Size DTU dominated by storage"
    ELSE 
    SELECT @DTU_Perf_99 AS "Pool Size DTU dominated by resource consumption";'
    
    #write-host $sql
    Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    
    

## Zusammenfassung

Nicht alle eigenständigen Datenbanken sind gute Kandidaten für elastische Datenbankpools. Datenbanken mit Auslastungsmustern, die sich durch geringe durchschnittliche Auslastung und relativ seltene Auslastungsspitzen auszeichnen, sind herausragende Kandidaten für elastische Pools. Die Nutzungsmuster von Anwendungen sind dynamisch; verwenden Sie die in diesem Artikel angegebenen Informationen und Tools also für eine erste Einschätzung, um zu sehen, ob ein elastischer Datenbankpool für einige oder alle Ihre Datenbanken eine gute Wahl ist. Dieser Artikel ist lediglich ein Ausgangspunkt, um Sie bei der Entscheidung zu unterstützen, ob ein elastischer Datenbankpool für Sie geeignet ist. Denken Sie daran, dass Sie den Verlauf der Ressourcennutzung (mit STA oder DVMs) kontinuierlich überwachen sollten und dass auch die Leistungsstufen aller Datenbanken immer wieder neu bewertet werden müssen. Vergessen Sie nicht, dass Sie Datenbanken ganz einfach in oder aus elastischen Pools verschieben können, und wenn Sie über eine sehr große Anzahl von Datenbanken verfügen, können Sie auch unterschiedliche Pools mit unterschiedlichen Größen erstellen, auf die Sie Ihre Datenbanken verteilen.



<!--Image references-->
[1]: ./media/sql-database-elastic-pool-guidance/one-database.png
[2]: ./media/sql-database-elastic-pool-guidance/four-databases.png
[3]: ./media/sql-database-elastic-pool-guidance/twenty-databases.png

<!---HONumber=August15_HO6-->