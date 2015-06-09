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
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="danlep"/>


#Azure Batch – Technische Übersicht
Azure Batch hilft Ihnen, umfangreiche parallel und hohe Performance computing (HPC)-Anwendungen in der Cloud ausgeführt. Es ist eine Plattform, Auftragsplanung und ermöglicht automatische Skalierung einer verwalteten Auflistung von virtuellen Computern (VMs), um die Aufgaben auszuführen. Mithilfe des Batch-Diensts können Sie konfigurieren, Batch-Arbeitsauslastungen in Azure bei Bedarf oder nach einem Zeitplan ausgeführt, und sich keine Gedanken über die Komplexität der Aufträge planen und Verwalten von virtuellen Computern in der zugrunde liegenden Plattform.

>[AZURE.NOTE]Batch ist in der Vorschauphase. Um Batch zu verwenden, benötigen Sie ein Azure-Konto, und Sie müssen für die Batch-Vorschau registrieren. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Create a Windows Azure account (in englischer Sprache)](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/).


## Anwendungsfälle

Batch verwendet die Flexibilität und die Skalierung der Cloud helfen Ihnen gerne bei *Batchverarbeitung* oder *Batch computing* -Ausführen einer großen Menge von ähnliche Aufgaben erstellen, den gewünschten Ergebnis. Ein Befehlszeilenpogramm oder Skript erhält einen Satz von Dateien als Eingabe, verarbeitet die Daten in einer Reihe von Aufgaben, und erstellt eine Gruppe von Ausgabedateien. Die Ausgabedateien möglicherweise das endgültige Ergebnis oder ein Zwischenschritt in einem größeren Workflow.

Batch Computing ist ein häufig genutztes Muster für Unternehmen, die Verarbeiten, Transformieren und Analysieren großer Datenmengen durchführen, entweder nach einem Zeitplan oder bei Bedarf. Es umfasst Verarbeitung am Ende der Schleife, z. B. bei täglichen Risikoberichten einer Bank oder bei Lohnbuchhaltung, die planmäßig ausgeführt werden muss. Darüber hinaus umfasst es umfangreiche Geschäfts-, Wissenschafts- und Engineering-Anwendungen, die in der Regel Tools und Ressourcen von einem Computecluster oder -raster benötigen. Anwendungen umfassen herkömmliche HPC-Anwendungen wie fluiddynamische Simulationen sowie spezielle Arbeitsauslastungen in den Bereichen Automobilindustrie, Öl-und Gasförderung, Biowissenschaftsforschung und Erstellung digitaler Inhalte.

Batch funktioniert sehr gut bei intrinsisch parallelen (manchmal als "hochgradig parallel" bezeichnete) Anwendungen oder Arbeitsauslastungen, mit denen parallele Aufgaben auf mehreren Computern ausgeführt werden, wie z. B. die Compute-VMs, die vom Batch-Dienst verwaltet werden. Siehe Abbildung 1.

![Parallele Aufgaben][parallel]

**Abbildung 1. Parallele Aufgaben, die auf mehreren Computern**

Beispiele:

* Risikomodellierung im Finanzbereich
* Bildrendering und-verarbeitung
* Mediencodierung und-transcodierung
* Analyse genetischer Sequenzen
* Testen von Software

Batch können Sie auch nutzen, um parallele Berechnungen mit einem Reduzierungsschritt am Ende und andere kompliziertere, nicht azyklische parallele Arbeitsauslastungen auszuführen.

>[AZURE.NOTE]Die Batch-Vorschau unterstützt derzeit Message-Passing-Interface (MPI)-Anwendungen.

## Entwicklungsszenarien

Die REST-basierten Batch-APIs unterstützen zwei Entwicklungsszenarien, mit denen Sie die Konfiguration und Ausführung von Arbeitsauslastungen Batch mit dem Batch-Dienst durchführen können:

