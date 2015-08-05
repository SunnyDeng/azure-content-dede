<properties
	pageTitle="Installieren und Konfigurieren von Azure PowerShell"
	description="Erfahren Sie, wie Sie Azure PowerShell installieren und konfigurieren."
	editor="tysonn"
	manager="stevenka"
	documentationCenter=""
	services=""
	authors="coreyp-at-msft"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="coreyp"/>

# Installieren und Konfigurieren von Azure PowerShell#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure-Befehlszeilenschnittstelle">Azure-Befehlszeilenschnittstelle</a></div>

Mit Windows PowerShell können Sie eine Vielzahl von Aufgaben in Azure interaktiv an einer Eingabeaufforderung oder automatisch durch Skripts durchführen. Azure PowerShell ist ein Modul, das Cmdlets zum Verwalten von Azure durch Windows PowerShell enthält. Sie können diese Cmdlets verwenden, um durch die Azure-Plattform bereitgestellte Lösungen und Dienste zu erstellen, zu testen, bereitzustellen und zu verwalten. In den meisten Fällen können Sie mit den Cmdlets dieselben Aufgaben ausführen wie über das Azure-Verwaltungsportal. Beispielsweise können Sie Cloud-Dienste, virtuelle Computer, virtuelle Netzwerke und Web-Apps erstellen und konfigurieren.

