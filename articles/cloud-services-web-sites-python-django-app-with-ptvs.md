<properties linkid="develop-python-tutorials-django-with-python-tools-for-visual-studio" urlDisplayName="Django with Python Tools for Visual Studio 2.0" pageTitle="Creating Django applications with Python Tools for Visual Studio 2.0" metaKeywords="" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a SQL Database or MySQL database instance and can be deployed to a website or cloud service." metaCanonical="" services="web-sites,cloud-services" documentationCenter="Python" title="Creating Django applications with Python Tools 2.0 for Visual Studio" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Erstellen von Django-Anwendungen mit Python Tools 2.0 für Visual Studio

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>In diesem Lernprogramm werden Python Tools 2.0 f&uuml;r Visual Studio verwendet, um eine einfache Django-Anwendung zu erstellen. Mit der Anwendung k&ouml;nnen die Benutzer bei Umfragen abstimmen. Zuerst wird eine lokale sqlite3-Datenbank verwendet, danach eine SQL Server- oder MySQL-Datenbank in Azure. Wir zeigen, wie die Django-Administratoroberfl&auml;che aktiviert wird und wie damit Umfragen zu unserer Datenbank hinzugef&uuml;gt werden. Au&szlig;erdem wird die in Visual Studio integrierte Django-Shell verwendet.  Zum Schluss wird die Anwendung auf einer Azure-Website und in einem Azure-Cloud-Dienst bereitgestellt.</p>
<p>Wenn Sie lieber ein Video zu diesem Thema ansehen m&ouml;chten, k&ouml;nnen Sie den Clip auf der rechten Seite ausw&auml;hlen. In diesem werden dieselben Schritte behandelt wie in diesem Lernprogramm.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/python/videos/django-tutorial-180x120.png') !important;" href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a> <span class="time">33:08</span></div>

</div>

In diesem Lernprogramm stehen Python-Tools für Visual Studio und Azure im Mittelpunkt. Weitere Details zu Django und der in diesem Lernprogramm erstellten Umfrageanwendung finden Sie unter [][]<https://www.djangoproject.com/></a>.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Anforderungen

Zum Durchführen des Lernprogramms benötigen Sie Folgendes:

-   [Python Tools 2.0 für Visual Studio][Python Tools 2.0 für Visual Studio]
-   [Python 2.7 (32 Bit)][Python 2.7 (32 Bit)]
-   Visual Studio und Azure-SDK:
  -   VS 2010 Pro oder höher mit Azure SDK 2.1
  -   VS 2012 Pro oder höher mit Azure SDK 2.1, 2.2 oder höher
  -   VS 2013 Pro oder höher mit Azure SDK 2.2 oder höher
  -   Kostenlose VS 2013 Integrated Shell. **Das Azure-SDK unterstützt die Integrated Shell nicht**. Mit Visual Studio können Sie Django-Anwendungen zwar lokal mit der kostenlosen Integrated Shell entwickeln, debuggen und ausführen, Sie können sie jedoch nicht auf Azure-Websites oder in Azure-Cloud-Diensten veröffentlichen.

Verwenden Sie den Webplattform-Installer, um das Azure-SDK zu installieren. Damit werden das SDK, der Emulator und Azure-Tools für Visual Studio installiert. Suchen Sie im Webplattform-Installer nach **Azure SDK for .NET**, und wählen Sie eine der für Ihre Version von Visual Studio unterstützten Versionen des SDK aus.

**Hinweis:** Um diese Anwendung erfolgreich mithilfe einer Azure-Website oder eines Cloud-Diensts auszuführen, müssen Sie die offizielle CPython 2.7-Distribution von [python.org][Python 2.7 (32 Bit)] verwenden. Anderen Distributionen funktionieren möglicherweise, werden jedoch nicht offiziell unterstützt.

## Herunterladen eines vorhandenen Projekts

Wenn Sie im Lernprogramm vorwärts springen möchten, können Sie [den Quellcode für dieses Projekt herunterladen][den Quellcode für dieses Projekt herunterladen].

Die sqlite3-Datenbank wurde bereits mit den folgenden Anmeldeinformationen für den Superuser erstellt:

    Username: tutorial
    Password: azure

Der Download umfasst KEINE virtuelle Umgebung. Erstellen Sie eine solche Umgebung, indem Sie die Schritte im Abschnitt [Erstellen einer virtuellen Umgebung][Erstellen einer virtuellen Umgebung] ausführen. Sobald dies geschehen ist, ist das Projekt bereit für den Abschnitt [Debuggen][Debuggen].

## Erstellen des Projekts

