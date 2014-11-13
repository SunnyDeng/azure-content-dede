<properties urlDisplayName="Django with Visual Studio" pageTitle="Django mit Visual Studio (Python) - Azure-Lernprogramm" metaKeywords="Azure Django web app, Azure Django virtual machine" description="In diesem Lernprogramm erfahren Sie, wie Sie eine Django-Webanwendung erstellen, die auf einem virtuellen Azure-Computer gehostet ist." metaCanonical="" services="cloud-services" documentationCenter="Python" title="Erstellen von Django-Anwendungen mit Python Tools f&uuml;r Visual Studio 1.5" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Erstellen von Django-Anwendungen mit Python Tools für Visual Studio 1.5

**Hinweis:** Dieses Lernprogramm ist auch als [Youtube-Video][Youtube-Video] erhältlich.

**Hinweis:** Ein [neues und umfassenderes Lernprogramm][neues und umfassenderes Lernprogramm] für PTVS 2.0 Beta ist nun erhältlich.

Entwickeln für Azure ist einfach, wenn Sie dafür die verfügbaren Tools verwenden.
Bei diesem Lernprogramm wird davon ausgegangen, dass Sie keine Erfahrung mit der Verwendung von Azure haben.
Nach Abschluss des Lernprogramms verfügen Sie über eine Django-Anwendung, die in der Cloud ausgeführt wird.

Sie erhalten Informationen zu folgenden Themen:

-   Erstellen einer Django-Basisanwendung
-   Lokales Ausführen und Debuggen Ihrer Django-Anwendung mithilfe des Django-Testservers
-   Lokales Ausführen Ihrer Django-Anwendung auf dem Serveremulator
-   Veröffentlichen und erneutes Veröffentlichen Ihrer Anwendung in Azure.

Im Rahmen dieses Lernprogramms erstellen Sie eine einfache
Webanwendung "Hello World". Die Anwendung wird in einer Instanz einer Webrolle gehostet,
die ihrerseits in einem dedizierten virtuellen Computer
gehostet wird, wenn sie in Azure läuft.

Unten finden Sie einen Screenshot der vollständigen Anwendung:

![][0]

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <span id="setup"></span> </a>Einrichten der Entwicklungsumgebung

Bevor Sie mit der Entwicklung der Azure-Anwendung beginnen können, müssen Sie über die entsprechenden Tools verfügen und die Entwicklungsumgebung einrichten. Informationen zum Abrufen und Installieren des Azure-SDK für Python finden Sie unter [Installieren von Python][Installieren von Python].

**Hinweis:** In diesem Lernprogramm werden Python 2.7 und Django 1.4 benötigt. Diese Version en sind im aktuellen Azure-SDK für Python enthalten.

**Hinweis:** Für die Bereitstellung auf dem Serveremulator und/oder in Azure ist die Vollversion von Visual Studio erforderlich (die im SDK enthaltene Shell wird nicht unterstützt).

## Erstellen einer neuen Django-Anwendung

Starten Sie zum Erstellen einer neuen Django-Anwendung zuerst Visual Studio, und erstellen Sie ein neues Projekt, indem Sie auf **Datei-\>Neues Projekt** klicken. Suchen Sie nach der Registerkarte "Python" (diese finden Sie entweder auf der obersten Ebene oder im Bereich "'Andere Sprachen"), und wählen Sie die Vorlage für die Django-Anwendung:

![Neue Python-Projektvorlagen][Neue Python-Projektvorlagen]

Klicken Sie auf **OK**, und Sie haben Ihre erste Django-App erstellt.

![Visual Studio im ersten Django-Projekt geöffnet][Visual Studio im ersten Django-Projekt geöffnet]

Als Nächstes können Sie damit beginnen, diese erste Django-App zu entwickeln. Sie können mit der rechten Maustaste auf den Modulknoten klicken und **Neue Django-App hinzufügen** wählen, um eine neue App in Ihrem Projekt einzurichten:

![Neuen App-Menüpunkt hinzufügen][Neuen App-Menüpunkt hinzufügen]

Nun können Sie den neuen Namen für Ihre Anwendung eingeben:

![Aufforderung zur Eingabe eines neuen App-Namens][Aufforderung zur Eingabe eines neuen App-Namens]

Wenn Sie einen Namen für die Anwendung eingegeben und auf **OK** geklickt haben, wird eine neue App zu Ihrem Projekt hinzugefügt:

![Projektmappen-Explorer mit neuer App hinzugefügt][Projektmappen-Explorer mit neuer App hinzugefügt]

