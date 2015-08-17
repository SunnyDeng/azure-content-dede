<properties
	pageTitle="Die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows"
	description="Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows zum Verwalten von Azure-Diensten"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="squillace"
	services=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2015"
	ms.author="rasquill"/>

# Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Azure CLI](xplat-cli.md)

Die Azure-Befehlszeilenschnittstelle stellt eine Reihe von plattformübergreifenden Open Source-Befehlen für die Arbeit mit der Azure-Plattform bereit. Sie bietet ähnliche Funktionen wie das Azure-Verwaltungsportal – etwa die Verwaltung von Websites, virtuellen Computern, mobilen Diensten, SQL-Datenbanken und anderen Diensten, die über die Azure-Plattform bereitgestellt werden.

Die Azure-Befehlszeilenschnittstelle basiert auf JavaScript und erfordert „Node.js“. Sie wird mithilfe des Azure SDK für Node.js implementiert und unter einer Apache 2.0-Lizenz veröffentlicht. Das Projekt-Repository befindet sich unter [https://github.com/WindowsAzure/azure-sdk-tools-xplat](https://github.com/azure/azure-xplat-cli).

In diesem Dokument erfahren Sie, wie Sie die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows installieren und konfigurieren und wie Sie damit allgemeine Aufgaben mit der Azure-Plattform ausführen.

<a id="install"></a>
## Gewusst wie: Installieren der Azure-Befehlszeilenschnittstelle

Die Installationsschritte für die Azure-Befehlszeilenschnittstelle finden auf der Seite [Installieren der Azure-Befehlszeilenschnittstelle](xplat-cli-install.md).


<a id="configure"></a>
## Verbinden mit dem Azure-Abonnement

Einige der Befehle der Azure-Befehlszeilenschnittstelle können zwar auch ohne Azure-Abonnement verwendet werden, für die meisten Befehle ist jedoch ein Abonnement erforderlich. Informationen zum Konfigurieren der Azure-Befehlszeilenschnittstelle für die Verwendung mit Ihrem Abonnement finden Sie unter [Herstellen einer Verbindung mit einem Azure-Abonnement über die Azure-Befehlszeilenschnittstelle](xplat-cli-connect.md).


<a id="use"></a>
## Verwenden der Azure-Befehlszeilenschnittstelle

Die Azure-Befehlszeilenschnittstelle wird mit dem Befehl `azure` aufgerufen. Wenn Sie den Befehl `azure` ohne Parameter verwenden, erscheint eine Liste mit verfügbaren Befehlen. Die angezeigten Hilfeinformationen sehen etwa wie folgt aus:

	info:             _    _____   _ ___ ___
	info:            /_\  |_  / | | | _ \ __|
	info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
	info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
	info:       (_______ _ _)         _ ______ _)_ _
	info:              (______________ _ )   (___ _ _)
	info:
	info:    Microsoft Azure: Microsoft's Cloud Platform
	info:
	info:    Tool version 0.8.10
	help:
	help:    Display help for a given command
	help:      help [options] [command]
	help:
	help:    Opens the portal in a browser
	help:      portal [options]
	help:
	help:    Commands:
	help:      account        Commands to manage your account information and publish settings
	help:      config         Commands to manage your local settings
	help:      hdinsight      Commands to manage your HDInsight accounts
	help:      mobile         Commands to manage your Mobile Services
	help:      network        Commands to manage your Networks
	help:      sb             Commands to manage your Service Bus configuration
	help:      service        Commands to manage your Cloud Services
	help:      site           Commands to manage your Web Sites
	help:      sql            Commands to manage your SQL Server accounts
	help:      storage        Commands to manage your Storage objects
	help:      vm             Commands to manage your Virtual Machines
	help:
	help:    Options:
	help:      -h, --help     output usage information
	help:      -v, --version  output the application version

Die oben aufgeführten Befehle der obersten Ebene enthalten Befehle für die Arbeit mit einem bestimmten Azure-Bereich. Der Befehl `azure account` enthält beispielsweise Befehle für Ihr Azure-Abonnement. Hierzu zählen etwa die zuvor verwendeten Einstellungen `download` und `import`. Ausführlichere Informationen zu den verfügbaren Befehlen und Optionen finden Sie unter „Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows“.

Die meisten Befehle werden im Format `azure <command> <operation> [parameters]` angegeben und führen einen Vorgang für einen Dienst oder ein Objekt (beispielsweise Ihre Kontokonfiguration) aus. Andere Befehle verfügen über untergeordnete Befehle und werden im Format `azure <command> <subcommand> <operation> [parameters]` angegeben. Mit den folgenden Beispielbefehlen wird Ihre Kontokonfiguration bearbeitet:

