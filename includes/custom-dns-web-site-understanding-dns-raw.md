Das Domain Name System (DNS) erleichtert das Auffinden von Ressourcen im Internet. Wenn Sie zum Beispiel eine Website-Adresse in Ihren Browser eingeben oder auf einen Link innerhalb einer Website klicken, übersetzt DNS die Domäne in eine IP-Adresse. Die IP-Adresse ist mit Straße und Hausnummer vergleichbar, aber sie ist nicht sehr einprägsam. Beispielsweise ist es viel einfacher, sich einen DNS-Namen wie **contoso.com** als eine IP-Adresse wie z. B. 192.168.1.88 oder 2001:0:4137:1f67:24a2:3888:9cce:fea3 zu merken.

Das DNS-System beruht auf *records*. Einträge ordnen einen bestimmten *name*, wie zum Beispiel **contoso.com**, entweder einer IP-Adresse oder einem anderen DNS-Namen zu. Wenn eine Anwendung wie ein Webbrowser einen Namen im DNS sucht, gelangt sie zum Eintrag und verwendet dessen Verweis als Adresse. Wenn es sich beim Verweis um eine IP-Adresse handelt, so verwendet der Browser diesen Wert. Wird auf einen anderen DNS-Namen gezeigt, so muss die Anwendung den Namen erneut auflösen. Letztendlich führen alle Namensauflösungen zu einer IP-Adresse.

Azure-Websites wird beim Erstellen automatisch ein DNS-Name zugewiesen. Dieser Name nimmt die Form **&lt;yoursitename&gt;.azurewebsites.net** an. Beim Erstellen von DNS-Einträgen steht auch eine virtuelle IP-Adresse zur Verfügung, sodass Sie Einträge erstellen können, die entweder auf **.azurewebsites.net** oder auf die IP-Adresse zeigen.

> [AZURE.NOTE] Die IP-Adresse Ihrer Website ändert sich, wenn Sie diese löschen und neu erstellen oder wenn Sie den Websitemodus vom Basis-, Freigabe- und Standardmodus in "Kostenlos" ändern.

Es gibt auch mehrere Eintragstypen, jeder mit eigenen Funktionen und Beschränkungen. Bei Websites sind jedoch nur zwei Einträge interessant:  *A*- und  *CNAME*-Einträge.

###Adresseintrag (A-Eintrag)

Ein A-Datensatz ordnet eine Domäne, wie z. B. **contoso.com** oder **www.contoso.com**, *or a wildcard domain* wie z. B. **\*.contoso.com** einer IP-Adresse zu. Im Falle einer Azure-Website entweder die virtuelle IP-Adresse des Dienstes oder eine spezifische, für die Website erworbene IP-Adresse.

Die Hauptvorteile eines A-Eintrags gegenüber einem CNAME-Eintrag sind:

* Sie können eine Stammdomäne wie **contoso.com** einer IP-Adresse zuweisen; viele Registrierungsstellen gestatten dies nur bei Verwendung von A-Einträgen.

* Sie können einen Eintrag mit einem Platzhalter verwenden, wie **\*.contoso.com**, der Anfragen für mehrere Unterdomänen verarbeiten kann, z. B. **mail.contoso.com**, **blogs.contoso.com** oder **www.contso.com**.

> [AZURE.NOTE] Da ein A-Datensatz einer statischen IP-Adresse zugewiesen ist, kann er nicht automatisch Änderungen an die IP-Adresse Ihrer Website übertragen. Eine IP-Adresse zur Verwendung mit A-Einträgen erhalten Sie, wenn Sie benutzerdefinierte Domänennameneinstellungen für Ihre Website konfigurieren.Dieser Wert kann sich jedoch ändern, wenn Sie Ihre Website löschen und neu erstellen oder den Websitemodus auf "Kostenlos" zurücksetzen.

###Aliaseintrag (CNAME-Eintrag)

Ein CNAME-Eintrag weist einen *specific* DNS-Namen, wie zum Beispiel **mail.contoso.com** oder **www.contoso.com**, einem anderen (kanonischen) Domänennamen zu. Bei Azure-Websites ist der kanonische Domänenname der **&lt;yoursitename>.azurewebsites.net**-Domänenname Ihrer Website. Sobald der CNAME erstellt worden ist, bildet er einen Alias für den Domänennamen **&lt;yoursitename>.azurewebsites.net**. Der CNAME-Eintrag wird in die IP-Adresse Ihres Domänennamens **&lt;yoursitename>.azurewebsites.net** aufgelöst, sodass Sie nichts unternehmen müssen, wenn sich die IP-Adresse Ihrer Website ändert.

> [AZURE.NOTE] Bei einigen Domänenregistrierungen können Sie Unterdomänen nur mit einem CNAME-Datensatz wie **www.contoso.com** zuweisen und nicht mit einem Stammnamen wie **contoso.com**. Weitere Informationen zu CNAME-Datensätzen finden Sie in der Dokumentation Ihrer Registrierungsstelle <a href="http://en.wikipedia.org/wiki/CNAME_record">im Wikipedia-Eintrag für den CNAME-Eintrag</a> oder im Dokument <a href="http://tools.ietf.org/html/rfc1035">IETF Domain Names - Implementation and Specification</a>.

###DNS-spezifische Merkmale von Azure-Websites

Die Verwendung eines A-Eintrags für Azure-Websites erfordert, dass Sie zunächst einen der folgenden CNAME-Einträge erstellen:

* **Für die Stammdomäne oder Platzhalter-Unterdomänen** - Ein DNS-Name von **awverify** bis **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**.

* **Für eine bestimmte Unterdomäne** - Ein DNS-Name von **awverify.&lt;Unterdomäne>** bis **awverify.&lt;Ihrwebsitename&gt;.azurewebsites.net**. Beispielsweise **awverify.blogs**, wenn der A-Eintrag für **blogs.contoso.com** vorgesehen ist.

Dieser CNAME-Eintrag dient zur Bestätigung, dass Sie die Domäne, die Sie zu verwenden versuchen, besitzen. Dies erfolgt zusätzlich zum Erstellen eines A-Eintrags mit Verweis auf die virtuelle IP-Adresse Ihrer Website.

Sie können die IP-Adresse sowie den **awverify**-Namen und die **.azurewebsites.net**-Namen für Ihre Website wie folgt ermitteln:

1. Öffnen Sie das [Azure-Verwaltungsportal](https://manage.windowsazure.com) in Ihrem Browser.

2. Klicken Sie auf der Registerkarte **Websites** auf den Namen Ihrer Site, wählen Sie dann **Dashboard** und am unteren Seitenrand **Domänen verwalten** aus.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

	> [AZURE.NOTE] Wenn **Domänen verwalten** nicht aktiviert ist, verwenden Sie eine kostenlose Website. Sie können bei einer kostenlosen Website keine benutzerdefinierten Domänennamen verwenden und müssen ein Upgrade zum Freigabe-, Basic- oder Standardmodus durchführen. Weitere Informationen zu den Website-Modi, darunter auch Informationen zum Ändern des Website-Modus, finden Sie unter [Skalieren von Websites](http://azure.microsoft.com/documentation/articles/web-sites-scale/).

6. Im Dialogfeld **Benutzerdefinierte Domänen verwalten** werden dann die **awverify**-Information, der momentan zugewiesene **.azurewebsites.net**-Domänenname und die virtuelle IP-Adresse angezeigt. Speichern Sie diese Informationen, das sie beim Erstellen der DNS-Einträge benötigt werden.

	![](./media/custom-dns-web-site/managecustomdomains.png)

<!--HONumber=42-->
