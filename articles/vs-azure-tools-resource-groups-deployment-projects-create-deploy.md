<properties 
   pageTitle="Erstellen und Bereitstellen von Bereitstellungsprojekten für Azure-Ressourcengruppen"
   description="Erstellen und Bereitstellen von Bereitstellungsprojekten für Azure-Ressourcengruppen"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2015"
   ms.author="kempb" />

# Erstellen und Bereitstellen von Bereitstellungsprojekten für Azure-Ressourcengruppen

Die Bereitstellungsprojektvorlage **Azure-Ressourcengruppe** ist in Visual Studio verfügbar, wenn Azure SDK 2.6 installiert ist. In Azure-Ressourcengruppenprojekten können mehrere zusammengehörige Azure-Ressourcen in einem einzelnen Bereitstellungsvorgang zusammengefasst und veröffentlicht werden. Azure-Ressourcengruppenprojekte nutzen eine Technologie namens **Azure-Ressourcen-Manager**. Der **Azure-Ressourcen-Manager** ist ein REST-API-Dienst, der das Definieren von Azure-Ressourcengruppen unterstützt, die mehrere Azure-Ressourcen enthalten, die in der Regel zusammen verwendet werden und einen ähnlichen Lebenszyklus aufweisen. Mithilfe von Ressourcengruppen können Sie für alle Ressourcen in einer Gruppe einen einzigen Funktionsaufruf verwenden, anstatt viele verschiedene Funktionen einzeln für jede Ressource aufzurufen. Weitere Informationen zu Azure-Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](./azure-portal/azure-preview-portal-using-resource-groups/).

Azure-Ressourcengruppenprojekte enthalten JSON-Vorlagen des Azure-Ressourcen-Managers, in denen die Elemente definiert sind, die in einer Ressourcengruppe bereitgestellt werden. Weitere Informationen finden Sie unter [Vorlagensprache des Azure-Ressourcen-Managers](resource-group-authoring-templates.md).

Der Azure-Ressourcen-Manager verfügt über viele verschiedene Ressourcenanbieter, die zum Bereitstellen von Ressourcen, wie z. B. Ubuntu Server und Windows Server 2012 R2, verwendet werden können. In diesem Thema wird eine **Web-Apps**-Ressource verwendet, die eine einfache leere Website in Azure bereitgestellt.

## Erstellen von Azure-Ressourcengruppenprojekten

In diesem Verfahren lernen Sie, ein Azure-Ressourcengruppenprojekt mit einer **Web-App**-Vorlage zu erstellen.

### So erstellen Sie ein Azure-Ressourcengruppenprojekt

1. Wählen Sie in Visual Studio **Datei**, **Neues Projekt** und anschließend **C#** oder **Visual Basic** aus. Wählen Sie dann **Cloud** und anschließend **Azure-Ressourcengruppenprojekt** aus.

    ![Cloudbereitstellungsprojekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796668.png)

1. Wählen Sie die Vorlage aus, die Sie im Azure-Ressourcen-Manager bereitstellen möchten. In diesem Beispiel wählen wir die Vorlage **Web-App** aus.

    ![Vorlage auswählen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796669.png)

    Sie können der Ressourcengruppe später auch weitere Ressourcen hinzufügen.

    >[AZURE.NOTE]Die Liste der verfügbaren Vorlagen wird online abgerufen und kann sich ändern.

    Visual Studio erstellt ein Bereitstellungsprojekt für Azure-Ressourcengruppen für eine Web-App.

1. Erweitern Sie die Knoten im Bereitstellungsprojekt, um festzustellen, was erstellt wurde.

    Da wir für dieses Beispiel die Vorlage "Web-App" gewählt haben, werden die folgenden Dateien angezeigt.

|Dateiname|Beschreibung|
|---|---|
|Deploy-AzureResourceGroup.ps1|Ein PowerShell-Skript, das PowerShell-Befehle für die Bereitstellung im Azure-Ressourcen-Manager aufruft.

**Hinweis** Dieses PowerShell-Skript wird von Visual Studio zum Bereitstellen Ihrer Vorlage verwendet. Alle Änderungen an diesem Skript wirken sich auch auf die Bereitstellung in Visual Studio aus. Gehen Sie also umsichtig vor. | ! WebSite.json| Eine Konfigurationsdatei, die alle Details angibt, die Sie im Azure-Ressourcen-Manager bereitstellen möchten. | | WebSite.param.dev.json| Ein Parameterdatei, die bestimmte Werte enthält, die für die Konfigurationsdatei erforderlich sind. | | AzCopy.exe| Ein Tool, das vom PowerShell-Skript zum Kopieren von Dateien aus dem lokalen Speicherablagepfad in den Container des Speicherkontos verwendet wird. Dieses Tool wird nur verwendet, wenn Sie das Bereitstellungsprojekt zum Bereitstellen von Code zugleich mit der Vorlage konfigurieren.|

