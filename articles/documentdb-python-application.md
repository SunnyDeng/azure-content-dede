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

<a name="_Toc395888515"></a><a name="_Toc395809324">Erstellen einer Webanwendung mit Python und Flask (MVC) mithilfe von DocumentDB</a>
===========================================================================================================================================

<a name="_Toc395809324">

<a name="_Toc395888516"></a><a name="_Toc395809325"></a><a name="_Toc389865467"></a><a name="_Toc389828008">Übersicht</a>
========================================================================================================================

<a name="_Toc395888517"></a><a name="_Toc395809326">Szenario</a>
----------------------------------------------------------------

Dieses Dokument umfasst eine vollständige Anleitung zur Erstellung einer Webanwendung
für eine Wahl mithilfe von Azure DocumentDB, um hervorzuheben, wie
Azure DocumentDB von Kunden effizient zum Speichern und Abfragen von JSON-Dokumenten genutzt werden
kann.

In dieser exemplarischen Vorgehensweise wird gezeigt, wie Sie den von Azure bereitgestellten Dienst DocumentDB
verwenden, um Daten von einer Python-Webanwendung zu speichern und zu nutzen, die auf Azure
gehostet werden. Dabei wird vorausgesetzt, dass Sie bereits über Erfahrung mit Python und
Azure-Websites verfügen.

Sie erhalten Informationen zu folgenden Themen:

1\. Erstellen und Bereitstellen eines DocumentDB-Kontos

2\. Erstellen einer Python MVC-Anwendung

3\. Herstellen einer Verbindung zu Azure DocumentDB über eine Webanwendung und anschließendes Verwenden von DocumentDB

4\. Bereitstellen der Webanwendung für Azure-Websites

Im Verlauf dieser exemplarischen Vorgehensweise erstellen Sie eine einfache Wahlanwendung, über die Sie
für eine Wahl abstimmen können.

![Alt text](./media/documentdb-python-application/image1.png)

<a name="_Toc395888520"></a><a name="_Toc395809329">Voraussetzungen</a>


Bevor Sie die Anweisungen in diesem Artikel befolgen, sollten Sie sicherstellen,
dass Folgendes installiert ist:

Visual Studio 2013 (oder [Visual Studio Express][], die kostenlose
Version)

Python Tools für Visual Studio, erhältlich [hier][]

Azure SDK für Visual Studio 2013, Version 2.4 oder höher, erhältlich
[hier][1]

Plattformübergreifende Azure-Befehlszeilentools, verfügbar über den [Microsoft
Webplattform-Installer][]

<a name="_Toc395888519"></a><a name="_Toc395809328">Erstellen eines DocumentDB-Datenbankkontos</a>
============================================================================================

Wenn Sie ein DocumentDB-Datenbankkonto in Azure bereitstellen möchten, öffnen Sie das
Azure-Verwaltungsportal, und klicken Sie auf die Azure Gallery-Kachel auf der
Homepage oder in der unteren linken Bildschirmecke auf das Pluszeichen ("+").

![Alt text](./media/documentdb-python-application/image2.png)


Dadurch wird die Azure Gallery geöffnet, in der Sie aus den zahlreichen
verfügbaren Azure-Diensten auswählen können. Wählen Sie in der Azure Gallery "Daten, Speicher und
Sicherung" aus der Liste der Kategorien aus.

![Alt text](./media/documentdb-python-application/image3.png)

Hier wählen Sie dann die Option für Azure DocumentDB aus.

![Alt text](./media/documentdb-python-application/image4.png)

Wählen Sie dann "Erstellen" im unteren Bildschirmbereich aus

![Alt text](./media/documentdb-python-application/image5.png)

Dadurch wird das Fenster "Neue DocumentDB" geöffnet, in dem Sie
Name, Region, Skalierung, Ressourcengruppe und andere Einstellungen für Ihr neues
Konto eingeben können.

![Alt text](./media/documentdb-python-application/image6.png)

