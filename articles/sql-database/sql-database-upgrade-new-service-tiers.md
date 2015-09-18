<properties 
	pageTitle="Upgrade von Web- oder Business-Datenbanken der SQL-Datenbank auf neue Dienstebenen"
	description="Führen Sie ein Upgrade der Web- oder Business-Datenbanken von Azure SQL-Datenbank auf die neuen Dienstebenen und Leistungsstufen ";Basic";, ";Standard"; und ";Premium"; von Azure SQL-Datenbank durch."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/18/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Upgrade von Web- oder Business-Datenbanken der SQL-Datenbank auf neue Dienstebenen

Die Azure SQL-Web- und Business-Datenbanken werden [im September 2015 eingestellt](http://msdn.microsoft.com/library/azure/dn741330.aspx). Beginnen Sie daher schon jetzt, das Upgrade Ihrer vorhandenen Web- oder Business-Datenbanken auf die Dienstebene Basic, Standard oder Premium zu planen.

Laden Sie das [Cookbook mit Anleitungen für die Migration von Web- und Business-Datenbanken](http://download.microsoft.com/download/3/C/9/3C9FF3D8-E702-4F5D-A38C-5EFA1DBA73E6/Azure_SQL_Database_Migration_Cookbook.pdf) herunter.

> [AZURE.NOTE] [Pricing tier recommendations](sql-database-service-tier-advisor.md) für Web- und Business-Datenbanken ist jetzt verfügbar.

## Übersicht

<p> Azure Web- und Business-SQL-Datenbanken werden in einer gemeinsamen, mehrinstanzenfähigen Umgebung ohne jede reservierte Ressourcenkapazität für die Datenbank ausgeführt. Die Aktivität anderer Datenbanken in dieser gemeinsam genutzten Ressourcenumgebung kann sich auf die Leistung auswirken. Die Verfügbarkeit von Ressourcen hängt zu jedem Zeitpunkt wesentlich von anderen parallelen Arbeitsauslastungen ab, die im System ausgeführt werden. Dies kann zu einer sehr unterschiedlichen und unvorhersehbaren Datenbankanwendungsleistung führen. Das Feedback von Kunden besagt, dass diese unvorhersehbare Leistung schwierig zu verwalten ist und eine besser vorhersagbare Leistung wünschenswert wäre.

Aufgrund dieses Feedbacks wurden für den Azure SQL-Datenbankdienst neue Datenbank-Dienstebenen [(Basic, Standard und Premium)](http://msdn.microsoft.com/library/dn741340.aspx) eingeführt, die vorhersagbare Leistung und eine Fülle neuer Funktionen für Geschäftskontinuität und Sicherheit bieten. Diese neuen Dienstebenen stellen eine angegebene Ressourcenebene für die Arbeitsauslastung einer Datenbank unabhängig von anderen Arbeitsauslastung von Kunden zur Verfügung, die in dieser Umgebung ausgeführt werden. Dies führt zu einem hochgradig vorhersagbaren Leistungsverhalten.

Diese Änderungen werfen jedoch auch neue Fragen auf. Sie müssen evaluieren und entscheiden, welche neue Dienstebene am besten für die aktuellen Web- und Business-Datenbanken geeignet ist und wie das eigentliche Upgrade erfolgen soll.

Die beste Wahl ist schließlich eine Kombination aus Dienstebene und Leistungsstufe, die Ihnen das optimale Gleichgewicht zwischen Funktionen, Leistung und Kosten bietet und zugleich alle geschäftlichen Erfordernisse und die Anforderungen der Anwendung erfüllt.

Dieser Artikel enthält eine schrittweise Anleitung für das Upgrade von Web-/Business-Datenbanken auf eine der neuen Dienstebenen/Leistungsstufen.

Ein wichtiger Faktor dabei ist, dass Azure SQL-Datenbanken nicht auf eine bestimmte Dienstebene oder Leistungsstufe beschränkt sind. Wenn sich die Anforderungen Ihrer Datenbank ändern, können Sie also einfach zwischen den verfügbaren Dienstebenen und Leistungsstufen wechseln. SQL-Datenbanken der Editionen Basic, Standard und Premium werden tatsächlich nach Stunde berechnet. Sie haben die Möglichkeit, jede Datenbank innerhalb eines 24-Stunden-Intervalls vier Mal auf eine höhere oder niedrigere Stufe zu skalieren (entweder im [Azure-Verwaltungsportal oder programmgesteuert](http://msdn.microsoft.com/library/azure/ff394116.aspx)). Sie können die Dienstebene und Leistungsstufe also anpassen, um die Leistungsanforderungen, den Funktionsumfang und die Kosten für die Datenbank abhängig von den Anwendungsanforderungen und schwankender Arbeitsauslastung zu maximieren. Gleichzeitig bedeutet dies aber auch, dass die Evaluierung und Anpassung der Dienstebene und Leistungsstufe Ihrer Datenbank (nach oben und unten) ein fortlaufender Prozess ist, der in Ihre planmäßige Wartung und Leistungsoptimierung eingebettet werden sollte.


## Upgrade von Web- und Business-Datenbanken

Beim Upgrade einer Web- oder Business-Datenbank auf eine neue Dienstebene/Leistungsstufe wird die Datenbank nicht offline geschaltet. Die Datenbank ist während des Upgradevorgangs weiterhin funktionsfähig. Zum Zeitpunkt des tatsächlichen Übergangs auf die neue Leistungsstufe können die Datenbankverbindungen ggf. für einen sehr kurzen Zeitraum (normalerweise im Sekundenbereich) vorübergehend unterbrochen werden. Wenn eine Anwendung über Maßnahmen zur Behandlung vorübergehender Fehler bei Verbindungsunterbrechungen verfügt, ist es ausreichend, am Ende des Upgrades Schutz vor unterbrochenen Verbindungen vorzusehen.

Das Upgrade einer Web- oder Business-Datenbank auf eine neue Dienstebene umfasst die folgenden Schritte:


1. Ermitteln der Dienstebene basierend auf dem Funktionsumfang
2. Ermitteln einer angemessenen Leistungsstufe basierend auf der bisherigen Ressourcennutzung
3. Warum entspricht die aktuelle Leistung meiner Web- oder Business-Datenbank höheren Premium-Stufen?
4. Optimieren der Arbeitsauslastung in Anpassung an eine niedrigere Leistungsstufe
5. *Upgrade auf die neue Dienstebene/Leistungsstufe*
6. Überwachen des Upgrades auf die neue Dienstebene/Leistungsstufe
7. Überwachen der Datenbank nach dem Upgrade



## 1\. Ermitteln der Dienstebene basierend auf dem Funktionsumfang

Die Dienstebenen Basic, Standard und Premium bieten einen unterschiedlichen Funktionsumfang. Der erste Schritt bei der Auswahl der geeigneten Ebene besteht also darin, die Dienstebene mit dem Mindestfunktionsumfang zu ermitteln, der für Ihre Anwendung und das Unternehmen erforderlich ist.

Dabei ist z. B. zu berücksichtigen, wie lange Sicherungen aufbewahrt werden müssen, ob die [standardmäßige oder aktive Georeplikation](http://msdn.microsoft.com/library/azure/dn783447.aspx) benötigt wird, und welche maximale Datenbankgröße erforderlich ist. Von diesen Faktoren hängt es ab, welche minimale Dienstebene in Frage kommt.

Die Ebene „Basic“ wird in erster Linie für sehr kleine Datenbanken mit geringer Aktivität verwendet. Bei einem Upgrade starten Sie daher normalerweise mit der Ebene „Standard“ oder „Premium“, je nachdem, welcher Funktionsumfang mindestens erforderlich ist.

Die Funktionen und Leistungsstufen der neuen Dienstebene werden in der folgenden Tabelle zusammengefasst und verglichen:

[AZURE.INCLUDE [Tarife für SQL-Datenbank](../../includes/sql-database-service-tiers-table.md)]

**Zusätzliche Ressourcen für den Vergleich von Dienstebenen und Leistungsstufen:**

| Artikel | Beschreibung |
|:--|:--|
|[Dienstebenen (Editionen) der Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/dn741340.aspx)| Übersicht über die Dienstebenen "Basic", "Standard" und "Premium".|
|[Dienst- und Leistungsebenen von Azure SQL-Datenbank](http://msdn.microsoft.com/library/dn741336.aspx)| Metriken und Funktionsumfang jeder Dienstebene (und Vorgehensweise bei der Überwachung der Datenbankauslastung im Verwaltungsportal und mithilfe von DMVs). |
|[Worin unterscheiden sich die Dienstebenen?](http://msdn.microsoft.com/library/dn369873.aspx#Different)| Weitere Informationen zu den verschiedenen Dienstebenen einschließlich der Gründe, die für oder gegen eine Ebene sprechen. |
|[Geschäftskontinuität in Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/hh852669.aspx)|Details zu den Funktionen für die Geschäftskontinuität und Notfallwiederherstellung (Zeitpunktwiederherstellung, Geowiederherstellung, Georeplikation) der unterschiedlichen Dienstebenen.|
|[SQL-Datenbank – Preisdetails](http://azure.microsoft.com/pricing/details/sql-database/)|Detaillierte Preisinformationen zu den unterschiedlichen Dienstebenen und Leistungsstufen.|

<br>

Nach Auswahl einer geeigneten Dienstebene, die die Anforderungen der Datenbank erfüllt, besteht der nächste Schritt in der Auswahl einer Leistungsstufe, die auf Ihre tatsächliche Datenbank-Arbeitsauslastung abgestimmt ist.



## 2\. Ermitteln einer angemessenen Leistungsstufe basierend auf der bisherigen Ressourcennutzung

Der Azure SQL-Datenbankdienst zeigt Informationen im Verwaltungsportal und in den Systemsichten an, um Ihnen eine neue Dienstebene und Leistungsstufe für Ihre vorhandene Web- oder Business-Datenbank vorzuschlagen.

Da Web-/Business-Datenbanken keine garantierten DTUs/Ressourcengrenzwerte zugeordnet sind, werden die Prozentwerte gemäß der Ressourcenmenge normalisiert, die für eine Datenbank der Leistungsstufe S2 zur Verfügung steht. Der durchschnittliche DTU-Prozentsatzverbrauch einer Datenbank in einem bestimmten Intervall kann als der höchste Prozentsatzwert für CPU, E/A und Protokollverwendung in diesem Intervall berechnet werden.


Im Verwaltungsportal erhalten Sie einen allgemeinen Überblick über die prozentualen DTU-Verbrauchswerte. Von dort aus können Sie über die Systemsichten detailliertere Informationen abrufen.

Sie können auch das neue Azure-Verwaltungsportal verwenden, um die empfohlene Dienstebene für Ihre Web- oder Business-Datenbank anzuzeigen, wenn Sie einen Server auf Azure SQL-Datenbank V12 ([in der Vorschau in einigen Regionen](sql-database-preview-whats-new.md#V12AzureSqlDbPreviewGaTable)) aktualisieren.

### Anzeigen der empfohlenen Dienstebene im neuen Azure-Verwaltungsportal
Das Verwaltungsportal empfiehlt die entsprechende Dienstebene für Ihre Web- oder Business-Datenbank im Rahmen der Aktualisierung eines Servers auf Azure SQL-Datenbank V12. Die Empfehlung basiert auf einer Verlaufsanalyse des Ressourcenverbrauchs der Datenbank.

**Neues Verwaltungsportal**

1. Melden Sie sich bei dem[ neuen Verwaltungsportal](https://portal.azure.com) an, und navigieren Sie zu einem Server mit einer vorhandenen Web- oder Business-Datenbank.
2. Klicken Sie auf dem Blatt "Server" auf **Neueste Aktualisierung**.
3. Klicken Sie auf **Diesen Server aktualisieren**.

Das Blatt **Diesen Server aktualisieren** zeigt jetzt eine Liste der Web- oder Business-Datenbanken auf dem Server zusammen mit der empfohlenen Dienstebene an.



### Anzeigen des DTU-Verbrauchs im Verwaltungsportal
Das Verwaltungsportal gibt Einblicke in den DTU-Verbrauch einer vorhandenen Web- oder Business-Datenbank. DTU-Informationen finden Sie im aktuellen Azure-Portal.


**Verwaltungsportal**

1. Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com) an, und navigieren Sie zu einer vorhandenen Web- oder Business-Datenbank.
2. Klicken Sie auf die Registerkarte **ÜBERWACHEN**.
3. Klicken Sie auf **METRIKEN HINZUFÜGEN**.
4. Wählen Sie **DTU-Prozentsatz** aus, und bestätigen Sie die Auswahl, indem Sie auf das darunter angezeigte Häkchen klicken.

Nach der Bestätigung sollten Daten zum **DTU-Prozentsatz** in der Tabelle angezeigt werden. Dieser Prozentsatz ergibt sich aus einem Vergleich mit DTUs einer Datenbank der Leistungsstufe S2 (50 DTUs).

Außerdem ist zu beachten, dass diese Daten dem Mittelwert der Stichproben entsprechen, die alle fünf Minuten gezogen werden. Folglich kann es zu vorübergehenden Aktivitätsspitzen zwischen Stichproben kommen, die in den Metriken jedoch unberücksichtigt bleiben.

![Daten zum DTU-Prozentsatz][2]

Die Daten im vorangehenden Beispiel zeigen die durchschnittliche Auslastung von ca. 10 DTUs (19,23 % von 50) und einen maximalen DTU-Prozentwert von ~ 28 DTUs (55,83 % x 50). Wenn diese Daten eine typische Arbeitsauslastung widerspiegeln, empfiehlt sich für das erste Upgrade die Standardstufe S1. Standard(S0) umfasst 10 DTUs, was im konkreten Fall der durchschnittlichen Nutzung entspricht. Das bedeutet jedoch auch, dass die Datenbank im Durchschnitt immer bei 100 % Auslastung läuft. Ein solcher Nutzungsplan ist sicher keine gute Wahl. Wahrscheinlich ist S1 die richtige Wahl für die gegebene durchschnittliche Auslastung. Was passiert aber in den Zeiten, wenn der maximale Grenzwert erreicht wird? Es kann sein, dass diese Spitzen durch einen nachts durchgeführten Wartungsprozess verursacht werden, der sich nicht auf die Nutzung des Kunden auswirkt. In diesem Fall ist ein zeitlich beschränkter Leistungsabfall akzeptabel. Genau so gut kann aber auch unklar sein, wann der prozentuale DTU-Verbrauch den Maximalwert erreicht. Eine weitergehende Analyse schafft hier Klarheit.

Über die bereitgestellten Systemsichten können Sie detaillierte Informationen zum Ressourcenverbrauch einer Datenbank abrufen.


### Systemsichten


Die Ressourcenverbrauchsdaten von Web- und Business-Datenbanken können über die Sicht [sys.resource\_stats](http://msdn.microsoft.com/library/azure/dn269979.aspx) in der Masterdatenbank des logischen Servers eingesehen werden, auf dem die aktuelle Datenbank gespeichert ist. Die Ressourcenverbrauchsdaten werden in Prozentsätzen des Grenzwerts der Leistungsstufe angezeigt. Diese Sicht stellt Daten der letzten maximal 14 Tage in Intervallen von 5 Minuten zur Verfügung.

> [AZURE.NOTE]Sie können nun die Sicht [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) in Web und Business-Datenbanken verwenden, um eine Sicht mit höherer Genauigkeit (alle 15 Sekunden) der Ressourcenverbrauchsdaten zu erhalten. Da "sys.dm\_db\_resource\_stats" historische Daten nur eine Stunde lang speichert, können Sie diese DMV stündlich abfragen und die Daten zur weiteren Analyse speichern.

Führen Sie die folgende Abfrage für die Masterdatenbank aus, um den durchschnittlichen DTU-Verbrauch für eine Datenbank abzurufen:

 
                   
     SELECT start_time, end_time
	 ,(SELECT Max(v)
         FROM (VALUES (avg_cpu_percent)
                    , (avg_physical_data_read_percent)
                    , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.resource_stats
    WHERE database_name = '<your db name>'
    ORDER BY end_time DESC;

Die Daten, die von [resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) und [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) für Web- und Business-Ebenen zurückgegeben werden, geben die Prozentsätze in Bezug auf die Standard S2-Leistungsebene an. Wenn zum Beispiel beim Ausführen mit einer Webdatenbank der Wert 70 % zurückgegeben wird, weist dies auf 70 % des S2-Ebenenhöchstwerts hin. Darüber hinaus kann für Web- und Businessebenen der Prozentsatz eine Zahl über 100 % zurückgeben, was ebenfalls auf dem S2-Ebenenhöchstwert basiert.

Die DTU-Verbrauchsinformationen für eine S2-Datenbankstufe ermöglichen Ihnen das Normalisieren des aktuellen Verbrauchs Ihrer Web- und Business-Datenbanken im Hinblick auf Datenbanken der neuen Ebene sowie eine Beurteilung der besseren Eignung. Wenn Ihr durchschnittlicher prozentualer DTU-Verbrauch z. B. einen Wert von 80 % anzeigt, besagt dies, dass die Datenbank DTU mit einer Rate von 80 % des Grenzwerts einer Datenbank auf der S2-Leistungsstufe verbraucht. Wenn in der Sicht **sys.resource\_stats** Werte angezeigt werden, die größer als 100 % sind, bedeutet dies, dass Sie eine Leistungsstufe benötigen, die größer als S2 ist. Nehmen Sie z. B. an, dass ein Spitzenwert für den DTU-Prozentsatz von 300 % angezeigt wird. Dies bedeutet, dass Sie drei Mal mehr Ressourcen verwenden als in der S2-Stufe verfügbar wären. Zum Bestimmen einer angemessenen Anfangsgröße vergleichen Sie die in einer S2-Stufe verfügbaren DTUs (50 DTUs) mit den nächsthöheren Stufen (S3/P1 = 100 DTUs oder 200 % von S2, P2 = 200 DTUs oder 400 % von S2). Da Sie bei 300 % der S2-Stufe liegen, können Sie in diesem Fall mit einer P2-Stufe beginnen und dann erneut einen Test durchführen.

Basierend auf dem prozentualen DTU-Verbrauch und der größten Edition, die für Ihre Arbeitsauslastung benötigt wird, können Sie ermitteln, welche Dienstebene und Leistungsstufe für die Arbeitsauslastung Ihrer Datenbank am besten geeignet ist (wie durch den prozentualen DTU-Wert und die relativen DTU-Potenzen verschiedener [Leistungsstufen](http://msdn.microsoft.com/library/azure/dn741336.aspx) angegeben). Die folgende Tabelle zeigt eine Zuordnung des prozentualen Web-/Business-Ressourcenverbrauchs zu den entsprechenden Leistungsstufen der neuen Ebene:

![Ressourcenverbrauch][4]

> **Hinweis:**
> Relative DTU-Werte zwischen verschiedenen Leistungsstufen basieren auf der Arbeitsauslastung [Azure SQL-Datenbankvergleichstest](http://msdn.microsoft.com/library/azure/dn741327.aspx). Da die Arbeitsauslastung Ihrer Datenbank wahrscheinlich von diesem Vergleichstest abweicht, sollten Sie die oben aufgeführten Berechnungen als Richtlinie für eine anfängliche Einordnung Ihrer Web-/Business-Datenbank in die neuen Dienstebenen verwenden. Nachdem Sie die Datenbank in die neue Ebene verschoben haben, verwenden Sie den im vorherigen Abschnitt beschriebenen Vorgang, um die richtigen Dienstebene zu überprüfen/optimieren, die für die Anforderungen Ihrer Arbeitsauslastung geeignet ist.
> 
> Während die vorgeschlagene Dienstebene/Leistungsstufe der neuen Edition Ihre Datenbankaktivität innerhalb der letzten 14 Tage berücksichtigt, basieren diese Daten auf den Datenstichproben für den Ressourcenverbrauch, gemittelt über 5 Minuten. Aus diesem Grund können ggf. kurzfristige Aktivitätsspitzen unberücksichtigt bleiben, die weniger als 5 Minuten dauern. Diese Anleitung sollte daher beim Upgrade der Datenbank als Ausgangspunkt verwendet werden. Nachdem Sie das Upgrade der Datenbank auf die empfohlene Ebene ausgeführt haben, sind weitere Überwachungs-, Test- und Überprüfungsvorgänge erforderlich, und die Datenbank kann nach Bedarf nach oben oder unten in eine andere Ebene/Leistungsstufe verschoben werden.

Die folgende Abfrage für die Masterdatenbank führt die Berechnung für die Datenbank der Web-/Business-Ebene aus und schlägt vor, welche Edition wahrscheinlich für die Arbeitsauslastung für jedes dieser Datenbeispielintervalle von 5 Minuten geeignet ist.

> **Hinweis**: Diese Abfrage eignet sich nur für Web-/Business-Datenbanken und ergibt **keine** korrekten Ergebnisse für Datenbanken in den neuen Dienstebenen.

    WITH DTU_mapping AS
    ( SELECT *
        FROM ( VALUES (1, 10,'Basic'), (2, 20,'S0'), (3, 40,'S1'), (4, 100, 'S2')
                    , (5, 200, 'S3/P1'), (6, 1600,'Premium')
           ) AS t(id, percent_of_S2, target_edition)
    ), rc as
    ( SELECT start_time, end_time
           , (SELECT Max(v)
                FROM (VALUES (avg_cpu_percent)
                       		, (avg_physical_data_read_percent)
                       		, (avg_log_write_percent)
                   ) AS value(v)) as [avg_DTU_percent]
        FROM sys.resource_stats	
       WHERE database_name = 'WebDB'
    )
    SELECT rc.*
         , (SELECT TOP(1) t.target_edition
              FROM DTU_mapping AS t
             WHERE t.percent_of_S2 > CAST(1.2*rc.avg_DTU_percent as int)
             ORDER BY t.percent_of_S2) as target_edition
    FROM rc;

**Beispielergebnis:**

![Beispielergebnis](media/sql-database-upgrade-new-service-tiers/sample_result.png)

In der grafischen Darstellung erkennen Sie den Trend des durchschnittlichen prozentualen DTU-Verbrauchs im Lauf der Zeit. Das folgende Beispieldiagramm zeigt eine Datenbank, die sich die meiste Zeit in der S2-Stufe befindet und einige Spitzenwerte aufweist, die bis zu einer P1-Datenbankebene ansteigen. Der DTU-Verbrauch im Lauf der Zeit variiert zwischen den Grenzwerten von "Basic" bis hin zu den Grenzwerten von "P1". Damit diese Datenbank vollständig in die neue Ebene integriert werden kann, benötigen Sie eine Datenbank der Premium-Dienstebene mit der Leistungsstufe "P1". Andererseits kann eine Datenbank mit der Leistungsstufe S2 funktionieren, wenn diese Spitzen in die Leistungsstufe P1 nur gelegentlich auftreten.

![DTU-Verbrauch](media/sql-database-upgrade-new-service-tiers/DTU_usage.png)

**Arbeitsspeicherauswirkungen auf die Leistung:** Arbeitsspeicher ist zwar eine der Ressourcendimensionen, die zur DTU-Bewertung beitragen - die SQL-Datenbank ist jedoch so konzipiert, dass der gesamte verfügbare Arbeitsspeicher für Datenbankvorgänge verwendet wird. Aus diesem Grund ist der Speicherverbrauch nicht im durchschnittlichen DTU-Verbrauch in der Abfrage oben enthalten. Andererseits wird der verfügbare Arbeitsspeicher für die Datenbank verringert, wenn Sie auf eine niedrigere Leistungsstufe wechseln. Dies kann zu einem höherer E/A-Verbrauch führen und sich auf die verbrauchten DTUs auswirken. Wenn Sie also auf eine niedrigere Leistungsstufe umsteigen, stellen Sie sicher, dass genügend Spielraum für den E/A-Prozentsatz verbleibt. Verwenden Sie die oben beschriebene dynamische Verwaltungssicht [sys.dm\_ db\_ resource\_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx), um diese Werte zu überwachen.



## 3\. Warum entspricht die aktuelle Leistung meiner Web- oder Business-Datenbank höheren Premium-Stufen?

Web- und Business-Datenbanken verfügen über keine reservierte Ressourcenkapazität für einzelne Datenbanken. Außerdem steht den Kunden kein Instrument zur Verfügung, um die Leistung einer Web- oder Business-Datenbank nach oben oder unten zu skalieren. Dies führt dazu, dass sich die Leistung einer Web- oder Business-Datenbank irgendwo zwischen extrem langsam und Premium-Level bewegen kann. Diese Leistungsschwankungen hängen *ungerechterweise* vom jeweiligen Gesamtressourcenverbrauch durch andere Datenbanken innerhalb einer mehrinstanzenfähigen Umgebung mit gemeinsamer Ressourcennutzung ab.

Das Ziel der Azure SQL-Datenbankdienste besteht jedoch ohne Zweifel darin, auf allen Web- und Business-Datenbanken möglichst 100 % Leistung zu erzielen. Inzwischen halten Web- und Business-Datenbanken ihre durchschnittliche Leistungsstufe erfolgreich auf dem Premium-Level. Das ist der Grund, warum die Leistung vorhandener Datenbanken den aktuellen Premium-Stufen entsprechen kann. Bei der Evaluierung des geeigneten Dienstebenen-Upgrades führt dies jedoch leider auch zu unrealistischen Erwartungen, wenn Web- und Business-Datenbanken mit den neuen Dienstebenen/Leistungsstufen verglichen werden.

Die folgende Abbildung hilft Ihnen dabei, die Unterschiede zwischen Web-/Business-Datenbanken und den Dienstebenen Basic, Standard und Premium besser zu verstehen. Hier sehen Sie neun SQL-Datenbanken im Web-/Business-Modell mit gemeinsamer Ressourcennutzung im Vergleich zu neun SQL-Datenbanken im Basic-, Standard- und Premium-Dienstebenenmodell. Beim Web-/Business-Modell erkennen Sie eindeutig die Auswirkungen, die "Noisy Neighbors" auf die anderen Datenbanken haben können, die dem Pool für die gemeinsame Ressourcennutzung ebenfalls angehören. Wenn auf einer Datenbank eine ressourcenintensive Arbeitsauslastung ausgeführt wird, wirkt sich dies auf alle anderen Datenbanken im Pool aus, und die Ressourcenverfügbarkeit nimmt ab. Auf der Dienstebene "Basic", "Standard" und "Premium" ***wird*** jeder Datenbank eine bestimmte Ressourcenkapazität zugeordnet, sodass andere Datenbanken innerhalb der gemeinsam genutzten Umgebung isoliert von "Noisy Neighbors" ausgeführt werden und nur den Grenzen der für die Datenbank ausgewählten Leistungsstufe unterliegen.

![Vorhersagbare Leistung der neuen Dienstebenen][3]

Wenn der prozentuale DTU-Gesamtwert sehr hoch ist, sollten Sie die Metriken der DTUs genauer analysieren. Insbesondere detaillierte Informationen zu Protokoll-E/A und Arbeitsspeichernutzung der Datenbank können aufschlussreich sein. So können Sie ggf. Problembereiche aufdecken, für die sich der DTU-Verbrauch verringern und optimieren lässt.


## 4\. Optimieren der Datenbankauslastung in Anpassung an eine niedrigere Leistungsstufe
Wenn die Analyse des historischen Ressourcenverbrauchs einer Datenbank ein Upgrade auf eine Leistungsstufe nahe legt, die Ihr Budget übersteigt, können Sie sich auch erst über weitere Möglichkeiten der Leistungsoptimierung informieren.

Wenn Ihnen der Ressourcenverbrauch Ihrer Anwendung gemessen an Ihren Erfahrungswerten sehr hoch erscheint und weit über der typischen Arbeitsauslastung liegt, können Sie u. U. einige weitere Möglichkeiten der Leistungsoptimierung für die Anwendung ausschöpfen.

Im Prinzip können alle Datenbanken von einer weiteren Leistungsoptimierung profitieren.

Zusätzlich zu typischen Optimierungsschritten wie Indexanalyse, Ausführungspläne usw. sollten Sie auch Datenzugriffsroutinen optimieren und auf die Azure SQL-Datenbank ausrichten.

| Artikel | Beschreibung |
|:--|:--|
| [Leitfaden zur Azure SQL-Datenbankleistung](http://msdn.microsoft.com/library/dn369873.aspx) | Der Abschnitt "Optimieren Ihrer Anwendung" enthält ausführliche Empfehlungen und Verfahren zur Leistungsoptimierung einer Azure SQL-Datenbank.|
|[Leistungsüberwachung und Optimierungstools](https://msdn.microsoft.com/library/ms179428.aspx)| Links und Beschreibungen der verfügbaren Tools für die Überwachung von SQL Server-Ereignissen und die Optimierung des physischen Datenbanklayouts.|
|[Überwachen und Optimieren der Leistung](https://msdn.microsoft.com/library/ms189081.aspx)|MSDN-Artikel zur SQL Server 2014-Leistungsoptimierung.|
| [Behandlung von Leistungsproblemen in SQL Server 2008](https://msdn.microsoft.com/library/dd672789.aspx) | Älteres, aber nach wie vor aktuelles Whitepaper mit Anleitungen zur Behandlung allgemeiner Leistungsprobleme, einschließlich nützlicher Informationen zur Behandlung von Arbeitsspeicher- und CPU-Engpässen.|
|[Problembehandlung und Optimieren von Abfragen bei Azure SQL-Datenbank](http://social.technet.microsoft.com/wiki/contents/articles/1104.troubleshoot-and-optimize-queries-with-azure-sql-database.aspx)|Älterer, aber nach wie vor nützlicher Artikel mit Informationen zu dynamischen Verwaltungssichten und wie diese zur Problembehandlung eingesetzt werden.|



## 5\. Upgrade auf die neue Dienstebene/Leistungsstufe
Nachdem Sie die geeignete Dienstebene/Leistungsstufe für die Web-/Business-Datenbank ermittelt haben, bieten sich mehrere Möglichkeiten für ein Upgrade der Datenbank auf die neue Ebene:

| Verwaltungstool | So ändern Sie die Dienstebene und Leistungsstufe einer Datenbank|
| :---| :---|
| [Azure-Verwaltungsportal](https://manage.windowsazure.com) | Klicken Sie auf der Seite "Dashboard" der Datenbank auf die Registerkarte **SKALIEREN**. |
| [Azure PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) | Verwenden Sie das Cmdlet [Set-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546732.aspx). |
| [Dienstverwaltungs-REST-API](http://msdn.microsoft.com/library/azure/dn505719.aspx) | Verwenden Sie den Befehl [Datenbank aktualisieren](http://msdn.microsoft.com/library/dn505718.aspx).|
| [Transact-SQL](http://msdn.microsoft.com/library/bb510741.aspx) | Verwenden Sie die Anweisung [ALTER DATABASE (Transact-SQL)](http://msdn.microsoft.com/library/ms174269.aspx). |

Ausführliche Informationen finden Sie unter [Ändern von Datenbank-Dienstebenen und -Leistungsstufen](http://msdn.microsoft.com/library/dn369872.aspx)


## 6\. Überwachen des Upgrades auf die neue Dienstebene/Leistungsstufe
Die Azure SQL-Datenbank stellt in der dynamischen Verwaltungssicht "sys.dm\_operation\_status" in der Masterdatenbank des logischen Servers, auf dem die aktuelle Datenbank gespeichert ist, Statusinformationen zu Verwaltungsvorgängen (z. B. "CREATE", "ALTER", "DROP") zur Verfügung, die für eine Datenbank ausgeführt werden [siehe Dokumentation zum Status des Vorgangs "sys.dm\_operation\_status"](http://msdn.microsoft.com/library/azure/dn270022.aspx) Verwenden Sie die dynamische Verwaltungssicht zum Vorgangsstatus, um den Status des Upgradevorgangs für eine Datenbank zu ermitteln. Die folgende Beispielabfrage zeigt alle Verwaltungsvorgänge, die für eine Datenbank ausgeführt werden:

    SELECT o.operation, o.state_desc, o.percent_complete
    , o.error_code, o.error_desc, o.error_severity, o.error_state
    , o.start_time, o.last_modify_time
    FROM sys.dm_operation_status AS o
    WHERE o.resource_type_desc = 'DATABASE'
    and o.major_resource_id = '<database_name>'
    ORDER BY o.last_modify_time DESC;

Wenn Sie das Verwaltungsportal für das Upgrade verwendet haben, ist auch eine Benachrichtigung aus dem Portal für den Vorgang verfügbar.

### Was geschieht, wenn die Arbeitsauslastung der Datenbank nach dem Upgrade die Ressourcengrenzwerte erreicht?
Leistungsstufen werden kalibriert und gesteuert, um die erforderlichen Ressourcen zum Ausführen der Arbeitsauslastung Ihrer Datenbank bis zu den maximalen Grenzwerten bereitzustellen, die für die ausgewählte Dienstebene/Leistungsstufe zulässig sind (d. h. der Ressourcenverbrauch liegt bei 100 %). Wenn die Arbeitsauslastung die Grenzwerte für CPU/Daten-E/A/Protokoll-E/A erreicht, erhalten Sie die Ressourcen auch weiterhin auf der maximal zulässigen Ebene. Es treten jedoch wahrscheinlich erhöhte Wartezeiten für Ihre Abfragen auf. Das Erreichen einer dieser Höchstgrenzen führt nicht zu Fehlern, sondern nur zu einer Verlangsamung Ihrer Arbeitsauslastung. Wenn die Verlangsamung jedoch zu schwerwiegend ist, tritt ein Timeout von Abfragen auf. Wenn Sie den Grenzwert für die maximal zulässigen gleichzeitigen Benutzersitzungen/-anforderungen (Arbeitsthreads) erreichen, erhalten Sie den [Fehler 10928 oder 10929](http://msdn.microsoft.com/library/azure/dn338078.aspx).


## 7\. Überwachen der Datenbank nach dem Upgrade
Nach dem Upgrade der Web-/Business-Datenbank auf die neue Ebene wird empfohlen, die Datenbank aktiv zu überwachen, um sicherzustellen, dass Anwendungen mit der gewünschten Leistung ausgeführt werden. Außerdem sollte die Nutzung optimiert werden. Die folgenden zusätzlichen Schritte werden zum Überwachen der Datenbank empfohlen.


**Ressourcenverbrauchsdaten:** Für Basic-, Standard- und Premium-Datenbanken sind präzisere Ressourcenverbrauchsdaten über eine neue dynamische Verwaltungssicht [sys.dm\_ db\_ resource\_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) in der Benutzerdatenbank verfügbar. Diese dynamische Verwaltungssicht stellt beinahe in Echtzeit Informationen zum Ressourcenverbrauch mit einer Genauigkeit von 15 Sekunden für die vorhergehende Stunde des Betriebs zur Verfügung. Der prozentuale DTU-Verbrauch für ein Intervall wird als maximaler prozentualer Verbrauch von CPU-, E/A- und Protokollressourcen berechnet. Die folgende Abfrage berechnet den durchschnittlichen prozentualen DTU-Verbrauch während der letzten Stunde:

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

 
In der zusätzlichen [Dokumentation](http://msdn.microsoft.com/library/dn800981.aspx) finden Sie Details zur Verwendung dieser dynamischen Verwaltungssicht. Im [Leitfaden zur Azure SQL-Datenbankleistung](http://msdn.microsoft.com/library/azure/dn369873.aspx) wird das Überwachen und Optimieren Ihrer Anwendung erläutert.


- **Warnungen:** Richten Sie im Azure-Verwaltungsportal "Warnungen" ein, damit Sie benachrichtigt werden, wenn der DTU-Verbrauch für eine aktualisierte Datenbank eine bestimmte hohe Ebene erreicht. Datenbankwarnungen können im Azure-Verwaltungsportal für verschiedene Leistungsmetriken wie DTU, CPU, E/A- und das Protokoll eingerichtet werden. 

	Sie können z. B. eine E-Mail-Benachrichtigung für den "DTU Prozentsatz" festlegen, wenn der durchschnittliche prozentuale DTU-Wert 75 % innerhalb der letzten 5 Minuten überschreitet. Lesen Sie [Vorgehensweise: Empfangen von Warnbenachrichtigungen und Verwalten von Warnungsregeln in Azure](http://msdn.microsoft.com/library/azure/dn306638.aspx), um weitere Informationen zum Konfigurieren von Warnbenachrichtigungen zu erhalten.


- **Geplantes Upgrade/Downgrade der Leistungsstufe:** Wenn Ihre Anwendung über bestimmte Szenarien verfügt, die nur zu bestimmten Zeiten der Woche bzw. des Tages mehr Leistung benötigen, können Sie [Azure Automation](http://msdn.microsoft.com/library/azure/dn643629.aspx) zum Vergrößern/Verkleinern der Leistungsstufe Ihrer Datenbank auf eine höhere/niedrigere Stufe als geplanten Vorgang verwenden.

	Führen Sie z. B. ein Upgrade der Datenbank auf eine höhere Leistungsstufe für die Dauer eines wöchentlichen Batch-/Wartungsauftrags aus, und verkleinern Sie die Leistungsstufe nach dem Abschluss des Auftrags. Diese Art der Planung ist auch hilfreich bei großen ressourcenintensiven Vorgängen, wie z . B. das Laden von Daten, erneute Indexerstellung usw. Beachten Sie, dass das Abrechnungsmodell für Azure SQL-Datenbank auf stündlicher Verwendung einer Dienstebene/Leistungsstufe basiert. Dank dieser Flexibilität können Sie vorgesehene oder geplante Upgrades kosteneffizienter ausführen.



## Zusammenfassung
Der Azure SQL-Datenbankdienst stellt Telemetriedaten und Tools zum Auswerten der Arbeitsauslastungen Ihrer Web-/Business-Datenbank sowie zum Ermitteln der am besten geeigneten Dienstebene für das Upgrade zur Verfügung. Der Upgradevorgang ist recht einfach und kann ausgeführt werden, ohne dass die Datenbank offline geschaltet werden muss. Es treten keine Datenverluste auf. Datenbanken, für die ein Upgrade ausgeführt wurde, profitieren von der vorhersagbaren Leistung und zusätzlichen Funktionen, die von den neuen Dienstebenen bereitgestellt werden.




<!--Image references-->
[2]: ./media/sql-database-upgrade-new-service-tiers/portal-dtus.JPG
[3]: ./media/sql-database-upgrade-new-service-tiers/web-business-noisy-neighbor.png
[4]: ./media/sql-database-upgrade-new-service-tiers/resource_consumption.png

 

<!----HONumber=September15_HO1-->