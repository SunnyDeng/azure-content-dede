<properties 
	pageTitle="Runbook und Modulkataloge für Azure Automation"
	description="Ihnen stehen Runbooks und Module von Microsoft und der Community zur Verfügung, die Sie in Ihrer Azure Automation-Umgebung installieren und verwenden können. Dieser Artikel beschreibt, wie Sie auf diese Ressourcen zugreifen und Ihre Runbooks im Katalog bereitstellen können."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/23/2015"
	ms.author="bwren" />


# Runbook und Modulkataloge für Azure Automation

Statt eigene Runbooks und Module in Azure Automation zu erstellen, können Sie eine Vielzahl von Lösungen nutzen, die bereits von Microsoft und der Community entwickelt wurden. Sie können diese Lösungen ohne Anpassung verwenden oder als Ausgangspunkt nutzen und für Ihre spezifischen Anforderungen bearbeiten.

Sie können Runbooks aus dem [Runbook-Katalog](#runbooks-in-runbook-gallery) und Module aus dem [PowerShell-Katalog](#modules-in-powerShell-gallery) abrufen. Sie können auch etwas zur Community beitragen, indem Sie von Ihnen entwickelte Lösungen zur Verfügung stellen.

## Runbooks im Runbook-Katalog

Der [Runbook-Katalog](http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=RootCategory&f[0].Value=WindowsAzure&f[1].Type=SubCategory&f[1].Value=WindowsAzure_automation&f[1].Text=Automation) bietet zahlreiche Runbooks von Microsoft und der Community, die Sie in Azure Automation importieren können. Sie können entweder ein Runbook aus dem Katalog im [TechNet Script Center](http://gallery.technet.microsoft.com/) herunterladen oder Runbooks direkt aus dem Katalog über das Azure-Portal und das Azure-Vorschauportal importieren.

Das direkte Importieren aus dem Runbook-Katalog ist nur über das Azure-Portal oder das Azure-Vorschauportal möglich. Diese Funktion kann nicht mithilfe von Windows PowerShell ausgeführt werden.

>[AZURE.NOTE]Überprüfen Sie unbedingt den Inhalt der aus dem Runbook-Katalog heruntergeladenen Runbooks, und seien Sie äußerst vorsichtig, wenn Sie sie in einer Produktionsumgebung installieren und ausführen.|

### So importieren Sie mit dem Azure-Portal ein Runbook aus dem Runbook-Katalog

1. Klicken Sie im Azure-Verwaltungsportal auf **Neu**, **App Services**, **Automation**, **Runbook**, **Aus Katalog**.
2. Wählen Sie eine Kategorie aus, um verwandte Runbooks anzuzeigen, und wählen Sie ein Runbook aus, um seine Details anzuzeigen. Klicken Sie zum Auswählen des gewünschten Runbooks auf die Schaltfläche mit dem Pfeil nach rechts.<br> ![Runbook-Katalog](media/automation-runbook-gallery/runbook-gallery.png)
3. Überprüfen Sie den Inhalt des Runbooks, und beachten Sie alle Anforderungen in der Beschreibung. Klicken Sie anschließend auf die Schaltfläche mit dem Pfeil nach rechts.
4. Überprüfen Sie die Details des Runbooks, und klicken Sie anschließend auf das Häkchen. Der Runbook-Name ist bereits ausgefüllt.
5. Das Runbook wird auf der Registerkarte **Runbooks** des Automation-Kontos angezeigt.

### So importieren Sie mit dem Azure-Vorschauportal ein Runbook aus dem Runbook-Katalog

1. Öffnen Sie im Azure-Vorschauportal Ihr Automation-Konto. 
2. Klicken Sie auf die Kachel **Runbooks**, um die Liste mit den Runbooks zu öffnen.
3. Klicken Sie auf die Schaltfläche **Katalog durchsuchen**. <br> ![Schaltfläche „Katalog durchsuchen“](media/automation-runbook-gallery/browse-gallery-button.png)
4. Suchen Sie den gewünschten Katalogartikel, und wählen Sie ihn zum Anzeigen der Details aus. <br> ![Katalog durchsuchen](media/automation-runbook-gallery/browse-gallery.png)
4. Klicken Sie auf **Quellprojekt anzeigen**, um den Artikel im [TechNet Script Center](http://gallery.technet.microsoft.com/) anzuzeigen.
5. Klicken Sie zum Importieren eines Artikels auf den Artikel, um seine Details anzuzeigen, und klicken Sie anschließend auf die Schaltfläche **Importieren**.<br> ![Schaltfläche „Importieren“](media/automation-runbook-gallery/gallery-item-detail.png)
6. Ändern Sie optional den Namen des Runbooks, und klicken Sie zum Importieren des Runbooks auf **OK**.
5. Das Runbook wird auf der Registerkarte **Runbooks** des Automation-Kontos angezeigt.


### Hinzufügen eines Runbooks zum Runbook-Katalog

Microsoft empfiehlt, Runbooks aus dem Runbook-Katalog hinzuzufügen, die für andere Kunden nützlich sein könnten. Sie können ein Runbook durch [Hochladen ins Script Center](http://gallery.technet.microsoft.com/site/upload) hinzufügen. Berücksichtigen Sie dabei Folgendes:

- Damit das Runbook im Assistenten angezeigt wird, geben Sie als **Kategorie** die Option *Windows Azure* und als **Unterkategorie** die Option *Automation* ein.  

- Laden Sie eine einzelne Datei vom Typ „.ps1“ oder „.graphrunbook“ hoch. Sind für das Runbook Module, untergeordnete Runbooks oder Objekte erforderlich, führen Sie diese in der Beschreibung der Übermittlung und im Kommentarbereich des Runbooks auf. Falls für Ihre Lösung mehrere Runbooks erforderlich sind, laden Sie sie einzeln hoch, und führen Sie die Namen der zugehörigen Runbooks in den jeweiligen Beschreibungen auf. Verwenden Sie unbedingt die gleichen Tags, damit die Runbooks in derselben Kategorie angezeigt werden. Dass zur Verwendung der Lösung noch andere Runbooks erforderlich sind, erfährt der Benutzer in der Beschreibung.

- Die Zusammenfassung für den Upload wird in den Ergebnissen des Runbook-Katalogs angezeigt. Geben Sie daher ausführliche Informationen ein, die dem Benutzer die Funktionen des Runbooks verdeutlichen.

- Weisen Sie dem Upload bis zu drei der folgenden Tags zu: Das Runbook wird im Assistenten unter den Kategorien aufgeführt, die mit den Tags übereinstimmen. Nicht in dieser Liste enthaltene Tags werden vom Assistenten ignoriert. Wenn Sie keine übereinstimmenden Tags angeben, wird das Runbook in der Kategorie „Andere“ aufgeführt.

 - Sicherung
 - Capacity Management
 - Änderungssteuerung
 - Compliance
 - Entwicklungs-/Testumgebungen
 - Notfallwiederherstellung.
 - Überwachung
 - Patching
 - Bereitstellung
 - Korrektur
 - Lebenszyklusverwaltung für virtuelle Computer


- Der Katalog wird von Automation stündlich aktualisiert. Ihre Beiträge werden daher unter Umständen nicht sofort angezeigt. Wenn Ihr Runbook nach einer Stunde nicht im Katalog angezeigt wird, überprüfen Sie die Anforderungen im Abschnitt [Hinzufügen eines Runbooks zum Runbook-Katalog](#AddRunbook).

## Module im PowerShell-Katalog

PowerShell-Module enthalten Cmdlets, die Sie in Ihren Runbooks verwenden können. Vorhandene Module, die Sie in Azure Automation installieren können, sind im [PowerShell-Katalog](http://www.powershellgallery.com) verfügbar. Sie starten diesen Katalog über das Azure-Vorschauportal und installieren die Module direkt in Azure Automation. Sie können sie auch herunterladen und manuell installieren. Die Module können nicht direkt über das Azure-Portal installiert werden, Sie können sie jedoch herunterladen und installieren wie alle anderen Module auch.

### So importieren Sie mit dem Azure-Vorschauportal ein Modul aus dem PowerShell-Katalog

1. Öffnen Sie im Azure-Vorschauportal Ihr Automation-Konto. 
2. Klicken Sie auf die Kachel **Objekte**, um die Liste mit den Objekten zu öffnen.
3. Klicken Sie auf die Kachel **Module**, um die Liste mit den Modulen zu öffnen.
3. Klicken Sie auf die Schaltfläche **PowerShell-Katalog**, um den PowerShell-Katalog in einem anderen Browserfenster zu öffnen. <br> ![PowerShell-Katalog](media/automation-runbook-gallery/powershell-gallery-button.png)
4. Über das Menü **Module** rufen Sie die Liste der verfügbaren Module auf.<br> ![Schaltfläche „PowerShell-Katalog“](media/automation-runbook-gallery/powershell-gallery.png)
4. Suchen Sie das gewünschte Modul, und wählen Sie es aus, um seine Details anzuzeigen.
5. Um das Modul direkt in Azure Automation zu installieren, klicken Sie auf die Schaltfläche **In Azure Automation bereitstellen**.<br> ![Schaltfläche „PowerShell-Katalog“](media/automation-runbook-gallery/powershell-gallery-detail.png)
6. Sie werden zu einem Bereich vom Typ **Benutzerdefinierte Bereitstellung** im Azure-Vorschauportal zurückgeleitet. Geben Sie an, ob Sie das Modul in einem **neuen oder vorhandenen Automation-Konto** installieren, und geben Sie den **Namen des Automation-Kontos** ein. Bei Verwendung eines vorhandenen Kontos wird der **Speicherort des Automation-Kontos** ignoriert. 
7. Wählen Sie **Ressourcengruppe**, und geben Sie eine vorhandene Ressourcengruppe an, oder erstellen Sie eine neue Ressourcengruppe für das Modul.
6. Wählen Sie **Rechtliche Bedingungen**, und klicken Sie auf **Kaufen**. Beachten Sie, dass trotz des Namens dieser Schaltfläche die Installation eines Moduls nicht tatsächlich in Rechnung gestellt wird.
7. Klicken Sie zum Importieren des Moduls auf **Erstellen**. Dies kann einige Minuten dauern, da jede Aktivität extrahiert werden muss.  
8. Sie erhalten jeweils eine Benachrichtigung, wenn das Modul bereitgestellt wird und wenn die Bereitstellung abgeschlossen ist. 


## Anfordern eines Runbooks oder eines Moduls

Sie können Anforderungen an [User Voice](http://feedback.azure.com/forums/246290-azure-automation) senden. Wenn Sie Hilfe beim Schreiben eines Runbooks benötigen oder eine Frage zu PowerShell haben, stellen Sie eine entsprechende Anfrage in unserem [Forum](http://social.msdn.microsoft.com/Forums/windowsazure/de-DE/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## Verwandte Artikel

- [Erstellen und Importieren eines Runbooks in Azure Automation](automation-creating-importing-runbook.md)
- [Grundlagen des PowerShell-Workflows](automation-powershell-workflow.md)

<!---HONumber=Oct15_HO3-->