<properties 
	pageTitle="Installieren und Konfigurieren von Azure PowerShell" 
	description="Erfahren Sie, wie Sie Azure PowerShell installieren und konfigurieren." 
	editor="tysonn" 
	manager="stevenka" 
	documentationCenter="" 
	services="" 
	authors="coreyp69"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/26/2014" 
	ms.author="coreyp"/>

# Installieren und Konfigurieren von Azure PowerShell

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/de-de/manage/install-and-configure-cli/" title="Plattform&uuml;bergreifende Befehlszeilenschnittstelle">Plattform&uuml;bergreifende Befehlszeilenschnittstelle</a></div>

Mit Windows PowerShell können Sie eine Vielzahl von Aufgaben in Azure interaktiv an einer Eingabeaufforderung oder automatisch durch Skripte durchführen. Azure PowerShell ist ein Modul, das Cmdlets zum Verwalten von Azure durch Windows PowerShell enthält. Sie können diese Cmdlets verwenden, um durch die Azure-Plattform bereitgestellte Lösungen und Dienste zu erstellen, zu testen, bereitzustellen und zu verwalten. In den meisten Fällen können Sie mit den Cmdlets dieselben Aufgaben ausführen wie über das Azure-Verwaltungsportal. Beispielsweise können Sie Cloud-Dienste, virtuelle Computer, virtuelle Netzwerke und Websites erstellen und konfigurieren.

Das Modul wird als herunterladbare Datei verteilt und der Quellcode wird in einem öffentlich verfügbaren Repository verwaltet. Weiter unten in diesem Thema wird in den Installationsanweisungen ein Link zu den herunterladbaren Dateien bereitgestellt. Informationen zum Quellcode finden Sie im [Azure PowerShell-Coderepository][Azure PowerShell-Coderepository].

Dieser Leitfaden enthält grundlegende Informationen zum Installieren und Einrichten von Azure PowerShell, um die Azure-Plattform zu verwalten.

## Inhaltsverzeichnis

-   [Erforderliche Komponenten für die Verwendung von Azure PowerShell][Erforderliche Komponenten für die Verwendung von Azure PowerShell]
-   [Gewusst wie: Installieren von Azure PowerShell][Gewusst wie: Installieren von Azure PowerShell]
-   [Gewusst wie: Verbinden mit Ihrem Abonnement][Gewusst wie: Verbinden mit Ihrem Abonnement]
-   [Verwenden der Cmdlets: Beispiel][Verwenden der Cmdlets: Beispiel]
-   [Hilfe][Hilfe]
-   [Zusätzliche Ressourcen][Zusätzliche Ressourcen]

### <span id="Prereq"></span></a>Erforderliche Komponenten für die Verwendung von Azure PowerShell

Azure ist eine abonnementbasierte Plattform. Das bedeutet, dass zur Verwendung der Plattform ein Abonnement benötigt wird. In den meisten Fällen werden für die Cmdlets Abonnementinformationen benötigt, um die Aufgaben mit Ihrem Abonnement auszuführen. (Einige der speicherbezogenen Cmdlets können ohne diese Informationen verwendet werden.) Sie stellen sie zur Verfügung, indem Sie die Verbindung mit Ihrem Abonnement auf Ihrem Computer konfigurieren. Anweisungen dazu finden Sie in diesem Artikel unter "Gewusst wie: Verbinden mit Ihrem Abonnement".

> [WACOM.NOTE] Ab Version 0.8.5 erfordern die Azure PowerShell-Module Microsoft .NET Framework 4.5.

Bei der Installation des Moduls wird vom Installationsprogramm geprüft, ob das System über die erforderliche Software verfügt, und es werden alle Abhängigkeiten installiert, z. B. die richtige Version von Windows PowerShell und .NET Framework.

## <span id="Install"></span></a>Gewusst wie: Installieren von Azure PowerShell

Sie können die Azure PowerShell-Module herunterladen und installieren, indem Sie den [Microsoft-Webplattform-Installer][Microsoft-Webplattform-Installer] ausführen. Klicken Sie in der Nachfrage auf **Ausführen**. Der Webplattform-Installer installiert die Azure PowerShell-Module und alle Abhängigkeiten. Befolgen Sie die Anweisungen, um die Installation abzuschließen.

