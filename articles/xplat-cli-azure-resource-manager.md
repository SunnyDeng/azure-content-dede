<properties linkid="script-xplat-intro" urlDisplayName="Microsoft Azure Cross-Platform Command-Line Interface" pageTitle="Using Microsoft Azure Cross-Platform Command-Line Interface with the Resource Manager" title="Using Microsoft Azure Cross-Platform Command-Line Interface with the Resource Manager" metaKeywords="windows azure cross-platform command-line interface Resource Manager, windows azure command-line resource manager, azure command-line resource manager, azure cli resource manager" description="Use the Microsoft Azure Cross-Platform Command-Line Interface with the Resource Manager" metaCanonical="http://www.windowsazure.com/de-de/script/xplat-cli-intro" umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="paulettm" documentationCenter="" solutions="" authors="larryfr" services="" />

Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager
=================================================================================================

Vor Kurzem wurde eine Vorschau des Ressourcen-Managers integriert, wobei es sich um eine neue Verwaltungsmethode von Microsoft Azure handelt. Dieser Artikel beschreibt, wie Sie die plattformübergreifende Azure-Befehlszeilenschnittstelle (xplat-cli) verwenden können, um mit dem Ressourcen-Manager zu arbeiten.

> [WACOM.NOTE] Der Ressourcen-Manager befindet sich derzeit in der Vorschau und bietet nicht denselben Umfang an Verwaltungsfunktionen wie die Azure-Dienstverwaltung.

> [WACOM.NOTE] Wenn xplat-cli noch nicht installiert und konfiguriert wurde, finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle](/de-de/documentation/articles/xplat-cli/) weitere Anweisungen zum Installieren, Konfigurieren und Verwenden der xplat-cli.

Ressourcen-Manager
------------------

Der Ressourcen-Manager ermöglicht es Ihnen, eine Gruppe aus *Ressourcen* (vom Benutzer verwaltete Entitäten wie Datenbankserver, Datenbank oder Website) als einzelne logische Einheit, oder *Ressourcengruppe*, zu verwalten. Eine Ressourcengruppe kann beispielsweise eine Website sowie SQL-Datenbank-Ressourcen enthalten.

Zur Unterstützung einer deklarativeren Methode zur Beschreibung von Änderungen an Ressourcen innerhalb einer Ressourcengruppe verwendet der Ressourcen-Manager *Vorlagen*, wobei es sich um JSON-Dokumente handelt. Die Vorlagensprache ermöglicht es Ihnen auch, Parameter zu beschreiben, die beim Ausführen eines Befehls inline ausgefüllt werden oder die in einer separaten JSON-Datei gespeichert werden. Auf diese Weise können Sie mühelos neue Ressourcen mit derselben Vorlage erstellen, indem Sie unterschiedliche Parameter bereitstellen. Eine Vorlage, mit der eine Website erstellt wird, enthält beispielsweise Parameter für den Websitenamen, die Region der Website, in der sich die Website befinden wird, sowie andere allgemeine Parameter.

> [WACOM.NOTE] Einzelheiten zur Vorlagensprache sind derzeit noch nicht dokumentiert. Sobald die Dokumentation verfügbar ist, wird dieses Thema aktualisiert, um einen Link zur Referenzdokumentation bereitzustellen.
>
> Mit dem Befehl `azure group template download` können Sie jedoch Vorlagen von Microsoft und Partnern aus dem Vorlagenkatalog herunterladen und ändern.

Wenn mit einer Vorlage eine Gruppe geändert oder erstellt wird, wird eine *Bereitstellung* erstellt, die dann auf die Gruppe angewendet wird.

Authentifizierung
-----------------

Für die Arbeit mit dem Ressourcen-Manager über die xplat-cli ist es derzeit erforderlich, dass Sie sich mit einem Organisations-Konto bei Microsoft Azure authentifizieren. Die Authentifizierung mit einem Microsoft-Konto oder einem Zertifikat über eine .publishsettings-Datei funktioniert nicht.

Weitere Informationen für die Authentifizierung mit einem Organisations-Konto finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle](/de-de/documentation/articles/xplat-cli/).

Arbeiten mit Gruppen und Vorlagen
---------------------------------

1.  Der Ressourcen-Manager befindet sich derzeit in der Vorschau, sodass die zu verwendenden xplat-cli-Befehle derzeit nicht standardmäßig aktiviert sind. Verwenden Sie den folgenden Befehl, um die Befehle zu aktivieren:

         azure config mode arm

    > [WACOM.NOTE] Der Ressourcen-Manager-Modus und Azure-Dienstverwaltungsmodus schließen sich gegenseitig aus. Das heißt, dass Ressourcen, die in einem Modus erstellt wurden, nicht im anderen Modus verwaltet werden können.

