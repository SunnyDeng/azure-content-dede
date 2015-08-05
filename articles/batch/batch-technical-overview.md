<properties
	pageTitle="Azure Batch – Technische Übersicht"
	description="Erfahren Sie mehr über die Konzepte, Workflows und Szenarien des Azure Batch-Diensts."
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/13/2015"
	ms.author="danlep"/>


#Azure Batch – Technische Übersicht
Azure Batch unterstützt Sie beim effizienten Ausführen von größeren parallelen und leistungsstarken Anwendungen (High Performance Computing, HPC) in der Cloud. Es handelt sich dabei um einen Plattformdienst, über den die Auftragsplanung und automatische Skalierung einer verwalteten Sammlung virtueller Computer (VMs) zum Ausführen der Aufträge bereitgestellt werden. Mit dem Batch-Dienst können Sie Batchworkloads so konfigurieren, dass sie in Azure bei Bedarf oder nach einem Zeitplan ausgeführt werden. Das komplexe Konfigurieren und Verwalten von HPC-Clustern, VMs oder Auftragsplanern entfällt.

>[AZURE.NOTE]Zum Verwenden von Batch benötigen Sie ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Create a Windows Azure account (in englischer Sprache)](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/).


## Anwendungsfälle

Batch nutzt die Flexibilität und die Skalierung der Cloud zur Unterstützung der *Batchverarbeitung* oder von *Batch Computing* – Es werden große Mengen an ähnlichen Aufgaben ausgeführt, um das gewünschte Ergebnis zu erzielen. Ein Befehlszeilenprogramm oder Skript erhält einen Satz von Dateien als Eingabe, verarbeitet die Daten in einer Reihe von Aufgaben und erstellt eine Gruppe von Ausgabedateien. Die Ausgabedateien können das endgültige Ergebnis oder ein Zwischenschritt in einem größeren Workflow sein.

Batch Computing ist ein häufig genutztes Muster für Unternehmen, die Verarbeiten, Transformieren und Analysieren großer Datenmengen durchführen, entweder nach einem Zeitplan oder bei Bedarf. Es umfasst Verarbeitung am Ende der Schleife, z. B. bei täglichen Risikoberichten einer Bank oder bei Lohnbuchhaltung, die planmäßig ausgeführt werden muss. Darüber hinaus umfasst es umfangreiche Geschäfts-, Wissenschafts- und Engineering-Anwendungen, die in der Regel Tools und Ressourcen von einem Computecluster oder -raster benötigen. Anwendungen umfassen herkömmliche HPC-Anwendungen wie fluiddynamische Simulationen sowie spezielle Workloads in Bereichen, die von der Erstellung digitaler Inhalte über Finanzdienstleistungen bis hin zur Biowissenschaftensforschung reichen.

Batch funktioniert sehr gut bei intrinsisch parallelen (manchmal als "hochgradig parallel" bezeichnete) Anwendungen oder Workloads, mit denen parallele Aufgaben auf mehreren Computern ausgeführt werden, wie z. B. die Compute-VMs, die vom Batch-Dienst verwaltet werden. Siehe Abbildung 1.

![Parallele Aufgaben][parallel]

**Abbildung 1: Parallele Aufgaben, die auf mehreren Computern ausgeführt werden**

Beispiele:

* Risikomodellierung im Finanzbereich
* Bildrendering und-verarbeitung
* Mediencodierung und-transcodierung
* Analyse genetischer Sequenzen
* Testen von Software

Batch können Sie auch nutzen, um parallele Berechnungen mit einem Reduzierungsschritt am Ende und andere kompliziertere, parallele Workloads auszuführen.

>[AZURE.NOTE]Derzeit können nur Windows-Server-Workloads mit Batch ausgeführt werden. Zudem unterstützt Batch derzeit keine Message-Passing-Interface (MPI)-Anwendungen.

## Entwicklungsszenarien

Batch unterstützt verschiedene Entwicklungsszenarien, mit denen Sie große, parallele Workloads mit dem Batch-Dienst konfigurieren und ausführen können. Diese Szenarien nutzen APIs zur Erstellung und Verwaltung von VM-Pools (Computeknoten) sowie zur Planung der Aufträge und Aufgaben, die auf ihnen ausgeführt werden. Unter [API-Grundlagen für Azure Batch](batch-api-basics.md) finden Sie weitere Informationen über die Batch-Konzepte.

Typische Batch-Entwicklungsszenarien finden Sie in den folgenden Abschnitten.

### Horizontales Skalieren eines parallelen Workloads

