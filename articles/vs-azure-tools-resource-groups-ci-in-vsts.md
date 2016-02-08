<properties
	pageTitle="Continuous Integration in VS Team Services mit Azure-Ressourcengruppenprojekten | Microsoft Azure"
	description="Beschreibt die Einrichtung von Continuous Integration in Visual Studio Team Services durch den Einsatz von Azure-Ressourcengruppen-Bereitstellungsprojekten in Visual Studio."
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="01/26/2016"
	ms.author="tarcher" />

# Continuous Integration in Visual Studio Team Services mit Azure-Ressourcengruppen-Bereitstellungsprojekten

Zum Bereitstellen einer Azure-Vorlage müssen Sie Aufgaben ausführen, um die verschiedenen Phasen zu durchlaufen: „Erstellen“, „Testen“, „Nach Azure kopieren“ (auch als „Staging“ bezeichnet) und „Vorlage bereitstellen“. Hierfür gibt es in Visual Studio Team Services (VS Team Services) zwei Möglichkeiten. Da beide Verfahren zum gleichen Ergebnis führen, können Sie das Verfahren wählen, das am besten in Ihren Workflow passt.

-	Fügen Sie Ihrer Builddefinition einen Schritt hinzu, mit dem das PowerShell-Skript ausgeführt wird, das im Azure-Ressourcengruppen-Bereitstellungsprojekt (Deploy-AzureResourceGroup.ps1) enthalten ist. Mit dem Skript werden Artefakte kopiert, und anschließend wird die Vorlage bereitgestellt.
-	Fügen Sie mehrere VS Team Services-Buildschritte hinzu, mit denen jeweils eine Stagingaufgabe ausgeführt wird.

Dieser Artikel veranschaulicht, wie Sie die erste Option verwenden (Nutzung einer Builddefinition zum Ausführen des PowerShell-Skripts). Ein Vorteil dieser Option ist, dass das von Entwicklern in Visual Studio verwendete Skript dem Skript entspricht, das von VS Team Services verwendet wird. Bei diesem Verfahren wird vorausgesetzt, dass Sie bereits ein Visual Studio-Bereitstellungsprojekt in VS Team Services eingecheckt haben.

## Kopieren von Artefakten nach Azure 

Unabhängig vom Szenario gilt Folgendes: Falls Sie über Artefakte verfügen, die für die Vorlagenbereitstellung benötigt werden, müssen Sie dem Azure-Ressourcen-Manager Zugriff darauf gewähren. Diese Artefakte können folgende Dateien umfassen:

-	Geschachtelte Vorlagen
-	Konfigurationsskripts und DSC-Skripts
-	Anwendungsbinärdateien