2.  Für die Arbeit mit Vorlagen können Sie Ihre eigene Vorlage erstellen oder eine aus dem Vorlagenkatalog auswählen. Verwenden Sie zum Auflisten der verfügbaren Vorlagen im Katalog den folgenden Befehl.

         azure group template list

    In der Antwort werden die Namen des Herausgebers und der Vorlage ähnlich wie nachfolgend gezeigt aufgeführt.

         data:    Publisher               Name
         data:    ----------------------------------------------------------------------------
         data:    Microsoft               Microsoft.WebSite.0.1.0-preview1
         data:    Microsoft               Microsoft.PHPStarterKit.0.1.0-preview1
         data:    Microsoft               Microsoft.HTML5EmptySite.0.1.0-preview1
         data:    Microsoft               Microsoft.ASPNETEmptySite.0.1.0-preview1
         data:    Microsoft               Microsoft.WebSiteMySQLDatabase.0.1.0-preview1

3.  Verwenden Sie den folgenden Befehl, um Details zu einer Vorlage anzuzeigen, mit der eine Azure-Website erstellt wird.

         azure group template show Microsoft.WebSiteSQLDatabase.0.1.0-preview1

    Dadurch werden beschreibende Informationen zur Vorlage zurückgegeben.

4.  Wenn Sie eine Vorlage ausgewählt haben, können Sie sie mit dem folgenden Befehl herunterladen.

         azure group template download Microsoft.WebSiteSQLDatabase.0.1.0-preview1

    Nach Herunterladen der Vorlage können Sie sie ändern, um sie besser an Ihre Anforderungen anzupassen. So können Sie beispielsweise der Vorlage eine andere Ressource hinzufügen.

    > [WACOM.NOTE] Wenn Sie die Vorlage ändern, verwenden Sie den Befehl `azure group template validate`, um die Vorlage zu validieren, bevor Sie sie verwenden, um eine bestehende Ressourcengruppe zu erstellen oder zu ändern.

5.  Öffnen Sie die Vorlagendatei in einem Texteditor. Beachten Sie die Sammlung mit **Parametern** am oberen Rand. Diese enthält eine Liste der Parameter, die diese Vorlage erwartet, um die von der Vorlage beschriebenen Ressourcen zu erstellen. Einige Parameter wie **sku** haben Standardwerte, während andere nur den Typ des Werts angeben, z. B. **siteName**. Wenn Sie eine Vorlage verwenden, können Sie Parameter als Teil der Befehlszeilenparameter bereitstellen oder eine Datei angeben, die die Parameterwerte enthält. Die Parameter müssen auf jeden Fall im JSON-Format vorliegen.

    Verwenden Sie die folgenden Daten und erstellen Sie eine Datei namens **params.json**, um eine Datei zu erstellen, die Parameter für die Vorlage "Microsoft.WebSiteSQLDatabase.0.1.0-preview1" enthält. Ersetzen Sie Werte, die mit **My** beginnen, z. B. **MyWebSite**, durch Ihre eigenen Werte. Mit **siteLocation** sollte eine Azure-Region in Ihrer Nähe festgelegt werden, z. B. **North Europe** oder **South Central US**.

         {
           "siteName": {
             "value": "MyWebSite"
           },
           "hostingPlanName": {
             "value": "MyHostingPlan"
           },
           "siteLocation": {
             "value": "North Europe"
           },
           "serverName": {
             "value": "MySQLServer"
           },
           "serverLocation": {
             "value": "North Europe"
           },
           "administratorLogin": {
             "value": "MySQLAdmin"
           },
           "administratorLoginPassword": {
             "value": "MySQLAdminPassword"
           },
           "databaseName": {
             "value": "MySQLDB"
           }
         }

6.  Verwenden Sie nach dem Speichern der Datei **params.json**, den folgenden Befehl, um eine neue Ressourcengruppe, basierend auf der Vorlage zu erstellen. Der Parameter `-e` bestimmt die **params.json**-Datei, die Sie im vorherigen Schritt erstellt haben.

         azure group create MyGroupName "MyDataCenter" -y Microsoft.WebSiteSQLDatabase.0.1.0-preview1 -d MyDeployment -e params.json

    Ersetzen Sie **MyGroupName** durch den Gruppennamen, den Sie verwenden möchten, und **MyDataCenter** durch den **siteLocation**-Wert, der in der Vorlage festgelegt wurde.

    > [WACOM.NOTE] Dieser Befehl gibt "OK" zurück, nachdem die Bereitstellung hochgeladen wurde, jedoch bevor die Bereitstellung auf die Ressourcen in der Gruppe angewendet wurde. Verwenden Sie den folgenden Befehl, um den Status der Bereitstellung zu prüfen.
    >
    > `azure group deployment show MyGroupName MyDeployment`
    >
    > Mit **ProvisioningState** wird der Status der Bereitstellung angezeigt.
    >
    > Wenn Sie erkennen, dass Ihre Konfiguration nicht richtig ist und eine lang laufende Bereitstellung angehalten werden muss, verwenden Sie den folgenden Befehl.
    >
    > `azure group deployment stop MyGroupName MyDeployment`
    >
    > Wenn Sie keinen Bereitstellungsnamen angeben, wird automatisch einer anhand des Namens der Vorlagendatei erstellt. Dieser Name wird als Teil der Ausgabe des Befehls `azure group create` zurückgegeben.