* Mit dem folgenden Befehl können Sie importierte Abonnements anzeigen:

		azure account list

* Wenn Sie Abonnements importiert haben, können Sie mit dem folgenden Befehl ein Abonnement als Standardabonnement festlegen:

		azure account set <subscription>

Mit dem Parameter `--help` oder `-h` können Sie Hilfe zu bestimmten Befehlen anzeigen. Alternativ kann auch das Format `azure help [command] [options]` verwendet werden, um diese Informationen zu erhalten. Beispielsweise geben die folgenden Befehle die gleichen Informationen zurück:

	azure account set --help

	azure account set -h

	azure help account set

Wenn Sie sich nicht sicher sind, welche Parameter für einen Befehl angegeben werden müssen, zeigen Sie mit `--help`, `-h` oder `azure help [command]` die entsprechende Hilfe an.

### Festlegen des Konfigurationsmodus

Mit der Azure-Befehlszeilenschnittstelle können Sie Verwaltungsvorgänge für einzelne _Ressourcen_ durchführen. Dabei kann es sich um vom Benutzer verwaltete Entitäten wie etwa einen Datenbankserver, eine Datenbank oder eine Website handeln. Dies ist der Standardbetriebsmodus der Azure-Befehlszeilenschnittstelle, der auch als **Azure-Dienstverwaltung** bezeichnet wird. Wenn jedoch eine komplexe Lösung vorliegt, die aus mehreren Ressourcen besteht, dann ist es hilfreich, wenn die gesamte Lösung als Einheit verwaltet werden kann.

Damit eine Gruppe von Ressourcen als eine logische Einheit oder _Ressourcengruppe_ verwaltet werden kann, haben wir eine Vorschau von **Ressourcen-Manager** als neue Möglichkeit zum Verwalten von Azure-Ressourcen eingeführt.

>[AZURE.NOTE]Der Ressourcen-Manager befindet sich derzeit in der Vorschau und bietet nicht denselben Umfang an Verwaltungsfunktionen wie die Azure-Dienstverwaltung.

Zur Unterstützung des neuen Ressourcen-Managers können Sie im Rahmen der Azure-Befehlszeilenschnittstelle mit dem Befehl `azure config mode` zwischen den Verwaltungsmodi wechseln.

Die Azure-Befehlszeilenschnittstelle verwendet standardmäßig den Azure-Dienstverwaltungsmodus. Mit dem folgenden Befehl können Sie zum Ressourcen-Manager-Modus wechseln:

	azure config mode arm

Verwenden Sie den folgenden Befehl, um wieder zum Azure-Dienstverwaltungsmodus zu wechseln:

	azure config mode asm

>[AZURE.NOTE]Der Ressourcen-Manager-Modus und der Azure-Dienstverwaltungsmodus schließen sich gegenseitig aus. Das heißt, dass Ressourcen, die in einem Modus erstellt wurden, nicht im anderen Modus verwaltet werden können.

Weitere Informationen zum Verwenden des Ressourcen-Managers mit der Azure-Befehlszeilenschnittstelle finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager][cliarm].

### Arbeiten mit Diensten im Azure-Dienstverwaltungsmodus

Mit der Azure-Befehlszeilenschnittstelle können Sie Azure-Dienste komfortabel verwalten. In diesem Beispiel erfahren Sie, wie Sie mit der Azure-Befehlszeilenschnittstelle eine Azure-Website verwalten.

1. Verwenden Sie den folgenden Befehl, um eine neue Azure-Website zu erstellen. Ersetzen Sie **meineWebsite** durch einen eindeutigen Namen.

		azure site create mywebsite

	Sie werden aufgefordert, die Region anzugeben, in der die Website erstellt wird. Wählen Sie eine geografisch nahegelegene Region aus. Nach Abschluss dieses Befehls ist die Website unter http://mywebsite.azurewebsites.net verfügbar. (Ersetzen Sie **mywebsite** durch den angegebenen Namen.)

	> [AZURE.NOTE]Wenn Sie Git für die Quellcodeverwaltung verwenden, können Sie den Parameter `--git` angeben, um bei Azure ein Git-Repository für diese Website zu erstellen. Damit wird auch ein Git-Repository in dem Verzeichnis initialisiert, aus dem der Befehl aufgerufen wurde, sofern noch keines vorhanden ist. Dadurch wird auch ein Git-Remote namens __azure__ erstellt, das verwendet werden kann, um mithilfe des Befehls `git push azure master` Push-Bereitstellungen für die Azure-Website vorzunehmen.

	> [AZURE.NOTE]Wird eine Fehlermeldung mit dem Hinweis angezeigt, dass „site“ kein Azure-Befehl ist, wird die Azure-Befehlszeilenschnittstelle wahrscheinlich im Ressourcengruppenmodus ausgeführt. Verwenden Sie den Befehl `azure config mode asm`, um zum Ressourcenmodus zurückzukehren.

