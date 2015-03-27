<properties 
    pageTitle="Erstellen einer Webanwendung mit Python und Flask mithilfe von DocumentDB | Azure" 
    description="Erfahren Sie, wie Sie mithilfe von DocumentDB Daten aus einer Anwendung in Azure gehosteten Python- und Flask-Webanwendung (MVC) speichern und darauf zugreifen." 
    services="documentdb" 
    documentationCenter="" 
    authors="crwilcox" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/12/2015" 
    ms.author="crwilcox"/>

# Erstellen einer Webanwendung mit Python und Flask (MVC) mithilfe von DocumentDB
------------------------------------------------------------------------

Dieses Dokument umfasst eine vollständige Anleitung zur Erstellung einer Webanwendung für eine Wahl mithilfe von Azure DocumentDB, um hervorzuheben, wie Azure DocumentDB von Kunden effizient zum Speichern und Abfragen von JSON-Dokumenten genutzt werden kann.

In dieser Anleitung wird Ihnen gezeigt, wie Sie den von Azure bereitgestellten Dienst DocumentDB verwenden, um Daten von einer auf Azure gehosteten Python-Webanwendung zu speichern und zu nutzen. Hierbei wird vorausgesetzt,dass Sie bereits über Erfahrung mit Python und Azure-Websites verfügen.

Diese Anleitung umfasst:

1. Erstellen und Bereitstellen eines DocumentDB-Kontos

2. Erstellen einer Python MVC-Anwendung

3. Herstellen einer Verbindung zu Azure DocumentDB über eine Webanwendung und anschließendes Verwenden von DocumentDB

4. Bereitstellen der Webanwendung für Azure-Websites

Im Verlauf dieser exemplarischen Vorgehensweise erstellen Sie eine einfache Wahlanwendung, über die Sie
für eine Wahl abstimmen können.

![Alt text](./media/documentdb-python-application/image1.png)


## Voraussetzungen

Bevor Sie diesen Artikel durcharbeiten, sollten Sie sicherstellen, dass Folgendes installiert ist:

- Visual Studio 2013 (oder [Visual Studio Express][], die kostenlose Version)

- Python Tools für Visual Studio ([hier][] erhältlich)

- Azure-SDK für Visual Studio 2013, Version 2.4 oder höher([hier][1] erhältlich)

- Python 2.7 ([hier][2] erhältlich)

- Microsoft Visual C++-Compiler für Python 2.7 ([hier][3] erhältlich)

## Schritt 1: Erstellen eines DocumentDB-Datenbankkontos

Wenn Sie ein DocumentDB-Datenbankkonto in Azure bereitstellen möchten, öffnen Sie das [Azure-Verwaltungsportal][], und klicken Sie dann entweder auf der Homepage auf die Azure Gallery-Kachel oder in der unteren linken Bildschirmecke auf das Pluszeichen ("+").

![Alt text](./media/documentdb-python-application/image2.png)


Dadurch wird der Azure Marketplace geöffnet, in dem Sie aus den zahlreichen verfügbaren Azure-Diensten auswählen können. Wählen Sie im Marketplace "Daten, + Analyse" aus der Liste der Kategorien aus.

![Alt text](./media/documentdb-python-application/image3.png)

Suchen Sie daraufhin nach "documentdb", und wählen Sie die Option für DocumentDB aus.

![Alt text](./media/documentdb-python-application/image4.png)

Wählen Sie dann "Erstellen" im unteren Bildschirmbereich aus

![Alt text](./media/documentdb-python-application/image5.png)

Dadurch wird das Blatt "Neuer DocumentDB" geöffnet, in dem Sie Name, Region, Skalierung, Ressourcengruppe und andere Einstellungen für Ihr neues Konto eingeben können.

![Alt text](./media/documentdb-python-application/image6.png)

Sobald Sie die Werte für Ihr Konto angegeben haben, klicken Sie auf "Erstellen", woraufhin der Bereitstellungsprozess mit der Erstellung Ihres Datenbankkontos beginnt.
Nach Abschluss des Bereitstellungsvorgangs sollte eine Benachrichtigung im entsprechenden Bereich des Portals angezeigt werden, und die Kachel auf Ihrem Startbildschirm (sofern Sie eine erstellt haben) zeigt die abgeschlossene Aktion an.

