<properties urlDisplayName="How to create" pageTitle="Erstellen eines Media Services-Kontos - Azure" metaKeywords="" description="Describes how to create a new Media Services account in Azure." metaCanonical="" services="media-services" documentationCenter="" title="How to Create a Media Services Account" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />




#Erstellen eines Media Services-Kontos

Über das Azure-Verwaltungsportal können Sie schnell ein Azure Media Services-Konto erstellen. Sie können mit Ihrem Konto auf Mediendienste zugreifen, die Funktionen zum Speichern, Verschlüsseln, Codieren, Verwalten und Streamen von Medieninhalten in Azure bereitstellen. Beim Erstellen eines Media Services-Kontos erstellen Sie auch ein zugehöriges Speicherkonto (oder verwenden ein vorhandenes) in der gleichen geografischen Region wie das Media Services-Konto. 

In diesem Thema wird erläutert, wie Sie mithilfe der Schnellerfassung ein neues Media Services-Konto erstellen und anschließend einem Speicherkonto zuordnen. 

##Inhaltsverzeichnis

-  [Konzepte][]
-  [Gewusst wie: Erstellen eines Media Services-Kontos mithilfe der Schnellerfassung][]


<h2><a id="concepts"></a>Konzepte</h2>
Für den Zugriff auf Media Services sind zwei zugeordnete Konten erforderlich:

-   **Ein Media Services-Konto**. Über Ihr Konto erhalten Sie Zugriff auf eine Reihe cloudbasierter Mediendienste in Azure. In einem Media Services-Konto wird kein Medieninhalt gespeichert. Stattdessen werden Metadaten über die Medieninhalte und Medienverarbeitungsaufträge in Ihrem Konto gespeichert. Beim Erstellen des Kontos wählen Sie eine verfügbare Media Services-Region aus. Bei der ausgewählten Region handelt es sich um ein Rechenzentrum, in dem Metadaten-Datensätze für Ihr Konto gespeichert werden. 
    > [WACOM.NOTE]
    > Folgende Media Services-Regionen sind verfügbar: **Nordeuropa**, **Westeuropa**, **USA (West)**, **USA (Ost)**, **Südostasien**, **Ostasien**, **Japan (West)**, **Japan (Ost)**. Media Services verwendet keine Affinitätsgruppen. 
-   **Ein zugeordnetes Speicherkonto**. Bei Ihrem Speicherkonto handelt es sich um ein Azure-Speicherkonto, das Ihrem Media Services-Konto zugeordnet ist. Das Speicherkonto stellt Blob-Speicher für Mediendateien zur Verfügung und muss sich in derselben geografischen Region befinden wie das Media Services-Konto. Bei der Erstellung eines Media Services-Kontos können Sie entweder ein vorhandenes Speicherkonto in derselben Region auswählen oder ein neues Speicherkonto in derselben Region erstellen. Wenn Sie ein Media Services-Konto löschen, bleiben die Blobs im zugehörigen Speicherkonto erhalten. 

<h2><a id="quick"></a>Gewusst wie: Erstellen eines Media Services-Kontos mithilfe der Schnellerfassung</h2>

1. Klicken Sie im [Verwaltungsportal][] auf **Neu**, dann auf **Media Service** und anschließend auf **Schnellerfassung**.
   
	![Media Services Quick Create](./media/media-services-create-account/wams-QuickCreate.png)

2. Geben Sie in das Feld **NAME** den Namen des neuen Kontos ein. Der Name eines Media Services-Kontos darf nur Kleinbuchstaben oder Ziffern ohne Leerzeichen enthalten und muss aus 3 bis 24 Zeichen bestehen. 

3. Wählen Sie unter **REGION** die geografische Region aus, in der die Metadaten-Datensätze für Ihr Media Services-Konto gespeichert werden sollen. In der Dropdownliste werden nur die verfügbaren Media Services-Regionen angezeigt. 

4. Wählen Sie unter **SPEICHERKONTO** ein Speicherkonto aus, das als Blob-Speicher für die Medieninhalte aus Ihrem Media Services-Konto dienen soll. Sie können ein vorhandenes Speicherkonto in derselben geografischen Region wie Ihr Media Services-Konto auswählen oder ein neues Speicherkonto erstellen. Ein neues Speicherkonto wird in derselben Region erstellt. 

5. Wenn Sie ein neues Speicherkonto erstellt haben, geben Sie im Feld **NEUER SPEICHERKONTONAME** einen Namen für das Speicherkonto an. Für Namen von Speicherkonten gelten die gleichen Regeln wie für Namen von Media Services-Konten.

6. Klicken Sie unten im Formular auf **Schnellerfassung**.

	Sie können den Status des Prozesses im Meldungsbereich unten im Fenster überwachen.

	Die Seite **Mediendienste** wird geöffnet, und das neue Konto wird angezeigt. Wenn sich der Status in "Aktiv" ändert, wurde das Konto erfolgreich erstellt.

	![Media Services Page](./media/media-services-create-account/wams-mediaservices-page.png)

	Wenn Sie auf den Kontonamen doppelklicken, wird standardmäßig die Seite "Schnellstart" angezeigt. Auf dieser Seite können Sie einige Verwaltungsaufgaben ausführen, die auch auf anderen Seiten des Portals verfügbar sind. Sie können beispielsweise eine Videodatei auf dieser Seite oder auf der Seite INHALT hochladen.

	Außerdem können Sie Code anzeigen, der mithilfe des Azure Media Services SDK die folgenden Aufgaben ausführt: Videos hochladen, verschlüsseln und veröffentlichen. Sie können auf einen der Links unter dem Abschnitt ETWAS CODE SCHREIBEN klicken, den Code kopieren und in Ihrer Anwendung verwenden. 


<!-- Reusable paths. -->

<!-- Anchors. -->
  [Konzepte]: #concepts
  [Voraussetzungen]: #begin
  [Gewusst wie: Erstellen eines Media Services-Kontos mithilfe der Schnellerfassung]: #quick

<!-- URLs. -->
  [Webplattform-Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  
  [Verwaltungsportal]: http://manage.windowsazure.com/



<!--HONumber=35.1-->