Weitere Informationen zu den für Azure verfügbaren Befehlszeilentools finden Sie unter [Befehlszeilentools][Befehlszeilentools].

Bei der Installation des Moduls wird auch eine benutzerdefinierte Konsole für Azure PowerShell installiert. Sie können die Cmdlets über die Windows PowerShell-Standardkonsole oder über die Azure PowerShell-Konsole ausführen.

Die Methode, die Sie zum Öffnen einer der beiden Konsolen verwenden, ist abhängig von der ausgeführten Windows-Version:

-   Auf einem Computer, auf dem mindestens Windows 8 oder Windows Server 2012 ausgeführt wird, können Sie die integrierte Suche verwenden. Beginnen Sie auf dem Startbildschirm mit der Eingabe von **power**. Daraufhin wird eine entsprechende Liste von Anwendungen zurückgegeben, die auch Windows PowerShell und Azure PowerShell umfasst. Klicken Sie auf eine dieser beiden Anwendungen, um die Konsole zu öffnen. (Klicken Sie mit der rechten Maustaste auf das Symbol, um die Anwendung an den Startbildschirm anzuheften.)

-   Verwenden Sie auf einem Computer, auf dem eine niedrigere Version als Windows 8 oder Windows Server 2012 ausgeführt wird, das Startmenü. Klicken Sie im Startmenü auf **Alle Programme** \> **Azure** und dann auf **Azure PowerShell**.

## <span id="Connect"></span></a>Gewusst wie: Verbinden mit Ihrem Abonnement

Zur Verwendung von Azure wird ein Abonnement benötigt. Wenn Sie über kein Abonnement verfügen, lesen Sie die Informationen unter [Erste Schritte mit Azure][Erste Schritte mit Azure].

Für die Cmdlets ist Ihr Abonnement erforderlich, damit Ihre Dienste verwaltet werden können. Es gibt zwei Möglichkeiten, Ihre Abonnementinformationen für Windows PowerShell bereitzustellen. Sie können ein Verwaltungszertifikat verwenden, das die Informationen enthält, oder sich mit Ihrem Microsoft-Konto oder einem Organisations-Konto bei Azure anmelden. Wenn Sie sich anmelden, authentifiziert Azure Active Directory (Azure AD) die Anmeldeinformationen und gibt ein Zugriffstoken zurück, mit dem Azure PowerShell Ihr Konto verwalten kann.

Beachten Sie bezüglich der Auswahl der geeigneten Authentifizierungsmethode Folgendes:

-   Die Authentifizierung mit Azure AD wird empfohlen, weil sich damit leichter der Zugriff auf ein Abonnement verwalten lässt. Mit dem Update in Version 0.8.6 wird ein Automatisierungsszenario mit Azure AD-Authentifizierung ebenso wie bei Verwendung eines Organisations-Kontos möglich. Auch mit der Azure Resource Manager-API ist dies möglich.
-   Bei Verwendung eines Zertifikats sind die Abonnementinformationen für den gesamten Gültigkeitszeitraum des Abonnements und Zertifikats verfügbar. Diese Methode erschwert jedoch die Verwaltung des Zugriffs auf ein freigegebenes Abonnement, etwa, wenn mehr als eine Person für den Zugriff auf das Konto berechtigt ist. Die Azure Resource Manager-API akzeptiert außerdem keine Zertifikatauthentifizierung.

Weitere Informationen zur Authentifizierungs- und Abonnemontverwaltung in Azure finden Sie unter [Verwalten von Konten, Abonnements und Administratorrollen][Verwalten von Konten, Abonnements und Administratorrollen].

### Verwenden von Azure AD

1.  Öffnen Sie die Azure PowerShell-Konsole, wie unter [Gewusst wie: Installieren von Azure PowerShell][Gewusst wie: Installieren von Azure PowerShell] beschrieben.

2.  Geben Sie den folgenden Befehl ein:

    `Add-AzureAccount`

3.  Geben Sie in dem Fenster die dem Konto zugeordnete E-Mail-Adresse und das zugehörige Kennwort ein.

