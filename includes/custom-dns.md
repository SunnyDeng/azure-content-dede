# Konfigurieren eines benutzerdefinierten Domänennamens für einen Azure-Clouddienst

> [WACOM.NOTE]
> Schneller ans Ziel kommen – mit der neuen [Komplettanleitung][Komplettanleitung] für Azure. Mit dieser Anleitung wird das Zuordnen eines benutzerdefinierten Domänennamens zu Azure-Clouddiensten und Azure-Websites sowie das Absichern der Kommunikation mittels SSL zum Kinderspiel.

Wenn Sie eine Anwendung in Azure erstellen, bietet Azure eine Unterdomäne in der cloudapp.net-Domäne, damit Benutzer über eine URL wie http://&lt;*myapp*>.cloudapp.net auf Ihre Anwendung zugreifen können. Sie können Ihre Anwendung jedoch auch unter Ihrem eigenen Domänennamen zur Verfügung stellen, beispielsweise contoso.com.

> [WACOM.NOTE]
> Die Vorgehensweisen in dieser Aufgabe gelten für Azure-Clouddienste. Informationen zu Speicherkonten finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für ein Azure-Speicherkonto][Konfigurieren eines benutzerdefinierten Domänennamens für ein Azure-Speicherkonto]. Informationen zu Websites finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website][Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website].

Themen in diesem Artikel:

-   [Informationen zu CNAME- und A-Datensätzen][Informationen zu CNAME- und A-Datensätzen]
-   [Hinzufügen eines CNAME-Datensatzes zu Ihrer benutzerdefinierten Domäne][Hinzufügen eines CNAME-Datensatzes zu Ihrer benutzerdefinierten Domäne]
-   [Hinzufügen eines A-Datensatzes für Ihre benutzerdefinierte Domäne][Hinzufügen eines A-Datensatzes für Ihre benutzerdefinierte Domäne]

## <a name="access-app"></a>Informationen zu CNAME- und A-Datensätzen

Mit CNAME- (oder Aliasdatensätze) und A-Datensätzen können Sie einen Domänennamen einem bestimmten Server (oder in diesem Fall Service) zuweisen, beide funktionieren jedoch unterschiedlich. Bei der Verwendung von A-Datensätzen mit Azure-Clouddiensten gibt es auch einige Besonderheiten, die Sie bei der Auswahl der Art des Datensatzes berücksichtigen sollten.

### CNAME- oder Alias-Eintrag

Ein CNAME-Datensatz weist eine *spezifische* Domäne, beispielsweise **contoso.com** oder **www.contoso.com**, zu einem kanonischen Domänennamen zu. In diesem Fall ist der kanonische Domänenname der Domänenname **\<myapp\>.cloudapp.net** Ihrer gehosteten Azure-Anwendung. Sobald der CNAME erstellt ist, erstellt er einen Alias für die **\<myapp\>.cloudapp.net**. Der CNAME-Datensatz wird automatisch an die IP-Adresse Ihres Service **\<myapp\>.cloudapp.net** übertragen. Wenn sich die IP-Adresse Ihres Clouddienstes ändert, müssen Sie nichts weiter tun.

> [WACOM.NOTE]
> Bei einigen Domänenregistrierungen können Sie Unterdomänen nur mit einem CNAME-Datensatz wie www.contoso.com zuweisen und nicht mit einem Stammnamen wie contoso.com. Weitere Informationen zu CNAME-Einträgen finden Sie in der von Ihrer Domänenregistrierungsstelle zur Verfügung gestellten Dokumentation, [im Wikipedia-Artikel "CNAME Resource Record"][im Wikipedia-Artikel "CNAME Resource Record"] oder im Dokument [IETF Domain Names - Implementation and Specification][im Wikipedia-Artikel "CNAME Resource Record"] (Domänennamen – Implementierung und Spezifizierung, in englischer Sprache).

### A-Eintrag

Ein A-Eintrag weist eine Domäne wie **contoso.com**, **www.contoso.com** *oder eine Domäne mit Platzhalter* wie **\*.contoso.com** einer IP-Adresse zu. Im Falle eines Azure Cloud Service ist dies die virtuelle IP des Service. Der Vorteil eines A-Datensatzes gegenüber einem CNAME-Datensatz ist, dass Sie einen Eintrag mit einem Platzhalter verwenden können, beispielsweise \***.contoso.com**, der Anfragen für mehrere Unterdomänen verarbeiten kann, beispielsweise **mail.contoso.com**, **login.contoso.com** oder **www.contso.com**.

