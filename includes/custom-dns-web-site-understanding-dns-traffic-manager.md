Das Domain Name System (DNS) erleichtert das Auffinden von Ressourcen im Internet. Wenn Sie zum Beispiel eine Adresse in Ihrem Browser eingeben, oder Sie auf einen Link innerhalb einer Website klicken, so übersetzt DNS die Domäne in eine IP-Adresse. Die IP-Adresse ist mit Straße und Hausnummer vergleichbar, aber sie ist nicht sehr einprägsam. Beispielsweise ist es viel einfacher, sich einen DNS-Namen wie **contoso.com** als eine IP-Adresse wie z. B. 192.168.1.88 oder 2001:0:4137:1f67:24a2:3888:9cce:fea3 zu merken.

Das DNS-System beruht auf *records*. Einträge ordnen einen bestimmten *name*, wie zum Beispiel **contoso.com**, entweder einer IP-Adresse oder einem anderen DNS-Namen zu. Wenn eine Anwendung wie ein Webbrowser einen Namen im DNS sucht, gelangt sie zum Eintrag und verwendet dessen Verweis als Adresse. Wenn es sich beim Verweis um eine IP-Adresse handelt, so verwendet der Browser diesen Wert. Wird auf einen anderen DNS-Namen gezeigt, so muss die Anwendung den Namen erneut auflösen. Letztendlich führen alle Namensauflösungen zu einer IP-Adresse.

Azure-Websites wird beim Erstellen automatisch ein DNS-Name zugewiesen. Dieser Name nimmt die Form **&lt;yoursitename&gt;.azurewebsites.net** an. Wenn Sie Ihre Website als Azure Traffic Manager-Endpunkt hinzufügen, ist diese über die **&lt;yourtrafficmanagerprofile&gt;.trafficmanager.net**-Domäne zugänglich.

> [AZURE.NOTE] Wenn Ihre Website als ein Traffic Manager-Endpunkt konfiguriert ist, verwenden Sie beim Erstellen von DNS-Einträgen die **.trafficmanager.net**-Adresse.

> [AZURE.NOTE] Mit Traffic Manager können nur CNAME-Einträge verwendet werden.

Es gibt auch mehrere Eintragstypen, jeder mit eigenen Funktionen und Beschränkungen, bei als Traffic Manager-Endpunkte konfigurierten Websites interessieren wir uns jedoch nur für den Eintrag  *CNAME*.

###CNAME- oder Alias-Eintrag

Ein CNAME-Eintrag weist einen *specific* DNS-Namen, wie zum Beispiel **mail.contoso.com** oder **www.contoso.com**, einem anderen (kanonischen) Domänennamen zu. Bei Azure-Websites, die den Traffic Manager verwenden, ist der kanonische Domänenname der **&lt;myapp>.trafficmanager.net**-Domänenname Ihres Traffic Manager-Profils. Sobald der CNAME erstellt worden ist, bildet er einen Alias für den Domänennamen **&lt;myapp>.trafficmanager.net**. Der CNAME-Eintrag wird in die IP-Adresse Ihres Domänennamens **&lt;myapp>.trafficmanager.net** aufgelöst, sodass Sie nichts unternehmen müssen, wenn sich die IP-Adresse Ihrer Website ändert.

Beim Traffic Manager eintreffender Verkehr wird nach der Lastenausgleichsmethode, für die er konfiguriert ist, zu Ihrer Website weitergeleitet. Für die Besucher Ihrer Website ist dies völlig transparent. Sie sehen nur den benutzerdefinierten Domänennamen in ihrem Browser.

> [AZURE.NOTE] Bei einigen Domänenregistrierungen können Sie Unterdomänen nur mit einem CNAME-Datensatz wie **www.contoso.com** zuweisen und nicht mit einem Stammnamen wie **contoso.com**. Weitere Informationen zu CNAME-Datensätzen finden Sie in der Dokumentation Ihrer Registrierungsstelle, <a href="http://en.wikipedia.org/wiki/CNAME_record">im Wikipedia-Eintrag zum CNAME-Datensatz</a> oder im Dokument <a href="http://tools.ietf.org/html/rfc1035">IETF Domain Names - Implementation and Specification</a>.

<!--HONumber=42-->
