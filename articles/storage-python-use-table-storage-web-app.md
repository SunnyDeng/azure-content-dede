<properties linkid="develop-python-web-app-with-blob-storage" urlDisplayName="Web App with Blob Storage" pageTitle="Python web app with table storage | Microsoft Azure" metaKeywords="Azure table storage Python, Azure Python application, Azure Python tutorial, Azure Python example" description="A tutorial that teaches you how to create a Python web application using the Azure Client Libraries. Django is used as the web framework." metaCanonical="" services="storage" documentationCenter="Python" title="Python Web Application using Table Storage" authors="" solutions="" videoId="" scriptId="" manager="" editor="mollybos" />

Python-Webanwendung mit Tabellenspeicher
========================================

In diesem Lernprogramm erfahren Sie, wie Sie eine Anwendung erstellen können, die den Tabellenspeicher mit den Azure-Clientbibliotheken für Python verwendet. Falls dies Ihre erste Python-App ist, sollten Sie zuvor einen Blick in die [Django Hello World-Webanwendung](http://windowsazure.com/de-de/documentation/articles/virtual-machines-python-django-web-app-windows-server) werfen.

In dieser Anleitung erstellen Sie eine webbasierte Anwendung mit Aufgabenlisten, die Sie in Azure bereitstellen können. Mit Aufgabenlisten können Benutzer Aufgaben abrufen, neue Aufgaben erstellen und Aufgaben als abgeschlossen markieren. Wir werden Django als Web-Framework verwenden.

Die Aufgaben werden im Azure-Speicher gespeichert. Der Azure-Speicher bietet einen unstrukturierten Datenspeicher, der gleichzeitig fehlertolerant und hochverfügbar ist. Der Azure-Speicher enthält verschiedene Datenstrukturen für die Speicherung und den Zugriff von Daten, und Sie können die Speicherdienste aus den APIs im Azure SDK für Python oder aus den REST-APIs nutzen. Weitere Informationen finden Sie unter [Speichern und Zugreifen auf Daten in Azure](http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx).

Sie erhalten Informationen zu folgenden Themen:

-   Arbeiten mit dem Azure-Tabellenspeicherdienst

Der folgende Screenshot zeigt eine Abbildung der fertigen Anwendung (die hinzugefügten Aufgabenelemente können abweichen):

![](./media/storage-python-use-table-storage-web-app/web-app-with-storage-Finaloutput-mac.png)

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

Einrichten der Entwicklungsumgebung
-----------------------------------

**Hinweis:** Informationen zur Installation von Python oder den Clientbibliotheken finden Sie im [Python-Installationshandbuch](http://windowsazure.com/de-de/documentation/articles/python-how-to-install).

*Hinweis für Windows*: Falls Sie den Windows WebPI-Installer verwendet haben, sollten Django und die Clientbibliotheken bereits auf Ihrem Computer installiert sein.

Erstellen eines Speicherkontos in Azure
---------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Erstellen eines Django-Projekts
-------------------------------

Führen Sie die folgenden Schritte aus, um die App zu erstellen:

-   Erstellen Sie ein Standard-Django-Projekt mit dem Namen 'TableserviceSample'
-   Wechseln Sie in der Befehlszeile in das Verzeichnis, in dem Sie Ihren Code speichern möchten, und führen Sie dort den folgenden Befehl aus:

        django-admin.py startproject TableserviceSample

-   Fügen Sie eine neue Python-Datei **views.py** zum Projekt hinzu
-   Fügen Sie den folgenden Code zu **views.py** hinzu, um die benötigte Django-Unterstützung zu importieren:

        from django.http import HttpResponse
        from django.template.loader import render_to_string
        from django.template import Context

-   Erstellen Sie einen neuen Ordner namens **templates** unterhalb des **TableserviceSample/TableserviceSample**-Ordners.
-   Bearbeiten Sie Ihre Anwendungseinstellungen, damit Ihre Vorlagen gefunden werden können. Öffnen Sie **settings.py** und fügen Sie den folgenden Eintrag zu INSTALLED\_APPS hinzu:

        'TableserviceSample',

-   Fügen Sie eine neue Django-Vorlagendatei **mytasks.html** zum **templates**-Ordner hinzu und fügen Sie den folgenden Code ein:

```
    <html>   
	<head><title></title></head>
    <body>
    <h2>My Tasks</h2> <br>
    <table border="1"> 
    <tr>
    <td>Name</td><td>Category</td><td>Date</td><td>Complete</td><td>Action</td></tr>
    {% for entity in entities %}
    <form action="update_task" method="GET">
    <tr><td>{{entity.name}} <input type="hidden" name='name' value="{{entity.name}}"></td>
    <td>{{entity.category}} <input type="hidden" name='category' value="{{entity.category}}"></td>
    <td>{{entity.date}} <input type="hidden" name='date' value="{{entity.date}}"></td>
    <td>{{entity.complete}} <input type="hidden" name='complete' value="{{entity.complete}}"></td>
    <td><input type="submit" value="Complete"></td>
    </tr>
    </form>
    {% endfor %}
    </table>
    <br>
    <hr>
    <table border="1">
    <form action="add_task" method="GET">
    <tr><td>Name:</td><td><input type="text" name="name"></input></td></tr>
    <tr><td>Category:</td><td><input type="text" name="category"></input></td></tr>
    <tr><td>Item Date:</td><td><input type="text" name="date"></input></td></tr>
    <tr><td><input type="submit" value="add task"></input></td></tr>
    </form>
    </table>
    </body>
     </html>    
```

Importieren des windowsazure-Speichermoduls
-------------------------------------------

Fügen Sie den folgenden Code am Anfang von **views.py** direkt nach den Django-Importen hinzu

    from azure.storage import TableService

Abrufen von Speicherkontoname und Speicherschlüssel
---------------------------------------------------

Fügen Sie den folgenden Code zu **views.py** direkt nach dem windowsazure-Import hinzu und ersetzen Sie 'youraccount' und 'yourkey' durch Ihren tatsächlichen Kontonamen und Schlüssel. Kontoname und Schlüssel können Sie im Azure-Verwaltungsportal abrufen.

    account_name = 'youraccount'
    account_key = 'yourkey'

Erstellen des TableService
--------------------------

Fügen Sie den folgenden Code nach **account\_name** hinzu:

    table_service = TableService(account_name=account_name, account_key=account_key)
    table_service.create_table('mytasks')

Auflisten von Aufgaben
----------------------

Fügen Sie die Funktion list\_tasks zu **views.py** hinzu:

    def list_tasks(request): 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

Aufgabe hinzufügen
------------------

Fügen Sie die Funktion add\_task zu **views.py** hinzu:

    def add_task(request):
        name = request.GET['name']
        category = request.GET['category']
        date = request.GET['date']
        table_service.insert_entity('mytasks', {'PartitionKey':name+category, 'RowKey':date, 'name':name, 'category':category, 'date':date, 'complete':'No'}) 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

Aufgabenstatus aktualisieren
----------------------------

Fügen Sie die Funktion update\_task zu **views.py** hinzu:

    def update_task(request):
        name = request.GET['name']
        category = request.GET['category']
        date = request.GET['date']
        partition_key = name + category
        row_key = date
        table_service.update_entity('mytasks', partition_key, row_key, {'PartitionKey':partition_key, 'RowKey':row_key, 'name': name, 'category':category, 'date':date, 'complete':'Yes'})
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

Zuordnen von URLs
-----------------

Nun müssen Sie die URLs in der Django-App zuordnen. Öffnen Sie **urls.py** und fügen Sie die folgenden Zuordnungen zu urlpatterns hinzu:

    url(r'^$', 'TableserviceSample.views.list_tasks'),
    url(r'^list_tasks$', 'TableserviceSample.views.list_tasks'),
    url(r'^add_task$', 'TableserviceSample.views.add_task'),
    url(r'^update_task$', 'TableserviceSample.views.update_task'),

Ausführen der Anwendung
-----------------------

-   Wechseln Sie zum Verzeichnis **TableserviceSample**, falls Sie dies noch nicht getan haben, und führen Sie den folgenden Befehl aus:

    python manage.py runserver

-   Verweisen Sie den Browser auf: `http://127.0.0.1:8000/`. Ersetzen Sie 8000 durch den tatsächlichen Port.

Sie können nun mit **Add Task** eine neue Aufgabe erstellen und anschließend auf **Complete** klicken, um die Aufgabe zu aktualisieren und deren Status auf "Yes" zu setzen.

Ausführen der Anwendung im Serveremulator, Veröffentlichen und Anhalten/Löschen Ihrer Anwendung
-----------------------------------------------------------------------------------------------

Sie haben ihre Anwendung im eingebauten Django-Server ausgeführt und können sie nun im Azure-Emulator (nur unter Windows) bereitstellen und anschließend auf Azure veröffentlichen. Weitere Informationen hierzu finden Sie im Artikel [Django Hello World-Webanwendung](http://windowsazure.com/de-de/documentation/articles/virtual-machines-python-django-web-app-windows-server), der diese Schritte im Detail beschreibt.

Nächste Schritte
----------------

Da Sie jetzt die Grundlagen des Azure-Tabellenspeicherdiensts erlernt haben, folgen Sie diesem Link, um zu erfahren, wie Sie komplexere Speicheraufgaben ausführen können.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure](http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx)
-   Besuchen Sie den Blog des Azure-Speicherteams: &lt;http://blogs.msdn.com/b/windowsazurestorage/&gt;

