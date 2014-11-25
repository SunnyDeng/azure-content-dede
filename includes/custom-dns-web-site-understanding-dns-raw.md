Das Domain Name System (DNS) erleichtert das Auffinden von Ressourcen im Internet. Wenn Sie zum Beispiel eine Website-Adresse in Ihren Browser eingeben oder auf einen Link innerhalb einer Website klicken, übersetzt DNS die Domäne in eine IP-Adresse. Die IP-Adresse ist mit Straße und Hausnummer vergleichbar, aber sie ist nicht sehr einprägsam. Ein DNS-Name wie **contoso.com** lässt sich beispielsweise viel leichter merken als eine IP-Adresse wie 192.168.1.88 oder 2001:0:4137:1f67:24a2:3888:9cce:fea3.

Das DNS-System beruht auf *Einträgen*. Einträge ordnen einen bestimmten *Namen*, wie zum Beispiel **contoso.com**, entweder einer IP-Adresse oder einem anderen DNS-Namen zu. Wenn eine Anwendung wie ein Webbrowser einen Namen im DNS sucht, gelangt sie zum Eintrag und verwendet dessen Verweis als Adresse. Wenn es sich beim Verweis um eine IP-Adresse handelt, so verwendet der Browser diesen Wert. Wird auf einen anderen DNS-Namen gezeigt, so muss die Anwendung den Namen erneut auflösen. Letztendlich führen alle Namensauflösungen zu einer IP-Adresse.

Azure-Websites wird beim Erstellen automatisch ein DNS-Name zugewiesen. Dieser Name nimmt die Form **\<yoursitename\>.azurewebsites.net** an. Beim Erstellen von DNS-Einträgen steht auch eine virtuelle IP-Adresse zur Verfügung, sodass Sie Einträge erstellen können, die entweder auf **.azurewebsites.net** oder auf die IP-Adresse zeigen.

> [WACOM.NOTE] Die IP-Adresse Ihrer Website ändert sich, wenn Sie diese löschen und neu erstellen oder wenn Sie den Websitemodus vom Basis-, Freigabe- und Standardmodus in "Kostenlos" ändern.

Es gibt auch mehrere Eintragstypen, jeder mit eigenen Funktionen und Beschränkungen. Bei Websites sind jedoch nur zwei Einträge interessant: *A*- und *CNAME*-Einträge.

### Adresseintrag (A-Eintrag)

Ein A-Eintrag weist eine Domäne wie **contoso.com**, **www.contoso.com** *oder eine Domäne mit Platzhalter* wie **\*.contoso.com** einer IP-Adresse zu. Im Falle einer Azure-Website entweder die virtuelle IP-Adresse des Dienstes oder eine spezifische, für die Website erworbene IP-Adresse.

Die Hauptvorteile eines A-Eintrags gegenüber einem CNAME-Eintrag sind:

-   Sie können eine Stammdomäne wie **contoso.com** einer IP-Adresse zuweisen; viele Registrierungsstellen gestatten dies nur bei Verwendung von A-Einträgen

-   Sie können einen Eintrag mit einem Platzhalter verwenden, wie **\*.contoso.com**, der Anfragen für mehrere Unterdomänen verarbeiten kann, z. B. **mail.contoso.com**, **blogs.contoso.com** oder **www.contso.com**.

> [WACOM.NOTE] Da ein A-Eintrag einer statischen IP-Adresse zugewiesen ist, kann er bei Änderungen der IP-Adresse Ihrer Website nicht automatisch aufgelöst werden. Eine IP-Adresse zur Verwendung mit A-Einträgen erhalten Sie, wenn Sie benutzerdefinierte Domänennameneinstellungen für Ihre Website konfigurieren.Dieser Wert kann sich jedoch ändern, wenn Sie Ihre Website löschen und neu erstellen oder den Websitemodus auf "Kostenlos" zurücksetzen.

### Aliaseintrag (CNAME-Eintrag)

