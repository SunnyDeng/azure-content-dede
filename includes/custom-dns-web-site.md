#Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website

Wenn Sie eine Website erstellen, stellt Azure ein Unterdomäne für die Domäne "azurewebsites.net" bereit, sodass Benutzer auf Ihre Website über eine URL wie http://&lt;mysite>.azurewebsites.net zugreifen können. Wenn Sie für Ihre Websites jedoch den Freigabe- oder Standardmodus festlegen, können Sie Ihre Website Ihrem eigenen Domänennamen zuordnen.

Optional können Sie mit Azure Traffic Manager einen Lastenausgleich für den auf Ihrer Website eingehenden Datenverkehr durchführen. Weitere Informationen zur Verwendung von Traffic Manager mit Websites finden Sie unter [Steuern des Azure-Websites-Verkehrs mit Azure Traffic Manager][trafficmanager].

> [WACOM.NOTE] Die Vorgehensweisen in dieser Aufgabe gelten für Azure-Websites. Informationen zu Cloud-Diensten finden Sie unter <a href="/de-de/develop/net/common-tasks/custom-dns/">Configuring a Custom Domain Name in Azure</a> (Konfigurieren eines benutzerdefinierten Domänennamens in Azure, in englischer Sprache).

> [WACOM.NOTE] Bei den Schritten dieser Aufgabe müssen Sie Ihre Websites für Freigabe- oder Standardmodus konfigurieren. Davon hängt möglicherweise ab, wie Ihr Abonnement abgerechnet wird. Weitere Informationen finden Sie in den <a href="/de-de/pricing/details/web-sites/">Preisdetails zu Websites</a>.

Themen in diesem Artikel:

-   [Informationen zu CNAME- und A-Datensätzen](#understanding-records)
-   [Konfigurieren Ihrer Websites für den Modus "Freigegeben" oder "Standard"](#bkmk_configsharedmode)
-   [Hinzufügen Ihrer Websites zu Traffic Manager](#trafficmanager)
-   [Hinzufügen eines CNAME-Datensatzes für Ihre benutzerdefinierte Domäne](#bkmk_configurecname)
-   [Hinzufügen eines A-Datensatzes für Ihre benutzerdefinierte Domäne](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>Informationen zu CNAME- und A-Datensätzen</h2>

Mit CNAME-Datensätzen (oder Aliasdatensätzen) und A-Datensätzen können Sie einen Domänennamen einer Website zuweisen, beide funktionieren jedoch unterschiedlich.

###CNAME- oder Alias-Eintrag

Ein CNAME-Datensatz weist eine spezifische Domäne, beispielsweise **contoso.com** oder **www.contoso.com**, einem kanonischen Domänennamen zu. In diesem Falle ist der kanonische Domänenname entweder der Domänenname **&lt;myapp>.azurewebsites.net** Ihrer Azure-Website oder der Domänenname **&lt;myapp>.trafficmgr.com** Ihres Traffic Manager-Profils. Sobald der CNAME erstellt ist, erstellt er einen Alias für den Domänennamen **&lt;myapp>.azurewebsites.net** oder **&lt;myapp>.trafficmgr.com**. Der CNAME-Eintrag wird automatisch zur IP-Adresse Ihres **&lt;myapp>.azurewebsites.net**- oder **&lt;myapp>.trafficmgr.com**-Domänennamens aufgelöst. Wenn sich also die IP-Adresse der Website ändert, müssen Sie nichts weiter tun.

> [WACOM.NOTE] Bei einigen Domänenregistrierungen können Sie Unterdomänen nur mit einem CNAME-Datensatz wie www.contoso.com zuweisen und nicht mit einem Stammnamen wie contoso.com. Weitere Informationen zu CNAME-Einträgen finden Sie in der von Ihrer Domänenregistrierungsstelle zur Verfügung gestellten Dokumentation, <a href="http://en.wikipedia.org/wiki/CNAME_record">im Wikipedia-Artikel "CNAME Resource Record"</a> oder im Dokument <a href="http://tools.ietf.org/html/rfc1035">IETF Domain Names - Implementation and Specification</a> (Domänennamen - Implementierung und Spezifizierung, in englischer Sprache).

###A-Eintrag

Ein A-Eintrag weist eine Domäne wie **contoso.com**, **www.contoso.com** oder eine Domäne mit Platzhalter wie **\*.contoso.com** einer IP-Adresse zu. Im Falle einer Azure-Website entweder die virtuelle IP-Adresse des Dienstes oder eine spezifische, für die Website erworbene IP-Adresse. Der Vorteil eines A-Datensatzes gegenüber einem CNAME-Datensatz ist, dass Sie einen Eintrag mit einem Platzhalter verwenden können, beispielsweise ***.contoso.com**, der Anfragen für mehrere Unterdomänen verarbeiten kann, beispielsweise **mail.contoso.com**, **login.contoso.com** oder **www.contso.com**.

> [WACOM.NOTE] Da ein A-Datensatz einer statischen IP-Adresse zugewiesen ist, kann er nicht automatisch Änderungen an die IP-Adresse Ihrer Website übertragen. Eine IP-Adresse zur Verwendung mit A-Einträgen erhalten Sie, wenn Sie benutzerdefinierte Domänennameneinstellungen für Ihre Website konfigurieren.Dieser Wert kann sich jedoch ändern, wenn Sie Ihre Website löschen und neu erstellen oder den Websitemodus auf "Kostenlos" zurücksetzen.

> [WACOM.NOTE] A-Datensätze können nicht für einen Lastenausgleich mit Traffic Manager verwendet werden. Weitere Informationen finden Sie unter [Steuern des Azure-Websites-Verkehrs mit Azure Traffic Manager][trafficmanager].
 
<a name="bkmk_configsharedmode"></a><h2>Konfigurieren Ihrer Websites für den Modus "Freigegeben" oder "Standard"</h2>

Ein benutzerdefinierter Domänenname für eine Website kann nur in den Azure-Websites-Modi "Freigegeben" und "Standard" festgelegt werden. Bevor Sie den Modus einer Website von "Kostenlos" in "Freigegeben" oder "Standard" ändern können, müssen Sie das für Ihr Abonnement geltende Ausgabenlimit aufheben. Weitere Informationen zu den Preisen für Freigabe- und Standardmodus finden Sie in der [Preisübersicht][PricingDetails].

1. Öffnen Sie in Ihrem Browser das [Verwaltungsportal][portal].
2. Klicken Sie auf der Registerkarte **Websites** auf den Namen Ihrer Website.

	![][standardmode1]

3. Klicken Sie auf die Registerkarte **SKALIEREN**.

	![][standardmode2]

	
4. Legen Sie im Abschnitt **Allgemein** den Websitemodus durch Klicken auf **FREIGEGEBEN** fest.

	![][standardmode3]

	> [WACOM.NOTE] Wenn Sie Traffic Manager mit dieser Website verwenden, müssen Sie den Modus "Standard" anstelle des Modus "Freigegeben" verwenden.

5. Klicken Sie auf **Speichern**.
6. Wenn Ihnen eine Eingabeaufforderung wegen höhere Kosten für den Freigabemodus (oder den Standardmodus, falls Sie Standard gewählt haben) angezeigt wird, klicken Sie auf **Ja**, wenn Sie zustimmen.

	<!--![][standardmode4]-->

	**Hinweis**<br/> 
	Wenn Sie eine Fehlermeldung erhalten, dass das Konfigurieren der Größenordnung für die Website mit dem angegebenen Namen fehlgeschlagen ist, können Sie über die Detailschaltfläche weitere Informationen anzeigen. 

<a name="trafficmanager"></a><h2>(Optional) Hinzufügen Ihrer Websites zu Traffic Manager</h2>

Gehen Sie folgendermaßen vor, um Ihre Website mit Traffic Manager zu verwenden.

1. Wenn Sie noch kein Traffic Manager-Profil haben, verwenden Sie die Informationen unter [Erstellen eines Traffic Manager-Profils mithilfe von "Schnellerfassung"][createprofile], um ein Profil zu erstellen. Bitte beachten Sie, dass mit Ihrem Traffic Manager-Profil der Domänenname **.trafficmgr.com** verknüpft ist. Dieser wird in einem späteren Schritt verwendet.

2. Verwenden Sie die Informationen unter [Hinzufügen und Löschen von Endpunkten][addendpoint], um dem Traffic Manager-Profil die Website als Endpunkt hinzuzufügen.

	> [WACOM.NOTE] Wenn Ihre Website beim Hinzufügen eines Endpunkts nicht aufgeführt ist, überprüfen Sie, dass sie für den Standardmodus konfiguriert wurde. Sie müssen für Ihre Website den Standardmodus verwenden, damit Sie mit Traffic Manager arbeiten können.

3. Melden Sie sich bei der Website Ihrer DNS-Registrierungsstelle an, und öffnen Sie die Seite für die DNS-Verwaltung. Suchen Sie nach Links oder Bereichen der Site, die als **Domänenname**, **DNS** oder **Namenserververwaltung** bezeichnet werden.

4. Navigieren Sie nun zu dem Bereich, in dem Sie CNAME-Datensätze auswählen oder eingeben können. Möglicherweise müssen Sie den Datensatztyp in einem Dropdownmenü auswählen oder die Seite für erweiterte Einstellungen aufrufen. Suchen Sie nach den Wörtern **CNAME**, **Alias** oder **Unterdomänen**.

5. Sie müssen auch den Domänen- oder Unterdomänenalias für den CNAME angeben. Beispielsweise **www**, wenn Sie einen Alias für **www.customdomain.com** erstellen möchten.

5. Sie müssen auch einen Hostnamen angeben, der der kanonische Domänenname dieses CNAME-Alias ist. Dies ist der **.trafficmgr.com**-Name für Ihre Website.

Der folgende CNAME-Datensatz leitet zum Beispiel den gesamten Datenverkehr von **www.contoso.com** an **contoso.trafficmgr.com** weiter, dem Domänennamen einer Website:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Hostname/Unterdomäne</strong></td>
<td><strong>Kanonische Domäne</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.trafficmgr.com</td>
</tr>
</table>

Einem Besucher von **www.contoso.com** wird niemals der tatsächliche Host angezeigt.
(contoso.azurewebsite.net) angezeigt, sodass der Weiterleitungsprozess für den Endbenutzer nicht sichtbar ist.

> [WACOM.NOTE] Wenn Sie Traffic Manager für eine Website verwenden, müssen Sie die Schritte in den folgenden Abschnitten "**Hinzufügen eines CNAME-Datensatzes für Ihre benutzerdefinierte Domäne**" und "**Hinzufügen eines A-Datensatzes für Ihre benutzerdefinierte Domäne**" nicht ausführen. Der in den vorhergehenden Schritten erstellte CNAME-Datensatz leitet eingehenden Datenverkehr an Traffic Manager weiter, der ihn wiederum an die Website-Endpunkte weiterleitet.

<a name="bkmk_configurecname"></a><h2>Hinzufügen eines CNAME-Datensatzes für Ihre benutzerdefinierte Domäne</h2>

Sie müssen einen neuen Eintrag zu der DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzufügen, um einen CNAME-Datensatz zu erstellen. Verwenden Sie hierzu die durch Ihre Registrierung bereitgestellten Tools. Die Methoden zum Festlegen eines CNAME-Datensatzes der verschiedenen Registrierungen sind zwar ähnlich, jedoch unterscheiden Sie sich auch in einigen Punkten. Die Konzepte sind allerdings gleich.

1. Verwenden Sie eine dieser Methoden, um den **.azurewebsite.net**-Domänennamen zu ermitteln, der Ihrer Website zugewiesen ist.

	* Melden Sie sich beim [Azure-Verwaltungsportal][portal] an, wählen Sie Ihre Website und dann **Dashboard** aus, und navigieren Sie anschließend zum Eintrag **Site-URL** im Abschnitt **Auf einen Blick**.

	* Installieren und konfigurieren Sie [Azure Powershell](/de-de/manage/install-and-configure-windows-powershell/), und verwenden Sie dann den folgenden Befehl:

			get-azurewebsite yoursitename | select hostnames

	* Installieren und konfigurieren Sie die [Azure plattformübergreifende Befehlszeilenschnittstelle](/de-de/manage/install-and-configure-cli/) (Inhalt in englischer Sprache), und verwenden Sie dann den folgenden Befehl:

			azure site domain list yoursitename

	Speichern Sie diesen **.azurewebsite.net**-Namen, da er in den folgenden Schritten verwendet wird.

3. Melden Sie sich bei der Website Ihrer DNS-Registrierungsstelle an, und öffnen Sie die Seite für die DNS-Verwaltung. Suchen Sie nach Links oder Bereichen der Site, die als **Domänenname**, **DNS** oder **Namenserververwaltung** bezeichnet werden.

4. Navigieren Sie nun zu dem Bereich, in dem Sie CNAME-Datensätze auswählen oder eingeben können. Möglicherweise müssen Sie den Datensatztyp in einem Dropdownmenü auswählen oder die Seite für erweiterte Einstellungen aufrufen. Suchen Sie nach den Wörtern **CNAME**, **Alias** oder **Unterdomänen**.

5. Sie müssen auch den Domänen- oder Unterdomänenalias für den CNAME angeben. Beispielsweise **www**, wenn Sie einen Alias für **www.customdomain.com** erstellen möchten. Wenn Sie einen Alias für die Stammdomäne erstellen möchten, wird dieser möglicherweise als "**@**"-Zeichen in den DNS-Tools Ihrer Registrierung aufgeführt.

5. Sie müssen auch einen Hostnamen angeben, der der kanonische Domänenname dieses CNAME-Alias ist. Dies ist der **.azurewebsite.net**-Name für Ihre Website.

Der folgende CNAME-Datensatz leitet zum Beispiel den gesamten Datenverkehr von **www.contoso.com** an **contoso.azurewebsite.net** weiter, dem Domänennamen einer Website:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Hostname/Unterdomäne</strong></td>
<td><strong>Kanonische Domäne</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.azurewebsite.net</td>
</tr>
</table>

Einem Besucher von **www.contoso.com** wird niemals der tatsächliche Host angezeigt.
(contoso.azurewebsite.net) angezeigt, sodass der Weiterleitungsprozess für den
Endbenutzer nicht sichtbar ist.

> [WACOM.NOTE] Das oben genannte Beispiel gilt nur für Verkehr an der Unterdomäne __www__. Da Sie keine Platzhalter mit CNAME-Datensätzen verwenden können, müssen Sie einen CNAME für jede Domäne/Unterdomäne erstellen. Wenn Sie den Verkehr von Unterdomänen wie *.contoso.com an Ihre azurewebsite.net-Adresse weiterleiten möchten, können Sie in Ihren DNS-Einstellungen einen Eintrag für __URL-Umleitung__ oder __URL-Weiterleitung__ erstellen. Alternativ können Sie einen A-Datensatz erstellen.

> [WACOM.NOTE] Es kann einige Zeit dauern, bis Ihr CNAME über das DNS-System weitergegeben wurde. Der CNAME für die Website kann erst nach abgeschlossener CNAME-Verteilung eingestellt werden. Mithilfe eines Services wie <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> (in englischer Sprache) können Sie überprüfen, ob der CNAME verfügbar ist.

###Hinzufügen eines Domänennamens zu Ihrer Website

Nachdem der CNAME-Datensatz für den Domänennamen verteilt wurde, müssen Sie ihn Ihrer Website zuweisen. Sie können entweder die plattformübergreifende Azure-Befehlszeilenschnittstelle oder das Azure-Verwaltungsportal verwenden, um den durch den CNAME-Datensatz festgelegten benutzerdefinierten Domänennamen Ihrer Website hinzuzufügen.

**Hinzufügen eines Domänennamens mithilfe der Befehlszeilentools**

Installieren und konfigurieren Sie die [Azure plattformübergreifende Befehlszeilenschnittstelle](/de-de/manage/install-and-configure-cli/) (Inhalt in englischer Sprache), und verwenden Sie dann den folgenden Befehl:

	azure site domain add customdomain yoursitename

Der folgende Befehl fügt beispielsweise den benutzerdefinierten Domänennamen **www.contoso.com** der Website **contoso.azurewebsite.net** hinzu:

	azure site domain add www.contoso.com contoso

Mit dem folgenden Befehl können Sie überprüfen, ob der benutzerdefinierte Domänenname der Website hinzugefügt wurde:

	azure site domain list yoursitename

Die von diesem Befehl zurückgegebene Liste sollte den Domänennamen sowie den Standardeintrag **.azurewebsite.net** enthalten.

**Hinzufügen eines Domänennamens mithilfe des Azure Verwaltungsportals**

1. Öffnen Sie in Ihrem Browser das [Azure Verwaltungsportal][portal].

2. Klicken Sie auf der Registerkarte **Websites** auf den Namen Ihrer Site, wählen Sie dann **Dashboard** und am unteren Seitenrand **Domänen verwalten** aus.

	![][setcname2]

6. Geben Sie in das Textfeld **DOMÄNENNAMEN** den Domänennamen ein, den Sie konfiguriert haben. 

	![][setcname3]

6. Klicken Sie auf das Häkchen, um den Domänennamen zu akzeptieren.

Nach Abschluss der Konfiguration wird der benutzerdefinierte Domänenname im Abschnitt **Domänennamen** der Seite **Konfigurieren** Ihrer Website. 

<a name="bkmk_configurearecord"></a><h2>Hinzufügen eines A-Datensatzes für Ihre benutzerdefinierte Domäne</h2>

Sie müssen zunächst die IP-Adresse Ihrer Website ermitteln, um einen A-Datensatz zu erstellen. Fügen Sie dann einen Eintrag zu der DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzu. Verwenden Sie hierzu die durch Ihre Registrierung bereitgestellten Tools. Die Methoden zum Festlegen eines A-Datensatzes der verschiedenen Registrierungen sind zwar ähnlich, jedoch unterscheiden Sie sich auch in einigen Punkten. Die Konzepte sind allerdings gleich. Neben dem A-Datensatz müssen Sie auch einen CNAME-Datensatz erstellen, den Azure verwendet, um den A-Datensatz zu überprüfen.

1. Öffnen Sie in Ihrem Browser das [Azure Verwaltungsportal][portal].

2. Klicken Sie auf der Registerkarte **Websites** auf den Namen Ihrer Site, wählen Sie dann **Dashboard** und am unteren Bildschirmrand **Domänen verwalten** aus.

	![][setcname2]

5. Navigieren Sie im Dialogfeld **Benutzerdefinierte Domänen verwalten** zu **Beim Konfigurieren von A-Datensätzen zu verwendende IP-Adresse**. Kopieren Sie die IP-Adresse. Diese wird zum Erstellen des A-Datensatzes verwendet.

5. Notieren Sie den awverify-Domänennamen am Ende des Texts, oben im Dialogfeld **Benutzerdefinierte Domänen verwalten**, und überprüfen Sie diesen. Er sollte **awverify.mysite.azurewebsites.net** lauten, wobei **mysite** der Name Ihrer Website ist. Kopieren Sie diesen, da das der Domänenname ist, der zum Erstellen der Überprüfung des CNAME-Datensatzes verwendet wird.

6. Melden Sie sich bei der Website Ihrer DNS-Registrierungsstelle an, und öffnen Sie die Seite für die DNS-Verwaltung. Suchen Sie nach Links oder Bereichen der Site, die als **Domänenname**, **DNS** oder **Namenserververwaltung** bezeichnet werden.

6. Navigieren Sie zu dem Bereich, in dem Sie A- und CNAME-Datensätze auswählen oder eingeben können. Möglicherweise müssen Sie den Datensatztyp in einem Dropdownmenü auswählen oder die Seite für erweiterte Einstellungen aufrufen.

7. Führen Sie die folgenden Schritte aus, um einen A-Datensatz zu erstellen:

	1. Wählen Sie die Domäne oder Unterdomäne aus, die den A-Datensatz verwenden wird, oder geben Sie diese ein. Wählen Sie beispielsweise **www**, wenn Sie einen Alias für **www.customdomain.com** erstellen möchten. Wenn Sie einen Platzhaltereintrag für alle Unterdomänen erstellen möchten, geben Sie "__*__" ein. Dieser Eintrag deckt alle Unterdomänen ab wie **mail.customdomain.com**, **login.customdomain.com** und **www.customdomain.com**.

		Wenn Sie einen A-Datensatz für die Stammdomäne erstellen möchten, wird dieser möglicherweise als "**@**"-Zeichen in den DNS-Tools Ihrer Registrierung aufgeführt.

	2. Geben Sie die IP-Adresse Ihres Clouddiensts in das angegebene Feld ein. So wird der im A-Datensatz verwendete Domäneneintrag der IP-Adresse Ihrer Clouddienstbereitstellung zugewiesen.

		Der folgende A-Datensatz leitet zum Beispiel den gesamten Verkehr von **contoso.com** an **137.135.70.239** weiter. Das ist die IP-Adresse unserer bereitgestellten Anwendung:

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

		Dieses Beispiel zeigt das Erstellen eines A-Datensatzes für die Stammdomäne. Wenn Sie einen Platzhaltereintrag erstellen möchten, der alle Unterdomänen abdeckt, würden Sie "__*__" als Unterdomäne eingeben.

7. Erstellen Sie dann einen CNAME-Datensatz, der einen Alias von **awverify** besitzt und eine kanonische Domäne von **awverify.mysite.azurewebsites.net**, die Sie zuvor erhalten haben.

	> [WACOM.NOTE] Während awverify zwar für manche Registrierungen funktioniert, benötigen andere hingegen möglicherweise den vollständigen Alias-Domänenname von awverify.www.customdomainname.com oder awverify.customdomainname.com.

	Das folgende Beispiel erstellt einen CNAME-Datensatz, den Azure zur Überprüfung einer A-Datensatzkonfiguration verwenden kann.

	<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
	<tr>
	<td><strong>Alias/Hostname/Unterdomäne</strong></td>
	<td><strong>Kanonische Domäne</strong></td>
	</tr>
	<tr>
	<td>awverify</td>
	<td>awverify.contoso.azurewebsites.net</td>
	</tr>
	</table>

> [WACOM.NOTE] Es kann einige Zeit dauern, bis der awverify-CNAME über das DNS-System weitergegeben wurde. Sie können den durch den A-Datensatz für die Website festgelegten benutzerdefinierten Domänennamen erst einstellen, wenn die Verteilung des awverify-CNAME abgeschlossen ist. Mithilfe eines Services wie <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> (in englischer Sprache) können Sie überprüfen, ob der CNAME verfügbar ist.

###Hinzufügen eines Domänennamens zu Ihrer Website

Nachdem der **awverify**-CNAME-Datensatz für den Domänennamen verteilt wurde, können Sie die durch den A-Datensatz festgelegte benutzerdefinierte Domäne Ihrer Website zuweisen. Sie können entweder die plattformübergreifende Azure-Befehlszeilenschnittstelle oder das Azure-Verwaltungsportal verwenden, um den durch den A-Datensatz festgelegten benutzerdefinierten Domänennamen Ihrer Website hinzuzufügen.

**Hinzufügen eines Domänennamens mithilfe der Befehlszeilentools**

Installieren und konfigurieren Sie die [Azure plattformübergreifende Befehlszeilenschnittstelle](/de-de/manage/install-and-configure-cli/) (Inhalt in englischer Sprache), und verwenden Sie dann den folgenden Befehl:

	azure site domain add customdomain yoursitename

Der folgende Befehl fügt beispielsweise den benutzerdefinierten Domänennamen **contoso.com** der Website **contoso.azurewebsite.net** hinzu:

	azure site domain add contoso.com contoso

Mit dem folgenden Befehl können Sie überprüfen, ob der benutzerdefinierte Domänenname der Website hinzugefügt wurde:

	azure site domain list yoursitename

Die von diesem Befehl zurückgegebene Liste sollte den Domänennamen sowie den Standardeintrag **.azurewebsite.net** enthalten.

**Hinzufügen eines Domänennamens mithilfe des Azure Verwaltungsportals**

1. Öffnen Sie in Ihrem Browser das [Azure Verwaltungsportal][portal].

2. Klicken Sie auf der Registerkarte **Websites** auf den Namen Ihrer Site, wählen Sie dann **Dashboard** und am unteren Seitenrand **Domänen verwalten** aus.

	![][setcname2]

6. Geben Sie in das Textfeld **DOMÄNENNAMEN** den Domänennamen ein, den Sie konfiguriert haben. 

	![][setcname3]

6. Klicken Sie auf das Häkchen, um den Domänennamen zu akzeptieren.

Nach Abschluss der Konfiguration wird der benutzerdefinierte Domänenname im Abschnitt **Domänennamen** der Seite **Konfigurieren** Ihrer Website.

> [WACOM.NOTE] Nachdem Sie den durch den A-Datensatz festgelegten benutzerdefinierten Domänennamen Ihrer Website hinzugefügt haben, können Sie den awverify-CNAME-Datensatz mit den von Ihrer Registrierungsstelle zur Verfügung gestellten Tools entfernen. Wenn Sie jedoch in Zukunft einen weiteren A-Datensatz hinzufügen möchten, müssen Sie den awverify-Datensatz neu erstellen, damit Sie den durch den neuen A-Datensatz festgelegten neuen Domänennamen der Website zuweisen können.

## Nächste Schritte

-   [How to manage web sites (Verwalten von Websites, in englischer Sprache)](/de-de/manage/services/web-sites/how-to-manage-websites/)

-   [Configure an SSL certificate for Web Sites (Konfigurieren eines SSL-Zertifikats für Web Sites, in englischer Sprache)](/de-de/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Konfigurieren Ihrer Websites für den Freigabemodus]: #bkmk_configsharedmode
[Konfigurieren des CNAME bei der Domänenregistrierungsstelle]: #bkmk_configurecname
[Konfigurieren eines CNAME-Überprüfungsdatensatzes bei der Domänenregistrierungsstelle]: #bkmk_configurecname
[Konfigurieren eines A-Datensatzes für den Domänennamen]:#bkmk_configurearecord
[Festlegen des Domänennamens im Verwaltungsportal]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /de-de/pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../custom-dns/
[trafficmanager]: /de-de/documentation/articles/web-sites-traffic-manager/
[addendpoint]: http://msdn.microsoft.com/library/windowsazure/hh744839.aspx
[createprofile]: http://msdn.microsoft.com/library/windowsazure/dn339012.aspx

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png 


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png
