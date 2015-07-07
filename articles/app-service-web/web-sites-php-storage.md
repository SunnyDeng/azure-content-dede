<properties 
	pageTitle="Erstellen einer PHP-Web-App in Azure App Service mithilfe von Azure-Speicher" 
	description="In diesem Lernprogramm erfahren Sie, wie Sie eine PHP-Web-App in Azure App Service erstellen und dazu den Azure-Tabellenspeicherdienst im Back-End verwenden können." 
	services="app-service\web, storage" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="tomfitz"/>

# Erstellen einer PHP-Web-App in Azure App Service mithilfe von Azure-Speicher

In diesem Lernprogramm erfahren Sie, wie Sie eine PHP-Web-App in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) erstellen und den Azure-Tabellenspeicherdienst im Back-End verwenden. Bei diesem Lernprogramm wird davon ausgegangen, dass [PHP][install-php] und ein Webserver auf Ihrem Computer installiert sind. Die Anweisungen in diesem Lernprogramm lassen sich von jedem Betriebssystem aus befolgen, einschließlich Windows, Max und Linux. Nachdem Sie diese Anleitung durchgearbeitet haben, verfügen Sie über eine in Azure ausgeführte PHP-Web-App, die auf den Tabellenspeicherdienst zugreift.
 
Sie erhalten Informationen zu folgenden Themen:

* Installieren der Azure-Clientbibliotheken und Übernehmen in die Anwendung
* Verwenden der Clientbibliotheken zum Erstellen von Tabellen und zum Erstellen, Abfragen und Löschen von Tabellenentitäten
* Erstellen eines Azure-Speicherkontos und Einrichten der Anwendung, damit sie dieses nutzt
* Erstellen einer Azure-Web-App und Bereitstellen von Inhalten mit Git
 
Sie erstellen eine einfache Tasklist-Webanwendung in PHP. Nachfolgend sehen Sie einen Screenshot der fertigen Anwendung:

![Azure PHP-Web-App][ws-storage-app]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

##Installieren der Azure-Clientbibliotheken

Gehen Sie folgendermaßen vor, um die PHP-Clientbibliotheken für Azure über Composer zu installieren:

1. [Installieren von Git][install-git]

	> [AZURE.NOTE]Unter Windows muss die ausführbare Git-Datei zu Ihrer PATH-Umgebungsvariable hinzugefügt werden.

2. Erstellen Sie im Stammverzeichnis Ihres Projekts eine Datei namens **composer.json**, und fügen Sie zu dieser den folgenden Code hinzu:

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

3. Laden Sie **[composer.phar][composer-phar]** in Ihr Projektverzeichnis herunter.

4. Öffnen Sie eine Eingabeaufforderung, und führen Sie in Ihrem Projektverzeichnis folgenden Befehl aus

		php composer.phar install

##Erste Schritte mit den Clientbibliotheken

Es gibt vier Grundschritte, die bei der Nutzung der Bibliotheken ausgeführt werden müssen, bevor Sie eine Azure-API aufrufen können. Sie erstellen ein Initialisierungsskript, das diese Schritte durchführen wird.

* Erstellen Sie eine Datei mit dem Namen **init.php**.

* Binden Sie zuerst das Autoloaderskript ein:

		require_once 'vendor\autoload.php'; 
	
* Schließen Sie die Namespaces ein, die Sie verwenden werden.

	Um einen Azure-Dienstclient zu erstellen, müssen Sie die **ServicesBuilder**-Klasse verwenden:

		use WindowsAzure\Common\ServicesBuilder;

	Um Ausnahmen zu erfassen, die von einem API-Aufruf generiert werden, benötigen Sie die **ServiceException**-Klasse:

		use WindowsAzure\Common\ServiceException;
	
* Zum Instanziieren des Dienstclients brauchen Sie auch eine gültige Verbindungszeichenfolge. Das Format der Tabellendienst-Verbindungszeichenfolge sieht folgendermaßen aus:

	Für den Zugriff auf einen Livedienst:
	
		DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
	
	Für den Zugriff auf den Emulatorspeicher:
	
		UseDevelopmentStorage=true