Nachdem Sie die Werte für Ihr Konto bereitgestellt haben, klicken Sie auf "Erstellen",
damit der Bereitstellungsprozess mit der Erstellung Ihres Datenbankkontos beginnt.
Nachdem die Bereitstellung abgeschlossen ist, sollte im Benachrichtigungsbereich des Portals eine Benachrichtigung
angezeigt werden. Die Kachel auf Ihrer
Startseite (sofern deren Erstellung ausgewählt wurde) ändert sich daraufhin,
um auf den abgeschlossenen Vorgang hinzuweisen.

![Alt text](./media/documentdb-python-application/image7.png)

Nachdem die Bereitstellung abgeschlossen ist, können Sie auf der Startseite auf die DocumentDB-Kachel klicken,
um das Hauptfenster für dieses neu erstellte
DocumentDB-Konto zu eröffnen.

![Alt text](./media/documentdb-python-application/image8.png)
![Alt text](./media/documentdb-python-application/image9.png)



Greifen Sie über die Schaltfläche "Schlüssel" auf Ihre Endpunkt-URL und den primären Schlüssel zu,
kopieren Sie diese dann in die Zwischenablage, um sie dort griffbereit zu halten, da wir diese Werte in der
als nächstes zu erstellenden Webanwendung verwenden werden.

</a>

<a name="_Toc395888520"></a><a name="_Toc395809329">Erstellen einer neuen Python Flask-Webanwendung</a>
=================================================================================================

Öffnen Sie Visual Studio, Datei-\ > Neues Projekt-\ > Python-\ >
Flask-Webprojekt mit dem Namen **tutorial**. Sie werden dann gefragt, ob Sie
externe Pakete installieren möchten. Klicken Sie auf die Option zum Installieren in einer virtuellen Umgebung,
und klicken Sie dann auf "Erstellen". Dadurch wird die erforderliche virtuelle Python-Umgebung
für das Projekt eingerichtet.

Bei Flask handelt es sich um ein Webframework, das uns beim schnelleren Erstellen von Webanwendungen
in Python unterstützt. [Klicken Sie hier, um Flask-Lernprogramme zu öffnen][].

![Alt text](./media/documentdb-python-application/image10.png)

<a name="_Toc395888520"></a><a name="_Toc395809329">Hinzufügen von Flask-Paketen zum Projekt</a>
==================================

Nachdem das Projekt eingerichtet ist, müssen Sie bestimmte Flask-Pakete hinzufügen, die
für unser Projekt erforderlich sind, z. B. Formulare. Klicken Sie mit der rechten Maustaste auf **env**,
und **installieren Sie die folgenden Python-Pakete (die Einhaltung der Reihenfolge ist
wichtig)**:

    flask==0.9
    flask-login
    flask-openid
    flask-mail==0.7.6
    sqlalchemy==0.7.9
    flask-sqlalchemy==0.16
    sqlalchemy-migrate==0.7.2
    flask-whooshalchemy==0.55a
    flask-wtf==0.8.4
    pytz==2013b
    flask-babel==0.8
    flup

![Alt text](./media/documentdb-python-application/image11.png)

**Hinweis:** In seltenen Fällen wird eventuell ein Fehler im Ausgabefenster angezeigt. Wenn
dies geschieht, prüfen Sie, ob der Fehler mit der Bereinigung zusammenhängt. Gelegentlich schlägt
die Bereinigung fehl, aber die Installation verläuft dennoch erfolgreich (blättern Sie
im Ausgabefenster nach oben, um dies zu prüfen).
<a name="verify-the-virtual-environment"></a> In diesem Fall ist es in Ordnung,wenn Sie fortfahren.

</h1>
<a name="_Toc395888522"></a><a name="_Toc395809331">Überprüfen der virtuellen Umgebung</a>
======================================================================================


Stellen Sie sicher, dass alles einwandfrei installiert ist. Drücken Sie
**F5**, um die Website zu starten. Dadurch wird der Flask-Entwicklungsserver
und anschließend der Webbrowser gestartet. Daraufhin sollte die folgende Seite angezeigt werden:

![Alt text](./media/documentdb-python-application/image12.png)

<a name="_Toc395888523"></a><a name="_Toc395809332">Hinzufügen von DocumentDB zum Projekt</a>
=========================================================================================

