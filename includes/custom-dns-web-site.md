
# Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website

Wenn Sie eine Website erstellen, bietet Azure eine benutzerfreundliche Unterdomäne in der Domäne azurewebsites.net, damit Ihre Benutzer über eine URL wie http://<mysite>.azurewebsites.net auf Ihre Website zugreifen können. Wenn Sie Ihre Website jedoch für den Freigabe- oder Standardmodus konfigurieren möchten, können Sie Ihre Website Ihrem eigenen Domänennamen zuweisen.

Optional können Sie Azure Traffic Manager verwenden, um einen Lastenausgleich des auf Ihrer Website eingehenden Verkehrs herzustellen. Weitere Informationen zur Verwendung von Traffic Manager für Websites finden Sie unter [Controlling Azure Web Sites Traffic with Azure Traffic Manager](/de-de/documentation/articles/web-sites-traffic-manager/)(Kontrolle des Verkehrs auf einer Azure-Website mithilfe des Azure Traffic Manager, in englischer Sprache).

> [WACOM.NOTE] Die Vorgehensweisen in dieser Aufgabe gelten für
> Azure-Web Sites. Informationen zu Clouddiensten finden Sie unter
> [Configuring a Custom Domain Name in Azure][1]
> (Konfigurieren eines benutzerdefinierten Domänennamens in Azure, in
> englischer Sprache).

> [WACOM.NOTE] Bei den Schritten dieser Aufgabe müssen Sie Ihre Websites
> für Freigabe- oder Standardmodus konfigurieren. Davon hängt
> möglicherweise ab, wie Ihr Abonnement abgerechnet wird. Weitere
> Informationen finden Sie unter [Web Sites -- Preisdetails][2]

Themen in diesem Artikel:

* [Informationen zu CNAME- und A-Datensätzen](#understanding-records)
* [Konfigurieren Ihrer Websites für den Freigabe- oder
  Standardmodus](#bkmk_configsharedmode)
* [Hinzufügen Ihrer Websites zu Traffic Manager](#trafficmanager)
* [Hinzufügen eines CNAME-Datensatzes für Ihre benutzerdefinierte
  Domäne](#bkmk_configurecname)
* [Hinzufügen eines A-Datensatzes zu Ihrer benutzerdefinierten
  Domäne](#bkmk_configurearecord)

<h2><a name="understanding-records" ></a>Informationen zu CNAME- und A-Datensätzen</h2>


Mit CNAME- (oder Aliasdatensätzen) und A-Datensätzen können Sie einen Domänennamen einer Website zuweisen, beide funktionieren jedoch unterschiedlich.

### CNAME- oder Aliasdatensatz

Ein CNAME-Datensatz weist eine *spezifische* Domäne, beispielsweise **contoso.com** oder **www.contoso.com**, zu einem kanonischen Domänennamen zu. In diesem Falle ist der kanonische Domänenname entweder der Domänenname **<myapp>.azurewebsites.net** Ihrer Azure-Website oder der Domänenname **<myapp>.trafficmgr.com** Ihres Traffic Manager-Profils. Sobald der CNAME erstellt ist, erstellt er einen Alias für den Domänennamen **<myapp>.azurewebsites.net** oder **<myapp>.trafficmgr.com**. Der CNAME-Datensatz wird automatisch
an die IP-Adresse Ihres Service **<myapp>.azurewebsites.net** oder **<myapp>.trafficmgr.com** übertragen. Wenn sich die IP-Adresse der Website ändert, müssen Sie nichts weiter tun.

> [WACOM.NOTE] Bei einigen Domänenregistrierungen können Sie
> Unterdomänen nur mit einem CNAME-Datensatz wie www.contoso.com
> zuweisen und nicht mit einem Stammnamen wie contoso.com. Weitere
> Informationen zu CNAME-Datensätzen finden Sie in der durch Ihre
> Registrierung zur Verfügung gestellte Dokumentation, [dem
> Wikipedia-Eintrag "CNAME Resource Record"][3]
> oder dem Dokument [IETF Domain Names - Implementation and
> Specification][4] (IEFT-Domännamen --
> Implementierung und Spezifizierung, in englischer Sprache).

### A-Datensatz

Ein A-Datensatz weist eine Domäne wie **contoso.com** oder **www.contoso.com** *oder eine Domäne mit Platzhalter* wie ***.contoso.com** einer IP-Adresse zu. Im Falle einer Azure-Website ist dies entweder die virtuelle IP des Dienstes oder eine bestimmte IP-Adresse, die Sie für Ihre Website erworben haben. Der Vorteil eines A-Datensatzes gegenüber einem CNAME-Datensatz ist, dass Sie einen Eintrag mit einem Platzhalter verwenden können, beispielsweise ***.contoso.com**, der Anfragen für mehrere Unterdomänen verarbeiten kann, beispielsweise **mail.contoso.com**, **login.contoso.com** oder **www.contso.com**.

> [WACOM.NOTE] Da ein A-Datensatz einer statischen IP-Adresse zugewiesen
> ist, kann er nicht automatisch Änderungen an die IP-Adresse Ihrer
> Website übertragen. Eine IP-Adresse zur Verwendung mit A-Datensätzen
> erhalten Sie, wenn Sie benutzerdefinierte Domänennameneinstellungen
> für Ihre Website konfigurieren. Dieser Wert kann sich ändern, wenn Sie
> Ihre Website löschen und neu erstellen, oder den Website-Modus zurück
> zu frei ändern.

> [WACOM.NOTE] A-Datensätze können nicht für einen Lastenausgleich mit
> Traffic Manager verwendet werden. Weitere Informationen erhalten Sie
> unter [Controlling Azure Web Sites Traffic with Azure Traffic
> Manager](/de-de/documentation/articles/web-sites-traffic-manager/)
> (Kontrolle des Verkehrs auf einer Azure-Website mithilfe des Azure
> Traffic Manager, in englischer Sprache).

<a name="bkmk_configsharedmode" ></a><h2>Konfigurieren Ihrer Websites für den Freigabe-
oder Standardmodus</h2>

Nur für die Freigabe- und Standardmodi für Azure Websites kann ein benutzerdefinierter Domänenname festgelegt werden. Bevor Sie den Modus einer Website vom freien Modus zum Freigabe- oder Standardmodus wechseln, müssen Sie zunächst die für Ihr Abonnement geltenden Kostenbeschränkungen aufheben. Weitere Informationen zu den Preisen für Freigabe- und Standardmodus finden Sie in der [Preisübersicht][5].

1.  Öffnen Sie das [Verwaltungsportal][6] in Ihrem Browser. 2.  Klicken Sie auf der Registerkarte **Web Sites** auf den Namen Ihrer
    Site.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-1.png)

3.  Klicken Sie auf die Registerkarte **SCALE**.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-2.png)

4.  Legen Sie im **allgemeinen** Bereich den Websitemodus fest, indem Sie auf **SHARED** klicken.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-3.png)


   
    > [WACOM.NOTE] Wenn Sie Traffic Manager mit dieser Website
    > verwenden, müssen Sie den Standardmodus anstelle des Freigabemodus
    > verwenden.

5.  Klicken Sie auf **Speichern**. 6.  Wenn Ihnen eine Eingabeaufforderung wegen höhere Kosten für den    Freigabemodus (oder den Standardmodus, falls Sie Standard gewählt haben) angezeigt wird, klicken Sie auf **Ja**, wenn Sie zustimmen.
    
    <!--![][standardmode4]-->
    
    **Hinweis**  
     Wenn Sie eine Fehlermeldung erhalten, dass das Konfigurieren der Größenordnung für die Website mit dem     angegebenen Namen fehlgeschlagen ist, können Sie über die Detailschaltfläche weitere Informationen anzeigen.

<a name="trafficmanager" ></a><h2>(Optional) Hinzufügen Ihrer Websites zu Traffic
Manager</h2>

Gehen Sie folgendermaßen vor, um Ihre Website mit Traffic Manager zu verwenden.

1.  Falls Sie nicht bereits ein Traffic Manager-Profil besitzen, erstellen Sie eines mithilfe der Angaben unter [Erstellen eines Traffic Manager-Profils mithilfe der Schnellerfassung][7]. Bitte beachten Sie, dass mit Ihrem Traffic Manager-Profil der Domänenname **.trafficmgr.com** verknüpft ist. Dieser wird in einem späteren     Schritt verwendet.

2.  Fügen Sie mithilfe der Angaben unter [Hinzufügen und Löschen von Endpunkten][8] Ihre Website als Endpunkt in ihrem Traffic Manager-Profil hinzu.


   
    > [WACOM.NOTE] Wenn Ihre Website beim Hinzufügen eines Endpunktes
    > nicht aufgelistet ist, prüfen Sie, ob sie für den Standardmodus
    > konfiguriert ist. Um mit Traffic Manager arbeiten zu können,
    > müssen Sie für Ihre Website den Standardmodus verwenden.

3.  Melden Sie sich bei der Website Ihrer DNS-Registrierung an, und rufen Sie die Seite zur DNS-Verwaltung auf. Suchen Sie nach Links oder Bereichen der Site, die als **Domänenname**, **DNS** oder **Namenserververwaltung** bezeichnet werden.

4.  Navigieren Sie nun zu dem Bereich, in dem Sie CNAME-Datensätze auswählen oder eingeben können. Möglicherweise müssen Sie den Datensatztyp in einem Dropdownmenü auswählen oder die Seite für erweiterte Einstellungen aufrufen. Suchen Sie nach den Wörtern **CNAME**, **Alias** oder **Unterdomänen**.

5.  Sie müssen auch den Domänen- oder Unterdomänenalias für den CNAME angeben. Beispielsweise **www**, wenn Sie einen Alias für **www.customdomain.com** erstellen möchten.

6.  Sie müssen auch einen Hostnamen angeben, der der kanonische Domänenname dieses CNAME-Alias ist. Dies ist der **.trafficmgr.com**-Name für Ihre Website.

Der folgende CNAME-Datensatz leitet zum Beispiel den gesamten Verkehr von **www.contoso.com** an **contoso.trafficmgr.com** weiter. Das ist der benutzerdefinierte Domänenname einer Website:

<table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Hostname/Unterdomäne</strong>
</td>

<td><strong>Kanonische Domäne</strong>
</td>

</tr>

<tr>
<td>www</td>

<td>contoso.trafficmgr.com</td>

</tr>

</table>

Einem Besucher von **www.contoso.com** wird niemals der wirkliche Host (contoso.azurewebsite.net) angezeigt. Die Weiterleitung ist also für den Endbenutzer nicht sichtbar.

> [WACOM.NOTE] Wenn Sie Traffic Manager für eine Website verwenden,
> müssen Sie die Schritte in den folgenden Abschnitten '**Hinzufügen
> eines CNAME-Datensatzes für Ihre benutzerdefinierte Domäne**' und
> '**Hinzufügen eines A-Datensatzes zu Ihrer benutzerdefinierten
> Domäne** nicht ausführen'. Der in den vorhergehenden Schritten
> erstelle CNAME-Datensatz leitet eingehenden Verkehr an Traffic Manager
> weiter. Von hier aus wird der Verkehr anschließend an die
> Website-Endpunkte weitergeleitet.

<a name="bkmk_configurecname" ></a><h2>Hinzufügen eines CNAME-Datensatzes für Ihre benutzerdefinierte Domäne</h2>

Sie müssen einen neuen Eintrag zu der DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzufügen, um einen CNAME-Datensatz zu erstellen. Verwenden Sie hierzu die durch Ihre Registrierung bereitgestellten Tools. Die Methoden zum Festlegen eines CNAME-Datensatzes der verschiedenen Registrierungen sind zwar ähnlich, jedoch unterscheiden Sie sich auch in einigen Punkten. Die Konzepte sind allerdings gleich.

1.  Verwenden Sie eine dieser Methoden, um den **.azurewebsite.net**-Domänennamen zu ermitteln, der Ihrer Website zugewiesen ist.
    
    * Melden Sie sich beim [Azure Verwaltungsportal][6] an, wählen Sie Ihre Website aus, wählen Sie **Dashboard** aus, und navigieren Sie dann zum Eintrag **Site URL** in der **Schnelleinsicht**.
    
    * Installieren und konfigurieren Sie [Azure PowerShell][9], und verwenden Sie dann den folgenden Befehl:
      
            get-azurewebsite yoursitename | select hostnames
    
    * Installieren und konfigurieren Sie die [Azure plattformübergreifende Befehlszeilenschnittstelle][10] (Inhalt in englischer Sprache), und verwenden Sie dann den folgenden Befehl:
      
            azure site domain list yoursitename
    
    Speichern Sie diesen **.azurewebsite.net**-Namen, da er in den folgenden Schritten verwendet wird.

2.  Melden Sie sich bei der Website Ihrer DNS-Registrierung an, und rufen Sie die Seite zur DNS-Verwaltung auf. Suchen Sie nach Links oder Bereichen der Site, die als **Domänenname**, **DNS** oder **Namenserververwaltung** bezeichnet werden.

3.  Navigieren Sie nun zu dem Bereich, in dem Sie CNAME-Datensätze auswählen oder eingeben können. Möglicherweise müssen Sie den Datensatztyp in einem Dropdownmenü auswählen oder die Seite für erweiterte Einstellungen aufrufen. Suchen Sie nach den Wörtern **CNAME**, **Alias** oder **Unterdomänen**.

4.  Sie müssen auch den Domänen- oder Unterdomänenalias für den CNAME angeben. Beispielsweise **www**, wenn Sie einen Alias für **www.customdomain.com** erstellen möchten. Wenn Sie einen Alias für die Stammdomäne erstellen möchten, wird dieser möglicherweise als "**@**"-Zeichen in den DNS-Tools Ihrer Registrierung aufgeführt.

5.  Sie müssen auch einen Hostnamen angeben, der der kanonische Domänenname dieses CNAME-Alias ist. Dies ist der **.azurewebsite.net**-Name für Ihre Website.

Der folgende CNAME-Datensatz leitet zum Beispiel den gesamten Verkehr von **www.contoso.com** an **contoso.azurewebsite.net** weiter. Das ist der benutzerdefinierte Domänenname einer Website:

<table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Hostname/Unterdomäne</strong>
</td>

<td><strong>Kanonische Domäne</strong>
</td>

</tr>

<tr>
<td>www</td>

<td>contoso.azurewebsite.net</td>

</tr>

</table>

Einem Besucher von **www.contoso.com** wird niemals der wirkliche Host (contoso.azurewebsite.net) angezeigt. Die Weiterleitung ist also für den Endbenutzer nicht sichtbar.

> [WACOM.NOTE] Das oben genannte Beispiel gilt nur für Verkehr an der
> Unterdomäne **www**. Da Sie keine Platzhalter mit CNAME-Datensätzen
> verwenden können, müssen Sie einen CNAME für jede Domäne/Unterdomäne
> erstellen. Wenn Sie den Verkehr von Unterdomänen wie *.contoso.com an
> Ihre azurewebsite.net-Adresse weiterleiten möchten, können Sie in
> Ihren DNS-Einstellungen einen Eintrag für **URL-Umleitung** oder
> **URL-Weiterleitung** erstellen. Alternativ können Sie einen
> A-Datensatz erstellen.

> [WACOM.NOTE] Es kann einige Zeit dauern, bis Ihr CNAME über das
> DNS-System weitergegeben wurde. Der CNAME für die Website wird erst
> angezeigt, wenn CNAME weitergegeben wurde. Mithilfe eines Services wie
> [http://www.digwebinterface.com/][11] (in
> englischer Sprache) können Sie überprüfen, ob der CNAME verfügbar ist.

### Hinzufügen eines Domänennamens zu Ihrer Website

Nachdem der CNAME-Datensatz für den Domänennamen weitergegeben wurde, müssen Sie ihn Ihrer Website zuweisen. Sie können entweder die plattformübergreifende Azure-Befehlszeilenschnittstelle oder das Azure Verwaltungsportal verwenden, um den durch den CNAME-Datensatz festgelegten benutzerdefinierten Domänennamen zu Ihrer Website hinzuzufügen.

**Hinzufügen eines Domänennamens mithilfe der Befehlszeilentools**

Installieren und konfigurieren Sie die [Azure plattformübergreifende Befehlszeilenschnittstelle][10] (Inhalt in englischer Sprache), und verwenden Sie dann den folgenden Befehl:

    azure site domain add customdomain yoursitename

Der folgende Befehl fügt beispielsweise einen benutzerdefinierten Domänennamen von **www.contoso.com** zur Website **contoso.azurewebsite.net** hinzu:

    azure site domain add www.contoso.com contoso

Mit dem folgenden Befehl können Sie überprüfen, ob der benutzerdefinierte Domänenname zur Website hinzugefügt wurde:

      azure site domain list yoursitename

Die von diesem Befehl zurückgegebene Liste sollte den Domänennamen sowie den Standardeintrag **.azurewebsite.net** enthalten.

**Hinzufügen eines Domänennamens mithilfe des Azure Verwaltungsportals**

1.  Öffnen Sie das [Azure Verwaltungsportal][6] in Ihrem Browser.

2.  Klicken Sie auf der Registerkarte **Web Sites** auf den Namen IhrerSite, wählen Sie dann **Dashboard** und unten auf der Seite **Domänen verwalten** aus.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  Geben Sie in das Textfeld **DOMÄNENNAMEN** den Domänennamen ein, den Sie konfiguriert haben.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4.  Klicken Sie auf das Häkchen, um den Domänennamen zu akzeptieren.

Sobald die Konfiguration abgeschlossen ist, wird der benutzerdefinierte Domänenname im Bereich der **Domänennamen** der Seite **Konfiguration** Ihrer Website angezeigt.

<a name="bkmk_configurearecord" ></a><h2>Hinzufügen eines A-Datensatzes zu Ihrer benutzerdefinierten Domäne</h2>

Sie müssen zunächst die IP-Adresse Ihrer Website ermitteln, um einen A-Datensatz zu erstellen. Fügen Sie dann einen Eintrag zu der DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzu. Verwenden Sie hierzu die durch Ihre Registrierung bereitgestellten Tools. Die Methoden zum Festlegen eines A-Datensatzes der verschiedenen Registrierungen sind zwar ähnlich, jedoch unterscheiden Sie sich auch in einigen Punkten. Die Konzepte sind allerdings gleich. Neben dem A-Datensatz müssen Sie auch einen CNAME-Datensatz erstellen, den Azure verwendet, um den A-Datensatz zu überprüfen.

1.  Öffnen Sie das [Azure Verwaltungsportal][6] in Ihrem Browser.

2.  Klicken Sie auf der Registerkarte **Web Sites** auf den Namen Ihrer Site, wählen Sie dann **Dashboard** und unten in der Anzeige **Domänen verwalten** aus.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  Navigieren Sie im Dialogfeld **Benutzerdefinierte Domänen verwalten** zu **Beim Konfigurieren von A-Datensätzen zu verwendende IP-Adresse**. Kopieren Sie die IP-Adresse. Diese wird zum Erstellen des A-Datensatzes verwendet.

4.  Notieren Sie den awverify-Domänennamen am Ende des Texts, oben im Dialogfeld **Benutzerdefinierte Domänen verwalten**, und überprüfen Sie diesen. Er sollte **awverify.mysite.azurewebsites.net** lauten, wobei **mysite** der Name Ihrer Website ist. Kopieren Sie diesen, da das der Domänenname ist, der zum Erstellen der Überprüfung des CNAME-Datensatzes verwendet wird.

5.  Melden Sie sich bei der Website Ihrer DNS-Registrierung an, und rufen Sie die Seite zur DNS-Verwaltung auf. Suchen Sie nach Links oder Bereichen der Site, die als **Domänenname**, **DNS** oder **Namenserververwaltung** bezeichnet werden.

6.  Navigieren Sie zu dem Bereich, in dem Sie A- und CNAME-Datensätze auswählen oder eingeben können. Möglicherweise müssen Sie den Datensatztyp in einem Dropdownmenü auswählen oder die Seite für erweiterte Einstellungen aufrufen.

7.  Führen Sie die folgenden Schritte aus, um einen A-Datensatz zu erstellen:
    
    1.  Wählen Sie die Domäne oder Unterdomäne aus, die den A-Datensatz  verwenden wird, oder geben Sie diese ein. Wählen Sie beispielsweise **www**, wenn Sie einen Alias für **www.customdomain.com** erstellen möchten. Wenn Sie einen Platzhaltereintrag für alle Unterdomänen erstellen möchten, geben Sie "*" ein. Dieser Eintrag deckt alle Unterdomänen ab wie **mail.customdomain.com**, **login.customdomain.com** und   **www.customdomain.com**.
        
        Wenn Sie einen A-Datensatz für die Stammdomäne erstellen möchten, wird dieser möglicherweise als "**@**"-Zeichen in den DNS-Tools Ihrer Registrierung aufgeführt.
    
    2.  Geben Sie die IP-Adresse Ihres Clouddiensts in das angegebene Feld ein. So wird der im A-Datensatz verwendete Domäneneintrag der IP-Adresse Ihrer Clouddienstbereitstellung zugewiesen.
        
        Der folgende A-Datensatz leitet zum Beispiel den gesamten Verkehr von **www.contoso.com** an **137.135.70.239** weiter. Das ist die IP-Adresse unserer bereitgestellten Anwendung:
        
        <table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
         <tr>
         <td><strong>Hostname/Unterdomäne</strong>
        </td>
        
         <td><strong>IP-Adresse</strong>
        </td>
        
         </tr>
        
         <tr>
         <td>@</td>
        
         <td>137.135.70.239</td>
        
         </tr>
        
         </table>
        
        Dieses Beispiel zeigt das Erstellen eines A-Datensatzes für die Stammdomäne. Wenn Sie einen Platzhaltereintrag erstellen möchten, der alle Unterdomänen abdeckt, würden Sie "*" als Unterdomäne eingeben.

8.  Erstellen Sie dann einen CNAME-Datensatz, der einen Alias von **awverify** besitzt und eine kanonische Domäne von **awverify.mysite.azurewebsites.net**, die Sie zuvor erhalten haben.


   
    > [WACOM.NOTE] Während awverify zwar für manche Registrierungen
    > funktioniert, benötigen andere hingegen möglicherweise den
    > vollständigen Alias-Domänenname von
    > awverify.www.customdomainname.com oder
    > awverify.customdomainname.com.
    
    Das folgende Beispiel erstellt einen CNAME-Datensatz, den Azure zur Überprüfung einer A-Datensatzkonfiguration verwenden kann.
    
    <table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
     <tr>
     <td><strong>Alias/Hostname/Unterdomäne</strong>
    </td>
    
     <td><strong>Kanonische Domäne</strong>
    </td>
    
     </tr>
    
     <tr>
     <td>awverify</td>
    
     <td>awverify.contoso.azurewebsites.net</td>
    
     </tr>
    
     </table>

> [WACOM.NOTE] Es kann einige Zeit dauern, bis der awverify-CNAME über
> das DNS-System weitergegeben wurde. Sie können den durch den
> A-Datensatz für die Website festgelegten benutzerdefinierten
> Domänennamen erst einstellen, wenn der awverify-CNAME weitergegeben
> wurde. Mithilfe eines Services wie
> [http://www.digwebinterface.com/][11] (in
> englischer Sprache) können Sie überprüfen, ob der CNAME verfügbar ist.

### Hinzufügen eines Domänennamens zu Ihrer Website

Nachdem der **awverify**-CNAME-Datensatz für den Domänennamen weitergegeben wurde, können Sie die durch den A-Datensatz festgelegte benutzerdefinierte Domäne Ihrer Website zuweisen. Sie können entweder die plattformübergreifende Azure-Befehlszeilenschnittstelle oder das Azure Verwaltungsportal verwenden, um den durch den A-Datensatz festgelegten benutzerdefinierten Domänennamen zu Ihrer Website hinzuzufügen.

**Hinzufügen eines Domänennamens mithilfe der Befehlszeilentools**

Installieren und konfigurieren Sie die [Azure plattformübergreifende Befehlszeilenschnittstelle][10] (Inhalt in englischer Sprache), und verwenden Sie dann den folgenden Befehl:

    azure site domain add customdomain yoursitename

Der folgende Befehl fügt beispielsweise einen benutzerdefinierten Domänennamen von **contoso.com** zur Website
**contoso.azurewebsite.net** hinzu:

    azure site domain add contoso.com contoso

Mit dem folgenden Befehl können Sie überprüfen, ob der benutzerdefinierte Domänenname zur Website hinzugefügt wurde:

      azure site domain list yoursitename

Die von diesem Befehl zurückgegebene Liste sollte den Domänennamen sowie den Standardeintrag **.azurewebsite.net** enthalten.

**Hinzufügen eines Domänennamens mithilfe des Azure Verwaltungsportals**

1.  Öffnen Sie das [Azure Verwaltungsportal][6] in Ihrem Browser.

2.  Klicken Sie auf der Registerkarte **Web Sites** auf den Namen Ihrer Site, wählen Sie dann **Dashboard** und unten auf der Seite **Domänen verwalten** aus.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  Geben Sie in das Textfeld **DOMÄNENNAMEN** den Domänennamen ein, den Sie konfiguriert haben.
    
    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4.  Klicken Sie auf das Häkchen, um den Domänennamen zu akzeptieren.

Sobald die Konfiguration abgeschlossen ist, wird der benutzerdefinierte Domänenname im Bereich der **Domänennamen** der Seite **Konfiguration** Ihrer Website angezeigt.

> [WACOM.NOTE] Nachdem Sie den durch den A-Datensatz festgelegten
> benutzerdefinierten Domänennamen zu Ihrer Website hinzugefügt haben,
> können Sie den awverify-CNAME-Datensatz entfernen. Hierzu können Sie
> die von Ihrer Registrierung zur Verfügung gestellten Tools verwenden.
> Wenn Sie jedoch in Zukunft einen weiteren A-Datensatz hinzufügen
> möchten, müssen Sie erneut einen awverify-Datensatz erstellen, damit
> Sie den durch den A-Datensatz festgelegten neuen Domänennamen der
> Website zuweisen können.

## Nächste Schritte

* [How to manage web sites (Verwalten von Websites, in englischer
  Sprache)][12]

* [Configure an SSL certificate for Web Sites (Konfigurieren eines
  SSL-Zertifikats für Web Sites, in englischer Sprache)][13]

<!-- Bookmarks -->

<!-- Links -->

<!-- images -->

<!-- images -->



[1]: http://www.windowsazure.com/de-de/develop/net/common-tasks/custom-dns/
[2]: http://www.windowsazure.com/de-de/pricing/details/web-sites/
[3]: http://en.wikipedia.org/wiki/CNAME_record
[4]: http://tools.ietf.org/html/rfc1035
[5]: https://www.windowsazure.com/en-us/pricing/details/
[6]: http://manage.windowsazure.com
[7]: http://msdn.microsoft.com/de-de/library/windowsazure/dn339012.aspx
[8]: http://msdn.microsoft.com/de-de/library/windowsazure/hh744839.aspx
[9]: http://www.windowsazure.com/de-de/manage/install-and-configure-windows-powershell/
[10]: http://www.windowsazure.com/de-de/manage/install-and-configure-cli/
[11]: http://www.digwebinterface.com/
[12]: http://www.windowsazure.com/de-de/manage/services/web-sites/how-to-manage-websites/
[13]: http://www.windowsazure.com/de-de/develop/net/common-tasks/enable-ssl-web-site/