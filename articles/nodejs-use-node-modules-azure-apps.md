<properties pageTitle="Arbeiten mit Node.js-Modulen" description="Sie erfahren etwas über die Arbeit mit Node.js-Modulen im Zusammenhang mit Azure Websites oder Cloud Services." services="" documentationCenter="nodejs" authors="MikeWasson" manager="wpickett" editor="mollybos"/>

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="02/19/2015" ms.author="mwasson"/>





# Verwenden von Node.js-Modulen mit Azure-Anwendungen

Dieses Dokument bietet Anweisungen zur Verwendung von Node.js-Modulen mit Anwendungen, die auf Azure gehostet werden. Sie erfahren, wie Sie sicherstellen können, dass eine Anwendung eine bestimmte Version eines Moduls sowie systemeigene Module von Azure verwendet.

Wenn Sie sich bereits mit Node.js-Modulen, **package.json**- und **npm-shrinkwrap.json**-Dateien auskennen, ist folgender Artikel eine kurze Zusammenfassung der in diesem Artikel diskutierten Inhalte:

* Azure-Websites verstehen **package.json**- und **npm-shrinkwrap.json**-Dateien und können Module basierend auf den Einträgen in diesen Dateien installieren.
* Azure Cloud Services erfordern, dass alle Module auf der Entwicklungsumgebung installiert sind und das Verzeichnis **node_modules** als Teil des Bereitstellungspakets enthalten ist.

> [AZURE.NOTE]Azure Virtual Machines werden in diesem Artikel nicht behandelt, da die Bereitstellung auf einem virtuellen Computer vom Betriebssystem abhängig ist, das von dem virtuellen Computer gehostet wird.