Das DocumentDB Python SDK wird in PyPi gehostet und kann mit "pip" installiert
werden.

Erweitern Sie den Knoten für Python-Umgebungen im Projektmappen-Explorer, klicken Sie dann mit der rechten Maustaste auf
Ihre Umgebung, und wählen Sie anschließend "Python-Paket installieren..." aus.

![Alt text](./media/documentdb-python-application/image13.png)

Geben Sie "--pre pydocumentdb" ein. Hierbei handelt es sich um den Namen des PyPi-Pakets. Sie können
optional eine bekannte Version bereitstellen, wenn Sie über die
zu installierende Version entscheiden möchten. Wenn Sie die Version nicht angeben, wird sichergestellt,
dass die neueste stabile Version installiert wird. Beachten Sie, dass Sie den gesamten Namen
eingeben müssen: "--pre pydocumentdb".

![Alt text](./media/documentdb-python-application/image14.png)

Dadurch wird das Paket "pydocumentdb" heruntergeladen und in Ihrer
Umgebung installiert. Anschließend sollte "pydocumentdb" als Modul unter Ihrer
Umgebung aufgeführt werden.

</h1>
<a name="_Toc395888524"></a><a name="_Toc395809333">Erstellen der Datenbank-, Sammlungs- und Dokumentdefinition</a>
============================================================================================================

Fügen Sie die folgende Python-Datei, **forms.py**  im Projektmappen-Explorer zum
Ordner "tutorial" hinzu. Fügen Sie den folgenden Code zur Datei
"forms.py" hinzu. Wir erstellen unsere Wahlanwendung. Dazu werden
 drei boolesche Felder erstellt, die auf Basis der Benutzereingabe gewechselt werden.

</h1>

    #forms.py
    from flask.ext.wtf import Form
    from wtforms import TextField, BooleanField
    from wtforms.validators import Required
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors
    class LoginForm(Form):
        openid = TextField('openid')
        remember_me = BooleanField('remember_me', default = False)
        remember_me1 = BooleanField('remember_me1', default = False)
        remember_me2 = BooleanField('remember_me2', default = False)

<a name="_Toc395888520"></a>
============================

### <a name="_Toc395809338">Erstellen von Datenbank, Sammlung und Dokument</a>

\#Hinweis: Es wird als sicherer erachtet, Ihre Anmeldeinformationen
für die Authentifizierung in einer Konfigurationsdatei und nicht in der Anwendung zu speichern. Der Einfachheit halber legen wir
sie im Quellcode ab. Erstellen Sie eine neue Funktion der Datei "views.py", und nennen Sie
sie "create". Fügen Sie Folgendes zu **views.py** hinzu. Löschen Sie keinen
vorhandenen Code.

</h1>

    @app.route('/create')
    def create():
        """Renders the contact page."""
        host = 'https://meetdemo.documents.azure.com:443/'
        masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
        client = document_client.DocumentClient(host, {'masterKey': masterKey})
        databases = client.ReadDatabases().ToArray()
        #delete any existing databases for simplicity
        for database in databases:
            client.DeleteDatabase(database['_self'])
        #create database
        db = client.CreateDatabase({ 'id': 'sample database' })
        # create collection
        collection = client.CreateCollection(db['_self'],{ 'id': 'sample collection' })
        # create document
        document = client.CreateDocument(collection['_self'],
        { 'id': 'sample document',
        'Web Site': '0 votes',
        'Cloud Service': '0 votes',
        'Virtual Machine': '0 votes',
        'name': 'sample document' })
        return render_template('create.html',title='Create Page',year=datetime.now().year,
        message='You just created a new database - sample database a collection - sample collection and a document - sample document that has your votes. Your old votes have been deleted')

<a name="_Toc395888529"></a><a name="_Toc395809338">Akzeptieren der Wahl des Benutzers und Aktualisieren des Dokuments</a>
=================================================================================================

### <a name="_Toc395809338">Hinzufügen der erforderlichen Importe

<a name="_Toc395888529"></a>
============================

