<properties linkid="develop-node-website-with-mongodb-mac" urlDisplayName="Web site with MongoDB" pageTitle="Node.js web site with MongoDB on a VM - Azure tutorial" metaKeywords="Azure tutorial MongoDB, MongoDB store data, access data MongoDB Node, Azure Node app" description="A tutorial that teaches you how to use MongoDB to store and access data from a Node application hosted on Azure." metaCanonical="http://www.windowsazure.com/de-de/develop/nodejs/tutorials/website-with-mongodb-mongolab/" services="web-sites,virtual-machines" documentationCenter="Node.js" title="Node.js Web Application with Storage on MongoDB (Virtual Machine)" authors="" solutions="" writer="" manager="" editor="" />

Erstellen einer Node.js-Anwendung auf Azure mit MongoDB auf einem virtuellen Computer
=====================================================================================

In diesem Lernprogramm erfahren Sie, wie Sie [MongoDB](http://www.mongodb.org), das auf einem virtuellen Azure-Computer gehostet wird, zum Speichern von Daten verwenden, und wie Sie von einer [Node](http://nodejs.org)-Anwendung aus, die in einer Azure-Website gehostet wird, auf Daten zugreifen. [MongoDB](http://www.mongodb.org) ist eine beliebte, leistungsfähige Open Source-NoSQL-Datenbank.

Sie erhalten Informationen zu folgenden Themen:

-   Einrichten eines virtuellen Computers mit Ubuntu und MongoDB aus dem VM Depot
-   Zugreifen auf MongoDB über eine Node-Anwendung
-   Verwenden des plattformübergreifenden Tools für Azure zum Erstellen einer Azure-Website

In diesem Lernprogramm erstellen Sie eine einfache webbasierte Anwendung zur Aufgabenverwaltung, mit der Sie Aufgaben erstellen, abrufen und vervollständigen können. Die Aufgaben werden in MongoDB gespeichert.

> [WACOM.NOTE] In diesem Programm wird eine Instanz von MongoDB verwendet, die auf einem virtuellen Computer installiert ist. Wenn Sie lieber eine gehostete MongoDB-Instanz verwenden möchten, die von MongoLabs bereitgestellt wird, finden Sie weitere Informationen unter [Erstellen einer Node.js-Anwendung auf Azure mit MongoDB mithilfe des MongoLab-Add-Ons](/de-de/develop/nodejs/tutorials/website-with-mongodb-mongolab/).

Die Projektdateien für dieses Lernprogramm werden in einem Verzeichnis namens **tasklist** gespeichert, und die fertige Anwendung sieht etwa wie folgt aus:

![Webseite mit einer leeren Aufgabenliste](./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_empty.png)

> [WACOM.NOTE] Viele der unten aufgeführten Schritte verweisen auf die Befehlszeile. Verwenden Sie in diesen Fällen die Befehlszeile für Ihr Betriebssystem, zum Beispiel **Windows PowerShell** (Windows) oder **Bash** (Unix Shell). Auf OS X-Systemen können Sie auf die Befehlszeile über die Terminal-Anwendung zugreifen.

Voraussetzungen
---------------

Für die Schritte in diesem Lernprogramm, die Node.js verwenden, benötigen Sie eine aktuelle Version von [Node.js](http://nodejs.org) in Ihrer Entwicklungsumgebung.

Zusätzlich muss [Git](http://git-scm.com) über die Befehlszeile in Ihrer Entwicklungsumgebung verfügbar sein, da dies verwendet wird, um die Anwendung auf einer Azure-Website bereitzustellen.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Erstellen eines virtuellen Computers
------------------------------------

Sie können zwar einen neuen virtuellen Computer erstellen und dann anhand der [MongoDB-Installationshandbücher](http://docs.mongodb.org/manual/installation/) MongoDB darauf installieren, doch die meiste Arbeit wurde bereits von der Community vorbereitet und steht im VM Depot zur Verfügung. Die folgenden Schritte zeigen, wie Sie ein Image aus dem VM Depot verwenden, auf dem MongoDB bereits installiert und konfiguriert ist.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Virtual Machines**, wählen Sie **Images** und dann **VM Depot** aus.

    ![Screenshot zur Auswahl von VM Depot](./media/web-sites-nodejs-store-data-mongodb/browsedepot.png)

2.  Wählen Sie ein Image aus, das MongoDB enthält. In diesem Fall wurde Ubuntu ausgewählt, um die Liste auf Images zu beschränken, die auf der Ubuntu Linux-Distribution basieren. Die endgültige Auswahl ist ein Image für MongoDB v2.2.3 auf Hardened Ubuntu.

    ![Screenshot des ausgewählten Image MongoDB v2.2.3 auf Hardened Ubuntu](./media/web-sites-nodejs-store-data-mongodb/selectimage.png)

    > [WACOM.NOTE] Verwenden Sie **More**, um alle Informationen zum Image anzuzeigen. Einige Images erfordern zusätzliche Konfigurationsschritte, nachdem Sie mit dem Image einen virtuellen Computer erstellt haben.

    Klicken Sie unten auf den Pfeil, um zum nächsten Bildschirm zu gelangen.

3.  Wählen Sie die Region und das Speicherkonto aus, das zum Speichern der VHD für dieses Image verwendet wird. Klicken Sie auf das Häkchen, um fortzufahren.

    ![Screenshot der Auswahl eines Speicherkontos](./media/web-sites-nodejs-store-data-mongodb/storageaccount.png)

    > [WACOM.NOTE] Daraufhin wird ein Kopiervorgang gestartet, der das Image aus dem VM Depot in das angegebene Speicherkonto kopiert. Dies kann eine Weile dauern, 15 Minuten oder länger.

4.  Sobald der Image-Status zu **Pending registration** wechselt, wählen Sie **Register** aus und geben einen benutzerfreundlichen Namen für das neue Image ein. Klicken Sie auf das Häkchen, um fortzufahren.

    ![Screenshot zur Image-Registrierung](./media/web-sites-nodejs-store-data-mongodb/register.png)

5.  Sobald sich der Image-Status in **Available** ändert, wählen Sie **+ New**, **Virtual Machine**, **From Gallery** aus. Wenn die Aufforderung **Choose an Image** angezeigt wird, wählen Sie **My Images** und anschließend das zuvor erstellte Image aus. Klicken Sie auf den Pfeil, um fortzufahren.

    ![Image-Screenshot](./media/web-sites-nodejs-store-data-mongodb/myimages.png)

6.  Geben Sie den Namen des virtuellen Computers, die Größe und den Benutzernamen an. Klicken Sie auf den Pfeil, um fortzufahren.

    ![Screenshot mit Name des virtuellen Computers, Benutzername usw.](./media/web-sites-nodejs-store-data-mongodb/vmname.png)

    > [WACOM.NOTE] Für dieses Lernprogramm benötigen Sie SSH, um eine Remote-Verbindung zum virtuellen Computer herzustellen. Aktivieren Sie **Use a password**, und geben Sie ein Kennwort an, falls Sie nicht mit der Verwendung von Zertifikaten mit SSH vertraut sind.
    >
    > Informationen zur Verwendung von SSH mit einem virtuellen Linux-Computer auf Azure finden Sie unter [Verwenden von SSH mit Linux auf Azure](http://www.windowsazure.com/de-de/documentation/articles/linux-use-ssh-key/).

7.  Geben Sie an, ob ein neuer oder vorhandener Clouddienst verwendet werden soll, sowie die Region, in der der virtuelle Computer erstellt wird. Klicken Sie auf den Pfeil, um fortzufahren.

    ![Screenshot der VM-Konfiguration](./media/web-sites-nodejs-store-data-mongodb/vmconfig.png)

8.  Geben Sie zusätzliche Endpunkte für den virtuellen Computer an. Da wir auf MongoDB zugreifen wollen, fügen Sie einen neuen Endpunkt mit den folgenden Informationen hinzu:

    -   Name - MongoDB
    -   Protokoll - TCP
    -   Öffentlicher Port - 27017
    -   Privater Port - 27017

    Um das MongoDB-Webportal zur Verfügung zu stellen, geben Sie einen weiteren Endpunkt mit den folgenden Informationen an:

    -   Name - MongoDBWeb
    -   Protokoll - TCP
    -   Öffentlicher Port - 28017
    -   Privater Port - 28017

    Klicken Sie zum Schluss auf das Häkchen, um den virtuellen Computer zu konfigurieren.

    ![Screenshot der Endpunktkonfiguration](./media/web-sites-nodejs-store-data-mongodb/endpoints.png)

9.  Sobald der Status des virtuellen Computers zu **Running** wechselt, sollten Sie in der Lage sein, **http://&lt;IhrVMDNSName\>.cloudapp.net:28017/** in einem Webbrowser zu öffnen, um zu bestätigen, dass MongoDB ausgeführt wird. Unten auf der Seite sollte ein Protokoll mit Informationen über den Dienst angezeigt werden, in etwa wie folgt:

         Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
            18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
            18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
            18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
            18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
            18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
            18:57:16 [initandlisten] recover : no journal files present, no recovery needed
            18:57:17 [initandlisten] waiting for connections on port 27017

    Falls das Protokoll Fehler anzeigt, lesen Sie die [MongoDB-Dokumentation](http://docs.mongodb.org/manual/) für Schritte zur Fehlerbehebung.

Installieren der Module und Generieren des Gerüsts
--------------------------------------------------

In diesem Abschnitt erstellen Sie eine neue Node-Anwendung in Ihrer Entwicklungsumgebung und verwenden "npm" zum Hinzufügen von Modulpaketen. Für die tasklist-Anwendung verwenden Sie die Module [Express](http://expressjs.com) und [Mongoose](http://mongoosejs.com). Das Express-Modul biete ein Modellansichts-Controller-Framework für Node, während Mongoose die Kommunikation mit MongoDB betreibt.

### Installieren von Express und Generieren des Gerüsts

1.  Wechseln Sie an der Befehlszeile zum Verzeichnis **tasklist**. Falls das Verzeichnis **tasklist** nicht vorhanden ist, erstellen Sie es.

    > [WACOM.NOTE] In diesem Lernprogramm wird auf den Ordner **tasklist** verwiesen. Der vollständige Ordnerpfad wird nicht angegeben, da die Pfadsemantik je nach Betriebssystem unterschiedlich ist. Sie sollten diesen Ordner in einem Verzeichnis erstellen, auf das Sie im lokalen Dateisystem einfach zugreifen können, z. B. **\~/node/tasklist** oder **c:\\node\\tasklist**.

2.  Geben Sie den folgenden Befehl für die Expressinstallation ein.

    npm install express -g

    > [WACOM.NOTE] Bei Verwendung des Parameters "-g" wird in einigen Betriebssystemen etwa folgender Fehler angezeigt: ***Error: EPERM, chmod '/usr/local/bin/express'***, verbunden mit der Aufforderung, das Konto als Administrator auszuführen. Verwenden Sie in diesem Fall den Befehl `sudo`, um npm mit einer höheren Berechtigungsebene auszuführen.

    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

         express@3.5.0 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express                                        
         ├── methods@0.1.0                                                                                              
         ├── merge-descriptors@0.0.2                                                                                    
         ├── fresh@0.2.2                                                                                                
         ├── cookie-signature@1.0.3                                                                                     
         ├── range-parser@1.0.0                                                                                         
         ├── debug@0.7.4                                                                                                
         ├── buffer-crc32@0.2.1                                                                                         
         ├── cookie@0.1.1                                                                                               
         ├── mkdirp@0.3.5                                                                                               
         ├── commander@1.3.2 (keypress@0.1.0)                                                                           
         ├── send@0.2.0 (mime@1.2.11)                                                                                   
         ├── connect@2.14.1 (response-time@1.0.0, pause@0.0.1, connect-timeout@1.0.0, method-override@1.0.0, vhost@1.0.0, static-favicon@1.0.0, morgan@1.0.0, serve-static@1.0.2, basic-auth-connect@1.0.0, qs@0.6.6, bytes@0.2.1, raw-body@1.1.3, errorhandler@1.0.0, cookie-parser@1.0.1, express-session@1.0.2, compression@1.0.0, csurf@1.0.0, serve-index@1.0.1, multiparty@2.2.0)                                                                              

    > [WACOM.NOTE] Der Parameter "-g" sorgt dafür, dass das Express-Modul global installiert wird. Auf diese Weise können wir den Befehl ***express*** aufrufen, um das Website-Gerüst zu generieren, ohne zusätzliche Pfadinformationen angeben zu müssen.

3.  Verwenden Sie den Befehl **express**, um das Gerüst zu erstellen, das für diese Anwendung verwendet wird:

    express

    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

            create : .
            create : ./package.json
            create : ./app.js
            create : ./public
            create : ./public/stylesheets
            create : ./public/stylesheets/style.css
            create : ./public/images
            create : ./public/javascripts
            create : ./routes
            create : ./routes/index.js
            create : ./routes/user.js
            create : ./views
            create : ./views/layout.jade
            create : ./views/index.jade

            install dependencies:
              $ cd . && npm install

            run the app:
              $ node app

    Nach Abschluss dieses Befehls sollten mehrere neue Verzeichnisse und Dateien im Verzeichnis **tasklist** vorhanden sein.

### Installieren zusätzlicher Module

Die Datei **package.json** ist eine der durch den Befehl **express** erstellten Dateien. Diese Datei enthält eine Liste zusätzlicher Module, die für eine Express-Anwendung erforderlich sind. Wenn Sie später diese Anwendung auf einer Azure-Website bereitstellen, wird anhand dieser Datei bestimmt, welche Module auf Azure installiert werden müssen, um Ihre Anwendung zu unterstützen.

1.  Verwenden Sie im Ordner **tasklist** Folgendes, um die in der Datei **package.json** beschriebenen Module zu installieren:

         npm install

    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

         express@3.5.0 node_modules\express                                                                            
         ├── methods@0.1.0                                                                                             
         ├── merge-descriptors@0.0.2                                                                                   
         ├── cookie-signature@1.0.3                                                                                    
         ├── fresh@0.2.2                                                                                               
         ├── debug@0.7.4                                                                                               
         ├── range-parser@1.0.0                                                                                        
         ├── buffer-crc32@0.2.1                                                                                        
         ├── cookie@0.1.1                                                                                              
         ├── mkdirp@0.3.5                                                                                              
         ├── commander@1.3.2 (keypress@0.1.0)                                                                          
         ├── send@0.2.0 (mime@1.2.11)                                                                                  
         ├── connect@2.14.1 (response-time@1.0.0, pause@0.0.1, connect-timeout@1.0.0, method-override@1.0.0, vhost@1.0., static-favicon@1.0.0, qs@0.6.6, morgan@1.0.0, basic-auth-connect@1.0.0, serve-static@1.0.2, bytes@0.2.1, rawbody@1.1.3, errorhandler@1.0.0, cookie-parser@1.0.1, csurf@1.0.0, compression@1.0.0, express-session@1.0.2, seve-index@1.0.1, multiparty@2.2.0)                                                                             
                                                                                                                  
         jade@1.3.0 node_modules\jade                                                                                  
         ├── character-parser@1.2.0                                                                                    
         ├── commander@2.1.0                                                                                           
         ├── mkdirp@0.3.5                                                                                              
         ├── monocle@1.1.51 (readdirp@0.2.5)                                                                           
         ├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)                                            
         ├── with@3.0.0 (uglify-js@2.4.12)                                                                             
         ├── constantinople@2.0.0 (uglify-js@2.4.12)                                                                   

    Damit werden alle Standardmodule installiert, die von einer Express-Anwendung verwendet werden.

2.  Geben Sie anschließend den folgenden Befehl ein, um das Mongoose-Modul lokal zu installieren und einen Eintrag dafür in der Datei **package.json** zu speichern:

         npm install mongoose --save

    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

         mongoose@3.8.8 node_modules\mongoose                     
         ├── regexp-clone@0.0.1                                   
         ├── muri@0.3.1                                           
         ├── sliced@0.0.5                                         
         ├── hooks@0.2.1                                          
         ├── mpath@0.1.1                                          
         ├── mpromise@0.4.3                                       
         ├── ms@0.1.0                                             
         ├── mquery@0.5.3 (debug@0.7.4)                           
         ├── mongodb@1.3.23 (kerberos@0.0.3, bson@0.2.5)          

    > [WACOM.NOTE] Meldungen zur Installation des C++ bson-Parsers können Sie einfach ignorieren.

Verwenden von MongoDB in einer Node-Anwendung
---------------------------------------------

In diesem Abschnitt erweitern Sie die durch den Befehl **express** erstellte Grundanwendung, indem Sie die Datei **task.js** hinzufügen, die das Modell für Ihre Aufgaben enthält. Außerdem ändern Sie die vorhandene Datei **app.js** und erstellen eine neue Controllerdatei **tasklist.js**, um das Modell zu verwenden.

### Erstellen des Modells

1.  Erstellen Sie im Verzeichnis **tasklist** ein neues Verzeichnis namens **models**.

2.  Erstellen Sie im Verzeichnis **models** eine neue Datei namens **task.js**. Diese Datei enthält das Modell für die von Ihrer Anwendung erstellten Aufgaben.

3.  Fügen Sie am Anfang der Datei **task.js** den folgenden Code hinzu, um auf die erforderlichen Bibliotheken zu verweisen:

         var mongoose = require('mongoose'),
             Schema = mongoose.Schema;

4.  Anschließend fügen Sie Code ein, um das Modell zu definieren und zu exportieren. Dieses Modell wird für die Interaktionen mit der MongoDB-Datenbank verwendet.

         var TaskSchema = new Schema({
             itemName      : String,
             itemCategory  : String,
             itemCompleted : { type: Boolean, default: false },
             itemDate      : { type: Date, default: Date.now }
         });

         module.exports = mongoose.model('TaskModel', TaskSchema);

5.  Speichern und schließen Sie die Datei **task.js**.

### Erstellen des Controllers

1.  Erstellen Sie im Verzeichnis **tasklist/routes** eine neue Datei namens **tasklist.js**, und öffnen Sie sie in einem Texteditor.

2.  Fügen Sie den folgenden Code zu **tasklist.js** hinzu. Dadurch werden das Mongoose-Modul und das in **task.js** definierte Aufgabenmodell geladen. Die TaskList-Funktion wird verwendet, um die Verbindung zum MongoDB-Server basierend auf dem Wert für **connection** zu erstellen:

         var mongoose = require('mongoose'),
             task = require('../models/task.js');

         module.exports = TaskList;

         function TaskList(connection) {
           mongoose.connect(connection);
         }

3.  Fügen Sie außerdem zur Datei **tasklist.js** die Methoden **showTasks**, **addTask** und **completeTasks** hinzu:

         TaskList.prototype = {
           showTasks: function(req, res) {
             task.find({itemCompleted: false}, function foundTasks(err, items) {
               res.render('index',{title: 'My ToDo List ', tasks: items})
             });
           },

           addTask: function(req,res) {
             var item = req.body.item;
             newTask = new task();
             newTask.itemName = item.name;
             newTask.itemCategory = item.category;
             newTask.save(function savedTask(err){
               if(err) {
                 throw err;
               }
             });
             res.redirect('/');
           }, 


           completeTask: function(req,res) {
             var completedTasks = req.body;
             for(taskId in completedTasks) {
               if(completedTasks[taskId]=='true') {
                 var conditions = { _id: taskId };
                 var updates = { itemCompleted: completedTasks[taskId] };
                 task.update(conditions, updates, function updatedTask(err) {
                  if(err) {
                    throw err;
                  }
                });
              }
            }
            res.redirect('/');
           }
         }

1.  Speichern Sie die Datei **tasklist.js**.

### Ändern von app.js

1.  Öffnen Sie im Verzeichnis **tasklist** die Datei **app.js** in einem Texteditor. Diese Datei wurde zuvor mit dem Befehl **express** erstellt.

2.  Fügen Sie den folgenden Code am Anfang der Datei **app.js** hinzu. Damit wird **TaskList** mit der Verbindungszeichenfolge für den MongoDB-Server initialisiert:

         var TaskList = require('./routes/tasklist');
         var taskList = new TaskList(process.env.MONGODB_URI);

    Beachten Sie die zweite Zeile: Sie rufen eine Umgebungsvariable auf, die Sie später konfigurieren werden und die die Verbindungsinformationen für die Mongo-Instanz enthält. Falls Sie eine lokale Mongo-Instanz zu Entwicklungszwecken haben, können Sie diesen Wert temporär auf "localhost" statt auf "process.env.MONGODB\_URI" setzen.

3.  Suchen Sie die Zeilen, die mit `app.get` beginnen, und ersetzen Sie sie durch die folgenden Zeilen:

         app.get('/', taskList.showTasks.bind(taskList));
         app.post('/addtask', taskList.addTask.bind(taskList));
         app.post('/completetask', taskList.completeTask.bind(taskList));

    Dadurch werden die in **tasklist.js** definierten Funktionen als Routen hinzugefügt.

4.  Speichern Sie die Datei **app.js**.

### Ändern der Indexansicht

1.  Wechseln Sie zum Verzeichnis **views**, und öffnen Sie die Datei **index.jade** in einem Texteditor.

2.  Ersetzen Sie den Inhalt der Datei **index.jade** durch den folgenden Code. Damit werden die Ansicht zur Anzeige vorhandener Aufgaben sowie ein Formular für das Hinzufügen neuer Aufgaben und das Markieren von vorhandenen Aufgaben als abgeschlossen definiert.

         h1= title
         form(action="/completetask", method="post")
           table(border="1")
             tr
               td Name
               td Category
               td Date
               td Complete
             each task in tasks
               tr
                 td #{task.itemName}
                 td #{task.itemCategory}
                 - var day   = task.itemDate.getDate();
                 - var month = task.itemDate.getMonth() + 1;
                 - var year  = task.itemDate.getFullYear();
                 td #{month + "/" + day + "/" + year}
                 td
                   input(type="checkbox", name="#{task._id}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
           input(type="submit", value="Update tasks")
         hr
         form(action="/addtask", method="post")
           table(border="1") 
             tr
               td Item Name: 
               td 
                 input(name="item[name]", type="textbox")
             tr
               td Item Category: 
               td 
                 input(name="item[category]", type="textbox")
           input(type="submit", value="Add item")

3.  Speichern und schließen Sie die Datei **index.jade**.

Bereitstellen der Anwendung in Azure
------------------------------------

Die Schritte in diesem Abschnitt verwenden die Azure-Befehlszeilentools zum Erstellen einer neuen Azure-Website und stellen die Anwendung dann mit Git bereit. Zur Durchführung dieser Schritte benötigen Sie ein Azure-Abonnement.

> [WACOM.NOTE] Diese Schritte können auch über das Azure-Portal ausgeführt werden. Informationen zur Verwendung des Azure-Portals zum Bereitstellen einer Node.js-Anwendung finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website](/de-de/develop/nodejs/tutorials/create-a-website-(mac)/).

> [WACOM.NOTE] Falls dies die erste von Ihnen erstellte Azure-Website ist, müssen Sie das Azure-Portal verwenden, um die Anwendung bereitzustellen.

### Installieren der Azure plattformübergreifenden Befehlszeilenschnittstelle

Die Azure plattformübergreifende Befehlszeilenschnittstelle (xplat-cli) ermöglicht das Ausführen von Verwaltungsvorgängen für Azure-Dienste. Falls Sie nicht bereits xplat-cli in Ihrer Entwicklungsumgebung installiert und konfiguriert haben, finden Sie weitere Informationen unter [Installieren und Konfigurieren der Azure plattformübergreifenden Befehlszeilenschnittstelle](/de-de/documentation/articles/xplat-cli/).

### Erstellen einer Azure-Website

1.  Wechseln Sie an der Befehlszeile zum Verzeichnis **tasklist**.

2.  Verwenden Sie den folgenden Befehl, um eine neue Azure-Website zu erstellen. Ersetzen Sie "eindeutigersitename" durch einen eindeutigen Sitenamen für Ihre Website. Dieser Wert wird als Teil der URL für die Website verwendet.

         azure site create eindeutigersitename --git

    Sie werden zur Eingabe des Datencenters aufgefordert, in dem sich die Website befinden wird. Wählen Sie ein Datencenter aus, das sich nahe an Ihrem Standort befindet.

    Der Parameter `--git` erstellt lokal im Ordner **tasklist** ein Git-Repository, falls keins vorhanden ist. Außerdem erstellen Sie ein [Git remote](http://git-scm.com/docs/git-remote) namens "azure", das zum Veröffentlichen der Anwendung in Azure verwendet wird. Daraus wird ein [iisnode.yml](https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml) erstellt, das Einstellungen enthält, die von Azure zum Hosting von Node-Anwendungen verwendet werden. Abschließend wird die Datei ".gitignore" erstellt, um zu verhindern, dass der Ordner "node-modules" auf .git veröffentlicht wird.

    > [WACOM.NOTE] Wenn dieser Befehl von einem Verzeichnis ausgeführt wird, das bereits ein Git-Repository enthält, wird das Verzeichnis nicht erneut initialisiert.

    > [WACOM.NOTE] Wird der Parameter "--git" ausgelassen, aber das Verzeichnis enthält ein Git-Repository, wird "azure" remote trotzdem erstellt.

    Nach Abschluss dieses Befehls wird eine Ausgabe ähnlich der folgenden angezeigt. Beachten Sie, dass die Zeile, die mit **Created web site at** beginnt, die URL für die Website enthält.

         info:   Executing command site create
         info:   Using location southcentraluswebspace
         info:   Executing `git init`
         info:   Creating default web.config file
         info:   Creating a new web site
         info:   Created web site at  mongodbtasklist.azurewebsites.net
         info:   Initializing repository
         info:   Repository initialized
         info:   Executing `git remote add azure http://username@mongodbtasklist.azurewebsites.net/mongodbtasklist.git`
         info:   site create command OK

    > [WACOM.NOTE\> Falls dies die erste Azure-Website für Ihr Abonnement ist, werden Sie angewiesen, das Portal zum Erstellen der Website zu verwenden. Weitere Informationen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website](/de-de/develop/nodejs/tutorials/create-a-website-(mac)/).

### Festlegen der MONGODB\_URL-Umgebungsvariable

Die Anwendung erwartet, dass die Verbindungszeichenfolge für die MongoDB-Instanz in der MONGODB\_URI-Umgebungsvariable verfügbar ist. Verwenden Sie den folgenden Befehl, um diesen Wert für die Website festzulegen:

    azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

Damit wird eine neue Anwendungseinstellung für die Website erstellt, die verwendet wird, um die von der Website gelesene MONGODB\_URI-Umgebungsvariable auszufüllen. Ersetzen Sie den Wert von "mymongodb.cloudapp.net" durch den Namen des virtuellen Computers, auf dem MongoDB installiert wurde.

### Veröffentlichen der Anwendung

1.  Wechseln Sie im Terminalfenster zum Verzeichnis **tasklist**, falls Sie sich nicht bereits dort befinden.

2.  Verwenden Sie die folgenden Befehle, um Dateien hinzuzufügen und anschließend in das lokale Git-Repository zu übernehmen:

         git add .
         git commit -m "adding files"

3.  Wenn Sie die letzten Änderungen am Git-Repository per Push an die Azure-Website übertragen, müssen Sie angeben, dass die Zielstruktur **master** lautet, da dies für den Websiteinhalt verwendet wird.

         git push azure master

    Eine Ausgabe ähnlich der folgenden wird angezeigt. Während der Bereitstellung lädt Azure alle npm-Module herunter.

         Counting objects: 17, done.
         Delta compression using up to 8 threads.
         Compressing objects: 100% (13/13), done.
         Writing objects: 100% (17/17), 3.21 KiB, done.
         Total 17 (delta 0), reused 0 (delta 0)
         remote: New deployment received.
         remote: Updating branch 'master'.
         remote: Preparing deployment for commit id 'ef276f3042'.
         remote: Preparing files for deployment.
         remote: Running NPM.
         ...
         remote: Deploying Web.config to enable Node.js activation.
         remote: Deployment successful.
         To https://username@mongodbtasklist.azurewebsites.net/MongoDBTasklist.git
          * [new branch] master -\> master

4.  Sobald der Push-Vorgang zur Übertragung abgeschlossen ist, rufen Sie die Website im Browser mit dem Befehl `azure site browse` auf, um Ihre Anwendung anzuzeigen.

Nächste Schritte
----------------

Die Schritte in diesem Artikel beschreiben zwar die Verwendung von MongoDB zum Speichern von Informationen, Sie können jedoch auch den Azure-Tabellendienst verwenden. Weitere Informationen dazu finden Sie unter [Node.js-Webanwendung mit dem Azure-Tabellendienst](/de-de/develop/nodejs/tutorials/web-site-with-storage/).

Wenn Sie erfahren möchten, wie Sie eine von MongoLabs bereitgestellte gehostete MongoDB-Instanz verwenden, finden Sie weitere Informationen unter [Erstellen einer Node.js-Anwendung auf Azure mit MongoDB mithilfe des MongoLab-Add-Ons](/de-de/develop/nodejs/tutorials/website-with-mongodb-mongolab/).

Informationen zur Sicherheit von MongoDB finden Sie unter [MongoDB-Sicherheit](http://docs.mongodb.org/manual/security/).

Zusätzliche Ressourcen
----------------------

[Azure-Befehlszeilentool für Mac und Linux](/de-de/develop/nodejs/tutorials/create-a-website-(mac)/)

 [Veröffentlichen auf Azure-Websites mit Git](/de-de/develop/nodejs/common-tasks/publishing-with-git/)