Python-Tools für Visual Studio unterstützt Python Virtual Environments. Wir erstellen ein Django-Projekt und verwenden eine virtuelle Umgebung, um die Abhängigkeiten zu installieren. Dies ist die empfohlene Methode zum Einrichten von Projekten, die auf Azure-Websites oder in Cloud-Diensten veröffentlicht werden.

1.  Öffnen Sie Visual Studio, Datei/Neues Projekt, Django-Anwendung mit dem Namen **tutorial**.

    ![Neues Projekt][Neues Projekt]

**Hinweis:** Im Projektmappen-Explorer wird unter "Referenzen" ein Knoten für Django 1.4 angezeigt. Dieser wird für die Bereitstellung des Azure-Cloud-Diensts verwendet, um Python und Django auf dem Zielcomputer zu installieren. Löschen Sie den Verweis auf Django 1.4 nicht aus dem Verweishinweis. Da wir eine virtuelle Umgebung verwenden und unser eigenes Django-Paket darin installieren, wird das in unserer virtuellen Umgebung installierte Django-Paket verwendet.

## <a name="creating-a-virtual-environment"></a>Erstellen einer virtuellen Umgebung

Wir verwenden eine virtuelle Umgebung, um unsere Abhängigkeiten zu installieren. Dies empfiehlt sich bei jeder Python-Anwendung und ist erforderlich für die Veröffentlichung in Azure.

1.  Erstellen Sie eine neue virtuelle Umgebung. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Python Environments**, und wählen Sie **Add Virtual Environment**.

    ![Add Virtual Environment][Add Virtual Environment]

2.  Wählen Sie Python 2.7 als Python-Basisübersetzer, und akzeptieren Sie den vorgegebenen Namen **env**. PTVS installiert pip und/oder virtualenv, wenn diese noch nicht installiert sind.

3.  Klicken Sie mit der rechten Maustaste auf **env** und auf **Install Python Package**: **django**

    ![Django installieren][Django installieren]

4.  Da Django zahlreiche Dateien umfasst, dauert die Installation eine Weile. Sie können den Fortschritt im Ausgabefenster sehen.

    ![Ausgabe bei der Installation von Django][Ausgabe bei der Installation von Django]

    **Hinweis:** In seltenen Fällen wird eventuell ein Fehler im Ausgabefenster angezeigt. Wenn dies geschieht, prüfen Sie, ob der Fehler mit der Bereinigung zusammenhängt. Gelegentlich schlägt die Bereinigung fehl, aber die Installation verläuft dennoch erfolgreich (blättern Sie im Ausgabefenster nach oben, um dies zu prüfen). Der Grund dafür ist, dass PTVS bei den neu erstellten temporären Dateien/Ordnern eine Sperre erhält, sodass sie im pip-Bereinigungsschritt nicht gelöscht werden können.

5.  Klicken Sie mit der rechten Maustaste auf **env** und auf **Install Python Package**: **pytz** (optional, jedoch empfohlen; wird von Django für die Unterstützung von Zeitzonen verwendet)

## Überprüfen der virtuellen Umgebung

1.  Stellen Sie sicher, dass alles einwandfrei installiert ist. Starten Sie die Website mit **F5** oder **STRG+F5**. Dadurch werden der Django-Entwicklungsserver und der Webbrowser gestartet. Daraufhin sollte die folgende Seite angezeigt werden:

    ![Django-Webbrowser][Django-Webbrowser]

## Erstellen der Umfrageanwendung

In diesem Abschnitt fügen wir eine Anwendung hinzu, mit der das Abstimmen bei Umfragen verarbeitet wird.

Ein Django-Projekt kann mehrere Anwendungen enthalten. In diesem Lernprogramm heißt unser Projekt "tutorial", und es entspricht dem Visual Studio-Projekt. Der Name der Anwendung, die wir hinzufügen, lautet **polls**, und dies ist zugleich der Name eines Ordners unter dem Projektknoten.

1.  Wählen Sie den **Projektnoten** und **Hinzufügen**-\>**Django-App** mit dem Namen **polls** aus. Dadurch wird ein Ordner für die Anwendung erstellt Codebausteinen für häufig verwendete Anwendungsdateien.

    ![Django-App hinzufügen][Django-App hinzufügen]

2.  Fügen Sie in **tutorial/settings.py** Folgendes zu **INSTALLED\_APPS** hinzu:

        'polls',

3.  und heben Sie unter **INSTALLED\_APPS** die Auskommentierung auf für:

        'django.contrib.admin',

