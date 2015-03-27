<properties 
title="Splitting and Merging with Elastic Scale" 
pageTitle="Aufteilen und Zusammenführen mit Elastic Scale" 
description="Erklärt, wie Sie Shards manipulieren und Daten über einen selbst gehosteten Dienst mithilfe von Elastic Scale-APIs verschieben." 
metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" 
services="sql-database" documentationCenter="" 
manager="jhubbard" 
authors="torsteng"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="torsteng" />

# Aufteilen und Zusammenführen mit Elastic Scale

In auf Azure SQL-Datenbank basierenden Anwendungen können Probleme auftreten, wenn die Daten oder die Verarbeitung nicht mehr einer einzelnen Skalierungseinheit in Azure SQL-Datenbank entsprechen. Beispiele hierfür sind Anwendungen, die stark anwachsen oder in denen eine bestimmte Gruppe von Instanzen über die Grenzen einer einzelnen Azure SQL-Datenbank hinaus anwächst. Der Elastic Scale **Split/Merge Service** erleichtert die Bewältigung dieses Problems ungemein. 

In dieser Diskussion des Aufteilungs-/Zusammenführungsdiensts wird das Hoch- und Herunterskalieren durch die Änderung der Anzahl von Azure DB-Datenbanken und das Anpassen der Verteilung von **Shardlets** unter den Datenbanken gehandhabt. (Begriffsdefinitionen finden Sie im [Elastic Scale-Glossar](./sql-database-elastic-scale-glossary.md)zur Verfügung steht). 

Bei den aktuell verfügbaren Azure SQL-Datenbankeditionen kann die Kapazität auch durch Hoch- oder Herunterskalieren der Kapazität einer einzelnen Azure SQL-DB-Datenbank reguliert werden. Auf das Hoch-/Herunterskalieren der elastischen Kapazitätsverwaltung wird vom Aufteilungs-/Zusammenführungsdienst nicht eingegangen – siehe hierzu Shard-Elastizität [Elastic Scale Shard-Elastizität](./sql-database-elastic-scale-elasticity.md)).
 
## Neuerungen bei Split/Merge

Die neueste Version von Split/Merge bietet die folgenden Verbesserungen:
* Es wird jetzt die Liste der Shard Maps unterstützt.
* Bereichsgrenzen in Anforderungen können einfacher mit Bereichen verglichen werden, die in der Shard Map gespeichert werden.
* Es werden jetzt mehrere Workerrolleninstanzen unterstützt, um die Verfügbarkeit zu verbessern.
* Im Rahmen Ihres Split/Merge-Vorgangs gespeicherte Anmeldeinformationen werden jetzt im Ruhezustand verschlüsselt.

## Aktualisieren

Gehen Sie folgendermaßen vor, um ein Upgrade auf die neueste Version von Split/Merge durchzuführen:

1. Laden Sie die neueste Version des Split/Merge-Pakets von NuGet gemäß der Beschreibung im Abschnitt "Herunterladen der Split/Merge-Pakete" des Lernprogramms zu den ersten Schritten mit Split/Merge herunter.
2. Ändern Sie Ihre Cloud-Dienstkonfigurationsdatei für Ihre Split/Merge-Bereitstellung, um die neuen Konfigurationsparameter widerzuspiegeln. Ein neuer erforderliche Parameter stellt die Informationen zum Zertifikat dar, das für die Verschlüsselung verwendet wird. Dies kann einfach über den Vergleich der im Download enthaltenen neuen Konfigurationsvorlagendatei mit der vorhandenen Konfiguration erreicht werden. Stellen Sie sicher, dass Sie die Einstellungen für "DataEncryptionPrimaryCertificateThumbprint" und "DataEncryptionPrimary" für die Web- und die Workerrolle hinzufügen.
3. Bevor Sie das Update in Azure bereitstellen, stellen Sie sicher, dass alle aktuell ausgeführten Split/Merge-Vorgänge abgeschlossen wurden. Sie können dazu problemlos die Tabellen "RequestStatus" und "PendingWorkflows" in der Split/Merge-Metadatendatenbank für laufende Anforderungen abfragen.
4. Aktualisieren Sie Ihre vorhandene Cloud-Dienstbereitstellung für Split/Merge im Azure-Abonnement mit dem neuen Paket und der aktualisierten Dienstkonfigurationsdatei.

Für das Upgrade von Split/Merge müssen Sie keine neue Metadatendatenbank bereitstellen. Die neue Version aktualisiert die vorhandene Metadatendatenbank automatisch auf die neue Version. 

