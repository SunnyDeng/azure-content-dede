<properties linkid="batch-technical-overview" urlDisplayName="" pageTitle="Azure Batch - Technische Übersicht" metaKeywords="" description="Erfahren Sie mehr über Konzepte, Workflows und Szenarien des Azure Batch-Dienstes." metaCanonical="" services="batch" documentationCenter="" title="Azure Batch technical overview" authors="danlep" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="batch" ms.workload="big-compute" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/08/2014" ms.author="danlep" />


#Azure Batch - Technische Übersicht
Mit Azure Batch können Sie umfangreiche paralleles und High Performance Computing (HPC)-Anwendungen in der Cloud ausführen, dank AuftVerschieben ragsplanung und automatischer Skalierung von Rechnerressourcen als Plattformdienst. Mithilfe der Batch-SDK- und Batch-Dienste können Sie Batch-Arbeitsauslastungen so konfigurieren, dass sie auf einer verwalteten Auflistung von virtuellen Computern bei Bedarf oder nach einem Zeitplan ausgeführt werden. so müssen Sie sich nicht mit der Komplexität der Auftragsplanung und Verwalten von Ressourcen in der zugrunde liegenden Plattform befassen.
 
>[WACOM.NOTE]Batch ist in der Vorschauphase. Um Batch zu verwenden benötigen Sie ein Azure-Konto und die Batch-Vorschau muss für Ihr Abonnement aktiviert werden. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Erstellen von Azure-Konten](http://www.windowsazure.com/de-de/develop/php/tutorials/create-a-windows-azure-account/). 


Dieser Artikel vermittelt Ihnen einen Überblick über:
 
* [Anwendungsfälle](#BKMK_Scenarios)
* [Entwicklungsszenarien](#BKMK_Approaches)
* [Batch-Konzepte](#BKMK_Entities)
* [Workflows für Arbeitsaufgaben](#BKMK_Workflow_workitems)
* [Workflow zum Veröffentlichen und Ausführen von Anwendungen](#BKMK_Workflow_cloudappss)
* [Zusätzliche Ressourcen](#BKMK_Resources)

<h2 id="BKMK_Scenarios">Anwendungsfälle</h2>

Batch verwendet die Flexibilität und die Skalierung der Cloud zur Unterstützung der *Batchverarbeitung* oder von *Batch Computing* - Programmgesteuertes Ausführen großer Mengen an ähnlichen Aufgaben. Ein Befehlszeilenpogramm oder Skript erhält einen Satz von Dateien als Eingabe, verarbeitet die Daten in einer Reihe von Aufgaben, und erstellt eine Gruppe von Ausgabedateien. Die Ausgabedateien können die Endergebnisse oder ein Zwischenschritt in einem größeren Workflow sein.       
 
Batch Computing ist ein häufig genutztes Muster für Unternehmen, die Verarbeiten, Transformieren und Analysieren großer Datenmengen durchführen, entweder nach einem Zeitplan oder bei Bedarf. Es umfasst Verarbeitung am Ende der Schleife, z. B. bei täglichen Risikoberichten einer Bank oder bei Lohnbuchhaltung, die planmäßig ausgeführt werden muss. Darüber hinaus umfasst es umfangreiche Geschäfts-, Wissenschafts- und Engineering-Anwendungen, die in der Regel Tools und Ressourcen von einem Computecluster oder -raster benötigen. Anwendungen umfassen herkömmliche HPC-Anwendungen wie fluiddynamische Simulationen sowie spezielle Arbeitsauslastungen in den Bereichen Automobilindustrie, Öl-und Gasförderung, Biowissenschaftsforschung und Erstellung digitaler Inhalte. 
 
Batch funktioniert sehr gut bei intrinsisch parallelen (manchmal als "hochgradig parallel" bezeichnete) Anwendungen oder Arbeitsauslastungen, mit denen parallele Aufgaben auf mehreren Computern ausgeführt werden, wie z. B. die Compute-VMs, die vom Batch-Dienst verwaltet werden. Siehe Abbildung 1.  

![Parallel tasks][parallel]

**Abbildung 1: Parallele Aufgaben, die auf mehreren Computern ausgeführt werden**

Beispiele:

* Risikomodellierung im Finanzbereich 
* Bildrendering und-verarbeitung
* Mediencodierung und-transcodierung
* Analyse genetischer Sequenzen
* Testen von Software

Batch können Sie auch nutzen, um parallele Berechnungen mit einem Reduzierungsschritt am Ende und andere kompliziertere, nicht azyklische parallele Arbeitsauslastungen auszuführen. 

>[WACOM.NOTE]Die Batch-Vorschau unterstützt derzeit Message-Passing-Interface (MPI)-Anwendungen.

<h2 id="BKMK_Approaches">Entwicklungsszenarien</h2>

Die REST-basierten Batch-APIs unterstützen zwei Entwicklungsszenarien, mit denen Sie die Konfiguration und Ausführung von Arbeitsauslastungen Batch mit dem Batch-Dienst durchführen können:
 
1. **Verteilen von Berechnungen als Arbeitsaufgaben** - Verwenden Sie die *Batch*-APIs, um einen flexiblen Pool von Compute-VMs zu erstellen und zu verwalten und von Arbeitsaufgaben anzugeben, die auf ihnen ausgeführt werden können. Dies gibt Ihnen vollständige Kontrolle über Ressourcen und erfordert, dass der Client die Ausführungspipeline für Aufgaben verwaltet - z. B. mit einem Workflowmanager oder Meta-Planer, der mit Batch-REST-APIs oder optional einem Auftragsmanagerfeature der Arbeitsaufgabe implementiert werden kann. Statt eines Serverclusters einrichten oder Code schreiben zu müssen, um Aufträge in die Warteschlange zu stellen und zu planen, automatisieren Sie die Planung von Computeaufträgen und skalieren einen Pool von Compute-VMs nach oben oder unten, um sie auszuführen. Als Teil der Angabe von Arbeitsaufgaben definieren Sie alle Abhängigkeiten und definieren die Verschiebung von Eingabe-und Ausgabedateien. 

2. **Veröffentlichen und Ausführen von Anwendungen mit dem Batch-Dienst** - Die *Batch Apps*-APIs bieten ein höheres Maß an Abstraktion und die Auftragsausführungspipeline wird vom Batch-Dienst gehostet. Mit Batch Apps können Sie einen Arbeitsauslastungsbatch als Dienst in der Cloud über eine Anwendung erstellen, die heute auf Clientarbeitsstationen oder Computeclustern ausgeführt wird. Mit Batch-Apps können Sie vorhandene Binärdateien und ausführbare Dateien umschließen und sie so veröffentlichen, dass sie auf in einem Pool zusammengefassten virtuelle Computern ausgeführt werden, die der Batch-Dienst im Hintergrund erstellt und verwaltet. Das Batch Apps-Framework ist für die Verschiebung von Eingabe- und Ausgabedateien, Auftragsausführung, Auftragsverwaltung und Datenpersistenz verantwortlich. Batch-Apps ermöglicht Ihnen auch, Aufgaben dafür zu erstellen, wie Daten partitioniert werden, sowie für mehrere Schritte eines Auftrags. Enthalten ist eine REST-basierte API und das Batch Apps-Portal, auf das Sie vom Azure-Verwaltungsportal aus zugreifen können, und hilft Ihnen übermittelte Aufträge zu überwachen.


<h2 id="BKMK_Entities">Batch-Konzepte</h2>

In den folgenden Abschnitten werden wichtige Konzepte für die Arbeit mit dem Batch-Dienst und APIs zusammengefasst. Weitere Informationen finden Sie unter [Grundlagen der API für Azure-Batch](http://azure.microsoft.com/de-de/documentation/articles/batch-api-basics). 

* [Batch-Konto](#BKMK_Account)
* [TVMs und Pools](#BKMK_TVM)
* [Arbeitsaufgaben, Aufträge und Aufgaben](#BKMK_Workitem)
* [Dateien und Verzeichnisse](#BKMK_Files)

<h3 id="BKMK_Account">Batch-Konto</h3>
Sie benötigen ein eindeutiges **Batch-Konto**, um den Batch-Dienst verwenden zu können. Beim Ausführen von API-Vorgängen mit dem Batch-Dienst authentifizieren Sie Ihre Anforderungen mit der URL des Kontos und einer zugeordneten Zugriffstaste. Außerdem benötigen Sie ein Batch-Konto für den Zugriff auf das Batch Apps-Portal.
 
Über das Azure-Verwaltungsportal können Sie ein Batch-Konto erstellen und Zugriffstasten für das Konto Verwalten.

Gewusst wie - Erstellen eines Batch-Kontos:

1. Melden Sie sich auf dem [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2. Klicken Sie auf **Neu**, **Compute**, klicken Sie auf **Batch-Dienst** und dann auf **Schnellerfassung**.
![Create a Batch account][account_portal]

3. Geben Sie Folgendes ein:

	* Geben Sie unter **Kontoname** einen eindeutigen Namen ein, der in der Batch-URL verwendet werden soll.
	* Wählen Sie im Bereich **Region** eine Region aus, in der Batch verfügbar ist.
	* Wenn Sie über mehrere Abonnements verfügen, wählen Sie unter **Abonnement** ein verfügbares Abonnement aus, dem die Verwendung von Batch in Rechnung gestellt wird.

 
<h3 id="BKMK_TVM">TVMs und Pools</h3>

Eine **TVM** ist ein Azure-VM, den der Batch-Dienst für die Ausführung von einer bestimmten Arbeitsauslastung (Task) für Ihre Anwendung zuweist - z. B. eine ausführbare Datei (.exe) oder, im Fall von Batch Apps, mindestens ein Programm aus einem Anwendungsbild. Im Gegensatz zu einer typischen Azure-VM wird eine TVM nicht direkt bereitgestellt oder verwaltet. Stattdessen erstellt und verwaltet der Batch-Dienst TVMs als **Pool** von ähnlich konfigurierten Rechenressourcen, wie in Abbildung 2 dargestellt. 

![Pool and TVMs][TVM_pool]

**Abbildung 2: Ein Pool von TVMs**

Wenn Sie die Batch-APIs verwenden, können Sie direkt einen Pool erstellen oder den Batch-Dienst so konfigurieren, dass er einen automatisch erstellt, wenn Sie die zu erledigenden Aufgaben angeben. Wenn Sie die Batch Apps-APIs verwenden, wird ein Pool automatisch erstellt, wenn Sie die cloudfähige Batch-Anwendung ausführen.


Attribute aus einem Pool enthalten:

* Eine [Größe](http://msdn.microsoft.com/library/azure/dn197896.aspx) für die TVMs 
* Das Betriebssystem, das auf den TVMs ausgeführt wird
* Die maximale Anzahl von TVMs
* Eine Skalierungsrichtlinie für den Pool - Eine Formel, die dynamisch anhand der aktuellen Arbeitsauslastung und der Ressourcennutzung die Anzahl der TVMs anpasst, die Aufgaben verarbeiten.
* Gibt an, ob Firewallports auf den TVMs aktiviert sind, um Intrapoolkommunikation zuzulassen.
* Ein Zertifikat, das auf den TVMs installiert ist - z. B. zum Authentifizieren des Zugriffs auf ein Speicherkonto.
* Eine Startaufgabe für TVMs, für einmalige Vorgänge wie die Installation von Anwendungen oder das Herunterladen von Daten, die von Aufgaben verwendet werden.

>[WACOM.NOTE]Derzeit können auf TVMs nur die Betriebssysteme Windows Server 2012 R2, Windows Server 2012 und Windows Server 2008 R2 SP1 ausgeführt werden.

Ein Pool kann nur von dem Batch-Konto verwendet werden, in dem er erstellt wurde. Ein Batch-Konto kann über mehrere Pools verfügen.

Jedem virtuellen Aufgabencomputer (TVM), der einem Pool hinzugefügt wird, ist ein eindeutiger Name und eine zugehörige IP-Adresse zugewiesen. Wenn eine TVM aus einem Pool entfernt wird, gehen die Änderungen verloren, die am Betriebssystem, lokalen Dateien, ihrem Namen und ihrer IP-Adresse vorgenommen wurden. Wenn eine TVM einen Pool verlässt, endet ihre Lebensdauer.


<h3 id="BKMK_Workitem">Arbeitsaufgaben, Aufträgen und Aufgaben</h3>

Eine **Arbeitsaufgabe** ist eine Vorlage, die angibt, wie eine Anwendung auf TVMs in einem Pool ausgeführt wird. Ein **Auftrag** ist eine geplante Instanz einer Arbeitsaufgabe und kann einmal oder mehrfach vorkommen. Ein Auftrag besteht aus mehreren **Aufgaben**. Abbildung 3 zeigt die grundlegenden Beziehungen.    
 
![Work item, job, and tasks][job_task]

**Abbildung 3: Eine Arbeitsaufgabe, ein Auftrag und Aufgaben**

Je nach die APIs, die Sie verwenden, um mit Batch zu entwickeln, müssen Sie mehr oder weniger Details zu Arbeitsaufgaben, Aufträgen und Aufgaben verwalten.

* Bei der Entwicklung einer Anwendung mit Batch-APIs der niedrigeren Ebene müssen Sie programmgesteuert alle Arbeitsaufgaben, Aufträge und Aufgaben definieren, die der Batch-Dienst ausführt und die TVM-Pools konfigurieren, die die Aufgaben ausführen.

* Wenn Sie eine Clientanwendung mithilfe der Batch Apps-APIs und -Tools integrieren, können Sie Komponenten verwenden, die automatisch einen Auftrag in Aufgaben unterteilen, die Aufgaben verarbeiten und die Ergebnisse der einzelnen Vorgänge für die abschließenden Auftragsergebnisse zusammenführen. Wenn Sie die Arbeitsauslastung an den Batch-Dienst senden, verwaltet das Batch-Apps-Framework die Aufträge und führt die Aufgaben auf den zugrunde liegenden Serverressourcen aus. 



<h3 id="BKMK_Files">Dateien und Verzeichnissen</h3>

Bei einer Datei handelt es sich um Daten, die als Eingabe für eine Aufgabe des Auftrags verwendet werden. Einer Aufgabe kann keine, eine oder es können ihr mehrere Eingabedateien zugeordnet sein. Dieselbe Datei kann auch für mehrere Aufgaben verwendet werden, beispielsweise die Aufgaben in einem Auftrag zum Rendern eines Films. Hier kann es häufig verwendete Strukturen und Modelle geben. Bei Aufgaben in einem Datenanalyseauftrag können die Dateien eine Reihe von Beobachtungen oder Messungen sein.

Jede Aufgabe verfügt über ein Arbeitsverzeichnis, unter dem Verzeichnisse und Dateien erstellt werden, die zum Speichern des Programms erforderlich sind, das durch eine Aufgabe ausgeführt wird, die Eingabedaten, die durch eine Aufgabe verarbeitet werden, und die Ausgabe der Verarbeitung durch eine Aufgabe. Diese Verzeichnisse und Dateien stehen für die Verwendung durch andere Tasks zur Verfügung, während ein Auftrag ausgeführt wird. Alle Aufgaben, Dateien und Verzeichnisse auf einer TVM gehören einem einzelnen Benutzerkonto.

Erneut, je nach mit Batch verwendeten APIs müssen Sie auch mehr oder weniger Details zu Speicherorten und Verschieben von Eingabe- und Ausgabeparameter für Aufträge und Aufgaben verwalten. Bei der Entwicklung mit Batch-APIs der niedrigeren Ebene geben Sie diese Abhängigkeiten und Verschiebungen von Dateien explizit an. Mit Batch Apps verarbeitet das Framework die meisten dieser Details für Sie. 

<h2 id="BKMK_Workflow_workitems">Workflow für Arbeitsaufgaben</h2>
Abbildung 4 zeigt einen typischen Arbeitsablauf für die Verteilung von Berechnungen auf einen Pool von TVMs mit der Batch-API.

![Work items workflow][work_item_workflow]

**Abbildung 4: Arbeitsablauf für die Verteilung von Arbeitsaufgaben auf einen Pool aus virtuellen Computern**

1.	Hochladen von Eingabedateien (z. B. Quelldaten oder Bilder), die für einen Auftrag zu einem Azure-Speicherkonto erforderlich sind. Diese Dateien müssen im Speicherkonto sein, damit der Batch-Dienst darauf zugreifen kann. Der Batch-Dienst lädt sie beim Ausführen der Aufgabe in eine TVM.
2.	Laden Sie die abhängigen Binärdateien in das Speicherkonto hoch. Die Binärdateien enthalten das Programm, das durch die Aufgabe und die abhängigen Assemblys ausgeführt wird. Auf diese Dateien muss auch aus dem Speicher zugegriffen werden und sie werden auf die TVM geladen.
3.	Erstellen Sie einen Pool von TVMs, geben Sie die Größe der TVMs im Pool, das ausgeführte Betriebssystem und andere Eigenschaften an. Wenn eine Aufgabe ausgeführt wird, wird ihr eine TVM aus diesem Pool zugewiesen.
4.	Erstellen einer Arbeitsaufgabe. Ein Auftrag wird automatisch erstellt, wenn Sie eine Arbeitsaufgabe erstellen. Mit einer Arbeitsaufgabe können Sie einen Auftrag mit Aufgaben verwalten.
5.	Hinzufügen von Aufgaben zum Auftrag. Jede Aufgabe verwendet das Programm, das Sie hochgeladen haben, um Informationen aus einer hochgeladenen Datei zu verarbeiten.
6.	Führen Sie die Anwendung aus, und überwachen Sie die Ergebnisse der Ausgabe.

<h2 id="BKMK_Workflow_cloudapps">Workflow zum Veröffentlichen und Ausführen von Anwendungen </h2>
Abbildung 5 zeigt einen einfachen Arbeitsablauf zum Veröffentlichen einer Anwendung mit der Batch Apps-API und zum darauf folgenden Übermitteln von Aufträgen an die Anwendung mit Batch.

![Application publishing workflow][app_pub_workflow]

**Abbildung 5: Arbeitsablauf zum Veröffentlichen und Ausführen einer Anwendung mit Batch Apps**

1.	Vorbereiten eines **Anwendungsbilds** - Eine Zip-Datei der ausführbaren Dateien der vorhandenen Anwendung und alle benötigten unterstützenden Dateien. Dabei kann es sich um dieselben ausführbaren Dateien handeln, die mit einer herkömmlichen Serverfarm oder einem Cluster ausgeführt werden.
2.	Erstellen Sie eine Zip-Datei der **Cloud-Assembly**, die Arbeitsauslastungen für den Batch-Dienst aufruft und disponiert. Diese enthält zwei Komponenten, die über das SDK verfügbar sind:

	a. **Auftragssplitter** - Unterteilt einen Auftrag in Aufgaben, die unabhängig voneinander verarbeitet werden können. Beispielsweise würde der Auftragssplitter bei einer Animation einen Auftrag zum Rendern eines Films in einzelne Frames unterteilen.

	b. **Aufgabenprozessor** - Ruft die ausführbare Datei der Anwendung für eine bestimmte Aufgabe auf. Der Prozessor würde z. B. bei einer Animation ein Renderingprogramm zum Rendern des einzelnen Frames aufrufen, der von der Aufgabe angegeben wurde. 

3.	Verwenden Sie die Batch Apps-APIs oder Entwicklertools zum Hochladen von Zip-Dateien, die Sie in den beiden vorherigen Schritten auf Azure-Speicherkonten vorbereitet haben. Diese Dateien müssen im Speicherkonto sein, damit der Batch-Dienst darauf zugreifen kann. Dies wird normalerweise einmal pro Anwendung von einem Dienstadministrator ausgeführt.
4.	Bieten Sie eine Möglichkeit zum Übermitteln von Aufträgen an den aktivierten Anwendungsdienst in Azure. Dies kann ein Plug-In für die Benutzeroberfläche der Anwendung, ein Webportal oder einen unbeaufsichtigten Dienst als Teil des Back-End-Systems sein. Es stehen für das SDK Beispiele zur Verfügung, um verschiedene Optionen zu veranschaulichen. 

	Zum Ausführen eines Auftrags:

	a. Laden Sie die Eingabedateien hoch (z. B. Quelldaten oder Bilder), die für den Auftrag des Benutzers spezifisch sind. Diese Dateien müssen im Speicherkonto sein, damit der Batch-Dienst darauf zugreifen kann.

	b. Übermitteln Sie einen Auftrag mit den erforderlichen Parametern und der Liste der Dateien.
	
	c. Überwachen Sie den Auftragsstatus mithilfe der APIs oder dem Batch Apps-Portal.
	
	d. Laden Sie die Ausgaben herunter.
	
<h2 id="BKMK_Resources">Zusätzliche Ressourcen</h2>

* [Erste Schritte mit der Azure-Batch-Bibliothek für .NET](http://azure.microsoft.com/de-de/documentation/articles/batch-dotnet-get-started/)
* [Entwicklungsbibliotheken und Tools für Azure Batch](http://azure.microsoft.com/de-de/documentation/articles/batch-development-libraries-tools/)
* [REST-API-Referenz für Azure Batch](http://go.microsoft.com/fwlink/p/?LinkId=517803)
* [REST-API-Referenz für Azure Batch Apps](http://go.microsoft.com/fwlink/p/?LinkId=517804)

[parallel]: ./media/batch-technical-overview/parallel.png
[TVM_pool]: ./media/batch-technical-overview/TVM_pool.png
[job_task]: ./media/batch-technical-overview/job_task.png
[account_portal]: ./media/batch-technical-overview/account_portal.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png
[app_pub_workflow]: ./media/batch-technical-overview/app_pub_workflow.png

<!--HONumber=35.2-->