4.  Ersetzen Sie **tutorial/urls.py** durch den folgenden Code:

        from django.conf.urls import patterns, include, url

        from django.contrib import admin
        admin.autodiscover()

        urlpatterns = patterns('',
            url(r'^', include('polls.urls', namespace="polls")),
            url(r'^admin/', include(admin.site.urls)),
        )

5.  Ersetzen Sie **polls/models.py** durch den folgenden Code:

        from django.db import models

        class Poll(models.Model):
            question = models.CharField(max_length=200)
            pub_date = models.DateTimeField('date published')

            def __unicode__(self):
                return self.question

        class Choice(models.Model):
            poll = models.ForeignKey(Poll)
            choice_text = models.CharField(max_length=200)
            votes = models.IntegerField(default=0)

            def __unicode__(self):
                return self.choice_text

6.  Ersetzen Sie **polls/views.py** durch den folgenden Code:

        from django.shortcuts import get_object_or_404, render
        from django.http import HttpResponseRedirect
        from django.core.urlresolvers import reverse
        from polls.models import Choice, Poll

        def vote(request, poll_id):
            p = get_object_or_404(Poll, pk=poll_id)
            try:
                selected_choice = p.choice_set.get(pk=request.POST['choice'])
            except (KeyError, Choice.DoesNotExist):
                # Redisplay the poll voting form.
                return render(request, 'polls/detail.html', {
                    'poll': p,
                    'error_message': "You didn't select a choice.",
                })
            else:
                selected_choice.votes += 1
                selected_choice.save()
                # Always return an HttpResponseRedirect after successfully dealing
                # with POST data. This prevents data from being posted twice if a
                # user hits the Back button.
                return HttpResponseRedirect(reverse('polls:results', args=(p.id,)))

7.  Fügen Sie die neue Python-Datei **polls/urls.py** mit dem folgenden Code hinzu:

        from django.conf.urls import patterns, url
        from django.views.generic import DetailView, ListView
        from polls.models import Poll

        urlpatterns = patterns('',
            url(r'^$',
                ListView.as_view(
                    queryset=Poll.objects.order_by('-pub_date')[:5],
                    context_object_name='latest_poll_list',
                    template_name='polls/index.html'),
                name='index'),
            url(r'^(?P<pk>\d+)/$',
                DetailView.as_view(
                    model=Poll,
                    template_name='polls/detail.html'),
                name='detail'),
            url(r'^(?P<pk>\d+)/results/$',
                DetailView.as_view(
                    model=Poll,
                    template_name='polls/results.html'),
                name='results'),
            url(r'^(?P<poll_id>\d+)/vote/$', 'polls.views.vote', name='vote'),
        )

8.  Erstellen Sie ein neues Objekt **polls/admin.py** mit dem folgenden Code:

        from django.contrib import admin
        from polls.models import Choice, Poll

        class ChoiceInline(admin.TabularInline):
            model = Choice
            extra = 3

        class PollAdmin(admin.ModelAdmin):
            fieldsets = [
                (None,               {'fields': ['question']}),
                ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
            ]
            inlines = [ChoiceInline]
            list_display = ('question', 'pub_date')
            list_filter = ['pub_date']
            search_fields = ['question']
            date_hierarchy = 'pub_date'

        admin.site.register(Poll, PollAdmin)

9.  Erstellen Sie unter dem Ordner **polls/templates** einen neuen Ordner mit dem Namen **polls**.

10. Verschieben Sie die Datei **polls/templates/index.html** per Drag & Drop oder mit Ausschneiden/Einfügen in den Ordner **polls/templates/polls**.

11. Ersetzen Sie **polls/templates/polls/index.html** durch den folgenden Markup-Code:

        <html>
        <head></head>
        <body>
        {% if latest_poll_list %}
            <ul>
            {% for poll in latest_poll_list %}
                <li><a href="{% url 'polls:detail' poll.id %}">{{ poll.question }}</a></li>
            {% endfor %}
            </ul>
        {% else %}
            <p>No polls are available.</p>
        {% endif %}
        </body>
        </html>

12. Erstellen Sie eine neue Django-HTML-Vorlage **polls/template/polls/detail.html** mit folgendem Inhalt:

        <html>
        <head></head>
        <body>
        <h1>{{ poll.question }}</h1>
        {% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}
        <form action="{% url 'polls:vote' poll.id %}" method="post">
        {% csrf_token %}
        {% for choice in poll.choice_set.all %}
            <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
            <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br />
        {% endfor %}
        <input type="submit" value="Vote" />
        </form>
        </body>
        </html>