Aktualisieren Sie nun **settings.py**, um Ihre Anwendung zu registrieren. Dadurch erkennt Django automatisch Vorlagendateien, die zum Vorlagenverzeichnis Ihrer App hinzugefügt werden. Fügen Sie den Anwendungsnamen in den Abschnitt INSTALLED\_APPS ein:

    'DjangoApplication.MyFirstApp',

![App in settings.py in INSTALLED\_APPS einfügen][App in settings.py in INSTALLED\_APPS einfügen]

Fügen wir nun einen Code in die Datei **views.py** ein, sodass eine einfache Vorlagendatei zurückgegeben wird:

    from django.http import HttpResponse
    from django.template.loader import render_to_string
    def home(request):
        return HttpResponse(render_to_string(
                                            'index.html',
                                            {'content': 'Hello World'}
                                            ))

![App in settings.py in INSTALLED\_APPS einfügen][1]

Lassen Sie uns nun eine einfache Vorlagendatei hinzufügen, die gerendert wird, wenn Sie diese Ansicht öffnen. Klicken Sie dafür mit der rechen Maustaste auf den Ordner "Vorlagen", und wählen Sie **"Neues Element hinzufügen"**:

![Neues Element zum Ordner "Vorlagen" hinzufügen][Neues Element zum Ordner "Vorlagen" hinzufügen]

Nun können sie in der Vorlagenliste "Django-HTML-Vorlage" wählen und als Dateinamen **index.html** eingeben:

![Neues Element zum Ordner "Vorlagen" hinzufügen][2]

Danach wird die Vorlage zum Projekt hinzugefügt und geöffnet. Hier können Sie den Beginn der entsprechenden Syntax sehen. Die Vorlagen-Tags sind hervorgehoben.

![Zum Projektmappen-Explorer hinzugefügte Vorlage][Zum Projektmappen-Explorer hinzugefügte Vorlage]

An diesem Punkt können Sie mit der Aktualisierung der Vorlage beginnen, um die gerenderte HTML zu ändern, und können dabei mit Intellisense arbeiten:

![Vorlagen-Intellisense für Django-Filter][Vorlagen-Intellisense für Django-Filter]

Die Groß- und Kleinschreibung ist in diesem Falle nicht wichtig, da sie sich nicht auf die Ergebnisse auswirkt. Am Ende müssen Sie nur noch Ihre Ansicht mit den URL-Mustern in der Datei **urls.py** registrieren. Fügen Sie dies in **urlpatterns** ein:

    url(r'^$', 'DjangoApplication.MyFirstApp.views.home', name='home'),

![URL registrieren][URL registrieren]

## Lokales Ausführen Ihrer Anwendung im Testserver

An diesem Punkt haben Sie Ihre allererste Django-Anwendung erstellt. Nun können Sie sie lokal ausführen, indem Sie einfach **F5 drücken**.

![Django Hello World in einem Browser und auf dem Testserver][Django Hello World in einem Browser und auf dem Testserver]

Dadurch wird Ihr Python-Übersetzer gestartet, der die **manage.py** in Django ausführt, um den Testserver zu starten. Nach dem erfolgreichen Start des Testservers wird auch ein Webbrowser zum Anzeigen der Website gestartet. Da sie den Start durch Drücken von F5 ausgeführt haben, erfolgt er im Debugger, sodass wir auch Haltepunkte in jedem Python-Code sowie im Anzeigecode in den Vorlagendateien selbst festlegen können:

![An einem Vorlagen-Haltepunkt gestoppter Debugger][An einem Vorlagen-Haltepunkt gestoppter Debugger]

Nun können Sie **auf die Stopp-Schaltfläche klicken** und zur Ausführung im Azure-Serveremulator übergehen.

## Lokales Ausführen der Anwendung im Emulator

Zur Ausführung auf dem Serveremulator müssen Sie nur ein Azure-Bereitstellungsprojekt zu Ihrer Projektmappe für das Django-Projekt hinzufügen.

**Hinweis:** Für die Bereitstellung auf dem Serveremulator und/oder in Azure ist die Vollversion von Visual Studio erforderlich (die im SDK enthaltene Shell wird nicht unterstützt).

Klicken Sie dafür mit der rechten Maustaste auf den Django-Projektknoten im Projektmappen-Explorer, und wählen Sie **Add Azure Cloud Service Project** aus:

![Bereitstellungsprojekt hinzufügen][Bereitstellungsprojekt hinzufügen]

Nach der Ausführung dieses Befehls wird im Projektmappen-Explorer ein neu hinzugefügtes Projekt hinzugefügt:

![Nach dem Hinzufügen des Bereitstellungsprojekts][Nach dem Hinzufügen des Bereitstellungsprojekts]