## Szenarien für Aufteilen/Zusammenführen (Split/Merge) 
Anwendungen müssen die Flexibilität über die Grenzen einer einzelnen Azure SQL DB-Datenbank hinaus ausdehnen, wie in den folgenden Szenarien veranschaulicht wird: 

* **Kapazität erweitern - Bereiche aufteilen**: Die Möglichkeit, die aggregierte Kapazität auf der Datenebene zu erweitern, ist eine Antwort auf steigende Kapazitätsanforderungen. In diesem Szenario stellt die Anwendung die zusätzliche Kapazität bereit, indem die Daten partitioniert und über eine zunehmend größere Anzahl von Datenbanken verteilt werden, bis die Kapazitätsanforderungen erfüllt sind. Die Funktion 'split' des Aufteilungs-/Zusammenführungsdiensts von Elastic Scale geht auf dieses Szenario ein. 

* **Kapazität verkleinern - Bereiche zusammenführen**: Die Kapazität schwankt aufgrund saisonaler Schwankungen des Geschäftsbetriebs. Dieses Szenario unterstreicht, dass eine Reduzierung auf eine geringere Anzahl von Skalierungseinheiten möglich sein muss, wenn das Geschäft nachlässt. Die Funktion 'merge' des Aufteilungs-/Zusammenführungsdiensts von Elastic Scale geht auf dieses Szenario ein. 

* **Hotspots verwalten - Shardlets verschieben**: Wenn mehrere Mandanten in einer Datenbank verwaltet werden, kann die Zuweisung von Shardlets zu Shards bei einigen Shards zu Kapazitätsengpässen führen. Dann müssen Shards neu zugewiesen oder stark in Anspruch genommene Shardlets in neue oder weniger genutzte Shards verschoben werden. 

Nachfolgend werden wir alle Verarbeitungsvorgänge im Dienst im Zusammenhang mit diesen Fähigkeiten als **split/merge/move**-Anforderungen bezeichnen. 


Abbildung 1: Konzeptueller Überblick über den Aufteilungs-/Zusammenführungsdienst


![Overview][1] 


**Hinweis**:  Nicht alle **Grow Capacity**-Szenarien erfordern den Aufteilungs-/Zusammenführungsdienst. Wenn Sie z. B. in regelmäßigen Abständen neue Shards in Ihrer Umgebung zum Speichern neuer Daten mit zunehmenden Sharding-Schlüsselwerten erstellen, können Sie die Client-APIs zur Shard-Zuordnungsverwaltung verwenden, um Datenbereiche zu den neu erstellten Shards weiterzuleiten. Der Aufteilungs-/Zusammenführungsdienst ist nur erforderlich, wenn auch vorhandene Daten verschoben werden müssen.

## Konzepte und wichtige Features

**Vom Kunden gehostete Dienste**: Der Aufteilungs-/Zusammenführungsdienst wird als vom Kunden gehosteter Dienst bereitgestellt. Sie müssen den Dienst in Ihrem Microsoft Azure-Abonnement bereitstellen und hosten. Das Paket, das Sie von NuGet herunterladen, enthält eine Vorlage, die mit den Informationen für Ihre Bereitstellung ausgefüllt werden muss. Im [Split/Merge-Lernprogramm](./sql-database-elastic-scale-configure-deploy-split-and-merge.md) zur Verfügung. Da der Dienst im Azure-Abonnement ausgeführt wird, können Sie die meisten Sicherheitsaspekte des Diensts steuern und konfigurieren. Die Standardvorlage enthält die Optionen zum Konfigurieren von SSL, der zertifikatbasierten Clientauthentifizierung, der Verschlüsselung für gespeicherte Anmeldeinformationen, des DoS-Schutzes und von IP-Einschränkungen. Im folgenden Dokument [Sicherheitsüberlegungen zu Elastic Scale](./sql-database-elastic-scale-configure-security.md).

Der standardmäßig bereitgestellte Dienst wird mit einer Workerrolle und einer Webrolle ausgeführt. Für beide wird die VM-Größe A1 in Azure Cloud Services verwendet. Sie können diese Einstellungen zwar nicht bei der Bereitstellung des Pakets ändern, nach einer erfolgreichen Bereitstellung können die Einstellungen jedoch im ausgeführten Cloud-Dienst (über das Azure-Portal) geändert werden. Beachten Sie, dass die Workerrolle aus technischen Gründen nicht für mehr als eine einzelne Instanz konfiguriert werden darf. 