1. **Verteilen Berechnungen als Workitems** -verwenden der *Batch* APIs zum Erstellen und verwalten einen flexiblen Pool von Compute VMs Arbeit wird angeben. Dies gibt Ihnen vollständige Kontrolle über Ressourcen und erfordert, dass den Client der Aufgabe Ausführungspipeline – z. B. zu verwalten, mit einem Workflow-Manager oder das Meta-Planer, mit der Batch-REST-APIs oder optional eine Auftrags-Manager-Feature des Arbeitselements implementiert werden kann. Statt eines Serverclusters einrichten oder Code schreiben zu müssen, um Aufträge in die Warteschlange zu stellen und zu planen, automatisieren Sie die Planung von Computeaufträgen und skalieren einen Pool von Compute-VMs nach oben oder unten, um sie auszuführen. Im Rahmen des Workitems angeben müssen Sie alle Abhängigkeiten definieren und definieren die Bewegung von Eingabe-und Ausgabedateien.

2. **Veröffentlichen und Ausführen von Anwendungen mit dem Dienst Batch** – der *Batch Apps* -APIs funktionieren auf einer höheren Ebene, so können Sie eine vorhandene Anwendung zu umschließen, damit er ausgeführt wird, als ein Dienst in einem Pool von virtuellen Computern im Hintergrund als Batch verwaltet. Die Anwendung möglicherweise eine, die heute auf Clientarbeitsstationen oder Compute-Cluster ausgeführt wird. Das Batch Apps-Framework ist für die Verschiebung von Eingabe- und Ausgabedateien, Auftragsausführung, Auftragsverwaltung und Datenpersistenz verantwortlich. Batch-Apps ermöglicht Ihnen auch, Aufgaben dafür zu erstellen, wie Daten partitioniert werden, sowie für mehrere Schritte eines Auftrags. Enthalten ist eine REST-basierte API und das Batch Apps-Portal, auf das Sie vom Azure-Verwaltungsportal aus zugreifen können, und hilft Ihnen übermittelte Aufträge zu überwachen.


## Batch-Konzepte

In den folgenden Abschnitten werden wichtige Konzepte für die Arbeit mit dem Batch-Dienst und APIs zusammengefasst. Weitere Informationen finden Sie unter [API-Grundlagen für Azure Batch](batch-api-basics.md).