> [AZURE.NOTE]Sie können die Unterstützung für die Installation von Modulen mithilfe der Dateien **package.json** oder **npm-shrinkwrap.json** auf Azure aktivieren, dies erfordert jedoch die Anpassung der Standardskripts, die von Clouddienstprojekten verwendet werden. Ein Beispiel dafür finden Sie unter [Azure Startup task to run npm install to avoid deploying node modules](http://nodeblog.azurewebsites.net/startup-task-to-run-npm-in-azure) (Azure-Startaufgabe zum Ausführen von npm install, um keine Node.js-Module bereitstellen zu müssen, in englischer Sprache)

##Node.js-Module

Module sind ladbare JavaScript-Pakete, die bestimmte Funktionen für die Anwendung bieten. Ein Modul wird normalerweise über das Befehlszeilentool **npm** installiert. Einige Module (wie das http-Modul) werden jedoch als Teil des Node.js-Pakets bereitgestellt.

Wenn Module installiert werden, werden diese im Verzeichnis **node_modules** im Stamm der Anwendungsverzeichnisstruktur gespeichert. Jedes Modul im Verzeichnis **node_modules** behält sein eigenes **node_modules**-Verzeichnis, das alle Module enthält, von denen es abhängt. Dies wiederholt sich für jedes Modul auf der Abhängigkeitskette. Dadurch kann jedes installierte Modul seine eigenen Versionsanforderungen für die Module haben, von denen es abhängt. Dies kann jedoch zu einer sehr großen Verzeichnisstruktur führen.

Wenn das Verzeichnis **node_modules** als Teil der Anwendung bereitgestellt wird, vergrößert dies die Bereitstellung im Vergleich zur Verwendung einer Datei **package.json** oder **npm-shrinkwrap.json**. Dies garantiert jedoch, dass die Version der in der Produktion verwendeten Module identisch mit derjenigen in der Entwicklung ist.

###Systemeigene Module

Auch wenn die meisten Module einfache Nur-Text-JavaScript-Dateien sind, gibt es auch Module, die plattformspezifische Binärimages sind. Diese Module werden bei der Installation gebildet, was normalerweise mit Python und node-gyp erfolgt. Da Azure Cloud Services auf den Ordner **node_modules** angewiesen sind, der als Teil der Anwendung bereitgestellt wird, sollte ein systemeigenes Modul, das als Teil der installierten Module enthalten ist, in einem Clouddienst funktionieren, solange dieser auf einem Windows-Entwicklungssystem erstellt wurde.

Azure Websites unterstützt nicht alle systemeigenen Module, sodass eine Kompilierung dieser Module unter sehr spezifischen Voraussetzungen möglicherweise fehlschlägt. Während einige beliebte Module wie MongoDB optionale systemeigene Abhängigkeiten besitzen, aber problemlos ohne diese funktionieren, erwiesen sich zwei Problemumgehungen als sehr erfolgreich mit fast allen zurzeit verfügbaren systemeigenen Modulen:

* Führen Sie **npm install** auf einem Windows-Computer aus, auf dem alle Voraussetzungen für das jeweilige systemeigene Modul installiert sind. Stellen Sie anschließend den erstellten Ordner **node_modules** als Teil der Anwendung in Azure Websites bereit.
* Azure Websites kann für die Ausführung benutzerdefinierter Bash- oder Shell-Skripts während der Bereitstellung konfiguriert werden, sodass Sie die Möglichkeit haben, benutzerdefinierte Befehle auszuführen und exakt zu konfigurieren, wie **npm install** ausgeführt wird. Ein Video mit der entsprechenden Vorgehensweise finden Sie unter [Benutzerdefinierte Website-Bereitstellungsskripts mit Kudu].

###Verwenden einer package.json-Datei

Mit der Datei **package.json** können Sie die von der Anwendung benötigten Abhängigkeiten auf oberster Ebene angeben, sodass die Hostingplattform die Abhängigkeiten installieren kann. Somit muss der Ordner **node_packages** nicht als Teil der Bereitstellung enthalten sein. Nachdem die Anwendung bereitgestellt wurde, wird der Befehl **npm install** verwendet, um die Datei **package.json** zu analysieren und alle aufgelisteten Abhängigkeiten zu installieren.

Während der Entwicklung können Sie bei der Installation von Modulen die Parameter **--save**, **--save-dev** oder **--save-optional** verwenden, um automatisch einen Eintrag für das Modul zur Datei**package.json** hinzuzufügen. Weitere Informationen hierzu finden Sie unter [npm-install](https://npmjs.org/doc/install.html).

Ein potenzielles Problem bei der Datei **package.json** ist, dass nur die Version für die Abhängigkeiten auf oberster Ebene angegeben wird. Manche installierten Module geben die Version der Module an, von denen sie abhängen, andere nicht. Somit ist es möglich, dass Sie eine andere Abhängigkeitskette erhalten als die in der Entwicklung verwendete.

> [AZURE.NOTE]Falls die Datei <b>package.json</b> auf ein systemeigenes Modul verweist, wird Ihnen bei der Bereitstellung auf einer Azure-Website ein Fehler ähnlich dem folgenden angezeigt, wenn die Anwendung mithilfe von Git veröffentlicht wird:

>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


###Verwenden einer npm-shrinkwrap.json-Datei

Die Datei **npm-shrinkwrap.json** ist ein Versuch, die Modulversionsbegrenzungen der Datei **package.json** zu umgehen. Während die Datei **package.json** nur Versionen der Module auf oberster Ebene enthält, enthält die Datei **npm-shrinkwrap.json** die Versionsanforderungen für die vollständige Modulabhängigkeitskette.

Wenn die Anwendung für die Produktion bereit ist, können Sie die Versionsanforderungen sperren und eine Datei **npm-shrinkwrap.json** über den Befehl **npm shrinkwrap** erstellen. Diese verwendet die derzeit im Ordner **node_modules** installierten Versionen, und speichert sie in der Datei **npm-shrinkwrap.json**. Nachdem die Anwendung zur Hostingumgebung bereitgestellt wurde, wird der Befehl **npm install** verwendet, um die Datei **npm-shrinkwrap.json** zu analysieren und alle aufgelisteten Abhängigkeiten zu installieren. Weitere Informationen hierzu finden Sie unter [npm-install](https://npmjs.org/doc/install.html).

> [AZURE.NOTE]Falls die Datei <b>npm-shrinkwrap.json</b> auf ein systemeigenes Modul verweist, wird Ihnen bei der Bereitstellung auf einer Azure-Website ein Fehler ähnlich dem folgenden angezeigt, wenn die Anwendung mithilfe von Git veröffentlicht wird:

>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##Nächste Schritte

Nachdem Sie wissen, wie Sie Node.js-Module mit Azure verwenden, erfahren Sie nun, wie Sie [die Node.js-Version angeben], [eine Node.js-Website erstellen und bereitstellen] und [die Azure-Befehlszeilenschnittstelle für Mac und Linux verwenden].

[die Node.js-Version angeben]: nodejs-specify-node-version-azure-apps.md
[die Azure-Befehlszeilenschnittstelle für Mac und Linux verwenden]: xplat-cli.md
[eine Node.js-Website erstellen und bereitstellen]: web-sites-nodejs-develop-deploy-mac.md
[Node.js Web Application with Storage on MongoDB (MongoLab)]: store-mongolab-web-sites-nodejs-store-data-mongodb.md
[Publishing with Git]: web-sites-publish-source-control.md
[Build and deploy a Node.js application to an Azure Cloud Service]: cloud-services-nodejs-develop-deploy-app.md
[Benutzerdefinierte Website-Bereitstellungsskripts mit Kudu]: /documentation/videos/custom-web-site-deployment-scripts-with-kudu/

<!---HONumber=July15_HO4-->