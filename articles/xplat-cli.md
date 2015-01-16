<properties urlDisplayName="Azure Cross-Platform Command-Line Interface" pageTitle="Die plattformübergreifende Azure-Befehlszeilenschnittstelle" title="Die plattformübergreifende Azure-Befehlszeilenschnittstelle" metaKeywords="plattformübergreifende Azure-Befehlszeilenschnittstelle, Azure-Befehlszeile, azure Befehlszeile, azure cli" description="Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle zur Verwaltung von Azure Services" metaCanonical="http://www.windowsazure.com/en-us/script/xplat-cli-intro" umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="need to identify contact" documentationCenter="" solutions="" authors="carolz" services="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="carolz" />

#Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/manage/install-and-configure-windows-powershell/" title="PowerShell">PowerShell</a><a href="/en-us/manage/install-and-configure-cli/" title="Cross-Platform CLI" class="current">Plattformübergreifende Befehlszeilenschnittstelle</a></div>

Die plattformübergreifende Azure-Befehlszeilenschnittstelle stellt eine Reihe von plattformübergreifenden Open Source-Befehlen für die Arbeit mit der Azure-Plattform bereit. Sie bietet ähnliche Funktionen wie das Azure-Verwaltungsportal, z. B. Funktionen zum Verwalten von Websites, virtuellen Computern, mobilen Diensten, SQL-Datenbanken und anderen Diensten, die auf der Azure Platform zur Verfügung stehen.