![Alt text](./media/documentdb-python-application/image7.png)

Nachdem die Bereitstellung abgeschlossen ist, können Sie auf dem Startbildschirm auf die DocumentDB-Kachel klicken, um das Hauptblatt für dieses neu erstellte DocumentDB-Konto zu öffnen.

![Alt text](./media/documentdb-python-application/image8.png)

Greifen Sie über die Schaltfläche "Schlüssel" auf Ihre Endpunkt-URL und den primären Schlüssel zu,kopieren Sie diese dann in die Zwischenablage, um sie dort griffbereit zu halten, da wir diese Werte in der als nächstes zu erstellenden Webanwendung verwenden werden.


## Schritt 2: Erstellen einer neuen Python Flask-Webanwendung

Öffnen Sie Visual Studio, Datei -\> Neues Projekt -\> Python -\> Flask-Webprojekt mit dem Namen **Lernprogramm**. 

Bei Flask handelt es sich um ein Webframework, das uns beim schnelleren Erstellen von Webanwendungen in Python unterstützt. [Klicken Sie hier, um Flask-Lernprogramme zu öffnen][].

![Alt text](./media/documentdb-python-application/image9.png)

Sie werden dann gefragt, ob Sie externe Pakete installieren möchten. Klicken Sie auf die Option zum **Installieren in einer virtuellen Umgebung**. Achten Sie darauf, dass Sie Python 2.7 als Basisumgebung verwenden, da PyDocumentDB Python 3.x derzeit nicht unterstützt.  Dadurch wird die erforderliche virtuelle Python-Umgebung für das Projekt eingerichtet.

![Alt text](./media/documentdb-python-application/image10.png)


## Schritt 3: Anpassen der Python Flask-Webanwendung


### Hinzufügen von Flask-Paketen zum Projekt

Nachdem das Projekt eingerichtet ist, müssen Sie bestimmte Flask-Pakete hinzufügen, die für unser Projekt erforderlich sind, z. B. PyDocumentDB, das Python-Paket für DocumentDB. Öffnen Sie die Datei **requirements.txt**, und ersetzen Sie den Inhalt durch Folgendes:

    flask==0.9
    flask-mail==0.7.6
    sqlalchemy==0.7.9
    flask-sqlalchemy==0.16
    sqlalchemy-migrate==0.7.2
    flask-whooshalchemy==0.55a
    flask-wtf==0.8.4
    pytz==2013b
    flask-babel==0.8
    flup
    pydocumentdb>=0.9.4-preview

Klicken Sie mit der rechten Maustaste auf **env**, und klicken Sie auf den Link zum **Installieren aus requirements.txt**.

![Alt text](./media/documentdb-python-application/image11.png)

**Hinweis:** In seltenen Fällen wird möglicherweise ein Fehler im Ausgabefenster angezeigt. Wenn dies geschieht, überprüfen Sie, ob der Fehler mit der Bereinigung zusammenhängt. Manchmal schlägt die Bereinigung fehl, aber die Installation ist dennoch erfolgreich (scrollen Sie im Ausgabefenster nach oben, um dies zu überprüfen).
<a name="verify-the-virtual-environment"></a>In diesem Fall können Sie fortfahren.


### Überprüfen der virtuellen Umgebung

Stellen Sie sicher, dass alles einwandfrei installiert ist. Drücken Sie **F5**, um die Website zu starten. Dadurch werden der Flask-Entwicklungsserver und anschließend der Webbrowser gestartet. Daraufhin sollte die folgende Seite angezeigt werden:

![Alt text](./media/documentdb-python-application/image12.png)

### Erstellen der Datenbank-, Sammlungs- und Dokumentdefinition