2. Verwenden Sie den folgenden Befehl, um die Websites für Ihr Abonnement aufzulisten:

		azure site list

	Diese Liste sollte die Website enthalten, die Sie im vorigen Schritt erstellt haben.

2. Verwenden Sie zum Beenden der Website den folgenden Befehl. Ersetzten Sie **meineWebsite** durch den Namen der Website.

		azure site stop mywebsite

	Nach abgeschlossener Befehlsausführung können Sie den Browser aktualisieren, um sich davon zu überzeugen, dass die Website beendet wurde.

3. Verwenden Sie zum Starten der Website den folgenden Befehl. Ersetzten Sie **meineWebsite** durch den Namen der Website.

		azure site start mywebsite

	Nach abgeschlossener Befehlsausführung können Sie den Browser aktualisieren, um sich davon zu überzeugen, dass die Website gestartet wurde.

4. Verwenden Sie zum Löschen der Website den folgenden Befehl. Ersetzten Sie **meineWebsite** durch den Namen der Website.

		azure site delete mywebsite

	Verwenden Sie nach Abschluss des Befehls den Befehl `azure site list`, um sich zu vergewissern, dass die Website nicht mehr vorhanden ist.

<a id="script"></a>
## Erstellen von Skripts für die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows

Sie können die Azure-Befehlszeilenschnittstelle nicht nur für manuelle Befehle verwenden, sondern auch für komplexe Automatisierungsabläufe, bei denen die Funktionen des Befehlszeileninterpreters und anderer im System verfügbarer Befehlszeilenprogramme verwendet werden. Beispielsweise werden mit dem folgenden Befehl alle aktuell ausgeführten Azure-Websites beendet:

	azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

In diesem Beispiel wird dem Befehl `grep`, der jede Zeile nach der Zeichenfolge „Running“ durchsucht, eine Liste mit Websites übergeben. Alle übereinstimmenden Zeilen werden dann an den Befehl `awk` übergeben, der `azure site stop` aufruft und aus der zweiten übermittelten Spalte (Name der ausgeführten Website) den Namen der zu beendenden Website entnimmt.

Hier wird zwar gezeigt, wie Sie Befehle miteinander verketten können, aber Sie können mit den Skriptfunktionen, die der Befehlszeileninterpreter bereitstellt, auch ausgeklügelte Skripts erstellen. Skriptfunktionen und -syntax sind von Befehlszeileninterpreter zu Befehlszeileninterpreter unterschiedlich. Bash ist wahrscheinlich der gängigste Befehlszeileninterpreter für auf UNIX basierende Systeme, einschließlich Linux und OS X.

Weitere Informationen zur Skripterstellung mit Bash finden Sie im [Advanced Bash-Scripting Guide][advanced-bash].

Allgemeine Informationen zur Skripterstellung für OS X- oder Linux-basierte Systeme finden Sie unter [Shell script][script].

Weitere Informationen zur Skripterstellung für Windows-basierte Systeme mit Stapelverarbeitungsdateien finden Sie unter [Befehlszeilenreferenz A-Z][batch].

### Erläuterung der Ergebnisse

Beim Erstellen von Skripts müssen Sie häufig die Ausgabe eines Befehls erfassen und an einen anderen Befehl übergeben oder einen Vorgang mit der Ausgabe ausführen, z. B. die Ausgabe nach einem bestimmten Wert durchsuchen. Die Azure-Befehlszeilenschnittstelle generiert die Ausgabe in STDOUT und STDERR. Jede Zeile beginnt mit einem Präfix: `info:` steht für informative Statusmeldungen, `data:` für Daten, die an einen Dienst zurückgegeben wurden. Mit dem Parameter `--verbose` oder `-v` können Sie die Azure-Befehlszeilenschnittstelle allerdings anweisen, ausführlichere Informationen zurückzugeben. Dadurch werden zusätzliche Informationen (mit dem Präfix `verbose:`) zurückgegeben.

Die folgende Ausgabe ist ein Beispiel für die Rückgabe des Befehls `azure site list`:

	info:    Executing command site list
	+ Enumerating sites
	data:    Name           Status   Mode  Host names
	data:    -------------  -------  ----  -------------------------------
	data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
	info:    site list command OK

