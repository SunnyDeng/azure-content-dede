<properties linkid="dev-nodejs-basic-web-app-with-storage" urlDisplayName="Web App with Storage" pageTitle="Web app with table storage (Node.js) | Microsoft Azure" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="A tutorial that builds on the Web App with Express tutorial by adding Azure Storage services and the Azure module." metaCanonical="" services="cloud-services,storage" documentationCenter="Node.js" title="Node.js Web Application using Storage" authors="" solutions="" manager="" editor="" />

Node.js-Webanwendung mit Speicher
=================================

In diesem Lernprogramm erweitern Sie die Anwendung aus dem Lernprogramm [Node.js-Webanwendung mit Express](http://www.windowsazure.com/de-de/develop/nodejs/tutorials/web-app-with-express/), indem Sie die Azure-Clientbibliotheken für Node.js für die Arbeit mit Datenverwaltungsdiensten verwenden. Sie werden Ihre Anwendung erweitern und eine webbasierte Anwendung mit Aufgabenlisten erstellen, die Sie in Azure bereitstellen können. Mit Aufgabenlisten können Benutzer Aufgaben abrufen, neue Aufgaben erstellen und Aufgaben als abgeschlossen markieren.

Die Aufgaben werden im Azure-Speicher gespeichert. Der Azure-Speicher bietet einen unstrukturierten Datenspeicher, der gleichzeitig fehlertolerant und hochverfügbar ist. Der Azure-Speicher enthält verschiedene Datenstrukturen für die Speicherung und den Zugriff von Daten, und Sie können die Speicherdienste aus den APIs im Azure SDK für Node.js oder aus den REST-APIs nutzen. Weitere Informationen finden Sie unter [Speichern und Zugreifen auf Daten in Azure](http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx).

Dieses Lernprogramm setzt voraus, dass Sie die Lernprogramme [Node.js-Webanwendung](http://www.windowsazure.com/de-de/develop/nodejs/tutorials/getting-started/) und [Node.js mit Express](http://www.windowsazure.com/de-de/develop/nodejs/tutorials/web-app-with-express/) abgeschlossen haben.

Sie erhalten Informationen zu folgenden Themen:

-   Arbeiten mit der Jade Template Engine
-   Arbeiten mit den Azure-Datenverwaltungsdiensten

Nachfolgend sehen Sie einen Screenshot der fertigen Anwendung:

![Die abgeschlossene Webseite in Internet Explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

Einstellen der Speicher-Anmeldeinformationen in Web.Config
----------------------------------------------------------

Sie müssen die Speicher-Anmeldeinformationen übergeben, um auf den Azure-Speicher zugreifen zu können. Verwenden Sie dazu die Anwendungseinstellungen in der Datei web.config. Diese Einstellungen werden als Umgebungsvariablen an Node übergeben und anschließend vom Azure SDK gelesen.

**Hinweis**

Die Speicher-Anmeldeinformationen werden nur verwendet, wenn die Anwendung in Azure bereitgestellt wird. Bei der Ausführung im Emulator verwendet die Anwendung den Speicheremulator.

Führen Sie die folgenden Schritte aus, um die Anmeldeinformationen des Speicherkontos abzurufen und zu den web.config-Einstellungen hinzuzufügen:

1.  Öffnen Sie die Azure PowerShell im **Start**-Menü, indem Sie **All Programs, Azure** erweitern, mit der rechten Maustaste auf **Azure PowerShell** klicken und anschließend **Run As Administrator** ausführen.

2.  Wechseln Sie in den Ordner, der Ihre Anwendung enthält. Zum Beispiel C:\\node\\tasklist\\WebRole1.

3.  Geben Sie im Azure PowerShell-Fenster das folgende Cmdlet ein, um die Anmeldeinformationen für das Speicherkonto abzurufen:

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    Dieser Befehl ruft eine Liste der Speicherkonten und Kontoschlüssel Ihres gehosteten Dienstes ab.

    **Hinweis**

    Da das Azure SDK beim Bereitstellen eines Dienstes ein Speicherkonto erstellt, sollten Sie bereits von den Bereitstellungen Ihrer Anwendung aus den vorherigen Lernprogrammen ein Speicherkonto besitzen.

4.  Öffnen Sie die Datei web.cloud.config mit den Umgebungseinstellungen, die bei der Bereitstellung der Anwendung in Azure verwendet werden:

        PS C:\node\tasklist\WebRole1> notepad web.cloud.config

5.  Fügen Sie den folgenden Block unter dem **configuration**-Element ein und ersetzen Sie {STORAGE ACCOUNT} und {STORAGE ACCESS KEY} durch den Kontonamen und den Schlüssel des Speicherkontos, das Sie für die Bereitstellung verwenden möchten:

        <appSettings>
          <add key="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}"/>
          <add key="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}"/>
        </appSettings>

    ![Inhalt der Datei web.cloud.config](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  Speichern Sie die Datei und schließen Sie Notepad.

Installieren von Modulen
------------------------

Sie müssen das Azure-Modul für Node installieren, um die Azure-Datenverwaltungsdienste nutzen zu können. Außerdem müssen Sie das Modul node-uuid installieren, das zum Generieren von eindeutigen Bezeichnern (UUIDs) verwendet wird. Geben Sie den folgenden Befehl ein, um diese Module zu installieren:

    PS C:\node\tasklist\WebRole1> npm install node-uuid azure

Nach Abschluss des Befehls befinden sich die Module im Ordner **node\_modules**. Führen Sie folgende Schritte aus, um diese Module in Ihrer Anwendung zu verwenden:

1.  Öffnen Sie die Datei server.js:

        PS C:\node\tasklist\WebRole1> notepad server.js

2.  Fügen Sie den folgenden Code nach der Zeile ein, die mit express.createServer() endet, um die Module node-uuid, home und azure zu verwenden. Das Modul home existiert noch nicht, wird aber demnächst erstellt.

    ![Der server.js-Code mit de Zeile app = modules.exports hervorgehoben](./media/storage-nodejs-use-table-storage-cloud-service-app/node38.png)

        var uuid = require('node-uuid');
        var Home = require('./home');
        var azure = require('azure');

3.  Fügen Sie den Code für die Erstellung des Speichertabellen-Clients hinzu und übergeben Sie Speicherkonto und Zugriffsschlüssel.

    **Hinweis**

    Beim Ausführen im Emulator verwendet das SDK automatisch den Emulator, selbst wenn die Datei web.config Speicher-Anmeldeinformationen enthält.

        var client = azure.createTableService();

4.  Anschließend erstellen Sie eine Tabelle mit dem Namen tasks im Azure-Speicher. Die folgende Logik erstellt eine neue Tabelle, falls diese nicht existiert, und füllt die Tabelle mit einigen Standarddaten.

        //Tabelle erstellen
        client.createTableIfNotExists('tasks', function(error){
            if(error){
                throw error;
            }

            var item = {
                name: 'Add readonly task list',
                category: 'Site work',
                date: '12/01/2011',
                RowKey: uuid(),
                PartitionKey: 'partition1',
                completed: false
            };

            client.insertEntity('tasks', item, function(){});

        });

5.  Ersetzen Sie den existierenden Code im route-Bereich durch den folgenden Code, der eine home-Controllerinstanz erstellt und alle Anfragen an **/** oder **/home** an diese Instanz weiterleitet.

    ![Die Datei server.js mit markiertem routes-Bereich.](./media/storage-nodejs-use-table-storage-cloud-service-app/node39.png)

        var home = new Home(client);
        app.get('/', home.showItems.bind(home));
        app.get('/home', home.showItems.bind(home));

    Beachten Sie, dass der Befehl nun an ein home-Objekt delegiert wird, anstatt inline verarbeitet zu werden. Der **bind**-Befehl stellt sicher, dass diese Verweise lokal im home-Controller korrekt aufgelöst werden.

Erstellen des Home-Controllers
------------------------------

Sie müssen nun einen home-Controller erstellen, der alle Anfragen für die Aufgabenlisten-Website verarbeitet. Führen Sie die folgenden Schritte aus, um den Controller zu erstellen:

1.  Erstellen Sie eine neue Datei home.js in Notepad. Diese Datei wird den Controller-Code enthalten, der die Logik für die Aufgabenliste verarbeitet.

        PS C:\node\tasklist\WebRole1> notepad home.js

2.  Fügen Sie den folgenden Code in die Datei ein und speichern Sie die Datei. Der folgende Code verwendet das JavaScript Modul-Schema. Er exportiert eine Home-Funktion. Der Home-Prototyp enthält die Funktionen für die Verarbeitung tatsächlicher Anfragen.

        var azure=require('azure');
        module.exports = Home;

        function Home (client) {
            this.client = client;
        };

        Home.prototype = {
            showItems: function (req, res) {
                var self = this;
                this.getItems(false, function (resp, tasklist) {
                    if (!tasklist) {
                        tasklist = [];
                    }           
                    self.showResults(res, tasklist);
                });
            },

            getItems: function (allItems, callback) {
                var query = azure.TableQuery
                    .select()
                    .from('tasks');
                
                if (!allItems) {
                    query = query.where('completed eq 
        ', 'false');
                }
                this.client.queryEntities(query, callback);
             },

             showResults: function (res, tasklist) {
                res.render('home', { 
                    title: 'Todo list', 
                    layout: false, 
                    tasklist: tasklist });
             },
        };

    Ihr Home-Controller enthält nun drei Funktionen:

    -   *showItems* verarbeitet die Anfrage.
    -   *getItems* verwendet den Tabellenclient, um offene Aufgabenelemente aus Ihrer tasks-Tabelle abzurufen. Bei der Abfrage können zusätzliche Filter angegeben werden. Die oben gezeigten Filter rufen nur Aufgaben ab, deren Wert für completed gleich false ist.
    -   *showResults* ruft die Express-Renderfunktion auf, um die Seite mit der home-Ansicht darzustellen, die Sie im nächsten Abschnitt erstellen werden.

### Ändern der Home-Ansicht

Die Markup-Syntax der Jade Template Engine ist weniger ausführlich als HTML und dient als Standard-Engine für die Arbeit mit Express. Führen Sie die folgenden Schritte aus, um eine Ansicht zur Anzeige der Aufgabenlisten-Elemente zu erstellen:

1.  Öffnen Sie die Datei home.jade im Windows PowerShell-Befehlsfenster mit dem folgenden Befehl:

        PS C:\node\tasklist\WebRole1\views> notepad home.jade

2.  Ersetzen Sie den Inhalt der Datei home.jade durch den folgenden Code und speichern Sie die Datei. Das folgende Formular enthält Funktionen zum Lesen und Ändern der Aufgabenelemente. (Der home-Controller unterstützt momentan nur Lesevorgänge; Sie werden dies später ändern.) Das Formular enthält Details zu den einzelnen Elementen in der Aufgabenliste.

        html
        head
            title Index
        body
            h1 My ToDo List

            form
                table(border="1")
                    tr
                        td Name
                        td Category
                        td Date
                        td Complete

                        each item in tasklist
                            tr
                                td #{item.name}
                                td #{item.category} 
                                td #{item.date} 
                                td 
                                    input(type="checkbox", name="completed", value="#{item.RowKey}") 

Ausführen der Anwendung im Serveremulator
-----------------------------------------

1.  Geben Sie das folgende Cmdlet im Windows PowerShell-Fenster ein, um Ihren Dienst im Serveremulator zu starten und eine Webseite anzuzeigen, die Ihren Dienst aufruft.

        PS C:\node\tasklist\WebRole1> Start-AzureEmulator -launch

    Ihr Browser öffnet die folgende Seite und zeigt das Aufgabenelement an, das aus dem Azure-Speicher abgerufen wurde:

    ![Internet Explorer mit einer My Tasklist-Seite mit einem Element in der Tabelle.](./media/storage-nodejs-use-table-storage-cloud-service-app/node40.png)

Hinzufügen einer Funktion zum Erstellen von Aufgaben
----------------------------------------------------

In diesem Abschnitt erweitern Sie Ihre Anwendung um eine Funktion zum Hinzufügen von Aufgaben.

### Hinzufügen einer neuen Route in Server.js

Fügen Sie in der Datei server.js die folgende Zeile nach dem letzten Routeneintrag für **/home** ein und speichern Sie die Datei.

![Die Datei server.js mit markiertem Bereich für die home-Route.](./media/storage-nodejs-use-table-storage-cloud-service-app/node41.png)

        app.post('/home/newitem', home.newItem.bind(home));

    Der Routen-Bereich sollte nun wie folgt aussehen:

       // Routen

       var home = new Home(client);
       app.get('/', home.showItems.bind(home));
       app.get('/home', home.showItems.bind(home));
       app.post('/home/newitem', home.newItem.bind(home));

### Hinzufügen des Node-UUID-Moduls

Fügen Sie die folgende Zeile am Anfang der Datei home.js nach der ersten Zeile hinzu, in der das Modul exportiert wird, um das node-uuid-Modul zur Generierung von eindeutigen Bezeichnern zu verwenden.

![Die Datei home.js mit markierter Zeile module.exports = Home.](./media/storage-nodejs-use-table-storage-cloud-service-app/node42.png)

       var uuid = require('node-uuid');

### Hinzufügen der Funktion für neue Elemente zum Home-Controller

Erstellen Sie eine **newItem**-Funktion, um die Funktionalität zum Erstellen von Elementen zu implementieren. Fügen Sie in der Datei home.js den folgenden Code nach der letzten Funktion ein und speichern Sie die Datei.

![Hervorgehobene showresults-Funktion](./media/storage-nodejs-use-table-storage-cloud-service-app/node43.png)

       newItem: function (req, res) {
           var self = this;
           var createItem = function (resp, tasklist) {
               if (!tasklist) {
                   tasklist = [];
               }

               var count = tasklist.length;

               var item = req.body.item;
               item.RowKey = uuid();
               item.PartitionKey = 'partition1';
               item.completed = false;

               self.client.insertEntity('tasks', item, function (error) {
                   if(error){  
                       throw error;
                   }
                   self.showItems(req, res);
               });
           };

           this.getItems(true, createItem);
       },

Die **newItem**-Funktion erledigt die folgenden Aufgaben:

-   Extrahiert das übermittelte Element aus dem Text.
-   Setzt die **RowKey**- und **PartitionKey**-Werte für das neue Element. Diese Werte werden benötigt, um das Element in die Azure-Tabelle einzufügen. Für den **RowKey**-Wert wird ein UUID generiert.
-   Einfügen des Elements in die tasks-Tabelle durch Aufrufen der **insertEntity**-Funktion.
-   Anzeigen der Seite durch Aufrufen der **getItems**-Funktion.

### Hinzufügen des Formulars für neue Elemente zur Home-Ansicht

Aktualisieren Sie nun die Ansicht, indem Sie ein neues Formular hinzufügen, in dem Benutzer Elemente hinzufügen können. Fügen Sie den folgenden Code am Ende der Datei home.jade ein und speichern Sie die Datei.

**Hinweis**

Leerzeichen sind in Jade entscheidend. Entfernen Sie daher keine Leerzeichen im folgenden Code.

        hr
        form(action="/home/newitem", method="post")
            table(border="1")    
                tr
                    td Item Name: 
                    td 
                        input(name="item[name]", type="textbox")
                tr
                    td Item Category: 
                    td 
                        input(name="item[category]", type="textbox")
                tr
                    td Item Date: 
                    td 
                        input(name="item[date]", type="textbox")
            input(type="submit", value="Add item")

### Ausführen der Anwendung im Emulator

1.  Sie können nun die aktualisierte Anwendung direkt aufrufen, da der Azure-Emulator bereits ausgeführt wird:

        PS C:\node\tasklist\WebRole1> start http://localhost:81/home

    Der Browser wird geöffnet und zeigt die folgende Seite an:

    ![Eine Webseite mit dem Titel My Task List, einer Tabelle mit Aufgaben und Feldern zum Hinzufügen neuer Aufgaben.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

2.  Geben Sie Folgendes ein: **Item Name:** "New task functionality", **Item Category:** "Site work"? und **Item Date:** "12/02/2011". Klicken Sie dann auf **Add item**.

    Das Element wird zu Ihrer tasks-Tabelle im Azure-Speicher hinzugefügt und erscheint wie im folgenden Screenshot gezeigt in Ihrer Seite.

    ![Eine Webseite mit dem Titel My Task List und einer Tabelle mit Aufgaben, nachdem Sie eine neue Aufgabe hinzugefügt haben.](./media/storage-nodejs-use-table-storage-cloud-service-app/node45.png)

Erneutes Veröffentlichen der Anwendung in Azure
-----------------------------------------------

Ihre Anwendung ist nun abgeschlossen und Sie können diese in Azure veröffentlichen, indem Sie die Bereitstellung im existierenden gehosteten Dienst aktualisieren.

1.  Rufen Sie im Windows PowerShell-Fenster das folgende Cmdlet auf, um Ihren gehosteten Dienst erneut auf Azure zu veröffentlichen. Ihre Speicher- und Standorteinstellungen wurden bereits gespeichert und müssen nicht erneut eingegeben werden.

        PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

    Nach Abschluss der Bereitstellung sollten Sie eine Ausgabe ähnlich der folgenden erhalten:

    ![Die Statusmeldungen während der Bereitstellung.](./media/storage-nodejs-use-table-storage-cloud-service-app/node35.png)

    Da Sie wie auch zuvor die Option **-launch** angegeben haben, öffnet sich der Browser mit Ihrer Anwendung in Azure nach Abschluss der Veröffentlichung.

    ![Ein Browserfenster, das die My Task List-Seite anzeigt. Die URL zeigt an, dass die Seite nun in Azure gehostet wird.](./media/storage-nodejs-use-table-storage-cloud-service-app/node47.png)

Anhalten und Löschen Ihrer Anwendung
------------------------------------

Nach der Bereitstellung der Anwendung empfiehlt es sich, diese zu deaktivieren, damit Sie andere Anwendungen im Rahmen der kostenlosen Serverzeit erstellen und bereitstellen können.

Bei Azure werden Webrolleninstanzen pro genutzter Serverzeitstunde berechnet. Serverzeit wird genutzt, sobald die Anwendung bereitgestellt wird, selbst wenn die Instanzen nicht ausgeführt werden und sich im Status Beendet befinden.

In den folgenden Schritten erfahren Sie, wie Sie die Anwendung beenden und löschen.

1.  Stoppen Sie im Windows PowerShell-Fenster die im vorherigen Abschnitt erstellte Dienstbereitstellung mit folgendem Cmdlet:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    Das Stoppen des Diensts kann einige Minuten dauern. Wenn der Dienst gestoppt wurde, erhalten Sie eine entsprechende Nachricht.

    ![Statusmeldungen nachdem der Dienst angehalten wurde.](./media/storage-nodejs-use-table-storage-cloud-service-app/node48.png)

2.  Um den Dienst zu löschen, rufen Sie folgendes Cmdlet auf:

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    Geben Sie nach entsprechender Aufforderung **Y** ein, um den Dienst zu löschen.

    Das Löschen des Diensts kann einige Minuten dauern. Nachdem der Dienst gelöscht wurde, erhalten Sie eine entsprechende Nachricht.

    ![Statusmeldungen nachdem der Dienst gelöscht wurde.](./media/storage-nodejs-use-table-storage-cloud-service-app/node49.png)