**Integration in Shard-Zuordnung**: Der Aufteilungs-/Zusammenführungsdienst interagiert mit der Shard-Zuordnung der Anwendung. Wenn der Aufteilungs-/Zusammenführungsdienst zum Aufteilen oder Zusammenführen von Bereichen oder zum Verschieben von Shardlets zwischen Shards verwendet wird, aktualisiert der Dienst die Shard-Zuordnung automatisch. Zu diesem Zweck stellt der Dienst eine Verbindung mit der Shard-Map-Manager-Datenbank der Anwendung her und verwaltet die Bereiche und Zuordnungen, während Split/Merge/Move-Anforderungen ausgeführt werden. Dadurch wird sichergestellt, dass die Shard-Zuordnung während der Ausführung von Aufteilungs-/Zusammenführungsvorgängen immer eine aktuelle Ansicht zeigt. Aufteilungs-, Zusammenführungs- und Verschiebungsvorgänge werden implementiert, indem einen Batch von Shardlets von der Quell-Shard in die Ziel-Shard verschoben werden. Während des Verschiebens von Shardlets werden die Shardlets, die im aktuellen Batch enthalten sind, in der Shard-Zuordnung als offline markiert und stehen für datenabhängige Routingverbindungen mit der **OpenConnectionForKey**-API nicht zur Verfügung. 

**Konsistente Shardlet-Verbindungen**: Wenn die Datenverschiebung für einen neuen Batch von Shardlets gestartet wird, werden alle von der Shard-Zuordnung gegebenen Routingverbindungen mit der Shard, in der das Shardlet gespeichert ist, getrennt, und nachfolgende Verbindungen von den Shard-Zuordnungs-APIs mit diesen Shardlets werden während der Ausführung der Datenverschiebung blockiert, um Inkonsistenzen zu vermeiden. Verbindungen mit anderen Shardlets in der gleichen Shard werden auch beendet, werden jedoch bei einem erneuten Versuch sofort hergestellt. Nachdem der Batch verschoben worden ist, werden die Shardlets für die Ziel-Shard wieder als online markiert, und die Quelldaten werden aus der Quell-Shard entfernt. Der Dienst durchläuft diese Schritte für jeden Batch, bis alle Shardlets verschoben worden sind. Dies führt dazu, dass im Lauf eines Split/Merge/Move-Vorgangs mehrmals Verbindungen getrennt werden.   

**Verwalten der Shardlet-Verfügbarkeit**: Durch die Beschränkung der oben beschriebenen Trennung von Verbindungen auf den aktuellen Batch von Shardlets, wird der Bereich der Nichtverfügbarkeit zu einem gegebenen Zeitpunkt jeweils auf eine Gruppe von Shardlets beschränkt. Dieses Vorgehen wird einem Ansatz vorgezogen, bei dem die komplette Shard während des gesamten Split/Merge-Vorgangs für alle zugehörigen Shardlets offline bleibt. Die Batchgröße, die als Anzahl unterschiedlicher Shardlets, die gleichzeitig verschoben werden sollen, definiert ist, ist ein Konfigurationsparameter. Sie kann für jeden Split/Merge-Vorgang von der Verfügbarkeit und den Leistungsanforderungen der Anwendung abhängig definiert werden. Beachten Sie, dass der Bereich, der in der Shard-Zuordnung blockiert wird, unter Umständen größer als die angegebene Batchgröße ist. Dies ist so, weil der Dienst die Bereichsgröße so wählt, dass die tatsächliche Anzahl der Sharding-Schlüsselwerte in den Daten ungefähr der Batchgröße entspricht. Dies ist besonders bei spärlich belegten Sharding-Schlüsseln zu beachten. 

**Metadatenspeicherung**: Der Aufteilungs-/Zusammenführungsdienst verwendet eine Datenbank, um seinen Status zu verwalten und um Protokolle während der Anforderungsverarbeitung zu speichern. Der Benutzer erstellt diese Datenbank in seinem Abonnement und stellt die zugehörige Verbindungszeichenfolge in der Konfigurationsdatei für die Dienstbereitstellung bereit. Administratoren der Organisation des Benutzers können auch eine Verbindung mit dieser Datenbank herstellen, um die Bearbeitung der Anforderung zu überprüfen und detaillierte Informationen zu möglichen Fehlern zu untersuchen.

**Shardinginformationen**: Der Aufteilungs-/Zusammenführungsdienst unterscheidet zwischen (1) partitionierten Tabellen, (2) Verweistabellen und (3) normalen Tabellen. Die Semantik eines Split/Merge/Move-Vorgangs hängt vom Typ der verwendeten Tabelle ab und wird wie folgt definiert: 

