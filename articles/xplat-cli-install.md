<properties
	pageTitle="Installieren der Azure-Befehlszeilenschnittstelle | Microsoft Azure"
	description="Installieren der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows, um Azure Services zu nutzen"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="danlep"/>

# Installieren der Azure-Befehlszeilenschnittstelle

Installieren Sie schnell die Azure-Befehlszeilenschnittstelle (Azure-CLI), um eine Reihe von auf der Open-Source-Shell basierenden Befehlen zum Erstellen und Verwalten von Ressourcen in Microsoft Azure zu verwenden. Verwenden Sie eines der bereitgestellten Installationspakete zum Installieren der Azure-Befehlszeilenschnittstelle in Ihrem Betriebssystem. Installieren Sie die CLI über Node.js und **npm**, oder installieren Sie die Azure-CLI als Container auf einem Docker-Host. Weitere Optionen und Hintergrundinformationen finden Sie im Projektrepository auf [GitHub](https://github.com/azure/azure-xplat-cli).

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-both-include.md)]

Sobald die Azure-CLI installiert ist, können Sie sie [mit Ihrem Azure-Abonnement verbinden](xplat-cli-connect.md) und die **azure**-Befehle in der Befehlszeilenschnittstelle (Bash, Terminal, Eingabeaufforderung usw.) für die Arbeit mit Ihren Azure-Ressourcen verwenden.




## Verwenden eines Installers

Die folgenden Installer-Pakete sind verfügbar:

* [Windows Installer][windows-installer]

* [OS X Installer](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Linux Installer][linux-installer]


## Installieren von "Node.js" und "npm"

Wenn Node.js bereits auf Ihrem System installiert ist, verwenden Sie den folgenden Befehl, um die Azure-Befehlszeilenschnittstelle zu installieren:

	npm install azure-cli -g

> [AZURE.NOTE]Bei Linux-Distributionen müssen Sie möglicherweise `sudo` verwenden, um den Befehl __npm__ erfolgreich ausführen zu können.

### Installieren von „Node.js“ und „npm“ in Linux-Distributionen, die die [dpkg](http://en.wikipedia.org/wiki/Dpkg)-Paketverwaltung verwenden

Die am häufigsten verwendeten Distributionen verwenden entweder das [Advanced Packaging Tool (apt)](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool) oder andere Tools, die auf dem `.deb`-Paketformat basieren. Beispiele hierfür sind Ubuntu und Debian.

Die meisten der neueren Distributionen erfordern die Installation von **nodejs-legacy**, um ein ordnungsgemäß konfiguriertes **npm**-Tool zum Installieren der Azure-Befehlszeilenschnittstelle zu erhalten. Der folgende Code zeigt die Befehle, mit denen **npm** ordnungsgemäß auf Ubuntu 14.04 installiert wird.

	sudo apt-get install nodejs-legacy
	sudo apt-get install npm
	sudo npm install -g azure-cli

Einige der älteren Distributionen, wie z. B. Ubuntu 12.04, erfordern die Installation der aktuellen binären Distribution von „Node.js“. Der folgende Code zeigt, wie Sie dazu durch die Installation und Verwendung von **curl** vorgehen müssen.

>[AZURE.NOTE]Die Befehle stammen aus den Installationsanweisungen, die Sie [hier](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager) finden. Bei der Verwendung von **sudo** als Pipeziel sollten Sie immer die Skripts überprüfen, die Sie installieren, und prüfen, ob sie sich so verhalten, wie Sie es erwarten, bevor Sie sie mit **sudo** ausführen. Je größer die Macht desto größer ist auch die Verantwortung.

	sudo apt-get install curl
	curl -sL https://deb.nodesource.com/setup | sudo bash -
	sudo apt-get install -y nodejs
	sudo npm install -g azure-cli

### Installieren von „Node.js“ und „npm“ in Linux-Distributionen, die die [rpm](http://en.wikipedia.org/wiki/RPM_Package_Manager)-Paketverwaltung verwenden

Für die Installation von „Node.js“ auf RPM-basierten Distributionen ist die Aktivierung des EPEL-Repository erforderlich. Der folgende Code zeigt die bewährten Methoden für die Installation auf CentOS 7. (Beachten Sie, dass der „-“ (Bindestrich) in der ersten Zeile wichtig ist!)

	su -
	yum update [enter]
	yum upgrade –y [enter]
	yum install epel-release [enter]
	yum install nodejs [enter]
	yum install npm [enter]
	npm install -g azure-cli  [enter]

