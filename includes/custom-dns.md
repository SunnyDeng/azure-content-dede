# Konfigurieren eines benutzerdefinierten Domänennamens für einen Azure-Cloud-Dienst

> [AZURE.NOTE]
> Schneller ans Ziel kommen - mit der NEUEN [Komplettanleitung für Azure](http://support.microsoft.com/kb/2990804)!  Mit dieser Anleitung wird das Zuordnen eines benutzerdefinierten Domänennamens zu Azure-Clouddiensten und Azure-Websites sowie das Absichern der Kommunikation mittels SSL zum Kinderspiel.

Wenn Sie eine Anwendung in Azure erstellen, bietet Azure eine Unterdomäne in der Domäne "cloudapp.net", damit die Benutzer auf Ihre Anwendung mit einer URL wie http://&lt;*myapp*>.cloudapp.net zugreifen können. Sie können Ihre Anwendung jedoch auch unter Ihrem eigenen Domänennamen zur Verfügung stellen, beispielsweise contoso.com.

> [AZURE.NOTE] 
> Die Verfahren in dieser Aufgabe gelten für Azure Cloud Services. Informationen zu Speicherkonten finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für ein Azure-Speicherkonto](../storage-custom-domain-name/). Für Websites siehe [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Websites](../web-sites-custom-domain-name/).

Themen in diesem Artikel:

-   [Grundlegendes zu CNAME- und A-Datensätzen](#access-app)
-   [Hinzufügen eines CNAME-Datensatzes für Ihre benutzerdefinierte Domäne](#add-cname)
-   [Hinzufügen eines A-Datensatzes für Ihre benutzerdefinierte Domäne](#add-aname)

<h2><a name="access-app"></a>Grundlegendes zu CNAME- und A-Datensätzen</h2>

Mit CNAME- (oder Aliasdatensätze) und A-Datensätzen können Sie einen Domänennamen einem bestimmten Server (oder in diesem Fall Service) zuweisen, beide funktionieren jedoch unterschiedlich. Bei der Verwendung von A-Datensätzen mit Azure-Clouddiensten gibt es auch einige Besonderheiten, die Sie bei der Auswahl der Art des Datensatzes berücksichtigen sollten.

###CNAME- oder Alias-Eintrag

Ein CNAME-Eintrag ordnet eine *specific* Domäne, wie z. B. **contoso.com** oder **www.contoso.com**, einem kanonischen Domänennamen zu. In diesem Fall ist der kanonische Domänenname der Domänenname **&lt;myapp>.cloudapp.net** Ihrer von Azure gehosteten Anwendung. Nach der Erstellung erstellt der CNAME-Eintrag einen Alias für **&lt;myapp>.cloudapp.net**. Der CNAME-Eintrag wird automatisch zur IP-Adresse Ihres Diensts **&lt;myapp>.cloudapp.net** aufgelöst. Wenn sich also die IP-Adresse des Cloud-Diensts ändert, müssen Sie keine Maßnahmen ergreifen.

> [AZURE.NOTE] 
> Bei einigen Domänenregistrierungen können Sie Unterdomänen nur mit einem CNAME-Datensatz wie www.contoso.com zuweisen und nicht mit einem Stammnamen wie contoso.com. Weitere Informationen zu CNAME-Datensätzen finden Sie in der Dokumentation Ihrer Registrierungsstelle, <a href="http://en.wikipedia.org/wiki/CNAME_record">dem Wikipedia-Eintrag zum CNAME-Datensatz</a> oder im Dokument <a href="http://tools.ietf.org/html/rfc1035">IETF Domain Names - Implementation and Specification</a>.

###A-Eintrag

Ein A-Datensatz ordnet eine Domäne, wie z. B. **contoso.com** oder **www.contoso.com**, *or a wildcard domain* wie z. B. **\*.contoso.com** einer IP-Adresse zu. Im Falle eines Azure Cloud Service ist dies die virtuelle IP des Service. Daher ist der Hauptvorteil eines A-Datensatz im Vergleich zu einem CNAME-Datensatz, dass Sie einen Eintrag haben können, der einen Platzhalter verwendet, wie z. B. ***.contoso.com**, der Anforderungen für mehrere Unterdomänen wie z. B. **mail.contoso.com**, **login.contoso.com** oder **www.contso.com** verarbeiten würde.

> [AZURE.NOTE]
> Da ein A-Datensatz einer statischen IP-Adresse zugeordnet ist, kann er Änderungen an der IP-Adresse des Cloud-Diensts nicht automatisch auflösen. Die von Ihrem Clouddienst verwendete IP-Adresse wird zum ersten Mal zugewiesen, wenn Sie sie an einem leeren Steckplatz bereitstellen (entweder Produktion oder Staging). Wenn Sie die Bereitstellung für den Steckplatz löschen, wird die IP-Adresse von Azure freigegeben und zukünftigen Bereitstellungen an dem Steckplatz wird eine neue IP-Adresse zugewiesen.
> 
> Die IP-Adresse eines bestimmten Bereitstellungs-Steckplatzes (Produktion oder Staging) wird beim Austausch von Staging- und Produktionsbereitstellung oder beim Durchführen eines direkten Upgrades einer vorhandenen Bereitstellung beibehalten. Weitere Informationen zum Durchführen dieser Aktionen finden Sie unter [Gewusst wie: Verwalten von Clouddiensten].(../cloud-services-how-to-manage/).


<h2><a name="add-cname"></a>Hinzufügen eines CNAME-Datensatzes für Ihre benutzerdefinierte Domäne</h2>

Sie müssen einen neuen Eintrag zu der DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzufügen, um einen CNAME-Datensatz zu erstellen. Verwenden Sie hierzu die durch Ihre Registrierung bereitgestellten Tools. Die Methoden zum Festlegen eines CNAME-Datensatzes der verschiedenen Registrierungen sind zwar ähnlich, jedoch unterscheiden Sie sich auch in einigen Punkten. Die Konzepte sind allerdings gleich.

1. Verwenden Sie eine der folgenden Methoden, um den Domänennamen **.cloudapp.net** zu finden, der Ihrem Cloud-Dienst zugewiesen ist.

  * Melden Sie sich beim [Azure-Verwaltungsportal]an, wählen Sie Ihren Cloud-Dienst aus, wählen Sie **Dashboard**, und suchen Sie dann nach dem Eintrag **Website-URL** im Abschnitt **Schnelleinsicht**.

  		  ![Schnelleinsicht, die die Website-URL][csurl] anzeigt

  * Installieren und konfigurieren Sie [Azure Powershell](../install-configure-powershell/), und verwenden Sie dann den folgenden Befehl:

    Get-AzureDeployment -ServiceName yourservicename | Select Url

  Speichern Sie die im Domänennamen verwendete URL, die von der jeweiligen Methode zurückgegeben wurde. Diesen Namen benötigen Sie zum Erstellen eines CNAME-Datensatzes.

1.  Melden Sie sich bei der Website Ihrer DNS-Registrierungsstelle an, und öffnen Sie die Seite für die DNS-Verwaltung. Suchen Sie nach Links oder Bereichen auf der Website mit der Bezeichnung **Domänenname**, **DNS** oder **Namenserververwaltung**.

2.  Navigieren Sie nun zu dem Bereich, in dem Sie CNAMEs auswählen oder eingeben können. Möglicherweise müssen Sie den Datensatztyp in einem Dropdownmenü auswählen oder die Seite für erweiterte Einstellungen aufrufen. Suchen Sie nach den Wörtern **CNAME**, **Alias** oder **Unterdomänen**.

3.  Sie müssen auch den Domänen- oder Unterdomänenalias für den CNAME angeben, z. B. **www**, wenn Sie einen Alias für **www.customdomain.com** erstellen möchten. Wenn Sie einen Alias für die Stammdomäne erstellen möchten, kann sie als '**@**'-Symbol in den DNS-Tools Ihrer Registrierungsstelle aufgeführt sein.

4. Anschließend müssen Sie einen kanonischen Hostnamen angeben, der in diesem Fall die Domäne **cloudapp.net** Ihrer Anwendung ist.

Beispielsweise leitet der folgende CNAME-Datensatz sämtlichen Datenverkehr von **www.contoso.com** nach **contoso.cloudapp.net**, weiter, den benutzerdefinierten Domänennamen Ihrer bereitgestellten Anwendung:

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
(contoso.cloudapp.net) angezeigt. Also ist der Weiterleitungsprozess für den
Endbenutzer nicht sichtbar.

> [AZURE.NOTE]
> Das obige Beispiel gilt nur für Datenverkehr an der Unterdomäne <strong>www</strong>. Da Sie keine Platzhalter mit CNAME-Datensätzen verwenden können, müssen Sie einen CNAME für jede Domäne/Unterdomäne erstellen. Wenn Sie Datenverkehr von Unterdomänen wie z. B. *.contoso.com an Ihre cloudapp.net-Adresse weiterleiten möchten, können Sie einen <strong>URL-Umleitungseintrag</strong> oder <strong>URL-Weiterleitungseintrag</strong> in Ihren DNS-Einstellungen konfigurieren oder einen A-Datensatz erstellen.


<h2><a name="add-aname"></a>Hinzufügen eines A-Datensatzes für Ihre benutzerdefinierte Domäne</h2>

Sie müssen zunächst die virtuelle IP-Adresse Ihres Clouddiensts ermitteln, um einen A-Datensatz zu erstellen. Fügen Sie dann einen neuen Eintrag zu der DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzu. Verwenden Sie hierzu die durch Ihre Registrierung bereitgestellten Tools. Die Methoden zum Festlegen eines A-Datensatzes der verschiedenen Registrierungen sind zwar ähnlich, jedoch unterscheiden Sie sich auch in einigen Punkten. Die Konzepte sind allerdings gleich.

1. Verwenden Sie eine der folgenden Methoden, um die IP-Adresse Ihres Clouddiensts zu ermitteln.

  * Melden Sie sich beim [Azure-Verwaltungsportal]an, wählen Sie Ihren Cloud-Dienst aus, wählen Sie **Dashboard**, und suchen Sie dann nach dem Eintrag **Öffentliche virtuelle IP-Adresse (VIP)** im Abschnitt **Schnelleinsicht**.

   		 ![Schnelleinsicht, die die VIP][VIP] anzeigt

  * Installieren und konfigurieren Sie [Azure Powershell](../install-configure-powershell/), und verwenden Sie dann den folgenden Befehl:

      get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip

    Falls Sie Ihrem Clouddienst mehrere Endpunkte zugewiesen haben, erhalten Sie mehrere Zeilen mit der IP-Adresse. Alle Zeilen sollten jedoch dieselbe Adresse anzeigen.

  Speichern Sie die IP-Adresse, da Sie sie zum Erstellen eines A-Datensatzes benötigen.

1.  Melden Sie sich bei der Website Ihrer DNS-Registrierungsstelle an, und öffnen Sie die Seite für die DNS-Verwaltung. Suchen Sie nach Links oder Bereichen auf der Website mit der Bezeichnung **Domänenname**, **DNS** oder **Namenserververwaltung**.

2.  Navigieren Sie nun zu dem Bereich, in dem Sie A-Datensätze auswählen oder eingeben können. Möglicherweise müssen Sie den Datensatztyp in einem Dropdownmenü auswählen oder die Seite für erweiterte Einstellungen aufrufen.

3. Wählen Sie die Domäne oder Unterdomäne aus, die diesen A-Datensatz verwenden wird, oder geben Sie diese ein. Wählen Sie z. B. **www** aus, wenn Sie einen Alias für **www.customdomain.com** erstellen möchten. Wenn Sie einen Platzhaltereintrag für alle Unterdomänen erstellen möchten, geben Sie '__*__'. ein.Dies deckt alle Unterdomänen wie z. B. **mail.customdomain.com**, **login.customdomain.com** und **www.customdomain.com** ab.

  Wenn Sie einen A-Datensatz für die Stammdomäne erstellen möchten, kann sie als '**@**'-Symbol in den DNS-Tools Ihrer Registrierungsstelle aufgeführt sein.

4. Geben Sie die IP-Adresse Ihres Clouddiensts in das angegebene Feld ein. So wird der im A-Datensatz verwendete Domäneneintrag der IP-Adresse Ihrer Clouddienstbereitstellung zugewiesen.

Beispielsweise leitet der folgende A-Datensatz sämtlichen Datenverkehr von **www.contoso.com** nach **137.135.70.239**, weiter, der IP-Adresse Ihrer bereitgestellten Anwendung:

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

-   [Gewusst wie: Verwalten von Clouddiensten](../cloud-services-how-to-manage/)
-   [Zuordnen von CDN-Inhalt (Content Delivery Network) zu einer benutzerdefinierten Domäne][]

  [Bereitstellen der Anwendung in einer benutzerdefinierten Domäne]: #access-app
  [Hinzufügen eines CNAME-Datensatzes für Ihre benutzerdefinierte Domäne]: #add-cname
  [Bereitstellen Ihrer Daten in einer benutzerdefinierten Domäne]: #access-data
  [VIP-Austausch]: http://msdn.microsoft.com/de-de/library/ee517253.aspx
  [Erstellen eines CNAME-Datensatzes, der die Unterdomäne mit dem Speicherkonto verknüpft]: #create-cname
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com
  [Benutzerdefinierte Domäne überprüfen (Dialogfeld)]: http://i.msdn.microsoft.com/dynimg/IC544437.jpg
  [Zuordnen von CDN-Inhalt (Content Delivery Network) zu einer benutzerdefinierten Domäne]: http://msdn.microsoft.com/de-de/library/windowsazure/gg680307.aspx
  [VIP]: ./media/custom-dns/csvip.png
  [CSURL]: ./media/custom-dns/csurl.png<!--HONumber=42-->