13. Erstellen Sie eine neue Django-HTML-Vorlage **polls/template/polls/results.html** mit folgendem Code:

        <html>
        <head></head>
        <body>
        <h1>{{ poll.question }}</h1>
        <ul>
        {% for choice in poll.choice_set.all %}
            <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
        {% endfor %}
        </ul>
        <a href="{% url 'polls:detail' poll.id %}">Vote again?</a>
        </body>
        </html>

14. Das Projekt enthält jetzt die folgenden Dateien:

    ![Projektmappen-Explorer][Projektmappen-Explorer]

## Lokales Erstellen einer sqlite3-Datenbank

Unsere Webanwendung ist nahezu einsatzbereit, zuerst müssen wir jedoch eine Datenbank konfigurieren. Um die Website lokal zu testen, erstellen wir eine sqlite3-Datenbank. Dies ist eine sehr einfache Datenbank, die keine zusätzliche Installation erfordert. Die Datenbankdatei wird im Projektordner erstellt.

1.  Fügen Sie in **tutorial/settings.py** die folgende Importanweisung am Anfang der Datei hinzu:

        from os import path

2.  Fügen Sie nach der Importzeile am Anfang der Datei die folgende Definition hinzu:

        PROJECT_ROOT = path.dirname(path.abspath(path.dirname(__file__)))

3.  Ändern Sie den Code im Abschnitt DATABASES folgendermaßen:

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.sqlite3',
                'NAME': path.join(PROJECT_ROOT, 'db.sqlite3'),
                'USER': '',
                'PASSWORD': '',
                'HOST': '',
                'PORT': '',
            }
        }

4.  Klicken Sie mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Django**-\>**Django Sync DB**. Ein interaktives Django-Verwaltungsfenster wird angezeigt. Da die Datenbank noch nicht vorhanden ist, werden Sie aufgefordert, Administratoranmeldeinformationen zu erstellen. Geben Sie einen Benutzernamen und ein Kennwort ein. Die E-Mail-Adresse ist optional.

    ![Django Sync DB][Django Sync DB]

5.  Starten Sie die Website mit F5 oder STRG-F5. Dadurch werden der Django-Entwicklungsserver und der Webbrowser gestartet. Der Stamm-URL für die Website zeigt den Index der Umfragen an, die Datenbank enthält jedoch noch keine Indizes.

    ![Webbrowser][Webbrowser]

6.  Navigieren Sie zu **<http://localhost>:{Port}/admin**. Die Portnummer finden Sie im Konsolenfenster des Entwicklungsservers. Melden Sie sich mit den im vorherigen Schritt erstellten Anmeldeinformationen an.

    ![Umfrage hinzufügen][Umfrage hinzufügen]

7.  Verwenden Sie die Administratoroberfläche, um eine oder zwei Umfragen hinzuzufügen. Verwenden Sie nicht zu viel Zeit damit, Umfragen zur lokalen Datenbank hinzuzufügen. Wir wechseln später zu einer Cloud-Datenbank und füllen die Datenbank dann mit Daten.

    ![Umfrageindex][Umfrageindex]

8.  Navigieren Sie zu **<http://localhost>:{Port}/**. Sie sehen einen Index der hinzugefügten Umfragen.

    ![][]

9.  Klicken Sie auf eine der Umfragen, um zur Abstimmungsseite zu gelangen.

    ![Umfragedetail][Umfragedetail]

10. Wenn Sie Ihre Stimme abgeben, werden Sie danach zur Ergebnisseite weitergeleitet. Dort sehen Sie, dass sich die Anzahl der Stimmen erhöht hat.

    ![Umfrageergebnisse][Umfrageergebnisse]

## Verwenden von Stylesheets und anderen statischen Dateien

In diesem Abschnitt aktualisieren wir das Erscheinungsbild unserer Website mithilfe eines Stylesheets. Statische Dateien wie Stylesheets werden unterschiedlich behandelt und müssen am richtigen Ort gespeichert werden.

1.  Suchen Sie in **tutorial/settings.py** nach der Zuordnung von **STATIC\_ROOT**, und ändern Sie sie folgendermaßen:

        STATIC_ROOT = path.join(PROJECT_ROOT, 'static').replace('\','/')

2.  Erstellen Sie unter dem Ordner **polls** einen neuen Ordner mit dem Namen **static**.

3.  Erstellen Sie unter dem Ordner **polls/static** einen neuen Ordner mit dem Namen **polls**.

4.  Erstellen Sie unter dem Ordner **polls/static/polls** einen neuen Ordner mit dem Namen **images**.

5.  Fügen Sie eine neue Stylesheet-Datei **polls/static/polls/style.css** mit folgendem Inhalt hinzu:

        body {
            color: darkblue;
            background: white url("images/background.jpg");
        }

6.  Fügen Sie dem Ordner **polls/static/polls/images** ein Bild hinzu, und nennen Sie es **background.jpg**.

7.  Das Projekt enthält jetzt die folgenden Dateien:

    ![Projektmappen-Explorer][1]

8.  Fügen Sie der Kopfzeile aller Vorlagen folgendes Markup hinzu, sodass sie auf das Stylesheet verweisen:

        <head>
        {% load staticfiles %}
        <link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}" />
        </head>