Fügen Sie die folgenden Importanweisungen am Anfang der Datei **views.py** hinzu. Dadurch
werden die DocumentDB-Pakete für PythonSDK und Flask importiert.


    from wtforms import Form, BooleanField, TextField, PasswordField, validators
    from forms import LoginForm
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors

### <a name="_Toc395809338">Lesen von Datenbank, Sammlung und Dokument</a>

Fügen Sie den folgenden Code zu **views.py** hinzu. Dieser ist für die Einrichtung
des Formulars, das Lesen der Datenbank, der Sammlung und des Dokuments zuständig. Löschen Sie in der
Datei "views.py" keinen vorhandenen Code. Fügen Sie diesen Code einfach am Ende der Datei hinzu.

    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = LoginForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            host = 'https://meetdemo.documents.azure.com:443/'
            masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
            client = document_client.DocumentClient(host, {'masterKey': masterKey})
            databases = client.ReadDatabases().ToArray()   #Read database
            db =databases[0] #For simplicity we are assuming there is only one database
            collections = client.ReadCollections(db['_self']).ToArray() #Read collection
            coll =collections[0] #For simplicity we are assuming there is only one collection
            documents = client.ReadDocuments(coll['_self']).ToArray() #Read document
            doc = documents[0]   #For simplicity we are assuming there is only document
    
### <a name="_Toc395809338">Registering the vote and modifying the document</a>

            replaced_document = doc
            if form.remember_me.data:
                print 'choice 1'             
                setvar = doc['Web Site']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Web Site'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
                print replaced_document
            elif form.remember_me1.data:
                print 'choice 2'             
                setvar = doc['Cloud Service']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Cloud Service'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
            else:
                print 'choice 2'             
                setvar = doc['Virtual Machine']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Virtual Machine'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)

### <a name="_Toc395809338">Anzeigen der Ergebnisse</a>

    return render_template('results.html', 
            title = 'Website = ' + replaced_document['Web Site'] + '\n' +'Cloud Service = ' + replaced_document['Cloud Service'] + '\n' + 'Virtual Machine = ' + replaced_document['Virtual Machine'])
        else :        
            return render_template('vote.html', 
            title = 'Vote',
            form = form,
            providers = app.config['OPENID_PROVIDERS'])

    @app.route('/results')
    def results():
        """Renders the results page."""
        return render_template(
            'results.html',
            title='Results',
            year=datetime.now().year,
            message='Your application description page.')


### <a name="_Toc395809338">Erstellen der HTML-Dateien</a>

Fügen Sie die folgenden HTML-Dateien zum Ordner "templates" hinzu:
create.html, results.html, vote.html.

Fügen Sie den folgenden Code zur Datei **create.html** hinzu. Dieser Code ist für die Anzeige
der Nachricht zuständig, dass eine neue Datenbank, eine neue Sammlung und ein neues Dokument erstellt wurden.

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Click here to Vote &raquo;</a></p>
    {% endblock %}

Fügen Sie den folgenden Code zur Datei **results.html** hinzu. Dieser Code ist für die Anzeige
der Wahlergebnisse zuständig.
    
    {% extends "layout.html" %}
    {% block content %}
    <h3>Results of the vote</h3>
    <pre>{{ title }}</pre>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote Again &raquo;</a></p>
    {% endblock %}

Fügen Sie den folgenden Code zur Datei **vote.html** hinzu. Dieser Code ist für die Anzeige
der Wahl und zum Akzeptieren der Stimmen zuständig. Beim Registrieren der Stimmen wird die Kontrolle
an die Datei "views.py" übergeben, wo die abgegebene Stimme erkannt und
das Dokument entsprechend angefügt wird.

    {% extends "layout.html" %}
    {% block content %}
    <h1>What is your favorite way to host an application on Azure?</h1>
    <form action="" method="post" name="login">
    {{form.hidden_tag()}}
    <p> </p>
        {{form.remember_me}} Website
        {{form.remember_me1}} Cloud
        {{form.remember_me2}} Virtual Machine
        <p><input type="submit" value="Cast your vote"></p>
    </form>
    {% endblock %}
    
<a name="_Toc395888529"></a>
============================