Das neue Projekt ist nun auch in der Projektmappe als Startprojekt markiert. An diesem Punkt müssen Sie **Visual Studio als Administrator neu starten**, um es im Serveremulator ausführen zu können, aber danach können Sie einfach **F5 drücken**, damit die App im Serveremulator ausgeführt und bereitgestellt wird:

![Nach dem Hinzufügen des Bereitstellungsprojekts][3]

Nun schauen wir auf dieselbe Webseite, allerdings mit einer leicht unterschiedlichen URL. Sie können auch sehen, dass es keine python.exe gibt, die den Django-Testserver ausführt. Stattdessen wird Django über IIS mit einem FastCGI-Gateway ausgeführt, das automatisch eingefügt und eingerichtet wird, wenn Sie in Visual Studio arbeiten.

Bei der Ausführung im Serveremulator können Sie Ihre Anwendung schnell durchlaufen: Gehen Sie einfach zurück zu Visual Studio und aktualisieren Sie Ihre Dateien sowie den Webbrowser. Die Ergebnisse sind sofort sichtbar!

## Bereitstellung der Anwendung zu Azure

Nun sind Sie bereit, das Projekt in Azure bereitzustellen. Dafür müssen Sie nur mit der rechten Maustaste auf das Azure-Bereitstellungsprojekt im Projektmappen-Explorer klicken und **Veröffentlichen** auswählen:

![Paket-App-Menü][Paket-App-Menü]

Nach der Auswahl von "Veröffentlichen" werden Sie aufgefordert, sich in Azure anzumelden. Sie können hier entweder Ihre vorhandenen Anmeldeinformationen importieren oder neue Anmeldeinformationen einrichten:

![Paketabonnement][Paketabonnement]

Nach der Auswahl der Anmeldeinformationen sehen Sie den Bildschirm mit den Azure-Veröffentlichungseinstellungen. Sie können nun zwischen verschiedenen Optionen zur Durchführung Ihrer Bereitstellung wählen oder einfach auf **Veröffentlichen** klicken:

![Paketeinstellungen][Paketeinstellungen]

Nun müssen Sie darauf warten, dass die Anwendung eingerichtet und bereitgestellt wird.

![Paketbereitstellung][Paketbereitstellung]

Wenn alles eingerichtet ist, können Sie auf den Link unter "DNS-Name" klicken, um Ihre Website in der Cloud zu sehen:

![Ihre Django-App in der Cloud][Ihre Django-App in der Cloud]

  [Youtube-Video]: http://www.youtube.com/watch?v=UsLti4KlgAY
  [neues und umfassenderes Lernprogramm]: ../web-sites-python-create-deploy-django-app/
  [0]: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstAppInCloud.png
  [Installieren von Python]: ../python-how-to-install/
  [Neue Python-Projektvorlagen]: ./media/python-django-app-tools-visual-studio/ptvs-dj-NewProject.png
  [Visual Studio im ersten Django-Projekt geöffnet]: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstProject.png
  [Neuen App-Menüpunkt hinzufügen]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddNewApp.png
  [Aufforderung zur Eingabe eines neuen App-Namens]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddNewAppPrompt.png
  [Projektmappen-Explorer mit neuer App hinzugefügt]: ./media/python-django-app-tools-visual-studio/ptvs-dj-MyFirstApp.png
  [App in settings.py in INSTALLED\_APPS einfügen]: ./media/python-django-app-tools-visual-studio/ptvs-dj-InstallApp.png
  [1]: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstView.png
  [Neues Element zum Ordner "Vorlagen" hinzufügen]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddFirstTemplate.png
  [2]: ./media/python-django-app-tools-visual-studio/ptvs-dj-NewDjangoTemplate.png
  [Zum Projektmappen-Explorer hinzugefügte Vorlage]: ./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateAdded.png
  [Vorlagen-Intellisense für Django-Filter]: ./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateIntellisense.png
  [URL registrieren]: ./media/python-django-app-tools-visual-studio/ptvs-dj-RegisterUrl.png
  [Django Hello World in einem Browser und auf dem Testserver]: ./media/python-django-app-tools-visual-studio/ptvs-dj-DjangoHelloWorldTestServer.png
  [An einem Vorlagen-Haltepunkt gestoppter Debugger]: ./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateBreakpoint.png
  [Bereitstellungsprojekt hinzufügen]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddDeploymentProject.png
  [Nach dem Hinzufügen des Bereitstellungsprojekts]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AfterDeployProjAdded.png
  [3]: ./media/python-django-app-tools-visual-studio/ptvs-dj-ComputeEmulator.png
  [Paket-App-Menü]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish1.png
  [Paketabonnement]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish2.png
  [Paketeinstellungen]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish3.png
  [Paketbereitstellung]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish4.png