9.  Führen Sie die Website erneut aus. Die Seiten für Index, Umfrage und Ergebnisse verwenden das erstellte Stylesheet mit dunkelblauem Text und einem Hintergrundbild.

    ![Umfragedetail][2]

## <a name="debugging"></a>Debuggen

Python-Tools für Visual Studio verfügt über eine spezielle Unterstützung zum Debuggen von Django-Vorlagen.

1.  Öffnen Sie **polls/templates/polls/index.html**, und setzen Sie mit **F9** in der folgenden Zeile einen Breakpoint:

        {% if latest_poll_list %}

2.  Starten Sie das Debuggen mit **F5**. Visual Studio unterbricht in der Vorlagendatei.

3.  Wenn Sie das **Fenster 'Lokal'** über **Debug-\>Windows-\>Lokal** öffnen, sehen Sie die Variable **latest\_poll\_list** und deren Wert.

4.  Sie können **F10** drücken, um wie in normalem Python-Code schrittweise vorwärts zu gehen. Innerhalb der for-Schleife können Sie den Wert von **poll** prüfen:

    ![Debuggen][3]

## Erstellen einer Datenbank in Azure

Nachdem wir überprüft haben, dass unsere Umfrageanwendung lokal funktioniert, wollen wir nun dazu übergehen, eine in Azure gehostete Datenbank zu verwenden.

In den beiden folgenden Abschnitten wird gezeigt, wie eine SQL-Datenbank und eine MySQL-Datenbank verwendet wird. Bei beiden handelt es sich um gehostete Dienste.

Eine weitere Option wäre die Erstellung eines virtuellen Computers und die Installation eines Datenbankservers. Anweisungen zum Einrichten von MySQL auf einem virtuellen Computer mit Azure Linux finden Sie [hier][hier].

**Hinweis:** Es ist möglich, eine sqlite3-Datenbank in Azure zu verwenden (nur für Entwicklungszwecke, vom Einsatz in einer Produktionsumgebung wird abgeraten). Sie müssen Ihrem Projekt die Datei **db.sqlite3** hinzufügen, um die Datenbank mit der Django-Anwendung bereitzustellen.

### SQL-Datenbank

In diesem Abschnitt erstellen wir eine SQL-Datenbank in Azure, fügen die erforderlichen Pakete zu unserer virtuellen Umgebung hinzu und ändern die Einstellungen, um die neue Datenbank zu verwenden.

1.  Klicken Sie im Azure-Verwaltungsportal auf **SQL-DATENBANKEN**.

2.  Erstellen Sie zunächst einen Server zum Hosten der Datenbank. Wählen Sie **SERVER** aus, und klicken Sie auf **HINZUFÜGEN**.

3.  Auf der Registerkarte **KONFIGURIEREN** des neu erstellten Servers wird IP-Adresse des aktuellen Clients angezeigt. Klicken Sie daneben auf **ADD TO THE ALLOWED IP ADDRESSES**.

    **Hinweis:** Gelegentlich erkennt Azure die IP-Adresse des Clients nicht richtig. Wenn Sie beim Synchronisieren der Datenbank eine Fehlermeldung erhalten, sollten Sie die IP-Adresse aus der Fehlermeldung kopieren/einfügen und zu den erlaubten IP-Adressen hinzufügen.

4.  Anschließend erstellen wir die Datenbank. Klicken Sie auf der Registerkarte **DATENBANKEN** unten in der Leiste auf **HINZUFÜGEN**.

5.  In Visual Studio installieren wir die Pakete, die für den Zugriff auf SQL Server-Datenbanken über Django benötigt werden, in unserer virtuellen Umgebung.

6.  Klicken Sie mit der rechten Maustaste auf **env** und auf **Install Python Package**: **pyodbc** mithilfe von **easy\_install**.

7.  Klicken Sie mit der rechten Maustaste auf **env** und auf **Install Python Package**: **django-pyodbc-azure** mithilfe von **pip**.

