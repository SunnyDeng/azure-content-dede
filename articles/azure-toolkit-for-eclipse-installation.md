<properties
    pageTitle="Installieren des Azure-Toolkits für Eclipse"
    description="Erfahren Sie, wie Sie das Azure-Toolkit für Eclipse installieren."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/19/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/de-DE/library/azure/hh690946.aspx -->

# Installieren des Azure-Toolkits für Eclipse #

Das Azure Toolkit für Eclipse stellt Vorlagen und Funktionen bereit, die Ihnen das einfache Erstellen, Entwickeln, Testen und Bereitstellen von Azure-Anwendungen mithilfe der Eclipse-Entwicklungsumgebung ermöglichen. Es handelt sich um ein Open-Source-Projekt, dessen Quellcode unter der Apache-Lizenz 2.0 auf der GitHub-Website des Projekts verfügbar ist:

<https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava>.

Die folgenden Schritte veranschaulichen die Installation des Azure-Toolkits für Eclipse.

## Voraussetzungen ##

* Eclipse IDE für Java EE-Entwickler, Indigo oder höher. Dies kann von <http://www.eclipse.org/downloads/> heruntergeladen werden.
* Ein Java Developer Kit (JDK) der Version 1.7 oder höher. 
* Betriebssysteme: Das Azure-Toolkit für Eclipse wurde unter den folgenden Betriebssystemen getestet:
    * Windows 10
    * Windows 8 und Windows 8.1
    * Windows 7
    * Windows Server 2012
    * Windows Server 2008

> [AZURE.IMPORTANT]Bei Verwendung des Azure-Toolkits für Eclipse unter Windows erfordert das Toolkit die Installation des Azure SDK in Version 2.7 oder höher. Für das Installieren des Azure SDK gibt es zwei Möglichkeiten:
> 
> * Sie können das Azure SDK mithilfe des Programms [Webplattform-Installer (WebPI)][] herunterladen und installieren.
> * Falls Sie dieses noch nicht installiert haben, wenn Sie Ihr erstes Azure-Bereitstellungsprojekt erstellen, werden Sie aufgefordert, automatisch die erforderliche Version von Azure SDK herunterzuladen.
> 
> Beachten Sie, dass das Azure SDK nur unter Windows erforderlich ist.

## So installieren Sie das Azure-Toolkit für Eclipse ##

1. Starten Sie Eclipse.
2. Klicken Sie im Eclipse-Menü auf <strong>Help</strong> und dann auf <strong>Install New Software</strong>, wie im folgenden Diagramm gezeigt. ![][ic590123]
3. Geben Sie im Dialogfeld <strong>Available Software</strong> im Textfeld <strong>Work with</strong> den Text <strong>http://dl.msopentech.com/eclipse</strong> ein, und drücken Sie dann die <strong>EINGABETASTE</strong>.
4. Aktivieren Sie im Fenster <strong>Name</strong> die Option <strong>Azure Toolkit for Eclipse</strong>, und deaktivieren Sie <strong>Contact all update sites during install to find required software</strong>. Der Bildschirm sieht dann etwa wie folgt aus: ![][ic719482]
5. Nach dem Erweitern von <strong>Azure Toolkit for Eclipse</strong> sehen Sie die folgenden Elemente:
    * **Azure Access Control Services Filter:** Diese Komponente bietet Unterstützung für die Authentifizierung von Benutzern mit Azure ACS.
    * **Azure Common Plugin:** Diese Komponente enthält die gemeinsam verwendeten Funktionen als Basis für die weiteren Komponenten.
    * **Azure Toolkit for Eclipse:** Diese Komponente enthält die Projektkonfigurationslogik, den Publish-to-Cloud-Assistenten und die Benutzeroberfläche.
    * **Microsoft JDBC Driver 4.0 for SQL Server:** Diese Komponente vereinfacht die Anwendungsentwicklung mit SQL-Datenbank.
    * **Package for Apache Qpid Client Libraries for JMS:** Diese Komponente stellt die JMS-Clientbibliothek aus dem Apache Qpid-Projekt in Ihrer Anwendung bereit, um dieser die Verwendung von Messaging über das Advanced Messaging Queuing Protocol (AMQP) zu ermöglichen.
    * **Package for Azure Libraries for Java:** Diese Komponente ermöglicht Ihnen das Erstellen von Azure-Anwendungen in Java, um die skalierbaren Azure-Cloudcomputingressourcen nutzen zu können.
    * **Application Insights-Plug-In for Java:** Diese Komponente ermöglicht Ihnen die Verwendung der Telemetriedatenprotokollierung und der Analysedienste von Azure für Ihre Anwendungen und Server-Instanzen.
6. Klicken Sie auf **Weiter**. (Wenn ungewöhnliche Verzögerungen bei der Installation des Toolkits auftreten, achten Sie darauf, dass **Contact all update sites during install to find required software** deaktiviert ist.)
7. Klicken Sie im Dialogfeld **Install Details** auf **Weiter**.
8. Überprüfen Sie im Dialogfeld **Review Licences** die Bedingungen der Lizenzverträge. Wenn Sie dem Lizenzvertrag zustimmen, klicken Sie auf **I accept the terms of the license agreements**, und klicken Sie dann auf **Fertig stellen**. (Bei den restlichen Schritten wird davon ausgegangen, dass Sie dem Lizenzvertrag zugestimmt haben. Wenn Sie die Bedingungen der Lizenzverträge nicht akzeptieren, beenden Sie den Installationsprozess.)
9. Wenn Sie aufgefordert werden, Eclipse zum Abschließen der Installation neu zu starten, klicken Sie auf **Restart Now**.

## Weitere Informationen ##

[Azure-Toolkit für Eclipse][]

[Creating a Hello World Application for Azure in Eclipse (in englischer Sprache)][]

[What‘s New in the Azure Toolkit for Eclipse (in englischer Sprache)][]

Weitere Informationen zum Verwenden von Azure mit Java finden Sie im [Azure Java Developer Center][] (in englischer Sprache).

<!-- URL List -->

[Azure-Toolkit für Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Creating a Hello World Application for Azure in Eclipse (in englischer Sprache)]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Webplattform-Installer (WebPI)]: http://go.microsoft.com/fwlink/?LinkID=252838
[What‘s New in the Azure Toolkit for Eclipse (in englischer Sprache)]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic590123]: ./media/azure-toolkit-for-eclipse-installation/ic590123.png
[ic719482]: ./media/azure-toolkit-for-eclipse-installation/ic719482.png

<!---HONumber=AcomDC_1203_2015-->