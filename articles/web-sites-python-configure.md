<properties 
	pageTitle="Konfigurieren von Python mit Azure-Websites" 
	description="Dieses Lernprogramm beschreibt die Optionen für das Erstellen und Konfigurieren einer einfachen WSGI-kompatiblen (Web Server Gateway Interface) Python-Anwendung auf Azure-Websites." 
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
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Konfigurieren von Python mit Azure-Websites

Dieses Lernprogramm beschreibt die Optionen für das Erstellen und Konfigurieren einer grundlegenden Web Server Gateway Interface (WSGI)-kompatiblen Python-Anwendung auf Azure-Websites.

Außerdem beschrieben werden zusätzliche Funktionen der Git-Bereitstellung, z. B. virtuelle Umgebung und Paketinstallation mithile von "requirements.txt".


## Bottle, Django oder Flask?

Der Azure-Katalog enthält Vorlagen für die Frameworks Bottle, Django und Flask.  Wenn Sie Ihre erste Azure-Website entwickeln oder nicht mit Git vertraut sind, empfehlen wir, dass Sie diese Lernprogramme befolgen, die schrittweise Anleitungen zum Entwickeln einer funktionierenden Anwendung aus dem Katalog mithilfe der Git-Bereitstellung unter Windows oder Mac enthalten:

- [Erstellen von Websites mit Bottle][]
- [Erstellen von Websites mit Django][]
- [Erstellen von Websites mit Flask][]


## Erstellen von Websites im Portal

Für dieses Lernprogramm wird davon ausgegangen, dass Sie über ein Azure-Abonnement verfügen und Zugang zum Azure-Verwaltungsportal haben.

Wenn Sie noch keine Website haben, können Sie eine über das Azure-Verwaltungsportal erstellen.  Klicken Sie auf die Schaltfläche "NEU" in der unteren linken Ecke. Daraufhin wird ein Fenster angezeigt. Klicken Sie auf SERVER, WEBSITE und dann auf SCHNELLERFASSUNG.

![](./media/web-sites-python-configure/configure-python-create-website.png)


## Git-Veröffentlichung

Verwenden Sie die Registerkarte SCHNELLSTART oder DASHBOARD für die neu erstellte Website, um die Git-Veröffentlichung zu konfigurieren.  In diesem Lernprogramm wird die Python-Website mit Git erstellt, verwaltet und auf Azure-Websites veröffentlicht.

![](./media/web-sites-python-configure/configure-python-git.png)

Sobald die Git-Veröffentlichung eingerichtet ist, wird ein Git-Repository erstellt und der Website zugewiesen.  Die URL des Repository wird angezeigt und kann ab sofort verwendet werden, um Daten aus der lokalen Entwicklungsumgebung mittels Push in die Cloud zu übertragen. Stellen Sie beim Veröffentlichen von Anwendungen mittels Git sicher, dass auch ein Git-Client installiert ist, und verwenden Sie die bereitgestellten Anweisungen, um den Websiteinhalt per Push auf Azure-Websites zu übertragen.


## Anwendungsübersicht

In den nächsten Abschnitten werden die folgenden Dateien erstellt.  Sie müssen im Stammverzeichnis des Git-Repositorys gespeichert werden.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## WSGI-Handler

