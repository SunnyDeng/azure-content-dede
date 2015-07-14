Das Domain Name System (DNS) erleichtert das Auffinden von Ressourcen im Internet. Wenn Sie beispielsweise eine Web-App-Adresse in Ihrem Browser eingeben oder auf einen Link auf einer Webseite klicken, wird mit DNS die Domäne in eine IP-Adresse übersetzt. Die IP-Adresse ist mit Straße und Hausnummer vergleichbar, aber sie ist nicht sehr einprägsam. Ein DNS-Name wie **contoso.com** lässt sich beispielsweise viel leichter merken als eine IP-Adresse wie 192.168.1.88 oder 2001:0:4137:1f67:24a2:3888:9cce:fea3.

Das DNS-System beruht auf *Einträgen*. Einträge ordnen einen bestimmten *Namen*, wie zum Beispiel **contoso.com**, entweder einer IP-Adresse oder einem anderen DNS-Namen zu. Wenn eine Anwendung wie ein Webbrowser einen Namen im DNS sucht, gelangt sie zum Eintrag und verwendet dessen Verweis als Adresse. Wenn es sich beim Verweis um eine IP-Adresse handelt, so verwendet der Browser diesen Wert. Wird auf einen anderen DNS-Namen gezeigt, so muss die Anwendung den Namen erneut auflösen. Letztendlich führen alle Namensauflösungen zu einer IP-Adresse.

Bei der Erstellung einer Web-App in App Service wird der Web-App automatisch ein DNS-Name zugewiesen. Dieser Name hat das Format **&lt;Web-App-Name&gt;.azurewebsites.net**. Beim Erstellen von DNS-Einträgen steht auch eine virtuelle IP-Adresse zur Verfügung, sodass Sie Einträge erstellen können, die entweder auf **.azurewebsites.net** oder auf die IP-Adresse zeigen.

> [AZURE.NOTE]Die IP-Adresse Ihrer Web-App ändert sich, wenn Sie die Web-App löschen und neu erstellen oder wenn Sie den Modus des App Service-Plans von **Basic**, **Shared** oder **Standard** in **Free** ändern.

Es gibt mehrere Datensatztypen, von denen jeder über eigene Funktionen und Beschränkungen verfügt. Für Web-Apps sind jedoch nur zwei Datensätze interessant: *A* und *CNAME*.

###Adresseintrag (A-Eintrag)

Ein A-Datensatz weist eine Domäne wie **contoso.com** oder **www.contoso.com** *oder eine Domäne mit Platzhalter* wie **\*.contoso.com** einer IP-Adresse zu. Bei einer Web-App in App Service ist das die virtuelle IP-Adresse des Diensts oder eine bestimmte IP-Adresse, die Sie für Ihre Web-App erworben haben.

Die Hauptvorteile eines A-Eintrags gegenüber einem CNAME-Eintrag sind:

* Sie können eine Stammdomäne wie **contoso.com** einer IP-Adresse zuweisen; viele Registrierungsstellen gestatten dies nur bei Verwendung von A-Einträgen

* Sie können einen Eintrag mit einem Platzhalter verwenden, wie **\*.contoso.com**, der Anfragen für mehrere Unterdomänen verarbeiten kann, z. B. **mail.contoso.com**, **blogs.contoso.com** oder **www.contso.com**.

> [AZURE.NOTE]Da ein A-Datensatz einer statischen IP-Adresse zugeordnet ist, können Änderungen an der IP-Adresse der Web-App nicht automatisch aufgelöst werden. Sie erhalten eine IP-Adresse zur Verwendung mit A-Datensätzen, wenn Sie benutzerdefinierte Domänennameneinstellungen für Ihre Web-App konfigurieren; dieser Wert kann sich jedoch ändern, wenn Sie Ihre Web-App löschen und neu erstellen oder den Modus des App Service-Plans auf **Free** zurücksetzen.

###Aliaseintrag (CNAME-Eintrag)

