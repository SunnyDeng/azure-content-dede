Das Domain Name System (DNS) erleichtert das Auffinden von Ressourcen im Internet. Wenn Sie zum Beispiel eine Adresse in Ihrem Browser eingeben, oder Sie auf einen Link innerhalb einer Website klicken, so übersetzt DNS die Domäne in eine IP-Adresse. Die IP-Adresse ist mit Straße und Hausnummer vergleichbar, aber sie ist nicht sehr einprägsam. Ein DNS-Name wie **contoso.com** lässt sich beispielsweise viel leichter merken als eine IP-Adresse wie 192.168.1.88 oder 2001:0:4137:1f67:24a2:3888:9cce:fea3.

Das DNS-System beruht auf *records*. Einträge ordnen einen bestimmten *Namen*, wie zum Beispiel **contoso.com**, einer IP-Adresse oder einem anderen DNS-Namen zu. Wenn eine Anwendung wie ein Webbrowser einen Namen im DNS sucht, gelangt sie zum Eintrag und verwendet dessen Verweis als Adresse. Wenn es sich beim Verweis um eine IP-Adresse handelt, so verwendet der Browser diesen Wert. Wird auf einen anderen DNS-Namen gezeigt, so muss die Anwendung den Namen erneut auflösen. Letztendlich führen alle Namensauflösungen zu einer IP-Adresse.

Azure-Websites wird beim Erstellen automatisch ein DNS-Name zugewiesen. Dieser Name nimmt die Form **&lt;Ihr-Websitename&gt;.azurewebsites.net** an. Wenn Sie Ihre Website als Azure Traffic Manager-Endpunkt hinzufügen, ist diese über die **&lt;Ihr-TrafficManager-Profil&gt;.trafficmanager.net**-Domäne zugänglich.

> [AZURE.NOTE] Wenn Ihre Website als ein Traffic Manager-Endpunkt konfiguriert ist, verwenden Sie beim Erstellen von DNS-Einträgen die **.trafficmanager.net**-Adresse.

> Mit Traffic Manager können nur CNAME-Einträge verwendet werden.

Es gibt auch mehrere Eintragstypen, jeder mit eigenen Funktionen und Beschränkungen, bei als Traffic Manager-Endpunkte konfigurierten Websites interessieren wir uns jedoch nur für den Eintrag  *CNAME*.

###CNAME- oder Alias-Datensatz

Ein CNAME-Eintrag weist einen *spezifischen* DNS-Namen, z. B. **mail.contoso.com** oder **www.contoso.com**, einem anderen (kanonischen) Domänennamen zu. Bei Azure-Websites, die den Traffic Manager verwenden, ist der kanonische Domänenname der **&lt;Meine-App>.trafficmanager.net**-Domänenname Ihres Traffic Manager-Profils. Nach dem Erstellen legt CNAME einen Alias für den **&lt;Meine-App>.trafficmanager.net**-Domänennamen an. Der CNAME-Eintrag wird automatisch in die IP-Adresse Ihres **&lt;Meine-App>.trafficmanager.net**-Domänennamens aufgelöst. Daher müssen Sie nichts weiter tun, wenn sich die IP-Adresse der Website ändert.

Beim Traffic Manager eintreffender Verkehr wird nach der Lastenausgleichsmethode, für die er konfiguriert ist, zu Ihrer Website weitergeleitet. Für die Besucher Ihrer Website ist dies völlig transparent. Sie sehen nur den benutzerdefinierten Domänennamen in ihrem Browser.

> [AZURE.NOTE] Bei einigen Domänenregistrierungen können Sie Unterdomänen nur mit einem CNAME-Datensatz wie **www.contoso.com** zuweisen und nicht mit einem Stammnamen wie **contoso.com**. Weitere Informationen zu CNAME-Einträgen finden Sie in der von Ihrer Registrierungsstelle zur Verfügung gestellten Dokumentation, im Wikipedia-Artikel <a href="http://en.wikipedia.org/wiki/CNAME_record">CNAME Resource Record</a> oder im Dokument <a href="http://tools.ietf.org/html/rfc1035">IETF Domain Names - Implementation and Specification</a> (Domänennamen - Implementierung und Spezifizierung, in englischer Sprache).

<!--HONumber=52--> 
