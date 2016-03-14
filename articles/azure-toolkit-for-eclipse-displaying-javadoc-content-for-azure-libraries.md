<properties
    pageTitle="Anzeigen von Javadoc-Inhalten in Eclipse für das Azure-Bibliothekenpaket für Java"
    description="Anzeigen von Javadoc-Inhalten für die Azure-Bibliotheken in Eclipse."
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
    ms.date="02/26/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

# Anzeigen von Javadoc-Inhalten in Eclipse für das Azure-Bibliothekenpaket für Java #

Die Javadoc-Inhalte für die Azure-Bibliotheken für Java können innerhalb der Eclipse-Umgebung durch Zuordnen dieser Bibliotheken mit den Javadoc-Inhalten angezeigt werden. Die folgenden Schritte veranschaulichen die Verwendung dieser Funktionen in Eclipse.

Dieses Verfahren setzt voraus, dass Sie bereits die Azure-Bibliothek für Java zu Ihrem Buildpfad hinzugefügt haben.

## Anzeigen von Javadoc-Inhalten in Eclipse für die Azure-Bibliotheken für Java ##

* Öffnen Sie im Projektexplorer von Eclipse im Abschnitt **Referenced Libraries** des Projekts das Kontextmenü für die Azure-Bibliothek für Java-JAR. Beispielsweise **microsoft-windowsazure-api-0.1.0.jar** (die Versionsnummer kann anders sein, abhängig von der Version, die Sie installiert haben).
* Klicken Sie auf **Eigenschaften**.
* Klicken Sie im Dialogfeld **Eigenschaften** im linken Bereich auf **Javadoc-Speicherort**. Das Dialogfeld **Javadoc-Speicherort** wird angezeigt.
* Sie können eine **Javadoc-URL** oder ein **archiviertes Javadoc** angeben.
    * Wenn Sie eine **Javadoc-URL** möchten, verwenden Sie eine URL wie z. B. ****http://dl.windowsazure.com/javadoc** oder ****http://dl.windowsazure.com/storage/javadoc**.
* Wenn Sie die Option **archiviertes Javadoc** wählen,können Sie eine externe Datei oder eine Arbeitsbereichsdatei angeben. Treffen Sie Ihre Auswahl und durchsuchen und überprüfen Sie sie nach Bedarf. Im folgenden Beispiel wird den Azure-Bibliotheken für Java das entsprechende Javadoc-JAR zugeordnet, das lokal in einen Ordner namens **c:\\MyAzureJARs** heruntergeladen wurde. ![][ic553487]
* *Optional*: Klicken Sie auf **Überprüfen**. Mögliche Probleme mit dem Javadoc-JAR können hier angezeigt werden.
* Klicken Sie auf **OK**.

Sobald die Zuordnung mit der Bibliothek erfolgt ist, sollten die Javadoc-Inhalte in der Eclipse-IDE angezeigt werden. Wenn z. B. innerhalb des Codes `blob` vom Typ `CloudBlockBlob` definiert ist, ist Folgendes ein Beispiel für Javadoc-Inhalte, die bei der Eingabe von `blob.acquireLease` im Code angezeigt werden:

![][ic553488]

## Weitere Informationen ##

[Azure-Toolkit für Eclipse][]

[Creating a Hello World Application for Azure in Eclipse (in englischer Sprache)][]

[Installieren des Azure-Toolkits für Eclipse][]

Weitere Informationen zum Verwenden von Azure mit Java finden Sie im [Azure Java Developer Center][].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure-Toolkit für Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse (in englischer Sprache)]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installieren des Azure-Toolkits für Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png

<!---HONumber=AcomDC_0302_2016-->