Löschen Sie den Code in der Datei **layout.html**, und ersetzen Sie ihn durch den folgenden Code. Dieser
ist für die Einrichtung von "navbar" sowie für die entsprechenden URLs für die Weiterleitung zuständig.

Löschen Sie den Code in der Datei "index.html", und ersetzen Sie ihn durch den folgenden Code. Dieser
Code dient als Zielseite für Ihre Anwendung.

</h1>
<a name="_Toc395888532"></a><a name="_Toc395809341">Hinzufügen einer Konfigurationsdatei und Ändern von __init__.py</a>
----------------------------------------------------------------------------------------------------------------

Klicken Sie mit der rechten Maustaste auf den Projektnamen "tutorial", und fügen Sie die Datei **config.py** hinzu.
Diese Konfiguration ist für Formulare in Flask erforderlich. Sie können damit auch einen
geheimen Schlüssel bereitstellen. Fügen Sie folgenden Code zur Datei "config.py" hinzu:
    
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    OPENID_PROVIDERS = [
       ]

Fügen Sie die Datei **\_\_init\_\_.py ebenso hinzu**. Suchen Sie die Datei im
Ordner "tutorial". Ersetzen Sie den ursprünglichen durch den folgenden Code. Dieser
Code ist für die Verbindung zwischen den Ansichten und der Datei "config.py" zuständig.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

Nachdem Sie die oben aufgeführten Schritte ausgeführt haben, sollte Ihr
Projektmappen-Explorer wie folgt aussehen.

![Alt text](./media/documentdb-python-application/image15.png)

<a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Lokales Ausführen der Anwendung</a>
============================

Drücken von F5, oder klicken Sie in Visual Studio auf die Schaltfläche "Ausführen". Daraufhin
sollte Folgendes auf Ihrem Bildschirm angezeigt werden.

![Alt text](./media/documentdb-python-application/image16.png)

Klicken Sie auf "vote", und wählen Sie Ihre Option aus.

![Alt text](./media/documentdb-python-application/image17.png)

Für jede abgegebene Stimme wird der entsprechende Zähler inkrementiert. Wenn
Sie das nächste Mal eine Stimme abgeben, können Sie die Ergebnisse sehen.

![Alt text](./media/documentdb-python-application/image18.png)

</h1>
<a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Bereitstellen der Anwendung auf Azure-Websites</a>
========================================================================================================================

Nachdem die fertige Anwendung für DocumentDB ordnungsgemäß funktioniert,
wird sie auf Azure-Websites bereitgestellt. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste
auf das Projekt(stellen Sie sicher, dass es nicht lokal
ausgeführt wird), und wählen Sie dann "Veröffentlichen" aus.

![Alt text](./media/documentdb-python-application/image19.png)


Konfigurieren Sie die Azure-Website, indem Sie Ihre Anmeldeinformationen bereitstellen und
eine neue Website erstellen bzw. eine alte Website wiederverwenden.

![Alt text](./media/documentdb-python-application/image20.png)


Dann schließt Visual Studio die Veröffentlichung Ihrer Webanwendung in wenigen Sekunden ab
und startet einen Browser, in dem das Ergebnis Ihrer Arbeit
in Azure ausgeführt wird!

</h1>
<a name="_Toc395809338">Zusammenfassung</a>
==========

Glückwunsch! Sie haben Ihre erste Python-Anwendung mit Azure DocumentDB erstellt und
auf Azure-Websites veröffentlicht.

Die vollständige Lösung finden Sie [hier][]. (Hinweis: Sie müssen
noch die virtuelle Umgebung hinzufügen und die oben erwähnten Python-Tools
und -Pakete installieren)

</h1>

  [Klicken Sie hier]: http://go.microsoft.com/fwlink/?LinkID=509840&clcid=0x409
  [Klicken Sie hier, um Flask-Lernprogramme zu öffnen.]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

  [Visual Studio Express]: http://www.visualstudio.com/de-de/products/visual-studio-express-vs.aspx
  [starten]: https://pytools.codeplex.com/releases/view/123624
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [Microsoft-Webplattform-Installer]: http://www.microsoft.com/web/downloads/platform.aspx

<!--HONumber=46--> 
