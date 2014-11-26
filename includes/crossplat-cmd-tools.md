# Verwenden des Azure-Befehlszeilentools für Mac und Linux

Dieser Leitfaden beschreibt, wie Sie mithilfe der Azure-Befehlszeilentools für Mac und Linux Dienste in Azure erstellen und verwalten. Die aufgeführten Szenarien umfassen die **Installation der Tools**, **den Import Ihrer Veröffentlichungseinstellungen**, **das Erstellen und Verwalten von Azure-Websites** und das **Erstellen und Verwalten von virtuellen Azure-Computern**. Umfassendes Referenzmaterial finden Sie in der [Dokumentation zum Azure-Befehlszeilentool für Mac und Linux][Dokumentation zum Azure-Befehlszeilentool für Mac und Linux].

## Inhaltsverzeichnis

-   [Beschreibung der Azure-Befehlszeilentools für Mac und Linux][Beschreibung der Azure-Befehlszeilentools für Mac und Linux]
-   [Installation der Azure-Befehlszeilentools für Mac und Linux][Installation der Azure-Befehlszeilentools für Mac und Linux]
-   [Erstellen eines Azure-Kontos][Erstellen eines Azure-Kontos]
-   [Herunterladen und Importieren der Veröffentlichungseinstellungen][Herunterladen und Importieren der Veröffentlichungseinstellungen]
-   [Erstellen und Verwalten einer Azure-Website][Erstellen und Verwalten einer Azure-Website]
-   [Erstellen und Verwalten eines virtuellen Azure-Computers][Erstellen und Verwalten eines virtuellen Azure-Computers]

## <span id="Overview"></span></a>Beschreibung der Azure-Befehlszeilentools für Mac und Linux

Die Azure-Befehlszeilentools für Mac und Linux umfassen Befehlszeilentools zum Bereitstellen und Verwalten von Azure-Diensten.

Unter anderem werden folgende Aufgaben unterstützt:

-   Importieren von Veröffentlichungseinstellungen
-   Erstellen und Verwalten von Azure-Websites
-   Erstellen und Verwalten von virtuellen Azure-Computern

Sie können eine vollständige Liste der unterstützten Befehle anzeigen, indem Sie nach der Installation der Tools `azure -help` in die Befehlszeile eingeben, oder die [Referenzdokumentation][Dokumentation zum Azure-Befehlszeilentool für Mac und Linux] konsultieren.

## <span id="Download"></span>Installation der Azure-Befehlszeilentools für Mac und Linux</a>

Die folgende Liste enthält Informationen zur Installation der Befehlszeilentools, abhängig von Ihrem Betriebssystem:

-   **Mac**: Laden Sie den [Azure SDK Installer][Azure SDK Installer] herunter. Öffnen Sie die heruntergeladene PKG-Datei, und führen Sie die Installationsschritte durch, zu denen Sie aufgefordert werden.

