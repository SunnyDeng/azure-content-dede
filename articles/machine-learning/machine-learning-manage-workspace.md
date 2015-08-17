<properties 
	pageTitle="Verwalten eines Machine Learning-Arbeitsbereichs | Microsoft Azure" 
	description="Verwalten des Zugriffs auf Azure Machine Learning-Arbeitsbereiche sowie Bereitstellen und Verwalten von ML-API-Webdiensten" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="garye"/>


# Verwalten eines Azure Machine Learning-Arbeitsbereichs 
Mit dem Azure-Verwaltungsportal können Sie Ihre Arbeitsbereiche für maschinelles Lernen wie folgt verwalten:

- Kontrollieren der Nutzung des Arbeitsbereichs
- Konfigurieren des Arbeitsbereichs zum Zulassen oder Verweigern des Zugriffs
- Verwalten von Webdiensten, die im Arbeitsbereich erstellt wurden
- Löschen des Arbeitsbereichs

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

Außerdem finden Sie auf der Dashboard-Registerkarte eine Übersicht über die Nutzung Ihres Arbeitsbereichs und eine Schnellübersicht über Informationen zum Arbeitsbereich.

> [AZURE.TIP]In Azure Machine Learning Studio können Sie auf der Registerkarte **WEBDIENSTE** Machine-Learning-Webdienste hinzufügen, aktualisieren oder löschen.

So verwalten Sie einen Arbeitsbereich:

1.	Melden Sie sich bei Ihrem Microsoft Azure-Konto an – verwenden Sie hierzu das Konto, das dem Azure-Abonnement zugeordnet ist.
2.	Klicken Sie im [Azure Verwaltungsportal](https://manage.windowsazure.com/) im Bereich der Microsoft Azure-Dienste auf **MACHINE LEARNING**.
3.	Klicken Sie auf den Arbeitsbereich, den Sie verwalten möchten.

Die Workspace-Seite enthält drei Registerkarten:

- **DASHBOARD** – ermöglicht das Anzeigen der Verwendung des Arbeitsbereichs und von Informationen
- **KONFIGURIEREN** – ermöglicht es, den Zugriff auf den Arbeitsbereich zu verwalten
- **WEBDIENSTE** – ermöglicht die VerwaItung von Webdiensten, die in diesem Arbeitsbereich veröffentlicht wurden

  
## So kontrollieren Sie die Nutzung des Arbeitsbereichs

Klicken Sie auf die Registerkarte **DASHBOARD**.

Im Dashboard können Sie die Gesamtnutzung des Arbeitsbereichs ablesen und sich eine schnelle Übersicht über die Informationen des Arbeitsbereichs verschaffen.

- Das Diagramm **SERVER** zeigt die Computerressourcen an, die im Arbeitsbereich verwendet werden. Sie können die Ansicht ändern, um relative oder absolute Werte anzuzeigen, und den im Diagramm angezeigten Zeitrahmen ändern.
- **Verwendungsübersicht** zeigt den vom Arbeitsbereich genutzten Azure-Speicher an.
- **Auf einen Blick** enthält eine Zusammenfassung der Informationen des Arbeitsbereichs und nützliche Links.

> [AZURE.NOTE]Über den Link **Bei ML Studio anmelden** wird Machine Learning Studio mit dem Microsoft-Konto geöffnet, an dem Sie gerade angemeldet sind. Das Microsoft-Konto, mit dem Sie sich am Azure-Portal angemeldet haben, um einen Arbeitsbereich zu erstellen, verfügt nicht automatisch über die Berechtigung zum Öffnen dieses Arbeitsbereichs. Um einen Arbeitsbereich zu öffnen, müssen Sie an dem Microsoft-Konto angemeldet sein, das als Besitzer des Arbeitsbereichs definiert wurde, oder Sie müssen eine Einladung des Besitzers zur Teilnahme am Arbeitsbereich erhalten haben.


## So erteilen oder blockieren Sie den Zugriff für Benutzer ##

Klicken Sie auf die Registerkarte **KONFIGURIEREN**.

Auf der Registerkarte "Konfiguration" haben Sie folgende Möglichkeiten:
 
- Blockieren des Zugriffs auf den Machine Learning-Arbeitsbereich, indem Sie auf "DENY" klicken. Benutzer können den Arbeitsbereich dann nicht mehr in Machine Learning Studio öffnen. Klicken Sie auf "ALLOW", um den Zugang wiederherzustellen.
- Ändern des Besitzers des Arbeitsbereichs, indem Sie ein anderes Microsoft-Konto angeben. 

Um zu verwalten, wer Zugriff auf den Arbeitsbereich in Machine Learning Studio hat, klicken Sie auf der Registerkarte **DASHBOARD** auf**Bei ML Studio anmelden** (siehe obigen Hinweis zu **ML Studio anmelden**). Dadurch wird der Arbeitsbereich in Machine Learning Studio geöffnet. Klicken Sie dort auf die Registerkarte **EINSTELLUNGEN** und dann auf **BENUTZER**. Sie können auf **INVITE MORE USERS** klicken, um Benutzern den Zugriff auf den Arbeitsbereich zu gewähren, oder einen Benutzer auswählen und durch Klicken auf **ENTFERNEN** entfernen.


## So verwalten Sie Webdienste in diesem Arbeitsbereich

Klicken Sie auf die Registerkarte **WEBDIENSTE**.

Hierdurch wird eine Liste von Webdiensten angezeigt, die von diesem Arbeitsbereich veröffentlicht wurden. Um einen Webdienst zu verwalten, klicken Sie auf den Namen in der Liste, um die Webdienstseite zu öffnen.

Ein Webdienst kann einen oder mehrere definierte Endpunkte haben.

- Sie können zusätzlich zum Endpunkt "Standard" weitere Endpunkte definieren. Klicken Sie am unteren Seitenrand auf **ENDPUNKT HINZUFÜGEN**, um den Endpunkt hinzuzufügen.

- Klicken Sie an einer beliebigen Stelle außerhalb des Namens auf die Endpunktzeile, und klicken Sie dann am unteren Seitenrand auf **ENDPUNKT LÖSCHEN**. Dadurch wird der Endpunkt aus dem Webdienst entfernt.
 
    > [AZURE.NOTE]Wenn eine Anwendung den Webdienst-Endpunkt gerade verwendet, wenn dieser gelöscht wird, erhält die Anwendung beim nächsten Zugriff auf den Dienst eine Fehlermeldung.

Klicken Sie auf den Namen eines Webdienst-Endpunkts, um diesen zu öffnen. Das Nutzungsdiagramm zeigt die vom Webdienst-Endpunkt verwendeten Computer- und Vorhersage-Ressourcen. Sie können die Ansicht ändern, um relative oder absolute Werte anzuzeigen, und den im Diagramm angezeigten Zeitrahmen ändern.

Diese Seite bietet Ihnen auch die Informationen, die Sie benötigen, um mithilfe der REST-API für Webdienste auf den Endpunkt zugreifen zu können. Weitere Informationen finden Sie unter [Verarbeiten eines Azure Machine Learning-Webdiensts][consume].

Sie können den Webdienst über diese Seite auch auf dem Azure Data Marketplace veröffentlichen. Weitere Informationen finden Sie unter [Veröffentlichen Ihres Azure Machine Learning-Webdiensts im Azure Marketplace][marketplace].

[consume]: machine-learning-consume-web-services.md
[marketplace]: machine-learning-publish-web-service-to-azure-marketplace.md

 

<!---HONumber=August15_HO6-->