Die plattformübergreifende Befehlszeilenschnittstelle ist in JavaScript geschrieben und setzt "Node.js" voraus. Sie wird mit dem Azure SDK für Node.js implementiert und unter einer Apache 2.0-Lizenz veröffentlicht. Das Projektrepository steht auf der folgenden Website zur Verfügung: [https://github.com/WindowsAzure/azure-sdk-tools-xplat](https://github.com/WindowsAzure/azure-sdk-tools-xplat).

In diesem Dokument wird beschrieben, wie Sie die plattformübergreifende Azure-Befehlszeilenschnittstelle installieren und konfigurieren und wie Sie sie zum Ausführen allgemeiner Aufgaben auf der Azure-Plattform verwenden.

##Inhalt dieses Dokuments

* [Installieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle](#install)
* [Verbinden mit Ihrem Azure-Abonnement](#configure)
* [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle](#use)
* [Erstellen von Skripts für die plattformübergreifende Azure-Befehlszeilenschnittstelle](#script)
* [Zusätzliche Ressourcen](#additional-resources)

<h2><a id="install"></a>Installieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle</h2>

Die plattformübergreifende Befehlszeilenschnittstelle kann auf zweierlei Weise installiert werden: mit den Installer-Paketen für Windows und OS X oder mit dem Befehl **npm**, wenn "Node.js" auf dem System installiert ist.

Bei Linux-Systemen muss "Node.js" installiert sein und müssen Sie entweder **npm** zur Installation der plattformübergreifenden Befehlszeilenschnittstelle wie unten beschrieben verwenden oder sie aus dem Quellcode erstellen. Der Quellcode ist auf der folgenden Webseite verfügbar: [http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409](http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409). Weitere Informationen zum Erstellen aus dem Quellcode finden Sie in der Datei "INSTALL", die im Archiv enthalten ist.

	Sobald die plattformübergreifende Befehlszeilenschnittstelle installiert worden ist, können Sie von Ihrer Befehlszeilenschnittstelle (Bash, Terminal, Eingabeaufforderung) aus mit dem Befehl **azure** auf die Befehle der plattformübergreifenden Befehlszeilenschnittstelle zugreifen.

###Verwenden eines Installer

Die folgenden Installer-Pakete sind verfügbar:

* [Windows Installer][windows-installer]

* [OS X Installer][mac-installer]

###Verwenden von npm

Wenn "Node.js" auf Ihrem System installiert ist, können Sie die plattformübergreifende Befehlzeilenschnittstelle mit dem folgenden Befehl installieren:

	npm install azure-cli -g

>[WACOM.NOTE] Möglicherweise müssen Sie "sudo" verwenden, um den Befehl "npm" erfolgreich ausführen zu können.

Damit werden die plattformübergreifende Befehlzeilenschnittstelle und die erforderlichen Abhängigkeiten installiert. Am Ende der Installation sollte eine Ausgabe angezeigt werden, die etwa wie folgt aussieht:

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

> [WACOM.NOTE]Node.js kann unter <a href="http://nodejs.org/">http://nodejs.org/</a> installiert werden.

<h2><a id="Configure"></a>Verbinden mit Ihrem Azure-Abonnement</h2>

Einige in der plattformübergreifenden Befehlszeilenschnittstelle verfügbaren Befehle funktionieren zwar auch ohne Azure-Abonnement, für die meisten Befehle ist jedoch ein Abonnement erforderlich. Um die plattformübergreifende Befehlszeilenschnittstelle für die Verwendung mit Ihrem Abonnement zu konfigurieren, können Sie eine der folgenden Aktionen ausführen:

* Herunterladen und Verwenden einer Datei mit Veröffentlichungseinstellungen.

OR

* Anmelden bei Azure mit einem Organisationskonto-Konto. Bei der Anmeldung werden die Anmeldeinformationen unter Verwendung von Azure Active Directory authentifiziert.

Beachten Sie bezüglich der Auswahl der geeigneten Authentifizierungsmethode Folgendes:

*  Bei der Anmeldemethode kann der Zugriff auf das Abonnement leichter zu verwalten sein, aber die Automatisierung möglicherweise unterbrochen werden, da das Zeitlimit für die Anmeldeinformationen überschritten werden kann und Sie sich dann erneut anmelden müssen.

	> [WACOM.NOTE]Die Anmeldemethode funktioniert nur bei Verwendung eines Unternehmenskontos.  Ein Organisations-Konto ist ein Benutzer, der von der Organisation verwaltet wird und in der Organisation als Azure Active Directory-Mandant definiert ist. Wenn Sie derzeit kein Organisationskonto besitzen und sich mit einem Microsoft-Konto bei Ihrem Azure-Abonnement anmelden, können Sie wie folgt mühelos ein Organisationskonto erstellen:
	> 
	> 1. Melden Sie sich beim [Azure-Verwaltungsportal][] an, und klicken Sie auf **Active Directory**.
	> 
	> 2. Wenn kein Verzeichnis vorhanden ist, wählen Sie **Create your directory** (Verzeichnis erstellen) aus, und stellen Sie die geforderten Informationen bereit.
	> 
	> 3. Wählen Sie Ihr Verzeichnis aus, und fügen Sie einen neuen Benutzer hinzu. Dieser neue Benutzer ist ein Organisationskonto-Konto.
	> 
	>     Während der Erstellung des Benutzers erhalten Sie sowohl eine E-Mail-Adresse für den Benutzer als auch ein temporäres Kennwort. Speichern Sie diese Informationen, da sie in einem anderen Schritt benötigt werden.
	> 
	> 4. Wählen Sie im Verwaltungsportal **Einstellungen** und anschließend **Administratoren** aus. Wählen Sie **Hinzufügen** aus, und fügen Sie den neuen Benutzer als Co-Administrator hinzu. Dies ermöglicht es dem Organisationskonto, Ihr Azure-Abonnement zu verwalten.
	> 
	> 5. Melden Sie sich schließlich vom Azure-Portal ab und dann unter Verwendung des Organisationskontos erneut beim Azure-Portal an. Wenn Sie sich zu diesem Zeitpunkt zum ersten Mal mit diesem Konto anmelden, werden Sie aufgefordert, das Kennwort zu ändern.
	>
	>Weitere Informationen zu Organisationskonto-Konten in Microsoft Azure finden Sie unter [Anmelden bei Microsoft Azure als Organisation][signuporg].

*  	Bei der Methode mit der Veröffentlichungseinstellungendatei wird ein Zertifikat installiert, das es Ihnen ermöglicht, solange Verwaltungsaufgaben auszuführen, wie das Abonnement und das Zertifikat gültig sind. Auf diese Weise ist es einfacher, Automatisierung für Aufgaben mit langer Ausführungszeit einzusetzen. Nach dem Herunterladen und Importieren der Informationen müssen Sie diese nicht erneut angeben. Diese Methode erschwert jedoch die Verwaltung des Zugriffs auf ein Abonnement, da jeder, der Zugriff auf das Zertifikat hat, das Abonnement verwalten kann.

Weitere Informationen zur Authentifizierung und Abonnementverwaltung finden Sie unter ["Was ist der Unterschied zwischen kontobasierter Authentifizierung und zertifikatbasierter Authentifizierung"][authandsub].

Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion][.]

###Verwenden der Anmeldemethode

Verwenden Sie den folgenden Befehl, um sich mit einem Organisations-Konto anzumelden:

azure login [Benutzername] [Kennwort]

> [WACOM.NOTE]Wenn Sie sich zu diesem Zeitpunkt zum ersten Mal mit diesen Anmeldeinformationen anmelden, werden Sie aufgefordert zu bestätigen, ob ein Authentifizierungstoken im Cache gespeichert werden soll. Diese Eingabeaufforderung wird auch angezeigt, wenn Sie zuvor den unten beschriebenen Befehl "azure logout" verwendet haben. Um diese Eingabeaufforderung in Automatisierungsszenarien zu umgehen, geben Sie den Parameter "-q" an.
>
> Wenn Sie sich mit einem Organisationskonto authentifizieren, werden die Informationen zum Zugriff auf Ihr Azure-Abonnement in einem ".azure"-Verzeichnis in Ihrem "user"-Verzeichnis gespeichert. Ihr user-Verzeichnis wird von Ihrem Betriebssystem geschützt. Es wird jedoch empfohlen, dass Sie Ihr user-Verzeichnis zusätzlich verschlüsseln. Sie haben dazu folgende Möglichkeiten:

Verwenden Sie den folgenden Befehl, um sich abzumelden:

	azure logout [Benutzername]

> [WACOM.NOTE]Wenn die mit dem Konto verknüpften Abonnements nur über Active Directory authentifiziert wurden, dann werden die Abonnementinformationen durch die Abmeldung aus dem lokalen Profil gelöscht. Wurde allerdings auch eine Veröffentlichungseinstellungendatei für die Abonnements importiert, dann werden nur die auf Active Directory bezogenen Informationen durch die Abmeldung aus dem lokalen Profil gelöscht.

> [WACOM.NOTE] Die folgenden Befehle werden nicht richtig ausgeführt, wenn ein Konto zur Authentifizierung verwendet wird:
> 
> * azure vm
> * azure network
> * azure mobile
> 
> Wenn Sie mit diesen Befehlen arbeiten müssen, dann verwenden Sie, wie im folgenden Abschnitt beschrieben, eine Veröffentlichungseinstellungendatei zum Authentifizieren in Azure:

###Verwenden der Methode mit der Veröffentlichungseinstellungendatei

Mit dem folgenden Befehl können Sie die Veröffentlichungseinstellungen für Ihr Konto herunterladen:

	azure account download

Daraufhin wird Ihr Standardwebbrowser geöffnet, und Sie werden aufgefordert, sich beim Azure-Verwaltungsportal anzumelden. Nachdem Sie sich angemeldet haben, wird eine Datei mit der Erweiterung ".publishsettings" heruntergeladen. Notieren Sie sich den Speicherort der Datei.

> [WACOM.NOTE] Wenn Ihr Konto mit mehreren Azure Active Directory-Mandanten verknüpft ist, werden Sie möglicherweise aufgefordert, das Active Directory auszuwählen, für das eine Datei mit Veröffentlichungseinstellungen heruntergeladen werden soll.
> 
> Das Active Directory, das Sie auf der Downloadseite oder im Azure-Verwaltungsportal ausgewählt haben, wird zur Standardeinstellung, die vom Portal und der Downloadseite verwendet wird. Nachdem eine Standardeinstellung festgelegt wurde, wird am oberen Rand der Downloadseite der Text "click here to return to the selection page" (Klicken Sie hier, um zur Auswahlseite zurückzukehren) angezeigt. Klicken Sie auf den bereitgestellten Link, um zur Auswahlseite zurückzukehren.

Als Nächstes importieren Sie die .publishsettings-Datei mit dem folgenden Befehl, wobei Sie [Pfad der .publishsettings-Datei] durch den Pfad Ihrer .publishsettings-Datei ersetzen:

	azure account import [Pfad der .publishsettings-Datei]

> [WACOM.NOTE]Wenn Sie Veröffentlichungseinstellungen importieren, werden die Informationen zum Zugriff auf Ihr Azure-Abonnement in einem ".azure"-Verzeichnis in Ihrem "user"-Verzeichnis gespeichert. Ihr user-Verzeichnis wird von Ihrem Betriebssystem geschützt. Es wird jedoch empfohlen, dass Sie Ihr user-Verzeichnis zusätzlich verschlüsseln. Sie haben dazu folgende Möglichkeiten:
>
> * Windows: Ändern Sie die Verzeichniseigenschaften, oder verwenden Sie BitLocker.
> * Mac: Aktivieren Sie FileVault für das Verzeichnis.
> * Ubuntu: Verwenden Sie die Homeverzeichnis-Verschlüsselung. Andere Linux-Distributionen bieten äquivalente Funktionen.

Nach dem Import der Veröffentlichungseinstellungen sollten Sie die .publishsettings-Datei löschen, da sie von den Befehlszeilentools nicht mehr benötigt wird und ein Sicherheitsrisiko darstellt, weil sie verwendet werden kann, um Zugriff auf Ihr Abonnement zu erlangen.

###Mehrere Abonnements

Wenn Sie mehrere Azure-Abonnements besitzen, erhalten Sie durch die Anmeldung Zugriff auf alle Abonnements, die mit Ihren Anmeldeinformationen verknüpft sind. Wenn Sie eine Veröffentlichungseinstellungendatei verwenden, enthält die .publishsettings-Datei Informationen zu allen Abonnements. Bei beiden Methoden wird ein Abonnement als Standardabonnement ausgewählt, das von der plattformübergreifenden Befehlszeilenschnittstelle zum Ausführen von Vorgängen verwendet wird. Mit dem Befehl "azure account list" können Sie die Abonnements anzeigen und herausfinden, welches Abonnement das Standardabonnement ist. Die Ausgabe dieses Befehls sieht etwa so aus:

info:    Executing command account list
 data:    Name              Id                                    Current
 data:    ----------------  ------------------------------------  -------
 data:    Azure-sub-1       ####################################  true
 data:    Azure-sub-2       ####################################  false

	In der obigen Liste geht aus der Spalte **Current** hervor, dass "Azure-sub-1" das aktuelle Standardabonnement ist. Um das Standardabonnement zu ändern, verwenden Sie den Befehl "azure account set", und geben Sie das Abonnement an, welches das Standardabonnement sein soll. Beispiel:

	azure account set Azure-sub-2

Damit wird das Standardabonnement in "Azure-sub-2" geändert. 

> [WACOM.NOTE] Die Änderung des Standardabonnements tritt sofort in Kraft, und es ist eine globale Änderung. Für weitere Befehle der plattformübergreifenden Befehlszeilenschnittstelle wird das neue Standardabonnement verwendet, unabhängig davon, ob die Befehle in derselben Befehlsschnittstelleninstanz oder einer anderen Instanz ausgeführt werden.

Wenn Sie in der plattformübergreifenden Befehlszeilenschnittstelle ein anderes Abonnement als das Standardabonnement verwenden, aber das Standardabonnement nicht ändern möchten, können Sie die Option "--subscription" und den Namen des Abonnements angeben, das Sie für den Vorgang verwenden möchten.

<h2><a id="use"></a>Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle</h2>

Sie greifen mit dem Befehl "azure" auf die plattformübergreifende Befehlszeilenschnittstelle zu. Um eine Liste der verfügbaren Befehle anzuzeigen, rufen Sie den Befehl "azure" ohne Parameter auf. Daraufhin sollten Hilfeinformationen angezeigt werden, die etwa wie folgt aussehen:

info:             _    _____   _ ___ ___
info:            /_\  |_  / | | | _ \ __|
info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
info:       (_______ _ _)         _ ______ _)_ _
info:              (______________ _ )   (___ _ _)
	info:
info:    Windows Azure: Microsoft's Cloud Platform
	info:
info:    Tool version 0.8.0
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

Die oben aufgeführten Befehle der obersten Ebene enthalten Befehle für die Arbeit mit einem bestimmten Azure-Bereich. Beispielsweise enthält der Befehl "azure account" Befehle, die sich auf Ihr Azure-Abonnement beziehen, wie die oben zum Importieren von Einstellungen verwendeten Befehle "download" und "import".

Die meisten Befehle sind als "azure <Befehl> <Vorgang>[Parameter]" formatiert und führen einen Vorgang mit einem Dienst oder Objekt aus, z. B. der Kontokonfiguration. Andere Befehle liefern Unterbefehle, und weisen das Format "azure <Befehl> <Unterbefehl> <Vorgang> [Parameter]" auf. Mit den folgenden Beispielbefehlen wird Ihre Kontokonfiguration bearbeitet:

* Mit dem folgenden Befehl können Sie importierte Abonnements anzeigen:

		azure account list

* Wenn Sie Abonnements importiert haben, können Sie mit dem folgenden Befehl ein Abonnement als Standardabonnement festlegen:

		azure account set <Abonnement>

Der Parameter "--help" oder "-h" kann angegeben werden, um Hilfetexte zu bestimmten Befehlen anzuzeigen. Stattdessen kann auch das Format "azure help [Befehl] [Optionen]" verwendet werden, um dieselben Informationen abzurufen. Beispielsweise geben die folgenden Befehle die gleichen Informationen zurück:

	azure account set --help

	azure account set -h

	azure help account set

Wenn Sie sich nicht ganz sicher sind, welche Parameter für einen Befehl angegeben werden müssen, rufen Sie mit "--help", "-h" oder "azure help [Befehl]" die entsprechende Hilfe ab.

###Festlegen des Konfigurationsmodus

	Mit der plattformübergreifenden Befehlszeilenschnittstelle können Sie Verwaltungsvorgänge für einzelne "Ressourcen" durchführen. Dabei kann es sich um vom Benutzer verwaltete Entitäten handeln, wie einen Datenbankserver, eine Datenbank oder eine Website. Dies ist der Standardbetriebsmodus der plattformübergreifenden Befehlszeilenschnittstelle, der auch als **Azure-Dienstverwaltung** bezeichnet wird. Wenn jedoch eine komplexe Lösung vorliegt, die aus mehreren Ressourcen besteht, dann ist es hilfreich, wenn die gesamte Lösung als Einheit verwaltet werden kann.

Damit eine Gruppe von Ressourcen als eine logische Einheit oder "Ressourcengruppe" verwaltet werden kann, haben wir eine Vorschau von **Ressourcen-Manager** als neue Möglichkeit zum Verwalten von Azure-Ressourcen eingeführt. 

>[WACOM.NOTE] Der Ressourcen-Manager befindet sich derzeit in der Vorschau und bietet nicht denselben Umfang an Verwaltungsfunktionen wie die Azure-Dienstverwaltung.

Um den neuen Ressourcen-Manager zu unterstützen, lässt die plattformübergreifende Befehlszeilenschnittstelle zu, dass Sie mit dem Befehl "azure config mode" zwischen diesen Verwaltungsmodi hin- und herwechseln.

Die plattformübergreifende Befehlszeilenschnittstelle verwendet standardmäßig den Azure-Dienstverwaltungsmodus. Mit dem folgenden Befehl können Sie zum Ressourcen-Manager-Modus wechseln:

	azure config mode arm

Verwenden Sie den folgenden Befehl, um wieder zum Azure-Dienstverwaltungsmodus zu wechseln:

	azure config mode asm 

>[WACOM.NOTE]Der Ressourcen-Manager-Modus und Azure-Dienstverwaltungsmodus schließen sich gegenseitig aus. Das heißt, dass Ressourcen, die in einem Modus erstellt wurden, nicht im anderen Modus verwaltet werden können.

	Weitere Informationen zum Arbeiten mit dem Ressourcen-Manager und der plattformübergreifenden Befehlszeilenschnittstelle finden Sie unter [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager][xplatarm].

###Arbeiten mit Diensten im Azure-Dienstverwaltungsmodus

Mit der plattformübergreifenden Befehlszeilenschnittstelle können Sie Azure-Dienste einfach verwalten. In diesem Beispiel erfahren Sie, wie Sie mit der plattformübergreifenden Befehlszeilenschnittstelle eine Azure-Website verwalten.

1. Verwenden Sie den folgenden Befehl, um eine neue Azure-Website zu erstellen. Ersetzen Sie **meineWebsite** durch einen eindeutigen Namen.

		azure site create mywebsite

Sie werden aufgefordert, die Region anzugeben, in der die Website erstellt wird. Wählen Sie eine Region, die geografisch in Ihrer Nähe liegt. Nachdem die Befehlsausführung abgeschlossen wurde, ist die Website unter der Adresse "http://mywebsite.azurewebsites.net" verfügbar (ersetzen Sie **mywebsite** durch den Namen, den Sie angegeben haben.)

	> [WACOM.NOTE]Wenn Sie Git zur Projektquellcodeverwaltung verwenden, können Sie den Parameter "--git" angeben, um in Azure ein Git-Repository für diese Website zu erstellen. Damit wird auch ein Git-Repository in dem Verzeichnis initialisiert, aus dem der Befehl aufgerufen wurde, sofern noch keines vorhanden ist. Zudem wird ein Git-Remoterepository namens "azure" erstellt, das verwendet werden kann, um Bereitstellungen mit dem Befehl "git push azure master" per Push auf die Azure-Website zu übertragen.

	> [WACOM.NOTE]Wenn eine Fehlermeldung angezeigt wird, die besagt, dass 'site' kein Azure-Befehl ist, dann wird die plattformübergreifende Befehlszeilenschnittstelle wahrscheinlich im Ressourcengruppenmodus ausgeführt. Verwenden Sie den Befehl "azure config mode asm", um zum Ressourcenmodus zu wechseln.

2. Verwenden Sie den folgenden Befehl, um die Websites für Ihr Abonnement aufzulisten:

		azure site list

	Diese Liste sollte die Website enthalten, die Sie im vorigen Schritt erstellt haben.

2. Verwenden Sie zum Beenden der Website den folgenden Befehl. Ersetzen Sie **myWebsite** durch den Namen der Website.

		azure site stop mywebsite

	Nach abgeschlossener Befehlsausführung können Sie den Browser aktualisieren, um sich davon zu überzeugen, dass die Website beendet wurde.

3. Verwenden Sie zum Starten der Website den folgenden Befehl. Ersetzen Sie **myWebsite** durch den Namen der Website.

		azure site start mywebsite

	Nach abgeschlossener Befehlsausführung können Sie den Browser aktualisieren, um sich davon zu überzeugen, dass die Website gestartet wurde.

4. 	Verwenden Sie zum Löschen der Website den folgenden Befehl. Ersetzen Sie **myWebsite** durch den Namen der Website.

		azure site delete mywebsite

	Nach abgeschlossener Befehlsausführung können Sie mit dem Befehl "azure site list" überprüfen, ob die Website tatsächlich nicht mehr vorhanden ist.

<h2><a id="script"></a>Erstellen von Skripts für die plattformübergreifende Azure-Befehlszeilenschnittstelle</h2>

	Sie können die plattformübergreifende Befehlszeilenschnittstelle zur manuellen Befehlseingabe verwenden, aber Sie können damit auch komplexe Automatisierungsabläufe erstellen, indem Sie die Funktionen des Befehlszeileninterpreters und andere auf dem System verfügbare Befehlszeilenprogramme nutzen. Beispielsweise werden mit dem folgenden Befehl alle aktuell ausgeführten Azure-Websites beendet:

	azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

	In diesem Beispiel wird dem Befehl "grep", der jede Zeile nach der Zeichenfolge "Running" durchsucht, eine Liste von Websites übergeben. Alle übereinstimmenden Ziele werden dann dem Befehl "awk" übergeben, der "azure site stop" aufruft und aus der zweiten an ihn übergebenen Spalte (der Name der ausgeführten Website) den Namen der zu beendenden Website entnimmt.

	Hier wird zwar gezeigt, wie Sie Befehle miteinander verketten können, aber Sie können mit den Skriptfunktionen, die der Befehlszeileninterpreter bereitstellt, auch ausgeklügelte Skripts erstellen. Skriptfunktionen und -syntax sind von Befehlszeileninterpreter zu Befehlszeileninterpreter unterschiedlich. Bash ist wahrscheinlich der gängigste Befehlszeileninterpreter für auf UNIX basierende Systeme, einschließlich Linux und OS X.

Weitere Informationen zur Skripterstellung mit Bash finden Sie im [Advanced Bash-Scripting Guide][advanced-bash].

Allgemeine Informationen zur Skripterstellung für OS X- oder Linux-basierte Systeme finden Sie unter [Shell script][script].

Weitere Informationen zur Skripterstellung für Windows-basierte Systeme mit Stapelverarbeitungsdateien finden Sie unter [Befehlszeilenreferenz A-Z][batch].

### Erläuterung der Ergebnisse

Beim Erstellen von Skripts müssen Sie häufig die Ausgabe eines Befehls erfassen und an einen anderen Befehl übergeben oder einen Vorgang mit der Ausgabe ausführen, z. B. die Ausgabe nach einem bestimmten Wert durchsuchen. Die plattformübergreifende Befehlszeilenschnittstelle generiert die Ausgabe in STDOUT und STDERR. Jede Zeile beginnt mit einem Präfix, wobei "info" informative Statusmeldungen kennzeichnet oder "data" die zu einem Dienst zurückgegebenen Daten einleitet. Sie können die plattformübergreifende Befehlszeilenschnittstelle mit dem Parameter "--verbose" oder "-v`" jedoch auch anweisen, ausführlichere Informationen zurückzugeben. 	Daraufhin werden zusätzliche Informationen ausgegeben, denen die Zeichenfolge "verbose:" voransteht.

Die folgende Ausgabe wird beispielsweise vom Befehl "azure site list" zurückgegeben:

info:    Executing command site list
	+ Enumerating sites
 data:    Name           Status   Mode  Host names
 data:    -------------  -------  ----  -------------------------------
 data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
info:    site list command OK

Wenn der Parameter "--verbose" oder "-v" angegeben wird, werden Informationen zurückgegeben, die etwa wie folgt aussehen:

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

Beachten Sie, dass die `verbose:`-Informationen im JSON-Datenformat dargestellt werden. Sie können den Parameter "--json" angeben, um die Informationen im JSON-Format zurückzugeben, wenn Sie mit Dienstprogrammen arbeiten, die das JSON-Format verarbeiten können, wie [jsawk](https://github.com/micha/jsawk) oder [jq](http://stedolan.github.io/jq/). Beispiel:

	azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q 

Mit dem obigen Befehl wird eine Liste von Websites im JSON-Format abgerufen, dann werden mit jsawk die Namen der Websites abgerufen, und schließlich wird mit xargs ein Befehl zum Löschen von Websites für jede Website ausgeführt, wobei der Websitename als Parameter übergeben wird.

>[WACOM.NOTE] Der Parameter "--json" blockiert die Generierung von Status- oder Dateninformationen (Zeichenfolgen mit dem Präfix "info:" und "data:"). Wenn der Parameter "--json" parameter beispielsweise für den Befehl "azure site create" angegeben wird, dann wird keine Ausgabe zurückgegeben, da dieser Befehl keine anderen Daten als "info"-Meldungen ausgibt.

###Arbeiten mit Fehlern

	Die plattformübergreifende Befehlszeilenschnittstelle protokolliert Fehlerinformationen in STDERR. Sie können zudem weitere Informationen zu Fehlern in eine Datei namens **azure.err** ausgeben lassen. Diese Datei befindet sich in dem Verzeichnis, aus dem das Skript aufgerufen wurde. Wenn Informationen in dieser Datei protokolliert werden, dann enthält STDOUT folgenden Eintrag:

info:    Error information has been recorded to azure.err

###Beendigungsstatus

Einige Befehle der plattformübergreifenden Befehlszeilenschnittstelle geben keinen Beendigungsstatus, der ungleich Null ist, zurück, wenn erforderliche Parameter fehlen. Stattdessen wird der Benutzer zur Eingabe aufgefordert. Wenn Sie beispielsweise den Befehl "azure site create" zum Erstellen einer neuen Website verwenden und weder den Sitenamen noch den Parameter "--location" angeben, dann werden Sie zur Eingabe dieser Werte aufgefordert.

Wenn Sie ein Skript schreiben, das sich auf den Beendigungsstatus stützt, dann müssen Sie sicherstellen, dass die von Ihnen verwendeten Befehle der plattformübergreifenden Befehlszeilenschnittstelle keine Benutzereingaben anfordern.

<h2><a id="additional-resources"></a>Zusätzliche Ressourcen</h2>

* Wenn Sie weitere Informationen zur plattformübergreifenden Befehlszeilenschnittstelle erhalten, Quellcode herunterladen, Probleme melden oder etwas zum Projekt beitragen möchten, dann besuchen Sie die Webseite [GitHub repository for the Azure Cross-Platform Command-Line Interface](https://github.com/WindowsAzure/azure-sdk-tools-xplat).

* Wenn Sie Probleme mit der Verwendung der plattformübergreifenden Befehlszeilenschnittstelle oder Azure haben, besuchen Sie die [Azure-Foren](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home).

* Weitere Informationen zu Azure finden Sie unter [http://azure.microsoft.com/](http://azure.microsoft.com).




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[authandsub]: http://msdn.microsoft.com/en-us/library/windowsazure/hh531793.aspx#BKMK_AccountVCert

[Azure-Website]: ../media/freetrial.png
[Vorschaufunktion auswählen]: ../media/antares-iaas-preview-02.png
[Abonnement auswählen]: ../media/antares-iaas-preview-03.png
[free-trial]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A7171371E
[advanced-bash]: http://tldp.org/LDP/abs/html/
[script]: http://en.wikipedia.org/wiki/Shell_script
[batch]: http://technet.microsoft.com/en-us/library/bb490890.aspx
[xplatarm]: /en-us/documentation/articles/xplat-cli-azure-resource-manager/
[Portal]: https://manage.windowsazure.com
[signuporg]: http://www.windowsazure.com/en-us/documentation/articles/sign-up-organization/