* [Batch-Konto](#BKMK_Account)
* [Tvms und pools](#BKMK_TVM)
* [WorkItems, Aufträgen und Aufgaben](#BKMK_Workitem)
* [Dateien und Verzeichnisse](#BKMK_Files)

### <a id="BKMK_Account">Batch-Konto</a>
Sie benötigen eine eindeutige **Konto als Batch** arbeiten mit dem Batch-Dienst. Alle Anforderungen, die an den Dienst Batch müssen mit dem Namen des Kontos und die Zugriffstaste authentifiziert werden.

Sie können eine Batch-Konto erstellen und Verwalten von Zugriffstasten für das Konto in der Azure-Portal oder mit dem [Batch-PowerShell-Cmdlets](batch-powershell-cmdlets-get-started.md).

So erstellen eine Batch-Konto im Portal:

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie auf **Neu**, klicken Sie auf **berechnen**, klicken Sie auf **Batch Service**, und klicken Sie dann auf **Schnellerfassung**. ![Erstellen eines Batch-Kontos][account_portal]

3. Geben Sie Folgendes ein:

	* In **Kontoname**, geben Sie einen eindeutigen Namen in der Batch-URL verwenden.
	* In **Region**, wählen Sie eine Region aus, in dem Batch verfügbar ist.
	* Wenn Sie mehr als ein Abonnement, in verfügen **Abonnement**, wählen Sie eine verfügbare Abonnement, das für die Verwendung des Stapels in Rechnung gestellt wird.


### <a id="BKMK_TVM">Tvms und pools</a>

Ein **TVM** (TVM) ist ein Azure-VM, dass der Batch-Dienst ist für die Ausführung eines bestimmtes Aufgabe für Ihre Anwendung – wie z. B. eine ausführbare Datei (.exe) oder im Fall von Batch-Apps, ein oder mehrere Programme aus einem Image Anwendung. Im Gegensatz zu einer typischen Azure-VM nicht bereitstellen oder eine TVM direkt verwalten; stattdessen der Batch-Dienst erstellt und verwaltet TVMs als ein **Pool** ähnlich konfigurierte VMs, wie dargestellt in Abbildung 2.

![Pool und TVMs][TVM_pool]

**Abbildung 2. Ein Pool von TVMs**

Wenn Sie die Batch-APIs verwenden, können Sie einen Pool direkt erstellen oder erstellen automatisch bei der Angabe der zu erledigenden Arbeit. Wenn Sie die Batch Apps-APIs verwenden, wird ein Pool automatisch erstellt, wenn Sie die cloudfähige Batch-Anwendung ausführen.


Attribute aus einem Pool enthalten:

* Ein [Größe](http://msdn.microsoft.com/library/azure/dn197896.aspx) für die TVMs
* Das Betriebssystem, das auf den TVMs ausgeführt wird
* Die maximale Anzahl von TVMs
* Eine Skalierungsrichtlinie für den Pool – Eine Formel, die dynamisch anhand der aktuellen Arbeitsauslastung und der Ressourcennutzung die Anzahl der TVMs anpasst, die Aufgaben verarbeiten.
* Gibt an, ob Firewallports auf den TVMs aktiviert sind, um Intrapoolkommunikation zuzulassen.
* Ein Zertifikat, das auf den TVMs installiert ist – z. B. zum Authentifizieren des Zugriffs auf ein Speicherkonto.
* Eine Startaufgabe für TVMs, für einmalige Vorgänge wie die Installation von Anwendungen oder das Herunterladen von Daten, die von Aufgaben verwendet werden.

>[AZURE.NOTE]Derzeit können auf TVMs nur die Betriebssysteme Windows Server 2012 R2, Windows Server 2012 und Windows Server 2008 R2 SP1 ausgeführt werden.

Ein Pool kann nur von dem Batch-Konto verwendet werden, in dem er erstellt wurde. Ein Batch-Konto kann über mehrere Pools verfügen.

Jede TVM in einem Pool Ruft einen eindeutigen Namen und IP-Adressen zugewiesen. Wenn eine TVM aus einem Pool entfernt wird, gehen die Änderungen verloren, die am Betriebssystem, lokalen Dateien, ihrem Namen und ihrer IP-Adresse vorgenommen wurden. Wenn eine TVM einen Pool verlässt, endet ihre Lebensdauer.


### <a id="BKMK_Workitem">WorkItems, Aufträgen und Aufgaben</a>

Ein **Workitem** ist eine Vorlage, der angibt, wie eine Anwendung auf TVMs in einem Pool ausgeführt wird. Ein **Auftrag** ein geplanten WorkItems ist und möglicherweise einmal oder erneut auftritt. Ein Auftrag besteht aus einer Auflistung von **Aufgaben**. Abbildung 3 zeigt die grundlegenden Beziehungen.

![WorkItem, Aufträgen und Aufgaben][job_task]

**Abbildung 3. Ein Arbeitselement, Aufträgen und Aufgaben**

Je nach die APIs, die Sie verwenden, um mit der Batch entwickeln, müssen Sie mehr oder weniger Details Workitems, Aufträgen und Aufgaben zu verwalten.

* Bei der Entwicklung einer Anwendung mit der Batch-APIs müssen Sie programmgesteuert Workitems, Aufträgen und Aufgaben definieren und konfigurieren Sie die TVM Pools, die die Aufgaben ausgeführt werden.

* Wenn Sie eine Clientanwendung mithilfe der Batch Apps–APIs und -Tools integrieren, können Sie Komponenten verwenden, die automatisch einen Auftrag in Aufgaben unterteilen, die Aufgaben verarbeiten und die Ergebnisse der einzelnen Vorgänge für die abschließenden Auftragsergebnisse zusammenführen. Wenn Sie die Arbeitsauslastung auf den Batch-Dienst senden, wird das Batch-Apps-Framework verwaltet die Aufträge und führt die Aufgaben auf die TVMs.



### <a id="BKMK_Files">Dateien und Verzeichnisse</a>

Bei einer Datei handelt es sich um Daten, die als Eingabe für eine Aufgabe des Auftrags verwendet werden. Einer Aufgabe kann keine, eine oder es können ihr mehrere Eingabedateien zugeordnet sein. Dieselbe Datei kann auch für mehrere Aufgaben verwendet werden, beispielsweise die Aufgaben in einem Auftrag zum Rendern eines Films. Hier kann es häufig verwendete Strukturen und Modelle geben. Bei Aufgaben in einem Datenanalyseauftrag können die Dateien eine Reihe von Beobachtungen oder Messungen sein.

Für jede Aufgabe, die die Eingabedaten verarbeiteten und die Ausgabedaten erstellt wird, enthält, ist ein Arbeitsverzeichnis vorhanden. Diese Verzeichnisse und Dateien stehen für die Verwendung durch andere Tasks zur Verfügung, während ein Auftrag ausgeführt wird. Alle Aufgaben, Dateien und Verzeichnisse auf einer TVM gehören einem einzelnen Benutzerkonto.

Je nach mit Batch Verwendung APIs verwenden, müssen Sie wieder mehr oder weniger Details zu den Eingabe- und Dateien für Aufträge und Aufgaben zu verwalten. Wenn Sie mit der Batch-APIs entwickeln, geben Sie diese Abhängigkeiten und die Datei Bewegungen explizit. Mit Batch Apps verarbeitet das Framework die meisten dieser Details für Sie.

## WorkItems-workflow
Abbildung 4 zeigt Sie, wie eine Anwendung in einem Pool übermittelt, in dem sie zur Verarbeitung gesendet werden. Dabei wird der Batch-API verwendet.

![WorkItems-workflow][work_item_workflow]

**Abbildung 4. Workflow zum Verteilen von Arbeitselementen in einem Pool zusammengefassten virtuellen Computern**

1.	Hochladen von Eingabedateien (z. B. Quelldaten oder Bilder), die für einen Auftrag zu einem Azure-Speicherkonto erforderlich sind. Diese Dateien müssen im Speicherkonto sein, damit der Batch-Dienst darauf zugreifen kann. Der Batch-Dienst lädt sie beim Ausführen der Aufgabe in eine TVM.
2.	Laden Sie die abhängigen Binärdateien in das Speicherkonto hoch. Die Binärdateien enthalten das Programm, das durch die Aufgabe und die abhängigen Assemblys ausgeführt wird. Auf diese Dateien muss auch aus dem Speicher zugegriffen werden und sie werden auf die TVM geladen.
3.	Erstellen Sie einen Pool von TVMs, geben Sie die Größe der TVMs im Pool, das ausgeführte Betriebssystem und andere Eigenschaften an. Wenn eine Aufgabe ausgeführt wird, wird ihr eine TVM aus diesem Pool zugewiesen.
4.	Erstellen Sie ein Arbeitselement. Ein Auftrag wird automatisch erstellt werden, wenn ein Arbeitselement zu erstellen. Mit einem Arbeitselement können Sie einen Auftrag von Aufgaben verwalten.
5.	Hinzufügen von Aufgaben zum Auftrag. Jede Aufgabe verwendet das Programm, das Sie hochgeladen haben, um Informationen aus einer hochgeladenen Datei zu verarbeiten.
6.	Führen Sie die Anwendung aus, und überwachen Sie die Ergebnisse der Ausgabe.

## Workflow zum Veröffentlichen und Ausführen von Anwendungen
Abbildung 5 zeigt einen einfachen Arbeitsablauf zum Veröffentlichen einer Anwendung mit der Batch Apps-API und zum darauf folgenden Übermitteln von Aufträgen an die Anwendung mit Batch.

![Application publishing-workflow][app_pub_workflow]

**Abbildung 5. Workflow veröffentlichen und Ausführen einer Anwendung mit Batch-Apps**

1.	Vorbereiten einer **Anwendungsabbild** – eine Zip-Datei, die vorhandene Anwendung ausführbaren Dateien und alle Unterstützungsdateien, die sie benötigen. Dabei kann es sich um dieselben ausführbaren Dateien handeln, die mit einer herkömmlichen Serverfarm oder einem Cluster ausgeführt werden.
2.	Erstellen Sie eine Zip-Datei von der **cloud-Assembly** aufrufen und dispatch-Arbeitsauslastungen mit dem Batch-Dienst. Diese enthält zwei Komponenten, die über das SDK verfügbar sind:

	a. **Auftrag Splitter** – spaltet einen Auftrag in Aufgaben, die unabhängig voneinander verarbeitet werden können. Beispielsweise würde der Auftragssplitter bei einer Animation einen Auftrag zum Rendern eines Films in einzelne Frames unterteilen.

	b. **Prozessor** – ausführbare Datei für eine bestimmte Aufgabe die Anwendung aufruft. Der Prozessor würde z. B. bei einer Animation ein Renderingprogramm zum Rendern des einzelnen Frames aufrufen, der von der Aufgabe angegeben wurde.

3.	Verwenden Sie die Batch Apps–APIs oder Entwicklertools zum Hochladen von Zip-Dateien, die Sie in den beiden vorherigen Schritten auf Azure-Speicherkonten vorbereitet haben. Diese Dateien müssen im Speicherkonto sein, damit der Batch-Dienst darauf zugreifen kann. Dies wird normalerweise einmal pro Anwendung von einem Dienstadministrator ausgeführt.
4.	Bieten Sie eine Möglichkeit zum Übermitteln von Aufträgen an den aktivierten Anwendungsdienst in Azure. Dies kann ein Plug-In für die Benutzeroberfläche der Anwendung, ein Webportal oder einen unbeaufsichtigten Dienst als Teil des Back-End-Systems sein. Es stehen für das SDK Beispiele zur Verfügung, um verschiedene Optionen zu veranschaulichen.

	Zum Ausführen eines Auftrags:

	a. Laden Sie die Eingabedateien hoch (z. B. Quelldaten oder Bilder), die für den Auftrag des Benutzers spezifisch sind. Diese Dateien müssen im Speicherkonto sein, damit der Batch-Dienst darauf zugreifen kann.

	b. Übermitteln Sie einen Auftrag mit den erforderlichen Parametern und der Liste der Dateien.

	c. Überwachen Sie den Auftragsstatus mithilfe der APIs oder dem Batch Apps-Portal.

	d. Laden Sie die Ausgaben herunter.

## Zusätzliche Ressourcen

* [Erste Schritte mit der Azure-Batch-Bibliothek für .NET](batch-dotnet-get-started.md)
* [Azure Batch Entwicklungsbibliotheken und tools](batch-development-libraries-tools.md)
* [Azure Batch REST-API-Referenz](http://go.microsoft.com/fwlink/p/?LinkId=517803)
* [Azure Batch Apps REST-API-Referenz](http://go.microsoft.com/fwlink/p/?LinkId=517804)

[parallel]: ./media/batch-technical-overview/parallel.png
[TVM_pool]: ./media/batch-technical-overview/TVM_pool.png
[job_task]: ./media/batch-technical-overview/job_task.png
[account_portal]: ./media/batch-technical-overview/account_portal.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png
[app_pub_workflow]: ./media/batch-technical-overview/app_pub_workflow.png

<!---HONumber=GIT-SubDir-->