* **Shardtabellen**: Mit Split/Merge/Move-Vorgängen werden Shardlets von der Quell- zur Ziel-Shard verschoben. Nach dem erfolgreichen Abschluss der gesamten Anforderung sind diese Shardlets nicht mehr in der Quelle vorhanden. Beachten Sie, dass die Zieltabellen in der Ziel-Shard vorhanden sein müssen und vor der Verarbeitung des Vorgangs keine Daten im Zielbereich enthalten dürfen. 

* **Verweistabellen**: Bei Verweistabellen werden die Daten mit Aufteilungs-, Zusammenführungs- und Verschiebungsvorgängen aus der Quell- in die Ziel-Shard kopiert. Beachten Sie jedoch, dass die Ziel-Shard für eine bestimmte Tabelle nicht geändert wird, wenn in der Ziel-Shard in dieser Tabelle bereits Zeilen vorhanden sind. Die Tabelle muss leer sein, damit Kopiervorgänge aus der Verweistabelle verarbeitet werden.

* **Weitere Tabellen**: Andere Tabellen können für die Quelle oder das Ziel eines Split/Merge-Vorgangs vorhanden sein. Der Aufteilungs-/Zusammenführungsdienst ignoriert diese Tabellen bei Datenverschiebungen oder Kopiervorgängen. Beachten Sie jedoch, dass sie diese Vorgänge bei Einschränkungen beeinträchtigen können.

Die Informationen zu Verweistabellen und partitionierten Tabellen werden von den **SchemaInfo**-APIs für die Shard-Zuordnung bereitgestellt. Das folgende Beispiel veranschaulicht die Verwendung dieser APIs anhand eines bestimmten Shard-Zuordnungs-Manager-Objekts smm: 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

Die Tabellen 'region' und 'nation' sind als Verweistabellen definiert und werden mit Split/Merge/Move-Vorgängen kopiert. 'customer' und 'orders' sind dagegen als partitionierte Tabellen definiert. C_CUSTKEY und O_CUSTKEY dienen als Sharding-Schlüssel. 

**Referenzielle Integrität**: Der Aufteilungs-/Zusammenführungsdienst analysiert Abhängigkeiten zwischen Tabellen und verwendet Fremdschlüssel und Primärschlüsselbeziehungen, um die Vorgänge für das Verschieben von Verweistabellen und Shardlets bereitzustellen. Im Allgemeinen werden zuerst die Verweistabellen in der Reihenfolge ihrer Abhängigkeit kopiert, und dann werden die Shardlets in der Reihenfolge ihrer Abhängigkeiten innerhalb der einzelnen Batches kopiert. Dies ist erforderlich, damit Fremdschlüssel-Primärschlüssel-Einschränkungen für die Ziel-Shard berücksichtigt werden, wenn neue Daten eingehen. 

**Konsistenz und Fertigstellung der Shard-Zuordnung**: Bei Fehlern nimmt der Aufteilungs-/Zusammenführungsdienst die Vorgänge nach einem Ausfall wieder auf und versucht, die aktuell ausstehenden Anforderungen abzuschließen. Allerdings können nicht behebbare Fehler auftreten, z. B. wenn die Ziel-Shard verloren geht oder irreparabel beschädigt wird. Unter diesen Umständen können sich einige Shardlets, die verschoben werden sollten, weiterhin in der Quell-Shard befinden. Der Dienst stellt sicher, dass Shardlet-Zuordnungen erst aktualisiert werden, nachdem die erforderlichen Daten erfolgreich in das Ziel kopiert wurden. Shardlets werden erst dann in der Quelle gelöscht, nachdem alle Daten in das Ziel kopiert und die zugehörigen Zuordnungen erfolgreich aktualisiert wurden. Der Löschvorgang erfolgt im Hintergrund, während der Bereich bereits in der Ziel-Shard online ist. Der Aufteilungs-/Zusammenführungsdienst stellt immer die Richtigkeit der in der Shard-Zuordnung gespeicherten Zuordnungen sicher.

## Beziehen der Binärdateien des Diensts

Die Binärdateien für den Aufteilungs-/Zusammenführungsdienst (Split/Merge) werden von [Nuget](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/) bereitgestellt. Informieren Sie sich im schrittweisen [Split/Merge-Lernprogramm](./sql-database-elastic-scale-configure-deploy-split-and-merge.md) über das Herunterladen von Binärdateien.

## Die Split/Merge-Benutzeroberfläche

