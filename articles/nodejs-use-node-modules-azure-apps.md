<properties linkid="develop-nodejs-common-tasks-working-with-node-modules" urlDisplayName="Working with Node.js Modules" pageTitle="Working with Node.js Modules" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="Node.js" title="Using Node.js Modules with Azure applications" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

Verwenden von Node.js-Modulen mit Azure-Anwendungen
===================================================

Dieses Dokument bietet Anweisungen zur Verwendung von Node.js-Modulen mit Anwendungen, die auf Azure gehostet werden. Sie erfahren, wie Sie sicherstellen können, dass eine Anwendung eine bestimmte Version eines Moduls sowie systemeigene Module von Azure verwendet.

Wenn Sie sich bereits mit Node.js-Modulen, **package.json**- und **npm-shrinkwrap.json**-Dateien auskennen, ist folgender Artikel eine kurze Zusammenfassung der in diesem Artikel diskutierten Inhalte:

-   Azure-Websites verstehen **package.json**- und **npm-shrinkwrap.json**-Dateien, und können Module basierend auf den Einträgen in diesen Dateien installieren.
-   Azure Cloud Services erfordern, dass alle Module auf der Entwicklungsumgebung installiert sind und das Verzeichnis **node\_modules** als Teil des Bereitstellungspakets enthalten ist.

**Hinweis**

Azure Virtual Machines werden in diesem Artikel nicht behandelt, da die Bereitstellung auf einem virtuellen Computer vom Betriebssystem abhängig ist, das von dem virtuellen Computer gehostet wird.

**Hinweis**