> [WACOM.NOTE]
> Da ein A-Datensatz einer statischen IP-Adresse zugewiesen ist, kann er nicht automatisch Änderungen an die IP-Adresse Ihres Clouddiensts übertragen. Die von Ihrem Clouddienst verwendete IP-Adresse wird zum ersten Mal zugewiesen, wenn Sie sie an einem leeren Steckplatz bereitstellen (entweder Produktion oder Staging). Wenn Sie die Bereitstellung für den Steckplatz löschen, wird die IP-Adresse von Azure freigegeben und zukünftigen Bereitstellungen an dem Steckplatz wird eine neue IP-Adresse zugewiesen.
>
> Die IP-Adresse eines bestimmten Bereitstellungs-Steckplatzes (Produktion oder Staging) wird beim Austausch von Staging- und Produktionsbereitstellung oder beim Durchführen eines direkten Upgrades einer vorhandenen Bereitstellung beibehalten. Weitere Informationen zum Durchführen dieser Aktionen finden Sie unter [Verwalten von Clouddiensten][Verwalten von Clouddiensten].

## <a name="add-cname"></a>Hinzufügen eines CNAME-Datensatzes zu Ihrer benutzerdefinierten Domäne

Sie müssen einen neuen Eintrag zu der DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzufügen, um einen CNAME-Datensatz zu erstellen. Verwenden Sie hierzu die durch Ihre Registrierung bereitgestellten Tools. Die Methoden zum Festlegen eines CNAME-Datensatzes der verschiedenen Registrierungen sind zwar ähnlich, jedoch unterscheiden Sie sich auch in einigen Punkten. Die Konzepte sind allerdings gleich.

1.  Verwenden Sie eine dieser Methoden, um den **.cloudapp.net**-Domänennamen zu ermitteln, der Ihrem Clouddienst zugewiesen ist.

  * Melden Sie sich beim [Azure Verwaltungsportal][Azure Verwaltungsportal] an, wählen Sie Ihren Clouddienst aus, wählen Sie **Dashboard** aus, und navigieren Sie dann zum Eintrag **Site URL** in der **Schnelleinsicht**.

          ![quick glance section showing the site URL][csurl]

  * Installieren und konfigurieren Sie [Azure PowerShell][Azure PowerShell], und verwenden Sie dann den folgenden Befehl:

    Get-AzureDeployment -ServiceName yourservicename | Select Url

 Speichern Sie die im Domänennamen verwendete URL, die von der jeweiligen Methode zurückgegeben wurde. Diesen Namen benötigen Sie zum Erstellen eines CNAME-Datensatzes.

1.  Melden Sie sich bei der Website Ihrer DNS-Registrierungsstelle an, und öffnen Sie die Seite für die DNS-Verwaltung. Suchen Sie nach Links oder Bereichen der Site, die als **Domänenname**, **DNS** oder **Namenserververwaltung** bezeichnet werden.

2.  Navigieren Sie nun zu dem Bereich, in dem Sie CNAMEs auswählen oder eingeben können. Möglicherweise müssen Sie den Datensatztyp in einem Dropdownmenü auswählen oder die Seite für erweiterte Einstellungen aufrufen. Suchen Sie nach den Wörtern **CNAME**, **Alias** oder **Unterdomänen**.

3.  Sie müssen außerdem den Domänen- oder Unterdomänenalias für den CNAME angeben, beispielsweise **www**, wenn Sie einen Alias für **www.customdomain.com** erstellen möchten. Wenn Sie einen Alias für die Stammdomäne erstellen möchten, wird dieser möglicherweise als "[\*\*@\*\*][\*\*@\*\*]"-Zeichen in den DNS-Tools Ihrer Registrierung aufgeführt.

4.  Dann müssen Sie einen kanonischen Hostnamen angeben. In diesem Fall ist das die Domäne **cloudapp.net** Ihrer Anwendung.

Der folgende CNAME-Datensatz leitet zum Beispiel den gesamten Verkehr von **www.contoso.com** an **contoso.cloudapp.net** weiter. Das ist der benutzerdefinierte Domänenname Ihrer bereitgestellten Anwendung:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Hostname/Unterdomäne</strong></td>
<td><strong>Kanonische Domäne</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.cloudapp.net</td>
</tr>
</table>

Einem Besucher von **www.contoso.com** wird niemals der wirkliche Host
(contoso.cloudapp.net) angezeigt. Die Weiterleitung ist also für den Endbenutzer
nicht sichtbar.

> [WACOM.NOTE]
> Das oben genannte Beispiel gilt nur für Verkehr an der Unterdomäne **www**. Da Sie keine Platzhalter mit CNAME-Datensätzen verwenden können, müssen Sie einen CNAME für jede Domäne/Unterdomäne erstellen. Wenn Sie den Verkehr von Unterdomänen wie \*.contoso.com an Ihre cloudapp.net-Adresse weiterleiten möchten, können Sie in Ihren DNS-Einstellungen einen Eintrag für **URL-Umleitung** oder **URL-Weiterleitung** erstellen. Alternativ können Sie einen A-Datensatz erstellen.

## <a name="add-aname"></a>Hinzufügen eines A-Datensatzes zu Ihrer benutzerdefinierten Domäne

Sie müssen zunächst die virtuelle IP-Adresse Ihres Clouddiensts ermitteln, um einen A-Datensatz zu erstellen. Fügen Sie dann einen neuen Eintrag zu der DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzu. Verwenden Sie hierzu die durch Ihre Registrierung bereitgestellten Tools. Die Methoden zum Festlegen eines A-Datensatzes der verschiedenen Registrierungen sind zwar ähnlich, jedoch unterscheiden Sie sich auch in einigen Punkten. Die Konzepte sind allerdings gleich.

