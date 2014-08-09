<properties  linkid="web-sites-staged-publishing" urlDisplayName="How to stage sites on Microsoft Azure" pageTitle="Staged Deployment on Microsoft Azure Web Sites" metaKeywords="Microsoft Azure Web Sites, Staged Deployment, Site Slots" description="Learn how to use staged publishing on Microsoft Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="" title="Staged Deployment on Microsoft Azure Web Sites" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

# Gestaffelte Bereitstellung auf Microsoft Azure-Websites

## Inhaltsverzeichnis ##

* [Übersicht](#Overview)
* [Hinzufügen eines Bereitstellungs-Steckplatzes zu einer Website](#Add)
* [Konfiguration für Bereitstellungs-Steckplätze](#AboutConfiguration)
* [Überführung von Bereitstellungs-Steckplätzen](#Swap)
* [Zurücksetzung einer Produktions-Website zur Bereitstellung](#Rollback)
* [Löschen eines Website-Steckplatzes](#Delete)
* [Azure PowerShell-Cmdlets für Website-Steckplätze](#PowerShell)
* [Befehle der plattformübergreifenden Azure-Befehlszeilenschnittstelle (xplat-cli) für Website-Steckplätze](#CLI)

<a name="Overview"></a>
## Übersicht ##
Die Option, Website-Steckplätze für Websites im Standardmodus zu erstellen, die auf Microsoft Azure-Websites ausgeführt werden, ermöglicht einen gestaffelten Bereitstellungsablauf. Erstellen Sie Website-Steckplätze (Entwicklung oder Staging) für jede Standardproduktions-Website (die nun ein Produktions-Steckplatz wird), und überführen Sie diese Steckplätze ohne Ausfallzeit. Die gestaffelte Bereitstellung ist für folgende Szenarien sehr wichtig:

* **Validierung vor Bereitstellung** -- Nachdem Sie Inhalte oder Konfigurationen in einem Bereitstellungs-Website-Steckplatz bereitgestellt haben, können Sie die Änderungen validieren, bevor diese Änderungen in Produktion gehen.

* **Erstellen und Integrieren von Website-Inhalten** -- Sie können schrittweise Inhaltsaktualisierungen zum gestaffelten Bereitstellungs-Steckplatz hinzufügen, und anschließend den Bereitstellungs-Steckplatz in die Produktion überführen, wenn die Aktualisierungen erfolgt sind.

* **Zurücksetzung einer Produktions-Website** -- Wenn die Änderungen, die in die Produktion überführt wurden, nicht wie erwartet ausfallen, können Sie den Originalinhalt zurück zur Produktion überführen.

Microsoft Azure bereitet alle Instanzen des ursprünglichen Website-Steckplatzes vor der Überführung in die Produktion vor, wodurch Kaltstarts bei der Bereitstellung von Inhalten vermieden werden. Die Verkehrsweiterleitung ist nahtlos, und es werden keine Anfragen aufgrund von Überführungsoperationen fallengelassen. Derzeit wird pro Standardwebsite nur ein Bereitstellungs-Steckplatz zusätzlich zum Standardproduktions-Steckplatz unterstützt.

<a name="Add"></a>
## Hinzufügen eines Bereitstellungs-Steckplatzes zu einer Website ##

Die Website muss im Standardmodus ausgeführt werden, damit die Erstellung des Website-Steckplatzes erfolgen kann.

1.  Klicken Sie auf der Schnellstartseite oder in der Schnellansicht der Dashboard-Seite für die Website auf **Add a new deployment slot** (Neuen Bereitstellungs-Steckplatz hinzufügen).
    
    ![Neuen Bereitstellungs-Steckplatz hinzufügen](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
    [WACOM.NOTE] Falls sich die Website noch nicht im Standardmodus befindet, wird folgende Nachricht angezeigt: **You must be in the standard mode to enable staged publishing** (Für die gestaffelte Veröffentlichung ist der Standardmodus erforderlich). An diesem Punkt können Sie **Upgrade** (Aktualisieren) auswählen und zur Registerkarte **Scale** (Skalieren) der Website navigieren, bevor Sie fortfahren.

2.  Das Dialogfeld **Add New Deployment Slot** (Neuen Bereitstellungs-Steckplatz hinzufügen) wird geöffnet.
    
    ![Dialogfeld Neuen Bereitstellungs-Steckplatz
    hinzufügen](./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png)
    
    Geben Sie einen Namen für den Bereitstellungs-Steckplatz an. Der Name darf nicht länger als 60 alphanumerische Zeichen sein. Es sind keine Sonderzeichen oder Leerzeichen zulässig.

3.  Erweitern Sie in der Liste der Websites das Zeichen links des Websitenamens, um den Bereitstellungs-Steckplatz anzuzeigen. Dieser enthält den Namen der Produktionswebsite, gefolgt von dem angegebenen Namen des Bereitstellungs-Steckplatzes in Klammern.
    
    ![Website-Liste mit Bereitstellungs-Steckplatz](./media/web-sites-staged-publishing/SiteListWithStagedSite.png)

4.  Wenn Sie den Namen des Bereitstellungs-Website-Steckplatzes auswählen, wird eine Seite mit einigen Registerkarten wie eine normale Website geöffnet. ***Ihr-Websitename*(*Bereitstellungs-Website-Steckplatz*)** wird oben auf der Portalseite angezeigt, um Sie daran zu erinnern, dass Sie den Bereitstellungs-Website-Steckplatz betrachten.
    
    ![Bereitstellungs-Steckplatz-Titel](./media/web-sites-staged-publishing/StagingTitle.png)

Sie können jetzt Inhalt und Konfiguration für den Bereitstellungs-Website-Steckplatz aktualisieren. Verwenden Sie für Aktualisierungen des Inhalts das Veröffentlichungsprofil oder die Bereitstellungsanmeldedaten, die zum Bereitstellungs-Website-Steckplatz gehören.

<a name="AboutConfiguration"></a>
## Konfiguration für Bereitstellungs-Steckplätze ##
Wenn ein Bereitstellungs-Steckplatz erstellt wird, wird die Konfiguration für den Bereitstellungs-Steckplatz standardmäßig vom Produktions-Website-Steckplatz kopiert. Die Konfiguration für alle Website-Steckplätze kann bearbeitet werden.

**Konfiguration, die sich beim Steckplatzwechsel ändern wird**:

* Allgemeine Einstellungen
* Verbindungszeichenfolgen
* Handlerzuordnungen
* Überwachungs- und Diagnoseeinstellungen

**Konfiguration, die sich beim Steckplatzwechsel nicht ändern wird**:

* Veröffentlichungsendpunkte
* Benutzerdefinierte Domänennamen
* SSL-Zertifikate und -Bindungen
* Skalierungseinstellungen

**Hinweise**:

* Bereitstellungs-Steckplätze sind nur für Websites im Standardmodus verfügbar.

* Wenn Sie eine Website in den Free-, Shared- oder Basic-Modus versetzen, kann diese nicht mehr überführt werden.

* Ein Bereitstellungs-Steckplatz, der in die Produktion überführt werden soll, muss genauso konfiguriert werden, wie er in der Produktion sein soll.

* Standardmäßig wird ein Bereitstellungs-Steckplatz zur selben Datenbank wie die Produktionswebsite zeigen. Sie können jedoch den Bereitstellungs-Steckplatz konfigurieren, dass er zu einer anderen Datenbank zeigt, indem Sie die Verbindungszeichenfolge(n) der Datenbank für den Bereitstellungs-Steckplatz ändern. Sie können dann die ursprüngliche Verbindungszeichenfolge(n) der Datenbank auf dem Bereitstellungs-Steckplatz wiederherstellen, bevor Sie diesen in die Produktion überführen.

<a name="Swap"></a>
## Überführung von Bereitstellungs-Steckplätzen ##

1.  Um Bereitstellungs-Steckplätze zu überführen, wählen Sie den Bereitstellungs-Steckplatz in der Liste der Websites und klicken Sie auf die Schaltfläche **Swap** in der Befehlsleiste.
    
    ![Schaltfläche Swap](./media/web-sites-staged-publishing/SwapButtonBar.png)

2.  Das Dialogfenster Swap Deployments wird angezeigt. Sie können im Dialogfeld auswählen, welcher Website-Steckplatz die Quelle und  welcher das Ziel sein soll.
    
    ![Dialogfeld Swap  Deployments](./media/web-sites-staged-publishing/SwapDeploymentsDialog.png)

3.  Klicken Sie auf das Häkchen, um den Vorgang abzuschließen. Wenn der Vorgang abgeschlossen ist, wurden die Website-Steckplätze überführt.

<a name="Rollback"></a>
## Zurücksetzung einer Produktions-Website zur Bereitstellung ##
Wenn Fehler der in die Produktion überführten Inhalte oder Konfigurationen bemerkt werden, können Sie einfach einen Bereitstellungs-Steckplatz (vorher die Produktions-Website) zurück in die Produktion überführen, und dann an der neuen Version der Website weitere Fehler beheben, während sich diese im Staging-Modus befindet.

> [WACOM.NOTE] 
> Für eine erfolgreiche Zurückführung muss der Bereitstellungs-Website-Steckplatz immer noch die unveränderten Inhalte und Konfigurationen der vorherigen Produktions-Website enthalten.

<a name="Delete"></a>
## Löschen eines Website-Steckplatzes ##

Klicken Sie in der Befehlsleiste unten auf der Seite des Azure-Websites-Portal auf **Delete** (Löschen). Sie erhalten die Option, die Website und alle Bereitstellungs-Steckplätze zu löschen, bzw. nur den Bereitstellungs-Steckplatz zu löschen.

![Löschen eines Website-Steckplatzes](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

Nachdem Sie die Bestätigungsmeldung mit **Yes** beantwortet haben, werden je nach gewählter Option ein bzw. alle Steckplätze gelöscht.

**Hinweise**:

* Die Skalierung ist nicht für Nicht-Produktions-Website-Steckplätze verfügbar. Sie ist nur für Produktions-Website-Steckplätze verfügbar.

* Das Management verknüpfter Ressourcen wird nicht für Nicht-Produktions-Website-Steckplätze unterstützt.

* Sie können bei Bedarf weiterhin direkt zur Produktions-Website veröffentlichen.

* Derzeit verwenden Ihre Bereitstellungs-Steckplätze (Websites) dieselben Ressourcen wie Ihre Produktions-Steckplätze (Websites), und werden auf denselben virtuellen Computern ausgeführt. Wenn Sie Belastungstests auf einem Staging-Steckplatz ausführen, wird die Produktionsumgebung eine vergleichbare Belastung erfahren.

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
## Azure PowerShell-Cmdlets für Website-Steckplätze

Azure PowerShell ist ein Modul, das Cmdlets für die Verwaltung von Azure über Windows PowerShell bietet, einschließlich Unterstützung bei der Verwaltung von Azure-Websites-Bereitstellungs-Steckplätzen.

* Informationen zum Installieren und Konfigurieren von Azure PowerShell sowie zur Authentifizierung von Azure PowerShell mit Ihrem Windows Azure-Abonnement finden Sie unter [ Installieren und Konfigurieren von Windows Azure PowerShell][1].

* Um eine Liste der verfügbaren Cmdlets für Azure-Websites in PowerShell zu erhalten, rufen Sie `help AzureWebsite` auf.

* * *

### Get-AzureWebsite
Das Cmdlet **Get-AzureWebsite** gibt Informationen über Azure-Websites für das aktuelle Abonnement an, wie in folgendem Beispiel:

`Get-AzureWebsite siteslotstest`

* * *

### New-AzureWebsite

Sie können einen Website-Steckplatz für jede Website im Standardmodus mithilfe des Cmdlet **New-AzureWebsite** erstellen und die Namen der Website und des Steckplatzes angeben. Geben Sie zudem dieselbe Region wie die Website für die Erstellung des Bereitstellungs-Steckplatz an, wie in folgendem Beispiel.

`New-AzureWebsite siteslotstest –Slot staging –Location “West US”`

* * *

### Publish-AzureWebsiteProject

Sie können das Cmdlet **Publish-AzureWebsiteProject** wie in folgendem Beispiel für die Inhaltsbereitstellung verwenden.

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package
[path].zip`

* * *

### Show-AzureWebsite

Nachdem die Aktualisierungen des Inhalts und der Konfiguration auf den neuen Steckplatz angewendet wurden, können Sie die Aktualisierungen validieren, indem Sie mithilfe des Cmdlet **Show-AzureWebsite** zum Steckplatz navigieren.

`Show-AzureWebsite -Name siteslotstest -Slot staging`

* * *

### Switch-AzureWebsiteSlot

Das Cmdlet **Switch-AzureWebsiteSlot** kann einen Überführungsvorgang ausführen, um den aktualisierten Bereitstellungs-Steckplatz wie in folgendem Beispiel zur Produktions-Website zu machen. Die Produktionswebsite wird keine Ausfallzeit haben sowie keinen Kaltstart erleben.

`Switch-AzureWebsiteSlot -Name siteslotstest`

* * *

### Remove-AzureWebsite

Wird ein Bereitstellungs-Steckplatz nicht mehr benötigt, kann dieser mithilfe des Cmdlet **Remove-AzureWebsite** wie in folgendem Beispiel gelöscht werden.

`Remove-AzureWebsite -Name siteslotstest -Slot staging`

* * *

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
## Befehle der plattformübergreifenden Azure-Befehlszeilenschnittstelle (xplat-cli) für Website-Steckplätze

Die plattformübergreifende Azure-Befehlszeilenschnittstelle (xplat-cli) bietet plattformübergreifende Befehle für die Arbeit mit Azure, einschließlich Unterstützung für die Verwaltung von Bereitstellungs-Steckplätzen auf Azure-Websites.

* Anweisungen zur Installation und Konfiguration von xplat-cli, einschließlich Informationen zur Verbindung von xplat-cli mit Ihrem Azure-Abonnement, finden Sie unter [Installieren und Konfigurieren der  plattformübergreifenden Azure-Befehlszeilenschnittstelle][2].

* Rufen Sie `azure site -h` auf, um eine Liste der für Azure-Websites verfügbaren Befehle in xplat-cli anzuzeigen.

* * *

### azure site list
Für Informationen zu Azure-Websites im aktuellen Abonnement rufen Sie **azure site list** wie in folgendem Beispiel auf.

`azure site list siteslotstest`

* * *

### azure site create
Um einen Website-Steckplatz für eine Website im Standardmodus zu erstellen, rufen Sie **azure site create** auf und geben Sie den Namen einer vorhandenen Website und den Namen des zu erstellenden Steckplatzes wie in folgendem Beispiel an.

`azure site create siteslotstest --slot staging`

Um die Quellcodeverwaltung für den neuen Steckplatz zu aktivieren, verwenden Sie die Option **--git** wie in folgendem Beispiel.

`azure site create –-git siteslotstest --slot staging`

* * *

### azure site swap
Um den aktualisierten Bereitstellungs-Steckplatz zur Produktions-Website zu machen, verwenden Sie den Befehl **azure site swap** für einen Überführungsvorgang wie in folgendem Beispiel. Die Produktionswebsite wird keine Ausfallzeit haben sowie keinen Kaltstart erleben.

`azure site swap siteslotstest`

* * *

### azure site delete
Um einen nicht mehr benötigten Bereitstellungs-Steckplatz zu löschen, verwenden Sie den Befehl **azure site delete** wie in folgendem Beispiel.

`azure site delete siteslotstest --slot staging`

* * *

<!-- IMAGES -->



[1]: http://www.windowsazure.com/de-de/documentation/articles/install-configure-powershell
[2]: http://www.windowsazure.com/de-de/documentation/articles/xplat-cli