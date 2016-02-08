<properties
	pageTitle="Bereitstellen von Vorlagen für VM-Skalierungsgruppen in Visual Studio | Microsoft Azure"
	description="Bereitstellen einer VM-Skalierungsgruppe über eine Visual Studio-Ressourcengruppenbereitstellung."
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="guybo"/>

# Bereitstellen von Vorlagen für VM-Skalierungsgruppen in Visual Studio
[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-windows-powershell-service-manager.md).

In diesem Artikel erfahren Sie, wie eine Azure-VM-Skalierungsgruppe über eine Visual Studio-Ressourcengruppenbereitstellung bereitgestellt wird.


[Azure-VM-Skalierungsgruppen](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) sind eine Azure Compute-Ressource zur Bereitstellung und Verwaltung einer Auflistung ähnlicher virtueller Computer mit einfach integrierten Optionen für automatische Skalierung und Lastenausgleich. Sie können VM-Skalierungsgruppen über [ARM-Vorlagen (Azure-Ressourcen-Manager)](https://github.com/Azure/azure-quickstart-templates) bereitstellen. ARM-Vorlagen können mithilfe der Azure-Befehlszeilenschnittstelle, mit PowerShell, REST und auch direkt über Visual Studio bereitgestellt werden. Visual Studio bietet eine Reihe von Beispielvorlagen, die als Teil eines Azure-Projekts zur Ressourcengruppenbereitstellung bereitgestellt werden können.

Azure-Ressourcengruppenbereitstellungen bieten eine Möglichkeit, mehrere zusammengehörige Azure-Ressourcen in einem einzelnen Bereitstellungsvorgang zusammenzufassen und zu veröffentlichen. Informationen dazu finden Sie hier: [Erstellen und Bereitstellen von Azure-Ressourcengruppen mit Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy/)

## Voraussetzungen

Um mit der Bereitstellung von VM-Skalierungsgruppen in Visual Studio zu beginnen, benötigen Sie Folgendes:

- Visual Studio 2013 oder 2015
- Azure SDK 2.7 oder 2.8

Hinweis: In diesen Anweisungen wird davon ausgegangen, dass Sie Visual Studio 2015 mit [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) verwenden.

## Erstellen eines Projekts

1. Erstellen Sie ein neues Projekt in Visual Studio 2015 durch Auswählen von **Datei | Neu | Projekt**.

	![Datei - Neu][file_new]

2. Wählen Sie unter **Visual C# | Cloud** die Option **Azure-Ressourcen-Manager** aus, um ein Projekt zur Bereitstellung einer ARM-Vorlage zu erstellen.

	![Projekt erstellen][create_project]

3.  Wählen Sie aus der Liste der Vorlagen die VM-Skalierungsgruppenvorlage für Linux oder Windows aus.

	![Vorlage auswählen][select_Template]

4. Nachdem das Projekt erstellt wurde, werden PowerShell-Bereitstellungsskripts, eine Azure-Ressourcen-Manager-Vorlage und eine Parameterdatei für die VM-Skalierungsgruppe angezeigt.

	![Projektmappen-Explorer][solution_explorer]

## Anpassen Ihres Projekts

Jetzt können Sie die Vorlage an die Anforderungen Ihrer Anwendung anpassen, beispielsweise VM-Erweiterungseigenschaften hinzufügen oder Lastenausgleichsregeln bearbeiten. Die VM-Skalierungsgruppenvorlagen sind standardmäßig so konfiguriert, dass die AzureDiagnostics-Erweiterung bereitgestellt wird. Auf diese Weise können ganz einfach Regeln zur automatischen Skalierung hinzugefügt werden. Zudem wird ein Lastenausgleichsmodul mit einer öffentlichen IP-Adresse bereitgestellt, das mit eingehenden NAT-Regeln konfiguriert ist, die Ihnen eine Verbindung der VM-Instanzen mit SSH (Linux) oder RDP (Windows) erlauben. Der Front-End-Portbereich beginnt bei 50000, wodurch Sie im Fall von Linux bei einer Verbindung per SSH mit Port 50000 der öffentlichen IP-Adresse (oder des Domänennamens) an Port 22 des ersten virtuellen Computers in der Skalierungsgruppe weitergeleitet werden. Durch Herstellen einer Verbindung mit Port 50001 werden Sie an Port 22 des zweiten virtuellen Computers weitergeleitet usw.

 Eine gute Möglichkeit zum Bearbeiten Ihrer Vorlagen mit Visual Studio besteht darin, die JSON-Gliederung zum Organisieren der Parameter, Variablen und Ressourcen einzusetzen. Mit Kenntnis des Schemas kann Visual Studio Fehler in der Vorlage aufzeigen, bevor Sie sie bereitstellen.

![JSON-Explorer][json_explorer]

## Bereitstellen des Projekts

6. Stellen Sie die ARM-Vorlage in Azure bereit, um die VM-Skalierungsgruppenressource zu erstellen. Klicken Sie mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Bereitstellen | Neue Bereitstellung** aus.

	![Vorlage bereitstellen][5deploy_Template]

7. Wählen Sie Ihr Abonnement im Dialogfeld „Für Ressourcengruppe bereitstellen“.

	![Vorlage bereitstellen][6deploy_Template]

8. Von hier aus können Sie auch eine neue Azure-Ressourcengruppe zum Bereitstellen Ihrer Vorlage erstellen.

	![Neue Ressourcengruppe][new_resource]

9. Wählen Sie als Nächstes die Schaltfläche **Parameter bearbeiten**, um Parameter einzugeben, die an die Vorlage übergeben werden. Bestimmte Werte, wie der Benutzername und das Kennwort für das Betriebssystem, sind zum Erstellen der Bereitstellung erforderlich.

	![Parameter bearbeiten][edit_parameters]

10. Klicken Sie jetzt auf **Bereitstellen**. Das Fenster **Ausgabe** zeigt den Fortschritt der Bereitstellung. Beachten Sie, dass bei der Aktion das Skript **Deploy-AzureResourceGroup.ps1** ausgeführt wird.

	![Ausgabefenster][output_window]

## Untersuchen der VM-Skalierungsgruppe

Nachdem die Bereitstellung abgeschlossen ist, sehen Sie die neue VM-Skalierungsgruppe im **Cloud-Explorer** von Visual Studio (aktualisieren Sie die Liste). Mit dem Cloud-Explorer können Sie Azure-Ressourcen in Visual Studio verwalten und gleichzeitig Anwendungen entwickeln. Sie können außerdem Ihre VM-Skalierungsgruppe im Azure-Portal und im Azure-Ressourcen-Explorer anzeigen.

![Cloud-Explorer][cloud_explorer]

 Das Portal bietet die beste Möglichkeit, Ihre Azure-Infrastruktur mit einem Webbrowser visuell zu verwalten, während der Azure-Ressourcen-Explorer eine einfache Möglichkeit zum Untersuchen und Debuggen von Azure-Ressourcen bietet. Er bietet Einblicke in die „Instanzsicht“ und führt PowerShell-Befehle für die Ressourcen auf, die Sie gerade anzeigen. Während sich VM-Skalierungsgruppen in der Vorschau befinden, zeigt der Ressourcen-Explorer die meisten Details für Ihre VM-Skalierungsgruppen an.

## Nächste Schritte

Nachdem Sie VM-Skalierungsgruppen erfolgreich über Visual Studio bereitgestellt haben, können Sie Ihr Projekt den Anforderungen der Anwendung entsprechend weiter anpassen. Beispielsweise können Sie die automatische Skalierung einrichten, indem Sie eine Insights-Ressource hinzufügen, Ihrer Vorlage eine Infrastruktur wie z. B. eigenständige VMs hinzufügen oder Anwendungen über die benutzerdefinierte Skripterweiterung bereitstellen. Eine gute Quelle für Beispielvorlagen ist das GitHub-Repository für [Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates) (suchen Sie nach „vmss“).

[file_new]: ./media/virtual-machines-vmss-vstemplates/1-FileNew.png
[create_project]: ./media/virtual-machines-vmss-vstemplates/2-CreateProject.png
[select_Template]: ./media/virtual-machines-vmss-vstemplates/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machines-vmss-vstemplates/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machines-vmss-vstemplates/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machines-vmss-vstemplates/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machines-vmss-vstemplates/6-DeployTemplate.png
[new_resource]: ./media/virtual-machines-vmss-vstemplates/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machines-vmss-vstemplates/8-EditParameter.png
[output_window]: ./media/virtual-machines-vmss-vstemplates/9-Output.png
[cloud_explorer]: ./media/virtual-machines-vmss-vstemplates/12-CloudExplorer.png

<!---HONumber=AcomDC_0128_2016-->