* Verwenden Sie die Factorymethode `ServicesBuilder::createTableService`, um einen Wrapper für die Tabellenspeicherdienstaufrufe zu instanziieren.

		$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	`$tableRestProxy` enthält eine Methode für alle REST-Aufrufe, die in Azure-Tabellen zur Verfügung stehen.


## Erstellen einer Tabelle

Bevor Sie Daten speichern können, müssen Sie zuerst einen Container, sprich die Tabelle, erstellen.

* Erstellen Sie eine Datei mit dem Namen **createtable.php**.

* Binden Sie zuerst das Initialisierungsskript ein, das Sie gerade erstellt haben. Dieses Skript werden Sie in jede Datei übernehmen, die auf Azure zugreift:

		<?php
		require_once "init.php";

* Rufen Sie dann *createTable* auf, und übergeben Sie den Namen der Tabelle. Ähnlich wie bei anderen NoSQL-Tabellenspeichern ist für Azure-Tabellen kein Schema erforderlich.
	
		try	{
			$tableRestProxy->createTable('tasks');
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		?>

	Fehlercodes und Meldungen finden Sie hier: [http://msdn.microsoft.com/library/windowsazure/dd179438.aspx][msdn-errors]


##Abfragen einer Tabelle

Die Startseite der Tasklist-Anwendung sollte alle bestehenden Aufgaben enthalten und das Hinzufügen von neuen ermöglichen.

* Erstellen Sie eine Datei namens **index.php**, und fügen Sie den folgenden HTML- und PHP-Code ein, der den Header der Seite bildet:
	
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

* Um Azure Tables nach **allen Entitäten** abzufragen, die in der Tabelle *tasks* gespeichert sind, rufen Sie die Methode *queryEntities* auf, wobei Sie nur den Namen der Tabelle übergeben. Im nachfolgenden Abschnitt **Aktualisieren einer Entität** erfahren Sie auch, wie Sie einen Filter, mit dem eine bestimmte Entität abgefragt wird, übergeben können.

		try {
		    $result = $tableRestProxy->queryEntities('tasks');
		}
		catch(ServiceException $e){
		    $code = $e->getCode();
		    $error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		
* So gleichen Sie Entitäten im Ergebnissatz ab:

		$entities = $result->getEntities();
			
		for ($i = 0; $i < count($entities); $i++) {

* Nach dem Erhalt einer `Entity` lautet das Modell zum Lesen von Daten `Entity->getPropertyValue('[name]')`:

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

* Als Letztes müssen Sie das Formular einfügen, das Daten in das Skript zum Einfügen von Aufgaben übergibt, und den HTML-Code vervollständigen:

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

* Erstellen Sie eine Datei mit dem Namen **additem.php**.

* Fügen Sie der Datei folgenden Code hinzu:

		<?php		
		require_once "init.php";		
		use WindowsAzure\Table\Models\Entity;
		use WindowsAzure\Table\Models\EdmType;		

* Der erste Schritt beim Einfügen einer Entität besteht in der Instanziierung eines `Entity`-Objekts und der Festlegung der Eigenschaften:
		
		$entity = new Entity();
		$entity->setPartitionKey('p1');
		$entity->setRowKey((string) microtime(true));
		$entity->addProperty('name', EdmType::STRING, $_POST['itemname']);
		$entity->addProperty('category', EdmType::STRING, $_POST['category']);
		$entity->addProperty('date', EdmType::STRING, $_POST['date']);
		$entity->addProperty('complete', EdmType::BOOLEAN, false);

* Anschließend können Sie die `$entity`, die Sie gerade erstellt haben, an die `insertEntity`-Methode übergeben:

		try{
			$tableRestProxy->insertEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* Sorgen Sie zuletzt dafür, dass nach dem Einfügen der Entität wieder die Startseite aufgerufen wird:

		header('Location: index.php');		
		?>
	
## Aktualisieren einer Entität

Die Tasklist-Anwendung bietet die Möglichkeit, ein Element als "abgeschlossen" zu markieren und die Markierung wieder aufzuheben. Die Startseite übergibt *RowKey* und *PartitionKey* einer Entität sowie den Zielstatus (markiert==1, nicht markiert==0).

* Erstellen Sie eine Datei namens **markitem.php**, und fügen Sie den Initialisierungsteil hinzu:

		<?php		
		require_once "init.php";
		

* Der erste Schritt bei der Aktualisierung einer Entität ist der Abruf aus der Tabelle:
		
		$result = $tableRestProxy->queryEntities('tasks', 'PartitionKey eq ''.$_GET['pk'].'' and RowKey eq ''.$_GET['rk'].''');		
		$entities = $result->getEntities();		
		$entity = $entities[0];

	Wie Sie sehen, hat der übergebene Abfragefilter das Format `Key eq 'Value'`. Eine umfassende Beschreibung der Abfragesyntax finden Sie [hier][msdn-table-query-syntax].

* Dann können Sie die Eigenschaften ändern:

		$entity->setPropertyValue('complete', ($_GET['complete'] == 'true') ? true : false);

* Die `updateEntity`-Methode führt die Aktualisierung durch:

		try{
			$result = $tableRestProxy->updateEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* Sorgen Sie dafür, dass nach dem Einfügen der Entität wieder die Startseite aufgerufen wird:

		header('Location: index.php');		
		?>


## Löschen einer Entität

Das Löschen eines Elements erfolgt mit einem einzelnen Aufruf von `deleteItem`. Die übergebenen Werte sind der **PartitionKey** und der **RowKey**, die zusammen den Primärschlüssel der Entität bilden. Erstellen Sie eine Datei namens **deleteitem.php**, und fügen Sie den folgenden Code hinzu:

		<?php
		
		require_once "init.php";		
		$tableRestProxy->deleteEntity('tasks', $_GET['pk'], $_GET['rk']);		
		header('Location: index.php');
		
		?>


## Erstellen eines Azure-Speicherkontos

Damit die Anwendung Daten in der Cloud speichern kann, müssen Sie zuerst ein Speicherkonto in Azure erstellen und dann die entsprechenden Authentifizierungsangaben an die *Configuration*-Klasse übergeben.

1. Melden Sie sich beim [Azure-Portal][management-portal] an.

2. Klicken Sie unten links im Portal auf das Symbol **Neu** und dann auf **Daten + Speicher** > **Speicher**. Geben Sie dem Speicherkonto einen eindeutigen Namen, und erstellen Sie eine neue [Ressourcengruppe](../resource-group-overview.md) dafür.

	![Erstellen eines neuen Speicherkontos][storage-quick-create]
	
	Wenn das Speicherkonto erstellt wurde, leuchtet auf der Schaltfläche **Benachrichtigungen** grün **ERFOLG**. Das Blatt des Speicherkontos wird geöffnet, und darauf wird angezeigt, dass es zu der neu erstellten Ressourcengruppe gehört.

5. Klicken Sie auf dem Blatt des Speicherkontos auf den Abschnitt **Einstellungen**. Notieren Sie sich den Kontonamen und den Primärschlüssel.

	![Auswählen von "Schlüssel verwalten"][storage-access-keys]

7. Öffnen Sie **init.php**, und ersetzen Sie `[YOUR_STORAGE_ACCOUNT_NAME]` und `[YOUR_STORAGE_ACCOUNT_KEY]` durch den im letzten Schritt notierten Kontonamen und Schlüssel. Speichern Sie die Datei .

## Erstellen einer Azure-Web-App und Einrichten der Git-Veröffentlichung

Führen Sie die folgenden Schritte aus, um eine Azure-Web-App zu erstellen:

1. Melden Sie sich beim [Azure-Portal][management-portal] an.

2. Erstellen Sie gemäß den Anweisungen unter [Erstellen einer Web-App mithilfe des Azure-Portals](../web-sites-create-deploy.md#createawebsiteportal) eine leere Web-App. Erstellen Sie unbedingt einen neuen [App Service-Plan](azure-web-sites-web-hosting-plans-in-depth-overview), und wählen Sie die Ressourcengruppe aus, die Sie zuvor für das Speicherkonto erstellt haben.

	Wenn die Web-App erstellt wurde, leuchtet auf der Schaltfläche **Benachrichtigungen** der Text **ERFOLG** in grün, und das Blatt der Web-App wird geöffnet, auf dem angezeigt wird, dass sie zu der neu erstellten Ressourcengruppe gehört.

6. Klicken Sie auf dem Blatt der Web-App auf **Kontinuierliche Bereitstellung einrichten**, und wählen Sie **Lokales Git-Repository** aus. Klicken Sie auf **OK**.

	![Git-Veröffentlichung einrichten][setup-git-publishing]

7. Bevor Sie das lokale Git-Repository in Azure bereitstellen können, müssen Sie die Anmeldeinformationen für die Bereitstellung konfigurieren. Klicken Sie auf dem Blatt der Web-App auf **Alle Einstellungen** > **Anmeldeinformationen für die Bereitstellung**, um die Anmeldeinformationen zu konfigurieren. Klicken Sie anschließend auf **Speichern**.

	![Anmeldedaten für die Veröffentlichung erstellen][credentials]

	Das Einrichten des Verzeichnisses dauert ein paar Sekunden.

8. Sobald das Git-Repository bereit ist, übertragen Sie Ihre Änderungen darauf. Die Repository-URL erhalten Sie durch Klicken auf den Bereitstellungsteil auf dem Blatt der Web-App.

	![Zurückgegebene Git-Bereitstellungsanweisungen nach dem Erstellen eines Repositorys für die Web-App.][git-instructions]

	Beachten Sie die Anweisungen, da diese im nächsten Abschnitt verwendet werden, um die Anwendung zu veröffentlichen.

##Veröffentlichen der Anwendung

Führen Sie die nachfolgenden Schritte aus, um Ihre Anwendung mit Git zu veröffentlichen.

1. Öffnen Sie den Ordner **vendor/microsoft/windowsazure** im Stammverzeichnis der Anwendung, und löschen Sie die folgenden Dateien und Ordner:
	* .git
	* .gitattributes
	* .gitignore
			
	Wenn der Paket-Manager Composer die Azure-Clientbibliotheken und deren Abhängigkeiten herunterlädt, wird dabei das GitHub-Repository geklont, in dem sie sich befinden. Im nächsten Schritt wird die Anwendung über Git bereitgestellt, indem auf der Grundlage des Stammordners der Anwendung ein Repository erstellt wird. Git ignoriert das Unterrepository, in dem sich die Clientbibliotheken befinden, es sei denn, die Repository-spezifischen Dateien werden entfernt.

2. Öffnen Sie GitBash (oder eine Terminalsitzung, wenn sich Git in Ihrem `PATH` befindet), wechseln Sie zum Stammverzeichnis Ihrer Anwendung, und führen Sie die folgenden Befehle aus:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

3. Navigieren Sie zu **http://[your web app domain]/createtable.php**, um die Tabelle für die Anwendung zu erstellen.
4. Navigieren Sie zu **http://[your web app domain]/index.php**, um mit der Anwendung zu arbeiten.

Nach Veröffentlichung Ihrer Anwendung können Sie Änderungen an ihr vornehmen und diese über Git veröffentlichen.

##Veröffentlichen von Änderungen an der Anwendung

Befolgen Sie die folgenden Schritte, um Änderungen an der Anwendung zu veröffentlichen:

1. Nehmen Sie lokal Änderungen an Ihrer Anwendung vor.
2. Öffnen Sie GitBash (oder eine Terminalsitzung, wenn sich Git an Ihrem `PATH` befindet), wechseln Sie zum Stammverzeichnis Ihrer Anwendung, und führen Sie die folgenden Befehle aus:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

3. Navigieren Sie zu **http://[your web app domain]/index.php**, um die Änderungen anzuzeigen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715).




[install-php]: http://www.php.net/manual/en/install.php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[composer-phar]: http://getcomposer.org/composer.phar
[msdn-errors]: http://msdn.microsoft.com/library/windowsazure/dd179438.aspx

[msdn-table-query-syntax]: http://msdn.microsoft.com/library/windowsazure/dd894031.aspx
[ws-storage-app]: ./media/web-sites-php-storage/ws-storage-app.png
[management-portal]: https://portal.azure.com

[storage-quick-create]: ./media/web-sites-php-storage/createstorage.png
[storage-access-keys]: ./media/web-sites-php-storage/keydetails.png

[setup-git-publishing]: ./media/web-sites-php-storage/setup_git_publishing.png
[credentials]: ./media/web-sites-php-storage/git-deployment-credentials.png

[git-instructions]: ./media/web-sites-php-storage/git-instructions.png
 

<!---HONumber=62-->