Sie können die Unterstützung für die Installation von Modulen mithilfe der Dateien **package.json** oder **npm-shrinkwrap.json** auf Azure aktivieren, dies erfordert jedoch die Anpassung der Standardskripts, die von Clouddienstprojekten verwendet werden. Ein Beispiel dafür finden Sie unter [Azure Startup task to run npm install to avoid deploying node modules](http://nodeblog.azurewebsites.net/startup-task-to-run-npm-in-azure) (in englischer Sprache)

Node.js-Module
--------------

Module sind ladbare JavaScript-Pakete, die bestimmte Funktionen für die Anwendung bieten. Ein Modul wird normalerweise über das Befehlszeilentool **npm** installiert. Einige Module (wie das http-Modul) werden jedoch als Teil des Node.js-Pakets bereitgestellt.

Wenn Module installiert werden, werden diese im Verzeichnis **node\_modules** im Stamm der Anwendungsverzeichnisstruktur gespeichert. Jedes Modul im Verzeichnis **node\_modules** behält sein eigenes **node\_modules**-Verzeichnis, das alle Module enthält, von denen es abhängt. Dies wiederholt sich für jedes Modul auf der Abhängigkeitskette. Dadurch kann jedes installierte Modul seine eigenen Versionsanforderungen für die Module haben, von denen es abhängt. Dies kann jedoch zu einer sehr großen Verzeichnisstruktur führen.

Wenn das Verzeichnis **node\_modules** als Teil der Anwendung bereitgestellt wird, vergrößert dies die Bereitstellung im Vergleich zur Verwendung einer Datei **package.json** oder **npm-shrinkwrap.json**. Dies garantiert jedoch, dass die Version der in der Produktion verwendeten Module identisch mit derjenigen in der Entwicklung ist.

### Systemeigene Module

Auch wenn die meisten Module einfache Nur-Text-JavaScript-Dateien sind, gibt es auch Module, die plattformspezifische Binärimages sind. Diese Module werden bei der Installation gebildet, was normalerweise mit Python und node-gyp erfolgt. Eine besondere Einschränkung von Azure-Websites ist, dass es zwar versteht, wie in einer **package.json**- oder **npm-shrinkwrap.json**-Datei angegebene Module installiert werden, aber weder Python noch node-gyp bietet und keine systemeigenen Module erstellen kann.

Da Azure Cloud Services auf den Ordner **node\_modules** angewiesen sind, der als Teil der Anwendung bereitgestellt wird, sollte ein systemeigenes Modul, das als Teil der installierten Module enthalten ist, in einem Clouddienst funktionieren, solange dieser auf einem Windows-Entwicklungssystem erstellt wurde.

Azure-Websites unterstützt keine systemeigenen Module. Einige Module wie JSDOM und MongoDB haben optional systemeigene Abhängigkeiten und funktionieren mit Anwendungen, die in Azure-Websites gehostet werden.

### Verwenden einer package.json-Datei

Mit der Datei **package.json** können Sie die von der Anwendung benötigten Abhängigkeiten auf oberster Ebene angeben, sodass die Hostingplattform die Abhängigkeiten installieren kann. Somit muss der Ordner **node\_packages** nicht als Teil der Bereitstellung enthalten sein. Nachdem die Anwendung bereitgestellt wurde, wird der Befehl **npm install** verwendet, um die Datei **package.json** zu analysieren und alle aufgelisteten Abhängigkeiten zu installieren.

Während der Entwicklung können Sie bei der Installation von Modulen die Parameter **--save**, **--save-dev** oder **--save-optional** verwenden, um automatisch einen Eintrag für das Modul zur Datei**package.json** hinzuzufügen. Weitere Informationen hierzu finden Sie unter [npm-install](https://npmjs.org/doc/install.html).

Ein potenzielles Problem bei der Datei **package.json** ist, dass nur die Version für die Abhängigkeiten auf oberster Ebene angegeben wird. Manche installierten Module geben die Version der Module an, von denen sie abhängen, andere nicht. Somit ist es möglich, dass Sie eine andere Abhängigkeitskette erhalten als die in der Entwicklung verwendete.

> [WACOM.NOTE] 
> Falls die Datei **package.json** auf ein systemeigenes Modul verweist, wird Ihnen bei einer Bereitstellung zu einer Azure-Website ein Fehler angezeigt, der folgendem Fehler ähnelt, wenn die Anwendung mithilfe von Git veröffentlicht wird:

>        npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>        npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1    

### Verwenden einer npm-shrinkwrap.json-Datei

Die Datei **npm-shrinkwrap.json** ist ein Versuch, die Modulversionsbegrenzungen der Datei **package.json** zu umgehen. Während die Datei **package.json** nur Versionen der Module auf oberster Ebene enthält, enthält die Datei **npm-shrinkwrap.json** die Versionsanforderungen für die vollständige Modulabhängigkeitskette.

Wenn die Anwendung für die Produktion bereit ist, können Sie die Versionsanforderungen sperren und eine Datei **npm-shrinkwrap.json** über den Befehl **npm shrinkwrap** erstellen. Diese verwendet die derzeit im Ordner **node\_modules** installierten Versionen, und speichert sie in der Datei **npm-shrinkwrap.json**. Nachdem die Anwendung zur Hostingumgebung bereitgestellt wurde, wird der Befehl **npm install** verwendet, um die Datei **npm-shrinkwrap.json** zu analysieren und alle aufgelisteten Abhängigkeiten zu installieren. Weitere Informationen hierzu finden Sie unter [npm-install](https://npmjs.org/doc/install.html).

> [WACOM.NOTE] 
> Falls die Datei **npm-shrinkwrap.json** auf ein systemeigenes Modul verweist, wird Ihnen bei einer Bereitstellung zu einer Azure-Website ein Fehler angezeigt, der folgendem Fehler ähnelt, wenn die Anwendung mithilfe von Git veröffentlicht wird:

>        npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>        npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1

Nächste Schritte
----------------

Nachdem Sie wissen, wie Sie Node.js-Module mit Azure verwenden, erfahren Sie nun, wie Sie [die Node.js-Version angeben](/de-de/develop/nodejs/common-tasks/specifying-a-node-version/), [eine Node.js-Website erstellen und bereitstellen](/de-de/develop/nodejs/tutorials/create-a-website-(mac)/) und [wie Sie die Azure-Befehlszeilentools für Mac und Linux verwenden](/de-de/develop/nodejs/how-to-guides/command-line-tools/).


[specify the Node.js version]: /de-de/develop/nodejs/common-tasks/specifying-a-node-version/
[How to use the Azure Command-Line Tools for Mac and Linux]: /de-de/develop/nodejs/how-to-guides/command-line-tools/
[build and deploy a Node.js Web Site]: /de-de/develop/nodejs/tutorials/create-a-website-(mac)/
[Node.js Web Application with Storage on MongoDB (MongoLab)]: /de-de/develop/nodejs/tutorials/website-with-mongodb-mongolab/
[Publishing with Git]: /de-de/develop/nodejs/common-tasks/publishing-with-git/
[Build and deploy a Node.js application to an Azure Cloud Service]: /de-de/develop/nodejs/tutorials/getting-started/