Das Modul wird als herunterladbare Datei verteilt und der Quellcode wird in einem öffentlich verfügbaren Repository verwaltet. Weiter unten in diesem Thema wird in den Installationsanweisungen ein Link zu den herunterladbaren Dateien bereitgestellt. Informationen zum Quellcode finden Sie im [Azure PowerShell-Coderepository](https://github.com/Azure/azure-powershell).

Dieser Leitfaden enthält grundlegende Informationen zum Installieren und Einrichten von Azure PowerShell, um die Azure-Plattform zu verwalten.

### <a id="Prereq"></a>Erforderliche Komponenten für die Verwendung von Azure PowerShell

Azure ist eine abonnementbasierte Plattform. Das bedeutet, dass zur Verwendung der Plattform ein Abonnement benötigt wird. In den meisten Fällen werden für die Cmdlets Abonnementinformationen benötigt, um die Aufgaben mit Ihrem Abonnement auszuführen. (Einige der speicherbezogenen Cmdlets können ohne diese Informationen verwendet werden.) Sie stellen sie zur Verfügung, indem Sie die Verbindung mit Ihrem Abonnement auf Ihrem Computer konfigurieren. Eine Anleitung finden Sie in diesem Artikel unter „Verbinden mit Ihrem Abonnement“.

> [AZURE.NOTE]Ab Version 0.8.5 erfordern die Azure PowerShell-Module Microsoft .NET Framework 4.5.

Bei der Installation des Moduls wird vom Installationsprogramm geprüft, ob das System über die erforderliche Software verfügt, und es werden alle Abhängigkeiten installiert, z. B. die richtige Version von Windows PowerShell und .NET Framework.

<a id="Install"></a>
## Installieren von Azure PowerShell

Sie können die Azure PowerShell-Module herunterladen und installieren, indem Sie den [Microsoft-Webplattform-Installer](http://go.microsoft.com/fwlink/p/?LinkId=320376) ausführen. Klicken Sie in der Nachfrage auf **Ausführen**. Der Webplattform-Installer installiert die Azure PowerShell-Module und alle Abhängigkeiten. Befolgen Sie die Anweisungen, um die Installation abzuschließen.

> [AZURE.NOTE]Wenn Sie nur den PowerShell-Installer herunterladen möchten, besuchen Sie https://github.com/Azure/azure-powershell/releases. In diesem Repository finden Sie auch den Quellcode für die PowerShell-Cmdlets.

Weitere Informationen zu den für Azure verfügbaren Befehlszeilentools finden Sie unter [Befehlszeilentools](http://go.microsoft.com/fwlink/?LinkId=320796).

Bei der Installation des Moduls wird auch eine benutzerdefinierte Konsole für Azure PowerShell installiert. Sie können die Cmdlets über die Windows PowerShell-Standardkonsole oder über die Azure PowerShell-Konsole ausführen.

Die Methode, die Sie zum Öffnen einer der beiden Konsolen verwenden, ist abhängig von der ausgeführten Windows-Version:

- Auf einem Computer, auf dem mindestens Windows 8 oder Windows Server 2012 ausgeführt wird, können Sie die integrierte Suche verwenden. Beginnen Sie auf dem Startbildschirm mit der Eingabe von **power**. Daraufhin wird eine entsprechende Liste von Anwendungen zurückgegeben, die auch Windows PowerShell und Azure PowerShell umfasst. Klicken Sie auf eine dieser beiden Anwendungen, um die Konsole zu öffnen. (Klicken Sie mit der rechten Maustaste auf das Symbol, um die Anwendung an den Startbildschirm anzuheften.)

- Verwenden Sie auf einem Computer, auf dem eine niedrigere Version als Windows 8 oder Windows Server 2012 ausgeführt wird, das Startmenü. Klicken Sie im Startmenü auf **Alle Programme** > **Azure** und dann auf **Azure PowerShell**.

<a id="Connect"></a>
## Verbinden mit Ihrem Abonnement

Zur Verwendung von Azure wird ein Abonnement benötigt. Wenn Sie über kein Abonnement verfügen, lesen Sie die Informationen unter [Erste Schritte mit Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

Für die Cmdlets ist Ihr Abonnement erforderlich, damit Ihre Dienste verwaltet werden können. Es gibt zwei Möglichkeiten, Ihre Abonnementinformationen für Windows PowerShell bereitzustellen. Sie können ein Verwaltungszertifikat verwenden, das die Informationen enthält, oder sich mit Ihrem Microsoft-Konto bzw. einem Geschäfts- oder Schulkonto bei Azure anmelden. Wenn Sie sich anmelden, authentifiziert Azure Active Directory (Azure AD) die Anmeldeinformationen und gibt ein Zugriffstoken zurück, mit dem Azure PowerShell Ihr Konto verwalten kann.

Beachten Sie bezüglich der Auswahl der geeigneten Authentifizierungsmethode Folgendes:

- Die Authentifizierung mit Azure AD wird empfohlen, weil sich damit leichter der Zugriff auf ein Abonnement verwalten lässt. Mit dem Update in Version 0.8.6 wird ein Automatisierungsszenario mit Azure AD-Authentifizierung ebenso wie bei Verwendung eines Schul-Kontos möglich. Auch mit der Azure Resource Manager-API ist dies möglich.
- Bei Verwendung eines Zertifikats sind die Abonnementinformationen für den gesamten Gültigkeitszeitraum des Abonnements und Zertifikats verfügbar. Diese Methode erschwert jedoch die Verwaltung des Zugriffs auf ein freigegebenes Abonnement, etwa, wenn mehr als eine Person für den Zugriff auf das Konto berechtigt ist. Die Azure Resource Manager-API akzeptiert außerdem keine Zertifikatauthentifizierung.

Weitere Informationen zur Authentifizierungs- und Abonnemontverwaltung in Azure finden Sie unter [Verwalten von Konten, Abonnements und Administratorrollen](http://go.microsoft.com/fwlink/?LinkId=324796).

### Verwenden von Azure AD

1. Öffnen Sie die Microsoft Azure PowerShell-Konsole, wie unter [Installieren von Azure PowerShell](#Install) beschrieben.

2. Geben Sie den folgenden Befehl ein:

		Add-AzureAccount

3. Geben Sie in dem Fenster die dem Konto zugeordnete E-Mail-Adresse und das zugehörige Kennwort ein.

4. Die Anmeldeinformationen werden von Azure authentifiziert und gespeichert, dann wird das Fenster geschlossen.

5. Ab Version 0.8.6 können Sie bei Verwendung eines Geschäfts- oder Schulkontos den folgenden Befehl eingeben, um das Popupfenster zu umgehen. Damit wird das Windows PowerShell-Standardfenster für Anmeldeinformationen eingeblendet, in dem Sie den Benutzernamen und das Kennwort Ihres Geschäfts- oder Schulkontos eingeben können.

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

	> [AZURE.NOTE]Weitere Informationen zur Sicherheit und zur Verwendung von Anmeldeinformationen finden Sie unter [Best Practices für das Bereitstellen von Kennwörtern und anderen vertraulichen Daten auf ASP.NET und Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

	> [AZURE.NOTE]Diese nicht-interaktive Anmeldemethode funktioniert nur mit einem Geschäfts- oder Schulkonto. Bei einem Geschäfts- oder Schulkonto handelt es sich um ein von Ihrem Unternehmen bzw. Ihrer Bildungseinrichtung verwaltetes Benutzerkonto, das in der entsprechenden Azure Active Directory-Instanz definiert ist. Wenn Sie derzeit kein Geschäfts- oder Schulkonto besitzen und sich mit einem Microsoft-Konto bei Ihrem Azure-Abonnement anmelden, können Sie mit den folgenden Schritten auf einfache Weise ein solches Konto erstellen.
	>
	> 1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an, und klicken Sie auf **Active Directory**.
	>
	> 2. Wenn kein Verzeichnis vorhanden ist, wählen Sie **Create your directory** (Verzeichnis erstellen) aus, und stellen Sie die geforderten Informationen bereit.
	>
	> 3. Wählen Sie Ihr Verzeichnis aus, und fügen Sie einen neuen Benutzer hinzu. Mit diesem neuen Benutzer können Sie sich über ein Geschäfts- oder Schulkonto anmelden.
	>
	>     Während der Erstellung des Benutzers erhalten Sie sowohl eine E-Mail-Adresse für den Benutzer als auch ein temporäres Kennwort. Speichern Sie diese Informationen, da sie in einem anderen Schritt benötigt werden.
	>
	> 4. Wählen Sie im Verwaltungsportal **Einstellungen** und anschließend **Administratoren** aus. Wählen Sie **Hinzufügen** aus, und fügen Sie den neuen Benutzer als Co-Administrator hinzu. Dies ermöglicht es dem Geschäfts- oder Schulkonto, Ihr Azure-Abonnement zu verwalten.
	>
	> 5. Melden Sie sich schließlich vom Azure-Portal ab und dann unter Verwendung des Geschäfts- oder Schulkontos erneut beim Azure-Portal an. Wenn Sie sich zu diesem Zeitpunkt zum ersten Mal mit diesem Konto anmelden, werden Sie aufgefordert, das Kennwort zu ändern.
	>
	>Weitere Informationen zur Anmeldung bei Microsoft Azure mit einem Geschäfts- oder Schulkonto finden Sie unter [Anmelden bei Microsoft Azure als Organisation](sign-up-organization.md).

### Verwenden eines Zertifikats

Das Azure-Modul enthält Cmdlets, mit denen Sie das Zertifikat herunterladen und importieren können.

> [AZURE.NOTE]Die Cmdlets im AzureResourceManager-Modul erfordern Azure AD (Add-AzureAccount). Sie unterstützen keine Veröffentlichungseinstellungendateien. Weitere Informationen zu den Cmdlets im AzureResourceManager-Modul finden Sie unter [Azure Resource Manager Cmdlets](http://go.microsoft.com/fwlink/?LinkID=394765).


- Mit dem Cmdlet **Get-AzurePublishSettingsFile** wird eine Webseite im Azure-Verwaltungsportal geöffnet, von der Sie die Abonnementinformationen herunterladen können. Die Informationen befinden sich in einer Datei mit der Erweiterung *.publishsettings.

- Die Datei mit der Erweiterung *.publishsettings wird mit **Import-AzurePublishSettingsFile** zur Verwendung durch das Modul importiert. Diese Datei enthält ein Verwaltungszertifikat mit Sicherheitsanmeldeinformationen.

> [AZURE.IMPORTANT]Es empfiehlt sich, das mithilfe von <b>Get-AzurePublishSettingsFile</b> heruntergeladene Veröffentlichungsprofil nach dem Import dieser Einstellungen zu löschen. Da das Verwaltungszertifikat Sicherheitsanmeldeinformationen enthält, sollte nicht von nicht autorisierten Benutzern darauf zugegriffen werden. Wenn Sie Informationen zu Ihren Abonnements benötigen, können Sie diese im [Azure-Verwaltungsportal](http://manage.windowsazure.com/) oder [Microsoft Online Services-Kundenportal](http://go.microsoft.com/fwlink/p/?LinkId=324875) einsehen.

1. Melden Sie sich mit den Anmeldeinformationen für Ihr Azure-Konto beim [Azure-Verwaltungsportal](http://manage.windowsazure.com) an.

2. Öffnen Sie die Azure PowerShell-Konsole, wie unter [Installieren von Azure PowerShell](#Install) beschrieben.

3. Geben Sie den folgenden Befehl ein:

		Get-AzurePublishSettingsFile

4. Laden Sie das Veröffentlichungsprofil herunter und speichern Sie es, wenn Sie eine entsprechende Aufforderung erhalten, und notieren Sie den Pfad und Namen der Datei mit der Erweiterung *.publishsettings. Diese Informationen werden benötigt, wenn Sie das Cmdlet **Import-AzurePublishSettingsFile** ausführen, um die Einstellungen zu importieren. Für den Standardspeicherort und -dateinamen gilt folgendes Format:

			C:\Users<UserProfile>\Download\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings

5. Geben Sie einen Befehl wie den folgenden ein, wobei Sie die Platzhalter durch Ihren Windows-Kontonamen und den Pfad und Dateinamen ersetzen:

		Import-AzurePublishSettingsFile C:\Users<UserProfile>\Downloads<SubscriptionName>-credentials.publishsettings

> [AZURE.NOTE]Wenn Sie nach dem Import Ihrer Veröffentlichungseinstellungen anderen Abonnements als Co-Administrator hinzugefügt werden, müssen Sie diesen Vorgang wiederholen, um eine neue Datei mit der Erweiterung „*.publishsettings“ herunterzuladen, und diese Einstellungen dann importieren. Informationen zum Hinzufügen von Co-Administratoren als Unterstützung bei der Verwaltung von Diensten für ein Abonnement finden Sie unter [Hinzufügen und Entfernen von Co-Administratoren für Azure-Abonnements](http://msdn.microsoft.com/library/windowsazure/gg456328.aspx).

### Anzeigen von Konto- und Abonnementinformationen

Sie können über mehrere Konten und Abonnements zur Verwendung durch Azure PowerShell verfügen und mehrere Konten hinzufügen, indem Sie "Add-AzureAccount" mehr als einmal ausführen.

Geben Sie Folgendes ein, um die verfügbaren Azure-Konten abzurufen:

	Get-AzureAccount

Geben Sie Folgendes ein, um Ihre Azure-Abonnements abzurufen:

	Get-AzureSubscription

## <a id="Ex"></a>Verwenden von Cmdlets: Beispiel ##

Nachdem Sie das Modul installiert und die Verbindung zwischen Ihrem Computer und dem Abonnement konfiguriert haben, können Sie eine Azure-Web-App erstellen. Mit diesem Beispiel finden Sie den Einstieg in die Verwendung der Azure-Cmdlets.

1. Starten Sie die Azure PowerShell-Konsole.

2. Geben Sie einen Namen für die Web-App ein. Dieser Name muss den DNS-Benennungskonventionen entsprechen. Gültige Namen dürfen nur Buchstaben von a bis z, Ziffern von 0 bis 9 und Bindestriche (-) enthalten.

	Der Name der Web-App muss in Azure eindeutig sein. In diesem Beispiel wird "mySite" verwendet. Wählen Sie selbst aber einen anderen Namen, beispielsweise Ihren Kontonamen, gefolgt von einer Nummer.

	Geben Sie einen Befehl wie den folgenden ein, nachdem Sie einen Namen ausgewählt haben. Ersetzen Sie "mySite" durch den von Ihnen ausgewählten Web-App-Namen.

		New-AzureWebsite mySite

	Die Web-App wird erstellt, und es wird ein Objekt zurückgegeben, das die neue Web-App darstellt. Die Objekteigenschaften enthalten nützliche Informationen zu der Web-App.

3. Um Informationen zu der Web-App zu erhalten, geben Sie folgenden Befehl ein. Dadurch werden einige Informationen zu allen Web-Apps im Abonnement, darunter diejenige, die Sie gerade erstellt haben, zurückgegeben.

		Get-AzureWebsite

4. Wenn Sie weitere Informationen zu Ihrer Web-App erhalten möchten, nehmen Sie den Namen der Web-App in den Befehl auf. Achten Sie darauf, "mySite" durch den Namen Ihrer Web-App zu ersetzen.

		Get-AzureWebsite -Name mySite

5. Web-Apps werden nach der Erstellung gestartet. Geben Sie folgenden Befehl einschließlich des Namens Ihrer Web-App ein, um die Web-App zu beenden.

		Stop-AzureWebsite -Name mySite

6. Um zu überprüfen, ob die Website beendet wurde, führen Sie den Befehl "Get-AzureWebsite" erneut aus.

		Get-AzureWebsite

7. Löschen Sie die Web-App, um diesen Test abzuschließen. Geben Sie Folgendes ein:

		Remove-AzureWebsite -Name mySite

7. Überprüfen Sie, ob die Web-App gelöscht wurde, um die Aufgabe abzuschließen.

		Get-AzureWebsite -Name mySite

##<a id="Help"></a>Hilfe##

Diese Ressourcen enthalten Hilfethemen für bestimmte Cmdlets:


-   Sie können in der Konsole das integrierte Hilfesystem verwenden. Das Cmdlet **Get-Help** ermöglicht den Zugriff auf dieses System. Die folgende Tabelle enthält einige Beispiele für Befehle, die Sie verwenden können, um Hilfe anzufordern. Sie können weitere Informationen in der Konsole erhalten, indem Sie **help** eingeben.

    <table border="1" cellspacing="4" cellpadding="4">
<tbody>
<tr align="left" valign="top">
	<td><b>Befehl</b></td>
	<td><b>Ergebnis</b></td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help</td>
	<td>Beschreibt die Verwendung des Hilfesystems. <p><b>Hinweis:</b> Die Beschreibung enthält auch Informationen zu Hilfedateien, die sich nicht auf das Azure-Modul beziehen. Insbesondere werden bei der Installation des Moduls auch Hilfedateien installiert. Sie können nicht separat heruntergeladen werden.</p>
</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help Azure</td>
	<td>Ruft alle Cmdlets im Azure-Modul ab.</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>Sprache</b>>-dev</td>
	<td>Ruft Cmdlets für die Entwicklung und Verwaltung von Anwendungen in einer bestimmten Sprache ab. Beispiele: help node-dev, help php-dev oder help python-dev.</td>
</tr>
    <tr align="left" valign="top">
	<td>Get-Help &lt;<b>Cmdlet</b>></td>
	<td>Ruft die Hilfe zu einem Windows PowerShell-Cmdlet ab. Ersetzten Sie <cmdlet> durch den Namen des Cmdlets.</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>Cmdlet</b>> -Parameter *</td>
	<td>Ruft Beschreibungen der Cmdlet-Parameter ab. Das Sternchen (*) steht für "alle".</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>Cmdlet</b>> -Examples</td>
	<td>Ruft die Syntax des Cmdlets und Beispiele zu dessen Verwendung ab.</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>Cmdlet</b>> -Full</td>
	<td>Ruft die gesamte Hilfe zu einem Cmdlet einschließlich technischer Details ab.</td>
</tr>
</tbody>
</table>



- Referenzinformationen zu den Cmdlets in den Azure PowerShell-Modulen sind in der Azure-Bibliothek verfügbar. Informationen finden Sie in der [Azure-Cmdlet-Referenz](http://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Hilfe von der Community erhalten Sie in den folgenden beliebten Foren:

- [Azure-Forum auf MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)


## <a id="Resources"></a>Zusätzliche Ressourcen ##

Im Folgenden werden einige der verfügbaren Ressourcen genannt, in denen Sie erfahren, wie Azure und Windows PowerShell verwendet werden.

- Weitere Informationen zum Zugriff auf Azure Storage-Komponenten finden Sie unter [Verwenden von Azure PowerShell mit Azure Storage](storage-powershell-guide-full.md).

- Um Feedback zu Cmdlets zu geben, Probleme zu melden oder auf den Quellcode zuzugreifen, rufen Sie das [Azure PowerShell-Coderepository](https://github.com/WindowsAzure/azure-sdk-tools) auf.

- Informationen zur Windows PowerShell-Befehlszeile und -skriptingumgebung finden Sie im [TechNet Script Center](http://go.microsoft.com/fwlink/p/?LinkId=320211).

- Informationen zum Installieren, Erlernen, Verwenden und Anpassen von Windows PowerShell finden Sie unter [Skripterstellung mit Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320210).

- Informationen zu Skripten und ihrer Ausführung in Windows PowerShell finden Sie unter [Ausführen von Skripten](http://go.microsoft.com/fwlink/p/?LinkId=320627). Dieser Artikel enthält grundlegende Informationen zum Erstellen von Skripten und Konfigurieren des Computers zur Ausführung von Skripten.

- Informationen zu Cmdlets für Azure AD finden Sie unter [Verwalten von Azure AD mithilfe von Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320628).





  [Microsoft Online Services Customer Portal]: https://mocp.microsoftonline.com/site/default.aspx

<!---HONumber=July15_HO4-->