Neben der Workerrolle enthält das Split/Merge-Dienstpaket auch eine Webrolle, die zum interaktiven Senden von Anforderungen von Split/Merge-Anforderungen verwendet werden kann. Die Benutzeroberfläche enthält die folgenden Hauptkomponenten:

-    Operation Type: Dies ist ein Optionsfeld, das die Art des Vorgangs steuert, der vom Dienst für diese Anforderung ausgeführt wird. Sie können die Aufteilungs-, Zusammenführungs- und Verschiebungsszenarien wählen, die unter Konzepte und Schlüsselfunktionen beschrieben werden. Darüber hinaus können Sie einen zuvor gesendeten Vorgang auch abbrechen.  Sie können Anforderungen für Shard-Zuordnungen teilen, zusammenführen und verschieben. Die Liste der Shard-Zuordnungen unterstützt nur Verschiebevorgänge.

-    Shard-Zuordnung: Der nächste Abschnitt zu den Anforderungsparametern enthält Informationen zur Shard-Zuordnung und der Datenbank, in der die Shard-Zuordnung gehostet wird. Sie müssen den Namen des Azure SQL-Datenbankservers und der Datenbank, in der die Shard-Zuordnung gehostet wird, die Anmeldeinformationen zum Herstellen einer Verbindung mit der Shard-Zuordnungsdatenbank und schließlich den Namen der Shard-Zuordnung angeben. Der Vorgang akzeptiert momentan nur einen einzigen Satz von Anmeldeinformationen. Diese Anmeldeinformationen müssen über ausreichende Berechtigungen verfügen, um die Shard-Zuordnung und die Benutzerdaten in den Shards ändern zu können.

-    Source Range (Split/Merge): Ein Aufteilungs- und Zusammenführungsvorgang verarbeitet einen Bereich mithilfe seines niedrigen und hohen Schlüssels. Um einen Vorgang mit einem unbegrenzt hohen Schlüsselwert anzugeben, aktivieren Sie das Kontrollkästchen "High key is max", und lassen Sie das Feld für den hohen Schlüssel leer. Die von Ihnen angegebenen Bereichsschlüsselwerte müssen nicht genau mit einer Zuordnung und ihren Grenzen in Ihrer Shard-Zuordnung übereinstimmen. Wenn Sie keine Bereichsgrenzen angeben, leitet der Dienst den nächsten Bereich automatisch für Sie ab. Sie können mit dem PowerShell-Skript GetMappings.ps1 die aktuellen Zuordnungen aus einer gegebenen Shard-Zuordnungen abrufen.

-    Split Key and Behavior (Split): Bei Aufteilungsvorgängen müssen Sie auch definieren, an welcher Stelle der Quellbereich geteilt werden soll. Hierzu geben Sie den Sharding-Schlüssel an, an dem die Aufteilung erfolgen soll. Mit dem nebenstehenden Optionsfeld definieren sie, ob der untere Teil des Bereichs (exklusive des Aufteilungsschlüssels) oder ob der obere Teil (inklusive Aufteilungsschlüssel) verschoben werden soll.

-    Source Shardlet (Move): Verschiebevorgänge unterscheiden sich von Aufteilungs- oder Zusammenführungsvorgängen, da hier kein Bereich zum Beschreiben der Quelle angegeben werden muss. Die Quelle für das Verschieben wird einfach durch den Sharding-Schlüsselwert identifiziert, der verschoben werden soll.

-    Target Shard (Split): Nachdem Sie die Informationen zur Quelle des Aufteilungsvorgangs angegeben haben, müssen Sie definieren, wohin die Daten kopiert werden sollen, indem Sie den Azure SQL-Datenbankserver und den Datenbanknamen des Ziels angeben.

-    Target Range (Merge): Zusammenführungsvorgänge verschieben Shardlets in eine vorhandene Shard. Sie identifizieren die vorhandene Shard durch Angabe der Bereichsgrenzen des vorhandenen Bereichs, mit dem zusammengeführt werden soll.

-    Batch Size: Die Batchgröße steuert die Anzahl der Shardlets, die während der Datenverschiebung gleichzeitig offline geschaltet werden. Dies ist ein ganzzahliger Wert, wobei Sie kleinere Werte angeben können, wenn Sie lange Ausfallzeiten für Shardlets vermeiden möchten. Bei Angabe größerer Werte vergrößert sich der Zeitraum, über den ein bestimmtes Shardlet offline ist, die Leistung kann dadurch jedoch verbessert werden.