Verwenden Sie die Batch-API, um intrinsische parallele Aufgaben wie Bildrendering auf einem Pool von bis zu Tausenden Prozessorkernen horizontal zu skalieren. Anstatt einen Computecluster einrichten oder Code schreiben zu müssen, um Aufträge in die Warteschlange zu stellen und zu planen sowie die erforderlichen Ein- und Ausgabedaten zu verschieben, automatisieren Sie die Planung von großen Computeaufträgen und skalieren einen Pool von Compute-VMs nach oben oder unten, um sie auszuführen. Sie können Clientanwendungen oder Front-Ends schreiben, um Aufträge und Aufgaben nach Bedarf, nach einem Zeitplan oder als Teil eines größeren, von Tools wie [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) verwalteten, Workflows auszuführen.

Abbildung 2 zeigt einen vereinfachten Workflow zum Übermitteln einer Anwendung in einen Batch-Pool, in dem er zur Verarbeitung verteilt wird.

![Workflow für Arbeitsaufgaben][work_item_workflow]

**Abbildung 2: Horizontales Skalieren eines parallelen Workloads auf Batch**

1.	Hochladen von Eingabedateien (z. B. Quelldaten oder Bilder), die für einen Auftrag zu einem Azure-Speicherkonto erforderlich sind. Diese Dateien müssen im Speicherkonto sein, damit der Batch-Dienst darauf zugreifen kann. Der Batch-Dienst lädt Dateien in Computeknoten, wenn die Aufgaben ausgeführt werden.
2.	Laden Sie die abhängigen Binärdateien in das Speicherkonto hoch. Die Binärdateien enthalten das Programm, das durch die Aufgabe und die abhängigen Assemblys ausgeführt wird. Auf diese Dateien muss auch aus dem Speicher zugegriffen werden, und sie werden auf die Computeknoten geladen.
3.	Erstellen Sie einen Pool von Computeknoten, indem Sie Eigenschaften wie die Größe der virtuellen Computer und das ausgeführte Betriebssystem festlegen. Sie können auch bestimmen, wie die Anzahl der Knoten im Pool dem Workload entsprechend nach oben oder unten skaliert wird. Wenn eine Aufgabe ausgeführt wird, wird ihr ein Knoten aus diesem Pool zugewiesen.
4.	Definieren Sie einen Auftrag, der im Pool ausgeführt werden soll.
5.	Hinzufügen von Aufgaben zum Auftrag. Jede Aufgabe verwendet das Programm, das Sie hochgeladen haben, um Informationen aus einer hochgeladenen Datei zu verarbeiten.
6.	Führen Sie die Anwendung aus, und überwachen Sie die Ergebnisse der Ausgabe.


### Bereitstellen von rechenintensiven Apps für die Cloud

Sie können die Vorschau-Batch-Apps-API verwenden, um eine vorhandene Anwendung zu umschließen, sodass sie als Dienst für einen Pool von Computenodes ausgeführt wird, die im Hintergrund von Batch verwaltet werden. Bei der Anwendung kann es sich um eine Anwendung handeln, die heute auf Clientworkstations oder einem Compute Cluster ausgeführt wird. Sie können den Dienst weiterentwickeln, sodass Benutzer Spitzenauslastungen in die Cloud auslagern oder vollständig in der Cloud arbeiten können. Das Batch-Apps-Framework ist für die Verschiebung von Eingabe- und Ausgabedaten, das Aufteilen von Aufträgen in Aufgaben, die Auftrags- und Aufgabenverarbeitung sowie die Datenpersistenz verantwortlich.

>[AZURE.IMPORTANT]Azure wird die Batch-Apps-API nur in Vorschauform bieten. Sie sollten sie nur zur Entwicklung von Testprojekten oder Machbarkeitsstudien verwenden. Wichtige Batch-Apps-Funktionen werden in künftigen Versionen in die Batch-API integriert.

Abbildung 3 zeigt einen einfachen Arbeitsablauf zum Veröffentlichen einer Anwendung mit der Batch Apps-API und zur Genehmigung für Benutzer, Aufträge an die Anwendung zu übermitteln.

![Workflow zum Veröffentlichen von Anwendungen][app_pub_workflow]

**Abbildung 3: Workflow zum Veröffentlichen und Ausführen einer Anwendung mit Batch Apps**

1.	Vorbereiten eines **Anwendungsabbilds** – Eine ZIP-Datei mit den vorhandenen ausführbaren Dateien der Anwendung und allen benötigten unterstützenden Dateien. Dabei kann es sich um dieselben ausführbaren Dateien handeln, die mit einer herkömmlichen Serverfarm oder einem Cluster ausgeführt werden.
2.	Erstellen Sie eine ZIP-Datei der **Cloud-Assembly**, die Workloads für den Batch-Dienst aufruft und disponiert. Dies umfasst zwei Komponenten:

	a. **Auftragssplitter** – Unterteilt einen Auftrag in Aufgaben, die unabhängig voneinander verarbeitet werden können. Beispielsweise würde der Auftragssplitter bei einer Animation einen Auftrag zum Rendern eines Films in einzelne Frames unterteilen.

	b. **Aufgabenprozessor** – Ruft die ausführbare Datei der Anwendung für eine bestimmte Aufgabe auf. Der Prozessor würde z. B. bei einer Animation ein Renderingprogramm zum Rendern des einzelnen Frames aufrufen, der von der Aufgabe angegeben wurde.

