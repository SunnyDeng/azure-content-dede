<properties
    pageTitle="Bereitstellen umfangreicher Bereitstellungen"
    description="Erfahren Sie mehr über das Bereitstellen umfangreicher Bereitstellungen mit dem Azure-Toolkit für Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->

# Bereitstellen umfangreicher Bereitstellungen #

Falls die Bereitstellung zu groß für den Standardordner „approot“ ist, kann eine lokale Speicherressource als Stammordner der Bereitstellung für das JDK und den Anwendungsserver verwendet werden.

## So verwenden Sie eine lokale Speicherressource als Stammordner für große Bereitstellungen ##

1. Erstellen Sie zunächst eine lokale Speicherressource. Der Name dieser Ressource ist unwichtig. Speicherressourcen werden auf Rollenebene definiert. Die schnellste Möglichkeit zum Aufrufen des Dialogfelds für das Konfigurieren von lokalem Speicher, in dem eine neue lokale Speicherressource erstellt werden kann, besteht aus folgenden Schritten: Klicken Sie in der Ansicht **Projekt-Explorer** mit der rechten Maustaste auf die Rolle (erweitern Sie den Azure-Projektknoten, wenn die Rolle nicht angezeigt wird), klicken Sie auf **Azure**, und klicken Sie dann auf **Lokaler Speicher**. Klicken Sie im Dialogfeld **Lokaler Speicher** auf **Hinzufügen**, um eine neue lokale Speicherressource zu erstellen.
1. Legen Sie die gewünschte Größe auf mindestens 2.048 MB fest (eine geringere Größe kann zu denselben Dateigrößenproblemen wie im Ordner „approot“ führen.)
1. Achten Sie darauf, dass das Kontrollkästchen **Clean the contents when the role instance is recycled** aktiviert ist. Dadurch wird verhindert, dass die Startlogik für die Bereitstellung in Konflikt mit vorhandenen Dateien in der Ressource ausgeführt wird, wenn die Rolleninstanz wiederverwendet wird.
1. Achten Sie darauf, dass der Wert von **Environment variable storing the resource's directory path after deployment** auf die Zeichenfolge **DEPLOYROOT** festgelegt ist. Das Dialogfeld Ihrer lokalen Speicherressource sieht ungefähr folgendermaßen aus. ![][ic667943]

Wenn Sie **DEPLOYROOT** für das Feld *Name* Ihrer lokalen Ressource verwenden und den Namen der automatisch generierten Umgebungsvariablen nicht ändern (die in diesem Fall auf **DEPLOYROOT\_PATH** festgelegt wird), funktioniert Ihre Anwendung auch.

Weitere Informationen zum Erstellen einer lokalen Speicherressource finden Sie unter [Local Storage Properties][] (in englischer Sprache).

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
[Local storage properties]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png

<!---HONumber=AcomDC_0302_2016-->