-    Operation Id (Cancel): Wenn Sie eine laufende Operation nicht mehr benötigt wird, können Sie den Vorgang abbrechen, indem Sie in diesem Feld die Vorgangs-ID angeben. Sie können die Vorgangs-ID der Anforderungsstatustabelle (siehe Abschnitt 8.1) oder der Ausgabe in dem Webbrowser entnehmen, mit dem Sie die Anforderung übermittelt haben.


## Anforderungen und Einschränkungen 

Die aktuelle Implementierung des Aufteilungs-/Zusammenführungsdiensts unterliegt den folgenden Anforderungen und Einschränkungen: 

* Derzeit müssen die Shards vorhanden und in der Shard-Zuordnung registriert sein, damit ein Split/Merge-Vorgang für diese Shards ausgeführt werden kann. 

* Der Aufteilungs-/Zusammenführungsdienst erstellt derzeit nicht automatisch Tabellen oder andere Datenbankobjekte. Dies bedeutet, dass das Schema für alle partitionierten Tabellen und Verweistabellen in der Ziel-Shard vorhanden sein muss, damit ein Split/Merge/Vorgang-Vorgang ausgeführt werden kann. Sharding-Tabellen müssen insbesondere in dem Bereich leer sein, in dem die neuen Shardlets durch einen Split/Merge/Move-Vorgang hinzugefügt werden sollen. Andernfalls schlägt die anfänglichen Konsistenzprüfung der Ziel-Shard für den Vorgang fehl. Beachten Sie außerdem, dass Verweisdaten nur kopiert werden, wenn die Verweistabelle leer ist, und dass die Konsistenz in Bezug auf andere gleichzeitige Schreibvorgänge in den Verweistabellen nicht garantiert wird. Es wird empfohlen, dass zum Zeitpunkt der laufenden Split/Merge-Vorgänge keine anderen Schreibvorgänge Änderungen an den Verweistabellen vornehmen.

* Der Dienst verwendet derzeit die Zeilenidentität, die durch einen eindeutigen Index oder Schlüssel mit dem Sharding-Schlüssel gegeben ist, zur Verbesserung der Leistung und Zuverlässigkeit für große Shardlets. Dadurch kann der Dienst Daten sogar in noch feineren Unterteilungen als den Sharding-Schlüsselwert verschieben. Dadurch lassen sich die maximale Größe des für Protokolle erforderlichen Speicherplatzes und die Anzahl von Sperren verringern, die während des Vorgangs erforderlich sind. Ziehen Sie das Erstellen eines eindeutigen Index oder eines Primärschlüssels unter Einbeziehung des Sharding-Schlüssels für eine bestimmte Tabelle in Betracht, wenn Sie diese Tabelle mit Split/Merge/Move-Anforderungen verwenden möchten. Um die Leistung zu verbessern, sollte der Sharding-Schlüssel die führende Spalte im Schlüssel bzw. Index bilden.

* Während der Verarbeitung der Anforderung können in Quell- und Ziel-Shard Shardlet-Daten vorhanden sein. Dies ist derzeit zum Schutz vor Ausfällen während der Shardlet-Verschiebung erforderlich. Wie oben erläutert, wird durch die Integration von Split/Merge in die Shard-Zuordnung von Elastic Scale sichergestellt, dass in Verbindungen, die mit der **OpenConnectionForKey**-Methode über die datenabhängigen Routing-APIs für die Shard-Zuordnung hergestellt werden, keine inkonsistenten Zwischenzustände auftreten. Wenn eine Verbindung mit der Quell- oder Ziel-Shard allerdings nicht unter Verwendung der **OpenConnectionForKey**-Methode hergestellt wird, können während der Ausführung von Split/Merge/Move-Anforderungen inkonsistente Zwischenzustände auftreten. Abhängig vom Zeitpunkt oder der Shard, die der Verbindung zugrunde liegt, können in diesen Verbindungen unvollständige oder doppelte Ergebnisse angezeigt werden. Diese Einschränkung betrifft derzeit auch die Verbindungen, die durch Elastic Scale Multi-Shard-Abfragen hergestellt werden.

* Die Metadatendatenbank für den Aufteilungs-/Zusammenführungsdienst darf nicht von verschiedenen Rollen gemeinsam verwendet werden. Beispielsweise muss eine Rolle des Aufteilungs-/Zusammenführungsdienst,die in der Staging-Umgebung ausgeführt wird, auf eine andere Metadatendatenbank als die Produktionsrolle verweisen.
 

## Abrechnung 