1.  Verwenden Sie eine der folgenden Methoden, um die IP-Adresse Ihres Clouddiensts zu ermitteln.

  * Melden Sie sich beim [Azure Verwaltungsportal][Azure Verwaltungsportal] an, wählen Sie Ihren Clouddienst aus, wählen Sie **Dashboard** aus, und navigieren Sie dann zur **öffentlichen virtuellen IP (VIP)** in der **Schnelleinsicht**.

   		 ![quick glance section showing the VIP][vip]

  * Installieren und konfigurieren Sie [Azure PowerShell][Azure PowerShell], und verwenden Sie dann den folgenden Befehl:

    get-azurevm -servicename yourservicename | get-azureendpoint -VM {$\_.VM} | select Vip

    Falls Sie Ihrem Clouddienst mehrere Endpunkte zugewiesen haben, erhalten Sie mehrere Zeilen mit der IP-Adresse. Alle Zeilen sollten jedoch dieselbe Adresse anzeigen.

  Speichern Sie die IP-Adresse, da Sie sie zum Erstellen eines A-Datensatzes benötigen.

1.  Melden Sie sich bei der Website Ihrer DNS-Registrierungsstelle an, und öffnen Sie die Seite für die DNS-Verwaltung. Suchen Sie nach Links oder Bereichen der Site, die als **Domänenname**, **DNS** oder **Namenserververwaltung** bezeichnet werden.

2.  Navigieren Sie nun zu dem Bereich, in dem Sie A-Datensätze auswählen oder eingeben können. Möglicherweise müssen Sie den Datensatztyp in einem Dropdownmenü auswählen oder die Seite für erweiterte Einstellungen aufrufen.

3.  Wählen Sie die Domäne oder Unterdomäne aus, die diesen A-Datensatz verwenden wird, oder geben Sie diese ein. Wählen Sie beispielsweise **www**, wenn Sie einen Alias für **www.customdomain.com** erstellen möchten. Wenn Sie einen Platzhaltereintrag für alle Unterdomänen erstellen möchten, geben Sie '__*__' ein. Dieser Eintrag deckt alle Unterdomänen ab wie **mail.customdomain.com**, **login.customdomain.com** und **www.customdomain.com**.

  Wenn Sie einen A-Datensatz für die Stammdomäne erstellen möchten, wird dieser möglicherweise als '**@**' Zeichen in den DNS-Tools Ihrer Registrierung aufgeführt.

1.  Geben Sie die IP-Adresse Ihres Clouddiensts in das angegebene Feld ein. So wird der im A-Datensatz verwendete Domäneneintrag der IP-Adresse Ihrer Clouddienstbereitstellung zugewiesen.

Der folgende A-Datensatz leitet zum Beispiel den gesamten Verkehr von **www.contoso.com** an **137.135.70.239** weiter. Das ist die IP-Adresse Ihrer bereitgestellten Anwendung:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Hostname/Unterdomäne</strong></td>
<td><strong>IP-Adresse</strong></td>
</tr>
<tr>
<td>@</td>
<td>137.135.70.239</td>
</tr>
</table>

Dieses Beispiel zeigt das Erstellen eines A-Datensatzes für die Stammdomäne. Wenn Sie einen Platzhaltereintrag erstellen möchten, der alle Unterdomänen abdeckt, würden Sie '__*__' als Unterdomäne eingeben.

## Nächste Schritte

-   [Verwalten von Cloud-Diensten][Verwalten von Clouddiensten]
-   [Zuordnen von CDN-Inhalt zu einer benutzerdefinierten Domäne][Zuordnen von CDN-Inhalt zu einer benutzerdefinierten Domäne]

  [Komplettanleitung]: http://support.microsoft.com/kb/2990804
  [myapp]: http://<*myapp
  [Konfigurieren eines benutzerdefinierten Domänennamens für ein Azure-Speicherkonto]: ../storage-custom-domain-name/
  [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website]: ../web-sites-custom-domain-name/
  [Informationen zu CNAME- und A-Datensätzen]: #access-app
  [Hinzufügen eines CNAME-Datensatzes zu Ihrer benutzerdefinierten Domäne]: #add-cname
  [Hinzufügen eines A-Datensatzes für Ihre benutzerdefinierte Domäne]: #add-aname
  [im Wikipedia-Artikel "CNAME Resource Record"]: http://tools.ietf.org/html/rfc1035
  [Verwalten von Clouddiensten]: ../cloud-services-how-to-manage/
  [Azure Verwaltungsportal]: https://manage.windowsazure.com
  [Azure PowerShell]: ../install-configure-powershell/
  [\*\*@\*\*]: mailto:'**@**
  [Zuordnen von CDN-Inhalt zu einer benutzerdefinierten Domäne]: http://msdn.microsoft.com/de-de/library/windowsazure/gg680307.aspx
  [vip]: ./media/custom-dns/csvip.png
