<properties 
	pageTitle="Konfigurieren von Python mit Azure Anwendungsdiensts Web-Apps" 
	description="In diesem Lernprogramm werden die Optionen zum Erstellen und Konfigurieren eines grundlegenden Web Servers Gateway Interface (WSGI)-kompatiblen Python-Anwendung auf Azure App Service Web-Apps beschrieben." 
	services="app-service\web" 
	documentationCenter="python" 
	tags="python"
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="huguesv"/>




# Konfigurieren von Python mit Azure Anwendungsdiensts Web-Apps

Dieses Lernprogramm beschreibt die Optionen zum Erstellen und Konfigurieren einer grundlegenden Web Server Gateway Interface (WSGI)-kompatiblen Python-Anwendung auf [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Außerdem beschrieben werden zusätzliche Funktionen der Git-Bereitstellung, z. B. virtuelle Umgebung und Paketinstallation mithile von "requirements.txt".


## Bottle, Django oder Flask?

Azure Marketplace enthält Vorlagen für die Flasche, Django und Kolben-Frameworks. Wenn Sie Ihre erste Webanwendung in Azure-App-Dienst entwickeln oder Sie nicht mit Git vertraut sind, empfehlen wir, dass Sie diese Lernprogramme, folgen die enthalten schrittweise Anleitungen zum Erstellen einer Anwendung aus dem Katalog mithilfe von Git-Bereitstellung von Windows- oder Mac:

- [Erstellen von Webanwendungen mit Flasche](web-sites-python-create-deploy-bottle-app.md)
- [Erstellen von Webanwendungen mit Django](web-sites-python-create-deploy-django-app.md)
- [Erstellen von Webanwendungen mit Kolben](web-sites-python-create-deploy-flask-app.md)


## Web-app-Erstellung auf Azure Preview-Portals

In diesem Lernprogramm wird davon ausgegangen, ein Azure-Abonnement und den Zugriff auf das Azure Preview Portal.

Wenn Sie nicht über eine vorhandene Webanwendung verfügen, können Sie erstellen, aus der [Azure Preview Portal](https://portal.azure.com). Klicken Sie auf die Schaltfläche "Neu" in der unteren linken Ecke **Web + Mobile** > **Web app**.

## Git-Veröffentlichung

Konfigurieren von Git-Veröffentlichung für Ihre neu erstellte Webanwendung anhand der Instruktionen unter [fortlaufende Bereitstellung mit GIT in Azure-App-Dienst](web-sites-publish-source-control.md). In diesem Lernprogramm wird Git erstellen, verwalten und Veröffentlichen von unserem Python-Webanwendung in Azure-App-Dienst verwendet.

Sobald Git-Veröffentlichung eingerichtet ist, wird ein Git-Repository erstellt und Ihrer Webanwendung zugeordnet. Die URL des Repository wird angezeigt und kann ab sofort verwendet werden, um Daten aus der lokalen Entwicklungsumgebung mittels Push in die Cloud zu übertragen. Zum Veröffentlichen von Anwendungen mittels Git stellen Sie sicher, dass auch ein Git-Client installiert ist, und führen Sie die Anweisungen auf den Inhalt der Webseite app Azure-App-Dienst übertragen.


## Anwendungsübersicht

In den nächsten Abschnitten werden die folgenden Dateien erstellt. Sie müssen im Stammverzeichnis des Git-Repositorys gespeichert werden.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## WSGI-Handler

WSGI ist ein Python-Standard, der in der Spezifikation [PEP 3333](http://www.python.org/dev/peps/pep-3333/) beschrieben wird und der eine Schnittstelle zwischen dem Webserver und Python definiert. Es bietet eine standardisierte Schnittstelle zum Schreiben verschiedener Webanwendungen und Frameworks mit Python. Beliebte Python-Webframeworks nutzen heute WSGI. Azure Webanwendungen für App-Dienst bietet, die Ihnen eine solche Frameworks support; Darüber hinaus können fortgeschrittene Benutzer auch ihre eigenen erstellen, solange der benutzerdefinierte Handler die Richtlinien der WSGI-Spezifikation entspricht.

Hier ist ein Beispiel für eine `app.py` einen benutzerdefinierten Handler definiert:

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

Sie können diese Anwendung lokal ausführen `python app.py`, navigieren Sie zu `http://localhost:5555` in Ihrem Webbrowser.


## Virtuelle Umgebung

Obwohl die obige Beispiel-App keine externen Pakete erfordert, ist es wahrscheinlich, dass Ihre Anwendung einige benötigt.

Zur besseren Verwaltung externer Paketabhängigkeiten unterstützt die Azure Git-Bereitstellung die Erstellung virtueller Umgebungen.

Wenn Azure ein requirements.txt im Stammverzeichnis des Repositorys erkennt, erstellt er automatisch eine virtuelle Umgebung mit dem Namen `env`. Dies erfolgt nur bei der ersten Bereitstellung oder bei jeder Bereitstellung, nachdem die ausgewählte Python-Laufzeit geändert wurde.

Sie werden wahrscheinlich eine virtuelle Umgebung für die lokale Entwicklung erstellen. Fügen Sie diese jedoch nicht Ihrem Git-Repository hinzu.


## Verwalten von Paketen

In "requirements.txt" aufgeführe Paket werden mit Pip in der virtuellen Umgebung automatisch installiert. Dies erfolgt bei jeder Bereitstellung, doch Pip überspringt die Installation, wenn ein Paket bereits installiert ist.

Beispiel für `requirements.txt`:

    azure==0.8.4


## Python-Version

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Beispiel für `runtime.txt`:

    python-2.7


## Web.config

Sie müssen eine "web.config"-Datei erstellen, um anzugeben, wie der Server Anforderungen verarbeiten soll.

Beachten Sie, dass wenn Sie eine "web.x.y.config"-Datei im Repository vorhanden ist, wobei "x.y" der ausgewählten Python-Laufzeit entspricht, dann kopiert Azure automatisch die entsprechende Datei als "web.config".

Die folgenden "web.config"-Beispiele basieren auf einem Proxyskript für eine virtuelle Umgebung, das im nächsten Abschnitt beschrieben wird. Sie arbeiten mit dem WSGI-Handler, der im Beispiel verwendeten `app.py` oben.

Beispiel `web.config` für Python 2.7:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python273_via_FastCGI" />
          <remove name="Python340_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Beispiel `web.config` für Python 3.4:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python273_via_FastCGI" />
          <remove name="Python340_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Statische Dateien werden für eine bessere Leistung direkt vom Webserver ohne Umweg über Python-Code verarbeitet.

In den obigen Beispielen muss der Speicherort der statischen Dateien auf dem Datenträger mit dem Speicherort in der URL übereinstimmen. Dies bedeutet, dass eine Anforderung für `http://pythonapp.azurewebsites.net/static/site.css` dient die Datei auf dem Datenträger am `\static\site.css`.

Es ist möglich, konfigurieren die Regel `Static Files` mit Dateien von einem Speicherort auf dem Datenträger, der die Position in der URL unterscheidet. In der folgenden Regeldefinition eine Anforderung für `http://pythonapp.azurewebsites.net/static/site.css` dient die Datei auf dem Datenträger am `\FlaskWebProject\static\site.css`, anstelle von `\static\site.css`.

    <rule name="Static Files" stopProcessing="true">
      <match url="^/static/.*" ignoreCase="true" />
      <action type="Rewrite" url="^/FlaskWebProject/static/.*" appendQueryString="true" />
    </rule>

`WSGI_ALT_VIRTUALENV_HANDLER` ist, geben Sie den WSGI-Handler an. In den Beispielen oben hat `app.wsgi_app` da der Handler eine Funktion namens ist `wsgi_app` in `app.py` im Stammordner.

`PYTHONPATH` kann angepasst werden, wenn Sie alle Ihre Abhängigkeiten in der virtuellen Umgebung installieren, durch deren Angabe in requirements.txt, sollte aber nicht erforderlich, ihn zu ändern.


## Proxy für die virtuelle Umgebung

Das folgende Skript dient zum Abrufen des WSGI-Handlers, Aktivieren der virtuellen Umgebung und Protokollieren von Fehlern. Es ist generisch ausgelegt und soll ohne Änderungen verwendet werden.

Inhalt des `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n');

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_HANDLER env var must be set')
        
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)

        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None

        if handler is None:
            raise ValueError('"%s" could not be imported' % handler_name)

        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []
        
        site.main()
        
        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## Anpassen der Git-Bereitstellung

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]


## Problembehandlung - Paketinstallation

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## Problembehandlung – virtuelle Umgebung

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)





 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->