### Installieren von „Node.js“ und „npm“ unter Windows und Mac OS X

Sie können „Node.js“ und „npm“ mit den Installationsprogrammen von [Nodejs.org](https://nodejs.org/en/download/) unter Windows und OS X installieren. Möglicherweise müssen Sie den Computer zum Abschließen der Installation neu starten. Überprüfen Sie, ob „Node.js“ und „npm“ ordnungsgemäß installiert wurden, indem Sie ein Befehls- oder Terminalfenster öffnen und Folgendes eingeben:

	npm -v

Wenn die Version der npm-Installation angezeigt wird, können Sie fortfahren und die Azure-Befehlszeilenschnittstelle installieren:

	npm install -g azure-cli

Am Ende der Installation sollte eine Ausgabe angezeigt werden, die etwa wie folgt aussieht:

	azure-cli@0.8.0 ..\node_modules\azure-cli
	|-- easy-table@0.0.1
	|-- eyes@0.1.8
	|-- xmlbuilder@0.4.2
	|-- colors@0.6.1
	|-- node-uuid@1.2.0
	|-- async@0.2.7
	|-- underscore@1.4.4
	|-- tunnel@0.0.2
	|-- omelette@0.1.0
	|-- github@0.1.6
	|-- commander@1.0.4 (keypress@0.1.0)
	|-- xml2js@0.1.14 (sax@0.5.4)
	|-- streamline@0.4.5
	|-- winston@0.6.2 (cycle@1.0.2, stack-trace@0.0.7, async@0.1.22, pkginfo@0.2.3, request@2.9.203)
	|-- kuduscript@0.1.2 (commander@1.1.1, streamline@0.4.11)
	|-- azure@0.7.13 (dateformat@1.0.2-1.2.3, envconf@0.0.4, mpns@2.0.1, mime@1.2.10, validator@1.4.0, xml2js@0.2.8, wns@0.5.3, request@2.25.0)

>[AZURE.NOTE]Bei Linux-Systemen können Sie die Azure-Befehlszeilenschnittstelle auch installieren, erstellen Sie sie aus dem [Quellcode](http://go.microsoft.com/fwlink/?linkid=253472) neu erstellen. Weitere Informationen zum Erstellen aus dem Quellcode finden Sie in der Datei „INSTALL“, die im Archiv enthalten ist.

## Verwenden eines Docker-Containers

Führen Sie auf einem Docker-Host folgenden Befehl aus:

```
docker run -it microsoft/azure-cli
```

## Ausführen von Azure-CLI-Befehlen
Sobald die Azure-CLI installiert ist, können Sie den Befehl **azure** in der Befehlszeilenschnittstelle (Bash, Terminal, Eingabeaufforderung usw.) ausführen. Um beispielsweise den Hilfebefehl auszuführen, geben Sie Folgendes ein:

```
azure help
```

Um die installierte Version der Azure-Befehlszeilenschnittstelle anzuzeigen, geben Sie Folgendes ein:

```
azure --version
```

Jetzt sind Sie soweit. Um Zugriff auf alle CLI-Befehle für die Arbeit mit Ihren eigenen Ressourcen zu erhalten, [stellen Sie über die Azure-CLI eine Verbindung mit Ihrem Azure-Abonnement her](xplat-cli-connect.md).

## Aktualisieren der CLI

Microsoft veröffentlicht regelmäßig aktualisierte Versionen der Azure-Befehlszeilenschnittstelle. Installieren Sie die CLI erneut mit dem Installationsprogramm für Ihr Betriebssystem, bzw., wenn „Node.js“ und „npm“ installiert sind, aktualisieren Sie sie, indem Sie Folgendes eingeben (in Linux-Distributionen müssen Sie möglicherweise **sudo** verwenden).

```
npm upgrade -g azure-cli
```

## Zusätzliche Ressourcen

* [Verwenden der Azure-CLI mit dem Azure-Ressourcen-Manager (ARM)][cliarm]

* [Verwenden der Azure-CLI mit Azure Service Management][cliasm]

* Wenn Sie weitere Informationen zur Azure-Befehlszeilenschnittstelle erhalten, den Quellcode herunterladen, Probleme melden oder etwas zum Projekt beitragen möchten, besuchen Sie die Webseite [GitHub repository for the Azure CLI](https://github.com/azure/azure-xplat-cli).

* Wenn Sie Fragen zur Verwendung der Azure-Befehlszeilenschnittstelle oder zu Azure haben, besuchen Sie die [Azure-Foren](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=azurescripting).



[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0114_2016-->