8.  Bearbeiten Sie die Datei **tutorial/settings.py**, und ändern Sie die **DATABASES**-Definition folgendermaßen: Ersetzen Sie **NAME**, **USER**, **PASSWORD** und **HOST** durch die in der ClearDB-Systemsteuerung aufgeführten Werte:

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<database name>',
                'USER': '<user name>@<server name>',
                'PASSWORD': '<user password>',
                'HOST': '<server name>.database.windows.net',
                'PORT': '',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': True,
                },
            }
        }

    Sie müssen unbedingt einen auf dem Computer installierten Treiber verwenden. Rufen Sie **Verwaltung** und dann **ODBC-Datenquellen (32 Bit)** über das Startmenü auf. Die Treiber werden auf der Registerkarte **Treiber** aufgeführt.

    Bei Ausführung auf einer Azure-Website funktioniert sowohl **SQL Server Native Client 10.0** als auch **SQL Server Native Client 11.0**.

    Bei Ausführung in einem Azure-Cloud-Dienst funktioniert nur **SQL Server Native Client 11.0**.

9.  Synchronisieren Sie die Datenbank, und erstellen Sie Admin-Anmeldeinformationen wie bei der lokalen sqlite3-Datenbank.

### MySQL-Datenbank

In diesem Abschnitt erstellen wir eine MySQL-Datenbank in Azure, fügen die erforderlichen Pakete zu unserer virtuellen Umgebung hinzu und ändern die Einstellungen, um die neue Datenbank zu verwenden.

Im Azure Store können Sie Ihrem Konto verschiedene Dienste hinzufügen, u.a. auch eine MySQL-Datenbank. Wir können ein kostenloses Testkonto erstellen, und kleine Datenbanken sind bis zu einer bestimmten Größe ebenfalls kostenlos.

1.  Wählen Sie im Azure-Verwaltungsportal **NEU**-\>**STORE**-\>**APP-DIENSTE**-\>**ClearDB MySQL-Datenbank**. Erstellen Sie eine Datenbank mit dem Plan 'Free'.

2.  Anschließend installieren wir das Paket, das für den Zugriff auf MySQL-Datenbanken über Django benötigt wird, in unserer virtuellen Umgebung. Klicken Sie mit der rechten Maustaste auf **env** und auf **Install Python Package**: **mysql-python** mithilfe von **easy\_install**.

3.  Bearbeiten Sie die Datei **tutorial/settings.py**, und ändern Sie die **DATABASES**-Definition folgendermaßen: Ersetzen Sie **NAME**, **USER**, **PASSWORD** und **HOST** durch die in der ClearDB-Systemsteuerung aufgeführten Werte:

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<database name>',
                'USER': '<user name>',
                'PASSWORD': '<user password>',
                'HOST': '<host url>',
                'PORT': '',
            }
        }

4.  Synchronisieren Sie die Datenbank, und erstellen Sie Admin-Anmeldeinformationen wie bei der lokalen sqlite3-Datenbank.

## Verwenden der Django-Shell

1.  Klicken Sie mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Django** -\> **Django-Shell öffnen**.

2.  In diesem interaktiven Fenster können wir mithilfe unserer Modelle auf unsere Datenbank zugreifen. Geben Sie den folgenden Code ein, um der Datenbank eine Umfrage hinzuzufügen:

        from polls.models import Poll, Choice
        from django.utils import timezone

        p = Poll(question="Favorite way to host Django on Azure?", pub_date=timezone.now())
        p.save()

        p.choice_set.create(choice_text='Web Site', votes=0)
        p.choice_set.create(choice_text='Cloud Service', votes=0)
        p.choice_set.create(choice_text='Virtual Machine', votes=0)

    ![Django-Shell, Umfrage hinzufügen][Django-Shell, Umfrage hinzufügen]

3.  Die statische Analyse der Modelle bietet eine eingeschränkte Ansicht der vollständigen API. Im interaktiven Fenster steht IntelliSense für die Live-Objekte zur Verfügung; daher ist dies eine hervorragende Methode zum Erkunden der API. Im interaktiven Fenster können Sie verschiedene Dinge versuchen:

        # all poll objects
        Poll.objects.all()

        # primary key for poll
        p.id

        # all choice objects for the poll
        p.choice_set.all()

        # get object by primary key
        Poll.objects.get(pk=1)

    ![Django-Shell, Umfrage abfragen][Django-Shell, Umfrage abfragen]

4.  Starten Sie die Website. Sie sehen die Umfrage, die mit der Django-Shell hinzugefügt wurde.

## Veröffentlichen in Azure