Da der Aufteilungs-/Zusammenführungsdienst als Cloud-Dienst in Ihrem Microsoft Azure-Abonnement ausgeführt wird, gelten die normalen Gebühren für Cloud-Dienste für Ihre Instanz des Aufteilungs-/Zusammenführungsdiensts (Split/Merge). Sofern Sie nicht häufig Split/Merge/Move-Vorgänge ausführen, empfehlen wir, den Split/Merge-Cloud-Dienst zu löschen. Dadurch können Sie Kosten für laufende oder bereitgestellte Cloud-Dienstinstanzen sparen. Sie können ihn erneut bereitstellen und Ihre sofort ausführbare Konfiguration starten, wenn Sie Split/Merge-Operationen ausführen müssen. 
  
## Überwachung 
### Statustabellen 

Der Aufteilungs-/Zusammenführungsdienst stellt die **RequestStatus**-Tabelle in der Metadaten-Store-Datenbank zum Überwachen abgeschlossener und laufender Anforderungen bereit. Die Tabelle enthält eine Zeile für jede Teilung/Merge-Anforderung, die an diese Instanz des Aufteilungs-/Zusammenführungsdiensts übermittelt wurde. Sie stellt für jede Anforderung die folgende Informationen bereit:

* **Timestamp**: Uhrzeit und Datum, als die Anforderung gestartet wurde.

* **OperationId**: Ein GUID-Wert, der die Anforderung eindeutig identifiziert. Diese Anforderung kann auch verwendet werden, um den Vorgang während der Ausführung abzubrechen.

* **Status**: Der aktuelle Status der Anforderung. Bei laufenden Anforderungen wird auch die aktuelle Phase der Anforderung angegeben.

* **CancelRequest**: Ein Flag, das angibt, ob die Anforderung abgebrochen wurde.

* **Progress**: Eine Prozentwert, der angibt, zu welchem Anteil der Vorgang schätzungsweise fertiggestellt ist. Der Wert 50 gibt an, dass der Vorgang etwa zu 50 % abgeschlossen ist.

* **Details**: Ein XML-Wert, der einen ausführlicheren Statusbericht bereitstellt. Der Statusbericht wird regelmäßig aktualisiert, wenn Zeilen aus der Quelle in das Ziel kopiert werden. Bei Fehlern oder Ausnahmen enthält diese Spalte auch detaillierte Informationen zu diesem Fehler.


### Azure-Diagnose 