Fügen Sie eine Python-Datei hinzu, indem Sie mit der rechten Maustaste auf den Ordner **Lernprogramme** im Projektmappen-Explorer klicken.  Nennen Sie die Datei **forms.py**.  Wir erstellen unsere Wahlanwendung.

    from flask.ext.wtf import Form
    from wtforms import RadioField

    class VoteForm(Form):
        deploy_preference  = RadioField('Deployment Preference', choices=[
            ('Web Site', 'Web Site'),
            ('Cloud Service', 'Cloud Service'),
            ('Virtual Machine', 'Virtual Machine')], default='Web Site')

### Fügen Sie die erforderlichen Importe zu views.py hinzu.

Fügen Sie die folgenden Importanweisungen am Anfang der Datei **views.py** hinzu. Dadurch werden die DocumentDB-Pakete für PythonSDK und Flask importiert.


    from forms import VoteForm
    import config
    import pydocumentdb.document_client as document_client
    

### Erstellen von Datenbank, Sammlung und Dokument

Fügen Sie den folgenden Code zu **views.py** hinzu. Dieser ist für die Erstellung der vom Formular verwendeten Datenbank zuständig. Löschen Sie keinen vorhandenen Code in **views.py**. Fügen Sie diesen am Ende ein.

    @app.route('/create')
    def create():
        """Renders the contact page."""
        client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
    
        # Attempt to delete the database.  This allows this to be used to recreate as well as create
        try:
            db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            client.DeleteDatabase(db['_self'])
        except:
            pass
    
        # Create database
        db = client.CreateDatabase({ 'id': config.DOCUMENTDB_DATABASE })
        # Create collection
        collection = client.CreateCollection(db['_self'],{ 'id': config.DOCUMENTDB_COLLECTION })
        # Create document
        document = client.CreateDocument(collection['_self'],
            { 'id': config.DOCUMENTDB_DOCUMENT,
            'Web Site': 0,
            'Cloud Service': 0,
            'Virtual Machine': 0,
            'name': config.DOCUMENTDB_DOCUMENT })
    
        return render_template(
            'create.html', 
            title='Create Page', 
            year=datetime.now().year,
            message='You just created a new database, collection, and document.  Your old votes have been deleted')


### Lesen von Datenbank, Sammlung und Dokument und Übermitteln des Formulars

Fügen Sie den folgenden Code zu **views.py** hinzu. Dieser ist für die Einrichtung des Formulars sowie das Lesen der Datenbank, der Sammlung und des Dokuments zuständig. Löschen Sie keinen vorhandenen Code in **views.py**. Fügen Sie diesen am Ende ein.
    
    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = VoteForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
            
            # Read databases and take first since id should not be duplicated.
            db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            
            # Read collections and take first since id should not be duplicated.
            coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.DOCUMENTDB_COLLECTION))
    
            # Read documents and take first since id should not be duplicated.
            doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.DOCUMENTDB_DOCUMENT))
    
            # Take the data from the deploy_preference and increment our database
            doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
            replaced_document = client.ReplaceDocument(doc['_self'], doc)
    
            # Create a model to pass to results.html
            class VoteObject:
                choices = dict()
                total_votes = 0
    
            vote_object = VoteObject()
            vote_object.choices = {
                "Web Site" : doc['Web Site'],
                "Cloud Service" : doc['Cloud Service'],
                "Virtual Machine" : doc['Virtual Machine']
            }
            vote_object.total_votes = sum(vote_object.choices.values())
    
            return render_template(
                'results.html', 
                year=datetime.now().year, 
                vote_object = vote_object)
    
        else :
            return render_template(
                'vote.html', 
                title = 'Vote',
                year=datetime.now().year,
                form = form)


### Erstellen der HTML-Dateien

Fügen Sie die folgenden HTML-Dateien zum Ordner "templates" hinzu: create.html, results.html, vote.html.

Fügen Sie den folgenden Code zur Datei **create.html** hinzu. Dieser Code ist für die Anzeige der Nachricht zuständig, dass eine neue Datenbank, eine neue Sammlung und ein neues Dokument erstellt wurde.

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}

Fügen Sie den folgenden Code zur Datei **results.html** hinzu. Dieser Code ist für die Anzeige der Wahlergebnisse zuständig.

    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
    <br />

    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
        <div class="col-sm-5">
            <div class="progress">
                <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" 
                     aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                    {{vote_object.choices[choice]}}
                </div>
            </div>
        </div>
    </div>
    {% endfor %}

    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}