3.	Verwenden Sie die Batch Apps–API oder Entwicklertools zum Hochladen von Zip-Dateien, die Sie in den beiden vorherigen Schritten auf Azure-Speicherkonten vorbereitet haben. Diese Dateien müssen im Speicherkonto sein, damit der Batch-Dienst darauf zugreifen kann. Dies wird normalerweise einmal pro Anwendung von einem Dienstadministrator ausgeführt.
4.	Bieten Sie eine Möglichkeit zum Übermitteln von Aufträgen an den aktivierten Anwendungsdienst in Azure. Dies kann ein Plug-In für die Benutzeroberfläche der Anwendung, ein Webportal oder einen unbeaufsichtigten Dienst als Teil des Back-End-Systems sein.

	Zum Ausführen eines Auftrags:

	a. Laden Sie die Eingabedateien hoch (z. B. Quelldaten oder Bilder), die für den Auftrag des Benutzers spezifisch sind. Diese Dateien müssen im Speicherkonto sein, damit der Batch-Dienst darauf zugreifen kann.

	b. Übermitteln Sie einen Auftrag mit den erforderlichen Parametern und der Liste der Dateien.

	c. Überwachen Sie den Auftragsstatus mithilfe der APIs oder dem Batch Apps-Portal.



## <a id="BKMK_Account">Batch-Konto</a>
Sie müssen mindestens ein eindeutiges **Batch-Konto** erstellen, um den Batch-Dienst zu verwenden und damit zu entwickeln. Alle Anforderungen, die Sie an den Batch-Dienst stellen, müssen mit dem Namen eines Kontos und dem dazugehörigen Zugriffsschlüssel authentifiziert werden.

Sie können ein Batch-Konto erstellen und Zugriffsschlüssel für das Konto im Azure-Vorschauportal oder mit den [Batch-PowerShell-Cmdlets](batch-powershell-cmdlets-get-started.md) verwalten.

So erstellen im Portal ein Batch-Konto

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an.

2. Klicken Sie auf **Neu** > **Compute**> **Marketplace** > **Alles** und geben Sie dann *Batch* in das Suchfeld ein.

	![Batch im Marketplace][marketplace_portal]

3. Klicken Sie in den Suchergebnissen auf **Batch-Dienst**, und klicken Sie dann auf **Erstellen**.

4. Geben Sie die folgenden Informationen auf dem Blatt**Neues Batch-Konto** ein:

	a. Geben Sie unter **Kontoname** einen eindeutigen Namen ein, der in der Batch-Konto-URL verwendet werden soll.

	>[AZURE.NOTE]Der Batch-Kontoname muss in Azure eindeutig sein, zwischen 3 und 24 Zeichen umfassen und kann nur Kleinbuchstaben und Zahlen enthalten.

	b. Klicken Sie auf **Ressourcengruppe** wählen eine vorhandene Ressourcengruppe für das Konto aus, oder erstellen eine neue.

	c. Wenn Sie über mehrere Abonnements verfügen, klicken Sie auf **Abonnement**, um ein verfügbares Abonnement auszuwählen, in dem das Konto erstellt wird.

	d. Wählen Sie in **Speicherort** eine Azure-Region aus, in der Batch verfügbar ist.

	![Erstellen eines Batch-Kontos][account_portal]

5. Klicken Sie auf **Erstellen**, um die Kontoerstellung abzuschließen.


Nachdem das Konto erstellt wurde, finden Sie es im Portal vor, um Zugriffsschlüssel und andere Einstellungen zu verwalten. Klicken Sie beispielsweise auf das Symbol zum Verwalten der Zugriffsschlüssel.

![Batch-Kontoschlüssel][account_keys]

## Zusätzliche Ressourcen

* [Erste Schritte mit der Azure Batch-Bibliothek für .NET](batch-dotnet-get-started.md)
* [Entwicklungsbibliotheken und Tools für Azure Batch](batch-development-libraries-tools.md)
* [REST-API-Referenz für Azure Batch](http://go.microsoft.com/fwlink/p/?LinkId=517803)
* [REST-API-Referenz für Azure Batch Apps](http://go.microsoft.com/fwlink/p/?LinkId=517804)

[parallel]: ./media/batch-technical-overview/parallel.png
[marketplace_portal]: ./media/batch-technical-overview/marketplace_batch.PNG
[account_portal]: ./media/batch-technical-overview/batch_acct_portal.png
[account_keys]: ./media/batch-technical-overview/account_keys.PNG
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png
[app_pub_workflow]: ./media/batch-technical-overview/app_pub_workflow.png

<!---HONumber=July15_HO4-->