Ein CNAME-Eintrag weist einen *spezifischen* DNS-Namen, wie zum Beispiel **mail.contoso.com** oder **www.contoso.com**, einem anderen (kanonischen) Domänennamen zu. Bei Azure-Websites ist der kanonische Domänenname der **\<IhrSitename\>.azurewebsites.net**-Domänenname Ihrer Website. Sobald der CNAME erstellt worden worden ist, bildet er einen Alias für den Domänennamen **\<IhrSitename\>.azurewebsites.net**. Der CNAME-Eintrag wird in die IP-Adresse Ihres Domänennamens **\<IhrSitename\>.azurewebsites.net** aufgelöst, sodass Sie nichts unternehmen müssen, wenn sich die IP-Adresse Ihrer Website ändert.

> [WACOM.NOTE] Manche Domänenregistrierungsstellen gestatten die Zuweisung von Unterdomänen nur bei Verwendung eines CNAME-Eintrags, wie zum Beispiel **www.contoso.com**, und nicht mit Stammnamen wie **contoso.com**. Weitere Informationen zu CNAME-Einträgen finden Sie in der von Ihrer Domänenregistrierungsstelle zur Verfügung gestellten Dokumentation, [im Wikipedia-Artikel "CNAME Resource Record"][im Wikipedia-Artikel "CNAME Resource Record"] oder im Dokument [IETF Domain Names - Implementation and Specification][IETF Domain Names - Implementation and Specification] (Domänennamen – Implementierung und Spezifizierung, in englischer Sprache).

### DNS-spezifische Merkmale von Azure-Websites

Die Verwendung eines A-Eintrags für Azure-Websites erfordert, dass Sie zunächst einen der folgenden CNAME-Einträge erstellen:

-   **Für die Stammdomäne oder Platzhalter-Unterdomänen** - Ein DNS-Name von **awverify** bis **awverify.\<Ihrwebsitename\>.azurewebsites.net**.

-   **Für eine bestimmte Unterdomäne** - Ein DNS-Name von **awverify.\<Unterdomäne\>** bis **awverify.\<Ihrwebsitename\>.azurewebsites.net**. Beispielsweise **awverify.blogs**, wenn der A-Eintrag für **blogs.contoso.com** vorgesehen ist.

Dieser CNAME-Eintrag dient zur Bestätigung, dass Sie die Domäne, die Sie zu verwenden versuchen, besitzen. Dies erfolgt zusätzlich zum Erstellen eines A-Eintrags mit Verweis auf die virtuelle IP-Adresse Ihrer Website.

Sie können die IP-Adresse sowie den **awverify.www**-Namen und die **.azurewebsites.net**-Namen für Ihre Website wie folgt ermitteln:

1.  Öffnen Sie das [Azure-Verwaltungsportal][Azure-Verwaltungsportal] in Ihrem Browser.

2.  Klicken Sie auf der Registerkarte **Websites** auf den Namen Ihrer Site, wählen Sie dann **Dashboard** und am unteren Seitenrand **Domänen verwalten** aus.

    ![][0]

    > [WACOM.NOTE] Wenn **Domänen verwalten** nicht aktiviert ist, verwenden Sie eine kostenlose Website. Sie können bei einer kostenlosen Website keine benutzerdefinierten Domänennamen verwenden und müssen ein Upgrade zum Freigabe-, Basic- oder Standardmodus durchführen. Weitere Informationen zu den Website-Modi, darunter auch Informationen zum Ändern des Website-Modus, finden Sie unter [Skalieren von Websites][Skalieren von Websites].

3.  Im Dialogfeld **BENUTZERDEFINIERTE DOMÄNEN VERWALTEN** werden dann die **awverify**-Information, der momentan zugewiesene **.azurewebsites.net**-Domänenname und die virtuelle IP-Adresse angezeigt. Speichern Sie diese Informationen, das sie beim Erstellen der DNS-Einträge benötigt werden.

    ![][1]

  [im Wikipedia-Artikel "CNAME Resource Record"]: http://en.wikipedia.org/wiki/CNAME_record
  [IETF Domain Names - Implementation and Specification]: http://tools.ietf.org/html/rfc1035
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com
  [0]: ./media/custom-dns-web-site/dncmntask-cname-6.png
  [Skalieren von Websites]: http://www.windowsazure.com/de-de/documentation/articles/web-sites-scale/
  [1]: ./media/custom-dns-web-site/managecustomdomains.png