Da sich die Datenbank jetzt in Azure befindet, besteht der nächste Schritt darin, die Website selbst in Azure zu hosten.

Azure verfügt über einige Optionen zum Hosten von Django-Anwendungen:

-   [Website][Website]
-   [Cloud-Dienste][Cloud-Dienste]
-   [Virtuelle Computer (Virtual Machines, VMs)][Virtuelle Computer (Virtual Machines, VMs)]

Python-Tools für Visual Studio verfügt über Funktionen zum Veröffentlichen auf Azure-Websites und in Azure-Cloud-Diensten. Diese Funktionen werden in den beiden nächsten Abschnitten erläutert, und Sie können sich mit einer oder mit beiden befassen.

In beiden Fällen übernimmt PTVS die IIS-Konfiguration und erstellt eine web.config-Datei, falls im Projekt noch keine solche Datei enthalten ist. Statische Dateien werden automatisch erfasst (manage.py collectstatic), solange STATIC\_ROOT in der Datei settings.py festgelegt ist.

Das Hosten von Django auf einem virtuellen Computer überschreitet den Rahmen dieses Lernprogramms. Dieser Vorgang erfordert das Erstellen eines virtuellen Computers mit dem gewünschten Betriebssystem (Windows oder Linux), die Installation von Python und die manuelle Bereitstellung der Django-Anwendung.

### Azure-Website

1.  Zunächst muss eine Website erstellt werden. Klicken Sie im Azure-Verwaltungsportal auf **NEU**-\>**SERVER**-\>**WEBSITE**-\>**SCHNELLERFASSUNG**. Wählen Sie einen beliebigen verfügbaren Namen.

2.  Laden Sie nach der Erstellung der Website das Veröffentlichungsprofil für die Website herunter.

    ![Website: Profil herunterladen][Website: Profil herunterladen]

3.  Klicken Sie in Visual Studio mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Veröffentlichen**.

    ![Website veröffentlichen][Website veröffentlichen]

4.  Importieren Sie die zuvor heruntergeladene Datei mit dem Website-Veröffentlichungsprofil.

5.  Akzeptieren Sie die Standardwerte, und klicken Sie auf **Veröffentlichen**, um die Veröffentlichung zu starten.

6.  Wenn die Veröffentlichung abgeschlossen ist, wird die veröffentlichte Website in einem Webbrowser geöffnet.

    ![Website-Browser][Website-Browser]

### Azure-Cloud-Dienst

1.  Klicken Sie mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Add Azure Cloud Service Project** oder **Konvertieren -\> Convert to Azure Cloud Service Project** (je nach der Version von Visual Studio wird die eine oder die andere Option angezeigt). Damit wird der Projektmappe ein neues Projekt mit dem Suffix .Azure hinzugefügt. Das neue Projekt ist in der Projektmappe als Startprojekt markiert.

    **Hinweis:** Wenn im Kontextmenü des Projekts der Befehl zum Erstellen des Azure-Cloud-Dienst-Projekts nicht angezeigt wird, müssen Sie die Azure-Tools für Visual Studio installieren. Diese Tools werden im Rahmen des Azure-SDK for .NET installiert. Informationen hierzu finden Sie im Abschnitt 'Anforderungen' am Anfang dieses Lernprogramms.

    ![Projektmappen-Explorer][4]

#### Ausführen in Azure-Emulator

1.  Sie müssen **Visual Studio als Administrator neu starten**, um die App im Serveremulator ausführen zu können.

2.  Wenn Sie das Debuggen mit **F5** starten, wird die App ausgeführt und im Serveremulator bereitgestellt. Überprüfen Sie, ob die Administratoroberfläche funktioniert und ob Sie in Umfragen abstimmen können.

    ![Serveremulator][Serveremulator]

3.  Sie können Visual Studio jetzt neu starten, wenn Sie es nicht mehr als Administrator ausführen möchten.

#### Veröffentlichen als Azure-Cloud-Dienst

1.  Anschließend veröffentlichen Sie den Cloud-Dienst in Azure. Klicken Sie mit der rechten Maustaste auf das Cloud-Dienst-Projekt **tutorial.Azure** und auf **Veröffentlichen**.

    **Hinweis:** Wählen Sie **Veröffentlichen** im Cloud-Dienst-Projekt aus. Damit wird das Dialogfeld **Azure-Anwendung veröffentlichen** aufgerufen, um eine Anwendung in einem Azure-Cloud-Dienst zu veröffentlichen. Wenn Sie **Veröffentlichen** im Django-Projekt auswählen, wird das Dialogfeld **Web veröffentlichen** aufgerufen, um eine Anwendung auf einer Azure-Website zu veröffentlichen.

    ![Cloud-Dienst veröffentlichen][Cloud-Dienst veröffentlichen]