Alle Bereitstellungsprojekte für Azure-Ressourcengruppen enthalten die folgenden vier grundlegenden Dateien. Andere Projekte enthalten möglicherweise zusätzliche Dateien zur Unterstützung weiterer Funktionen.

## Anpassen eines Azure-Ressourcengruppenprojekts

Sie können ein Bereitstellungsprojekt anpassen, indem Sie die JSON-Vorlagendateien bearbeiten, in denen bereitzustellenden Azure-Ressourcen beschrieben werden. JSON ist die Kurzform von "JavaScript Object Notation". Es handelt sich um ein serialisiertes Datenformat, das einfach zu verwenden ist.

Azure-Ressourcengruppenprojekte im Projektmappen-Explorer verfügen unter dem Knoten **Vorlagen** über zwei Vorlagendateien, die Sie ändern können: eine Azure-Ressourcen-Manager-Vorlagendatei und eine Parameterdatei.

- **Azure-Ressourcen-Manager-Vorlagendateien** (mit der Erweiterung ".json") geben die Dateien, in denen die gewünschten Ressourcen enthalten sind, sowie Parameter an, die vom Bereitstellungsprojekt benötigt werden, wie etwa den Websitenamen und Speicherort. Sie geben außerdem die Abhängigkeiten der Komponenten in der Azure-Ressourcengruppe und deren Eigenschaften an, wie z. B. Namen, Tags und Regeln für Trigger. Sie können diese Datei ändern, um eigene Funktionen hinzuzufügen. Beispielsweise können Sie der Vorlage eine Datenbank hinzufügen. Informationen zu den Parametern, die Sie angeben müssen, finden Sie in der Dokumentation zu den einzelnen Ressourcenanbietern. Weitere Informationen finden Sie unter [Ressourcenanbieter](https://msdn.microsoft.com/library/azure/dn790572.aspx).

- **Parameterdateien** (mit der Erweiterung "param.*.json") enthalten die Werte für die in der Konfigurationsdatei angegebenen Parameter, die für die einzelnen Ressourcenanbieter erforderlich sind. In diesem Beispiel definiert die Konfigurationsdatei für eine Web-App (WebSite.json) Parameter für "siteName" und "siteLocation". Während der Bereitstellung werden Sie aufgefordert, Werte für die Parameter in der Vorlagendatei anzugeben. Diese Werte werden in der Parameterdatei gespeichert. Sie können die Parameterdatei auch direkt bearbeiten.

JSON-Dateien können im Visual Studio-Editor bearbeitet werden. Wenn Sie die [PowerShell-Tools für Visual Studio](https://visualstudiogallery.msdn.microsoft.com/c9eb3ba8-0c59-4944-9a62-6eee37294597) installieren, verfügen Sie außerdem über Syntaxhervorhebung, Erkennung von zugehörigen Klammern und IntelliSense, damit Sie PowerShell-Skripts einfacher lesen und bearbeiten können. Ein Link zum Installieren der PowerShell-Tools wird im Editor am oberen Rand angezeigt, sofern sie noch nicht installiert sind.

![PowerShell-Tools installieren](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796671.png)

Die JSON-Dateien verwenden ein Schema, auf das am Anfang jeder Datei verwiesen wird. Sie können das Schema herunterladen und analysieren, wenn Sie es besser verstehen möchten. Das Schema definiert, welche Elemente zulässig sind, und legt die Typen und Formate von Feldern, die möglichen Werte aufgezählter Werte usw. fest.

Wenn Sie Bereitstellungen für verschiedene Zielkonfigurationen vornehmen oder die Einstellungen häufig ändern möchten, können Sie verschiedene Exemplare der *param*-Datei erstellen. Versuchen Sie, für alle Umgebungen die gleiche Vorlage zu verwenden.

## Bereitstellen eines Azure-Ressourcengruppenprojekts in einer Azure-Ressourcengruppe

Azure-Ressourcengruppenprojekte werden in Azure-Ressourcengruppen bereitgestellt. Diese stellen lediglich eine logische Gruppierung von Ressourcen in Azure dar, wie etwa Webanwendungen, Datenbanken usw.

1. Wählen Sie im Kontextmenü des Bereitstellungsprojektknotens **Bereitstellen**, **Neue Bereitstellung** aus.

    ![Bereitstellen, Menüelement "Neue Bereitstellung"](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796672.png)

    Das Dialogfeld **In Ressourcengruppe bereitstellen** wird angezeigt.

    ![Dialogfeld "In Ressourcengruppe bereitstellen"](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796673.png)

1. Wählen Sie im Dropdownfeld **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue. Öffnen Sie zum Erstellen einer Ressourcengruppe das Dropdownfeld **Ressourcengruppe**, und wählen Sie dann **<Create New...>** aus.

    Das Dialogfeld **Ressourcengruppe erstellen** wird angezeigt.

    ![Dialogfeld "Ressourcengruppe erstellen"](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796674.png)

    >[AZURE.NOTE]Zu Beginn eines neuen Bereitstellungsprojekts ist es normalerweise sinnvoll, eine neue Ressourcengruppe zu erstellen, in der die Bereitstellung erfolgt.

1. Geben Sie einen Namen und Speicherort für die Ressourcengruppe ein, und wählen Sie dann die Schaltfläche **Erstellen** aus.

    Der Speicherort der Ressourcengruppe muss nicht mit dem Speicherort der Ressourcen identisch sein, da die Ressourcen in einer Gruppe sich über mehrere Regionen erstrecken können.

1. Wählen Sie die Vorlagenkonfiguration und die Parameterdateien aus, die Sie für diese Bereitstellung verwenden möchten, oder übernehmen Sie die Standardeinstellungen.

    Sie können die Eigenschaften einer Ressource bearbeiten, wenn Sie die Schaltfläche **Parameter bearbeiten** auswählen. Wenn zum Zeitpunkt der Bereitstellung erforderliche Parameter fehlen, wird das Dialogfeld **Parameter bearbeiten** angezeigt, sodass Sie sie angeben können. Für Parameter, denen Werte fehlen, wird im Feld **Wert** neben dem Parameter **<null>** angezeigt. Bei diesem Beispiel (einer Web-App-Ressource) umfassen die erforderlichen Parameter den Websitenamen, den Hostingplan und den Speicherort der Website. (Sie erinnern sich vielleicht, dass diese Parameter in der Parameterdatei standardmäßig auf NULL festgelegt sind.) Die anderen Parameter weisen Standardwerte auf.

    ![Dialogfeld "Parameter bearbeiten"](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796675.png)

1. Geben Sie im Dialogfeld **Parameter bearbeiten** den Websitenamen, den Speicherort der Website sowie den Namen des Hostingplans ein, und überprüfen Sie dann die Werte der anderen Eigenschaften. Klicken Sie dann auf die Schaltfläche **Speichern**.

    - Der *siteName*-Parameter ist der erste Teil der URL der Webseite. Beispielsweise ist in der URL "mywebsitename.azurewebsites.net" der Name der Website "mywebsitename".

    - Der *hostingPlanName*-Parameter gibt den Hostingplan an. In diesem Beispiel können Sie "Free" verwenden. Weitere Informationen zu Hostingplänen finden Sie unter [Hostingpläne für Azure-Websites – Ausführliche Übersicht](http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/).

    - Der *siteLocation*-Parameter bezeichnet die Azure-Region, in der die Website gehostet werden soll, wie etwa "USA, Westen". Eine Liste der verfügbaren Regionen finden Sie unter [Azure-Regionen](http://azure.microsoft.com/regions/).

1. Wählen Sie die Schaltfläche **Bereitstellen** aus, um das Projekt in Azure bereitzustellen.

    Sie können den Verlauf der Bereitstellung im Fenster **Ausgabe** sehen. Abhängig von Ihrer Konfiguration kann die Bereitstellung mehrere Minuten in Anspruch nehmen.

    >[AZURE.NOTE]Sie werden möglicherweise aufgefordert, die Microsoft Azure PowerShell-Cmdlets zu installieren. Da diese Cmdlets zum Bereitstellen von Azure-Ressourcengruppen erforderlich sind, müssen Sie sie installieren.

1. Öffnen Sie in Ihrem Browser das [Azure-Portal](https://portal.azure.com/). Da es sich um eine neue Änderung handelt, sollte auf der Registerkarte **Benachrichtigungen** eine neue Benachrichtigung verfügbar sein. Wählen Sie sie aus, um Details zur neuen Azure-Ressourcengruppe anzuzeigen. Eine Liste aller verfügbaren Ressourcengruppen wird angezeigt, wenn Sie die Registerkarte **Durchsuchen** und dort **Ressourcengruppen** auswählen.

    ![Die bereitgestellte Azure-Ressourcengruppe](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796676.png)

1. Wenn Sie Änderungen vornehmen und das Projekt erneut bereitstellen möchten, können Sie die vorhandene Ressourcengruppe direkt im Kontextmenü des Azure-Ressourcengruppenprojekts auswählen. Wählen Sie im Kontextmenü **Bereitstellen** und dann die Ressourcengruppe aus, in der Sie soeben die Bereitstellung vorgenommen haben.

    ![Bereitgestellte Azure-Ressourcengruppe](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796677.png)

    Beim Bereitstellen einer Azure-Ressourcengruppe wird nur das betreffende Projekt bereitgestellt. Wenn Ihre Projektmappe ein Codeprojekt oder beliebige weitere Projekte enthält, müssen Sie diese separat bereitstellen.

## Verwenden von Azure SDK 2.5-Cloudbereitstellungsprojekten mit Azure SDK 2.6

Wenn Sie Cloudbereitstellungsprojekte verwenden, die mit Azure SDK 2.5 erstellt wurden, ist ein Upgrade auf Azure SDK 2.6 sinnvoll, damit Sie die neuen Features für das Bearbeiten und Bereitstellen von Azure-Ressourcenvorlagen verwenden können. Die einfachste Möglichkeit, die in Azure SDK 2.5 erstellten Vorlagen wiederzuverwenden, besteht darin, Azure SDK 2.6-Versionen des Projekts zu erstellen, Ihre Vorlagen in das neue Projekt zu verschieben und einige Anpassungen vorzunehmen.

### So verwenden Sie Azure SDK 2.5-Cloudbereitstellungsprojekte mit Azure SDK 2.6

1. Fügen Sie Ihrer Projektmappe ein neues Azure SDK 2.6-Azure-Ressourcengruppenprojekt hinzu.

    1. Öffnen Sie die Bereitstellung, die Ihr Azure SDK 2.5-Cloudbereitstellungsprojekt enthält.

    1. Wählen Sie im Menü **Datei** die Befehle **Neu** und **Projekt** aus.

    1. Suchen Sie im Dialogfeld **Neues Projekt** unter **Visual C#/Cloud** oder **Visual Basic/Cloud** das Projekt **Azure-Ressourcengruppe**. 
    
         Der Vorlagenname des Projekts wurde von **Cloudbereitstellung** in "Azure-Ressourcengruppe" geändert.

    1. Geben Sie dem Projekt einen Namen.

    1. Ändern Sie das Dropdownfeld der Projektmappe in **Zu Lösung hinzufügen**.

    1. Als Nächstes werden Sie aufgefordert, eine Vorlage auszuwählen. Da Sie Ihre vorhandenen Vorlagen aus dem Azure SDK 2.5-Cloudbereitstellungsprojekt verschieben, können Sie eine beliebige Vorlage auswählen. Wählen wir also die leere Vorlage unten in der Liste aus.

    1. Klicken Sie auf die Schaltfläche **OK**.
    
        Das neue Projekt wird Ihrer Projektmappe hinzugefügt.

1. Kopieren Sie Ihre Vorlagen- und Parameterdateien aus dem Azure SDK 2.5-Cloudbereitstellungsprojekt in Ihr Azure SDK 2.6-Ressourcengruppenprojekt.

    1. Suchen Sie im Projektmappen-Explorer die Vorlagen- und Parameterdateien, die Sie in das Azure SDK 2.5-Bereitstellungsprojekt kopieren möchten, wählen Sie sie aus, und kopieren Sie sie.
    
    2. Fügen Sie die Dateien in den Ordner **Vorlagen** in Ihrem neuen Azure SDK 2.6-Ressourcengruppenprojekt ein.

1. Suchen Sie im Projektmappen-Explorer die Vorlagen- und Parameterdateien, die Sie in das Azure SDK 2.5-Bereitstellungsprojekt kopieren möchten, wählen Sie sie aus, und kopieren Sie sie.

1. Fügen Sie die Dateien in den Ordner "Vorlagen" in ihrem neuen Azure SDK 2.6-Ressourcengruppenprojekt ein.

1. Wenn Sie zusammen mit der Vorlage auch eine Webanwendung bereitstellen, müssen Sie einen Verweis vom neuen Azure SDK 2.6-Ressourcengruppenprojekt auf Ihre Webanwendung erstellen.

    1. Wählen Sie im Kontextmenü des Knotens **Verweise** Ihres neuen Azure SDK 2.6-Ressourcengruppenprojekts im Projektmappen-Explorer den Befehl **Verweis hinzufügen** aus.

    1. Aktivieren Sie das Kontrollkästchen neben der Webanwendung in der Liste der Projekte, und wählen Sie dann die Schaltfläche **OK** aus.

1. Wählen Sie im Projektmappen-Explorer im Kontextmenü des Knotens "Verweise" Ihres neuen Azure SDK 2.6-Ressourcengruppenprojekts den Befehl "Verweis hinzufügen" aus.

1. Aktivieren Sie das Kontrollkästchen neben der Webanwendung in der Liste der Projekte, und wählen Sie dann die Schaltfläche "OK" aus.

1. Benennen Sie alle Instanzen der Parameter *dropLocation* und *dropLocationSasToken* in "\_artifactsLocation" und "\_artifactsLocationSasToken" um.

1. Wenn Sie sie nicht verwenden möchten, können Sie die leeren Vorlagen- und Parameterdateien löschen, die beim Erstellen des Azure SDK 2.6-Projekts automatisch hinzugefügt wurden.

    1. Löschen Sie die Datei "DeployTemplate.json".

    1. Löschen Sie die Datei "DeploymentTemplate.param.dev.json".

1. Wenn Sie im Azure SDK 2.5-Projekt Änderungen am Skript "Publish-AzureResourceGroup.ps1" vorgenommen haben, müssen Sie diese Änderungen in das Skript "Deploy-AzureResourceGroup.ps1" im Azure SDK 2.6-Projekt verschieben.

    Sie können Ihre Vorlage jetzt mithilfe des Bereitstellungsbefehls im Azure SDK 2.6-Azure-Ressourcengruppenprojekt bereitstellen und die neuen Funktionen zum Bearbeiten von Vorlagen in Azure SDK 2.6 nutzen. Sobald das 2.6-Projekt zu Ihrer Zufriedenheit funktioniert, können Sie das Azure SDK 2.5-Projekt aus Ihrer Projektmappe entfernen.

## Warum für das Projekt eine Aktualisierung erforderlich war

Es wurde eine Reihe von Änderungen an Vorlagen für die Bereitstellung in Azure SDK 2.6 vorgenommen, durch die Azure SDK 2.5-Bereitstellungsskripts und -vorlagen inkompatibel werden. Die erste – und größte – Änderung besteht darin, dass die Bereitstellung gestartet wird. Azure SDK 2.5 enthielt kompilierten Code, der die Azure-REST-APIs zum Hochladen der Vorlage und Starten der Bereitstellung verwendete. Wir haben Feedback von vielen Entwicklern erhalten, dass sie Visual Studio gerne verwenden würden, um das im Projekt enthaltene PowerShell-Skript einfach nur zu starten. Daher startet in Azure SDK 2.6 der Bereitstellungsbefehl das im Projekt enthaltene PowerShell-Skript, um die Vorlage bereitzustellen. Dadurch können Sie die Bereitstellung anpassen und die Anpassungen immer ausführen lassen, ganz gleich, ob Sie von der Befehlszeile mit Azure PowerShell oder mithilfe von VS mit dem Bereitstellungsbefehl bereitstellen. Zum Bereitstellen aus Visual Studio müssen Sie das Azure SDK 2.6-PowerShell-Bereitstellungsskript verwenden, wenn das Azure SDK 2.6 installiert ist.

Anpassungen wurden auch an einigen Variablennamen und Buildaufgaben vorgenommen, um sie besser mit den Benennungskonventionen für automatische TFS-Builds und andere Microsoft-interne Projekte kompatibel zu machen. Der Code in Visual Studio, der die erforderlichen Variablen und Werte zum Starten des PowerShell-Skripts sammelt, sucht nach diesen neuen Namen.

## Nächste Schritte

Informationen zum Hinzufügen von Ressourcen zu Ihrer Azure-Ressourcengruppe in Visual Studio finden Sie unter [Hinzufügen von Ressourcen zu einer Azure-Ressourcengruppe](https://msdn.microsoft.com/library/azure/mt125415.aspx).

<!---HONumber=Sept15_HO2-->