4.  Die Anmeldeinformationen werden von Azure authentifiziert und gespeichert, dann wird das Fenster geschlossen.

5.  Ab Version 0.8.6 können Sie bei Verwendung eines Organisations-Kontos den folgenden Befehl eingeben, um das Popupfenster zu umgehen. Damit wird das Windows PowerShell-Standardfenster für Anmeldeinformationen eingeblendet, in dem Sie den Benutzernamen und das Kennwort Ihres Organisations-Kontos eingeben können.

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

6.  Wenn Sie dies in einem Automatisierungsskript verwenden und vermeiden möchten, dass ein Popupfenster eingeblendet wird, verwenden Sie folgenden Codeausschnitt:

        $userName = "<your organizational account user name>"
        $securePassword = ConvertTo-SecureString -String "<your organizational account password>" -AsPlainText -Force
        $cred = New-Object System.Management.Automation.PSCredential($userName, $securePassword)
        Add-AzureAccount -Credential $cred 

    > [WACOM.NOTE] Diese nicht interaktive Anmeldemethode funktioniert nur bei Verwendung eines Organisations-Kontos. Ein Organisations-Konto ist ein Benutzer, der von der Organisation verwaltet wird und in der Organisation als Azure Active Directory-Mandant definiert ist. Wenn Sie derzeit kein Organisations-Konto besitzen und sich mit einem Microsoft-Konto bei Ihrem Azure-Abonnement anmelden, können Sie wie folgt mühelos ein Organisations-Konto erstellen.
    >
    > 1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, und klicken Sie auf **Active Directory**.
    >
    > 2.  Wenn kein Verzeichnis vorhanden ist, wählen Sie **Create your directory** (Verzeichnis erstellen) aus, und stellen Sie die geforderten Informationen bereit.
    >
    > 3.  Wählen Sie Ihr Verzeichnis aus, und fügen Sie einen neuen Benutzer hinzu. Dieser neue Benutzer ist ein Organisations-Konto.
    >
    >     Während der Erstellung des Benutzers erhalten Sie sowohl eine E-Mail-Adresse für den Benutzer als auch ein temporäres Kennwort. Speichern Sie diese Informationen, da sie in einem anderen Schritt benötigt werden.
    >
    > 4.  Wählen Sie im Verwaltungsportal **Einstellungen** und anschließend **Administratoren** aus. Wählen Sie **Hinzufügen** aus, und fügen Sie den neuen Benutzer als Co-Administrator hinzu. Dies ermöglicht es dem Organisations-Konto, Ihr Azure-Abonnement zu verwalten.
    >
    > 5.  Melden Sie sich schließlich vom Azure-Portal ab und dann unter Verwendung des Organisations-Kontos erneut beim Azure-Portal an. Wenn Sie sich zu diesem Zeitpunkt zum ersten Mal mit diesem Konto anmelden, werden Sie aufgefordert, das Kennwort zu ändern.
    >
    > Weitere Informationen zu Organisations-Konten in Microsoft Azure finden Sie unter [Anmelden bei Microsoft Azure als Organisation][Anmelden bei Microsoft Azure als Organisation].

### Verwenden eines Zertifikats

Das Azure-Modul enthält Cmdlets, mit denen Sie das Zertifikat herunterladen und importieren können.

-   Mit dem Cmdlet **Get-AzurePublishSettingsFile** wird eine Webseite im
    Azure-Verwaltungsportal geöffnet, von der Sie
    die Abonnementinformationen herunterladen können. Die Informationen befinden sich in einer Datei mit der Erweiterung \*.publishsettings.

-   Die Datei mit der Erweiterung \*.publishsettings wird mit **Import-AzurePublishSettingsFile** zur Verwendung durch das Modul importiert. Diese Datei enthält ein Verwaltungszertifikat mit Sicherheitsanmeldeinformationen.

<div class="dev-callout"> 
<b>Hinweis</b>
<p>Die Cmdlets im AzureResourceManager-Modul erfordern Azure AD (Add-AzureAccount). Sie unterst&uuml;tzen keine Ver&ouml;ffentlichungseinstellungendateien. Weitere Informationen zu den Cmdlets im AzureResourceManager-Modul finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkID=394765">Azure Resource Manager Cmdlets</a>.</p> 
</div>

