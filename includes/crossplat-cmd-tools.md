#Verwenden der Azure-Befehlszeilentools für Mac und Linux

Dieser Leitfaden beschreibt, wie Sie mithilfe der Azure-Befehlszeilentools für Mac und Linux Dienste in Azure erstellen und verwalten. Die aufgeführten Szenarien umfassen die **Installation der Tools**, **den Import Ihrer Veröffentlichungseinstellungen**, **das Erstellen und Verwalten von Azure-Websites** und das **Erstellen und Verwalten von virtuellen Azure-Computern**. Umfassendes Referenzmaterial finden Sie in der [Dokumentation zum Azure-Befehlszeilentool für Mac und Linux][reference-docs].

##Inhaltsverzeichnis
* [Beschreibung der Azure-Befehlszeilentools für Mac und Linux](#Overview)
* [Installation der Azure-Befehlszeilentools für Mac und Linux](#Download)
* [Erstellen eines Azure-Kontos](#CreateAccount)
* [Herunterladen und Importieren der Veröffentlichungseinstellungen](#Account)
* [Erstellen und Verwalten einer Azure-Website](#WebSites)
* [Erstellen und Verwalten eines virtuellen Azure-Computers](#VMs)


<h2><a id="Overview"></a>Beschreibung der Azure-Befehlszeilentools für Mac und Linux</h2>

Die Azure-Befehlszeilentools für Mac und Linux umfassen Befehlszeilentools zum Bereitstellen und Verwalten von Azure-Diensten.
 
Unter anderem werden folgende Aufgaben unterstützt:

* Importieren von Veröffentlichungseinstellungen
* Erstellen und Verwalten von Azure-Websites
* Erstellen und Verwalten von virtuellen Azure-Computern

Sie können eine vollständige Liste der unterstützten Befehle anzeigen, indem Sie nach der Installation der Tools `azure -help` an der Befehlszeile eingeben, oder die [Referenzdokumentation][reference-docs] konsultieren.

<h2><a id="Download">Installation der Azure-Befehlszeilentools für Mac und Linux</a></h2>

Die folgende Liste enthält Informationen zur Installation der Befehlszeilentools, abhängig von Ihrem Betriebssystem:

* **Mac**: Laden Sie den [Azure SDK Installer][mac-installer] herunter. Öffnen Sie die heruntergeladene PKG-Datei, und führen Sie die Installationsschritte durch, zu denen Sie aufgefordert werden.

* **Linux**: Installieren Sie die neueste Version von [Node.js][nodejs-org] (siehe [Install Node.js via Package Manager][install-node-linux] (Installieren von Node.js mithilfe des Paket-Managers, in englischer Sprache)), und führen Sie dann den folgenden Befehl aus:

		npm install azure-cli -g

	**Hinweis**: Möglicherweise müssen Sie diesen Befehl mit erhöhten Rechten ausführen:

		sudo npm install azure-cli -g

* **Windows**: Führen Sie den Windows-Installer (MSI-Datei) aus. Sie finden diesen unter [Azure-Befehlszeilentools][windows-installer].


Geben Sie `azure` an der Eingabeaufforderung ein, um die Installation zu überprüfen. Wenn die Installation erfolgreich war, wird eine Liste aller verfügbaren `azure`-Befehle angezeigt.

<h2><a id="CreateAccount"></a>Erstellen eines Azure-Kontos</h2>

Sie benötigen ein Azure-Konto, um die Azure-Befehlszeilentools für Mac und Linux zu verwenden.

Öffnen Sie einen Webbrowser, und rufen Sie die Seite [http://www.windowsazure.com][windowsazuredotcom] auf. Klicken Sie in der oberen rechten Ecke auf **Kostenlose Testversion**.

![Azure-Website][Azure Web Site]

Befolgen Sie die Anweisungen zum Erstellen eines Kontos.

<h2><a id="Account"></a>Herunterladen und Importieren der Veröffentlichungseinstellungen</h2>

Laden Sie zunächst Ihre Veröffentlichungseinstellungen herunter, und importieren Sie sie. Daraufhin können Sie die Tools zum Erstellen und Verwalten von Azure-Diensten verwenden. Mit dem Befehl `account download` können Sie die Veröffentlichungseinstellungen herunterladen:

	azure account download

Ihr Standardbrowser wird geöffnet, und Sie werden aufgefordert, sich beim Verwaltungsportal anzumelden. Nachdem Sie sich angemeldet haben, wird Ihre `.publishsettings`-Datei heruntergeladen. Notieren Sie sich den Speicherort der Datei.

Importieren Sie als Nächstes die `.publishsettings`-Datei, indem Sie den folgenden Befehl ausführen und dabei `{path to .publishsettings file}` durch den Pfad zu Ihrer `.publishsettings`-Datei ersetzen:

	azure account import {path to .publishsettings file}

Sie können alle vom Befehl <code>import</code> gespeicherten Informationen entfernen, indem Sie den Befehl <code>account clear</code> verwenden:

	azure account clear

Verwenden Sie die Option `-help`, um eine Liste der Optionen für `account`-Befehle anzuzeigen.

	azure account -help

Nachdem Sie Ihre Veröffentlichungseinstellungen importiert haben, löschen Sie die `.publishsettings`-Datei aus Sicherheitsgründen.

> [AZURE.NOTE]Wenn Sie Veröffentlichungseinstellungen importieren, werden die Anmeldeinformationen für den Zugriff auf Ihr Azure-Abonnement in Ihrem `user`-Ordner gespeichert. Ihr `user`-Ordner ist durch Ihr Betriebssystem geschützt. Es wird jedoch empfohlen, dass Sie zusätzliche Schritte vornehmen, um Ihren `user`-Ordner zu verschlüsseln. Sie haben dazu folgende Möglichkeiten:
> 
> - Windows: Ändern Sie die Ordnereigenschaften, oder verwenden Sie BitLocker.
> - Mac: Aktivieren Sie FileVault für den Ordner.
> - Ubuntu: Verwenden Sie die Homeverzeichnis-Verschlüsselung. Andere Linux-Distributionen bieten äquivalente Funktionen.

Nun können Sie mit dem Erstellen und Verwalten von Azure-Websites und virtuellen Azure-Computern beginnen.

<h2><a id="WebSites"></a>Erstellen und Verwalten einer Azure-Website</h2>

###Erstellen einer Website

Erstellen Sie zunächst ein leeres Verzeichnis namens `MySite`, und navigieren Sie zu diesem Verzeichnis, um eine Azure-Website zu erstellen.

Führen Sie dann den folgenden Befehl aus:

	azure site create MySite --git

Die Ausgabe dieses Befehls enthält die Standard-URL für die neu erstellte Website. Mit der `--git`-Option können Sie Git zur Veröffentlichung Ihrer Website verwenden, indem Sie Git-Repositorys sowohl in Ihrem lokalen Anwendungsverzeichnis als auch im Datencenter Ihrer Website erstellen. Falls Ihr lokaler Ordner bereits ein Git-Repository ist, fügt der Befehl ein neues Remoterepository zu dem bereits vorhandenen Repository hinzu, das auf das Repository im Datencenter Ihrer Website verweist.

Beachten Sie, dass Sie den Befehl `azure site create` mit einer der folgenden Optionen ausführen können:

* `--location [location name]`. Mit dieser Option geben Sie den Standort des Datencenters an, in dem Ihre Website erstellt wird (z. B. "USA, Westen"). Wenn Sie diese Option auslassen, werden Sie aufgefordert, einen Standort auszuwählen.
* `--hostname [custom host name]`. Mithilfe dieser Option können Sie einen benutzerdefinierten Hostnamen für Ihre Website auswählen.

Sie können dann Inhalte zu Ihrem Websiteverzeichnis hinzufügen. Verwenden Sie den regulären Git-Ablauf (`git add`, `git commit`), um Ihre Inhalte zu übermitteln. Verwenden Sie den folgenden Git-Befehl, um Ihren Website-Inhalt an Azure zu übertragen:

	git push azure master

###Einrichten der Veröffentlichung über GitHub

Verwenden Sie die Option `--GitHub` beim Erstellen einer Website, um die kontinuierliche Veröffentlichung von einem GitHub-Repository einzurichten:

	auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Wenn Sie einen lokalen Klon eines GitHub-Repositorys haben, oder wenn Sie über ein Repository mit einem einzigen Remoteverweis auf ein Git-Repository verfügen, veröffentlicht dieser Befehl automatisch Code im GitHub-Repository auf Ihrer Website. Von da an werden alle Änderungen, die an das GitHub-Repository übertragen werden, automatisch auf Ihrer Website veröffentlicht.

Wenn Sie Veröffentlichung von GitHub einrichten, ist die verwendete Standardverzweigung die Hauptverzweigung. Führen Sie den folgenden Befehl aus Ihrem lokalen Repository aus, um einen anderen Zweig festzulegen:

	azure site repository <branch name>

###Konfigurieren von App-Einstellungen

App-Einstellungen sind Schlüssel/Wert-Paare, die Ihrer Anwendung während der Laufzeit zur Verfügung stehen. Sobald diese Einstellungen für eine Azure-Website eingerichtet sind, überschreiben die App-Einstellungswerte Einstellungen mit demselben Schlüssel, die in der Web.config-Datei Ihrer Website definiert sind. Für Node.js- und PHP-Anwendungen sind App-Einstellungen als Umgebungsvariablen verfügbar. Das folgende Beispiel zeigt Ihnen, wie Sie ein Schlüssel/Wert-Paar festlegen:

	azure site config add <key>=<value> 

Verwenden Sie den folgenden Befehl, um eine Liste alle Schlüssel/Wert-Paare anzuzeigen:

	azure site config list 

Oder wenn Sie den Schlüssel kennen und den Wert anzeigen möchten, können Sie den folgenden Befehl verwenden:

	azure site config get <key> 

Wenn Sie den Wert eines vorhandenen Schlüssels ändern möchten, müssen Sie den vorhandenen Schlüssel zunächst löschen und ihn dann erneut hinzufügen. Der Befehl zum Löschen lautet:

	azure site config clear <key> 

###Auflisten und Anzeigen von Websites:

Verwenden Sie den folgenden Befehl, um Ihre Websites aufzulisten:

	azure site list

Verwenden Sie den Befehl `site show`, um detaillierte Informationen über eine Website abzurufen. Das folgende Beispiel zeigt die Detailinformationen für `MySite`:

	azure site show MySite

###Anhalten, Starten oder Neustarten einer Website

Sie können eine Website mit den Befehlen `site stop`, `site start` oder `site restart` anhalten, starten oder neu starten:

	azure site stop MySite
	azure site start MySite
	azure site restart MySite

###Löschen einer Website

Sie können eine Website mit dem Befehl `site delete` löschen:

	azure site delete MySite

Wenn Sie einen der oben aufgeführten Befehle innerhalb des Ordners ausführen, in dem Sie den Befehl `site create` ausführen können, müssen Sie den Websitenamen `MySite` nicht als letzten Parameter festlegen.

Verwenden Sie die Option `site`, um eine vollständige Liste der `-help`-Befehle anzuzeigen.

	azure site -help 

<h2><a id="VMs"></a>Erstellen und Verwalten eines virtuellen Azure-Computers</h2>

Ein virtueller Azure-Computer wird aus einem Image eines virtuellen Computers (VHD-Datei) erstellt, das Sie bereitstellen oder das im Imagekatalog verfügbar ist. Um die verfügbaren Images anzuzeigen, verwenden Sie den Befehl `vm image list`:

	azure vm image list

Sie können einen virtuellen Computer mit dem Befehl `vm create` über eines der verfügbaren Images bereitstellen oder starten. Das folgende Beispiel zeigt, wie Sie einen virtuellen Linux-Computer (genannt `myVM`) aus einem Image im Imagekatalog (CentOS 6.2) starten. Der Root-Benutzername und das -Kennwort für den virtuellen Computer lauten `myusername` bzw. `Mypassw0rd`. (Beachten Sie, dass der Parameter `--location` das Datencenter angibt, in dem der virtuelle Computer erstellt wird. Wenn Sie den Parameter `--location` auslassen, werden Sie aufgefordert, einen Standort auszuwählen.)

	azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-de-DE-30GB.vhd myusername --location "West US"

Möglicherweise möchten Sie das `--ssh`-Flag (Linux) oder das `--rdp`-Flag (Windows) an `vm create` übergeben, um Remoteverbindungen mit dem neu erstellten virtuellen Computer zu ermöglichen.

Wenn Sie stattdessen einen virtuellen Computer aus einem benutzerdefinierten Image erstellen möchten, können Sie mit dem Befehl `vm image create` ein Image einer VHD-Datei erstellen und dann mit dem Befehl `vm create` den virtuellen Computer bereitstellen. Das folgende Beispiel zeigt, wie Sie ein Linux-Image (genannt `myImage`) aus einer lokalen VHD-Datei erstellen. (Der `--location`-Parameter gibt die Daten an, in denen das Image gespeichert ist.)

	azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

Statt ein Image aus einer lokalen VHD-Datei zu erstellen, können Sie ein Image aus einer VHD-Datei erstellen, die im Azure Blob Storage gespeichert ist. Hierzu können Sie den `blob-url`-Parameter verwenden:

	azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

Nachdem Sie ein Image erstellt haben, können Sie mithilfe von `vm create` einen virtuellen Computer aus dem Image erstellen. Der unten genannte Befehl erstellt einen virtuellen Computer namens `myVM` aus dem oben erstellten Image (`myImage`).

	azure vm create myVM myImage myusername --location "West US"

Nachdem Sie einen virtuellen Computer bereitgestellt haben, möchten Sie möglicherweise Endpunkte erstellen, um beispielsweise Remotezugriff auf den virtuellen Computer zu ermöglichen. Das folgende Beispiel verwendet den Befehl `vm create endpoint`, um den externen Port 22 und den lokalen Port 22 auf `myVM` zu öffnen:

	azure vm endpoint create myVM 22 22

Detaillierte Informationen zu einem virtuellen Computer (einschließlich IP-Adresse, DNS-Name und Endpunktinformationen) erhalten Sie mit dem Befehl `vm show`.

	azure vm show myVM

Verwenden Sie einen der folgenden Befehle, um den virtuelle Computer herunterzufahren, zu starten oder neu zu starten:

	azure vm shutdown myVM
	azure vm start myVM
	azure vm restart myVM

Verwenden Sie den Befehl `vm delete`, um den virtuellen Computer zu löschen:

	azure vm delete myVM

Eine vollständige Liste der Befehle zum Erstellen und Verwalten von virtuellen Computern erhalten Sie über die Option `-h`:

	azure vm -h

<!-- IMAGES -->
[Azure Web Site]: ./media/crossplat-cmd-tools/freetrial.png

<!-- LINKS -->
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/?LinkID=275464
[reference-docs]: http://go.microsoft.com/fwlink/?LinkId=252246
[windowsazuredotcom]: http://www.windowsazure.com

<!---HONumber=Oct15_HO3-->