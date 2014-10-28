<properties linkid="develop-php-website-with-storage" urlDisplayName="Web w/ Storage" pageTitle="PHP website with table storage - Azure tutorial" metaKeywords="Azure table storage PHP, Azure PHP website, Azure PHP web site, Azure PHP tutorial, Azure PHP example" description="This tutorial shows you how to create a PHP website and use the Azure Tables storage service in the back-end." metaCanonical="" services="web-sites,storage" documentationCenter="PHP" title="Create a PHP Website using Azure Storage" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Erstellen einer PHP-Website mit Azure-Speicher

In diesem Lernprogramm erfahren Sie, wie Sie eine PHP-Website erstellen und den Azure-Tabellenspeicherdienst im Back-End verwenden. Bei diesem Lernprogramm wird davon ausgegangen, dass [PHP][PHP] und ein Webserver auf Ihrem Computer installiert sind. Die Anweisungen in diesem Lernprogramm lassen sich von jedem Betriebssystem aus befolgen, einschließlich Windows, Max und Linux. Nachdem Sie diese Anleitung durchgearbeitet haben, verfügen Sie über eine in Azure ausgeführte PHP-Website, die auf den Tabellenspeicherdienst zugreift.

Sie erhalten Informationen zu folgenden Themen:

-   Installieren der Azure-Clientbibliotheken und Übernehmen in die Anwendung
-   Verwenden der Clientbibliotheken zum Erstellen von Tabellen und zum Erstellen, Abfragen und Löschen von Tabellenentitäten
-   Erstellen eines Azure-Speicherkontos und Einrichten der Anwendung, damit sie dieses nutzt
-   Erstellen einer Azure-Website und Bereitstellen von Inhalten mit Git

Sie erstellen eine einfache Tasklist-Webanwendung in PHP. Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Azure-PHP-Website][Azure-PHP-Website]

[WACOM.INCLUDE [create-account-and-websites-note][create-account-and-websites-note]]

## Installieren der Azure-Clientbibliotheken

Gehen Sie folgendermaßen vor, um die PHP-Clientbibliotheken für Azure über Composer zu installieren:

1.  [Installieren von Git][Installieren von Git]

    > [WACOM.NOTE]
    > Unter Windows muss auch die ausführbare Git-Datei zur PATH-Umgebungsvariable hinzugefügt werden.

2.  Erstellen Sie im Stammverzeichnis Ihres Projekts eine Datei namens **composer.json**, und fügen Sie zu dieser den folgenden Code hinzu:

        {
            "require": {
                "microsoft/windowsazure": "*"
            },          
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "minimum-stability": "dev"
        }

3.  Laden Sie **[composer.phar][composer.phar]** in Ihr Projektverzeichnis herunter.

4.  Öffnen Sie eine Eingabeaufforderung, und führen Sie in Ihrem Projektverzeichnis folgenden Befehl aus

        php composer.phar install

## Erste Schritte mit den Clientbibliotheken

Es gibt vier Grundschritte, die bei der Nutzung der Bibliotheken ausgeführt werden müssen, bevor Sie eine Azure-API aufrufen können. Sie erstellen ein Initialisierungsskript, das diese Schritte durchführen wird.

-   Erstellen Sie eine Datei mit dem Namen **init.php**.

-   Binden Sie zuerst das Autoloaderskript ein:

        require_once 'vendor\autoload.php'; 

-   Schließen Sie die Namespaces ein, die Sie verwenden werden.

    Um einen Azure-Dienstclient zu erstellen, müssen Sie die **ServicesBuilder**-Klasse verwenden:

        use WindowsAzure\Common\ServicesBuilder;

    Um Ausnahmen zu erfassen, die von einem API-Aufruf generiert werden, benötigen Sie die **ServiceException**-Klasse:

        use WindowsAzure\Common\ServiceException;

-   Zum Instanziieren des Dienstclients brauchen Sie auch eine gültige Verbindungszeichenfolge. Das Format der Tabellendienst-Verbindungszeichenfolge sieht folgendermaßen aus:

    Für den Zugriff auf einen Livedienst:

        DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

    Für den Zugriff auf den Emulatorspeicher:

        UseDevelopmentStorage=true

-   Verwenden Sie die Factorymethode `ServicesBuilder::createTableService`, um einen Wrapper um die Tabellendienstaufrufe zu instanziieren.

        $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    `$tableRestProxy` enthält eine Methode für alle REST-Aufrufe, die in Azure-Tabellen zur Verfügung stehen.