<div class="dev-callout"> 
<b>Wichtig</b> 
<p>Es empfiehlt sich, das mithilfe von <b>Get-AzurePublishSettingsFile</b> heruntergeladene Ver&ouml;ffentlichungsprofil nach dem Import dieser Einstellungen zu
l&ouml;schen. Da das Verwaltungszertifikat Sicherheitsanmeldeinformationen enth&auml;lt, sollte nicht von nicht autorisierten Benutzern darauf zugegriffen werden.
 Wenn Sie Informationen zu Ihren Abonnements ben&ouml;tigen, k&ouml;nnen Sie diese im <a href="http://manage.windowsazure.com/">Azure-Verwaltungsportal</a> oder <a href="http://go.microsoft.com/fwlink/p/?LinkId=324875">Microsoft Online Services-Kundenportal</a> einsehen.
</p> 
</div>

1.  Melden Sie sich mit den Anmeldeinformationen für Ihr Azure-Konto beim [Azure-Verwaltungsportal][2] an.

2.  Öffnen Sie die Azure PowerShell-Konsole, wie unter [Gewusst wie: Installieren von Azure PowerShell][Gewusst wie: Installieren von Azure PowerShell] beschrieben.

3.  Geben Sie den folgenden Befehl ein:

    `Get-AzurePublishSettingsFile`

4.  Laden Sie das Veröffentlichungsprofil herunter, und speichern Sie es, wenn Sie eine
    entsprechende Aufforderung erhalten, und notieren Sie den Pfad und Namen der Datei mit der Erweiterung \*.publishsettings. Diese Informationen werden benötigt, wenn Sie das Cmdlet
    **Import-AzurePublishSettingsFile** ausführen, um die Einstellungen zu importieren. Für den
    Standardspeicherort und -dateinamen gilt folgendes Format:

    `C:\UsersC:\\Users\<UserProfile>\\Download\\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings`lt;UserProfile\>\\Download\\[\*MySubscription\*-...]-\*downloadDate\*-credentials.publishsettings</code>

5.  Geben Sie einen Befehl wie den folgenden ein, wobei Sie die Platzhalter durch Ihren Windows-Kontonamen und den Pfad und Dateinamen ersetzen:

    `Import-AzurePublishSettingsFile C:\UsersImport-AzurePublishSettingsFile C:\Users\<UserProfile>\Downloads\<SubscriptionName>-credentials.publishsettings`lt;UserProfile\>\\Downloads`Import-AzurePublishSettingsFile C:\Users\<UserProfile>\Downloads\<SubscriptionName>-credentials.publishsettings`lt;SubscriptionName\>-credentials.publishsettings</code>

> [WACOM.NOTE] Wenn Sie nach dem Import Ihrer Veröffentlichungseinstellungen als Co-Administrator zu anderen Abonnements hinzugefügt
> werden, müssen Sie diesen Vorgang wiederholen, um eine neue Datei
> mit der Erweiterung \*.publishsettings herunterzuladen und dann diese Einstellungen zu importieren. Informationen zum Hinzufügen von Co-Administratoren als
> Unterstützung bei der Verwaltung von Diensten für ein Abonnement finden Sie unter [Hinzufügen und Entfernen von Co-Administratoren für Azure-Abonnements][Hinzufügen und Entfernen von Co-Administratoren für Azure-Abonnements].

### Anzeigen von Konto- und Abonnementinformationen

Sie können über mehrere Konten und Abonnements zur Verwendung durch Azure PowerShell verfügen und mehrere Konten hinzufügen, indem Sie "Add-AzureAccount" mehr als einmal ausführen.

Geben Sie Folgendes ein, um die verfügbaren Azure-Konten abzurufen:

    Get-AzureAccount

Geben Sie Folgendes ein, um Ihre Azure-Abonnements abzurufen:

    Get-AzureSubscription

## <span id="Ex"></span></a>Verwenden der Cmdlets: Beispiel