Wenn der Parameter `--verbose` oder `-v` angegeben wird, werden Informationen zurückgegeben, die etwa wie folgt aussehen:

	info:    Executing command site list
	verbose: Subscription ####################################
	verbose: Enumerating sites
	verbose: [
	verbose:     {
	verbose:         ComputeMode: 'Shared',
	verbose:         HostNameSslStates: {
	verbose:             HostNameSslState: [
	verbose:                 {
	verbose:                     IpBasedSslResult: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     SslState: 'Disabled',
	verbose:                     ToUpdateIpBasedSsl: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     VirtualIP: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     Thumbprint: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     ToUpdate: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     Name: 'myawesomesite.azurewebsites.net'
	verbose:                 },
	...
	verbose:     }
	verbose: ]
	data:    Name           Status   Mode  Host names
	data:    -------------  -------  ----  -------------------------------
	data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
	info:    site list command OK

Beachten Sie dass `verbose:`-Informationen im JSON-Datenformat dargestellt werden. Mit dem Parameter `--json` können Sie die Informationen im JSON-Format zurückgeben, falls Sie mit Hilfsprogrammen arbeiten, die das JSON-Format verarbeiten können (etwa [jsawk](https://github.com/micha/jsawk) oder [jq](http://stedolan.github.io/jq/)). Beispiel:

	azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q

Mit dem obigen Befehl wird eine Liste von Websites im JSON-Format abgerufen, dann werden mit jsawk die Namen der Websites abgerufen, und schließlich wird mit xargs ein Befehl zum Löschen von Websites für jede Website ausgeführt, wobei der Websitename als Parameter übergeben wird.

>[AZURE.NOTE]Der Parameter `--json` blockiert die Generierung von Status- oder Dateninformationen (Zeichenfolgen mit dem Präfix `info:` und `data:`). Wenn der Parameter `--json` beispielsweise für den Befehl `azure site create` angegeben wird, wird keine Ausgabe zurückgegeben, da dieser Befehl ausschließlich `info:`-Daten zurückgibt.

### Arbeiten mit Fehlern

Die Azure-Befehlszeilenschnittstelle protokolliert Fehlerinformationen in STDERR. Sie können zudem weitere Informationen zu Fehlern in eine Datei namens **azure.err** ausgeben lassen. Diese befindet sich dann in dem Verzeichnis, in dem das Skript aufgerufen wurde. Wenn Informationen in dieser Datei protokolliert werden, dann enthält STDOUT folgenden Eintrag:

	info:    Error information has been recorded to azure.err

### Beendigungsstatus

Einige Befehle der Azure-Befehlszeilenschnittstelle geben keinen Beendigungsstatus ungleich Null zurück, wenn erforderliche Parameter fehlen. Stattdessen wird der Benutzer zur Eingabe aufgefordert. Wenn Sie beispielsweise den Befehl `azure site create` zum Erstellen einer neuen Website verwenden und weder den Websitenamen noch den Parameter `--location` angeben, werden Sie zur Angabe dieser Werte aufgefordert.

Wenn Sie ein Skript schreiben, das auf dem Beendigungsstatus basiert, müssen Sie sicherstellen, dass die von Ihnen verwendeten Befehle der Azure-Befehlszeilenschnittstelle keine Benutzereingaben erfordern.

<a id="additional-resources"></a>

## Zusätzliche Ressourcen

* [Verwenden der Azure-Befehlszeilenschnittstelle mit der Dienstverwaltung][Using the Azure CLI]

* [Verwenden der Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager][cliarm]

* Wenn Sie weitere Informationen zur Azure-Befehlszeilenschnittstelle erhalten, Quellcode herunterladen, Probleme melden oder etwas zum Projekt beitragen möchten, dann besuchen Sie die Webseite [GitHub repository for the Azure CLI](https://github.com/azure/azure-xplat-cli).

* Wenn Sie Probleme bei der Verwendung der Azure-Befehlszeilenschnittstelle oder Azure haben, besuchen Sie die [Azure-Foren](http://social.msdn.microsoft.com/Forums/windowsazure/home).

* Weitere Informationen zu Azure finden Sie unter [http://azure.microsoft.com/](http://azure.microsoft.com).




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert

[Azure Web Site]: ../media/freetrial.png
[select a preview feature]: ../media/antares-iaas-preview-02.png
[select subscription]: ../media/antares-iaas-preview-03.png
[free-trial]: http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E
[advanced-bash]: http://tldp.org/LDP/abs/html/
[script]: http://en.wikipedia.org/wiki/Shell_script
[batch]: http://technet.microsoft.com/library/bb490890.aspx
[cliarm]: xplat-cli-azure-resource-manager.md
[portal]: https://manage.windowsazure.com
[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[Using the Azure CLI]: virtual-machines-command-line-tools.md

<!---HONumber=August15_HO6-->