7.  Verwenden Sie zum Anzeigen der Gruppe den folgenden Befehl.

         azure group show MyGroupName

    Dieser Befehl gibt Informationen zu den Ressourcen in der Gruppe zurück. Bei mehreren Gruppen können Sie den Befehl `azure group list` verwenden, um eine Liste der Gruppennamen abzurufen. Verwenden Sie dann `azure group show`, um die Details einer bestimmten Gruppe anzuzeigen.

Arbeiten mit Ressourcen
-----------------------

Auch wenn Vorlagen es Ihnen ermöglichen, gruppenweite Änderungen an der Konfiguration zu deklarieren, müssen Sie manchmal mit nur einer einzelnen Ressource arbeiten. Verwenden Sie dazu die Befehle `azure resource`.

> [WACOM.NOTE] Wenn Sie die Befehle `azure resource` verwenden, mit Ausnahme des Befehls `list`, müssen Sie mit dem Parameter `-o` die API-Version der Arbeitsressource angeben. Wenn Sie die zu verwendende API-Version nicht kennen, öffnen Sie die Vorlagendatei und gehen Sie zum Feld **apiVersion** der Ressource.

1.  Verwenden Sie den folgenden Befehl, um alle Ressourcen in einer Gruppe aufzulisten.

         azure resource list MyGroupName

2.  Verwenden Sie den folgenden Befehl, um einzelne Ressourcen, z. B. die Website, in der Gruppe anzuzeigen.

         azure resource show MyGroupName MyWebSiteName Microsoft.Web/sites -o "2014-04-01"

    Beachten Sie den Parameter **Microsoft.Web/sites**. Mit diesem Parameter wird der Typ der Ressourcen angegeben, für den Sie Informationen anfordern. Wenn Sie die zuvor heruntergeladene Vorlagendatei anschauen, sehen Sie, dass derselbe Wert verwendet wird, um den Typ der in der Vorlage beschriebenen Website-Ressource zu definieren.

    Mit diesem Befehl werden Informationen in Bezug auf die Website zurückgegeben. Das Feld **hostNames** sollte beispielsweise die URL für die Website enthalten. Verwenden Sie dies mit Ihrem Browser, um zu überprüfen, dass die Website aktiv ist.

3.  Bei der Anzeige von Details einer Ressource, ist es oftmals nützlich den Parameter `--json` zu verwenden, da dadurch die Ausgabe besser lesbar wird, da es sich bei einigen Werten um eine verschachtelte Struktur oder Sammlung handelt. Im Folgenden sehen Sie, wie die Ergebnisse des show-Befehls als JSON-Dokument zurückgegeben werden.

         azure resource show MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01" --json

    > [WACOM.NOTE] Sie können die JSON-Daten in einer Datei speichern. Verwenden Sie dazu das Zeichen "\>", um die Ausgabe in die Datei zu leiten. Beispiel:
    >
    > `azure resource show MyGroupName MyWebSite Microsoft.Web/sites --json > myfile.json`

4.  Verwenden Sie den folgenden Befehl, um eine bestehende Ressource zu löschen.

         azure resource delete MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01"

Protokollierung
---------------

Verwenden Sie den Befehl `azure group log show`, um protokollierte Informationen zu Vorgängen für eine Gruppe anzuzeigen. Standardmäßig wird dadurch der letzte Vorgang aufgelistet, der für die Gruppe durchgeführt wurde. Verwenden Sie zur Anzeige aller Vorgänge den optionalen Parameter `--all`. Verwenden Sie für die letzte Bereitstellung `--last-deployment`. Verwenden Sie für eine bestimmte Bereitstellung `--deployment` und geben Sie den Namen der Bereitstellung an. Im folgenden Beispiel wird ein Protokoll aller Vorgänge zurückgegeben, die für die Gruppe "MyGroup" durchgeführt wurden.

    azure group log show mygroup --all

Nächste Schritte
----------------

-   Weitere Informationen über die Arbeit mit der plattformübergreifenden Azure-Befehlszeilenschnittstellen finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle](/de-de/documentation/articles/xplat-cli/).
-   Weitere Informationen über die Arbeit mit dem Ressourcen-Manager in der Windows Azure PowerShell finden Sie unter [Erste Schritte mit Windows PowerShell mit dem Ressourcen-Manager](http://go.microsoft.com/fwlink/?LinkId=394760).