Ein CNAME-Eintrag weist einen *spezifischen* DNS-Namen, wie zum Beispiel **mail.contoso.com** oder **www.contoso.com**, einem anderen (kanonischen) Domänennamen zu. Im Fall von App Service-Web-Apps ist der kanonische Domänenname der Domänenname **&lt;Web-App-Name>.azurewebsites.net** Ihrer Web-App. Sobald dies angelegt wurde, erstellt der CNAME einen Alias für den Domänennamen **&lt;Web-App-Name>.azurewebsites.net**. Der CNAME-Eintrag wird automatisch in die IP-Adresse Ihres Domänennamens **&lt;Web-App-Name>.azurewebsites.net** aufgelöst, sodass Sie nichts unternehmen müssen, wenn sich die IP-Adresse Ihrer Web-App ändert.

> [AZURE.NOTE]Bei einigen Domänenregistrierungen können Sie Unterdomänen nur mit einem CNAME-Datensatz wie **www.contoso.com** zuweisen und nicht mit einem Stammnamen wie **contoso.com**. Weitere Informationen zu CNAME-Datensätzen finden Sie in der durch Ihre Registrierung zur Verfügung gestellten Dokumentation, <a href="http://en.wikipedia.org/wiki/CNAME_record">im Wikipedia-Eintrag "CNAME Resource Record"</a> oder im Dokument <a href="http://tools.ietf.org/html/rfc1035">IETF Domain Names - Implementation and Specification</a>.

###Web-App-DNS-Besonderheiten

Bei der Verwendung eines A-Datensatzes für Web-Apps müssen Sie zunächst einen der folgenden CNAME-Datensätze erstellen:

* **Für die Stammdomäne oder Platzhalter-Unterdomänen** - Ein DNS-Name von **awverify** bis **awverify.&lt;Web-App-Name&gt;.azurewebsites.net**.

* **Für eine bestimmte Unterdomäne** - Ein DNS-Name von **awverify.&lt;Unterdomäne>** bis **awverify.&lt;Web-App-Name&gt;.azurewebsites.net**. Beispielsweise **awverify.blogs**, wenn der A-Eintrag für **blogs.contoso.com** vorgesehen ist.

Dieser CNAME-Eintrag dient zur Bestätigung, dass Sie die Domäne, die Sie zu verwenden versuchen, besitzen. Dies erfolgt zusätzlich zum Erstellen eines A-Datensatzes mit Verweis auf die virtuelle IP-Adresse Ihrer Web-App.

Sie können die IP-Adresse sowie den **awverify**-Namen und die **.azurewebsites.net**-Namen für Ihre Web-App wie folgt ermitteln:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com) in Ihrem Browser.

2. Klicken Sie auf dem Blatt **Web-Apps** auf den Namen der Web-App, wählen Sie **Alle Einstellungen** und dann im unteren Bereich der Seite die Option **Benutzerdefinierte Domänen und SSL** aus.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

	> [AZURE.NOTE]Wenn **Domänen verwalten** nicht aktiviert ist, verwenden Sie eine kostenlose Web-App im Modus **Free**. Die können benutzerdefinierte Domänennamen nicht mit einer Web-App im Modus **Free** verwenden und müssen für den App Service-Plan ein Upgrade auf einen der Modi **Shared**, **Basic** oder **Standard** ausführen. Weitere Informationen zu den Modi des App Service-Plans, darunter auch Informationen zum Ändern des Web-App-Modus, finden Sie unter [Skalieren von Web-Apps](../articles/web-sites-scale.md).

6. Im Dialogfeld **BENUTZERDEFINIERTE DOMÄNEN VERWALTEN** werden dann die **awverify**-Information, der momentan zugewiesene **.azurewebsites.net**-Domänenname und die virtuelle IP-Adresse angezeigt. Speichern Sie diese Informationen, das sie beim Erstellen der DNS-Einträge benötigt werden.

	![](./media/custom-dns-web-site/managecustomdomains.png)

<!---HONumber=62-->