Nachdem Sie das Modul installiert und die Verbindung zwischen Ihrem Computer und dem Abonnement konfiguriert haben, können Sie eine Azure-Website erstellen. Mit diesem Beispiel finden Sie den Einstieg in die Verwendung der Azure-Cmdlets.

1.  Starten Sie die Azure PowerShell-Konsole.

2.  Wählen Sie einen Namen Ihre Website. Dieser Name muss den DNS-Benennungskonventionen entsprechen. Gültige Namen dürfen nur Buchstaben von a bis z, Ziffern von 0 bis 9 und Bindestriche (-) enthalten.

    Der Websitename muss in Azure eindeutig sein. In diesem Beispiel wird "mySite" verwendet. Wählen Sie selbst aber einen anderen Namen, beispielsweise Ihren Kontonamen, gefolgt von einer Nummer.

    Geben Sie einen Befehl wie den folgenden ein, nachdem Sie einen Namen ausgewählt haben. Ersetzen Sie "mySite" durch den von Ihnen ausgewählten Websitenamen.

    `New-AzureWebsite mySite`

    Die Website wird erstellt, und es wird ein Objekt zurückgegeben, das die neue Website darstellt. Die Objekteigenschaften enthalten nützliche Informationen zu der Website.

3.  Um Informationen zu der Website zu erhalten, geben Sie folgenden Befehl ein. Dadurch werden einige Informationen zu allen Websites im Abonnement, darunter diejenige, die Sie gerade erstellt haben, zurückgegeben.

    `Get-AzureWebsite`

4.  Wenn Sie weitere Informationen zu Ihrer Website erhalten möchten, nehmen Sie den Websitenamen in den Befehl auf. Achten Sie darauf, "mySite" durch den Namen Ihrer Website zu ersetzen.

    `Get-AzureWebsite -Name mySite`

5.  Websites werden nach der Erstellung gestartet. Geben Sie folgenden Befehl einschließlich des Namens Ihrer Website ein, um die Website zu beenden.

    `Stop-AzureWebsite -Name mySite`

6.  Um zu überprüfen, ob die Website beendet wurde, führen Sie den Befehl "Get-AzureWebsite" erneut aus.

    `Get-AzureWebsite`

7.  Löschen Sie die Website, um diesen Test abzuschließen. Geben Sie Folgendes ein:

    `Remove-AzureWebsite -Name mySite`

8.  Überprüfen Sie, ob die Website gelöscht wurde, um die Aufgabe abzuschließen.

    `Get-AzureWebsite -Name mySite`

## <span id="Help"></span></a>Hilfe

Diese Ressourcen enthalten Hilfethemen für bestimmte Cmdlets:

-   Sie können in der Konsole das integrierte Hilfesystem verwenden. Das Cmdlet **Get-Help** ermöglicht den Zugriff auf dieses System. Die folgende Tabelle enthält einige Beispiele für Befehle, die Sie verwenden können, um Hilfe anzufordern. Sie können weitere Informationen in der Konsole erhalten, indem Sie **help** eingeben.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Befehl</strong></td>
    <td align="left"><strong>Ergebnis</strong></td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help</td>
    <td align="left">Beschreibt die Verwendung des Hilfesystems.
    <p><strong>Hinweis</strong>: Die Beschreibung enthält auch Informationen zu Hilfedateien, die sich nicht auf das Azure-Modul beziehen. Insbesondere werden bei der Installation des Moduls auch Hilfedateien installiert. Sie können nicht separat heruntergeladen werden.</p></td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help Azure</td>
    <td align="left">Ruft alle Cmdlets im Azure-Modul ab.</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>Sprache</strong>&gt;-dev</td>
    <td align="left">Ruft Cmdlets für die Entwicklung und Verwaltung von Anwendungen in einer bestimmten Sprache ab. Beispiele: help node-dev, help php-dev oder help python-dev.</td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help &lt;<strong>Cmdlet</strong>&gt;</td>
    <td align="left">Ruft die Hilfe zu einem Windows PowerShell-Cmdlet ab. Ersetzten Sie <cmdlet> durch den Namen des Cmdlets.</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>Cmdlet</strong>&gt; -Parameter *</td>
    <td align="left">Ruft Beschreibungen der Cmdlet-Parameter ab. Das Sternchen (*) steht für &quot;alle&quot;.</td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help &lt;<strong>Cmdlet</strong>&gt; -Examples</td>
    <td align="left">Ruft die Syntax des Cmdlets und Beispiele zu dessen Verwendung ab.</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>Cmdlet</strong>&gt; -Full</td>
    <td align="left">Ruft die gesamte Hilfe zu einem Cmdlet einschließlich technischer Details ab.</td>
    </tr>
    </tbody>
    </table>