Fügen Sie den folgenden Code zur Datei **vote.html** hinzu. Dieser Code ist für die Anzeige der Wahl und zum Akzeptieren der Stimmen zuständig. Beim Registrieren der Stimmen wird die Kontrolle an die Datei "views.py" übergeben, wo die abgegebene Stimme erkannt und das Dokument entsprechend angefügt wird.

    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
        {{form.deploy_preference}}
        <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}

Ersetzen Sie den Inhalt von **index.html** durch Folgendes. Dies dient als Startseite für Ihre Anwendung.

    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + DocumentDB Voting Application.</h2>
    <h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}


### Hinzufügen einer Konfigurationsdatei und Ändern von \_\_init\_\_.py

Klicken Sie mit der rechten Maustaste auf den Projektnamen "Lernprogramm", und fügen Sie die Datei **config.py** hinzu.
Diese Konfiguration ist für Formulare in Flask erforderlich. Sie können damit auch einen geheimen Schlüssel bereitstellen. Dies ist jedoch für dieses Lernprogramm nicht erforderlich. Fügen Sie folgenden Code zur Datei "config.py" hinzu:   Ändern Sie die Werte von **DOCUMENTDB\_HOST** und **DOCUMENTDB\_KEY**.

    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    
    DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
    
    DOCUMENTDB_DATABASE = 'voting database'
    DOCUMENTDB_COLLECTION = 'voting collection'
    DOCUMENTDB_DOCUMENT = 'voting document'


Ersetzen Sie den Inhalt von **\_\_init\_\_.py** durch Folgendes.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

Nachdem Sie die oben aufgeführten Schritte ausgeführt haben, sollte Ihr
Projektmappen-Explorer wie folgt aussehen.

![Alt text](./media/documentdb-python-application/image15.png)


## Schritt 4: Lokales Ausführen der Anwendung

Drücken Sie F5, oder klicken Sie in Visual Studio auf die Schaltfläche "Ausführen". Daraufhin sollte Folgendes auf Ihrem Bildschirm angezeigt werden.

![Alt text](./media/documentdb-python-application/image16.png)

Klicken Sie auf "Wahldatenbank erstellen/löschen", um die Datenbank zu erstellen.

![Alt text](./media/documentdb-python-application/image17.png)

Klicken Sie daraufhin auf "Abstimmen", und wählen Sie Ihre Option aus.

![Alt text](./media/documentdb-python-application/image18.png)

Für jede abgegebene Stimme wird der entsprechende Zähler inkrementiert.

![Alt text](./media/documentdb-python-application/image19.png)


## Schritt 5: Bereitstellen der Anwendung auf Azure-Websites

Nachdem eine vollständige, DocumentDB korrekt entgegenwirkende, Anwendung erstellt ist, stellen wir sie auf Azure Websites bereit. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt(stellen Sie sicher,dass es nicht lokal ausgeführt wird), und wählen Sie dann "Veröffentlichen" aus.  Wählen Sie dann "Microsoft Azure-Websites" aus.

![Alt text](./media/documentdb-python-application/image20.png)


Konfigurieren Sie die Azure-Website, indem Sie Ihre Anmeldeinformationen angeben und auf "Veröffentlichen" klicken.

![Alt text](./media/documentdb-python-application/image21.png)

Dann schließt Visual Studio die Veröffentlichung Ihrer Webanwendung in wenigen Sekunden ab und startet einen Browser, in dem das Ergebnis Ihrer Arbeit in Azure ausgeführt wird!


## Nächste Schritte

Glückwunsch! Sie haben Ihre erste Python-Anwendung mit Azure DocumentDB erstellt und auf Azure-Websites veröffentlicht.


  [Klicken Sie hier, um Flask-Lernprogramme zu öffnen]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

  [Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
  [hier]: http://aka.ms/ptvs
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [2]: https://www.python.org/downloads/windows/
  [3]: http://aka.ms/vcpython27 
  [Microsoft-Webplattform-Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [Azure-Verwaltungsportal]: http://portal.azure.com
<!--HONumber=47-->
