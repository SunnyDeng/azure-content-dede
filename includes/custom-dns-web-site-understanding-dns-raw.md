<p>Das Domain Name System (DNS) erleichtert das Auffinden von Ressourcen im Internet. Wenn Sie zum Beispiel eine Adresse in Ihrem Browser eingeben, oder Sie auf einen Link innerhalb einer Website klicken, so &uuml;bersetzt DNS die Dom&auml;ne in eine IP-Adresse. Die IP-Adresse ist mit Stra&szlig;e und Hausnummer vergleichbar, aber sie ist nicht sehr einpr&auml;gsam. Ein DNS-Name wie <strong>contoso.com</strong> l&auml;sst sich beispielsweise viel leichter merken als eine IP-Adresse wie 192.168.1.88 oder 2001:0:4137:1f67:24a2:3888:9cce:fea3.</p>


Das DNS-System beruht auf *Einträgen*. Einträge ordnen einen bestimmten *Namen*, wie zum Beispiel **contoso.com**, entweder einer IP-Adresse oder einem anderen DNS-Namen zu. Wenn eine Anwendung wie ein Webbrowser einen Namen im DNS sucht, gelangt sie zum Eintrag und verwendet dessen Verweis als Adresse. Wenn es sich beim Verweis um eine IP-Adresse handelt, so verwendet der Browser diesen Wert. Wird auf einen anderen DNS-Namen gezeigt, so muss die Anwendung den Namen erneut auflösen. Letztendlich führen alle Namensauflösungen zu einer IP-Adresse.

Azure-Websites wird beim Erstellen automatisch ein DNS-Name zugewiesen. Dieser Name nimmt die Form **&lt;yoursitename\>.azurewebsites.net** an. Beim Erstellen von DNS-Einträgen steht auch eine virtuelle IP-Adresse zur Verfügung, sodass Sie Einträge erstellen können, die entweder auf **.azurewebsites.net** oder auf die IP-Adresse zeigen.

> [WACOM.NOTE] Die IP-Adresse Ihrer Website ändert sich, wenn Sie diese löschen und neu erstellen, oder wenn Sie den Websitemodus von Basic, Shared oder Standard zu Free ändern.

Es gibt auch mehrere Eintragstypen, jeder mit eigenen Funktionen und Beschränkungen, bei Websites interessieren wir uns jedoch nur für die zwei Einträge *CNAME* und *A*.

### CNAME- oder Alias-Eintrag

Ein CNAME-Eintrag weist einen *spezifischen* DNS-Namen, wie zum Beispiel **mail.contoso.com** oder **www.contoso.com**, einem anderen (kanonischen) Domänennamen zu. Bei Azure-Websites ist der kanonische Domänenname der **&lt;myapp\>.azurewebsites.net**-Domänenname Ihrer Website. Nach Erstellung legt CNAME einen Alias für den **&lt;myapp\>.azurewebsites.net**-Domänennamen an. Der CNAME-Eintrag wird automatisch in die IP-Adresse Ihres **&lt;myapp\>.azurewebsites.net**-Domänennamens aufgelöst. Daher müssen Sie nichts weiter tun, wenn sich die IP-Adresse der Website ändert.

> [WACOM.NOTE] Manche Domänenregistrierungsstellen gestatten die Zuweisung von Unterdomänen nur bei Verwendung eines CNAME-Eintrags, wie zum Beispiel **www.contoso.com**, und nicht mit Stammnamen wie **contoso.com**. Weitere Informationen zu CNAME-Einträgen finden Sie in der von Ihrer Domänenregistrierungsstelle zur Verfügung gestellten Dokumentation, [im Wikipedia-Artikel "CNAME Resource Record"](http://en.wikipedia.org/wiki/CNAME_record) oder im Dokument [IETF Domain Names - Implementation and Specification](http://tools.ietf.org/html/rfc1035) (Domänennamen – Implementierung und Spezifizierung, in englischer Sprache).

### A-Eintrag

Ein A-Eintrag weist eine Domäne wie **contoso.com**, **www.contoso.com** *oder eine Domäne mit Platzhalter* wie **\*.contoso.com** einer IP-Adresse zu. Im Falle einer Azure-Website entweder die virtuelle IP-Adresse des Dienstes oder eine spezifische, für die Website erworbene IP-Adresse.

Die Hauptvorteile eines A-Eintrags gegenüber einem CNAME-Eintrag sind:

-   Sie können eine Stammdomäne wie **contoso.com** einer IP-Adresse zuweisen; viele Registrierungsstellen gestatten dies nur bei Verwendung von A-Einträgen

-   Sie können einen Eintrag mit einem Platzhalter verwenden wie **\*.contoso.com**, der Anfragen für mehrere Unterdomänen verarbeiten kann, beispielsweise **mail.contoso.com**, **login.contoso.com** oder **www.contso.com**.

> [WACOM.NOTE] Da ein A-Eintrag einer statischen IP-Adresse zugewiesen ist, kann er bei Änderungen der IP-Adresse Ihrer Website nicht automatisch aufgelöst werden. Eine IP-Adresse zur Verwendung mit A-Einträgen erhalten Sie, wenn Sie benutzerdefinierte Domänennameneinstellungen für Ihre Website konfigurieren; dieser Wert kann sich jedoch ändern, wenn Sie Ihre Website löschen und neu erstellen, oder Sie den Websitemodus auf Free zurücksetzen.

### DNS-Besonderheiten bei Azure-Websites

Verwendung eines A-Eintrags für Azure-Websites erfordert, dass Sie zunächst einen CNAME-Eintrag erstellen, der entweder:

-   einen DNS-Namen von **www** Ihrer Website **&lt;yourwebsitename\>.azurewebsites.net** zuweist. ODER
-   einen DNS-Namen von **awverify.www** Ihrer Website **awverify.&lt;yourwebsitename\>.azurewebsites.net** zuweist.

Dieser CNAME-Eintrag dient zur Bestätigung, dass Sie die Domäne, die Sie zu verwenden versuchen, besitzen. Dies erfolgt zusätzlich zum Erstellen eines A-Eintrags mit Verweis auf die virtuelle IP-Adresse Ihrer Website.

Sie können die IP-Adresse sowie den **awverify.www**-Namen und die **.azurewebsites.net**-Namen für Ihre Website anhand der folgenden Schritte ermitteln:

1.  Öffnen Sie das [Azure-Verwaltungsportal](https://manage.windowsazure.com) in Ihrem Browser.

2.  Klicken Sie auf der Registerkarte **Websites** auf den Namen Ihrer Site, wählen Sie dann **Dashboard** und unten auf der Seite **Domänen verwalten** aus.

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  Im Dialogfeld **BENUTZERDEFINIERTE DOMÄNEN VERWALTEN** werden dann die **awverify**-Information, der momentan zugewiesene **.azurewebsites.net**-Domänenname und die virtuelle IP-Adresse angezeigt.