-   Referenzinformationen zu den Cmdlets in den Azure PowerShell-Modulen sind in der Azure-Bibliothek verfügbar. Informationen finden Sie in der [Azure-Cmdlet-Referenz][Azure-Cmdlet-Referenz].

Hilfe von der Community erhalten Sie in den folgenden beliebten Foren:

-   [Azure-Forum auf MSDN][Azure-Forum auf MSDN]
-   [Stackoverflow][Stackoverflow]

## <span id="Resources"></span></a>Zusätzliche Ressourcen

Im Folgenden werden einige der verfügbaren Ressourcen genannt, in denen Sie erfahren, wie Azure und Windows PowerShell verwendet werden.

-   Um Feedback zu Cmdlets zu geben, Probleme zu melden oder auf den Quellcode zuzugreifen, rufen Sie das [Azure PowerShell-Coderepository][Azure PowerShell-Coderepository] auf.

-   Informationen zur Windows PowerShell-Befehlszeile und -skriptingumgebung finden Sie im [TechNet Script Center][TechNet Script Center].

-   Informationen zum Installieren, Erlernen, Verwenden und Anpassen von Windows PowerShell finden Sie unter [Skripterstellung mit Windows PowerShell][Skripterstellung mit Windows PowerShell].

-   Informationen zu Skripten und ihrer Ausführung in Windows PowerShell finden Sie unter [Ausführen von Skripten][Ausführen von Skripten]. Dieser Artikel enthält grundlegende Informationen zum Erstellen von Skripten und Konfigurieren des Computers zur Ausführung von Skripten.

-   Informationen zu Cmdlets für Azure AD finden Sie unter [Verwalten von Azure AD mithilfe von Windows PowerShell][Verwalten von Azure AD mithilfe von Windows PowerShell].

  [Azure PowerShell-Coderepository]: https://github.com/WindowsAzure/azure-sdk-tools
  [Erforderliche Komponenten für die Verwendung von Azure PowerShell]: #Prereq
  [Gewusst wie: Installieren von Azure PowerShell]: #Install
  [Gewusst wie: Verbinden mit Ihrem Abonnement]: #Connect
  [Verwenden der Cmdlets: Beispiel]: #Ex
  [Hilfe]: #Help
  [Zusätzliche Ressourcen]: #Resources
  [Microsoft-Webplattform-Installer]: http://go.microsoft.com/fwlink/p/?LinkId=320376
  [Befehlszeilentools]: http://go.microsoft.com/fwlink/?LinkId=320796
  [Erste Schritte mit Azure]: http://go.microsoft.com/fwlink/p/?LinkId=320795
  [Verwalten von Konten, Abonnements und Administratorrollen]: http://go.microsoft.com/fwlink/?LinkId=324796
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com
  [Anmelden bei Microsoft Azure als Organisation]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
  [2]: http://manage.windowsazure.com
  [Hinzufügen und Entfernen von Co-Administratoren für Azure-Abonnements]: http://msdn.microsoft.com/library/windowsazure/gg456328.aspx
  [Azure-Cmdlet-Referenz]: http://msdn.microsoft.com/library/windowsazure/jj554330.aspx
  [Azure-Forum auf MSDN]: http://go.microsoft.com/fwlink/p/?LinkId=320212
  [Stackoverflow]: http://go.microsoft.com/fwlink/?LinkId=320213
  [TechNet Script Center]: http://go.microsoft.com/fwlink/p/?LinkId=320211
  [Skripterstellung mit Windows PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320210
  [Ausführen von Skripten]: http://go.microsoft.com/fwlink/p/?LinkId=320627
  [Verwalten von Azure AD mithilfe von Windows PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320628

<!--HONumber=46--> 