-   **Linux**: Installieren Sie die neueste Version von [Node.js][Node.js] (siehe [Install Node.js via Package Manager][Install Node.js via Package Manager] (Installieren von Node.js mithilfe des Paket-Managers, in englischer Sprache), und führen Sie dann den folgenden Befehl aus:

        npm install azure-cli -g

    **Hinweis**: Möglicherweise müssen Sie diesen Befehl mit erhöhten Rechten ausführen:

        sudo npm install azure-cli -g

-   **Windows**: Führen Sie den Windows-Installer (MSI-Datei) aus. Sie finden diesen unter [Azure-Befehlszeilentools][Azure-Befehlszeilentools].

Geben Sie `azure` an der Eingabeaufforderung ein, um die Installation zu überprüfen. Wenn die Installation erfolgreich war, wird eine Liste aller verfügbaren `azure`-Befehle angezeigt.

## <span id="CreateAccount"></span></a>Erstellen eines Azure-Kontos

Sie benötigen ein Azure-Konto, um die Azure-Befehlszeilentools für Mac und Linux zu verwenden.

Öffnen Sie einen Webbrowser, und rufen Sie die Seite [][]<http://www.windowsazure.com></a> auf. Klicken Sie in der oberen rechten Ecke auf **Kostenlose Testversion**.

![Azure-Website][Azure-Website]

Befolgen Sie die Anweisungen zum Erstellen eines Kontos.

## <span id="Account"></span></a>Herunterladen und Importieren der Veröffentlichungseinstellungen

Laden Sie zunächst Ihre Veröffentlichungseinstellungen herunter, und importieren Sie sie. Daraufhin können Sie die Tools zum Erstellen und Verwalten von Azure-Diensten verwenden. Mit dem Befehl `account download` können Sie die Veröffentlichungseinstellungen herunterladen:

    azure account download

Ihr Standardbrowser wird geöffnet, und Sie werden aufgefordert, sich beim Verwaltungsportal anzumelden. Nachdem Sie sich angemeldet haben, wird Ihre `.publishsettings`-Datei heruntergeladen. Notieren Sie sich den Speicherort der Datei.

Importieren Sie als Nächstes die `.publishsettings`-Datei, indem Sie den folgenden Befehl ausführen und dabei `{path to .publishsettings file}` durch den Pfad zu Ihrer `.publishsettings`-Datei ersetzen:

    azure account import {path to .publishsettings file}

Sie können alle vom Befehl `import` gespeicherten Informationen entfernen, indem Sie den Befehl `account clear` verwenden:

    azure account clear

Verwenden Sie die Option `-help`, um eine Liste der Optionen für `account`-Befehle anzuzeigen.

    azure account -help

Nachdem Sie Ihre Veröffentlichungseinstellungen importiert haben, löschen Sie die `.publishsettings`-Datei aus Sicherheitsgründen.

<div class="dev-callout"> 
<b>Hinweis</b> 
<p>Wenn Sie Ver&ouml;ffentlichungseinstellungen importieren, werden die Anmeldeinformationen f&uuml;r den Zugriff auf Ihr Azure-Abonnement in Ihrem Ordner <code data-inline="1">user</code> gespeichert. Ihr <code data-inline="1">user</code>-Ordner ist durch Ihr Betriebssystem gesch&uuml;tzt. Es wird jedoch empfohlen, dass Sie zus&auml;tzliche Schritte vornehmen, um Ihren <code data-inline="1">user</code>-Ordner zu verschl&uuml;sseln. Sie haben dazu folgende M&ouml;glichkeiten:</p>

<ul>
<li>Windows: &Auml;ndern Sie die Ordnereigenschaften, oder verwenden Sie BitLocker.</li>
<li>Mac: Aktivieren Sie FileVault f&uuml;r den Ordner.</li>
<li>Ubuntu: Verwenden Sie die Homeverzeichnis-Verschl&uuml;sselung. Andere Linux-Distributionen bieten &auml;quivalente Funktionen.</li>
</ul>

</div>

Nun können Sie mit dem Erstellen und Verwalten von Azure-Websites und virtuellen Azure-Computern beginnen.

## <span id="WebSites"></span></a>Erstellen und Verwalten einer Azure-Website

### Erstellen einer Website

Erstellen Sie zunächst ein leeres Verzeichnis namens `MySite`, und navigieren Sie zu diesem Verzeichnis, um eine Azure-Website zu erstellen.

Führen Sie dann den folgenden Befehl aus:

    azure site create MySite --git

Die Ausgabe dieses Befehls enthält die Standard-URL für die neu erstellte Website. Mit der `--git`-Option können Sie Git zur Veröffentlichung Ihrer Website verwenden, indem Sie Git-Repositorys sowohl in Ihrem lokalen Anwendungsverzeichnis als auch im Rechenzentrum Ihrer Website erstellen. Falls Ihr lokaler Ordner bereits ein Git-Repository ist, fügt der Befehl ein neues Remoterepository zu dem bereits vorhandenen Repository hinzu, das auf das Repository im Rechenzentrum Ihrer Website verweist.

Beachten Sie, dass Sie den Befehl `azure site create` mit einer der folgenden Optionen ausführen können:

-   `--location [location name]`. Mit dieser Option geben Sie den Standort des Rechenzentrums an, in dem Ihre Website erstellt wird (z. B. "West US"). Wenn Sie diese Option auslassen, werden Sie aufgefordert, einen Standort auszuwählen.
-   `--hostname [custom host name]`. Mithilfe dieser Option können Sie einen benutzerdefinierten Hostnamen für Ihre Website auswählen.

Sie können dann Inhalte zu Ihrem Websiteverzeichnis hinzufügen. Verwenden Sie den regulären Git-Ablauf (`git add`, `git commit`), um Ihre Inhalte zu übermitteln. Verwenden Sie den folgenden Git-Befehl, um Ihren Website-Inhalt an Azure zu übertragen:

    git push azure master

### Einrichten der Veröffentlichung über GitHub

Verwenden Sie die Option `--GitHub` beim Erstellen einer Site, um die kontinuierliche Veröffentlichung von einem GitHub-Repository einzurichten:

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Wenn Sie einen lokalen Klon eines GitHub-Repositorys haben, oder wenn Sie ein Repository mit einem einzigen Remotebezug zu einem Git-Repository haben, veröffentlicht dieser Befehl automatisch Code im GitHub-Repository auf Ihrer Site. Von da an werden alle Änderungen, die an das GitHub-Repository übertragen werden, automatisch auf Ihrer Site veröffentlicht.

Wenn Sie Veröffentlichung von GitHub einrichten, ist die verwendete Standardverzweigung die Hauptverzweigung. Führen Sie den folgenden Befehl aus Ihrem lokalen Repository aus, um einen anderen Zweig festzulegen:

    azure site repository <branch name>

### Konfigurieren von App-Einstellungen

App-Einstellungen sind Schlüssel/Wert-Paare, die Ihrer Anwendung während der Laufzeit zur Verfügung stehen. Sobald diese Einstellungen für eine Azure-Website eingerichtet sind, überschreiben die App-Einstellungswerte Einstellungen mit demselben Schlüssel, die in der Web.config-Datei Ihrer Site definiert sind. Für Node.js- und PHP-Anwendungen sind App-Einstellungen als Umgebungsvariablen verfügbar. Das folgende Beispiel zeigt Ihnen, wie Sie ein Schlüssel/Wert-Paar festlegen:

    azure site config add <key>=<value> 

Verwenden Sie den folgenden Befehl, um eine Liste alle Schlüssel/Wert-Paare anzuzeigen:

    azure site config list 

Oder wenn Sie den Schlüssel kennen und den Wert anzeigen möchten, können Sie den folgenden Befehl verwenden:

    azure site config get <key> 

Wenn Sie den Wert eines vorhandenen Schlüssels ändern möchten, müssen Sie den vorhandenen Schlüssel zunächst löschen und ihn dann erneut hinzufügen. Der Befehl zum Löschen lautet:

    azure site config clear <key> 

### Auflisten und Anzeigen von Sites:

Verwenden Sie den folgenden Befehl, um Ihre Websites aufzulisten:

    azure site list

Verwenden Sie den Befehl `site show`, um detaillierte Informationen über eine Site abzurufen. Das folgende Beispiel zeigt die Details für `MySite`:

    azure site show MySite

### Anhalten, Starten oder Neustarten einer Site

Sie können eine Site mit den Befehlen `site stop`, `site start` oder `site restart` anhalten, starten oder neustarten:

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

### Löschen einer Site

Sie können eine Site mit dem Befehl `site delete` löschen:

    azure site delete MySite

Wenn Sie einen der oben aufgeführten Befehle innerhalb des Ordners ausführen, in dem Sie den Befehl `site create` ausführen können, müssen Sie den Sitenamen `MySite` nicht als letzten Parameter festlegen.

Verwenden Sie die Option `-help`, um eine vollständige Liste der `site`-Befehle anzuzeigen.

    azure site -help 

## <span id="VMs"></span></a>Erstellen und Verwalten eines virtuellen Azure-Computers

Ein virtueller Azure-Computer wird aus einem Abbild eines virtuellen Computers (VHD-Datei) erstellt, das Sie bereitstellen oder das in der Bildergalerie verfügbar ist. Um die verfügbaren Abbilder anzuzeigen, verwenden Sie den Befehl `vm image list`:

    azure vm image list

Sie können einen virtuellen Computer mit dem Befehl `vm create` aus einem der verfügbaren Abbilder bereitstellen oder starten. Das folgende Beispiel zeigt, wie Sie einen virtuellen Linux-Computer (genannt `myVM`) aus einem Abbild in der Bildergalerie (CentOS 6.2) starten. Der Root-Benutzername und das -Kennwort für den virtuellen Computer lauten `myusername` bzw. `Mypassw0rd`. (Beachten Sie, dass der Parameter `--location` das Rechenzentrum angibt, in dem der virtuelle Computer erstellt wird. Wenn Sie den Parameter `--location` auslassen, werden Sie aufgefordert, einen Standort auszuwählen.)

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-de-de-30GB.vhd myusername --location "West US"

Möglicherweise möchten Sie die `--ssh`-Kennzeichnung (Linux) oder die `--rdp`-Kennzeichnung (Windows) an `vm create` übergeben, um Remoteverbindungen zum neu erstellten virtuellen Computer zu ermöglichen.

Wenn Sie stattdessen einen virtuellen Computer aus einem benutzerdefinierten Abbild erstellen möchten, können Sie mit dem Befehl `vm image create` ein Abbild einer VHD-Datei erstellen und dann mit dem Befehl `vm create` den virtuellen Computer bereitstellen. Das folgende Beispiel zeigt, wie Sie ein Linux-Abbild (genannt `myImage`) von einer lokalen VHD-Datei erstellen. (Der `--location`-Parameter gibt die Daten an, in denen das Abbild gespeichert ist.)

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

Anstatt ein Abbild von einer lokalen VHD-Datei zu erstellen, können Sie ein Abbild von einer VHD-Datei erstellen, die im Azure Blob Storage gespeichert ist. Hierzu können Sie den `blob-url`-Parameter verwenden:

    azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

Nachdem Sie ein Abbild erstellt haben, können Sie mithilfe von `vm create` einen virtuellen Computer aus dem Abbild erstellen. Der unten genannte Befehl erstellt einen virtuellen Computer namens `myVM` aus dem oben erstellten Abbild (`myImage`).

    azure vm create myVM myImage myusername --location "West US"

Nachdem Sie einen virtuellen Computer bereitgestellt haben, möchten Sie möglicherweise Endpunkte erstellen, um beispielsweise Remotezugriff auf den virtuellen Computer zu ermöglichen. Das folgende Beispiel verwendet den Befehl `vm create endpoint`, um den externen Port 22 und den lokalen Port 22 auf `myVM` zu öffnen.

    azure vm endpoint create myVM 22 22

Detaillierte Informationen zu einem virtuellen Computer (einschließlich IP-Adresse, DNS-Name und Endpunktinformationen) erhalten Sie mit dem Befehl `vm show`.

    azure vm show myVM

Verwenden Sie einen der folgenden Befehle, um den virtuelle Computer herunterzufahren, zu starten oder neu zu starten:

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

Verwenden Sie den Befehl `vm delete`, um die VM zu löschen:

    azure vm delete myVM

Eine vollständige Liste der Befehle zum Erstellen und Verwalten von virtuellen Computern erhalten Sie über die Option `-h`:

    azure vm -h

<!-- IMAGES -->
<!-- LINKS -->

  [Dokumentation zum Azure-Befehlszeilentool für Mac und Linux]: http://go.microsoft.com/fwlink/?LinkId=252246
  [Beschreibung der Azure-Befehlszeilentools für Mac und Linux]: #Overview
  [Installation der Azure-Befehlszeilentools für Mac und Linux]: #Download
  [Erstellen eines Azure-Kontos]: #CreateAccount
  [Herunterladen und Importieren der Veröffentlichungseinstellungen]: #Account
  [Erstellen und Verwalten einer Azure-Website]: #WebSites
  [Erstellen und Verwalten eines virtuellen Azure-Computers]: #VMs
  [Azure SDK Installer]: http://go.microsoft.com/fwlink/?LinkId=252249
  [Node.js]: http://nodejs.org/
  [Install Node.js via Package Manager]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
  [Azure-Befehlszeilentools]: http://go.microsoft.com/fwlink/?LinkID=275464
  []: http://www.windowsazure.com
  [Azure-Website]: ./media/crossplat-cmd-tools/freetrial.png