## Erstellen einer Tabelle

Bevor Sie Daten speichern können, müssen Sie zuerst einen Container, sprich die Tabelle, erstellen.

-   Erstellen Sie eine Datei mit dem Namen **createtable.php**.

-   Binden Sie zuerst das Initialisierungsskript ein, das Sie gerade erstellt haben. Dieses Skript werden Sie in jede Datei übernehmen, die auf Azure zugreift:

        <?php
        require_once "init.php";

-   Rufen Sie dann *createTable* auf, und übergeben Sie den Namen der Tabelle. Ähnlich wie bei anderen NoSQL-Tabellenspeichern ist für Azure-Tabellen kein Schema erforderlich.

        try {
            $tableRestProxy->createTable('tasks');
        }
        catch(ServiceException $e){
            $code = $e->getCode();
            $error_message = $e->getMessage();
            echo $code.": ".$error_message."<br />";
        }
        ?>

    Fehlercodes und eine Meldungsübersicht finden Sie hier: [http://msdn.microsoft.com/de-de/library/windowsazure/dd179438.aspx](http://msdn.microsoft.com/de-de/library/windowsazure/dd179438.aspx)

## Abfragen einer Tabelle

Die Startseite der Tasklist-Anwendung sollte alle bestehenden Aufgaben enthalten und das Hinzufügen von neuen ermöglichen.

-   Erstellen Sie eine Datei namens **index.php**, und fügen Sie den folgenden HTML- und PHP-Code ein, der den Header der Seite bildet:

        <html>
        <head>
            <title>Index</title>
            <style type="text/css">
                body { background-color: #fff; border-top: solid 10px #000;
                    color: #333; font-size: .85em; margin: 20; padding: 20;
                    font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
                }
                h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
                h1 { font-size: 2em; }
                h2 { font-size: 1.75em; }
                h3 { font-size: 1.2em; }
                table { margin-top: 0.75em; }
                th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
                td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
            </style>
        </head>
        <body>
        <h1>My ToDo List <font color="grey" size="5">(powered by PHP and Azure Tables) </font></h1>
        <?php       
        require_once "init.php";

-   Um Azure-Tabellen nach **allen Entitäten** abzufragen, die in der Tabelle *tasks* gespeichert sind, rufen Sie die Methode *queryEntities* auf, wobei Sie nur den Namen der Tabelle übergeben. Im nachfolgenden Abschnitt **Aktualisieren einer Entität** erfahren Sie auch, wie Sie einen Filter, mit dem eine bestimmte Entität abgefragt wird, übergeben können.

        try {
            $result = $tableRestProxy->queryEntities('tasks');
        }
        catch(ServiceException $e){
            $code = $e->getCode();
            $error_message = $e->getMessage();
            echo $code.": ".$error_message."<br />";
        }

-   So gleichen Sie Entitäten im Ergebnissatz ab:

        $entities = $result->getEntities();

        for ($i = 0; $i < count($entities); $i++) {

-   Nach dem Erhalt einer `Entity`, lautet das Modell zum Lesen von Daten `Entity->getPropertyValue('[name]')`:

            if ($i == 0) {
                echo "<table border='1'>
                <tr>
                    <td>Name</td>
                    <td>Category</td>
                    <td>Date</td>
                    <td>Mark Complete?</td>
                    <td>Delete?</td>
                </tr>";
            }
            echo "
                <tr>
                    <td>".$entities[$i]->getPropertyValue('name')."</td>
                    <td>".$entities[$i]->getPropertyValue('category')."</td>
                    <td>".$entities[$i]->getPropertyValue('date')."</td>";
                    if ($entities[$i]->getPropertyValue('complete') == false)
                        echo "<td><a href='markitem.php?complete=true&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Mark Complete</a></td>";
                    else
                        echo "<td><a href='markitem.php?complete=false&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Unmark Complete</a></td>";
                    echo "
                    <td><a href='deleteitem.php?pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Delete</a></td>
                </tr>";
        }

        if ($i > 0)
            echo "</table>";
        else
            echo "<h3>No items on list.</h3>";
        ?>

-   Als Letztes müssen Sie das Formular einfügen, das Daten in das Skript zum Einfügen von Aufgaben übergibt, und den HTML-Code vervollständigen:

            <hr/>
            <form action="additem.php" method="post">
                <table border="1">
                    <tr>
                        <td>Item Name: </td>
                        <td><input name="itemname" type="textbox"/></td>
                    </tr>
                    <tr>
                        <td>Category: </td>
                        <td><input name="category" type="textbox"/></td>
                    </tr>
                    <tr>
                        <td>Date: </td>
                        <td><input name="date" type="textbox"/></td>
                    </tr>
                </table>
                <input type="submit" value="Add item"/>
            </form>
        </body>
        </html>

## Einfügen von Entitäten in eine Tabelle

Ihre Anwendung kann jetzt alle in der Tabelle gespeicherten Elemente lesen. Da es anfangs noch keine Elemente gibt, fügen Sie zunächst eine Funktion hinzu, die Daten in die Datenbank schreibt.

-   Erstellen Sie eine Datei mit dem Namen **additem.php**.

-   Fügen Sie der Datei folgenden Code hinzu:

        <?php       
        require_once "init.php";        
        use WindowsAzure\Table\Models\Entity;
        use WindowsAzure\Table\Models\EdmType;      

-   Der erste Schritt beim Einfügen einer Entität besteht in der Instanziierung eines `Entity`-Objekts und der Festlegung der Eigenschaften:

        $entity = new Entity();
        $entity->setPartitionKey('p1');
        $entity->setRowKey((string) microtime(true));
        $entity->addProperty('name', EdmType::STRING, $_POST['itemname']);
        $entity->addProperty('category', EdmType::STRING, $_POST['category']);
        $entity->addProperty('date', EdmType::STRING, $_POST['date']);
        $entity->addProperty('complete', EdmType::BOOLEAN, false);

-   Dann können Sie die `$entity`, die Sie gerade erstellt haben, an die Methode `insertEntity` übergeben:

        try{
            $tableRestProxy->insertEntity('tasks', $entity);
        }
        catch(ServiceException $e){
            $code = $e->getCode();
            $error_message = $e->getMessage();
            echo $code.": ".$error_message."<br />";
        }

-   Sorgen Sie zuletzt dafür, dass nach dem Einfügen der Entität wieder die Startseite aufgerufen wird:

        header('Location: index.php');      
        ?>

## Aktualisieren einer Entität

Die Tasklist-Anwendung bietet die Möglichkeit, ein Element als "abgeschlossen" zu markieren und die Markierung wieder aufzuheben. Die Startseite übergibt *RowKey* und *PartitionKey* einer Entität sowie den Zielstatus (markiert==1, nicht markiert==0).

-   Erstellen Sie eine Datei namens **markitem.php**, und fügen Sie den Initialisierungsteil hinzu:

        <?php       
        require_once "init.php";

-   Der erste Schritt bei der Aktualisierung einer Entität ist der Abruf aus der Tabelle:

        $result = $tableRestProxy->queryEntities('tasks', 'PartitionKey eq \''.$_GET['pk'].'\' and RowKey eq \''.$_GET['rk'].'\'');     
        $entities = $result->getEntities();     
        $entity = $entities[0];

    Wie Sie sehen, hat der übergebene Abfragefilter das Format `Key eq 'Value'`. Eine umfassende Beschreibung der Abfragesyntax finden Sie [hier][hier].

-   Dann können Sie die Eigenschaften ändern:

        $entity->setPropertyValue('complete', ($_GET['complete'] == 'true') ? true : false);

-   Die Methode `updateEntity` führt die Aktualisierung durch:

        try{
            $result = $tableRestProxy->updateEntity('tasks', $entity);
        }
        catch(ServiceException $e){
            $code = $e->getCode();
            $error_message = $e->getMessage();
            echo $code.": ".$error_message."<br />";
        }

-   Sorgen Sie dafür, dass nach dem Einfügen der Entität wieder die Startseite aufgerufen wird:

        header('Location: index.php');      
        ?>

## Löschen einer Entität

Ein Element können Sie mit einem einzelnen Aufruf von `deleteItem` löschen. Die übergebenen Werte sind der **PartitionKey** und der **RowKey**, die zusammen den Primärschlüssel der Entität bilden. Erstellen Sie eine Datei namens **deleteitem.php**, und fügen Sie den folgenden Code hinzu:

        <?php
        
        require_once "init.php";        
        $tableRestProxy->deleteEntity('tasks', $_GET['pk'], $_GET['rk']);       
        header('Location: index.php');
        
        ?>

## Erstellen eines Azure-Speicherkontos

Damit die Anwendung Daten in der Cloud speichern kann, müssen Sie zuerst ein Speicherkonto in Azure erstellen und dann die entsprechenden Authentifizierungsangaben an die *Configuration*-Klasse übergeben.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.

2.  Klicken Sie unten links im Portal auf das Symbol **+ Neu**.

    ![Neue Azure-Website erstellen][Neue Azure-Website erstellen]

3.  Klicken Sie auf **Datendienste**, **Speicher** und dann **Schnellerfassung**.

    ![Neue Website benutzerdefiniert erstellen][Neue Website benutzerdefiniert erstellen]

    Geben Sie einen Wert für **URL** ein, und wählen Sie das Rechenzentrum für Ihre Website aus der Dropdownliste **REGION** aus. Klicken Sie am unteren Rand des Dialogfelds auf **Speicherkonto erstellen**.

    ![Websitedetails eingeben][Websitedetails eingeben]

    Nachdem das Speicherkonto erstellt wurde, wird der Text **Creation of Storage Account "[SITENAME]" completed successfully** angezeigt.

4.  Stellen Sie sicher, dass die Registerkarte **Speicher** ausgewählt wurde, und wählen Sie dann das gerade erstellte Speicherkonto aus der Liste aus.

5.  Klicken Sie am unteren Rand in der App-Leiste auf **Schlüssel verwalten**.

    !['Schlüssel verwalten' auswählen]['Schlüssel verwalten' auswählen]

6.  Notieren Sie sich den Namen des Speicherkontos, das Sie erstellt haben, sowie den des Primärschlüssels.

    !['Schlüssel verwalten' auswählen][1]

7.  Öffnen Sie **init.php**, und ersetzen Sie `[YOUR_STORAGE_ACCOUNT_NAME]` und `[YOUR_STORAGE_ACCOUNT_KEY]` durch den im letzten Schritt notierten Kontonamen und Schlüssel. Speichern Sie die Datei.

## Erstellen einer Azure-Website und Einrichten der Git-Veröffentlichung

Führen Sie die folgenden Schritte aus, um eine Azure-Website zu erstellen:

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie unten links im Portal auf das Symbol **+ Neu**.

    ![Neue Azure-Website erstellen][Neue Azure-Website erstellen]

3.  Klicken Sie auf **Server**, **Website** und dann auf **Schnellerfassung**.

    ![Neue Website benutzerdefiniert erstellen][2]

    Geben Sie einen Wert für **URL** ein, und wählen Sie das Rechenzentrum für Ihre Website aus der Dropdownliste **REGION** aus. Klicken Sie am unteren Rand des Dialogfelds auf **Neue Website erstellen**.

    ![Websitedetails eingeben][3]

    Nachdem die Website erstellt wurde, wird der Text **Die Erstellung der Website "[WEBSITENAME]" wurde erfolgreich abgeschlossen** angezeigt. Nun können Sie die Git-Veröffentlichung aktivieren.

4.  Klicken Sie auf den Namen der Website, der in der Liste der Websites aufgeführt ist, um das Dashboard **Schnellstart** der Website zu öffnen.

    ![Website-Dashboard öffnen][Website-Dashboard öffnen]

5.  Wählen Sie in der unteren rechten Ecke der Schnellstartseite **Bereitstellung über Quellcodeverwaltung einrichten** aus.

    ![Git-Veröffentlichung einrichten][Git-Veröffentlichung einrichten]

6.  Wählen Sie bei der Frage "Wo befindet sich Ihr Quellcode?" **Lokales Git-Repository** aus, und klicken Sie dann auf den Pfeil.

    ![Wo befindet sich Ihr Quellcode][Wo befindet sich Ihr Quellcode]

7.  Um die Git-Veröffentlichung aktivieren zu können, müssen Sie einen Benutzernamen und ein Kennwort angeben. Notieren Sie sich den Benutzernamen und das Kennwort, die Sie erstellen. (Wenn Sie schon einmal ein Git-Verzeichnis eingerichtet haben, wird dieser Schritt übersprungen.)

    ![Anmeldedaten für die Veröffentlichung erstellen][Anmeldedaten für die Veröffentlichung erstellen]

    Das Einrichten des Verzeichnisses dauert ein paar Sekunden.

8.  Sobald das Git-Repository bereit ist, erhalten Sie Anweisungen zu den Git-Befehlen, die zum Einrichten eines lokalen Repositorys und zum anschließenden Übertragen der Dateien an Azure per Push verwendet werden.

    ![Zurückgegebene Git-Bereitstellungsanweisungen nach dem Erstellen eines Repositorys für die Website][Zurückgegebene Git-Bereitstellungsanweisungen nach dem Erstellen eines Repositorys für die Website]

    Beachten Sie die Anweisungen, da diese im nächsten Abschnitt verwendet werden, um die Anwendung zu veröffentlichen.

## Veröffentlichen der Anwendung

Führen Sie die nachfolgenden Schritte aus, um Ihre Anwendung mit Git zu veröffentlichen.

1.  Öffnen Sie den Ordner **vendor/microsoft/windowsazure** im Stammverzeichnis der Anwendung, und löschen Sie die folgenden Dateien und Ordner:

    -   .git
    -   .gitattributes
    -   .gitignore

    Wenn der Paketmanager Composer die Azure-Clientbibliotheken und die Abhängigkeiten herunterlädt, wird dazu das GitHub-Repository geklont, in dem sie sich befinden. Im nächsten Schritt wird die Anwendung über Git bereitgestellt, indem aus dem Stammordner der Anwendung heraus ein Repository erstellt wird. Git ignoriert das Unterrepository, in dem sich die Clientbibliotheken befinden, es sei denn, die Repository-spezifischen Dateien werden entfernt.

2.  Öffnen Sie GitBash (oder eine Terminalsitzung, wenn sich Git in Ihrem `PATH` befindet), wechseln Sie zum Stammverzeichnis Ihrer Anwendung, und führen Sie die folgenden Befehle aus. (**Hinweis:** Dies sind dieselben Schritte, die am Ende des Abschnitts **Erstellen einer Azure-Website und Einrichten der Git-Veröffentlichung** erwähnt werden):

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

3.  Navigieren Sie zu **http://[Ihre Websitedomäne]/createtable.php**, um die Tabelle für die Anwendung zu erstellen.
4.  Navigieren Sie zu **http://[Ihre Websitedomäne]/index.php**, um mit der Anwendung zu arbeiten.

Nach Veröffentlichung Ihrer Anwendung können Sie Änderungen an ihr vornehmen und diese über Git veröffentlichen.

## Veröffentlichen von Änderungen an der Anwendung

Befolgen Sie die folgenden Schritte, um Änderungen an der Anwendung zu veröffentlichen:

1.  Nehmen Sie lokal Änderungen an Ihrer Anwendung vor.
2.  Öffnen Sie GitBash (oder eine Terminalsitzung, wenn sich Git in Ihrem `PATH` befindet), wechseln Sie zum Stammverzeichnis Ihrer Anwendung, und führen Sie die folgenden Befehle aus:

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

3.  Navigieren Sie zu **http://[Ihre Websitedomäne]/index.php**, um die Änderungen anzuzeigen.

  [PHP]: http://www.php.net/manual/en/install.php
  [Azure-PHP-Website]: ./media/web-sites-php-storage/ws-storage-app.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Installieren von Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  []: http://msdn.microsoft.com/de-de/library/windowsazure/dd179438.aspx
  [hier]: http://msdn.microsoft.com/de-de/library/windowsazure/dd894031.aspx
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com
  [Neue Azure-Website erstellen]: ./media/web-sites-php-storage/new_website.jpg
  [Neue Website benutzerdefiniert erstellen]: ./media/web-sites-php-storage/storage-quick-create.png
  [Websitedetails eingeben]: ./media/web-sites-php-storage/storage-quick-create-details.png
  ['Schlüssel verwalten' auswählen]: ./media/web-sites-php-storage/storage-manage-keys.png
  [1]: ./media/web-sites-php-storage/storage-access-keys.png
  [2]: ./media/web-sites-php-storage/website-quick-create.png
  [3]: ./media/web-sites-php-storage/website-quick-create-details.png
  [Website-Dashboard öffnen]: ./media/web-sites-php-storage/go_to_dashboard.png
  [Git-Veröffentlichung einrichten]: ./media/web-sites-php-storage/setup_git_publishing.png
  [Wo befindet sich Ihr Quellcode]: ./media/web-sites-php-storage/where_is_code.png
  [Anmeldedaten für die Veröffentlichung erstellen]: ./media/web-sites-php-storage/git-deployment-credentials.png
  [Zurückgegebene Git-Bereitstellungsanweisungen nach dem Erstellen eines Repositorys für die Website]: ./media/web-sites-php-storage/git-instructions.png