WSGI ist ein Python-Standard, der in der Spezifikation [PEP 3333](http://www.python.org/dev/peps/pep-3333/) beschrieben wird und der eine Schnittstelle zwischen dem Webserver und Python definiert. Es bietet eine standardisierte Schnittstelle zum Schreiben verschiedener Webanwendungen und Frameworks mit Python.  Beliebte Python-Webframeworks nutzen heute WSGI.  Azure-Websites bietet Ihnen die Unterstützung für solche Frameworks. Darüber hinaus können erfahrenere Benutzer auch ihre eigenen Frameworks erstellen, sofern der benutzerdefinierte Handler die Richtlinien der WSGI-Spezifikation befolgt.

Hier ist ein Beispiel für der Datei  `app.py`, die einen benutzerdefinierten Handler definiert:

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

Sie können diese Anwendung lokal mit `python app.py` ausführen und dann in Ihrem Webbrowser zu `http://localhost:5555` navigieren.


## Virtuelle Umgebung

Obwohl die obige Beispiel-App keine externen Pakete erfordert, ist es wahrscheinlich, dass Ihre Anwendung einige benötigt.

Zur besseren Verwaltung externer Paketabhängigkeiten unterstützt die Azure Git-Bereitstellung die Erstellung virtueller Umgebungen.

Wenn Azure eine Datei namens "requirements.txt" im Stammverzeichnis des Repositorys erkennt, wird automatisch eine virtuelle Umgebung mit dem Namen  `env` erstellt.  Dies erfolgt nur bei der ersten Bereitstellung oder bei jeder Bereitstellung, nachdem die ausgewählte Python-Laufzeit geändert wurde.

Sie werden wahrscheinlich eine virtuelle Umgebung für die lokale Entwicklung erstellen. Fügen Sie diese jedoch nicht Ihrem Git-Repository hinzu.


## Verwalten von Paketen

In "requirements.txt" aufgeführe Paket werden mit Pip in der virtuellen Umgebung automatisch installiert.  Dies erfolgt bei jeder Bereitstellung, doch Pip überspringt die Installation, wenn ein Paket bereits installiert ist.

Beispiel für `requirements.txt`:

    azure==0.8.4


## Python-Version

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

Beispiel für `runtime.txt`:

    python-2.7


## Web.config

Sie müssen eine "web.config"-Datei erstellen, um anzugeben, wie der Server Anforderungen verarbeiten soll.

Beachten Sie, dass wenn Sie eine "web.x.y.config"-Datei im Repository vorhanden ist, wobei "x.y" der ausgewählten Python-Laufzeit entspricht, dann kopiert Azure automatisch die entsprechende Datei als "web.config".

Die folgenden "web.config"-Beispiele basieren auf einem Proxyskript für eine virtuelle Umgebung, das im nächsten Abschnitt beschrieben wird.  Sie funktionieren mit dem im obigen Beispiel von  `app.py` verwendeten WSGI-Handler.

Beispiel von  `web.config` für Python 2.7:

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


Beispiel von  `web.config` für Python 3.4:

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

In den obigen Beispielen muss der Speicherort der statischen Dateien auf dem Datenträger mit dem Speicherort in der URL übereinstimmen.  Dies bedeutet, dass eine Anforderung für  `http://pythonapp.azurewebsites.net/static/site.css` der Datei auf dem Datenträger in  `\static\site.css` gilt.

Es ist möglich, die Regel  `Static Files` so zu konfigurieren, dass Dateien an einem Speicherort auf dem Datenträger bereitgestellt werden, der sich vom Speicherort in der URL unterscheidet.  In der folgenden Regeldefinition gilt eine Anforderung für  `http://pythonapp.azurewebsites.net/static/site.css` der Datei auf dem Datenträger  in  `\FlaskWebProject\static\site.css` anstelle von  `\static\site.css`.

    <rule name="Static Files" stopProcessing="true">
      <match url="^/static/.*" ignoreCase="true" />
      <action type="Rewrite" url="^/FlaskWebProject/static/.*" appendQueryString="true" />
    </rule>

`WSGI_ALT_VIRTUALENV_HANDLER` dient zum Angeben des WSGI-Handlers.  In den obigen Beispielen, handelt es sich um  `app.wsgi_app`, da der Handler eine Funktion namens  `wsgi_app` in  `app.py` im Stammordner ist.

`PYTHONPATH` kann angepasst werden, doch wenn Sie alle Ihre Abhängigkeiten in der virtuellen Umgebung installieren, indem Sie sie in "requirements.txt" angeben, sollten keine Änderungen erforderlich sein.


## Proxy für die virtuelle Umgebung

Das folgende Skript dient zum Abrufen des WSGI-Handlers, Aktivieren der virtuellen Umgebung und Protokollieren von Fehlern. Es ist generisch ausgelegt und soll ohne Änderungen verwendet werden.

Inhalt von  `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # Dieser Quellcode unterliegt den Bestimmungen und Bedingungen der Apache-Lizenz, Version 2.0. Ein 
     # Kopie der Lizenz finden Sie in der Datei "License.html" im Stammverzeichnis von diesem Verteilungspunkt. Wenn 
     # Sie keine Apache-Lizenz, Version 2.0, finden, senden Sie bitte eine e-Mail an 
     # vspython@microsoft.com. Durch die Verwendung dieses Quellcodes stimmen Sie den 
     # Bedingungen der Apache-Lizenz, Version 2.0 zu.
     #
     # Sie dürfen diesen oder einen anderen Hinweis nicht aus dieser Software entfernen.
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

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-deployment.md)]


## Problembehandlung - Bereitstellung

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


## Problembehandlung - Paketinstallation

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


## Problembehandlung - Virtuelle Umgebung

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]



[Erstellen von Websites mit Bottle]: web-sites-python-create-deploy-bottle-app.md
[Erstellen von Websites mit Django]: web-sites-python-create-deploy-django-app.md
[Erstellen von Websites mit Flask]: web-sites-python-create-deploy-flask-app.md

<!--HONumber=52-->