2.  Sie müssen Ihre Azure-Abonnementdatei importieren. Klicken Sie auf den Link [Anmelden, um Anmeldeinformationen herunterzuladen][Anmelden, um Anmeldeinformationen herunterzuladen], um die Datei aus dem Azure-Portal herunterzuladen.

3.  Wählen Sie auf der Seite 'Einstellungen' im Kombinationsfeld 'Cloud-Dienst' den Eintrag **Neu erstellen**, um einen neuen Cloud-Dienst zu erstellen. Sie können einen beliebigen verfügbaren Namen verwenden.

    ![Cloud-Dienst-Einstellungen][Cloud-Dienst-Einstellungen]

4.  Akzeptieren Sie die Standardwerte, und klicken Sie auf **Veröffentlichen**. Dies dauert länger als die Veröffentlichung auf einer Website, da ein virtueller Computer für den Cloud-Dienst bereitgestellt werden muss. Sie können den Fortschritt im Fenster 'Azure-Aktivitätsprotokoll' sehen.

    ![Cloud-Dienst veröffentlichen][5]

5.  Klicken Sie nach Abschluss des Vorgangs auf den Website-URL im Fenster 'Azure-Aktivitätsprotokoll', um einen Webbrowser zu öffnen.

    ![Cloud-Dienst, Browser][Cloud-Dienst, Browser]

## Zusammenfassung

In diesem Lernprogramm haben wir eine Django-Anwendung mithilfe von [Python-Tools für Visual Studio][Python Tools 2.0 für Visual Studio] entwickelt. Wir haben drei verschiedene Datenbanken verwendet: sqlite3-, SQL Server- und MySQL-Datenbank. Zum Schluss haben wir die Anwendung auf Azure-Websites und in Azure-Cloud-Diensten veröffentlicht.

  [Lernprogramm ansehen]: http://www.youtube.com/watch?v=wkqjafvvU5w
  []: https://www.djangoproject.com/
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Python Tools 2.0 für Visual Studio]: http://pytools.codeplex.com
  [Python 2.7 (32 Bit)]: http://www.python.org/download/
  [den Quellcode für dieses Projekt herunterladen]: http://download-codeplex.sec.s-msft.com/Download?ProjectName=pytools&DownloadId=783376
  [Erstellen einer virtuellen Umgebung]: #creating-a-virtual-environment
  [Debuggen]: #debugging
  [Neues Projekt]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-001-new-project.png
  [Add Virtual Environment]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-002-add-virtual-env.png
  [Django installieren]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-003-install-django.png
  [Ausgabe bei der Installation von Django]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-004-install-django-output.png
  [Django-Webbrowser]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-004b-itworked.png
  [Django-App hinzufügen]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-005-add-django-app.png
  [Projektmappen-Explorer]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-006-solution-explorer.png
  [Django Sync DB]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-007-sqlite3.png
  [Webbrowser]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-008-dev-server.png
  [Umfrage hinzufügen]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-login.png
  [Umfrageindex]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-add-poll.png
  []: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-010-index.png
  [Umfragedetail]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-011-detail.png
  [Umfrageergebnisse]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-012-results.png
  [1]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-013-solution-explorer.png
  [2]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-014-detail-styled.png
  [3]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-015-debugging.png
  [hier]: http://www.windowsazure.com/de-de/manage/linux/common-tasks/mysql-on-a-linux-vm/
  [Django-Shell, Umfrage hinzufügen]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-018-shell-add-poll.png
  [Django-Shell, Umfrage abfragen]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-019-shell-query.png
  [Website]: http://www.windowsazure.com/de-de/services/web-sites/
  [Cloud-Dienste]: http://www.windowsazure.com/de-de/services/cloud-services/
  [Virtuelle Computer (Virtual Machines, VMs)]: http://www.windowsazure.com/de-de/services/virtual-machines/
  [Website: Profil herunterladen]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-download-profile.png
  [Website veröffentlichen]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-publish.png
  [Website-Browser]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website.png
  [4]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-solution-explorer.png
  [Serveremulator]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-emulator.png
  [Cloud-Dienst veröffentlichen]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish.png
  [Anmelden, um Anmeldeinformationen herunterzuladen]: https://manage.windowsazure.com/publishsettings/index?client=vs&schemaversion=2.0
  [Cloud-Dienst-Einstellungen]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-settings.png
  [5]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish-progress.png
  [Cloud-Dienst, Browser]: ./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice.png