Die Dienstvorlage für Split/Merge ist so vorkonfiguriert, dass WAD-Speicher (Microsoft Azure-Diagnose) für die zusätzliche ausführliche Protokollierung und als Diagnosespeicher verwendet wird. Sie können die auf WAD bezogene Konfiguration, z. B. das Speicherkonto und die Anmeldeinformationen, über Ihre Dienstkonfigurationsdatei für Split/Merge steuern. Die WAD-Konfiguration für den Dienst entspricht den Anleitungen in [Cloud-Dienst-Grundlagen](http://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Sie enthält die Definitionen zum Protokollieren von Leistungsindikatoren und die Definitionen für IIS-Protokolle, Windows-Ereignisprotokolle und Split/Merge-Anwendungsereignisprotokolle. Sie können in Visual Studio im Server-Explorer im Azure-Teil der Explorer-Struktur einfach auf diese Protokolle zugreifen:

![Azure Diagnostics][2]   

Die in der obigen Abbildung hervorgehobene Tabelle WADLogsTable enthält die detaillierten Ereignisse aus dem Anwendungsprotokoll des Aufteilungs-/Zusammenführungsdiensts. Beachten Sie jedoch, dass die als Teil des heruntergeladenen Pakets bereitgestellte Standardkonfiguration auf die Bereitstellung in einer Produktionsumgebung ausgerichtet ist. Folglich ist das Intervall, in dem Protokolle und Leistungsindikatoren von den Dienstinstanzen abgerufen werden, groß (5 Minuten). Für Test- und Entwicklungsumgebungen können Sie das Intervall verkleinern, indem Sie die Diagnoseeinstellungen der Web- oder Workerrolle an Ihre Anforderungen anpassen. Sie können dazu mit der rechten Maustaste im Visual Studio Server-Explorer (siehe oben) auf die Rolle klicken und dann im Dialogfeld für die Konfigurationseinstellungen für die Diagnose den Wert für "Transfer Period" (Übertragungsintervall) anpassen:

![Configuration][3]


Über die verschiedenen Registerkarten im Dialogfeld werden die verschiedenen Protokolltypen gesteuert. Jede Registerkarte verfügt über eine eigene Einstellung für das Übertragungsintervall. 
Wenn Probleme mit der Bereitstellung des Aufteilungs-/Zusammenführungsdiensts auftreten, benötigen die Microsoft-Teams in der Regel Informationen zu den Protokollen und Leistungsindikatoren. Sie können mit Tools wie [Azure-Speicher-Explorer](http://azurestorageexplorer.codeplex.com/) WAD-Protokolle mühelos in CSV-Dateien exportieren. 

## Leistung

Die Leistung der Aufteilungs-, Zusammenführungs- und Verschiebungsvorgänge hängt von einer Vielzahl von Faktoren ab. Im Allgemeinen ist von den höheren, leistungsstärkeren Dienstebenen in Azure SQL-Datenbank eine bessere Leistung zu erwarten. Die Massenkopier- und -löschvorgänge, die intern vom Aufteilungs-/Zusammenführungsdienst verwendet werden, profitieren von höheren E/A-, CPU- und Speicherressourcenzuteilungen für die höheren Dienstebenen. Aus diesem Grund kann es reizvoll sein, die Dienstebene nur für einen angegebenen Satz von Datenbanken für einen klar definierten begrenzten Zeitraum zu erhöhen, damit geplante Split/Merge-Vorgänge für in diesen Datenbanken gehostete Bereiche schnell abgeschlossen werden.

Beachten Sie, dass der Dienst im Rahmen der normalen Vorgänge auch Überprüfungsabfragen ausführt. Mit diesen Überprüfungsabfragen wird unter anderem geprüft, ob Daten unerwartet im Zielbereich vorhanden sind, und sichergestellt, dass alle Split/Merge/Move-Vorgänge in einem konsistenten Zustand gestartet werden. Für diese Abfragen werden Sharding-Schlüsselbereiche verwendet, die durch den Umfang des Split/Merge/Move-Vorgangs und die Batchgröße, die als Teil der Anforderungsdefinition angegeben wird, definiert werden. Diese Abfragen funktionieren am besten, wenn ein Index vorhanden ist, in dem der Shardingschlüssel die führende Spalte bildet. 

Darüber hinaus ermöglicht die Eindeutigkeit des Shardingschlüssels als führender Spalte dem Dienst den Einsatz eines optimierten Ansatzes, der den Ressourcenverbrauch im Hinblick auf Speicherplatz und Arbeitsspeicher beschränkt. Diese Eindeutigkeitseigenschaft ist zum Verschieben großer Datenmengen (in der Regel über 1 GB) erforderlich. 

## Best Practices und Problembehandlung 
-    Definieren Sie einen Testmandanten, und probieren Sie Ihre wichtigsten Split/Merge/Move-Vorgänge mit dem Testmandanten über mehrere Shards hinweg aus. Auf diese Weise können Sie leichter sicherzustellen, dass alle Metadaten in der Shard-Zuordnung ordnungsgemäß definiert sind und dass die Vorgänge keine Einschränkungen oder Fremdschlüssel verletzen.
-    Verwenden Sie für den Testmandanten eine Datengröße, welche die maximale Größe des größten Mandanten überschreitet, um sicherzustellen, dass keine Probleme im Zusammenhang mit der Datengröße auftreten. Damit können Sie den Zeitraum einschätzen, der zum Verschieben eines einzelnen Mandanten maximal benötigt wird. 
-    Der Aufteilungs-/Zusammenführungsdienst erfordert, dass die Daten aus der Quell-Shard entfernt werden, nachdem die Daten erfolgreich an das Ziel kopiert wurden. Prüfen Sie Ihr Schema genau, um sicherzustellen, dass das Schema die Löschungen zulässt. Beispielsweise können delete-Trigger verhindern, dass der Dienst Daten aus der Quelle löscht, und möglicherweise zum Scheitern der Vorgänge führen.
-    Stellen Sie sicher, dass der Sharding-Schlüssel die führende Spalte im Primärschlüssel oder der eindeutigen Indexdefinition bildet. Damit wird die beste Leistung für die Split/Merge-Überprüfungsabfragen und die tatsächliche Verschiebe- und Löschvorgänge der Daten sichergestellt, die immer auf Sharding-Schlüsselbereichen basieren.
-    Aus Leistungs- und Kostengründen sollten Sie den Aufteilungs-/Zusammenführungsdienst der Region und dem Rechenzentrum zuordnen, in denen Ihre Datenbanken gespeichert sind. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

## Referenzen 

* [Split/Merge-Lernprogramm](./sql-database-elastic-scale-configure-deploy-split-and-merge.md)

* [Sicherheitsüberlegungen zu Elastic Scale](./sql-database-elastic-scale-configure-security.md)  


<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-split-and-merge/diagnostics-config.png

<!--HONumber=47-->