### Geschachtelte Vorlagen und Konfigurationsskripts
Wenn Sie von Visual Studio bereitgestellte (oder mit Visual Studio-Codeausschnitten erstellte) Vorlagen verwenden, werden die Artefakte vom PowerShell-Skript nicht nur bereitgestellt. Es wird außerdem der URI für die Ressourcen für unterschiedliche Bereitstellungen parametrisiert. Anschließend kopiert das Skript die Artefakte in einen sicheren Container in Azure, erstellt ein SAS-Token für diesen Container und übergibt diese Informationen dann an die Vorlagenbereitstellung. Weitere Informationen zu geschachtelten Vorlagen finden Sie unter [Erstellen einer Vorlagenbereitstellung](https://msdn.microsoft.com/library/azure/dn790564.aspx).

## Einrichten von Continuous Deployment in VS Team Services

Zum Aufrufen des PowerShell-Skripts in VS Team Services müssen Sie Ihre Builddefinition aktualisieren. Die Schritte sind hier in Kurzform aufgeführt:

1.	Bearbeiten Sie die Builddefinition.
1.	Richten Sie die Azure-Autorisierung in VS Team Services ein.
1.	Fügen Sie einen Azure PowerShell-Buildschritt hinzu, bei dem auf das PowerShell-Skript im Azure-Ressourcengruppen-Bereitstellungsprojekt verwiesen wird.
1.	Legen Sie den Wert des Parameters *-ArtifactsStagingDirectory* fest, um mit einem Projekt zu arbeiten, das in VS Team Services erstellt wurde.

### Ausführliche exemplarische Vorgehensweise

Hier werden Sie durch die erforderlichen Schritte der Continuous Deployment-Konfiguration in VS Team Services geführt.

1.	Bearbeiten Sie die VS Team Services-Builddefinition, und fügen Sie einen Azure PowerShell-Buildschritt hinzu. Wählen Sie die Builddefinition in der Kategorie **Builddefinitionen** aus, und wählen Sie dann den Link **Bearbeiten**.

    ![][0]

1.	Fügen Sie der Builddefinition einen neuen **Azure PowerShell**-Buildschritt hinzu, und wählen Sie dann die Schaltfläche **Buildschritt hinzufügen…**.

    ![][1]

1.	Wählen Sie die Kategorie **Bereitstellungsaufgabe** und dann die Aufgabe **Azure PowerShell**, und klicken Sie auf die Schaltfläche **Hinzufügen**.

    ![][2]

1.	Wählen Sie den Buildschritt **Azure PowerShell**, und geben Sie die Werte ein.

    1.	Falls Sie bereits über einen Azure-Dienstendpunkt verfügen, der VS Team Services hinzugefügt wurde, wählen Sie das Abonnement im Dropdown-Listenfeld **Azure-Abonnement** aus, und springen Sie dann zum nächsten Abschnitt. 

        Wenn bei Ihnen kein Azure-Dienstendpunkt in VS Team Services verfügbar ist, müssen Sie einen hinzufügen. In diesem Unterabschnitt werden Sie durch das erforderliche Verfahren geführt. Wenn für Ihr Azure-Konto ein Microsoft-Konto (z. B. Hotmail) verwendet wird, müssen Sie die folgenden Schritte ausführen, um eine Authentifizierung des Dienstprinzipals zu erhalten.

    1.	Wählen Sie den Link **Verwalten** neben dem Dropdown-Listenfeld **Azure-Abonnement**.

        ![][3]

    1. Wählen Sie im Dropdown-Listenfeld **Neuer Dienstendpunkt** die Option **Azure**.

        ![][4]

    1.	Wählen Sie im Dialogfeld **Azure-Abonnement hinzufügen** die Option **Dienstprinzipal**.

        ![][5]

    1.	Geben Sie Ihre Informationen zum Azure-Abonnement im Dialogfeld **Azure-Abonnement hinzufügen** an. Sie müssen Folgendes angeben:
        -	Abonnement-ID
        -	Abonnementname
        -	Dienstprinzipal-ID
        -	Dienstprinzipalschlüssel
        -	Mandanten-ID

    1.	Fügen Sie im Namensfeld **Abonnement** einen Namen Ihrer Wahl hinzu. Dieser Wert wird später in der Dropdownliste **Azure-Abonnement** in VS Team Services angezeigt.

    1.	Wenn Sie die ID Ihres Azure-Abonnements nicht kennen, können Sie diese mit einem der folgenden Befehle ermitteln.
        
        Verwenden Sie für PowerShell-Skripts den folgenden Befehl:

        `Get-AzureRmSubscription`

        Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

        `azure account show`
    

    1.	Verwenden Sie zum Ermitteln von Dienstprinzipal-ID, Dienstprinzipalschlüssel und Mandanten-ID die Vorgehensweise unter [Erstellen einer Active Directory-Anwendung und eines Dienstprinzipals mithilfe des Portals](resource-group-create-service-principal-portal.md) oder [Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager](resource-group-authenticate-service-principal.md).

    1.	Geben Sie die Werte für „Dienstprinzipal-ID“, „Dienstprinzipalschlüssel“ und „Mandanten-ID“ im Dialogfeld **Azure-Abonnement hinzufügen** an, und wählen Sie dann die Schaltfläche **OK**.

        Sie verfügen nun über einen gültigen Dienstprinzipal, den Sie zum Ausführen des Azure PowerShell-Skripts verwenden können.

1.	Bearbeiten Sie die Builddefinition, und wählen Sie den Buildschritt **Azure PowerShell**. Wählen Sie das Abonnement im Dropdown-Listenfeld **Azure-Abonnement** aus. (Falls das Abonnement nicht angezeigt wird, wählen Sie die Schaltfläche **Aktualisieren** neben dem Link **Verwalten**.)

    ![][8]

1.	Geben Sie einen Pfad zum PowerShell-Skript „Deploy-AzureResourceGroup.ps1“ an. Wählen Sie hierfür die Schaltfläche mit den Auslassungspunkten (...) neben dem Feld **Skriptpfad**, und navigieren Sie zum PowerShell-Skript „Deploy-AzureResourceGroup.ps1“ im Ordner **Skripts** Ihres Projekts. Wählen Sie ihn aus, und klicken Sie dann auf die Schaltfläche **OK**.

    ![][9]

1. Aktualisieren Sie nach dem Auswählen des Skripts den Pfad zum Skript so, dass es über „Build.StagingDirectory“ ausgeführt wird (dasselbe Verzeichnis, auf das *ArtifactsLocation* festgelegt ist). Dies erreichen Sie, indem Sie am Anfang des Skriptpfads „$(Build.StagingDirectory)/“ hinzufügen.

    ![][10]

1.	Geben Sie im Feld **Skriptargumente** die folgenden Parameter ein (in einer Zeile). Wenn Sie das Skript in Visual Studio ausführen, sehen Sie, wie VS die Parameter im **Ausgabefenster** verwendet. Sie können dies als Ausgangspunkt zum Festlegen der Parameterwerte im Buildschritt verwenden.

    | Parameter | Beschreibung|
    |---|---|
    | -ResourceGroupLocation | Der Wert für den geografischen Standort, an dem sich die Ressourcengruppe befindet, z. B. **eastus** oder **'USA, Osten'**. (Verwenden Sie einfache Anführungszeichen oben, wenn der Name ein Leerzeichen enthält.) Weitere Informationen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/de-DE/regions/).| |
    | -ResourceGroupName | Der Name der Ressourcengruppe, die für diese Bereitstellung verwendet wird.| |
    | -UploadArtifacts | Mit diesem Parameter wird (falls vorhanden) angegeben, dass Artefakte aus dem lokalen System in Azure hochgeladen werden müssen. Sie müssen diesen Switch nur festlegen, falls für Ihre Vorlagenbereitstellung zusätzliche Artefakte erforderlich sind, die Sie mit dem PowerShell-Skript bereitstellen möchten (z. B. Konfigurationsskripts oder geschachtelte Vorlagen). |
    | -StorageAccountName | Der Name des Speicherkontos, das zum Bereitstellen von Artefakten für diese Bereitstellung verwendet wird. Dieser Parameter ist nur erforderlich, wenn Sie Artefakte nach Azure kopieren. Dieses Speicherkonto wird durch die Bereitstellung nicht automatisch erstellt, sondern muss bereits vorhanden sein.| |
    | -StorageAccountResourceGroupName | Der Name der Ressourcengruppe, die dem Speicherkonto zugeordnet ist. Dieser Parameter ist nur erforderlich, wenn Sie Artefakte nach Azure kopieren.| |
    | -TemplateFile | Der Pfad zur Vorlagendatei im Azure-Ressourcengruppen-Bereitstellungsprojekt. Zur Steigerung der Flexibilität verwenden Sie für diesen Parameter anstelle eines absoluten Pfads einen Pfad, der relativ zum Speicherort des PowerShell-Skripts ist.|
    | -TemplateParametersFile | Gibt den Pfad zur Parameterdatei im Azure-Ressourcengruppen-Bereitstellungsprojekt an. Zur Steigerung der Flexibilität verwenden Sie für diesen Parameter anstelle eines absoluten Pfads einen Pfad, der relativ zum Speicherort des PowerShell-Skripts ist.|
    | -ArtifactStagingDirectory | Mit diesem Parameter können Sie dem PowerShell-Skript den Ordner mitteilen, aus dem die Binärdateien des Projekts kopiert werden sollen. Dieser Wert überschreibt den Standardwert, der vom PowerShell-Skript verwendet wird. Legen Sie den Wert bei Verwendung von VS Team Services wie folgt fest: -ArtifactStagingDirectory $(Build.StagingDirectory) |

    Hier ist ein Beispiel für Skriptargumente angegeben (Zeile wurde zu besseren Lesbarkeit umgebrochen):

    ```	
    -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
    -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
    –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'	
    ```

    Wenn Sie fertig sind, sollte das Feld **Skriptargumente** etwa wie unten angegeben aussehen.

    ![][11]

1.	Nachdem Sie alle erforderlichen Komponenten für den Azure PowerShell-Buildschritt hinzugefügt haben, wählen Sie die Schaltfläche **Build zur Warteschlange hinzufügen**, um das Projekt zu erstellen. Im **Buildfenster** wird die Ausgabe des PowerShell-Skripts angezeigt.

## Nächste Schritte

Weitere Informationen zum Azure-Ressourcen-Manager und Azure-Ressourcengruppen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md).


[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png

<!---HONumber=AcomDC_0128_2016-->