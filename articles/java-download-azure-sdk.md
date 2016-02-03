<properties 
	pageTitle="Herunterladen des Azure SDK für Java" 
	description="Erfahren Sie, wie Sie das Azure SDK für Java herunterladen, mit Beispielcode für Maven-Projekte und grundlegenden Installationsschritten für das Azure-Toolkit für Eclipse." 
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
	ms.date="01/09/2016" 
	ms.author="robmcm"/>

# Herunterladen des Azure SDK für Java #

Dieser Artikel enthält eine Anleitung zum Herunterladen und Installieren der Azure-Bibliotheken für Java.

[Hinweis:][license] Die Azure-Bibliotheken für Java werden unter der **Apache-Lizenz, Version 2.0,** verteilt.

## Azure-Bibliotheken für Java – manueller Download ##

Klicken Sie zum manuellen Installieren der Azure-Bibliotheken für Java auf <http://go.microsoft.com/fwlink/?LinkId=690320>. Es wird eine ZIP-Datei heruntergeladen, die alle Bibliotheken und alle Abhängigkeiten enthält.

Nachdem Sie die ZIP-Datei auf Ihren Computer heruntergeladen haben, extrahieren Sie den Inhalt, und fügen Sie die JAR-Dateien über eine der folgenden Optionen Ihrem Projekt hinzu:

* Importieren Sie die JAR-Dateien in Ihr Java-Projekt in Eclipse.

* Konfigurieren Sie den **Build Path** für das Java-Projekt in Eclipse, sodass er den Pfad der JAR-Dateien enthält.

Ausführliche Informationen zum Einrichten des Buildpfads in Eclipse finden Sie im Artikel [Java Build Path][] (in englischer Sprache) auf der Eclipse-Website.

**Hinweis:** Informationen zur Lizenz und weitere Informationen finden Sie in den Dateien „license.txt“ und „ThirdPartyNotices.txt“ in der ZIP-Datei.

## Azure-Bibliotheken für Java – Erstellung mit Maven ##

### Schritt 1: Einrichten des Projekts für die Verwendung von Maven zum Erstellen ###

Informationen zum Erstellen von Maven-Projekten in Eclipse, die die Azure-Bibliotheken für Java verwenden, finden Sie im Artikel [Getting Started with Azure Management Libraries for Java][maven-getting-started] (in englischer Sprache).

### Schritt 2: Konfigurieren Ihrer Maven-Einstellungen mit den erforderlichen Abhängigkeiten ###

Nachdem das Projekt für die Verwendung von Maven für die Erstellung konfiguriert wurde, können Sie Ihrer Datei „pom.xml“ die erforderlichen Abhängigkeiten mit einer Syntax wie im folgenden Beispiel hinzufügen. Beachten Sie, dass Sie nicht jede im folgenden Beispiel aufgeführte Abhängigkeit hinzufügen müssen. Sie müssen nur die spezifischen Abhängigkeiten hinzufügen, die für Ihr Projekt erforderlich sind.

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

**Hinweis:** Ersetzen Sie in jedem `<version>`-Element im vorangegangenen Beispiel den Platzhalter „n.n.n“ durch gültige Versionsnummern, die Sie im [Azure-Bibliothekenrepository auf Maven][] erhalten.

## Installieren des Azure-Toolkits für Eclipse ##

Dieser Abschnitt enthält grundlegende Informationen zum Installieren des Azure-Toolkits für Eclipse. Weitere Informationen finden Sie unter [Installing the Azure Toolkit for Eclipse][] (in englischer Sprache).

### Voraussetzungen ###

1. Windows-Betriebssystem, wie im Artikel [Neuigkeiten im Azure-Toolkit für Eclipse][] aufgeführt.
1. Macintosh- oder Linux-Betriebssystem, wie im Artikel [Neuigkeiten im Azure-Toolkit für Eclipse][] aufgeführt.
1. Eclipse IDE für Java EE-Entwickler, Indigo oder höher. Dies kann von <http://www.eclipse.org/downloads/> heruntergeladen werden.

### Grundlegende Installationsschritte ###

1. Wählen Sie im Menü von Eclipse **Help**, und wählen Sie **Install New Software** aus.
1. Geben Sie den Websitespeicherort <http://dl.msopentech.com/eclipse> ein, und drücken Sie die **Eingabetaste**.
1. Wählen Sie die zu installierenden Elemente aus, und klicken Sie auf **Finish**.

Im Azure-Toolkit für Eclipse wird die aktuelle Version des Azure SDK verwendet. Diese können Sie mithilfe des Webplattform-Installer (WebPI) unter <http://go.microsoft.com/fwlink/?LinkID=252838> herunterladen. Falls Sie diesen jedoch noch nicht installiert haben, wenn Sie Ihr erstes Azure-Bereitstellungsprojekt erstellen, installiert das Azure-Toolkit für Eclipse automatisch die entsprechende Version von Azure SDK.

## Weitere Informationen ##

[Azure-Toolkit für Eclipse][]

[Installieren des Azure-Toolkits für Eclipse][]

[Creating a Hello World Application for Azure in Eclipse (in englischer Sprache)][]

Weitere Informationen zum Verwenden von Azure mit Java finden Sie im [Azure Java Developer Center][] (in englischer Sprache).

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure-Bibliothekenrepository auf Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Azure-Toolkit für Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse (in englischer Sprache)]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Installieren des Azure-Toolkits für Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Java Build Path]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Neuigkeiten im Azure-Toolkit für Eclipse]: http://go.microsoft.com/fwlink/?LinkId=690333

<!---HONumber=AcomDC_0114_2016-->