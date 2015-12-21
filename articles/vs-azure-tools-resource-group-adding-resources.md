<properties 
   pageTitle="Bearbeiten einer Ressourcen-Manager-Vorlage mit Visual Studio | Microsoft Azure"
   description="Erfahren Sie, wie Ressourcen mithilfe von Visual Studio einer Azure-Ressourcen-Manager-Vorlage hinzugefügt werden."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="11/13/2015"
   ms.author="kempb" />

# Bearbeiten von Ressourcen-Manager-Vorlagen mit Visual Studio

Mit Visual Studio können Sie die Ressourcen-Manager-Vorlage für die Ressourcengruppe bearbeiten. Sie können entweder über das Fenster „JSON-Gliederung“ von Visual Studio oder direkt an der Vorlagensyntax Änderungen an der Vorlage vornehmen.

## Hinzufügen von Ressourcen zu einer Ressourcengruppe über das Fenster „JSON-Gliederung“

### So fügen Sie einer Ressourcengruppe Ressourcen hinzu

1. Wählen Sie im Projektmappen-Explorer die JSON-Datei für die Azure-Ressourcengruppe aus. Die JSON-Datei wird im Editor angezeigt, und außerdem wird das Fenster **JSON-Gliederung** neben dem Editor angezeigt. Wenn Sie das Fenster "JSON-Gliederung" schließen, wird es nicht mehr automatisch geöffnet, bis Sie den Befehl "Gliederung anzeigen" im Kontextmenü einer JSON-Vorlagendatei (keiner Parameterdatei) auswählen.

    ![JSON-Datei für Azure-Ressourcengruppe](./media/vs-azure-tools-resource-group-adding-resources/arm-json-file.png)

1. Wählen Sie im Fenster **JSON-Gliederung** den Knoten **Ressourcen** und dann entweder den Befehl **Neue Ressource hinzufügen** im Kontextmenü oder die Schaltfläche **Ressource hinzufügen** oben im Fenster "JSON-Gliederung" aus.

    ![Hinzufügen einer neuen Ressource zu einer Ressourcengruppe](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource.png)

1. In der Liste der Ressourcen im Dialogfeld **Ressource hinzufügen** wählen Sie die Ressource aus, die Sie hinzufügen möchten. Geben Sie die für die Ressource erforderlichen Informationen ein, und wählen Sie dann die Schaltfläche **Hinzufügen** aus. Wenn Sie z. B. ein Speicherkonto hinzufügen, müssen Sie einen Basisnamen für die Parameter angeben, die in Ihrem Auftrag erstellt werden.
 
    ![Dialogfeld "Ressource hinzufügen"](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-dialog.png)

    Die Ressource wird der Ressourcenliste im Fenster **JSON-Gliederung** hinzugefügt, und der neue JSON-Code, der die Ressourcen darstellt, wird in der Datei angezeigt. Außerdem können zugehörige Parameter und/oder Variablen hinzugefügt werden.


    ![JSON-Datei hinzugefügte Ressource](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-json.png)

1. Stellen Sie die neue Ressource in der Azure-Ressourcengruppe bereit. Wählen Sie im Kontextmenü des Ressourcengruppenprojekts im Projektmappen-Explorer den Befehl **Bereitstellen** und dann den Namen der Ressourcengruppe aus.

    ![Bereitgestellte Azure-Ressourcengruppe](./media/vs-azure-tools-resource-group-adding-resources/deploy-arm-resource-group.png)

    Das Dialogfeld **In Ressourcengruppe bereitstellen** wird angezeigt.


1. Wählen Sie die Schaltfläche **Bereitstellen** aus.

1. Wenn von Ihnen Parameter angegeben werden müssen, wird das Dialogfeld **Parameter bearbeiten** angezeigt. Geben Sie die erforderlichen Werte ein, und wählen Sie dann die Schaltfläche **Speichern**. Die neue Ressource wird in der Azure-Ressourcengruppe bereitgestellt.

## Bearbeiten der Vorlagensyntax

Mit Visual Studio können Sie die Vorlage auch direkt bearbeiten. Wenn Sie mit der Bearbeitung von Werten in der Vorlage beginnen, erhalten Sie im Editor Hilfe zu den möglichen Werte, die Sie angeben können.

![Bearbeiten der Vorlage](./media/vs-azure-tools-resource-group-adding-resources/arm-edit-template.png)

Weitere Informationen zur Struktur der Vorlage finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md).

## Weitere Informationen

[Erstellen und Bereitstellen von Azure-Ressourcengruppen mit Visual Studio](vs-azure-tools-resource-groups-deployment-project-create-deploy.md)

[Azure-Ressourcen-Manager-Cmdlets](https://msdn.microsoft.com/library/azure/dn757692.aspx)

[Verwenden von Windows PowerShell mit Azure-Ressourcen-Manager](../powershell-azure-resource-manager/)

[Channel 9-Video: Azure-Ressourcen-Manager](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B224#fbid=)

<!---HONumber=AcomDC_1210_2015-->