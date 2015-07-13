<properties
   pageTitle="Anleitungen zum Content Delivery Network (CDN) | Microsoft Azure"
   description="Anleitungen zum Content Delivery Network (CDN) für die Bereitstellung von in Azure gehosteten Inhalten mit hoher Bandbreite."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# Anleitungen zum Content Delivery Network (CDN)

![](media/best-practices-cdn/pnp-logo.png)

## Übersicht

Das Microsoft Azure Content Delivery Network (CDN) bietet Entwicklern eine globale Lösung für die Bereitstellung von Inhalten mit hoher Bandbreite, die in Azure gehostet werden. Das CDN speichert öffentlich verfügbare Objekte zwischen, die aus dem Azure-Blob-Speicher oder einem Anwendungsordner an strategischen Speicherorten geladen werden, um die maximale Bandbreite zum Bereitstellen von Inhalten für Benutzer zu bieten. Es wird normalerweise zum Bereitstellen statischer Inhalte wie Bilder, Stylesheets, Dokumente, Dateien, clientseitige Skripts und HTML-Seiten verwendet.

Die Hauptvorteile der Verwendung des CDN sind eine niedrigere Latenz und eine schnellere Bereitstellung von Inhalten für Benutzer, unabhängig von ihrem geografischen Standort in Bezug auf das Rechenzentrum, in dem die Anwendung gehostet wird, und eine Reduzierung der Auslastung der Anwendung selbst, da sie von der erforderlichen Verarbeitung für den Zugriff und die Bereitstellung von Inhalten befreit ist. Diese Reduzierung der Auslastung kann die Leistung und Skalierbarkeit der Anwendung erhöhen sowie die Hostingkosten durch Reduzieren der Ressourcen, die zum Erreichen einer bestimmten Leistungs- und Verfügbarkeitsstufe erforderlich sind, senken.

Möglicherweise können Sie auch andere Content Delivery Network-Systeme verwenden, die nicht von Azure in Ihren Anwendungen implementiert werden, wenn das Azure CDN nicht Ihren Anforderungen entspricht. Alternativ können Sie das Azure CDN möglicherweise für Anwendungen verwenden, die von anderen Anbietern gehostet werden, indem statische Inhalte in Azure-Speicher- oder Azure-Serverinstanzen verfügbar gemacht werden. ![](media/best-practices-cdn/CDN.png)

## Wie und warum das CDN verwendet wird

Das CDN wird häufig für Folgendes verwendet:

- Bereitstellen statischer Ressourcen für Clientanwendungen, häufig von einer Website. Dabei kann es sich um Bilder, Stylesheets, Dokumente, Dateien, clientseitige Skripts, HTML-Seiten, HTML-Fragmente oder andere Inhalte handeln, die der Server nicht für jede Anforderung ändern muss. Die Anwendung kann Elemente zur Laufzeit erstellen und dem CDN verfügbar machen (z. B. durch Erstellen einer Liste mit aktuellen Schlagzeilen), führt diese Aktion aber nicht für jede Anforderung aus.

- Bereitstellen von öffentlichen statischen und freigegebenen Inhalten auf Geräten wie Mobiltelefonen und Tablet-PCs, auf denen die Anwendung selbst ein Webdienst ist, der eine API für Clients bietet. Zusätzlich zu anderen Inhalten, die der Server nicht für jede Anforderung ändern muss, kann das CDN statische Datasets zur Verwendung durch den Client bereitstellen, möglicherweise zum Generieren der Client-UI. Beispielsweise könnten so JSON- oder XML-Dokumente bereitgestellt werden.

- Verwaltung vollständiger Websites, die nur aus öffentlichen statischen Inhalten für Clients bestehen, ohne dass dedizierte Compute-Ressourcen erforderlich sind.

- Streaming von Videodateien an den Client bei Bedarf. Videos profitieren von der niedrigen Latenz und der zuverlässigen Konnektivität aus den Rechenzentren auf der ganzen Welt, die CDN-Verbindungen anbieten.

- Das allgemeine Verbessern der Benutzerfreundlichkeit, insbesondere für Benutzer, die sich weit vom Standort des Anwendungsrechenzentrums entfernt befinden und andernfalls höhere Latenzen hätten. Ein Großteil der Gesamtgröße des Inhalts in einer Webanwendung ist häufig statisch, und das Verwenden des CDN kann dazu beitragen, die Leistung und die allgemeine Benutzerfreundlichkeit aufrechtzuerhalten, während es gleichzeitig nicht erforderlich ist, die Anwendung in mehreren Rechenzentren bereitzustellen.

- Umgang mit der wachsenden Auslastung von Anwendungen auf mobilen und standortgebundenen Geräten, die Teil des Internets der Dinge (IoT, Internet of Things) sind. Die große Anzahl solcher Geräte und Appliances könnte die Anwendung problemlos überlasten, wenn es erforderlich wäre, Sendenachrichten zu verarbeiten und die Firmwareupdateverteilung direkt zu verwalten.

- Umgang mit Spitzen und zunehmender Nachfrage, ohne dass die Anwendung skaliert werden muss, um den darauf folgenden Anstieg der laufenden Kosten zu vermeiden. Wenn zum Beispiel ein Update für ein Betriebssystem veröffentlicht wird, etwa für ein Hardwaregerät wie ein bestimmtes Routermodell, oder für ein Consumergerät, z. B. ein Smart-TV, steigt der Bedarf stark an, da dieses Update innerhalb kurzer Zeit von Millionen von Benutzern und Geräten heruntergeladen wird.

- Die folgende Tabelle zeigt Beispiele für die mittlere Zeit bis zum ersten Byte von verschiedenen geografischen Standorten aus. Die Zielwebrolle wird in Azure West US (Westen der USA) bereitgestellt. Es gibt eine starke Wechselbeziehung zwischen dem größeren Boost durch das CDN und der Entfernung zu einem CDN-Knoten. Eine Liste aller Azure CDN-Knotenstandorte finden Sie unter [POP-Standorte von Azure Content Delivery Network (CDN)](http://msdn.microsoft.com/library/azure/gg680302.aspx).

<table xmlns:xlink="http://www.w3.org/1999/xlink"><tr><th><a name="_MailEndCompose" href="#"><span /></a><br /></th><th><p>Zeit bis zum ersten Byte (Ursprung)</p></th><th><p>Zeit bis zum ersten Byte (CDN)</p></th><th><p> % schneller für CDN</p></th></tr><tr><td><p>* San Jose, CA</p></td><td><p>47,5</p></td><td><p>46,5</p></td><td><p>2 %</p></td></tr><tr><td><p>** Dulles, VA</p></td><td><p>109</p></td><td><p>40,5</p></td><td><p>169 %</p></td></tr><tr><td><p>Buenos Aires, AR</p></td><td><p>210</p></td><td><p>151</p></td><td><p>39 %</p></td></tr><tr><td><p>* London, UK</p></td><td><p>195</p></td><td><p>44</p></td><td><p>343 %</p></td></tr><tr><td><p>Shanghai, CN</p></td><td><p>242</p></td><td><p>206</p></td><td><p>17 %</p></td></tr><tr><td><p>* Singapore</p></td><td><p>214</p></td><td><p>74</p></td><td><p>189 %</p></td></tr><tr><td><p>* Tokio, JP</p></td><td><p>163</p></td><td><p>48</p></td><td><p>240 %</p></td></tr><tr><td><p>Seoul, KR</p></td><td><p>190</p></td><td><p>190</p></td><td><p>0 %</p></td></tr></table>* Hat einen Azure CDN-Knoten in der gleichen Stadt. * Hat einen Azure CDN-Knoten in einer Nachbarstadt.


## Herausforderungen  

Es gibt verschiedene Herausforderungen, die beim Planen der CDN-Verwendung zu berücksichtigen sind:

- **Bereitstellung** Sie müssen entscheiden, von wo Inhalte für das CDN geladen werden sollen (der Ursprung, aus dem das CDN Inhalte abruft) und ob Sie den Inhalt in mehreren Speichersystemen bereitstellen müssen (z. B. im CDN und an einem alternativen Standort).

- Ihr Anwendungsbereitstellungsmechanismus muss das Bereitstellen statischer Inhalte und Ressourcen sowie das Bereitstellen der Anwendungsdateien, z. B. ASPX-Seiten, berücksichtigen. Beispielsweise kann ein separater Schritt erforderlich sein, um Inhalte in den Azure-Blob-Speicher zu laden.

- **Versionierung und Cachesteuerung** Sie müssen berücksichtigen, wie Sie statische Inhalte aktualisieren und neue Versionen bereitstellen möchten. Das CDN bietet derzeit keinen Mechanismus für das Leeren von Inhalten, sodass neue Versionen verfügbar sind. Dies ist eine ähnliche Herausforderung wie das Verwalten der clientseitigen Zwischenspeicherung, z. B. in einem Webbrowser.

- **Testen** Es kann schwierig sein, lokale Tests Ihrer CDN-Einstellungen durchzuführen, wenn Sie eine Anwendung lokal oder im Staging entwickeln und testen.

- **SEO** Inhalte, wie z. B. Bilder und Dokumente, werden bei der Verwendung des CDN von einer anderen Domäne verarbeitet, was eine Auswirkung auf SEO für diesen Inhalt hat.

- **Sicherheit** Viele CDN-Dienste, wie z. B. Azure CDN, bieten derzeit keinerlei Zugriffssteuerung für den Inhalt an.

- **Resilienz** Das CDN ist eine potenzielle Fehlerquelle für eine Anwendung. Es hat eine niedrigere Verfügbarkeits-SLA als der Blob-Speicher (was zum direkten Bereitstellen von Inhalten verwendet werden kann), sodass Sie möglicherweise einen Fallbackmechanismus für kritische Inhalte implementieren müssen.

- Clients können direkt aus einer Umgebung heraus verbunden sein, die keinen Zugriff auf Ressourcen im CDN zulässt. Dies könnte eine Umgebung mit eingeschränkter Sicherheit sein, in der der Zugriff auf eine Gruppe bekannter Quellen beschränkt ist oder in der das Laden von Ressourcen nur vom Seitenursprung möglich ist. Aus diesem Grund ist eine Fallbackimplementierung erforderlich.

- Sie sollten einen Mechanismus zum Überwachen der Inhaltsverfügbarkeit über das CDN implementieren.

In folgenden Szenarien kann CDN weniger nützlich sein:

- Wenn der Inhalt eine niedrige Trefferrate hat und darum selten oder nur einmal innerhalb des Gültigkeitszeitraums darauf zugegriffen wird. Wenn das erste Mal ein Element heruntergeladen wird, fallen zwei Transaktionsgebühren an (vom Ursprung in das CDN und dann aus dem CDN an den Kunden).

- Wenn die Daten privat sind, z. B. für große Unternehmen oder Versorgungskettenökosysteme.


## Allgemeine Richtlinien und bewährte Verfahren

Das Verwenden des CDN ist eine gute Möglichkeit, um die Belastung Ihrer Anwendung zu minimieren und Verfügbarkeit sowie Leistung zu maximieren. Sie sollten dies für alle entsprechenden Inhalte und Ressourcen, die Ihre Anwendung verwendet, berücksichtigen. Beim Entwerfen einer Strategie zum Verwenden des CDN sollten Sie folgende Punkte berücksichtigen:

- **Ursprung ** Die Bereitstellung von Inhalten über das CDN erfordert die Angabe eines HTTP-Endpunkts (Port 80), den der CDN-Dienst für den Zugriff auf den Inhalt und für die Zwischenspeicherung verwendet. Der Endpunkt kann einen Azure-Blob-Speichercontainer angeben, der den statischen Inhalt enthält, den Sie über das CDN bereitstellen möchten. Der Container muss als öffentlich markiert sein. Nur Blobs in einem öffentlichen Container, die öffentlichen Lesezugriff haben, sind über das CDN verfügbar. 
- Der Endpunkt kann einen Ordner namens **cdn** im Stammverzeichnis einer der Compute-Ebenen der Anwendung (z. B. eine Webrolle oder ein virtueller Computer) angeben. Die Ergebnisse aus Anforderungen für Ressourcen, einschließlich dynamischer Ressourcen wie z. B. ASPX-Seiten, werden im CDN zwischengespeichert. Der minimale Zeitraum zum Zwischenspeichern beträgt 300 Sekunden. Kürzere Zeiträume verhindern, dass der Inhalt im CDN bereitgestellt wird (weitere Informationen finden Sie im Abschnitt <a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Cachesteuerung</a>").

- Bei Verwendung von Azure-Websites wird der Endpunkt durch Auswählen des Standorts beim Erstellen der CDN-Instanz auf den Stammordner der Website festgelegt. Der gesamte Inhalt der Website ist über das CDN verfügbar.

- In den meisten Fällen wird dadurch mehr Flexibilität und Kontrolle bereitgestellt, dass Ihr CDN-Endpunkt auf einen Ordner innerhalb einer der Compute-Ebenen der Anwendung verweist. Auf diese Weise wird zum Beispiel das Verwalten von aktuellen und zukünftigen Routinganforderungen und das dynamische Generieren statischer Inhalte, wie etwa Miniaturansichten, vereinfacht.

- Sie können Abfragezeichenfolgen verwenden, um Objekte im Cache zu unterscheiden, wenn Inhalte von dynamischen Quellen, wie z. B. ASPX-Seiten, bereitgestellt werden. Allerdings kann dieses Verhalten durch eine Einstellung im Verwaltungsportal bei der Angabe des CDN-Endpunkts deaktiviert werden. Bei der Bereitstellung von Inhalten aus dem Blob-Speicher werden Abfragezeichenfolgen als Zeichenfolgenliterale behandelt, sodass zwei Elemente mit dem gleichen Namen aber mit unterschiedlichen Abfragezeichenfolgen als separate Elemente im CDN gespeichert werden.

- Sie können die URL-Umschreibung für Ressourcen wie Skripts und für andere Inhalte nutzen, um zu vermeiden, dass die Dateien in den CDN-Ursprungsordner verschoben werden.

- Wenn Sie Azure-Speicher-Blobs für Inhalte für das CDN verwenden, unterscheidet die URL der Ressourcen in Blobs für den Container und den Blob-Namen nach Groß- und Kleinschreibung.

- Wenn Sie Azure-Websites verwenden, geben Sie den Pfad zur CDN-Instanz in den Links zu Ressourcen an. Das Folgenden gibt beispielsweise eine Bilddatei im Ordner **images** der Website an, die über das CDN bereitgestellt wird:

  ```
  <img src="http://[your-cdn-instance].vo.msecnd.net/Images/image.jpg" />
  ```

- **Bereitstellung** Statische Inhalte müssen möglicherweise unabhängig von der Anwendung bereitgestellt werden, wenn Sie sie nicht in das Anwendungsbereitstellungspaket oder den Anwendungsbereitstellungsprozess einschließen. Berücksichtigen Sie, wie sich dies auf den Versionierungsansatz auswirkt, den Sie zum Verwalten der Anwendungskomponenten und des statischen Ressourceninhalts verwenden.

- Berücksichtigen Sie, wie die Bündelung (Kombinieren mehrerer Dateien in einer Datei) und die Minimierung (Entfernen unnötiger Zeichen wie Leerzeichen, Zeilenumbrüche, Kommentare und andere Zeichen) für Skript- und CSS-Dateien verarbeitet werden. Diese häufig verwendeten Techniken können Ladezeiten für Clients reduzieren und sind mit der Bereitstellung von Inhalten über das CDN kompatibel. Weitere Informationen finden Sie unter [Bündelung und Minimierung](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification).

- Wenn Sie den Inhalt an einem zusätzlichen Speicherort bereitstellen müssen, wird dafür ein zusätzlicher Schritt im Bereitstellungsprozess benötigt. Wenn die Anwendung den Inhalt für das CDN aktualisiert, vielleicht in regelmäßigen Abständen oder als Reaktion auf ein Ereignis, muss sie den aktualisierten Inhalt an allen zusätzlichen Speicherorten sowie auf dem Endpunkt des CDN speichern.

- Sie können keinen CDN-Endpunkt für eine Anwendung, die im Azure-Staging bereitgestellt wird, oder im lokalen Azure-Emulator in Visual Studio einrichten. Dies wirkt sich auf Komponententests, Funktionstests und abschließende Tests vor der Bereitstellung aus. Sie müssen einen alternativen Mechanismus implementieren, um dies zuzulassen. Sie könnten beispielsweise vorab den Inhalt mit einer temporären benutzerdefinierten Anwendung oder einem entsprechenden Programm im CDN bereitstellen und Tests während des Cachezeitraums durchführen. Alternativ können Sie auch Compilerdirektiven oder globale Konstanten verwenden, um zu steuern, von wo die Anwendung die Ressourcen lädt. Bei der Ausführung im Debugmodus können z. B. Ressourcen wie clientseitige Skriptpakete und und andere Inhalte aus einem lokalen Ordner geladen werden, und CDN wird bei der Ausführung im Releasemodus verwendet.

- Das CDN unterstützt keine systemeigenen Komprimierungsfunktionen. Es stellt jedoch bereits komprimierte Inhalte bereit, z. B. ZIP- und GZIP-Dateien. Wenn Sie einen Anwendungsordner als CDN-Endpunkt verwenden, kann der Server einen Teil des Inhalts standardmäßig auf die gleiche Weise wie beim direkten Bereitstellen für einen Webbrowser oder einen anderen Clienttyp komprimieren. Dies hängt vom **Accept-Encoding** Wert ab, der vom Client gesendet wird. In Azure wird standardmäßig Inhalt automatisch komprimiert, wenn die CPU-Auslastung unter 50 % liegt. Das Ändern der Einstellungen kann die Verwendung eines Starttasks erfordern, um die Komprimierung der dynamischen Ausgabe in IIS zu aktivieren, wenn Sie Cloud Services zum Hosten der Anwendung verwenden. Weitere Informationen finden Sie unter [Aktivieren der GZIP-Komprimierung mit Azure CDN über eine Webrolle](http://blogs.msdn.com/b/avkashchauhan/archive/2012/03/05/enableing-gzip-compression-with-windows-azure-cdn-through-web-role.aspx).

- **Routing und Versionsverwaltung** Möglicherweise müssen Sie unterschiedliche CDN-Instanzen verwenden. Wenn Sie z. B. eine neue Version der Anwendung bereitstellen, möchten Sie u. U. ein anderes CDN verwenden. Wenn Sie den Azure-Blob-Speicher als Inhaltsursprung verwenden, können Sie einfach ein separates Speicherkonto oder einen separaten Container erstellen und mit dem CDN-Endpunkt darauf verweisen. Bei Verwendung des **cdn**-Stammordners innerhalb der Anwendung als CDN-Endpunkt können Sie URL-Umschreibungstechniken für direkte Anforderungen an einen anderen Ordner verwenden.

- Verwenden Sie nicht die Abfragezeichenfolge, um unterschiedliche Versionen der Anwendung in Links zu Ressourcen im CDN zu kennzeichnen, da die Abfragezeichenfolge, wenn Inhalte aus dem Azure-Blob-Speicher abgerufen werden, Teil des Ressourcennamens (der Blob-Name) ist. Das kann sich auch auf das Zwischenspeichern von Ressourcen durch den Client auswirken.

- Das Bereitstellen neuer Versionen von statischen Inhalten beim Aktualisieren einer Anwendung kann eine Herausforderung darstellen, wenn die vorherigen Ressourcen im CDN zwischengespeichert sind. Weitere Informationen finden Sie im Abschnitt "<a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">Cachesteuerung</a>".

<a name="cachecontrol" href="#" xmlns:xlink="http://www.w3.org/1999/xlink"><span /></a>**Cachesteuerung**+ Berücksichtigen Sie, wie und auf welchen Anwendungsebenen das Zwischenspeichern verwaltet werden soll. Wenn Sie beispielsweise einen Ordner als CDN-Ursprung verwenden, können Sie die Cachefähigkeit von Seiten, die Inhalte generieren, und den Inhaltsablauf für alle Ressourcen in einem bestimmten Ordner angeben. Sie können auch Cacheeigenschaften für das CDN und für den Client mithilfe von standardmäßigen HTTP-Headern angeben. Obwohl Sie das Zwischenspeichern wahrscheinlich bereits auf dem Server und dem Client verwalten, kann das CDN Sie darauf aufmerksam machen, wie Ihre Inhalte wo zwischengespeichert werden.

- Um zu verhindern, dass Objekte im CDN verfügbar sind, können Sie sie aus dem Ursprung (Blob-Container oder **cdn**-Stammordner der Anwendung) entfernen oder den CDN-Endpunkt löschen. Sie können den Container oder Blob aber auch im Falle des Blob-Speichers privat machen. Allerdings werden Elemente nur aus dem CDN entfernt, wenn ihre Lebenszeit (Time-To-Live, TTL) abläuft.

- Wenn keine Cachegültigkeitsdauer angegeben wird (z. B. wenn der Inhalt aus dem Blob-Speicher geladen wird), wird er bis zu 72 Stunden lang im CDN zwischengespeichert.

- In einer Webanwendung können Sie die Zwischenspeicherung und den Ablauf für den gesamten Inhalt mit dem **clientCache**-Element im Abschnitt **system.webServer/staticContent** einer web.config-Datei festlegen. Sie können eine web.config-Datei in einem beliebigen Ordner platzieren, damit sie sich auf alle Dateien in diesem Ordner und allen Unterordnern auswirkt.

- Wenn Sie eine dynamische Technik wie ASP.NET verwenden, um den Inhalt für das CDN zu erstellen, stellen Sie sicher, dass Sie die **Cache.SetExpires**-Eigenschaft auf jeder Seite angeben. Das CDN speichert die Ausgabe von Seiten, die die öffentliche Standardeinstellung für den Cache verwenden, nicht zwischen. Legen Sie den Cacheablaufzeitraum auf einen passenden Wert fest, um sicherzustellen, dass der Inhalt nicht verworfen, sondern in sehr kurzen Intervallen aus der Anwendung neu geladen wird.

- **Sicherheit** Das CDN kann Inhalte über HTTPS (SSL) mithilfe des vom CDN bereitgestellten Zertifikats bereitstellen. Die Inhalte sind aber auch über HTTP verfügbar. Sie können den HTTP-Zugriff auf Elemente im CDN nicht blockieren. Möglicherweise müssen Sie HTTPS verwenden, um statische Inhalte anzufordern, die auf über HTTPS geladenen Seiten angezeigt werden (zum Beispiel ein Einkaufswagen), um Browserwarnungen über vermischte Inhalte zu vermeiden.

- Viele CDN-Dienste, wie z. B. das Azure CDN, bieten derzeit keine Funktionen für die Zugriffssteuerung, um für einen sicheren Zugriff auf den Inhalt zu sorgen. Sie können keine Shared Access Signatures (SAS) mit dem CDN verwenden.

- Wenn Sie clientseitige Skripts mit dem CDN bereitstellen, stoßen Sie möglicherweise auf Probleme, wenn diese Skripts einen **XMLHttpRequest**-Aufruf verwenden, um HTTP-Anforderungen für andere Ressourcen, wie z. B. Daten, Bilder oder Schriftarten in einer anderen Domäne, auszuführen. Viele Webbrowser verhindern CORS (Cross-Origin Resource Sharing, ursprungsübergreifende Ressourcenfreigabe), sofern der Webserver nicht so konfiguriert wurde, dass die entsprechenden Antwortheader festgelegt werden. Weitere Informationen zu CORS finden Sie im Abschnitt "Bedrohungsminimierung" im Handbuch zu <span class="highlight" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">API-Sicherheitsüberlegungen</span>. Sie können das CDN für die Unterstützung von CORS konfigurieren. Falls der Ursprung, aus dem Sie Inhalte bereitstellen, ein Azure-Blob-Speicher ist, können Sie eine **CorsRule** zu den Diensteigenschaften hinzufügen. Die Regel kann die zulässigen Ursprünge für CORS-Anforderungen, die zulässigen Methoden wie z. B. GET und das maximale Alter in Sekunden für die Regel angeben (der Zeitraum, in dem der Client die verknüpften Ressourcen nach dem Laden des ursprünglichen Inhalts anfordern muss). Weitere Informationen finden Sie unter [Cross-Origin Resource Sharing (CORS)-Support für die Azure-Speicherdienste](http://msdn.microsoft.com/library/azure/dn535601.aspx).

- Falls der Ursprung, aus dem Sie Inhalte bereitstellen, ein Ordner innerhalb der Anwendung ist, zum Beispiel der **cdn**-Stammordner, können Sie ausgehende Regeln in der Anwendungskonfigurationsdatei konfigurieren, um einen **Access-Control-Allow-Origin**-Header für alle Antworten festzulegen. Weitere Informationen zum Verwenden der Regeln für das Umschreiben finden Sie im Thema zum [URL-Umschreibemodul](http://www.iis.net/learn/extensions/url-rewrite-module). Beachten Sie, dass diese Technik für Azure-Websites nicht möglich ist.

- **Benutzerdefinierte Domänen** Die meisten CDNs, auch das Azure-CDN, ermöglichen Ihnen das Angeben eines benutzerdefinierten Domänennamens und die Verwendung des Namens zum Zugriff auf Ressourcen über das CDN. Sie können einen benutzerdefinierten Unterdomänennamen auch mit dem **CNAME**-Eintrag in DNS einrichten. Mit diesem Ansatz erhalten Sie eine zusätzliche Abstraktions- und Kontrollebene.

- Wenn Sie einen **CNAME** verwenden, können Sie (zum Zeitpunkt des Verfassens dieser Anleitung) nicht gleichzeitig SSL verwenden, da das CDN ein eigenes einzelnes SSL-Zertifikat verwendet, das nicht zu Ihren benutzerdefinierten Domänen-/Unterdomänennamen passt.

- **CDN-Fallback** Sie sollten überlegen, wie Ihre Anwendung einen Fehler oder einen vorübergehenden Ausfall des CDN verarbeitet. Clientanwendungen können möglicherweise Kopien der Ressourcen verwenden, die lokal (auf dem Client) bei früheren Anforderungen zwischengespeichert wurden, oder Sie können Code verwenden, der Ausfälle erkennt und stattdessen Ressourcen vom Ursprung anfordert (der Anwendungsordner oder Azure-Blob-Container, der die Ressourcen enthält), wenn das CDN nicht verfügbar ist.

- **SEO** Falls SEO in Ihrer Anwendung ein wichtiger Aspekt ist, stellen sicher, dass ein kanonischer **Rel**-Header für jede Seite oder Ressource eingeschlossen wird.

- Verwenden Sie einen **CNAME**-Unterdomäneneintrag, und greifen Sie auf die Ressourcen mit diesem Namen zu.

- Berücksichtigen Sie die Auswirkungen der Tatsache, dass die IP-Adresse des CDN möglicherweise ein Land oder eine Region darstellt, das bzw. die sich von der Anwendung unterscheidet.

- Wenn Sie den Azure-Blob-Speicher als Ursprung verwenden, behalten Sie die gleiche Dateistruktur für Ressourcen im CDN wie in den Anwendungsordnern bei.


- **Überwachung und Protokollierung** Schließen Sie CDN als Teil Ihrer Anwendungsüberwachungsstrategie zum Erkennen und Messen von Ausfällen oder längeren hohen Latenzzeiten ein.

- Aktivieren Sie die Protokollierung für das CDN, und fügen Sie sie Ihren täglichen Aufgaben hinzu.

- **Kostenauswirkungen** Ihnen werden Gebühren für ausgehende Datenübertragungen aus dem CDN und für Speichertransaktionen in Rechnung gestellt, wenn das CDN Daten aus Ihrer Anwendung lädt. Sie sollten realistische Cacheablaufzeiträume für Inhalte festlegen, um Aktualität sicherzustellen, aber keine so kurzen Zeiträume auswählen, dass Inhalte wiederholt aus der Anwendung oder dem Blob-Speicher in das CDN geladen werden müssen. Allerdings machen es sehr lange Ablaufzeiträume schwieriger, Elemente aus dem CDN zu entfernen, da Sie warten müssen, bis diese abgelaufen sind.

- Auch Elemente, die nur selten heruntergeladen werden, erhöhen die Transaktionsgebühren, ohne dass die Serverauslastung deutlich reduziert wird.



## Beispielcode
Dieser Abschnitt enthält einige Beispiele für Code und Techniken für die Arbeit mit dem CDN.


## URL-Umschreibung
Der folgende Ausschnitt aus einer Web.config-Datei im Stammverzeichnis einer von Clouddiensten gehosteten Anwendung veranschaulicht das Umschreiben von URLs bei Verwendung des CDN. Anforderungen vom CDN für Inhalte, die zwischenspeichert werden, werden an bestimmte Ordner innerhalb des Anwendungsstammverzeichnisses basierend auf dem Ressourcentyp (zum Beispiel Skripts und Bilder) umgeleitet.

```XML
<system.webServer>
  ...
  <rewrite>
    <rules>
      <rule name="VersionedResource" stopProcessing="false">
        <match url="(.*)_v(.*).(.*)" ignoreCase="true" />
        <action type="Rewrite" url="{R:1}.{R:3}" appendQueryString="true" />
      </rule>
      <rule name="CdnImages" stopProcessing="true">
        <match url="cdn/Images/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Images/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnContent" stopProcessing="true">
        <match url="cdn/Content/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Content/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScript" stopProcessing="true">
        <match url="cdn/Scripts/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Scripts/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScriptBundles" stopProcessing="true">
        <match url="cdn/bundles/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/bundles/{R:1}" appendQueryString="true" />
      </rule>
    </rules>
  </rewrite>
  ...
</system.webServer>
```

Durch das Hinzufügen der Umschreiberegeln werden folgende Umleitungen ausgeführt:

- Die erste Regel ermöglicht Ihnen das Einbetten einer Version in den Dateinamen einer Ressource, die dann ignoriert wird. **Dateiname_v123.jpg ** wird zum Beispiel in **Dateiname.jpg** umgeschrieben. 
- Die nächsten vier Regeln zeigen das Umleiten von Anforderungen, wenn Sie die Ressourcen nicht in einem Ordner namens **cdn** im Stammverzeichnis der Webrolle speichern möchten. Die Regeln ordnen die URLs **cdn/Images**, **cdn/Content**, **cdn/Scripts** und **cdn/bundles** URLs ihren jeweiligen Stammordnern in der Webrolle zu. Zum Verwenden der URL-Umschreibung müssen Sie einige Änderungen an der Bündelung von Ressourcen vornehmen.


## Bündelung und Minimierung ##

Bündelung und Minimierung können von ASP.NET verarbeitet werden. In einem MVC-Projekt definieren Sie Ihre Pakete in **BundleConfig.cs**. Ein Verweis auf das minimierte Skriptbündel wird durch den Aufruf der **Script.Render**-Methode, normalerweise im Code in der Ansichtsklasse, erstellt. Diese Referenz enthält eine Abfragezeichenfolge mit einem Hash, der auf dem Inhalt des Bündels basiert. Wenn sich der Bündelinhalt ändert, wird auch der generierte Hash geändert. Standardmäßig ist für Azure CDN-Instanzen die Einstellung für den **Status der Abfragezeichenfolge** deaktiviert. Damit aktualisierte Skriptbündel ordnungsgemäß vom CDN verarbeitet werden, müssen Sie die Einstellung für den **Status der Abfragezeichenfolge** für die CDN-Instanz aktivieren. Beachten Sie, dass es möglicherweise länger als eine Stunde dauern kann, bevor das CDN erstellt wird und die Änderungen an den Einstellungen wirksam werden.


## Weitere Informationen
+ [Azure CDN](http://azure.microsoft.com/services/cdn/)
+ [Übersicht über das Azure Content Delivery Network (CDN)](http://msdn.microsoft.com/library/azure/ff919703.aspx)
+ [Verarbeiten von Inhalt aus Azure CDN in einer Webanwendung](cdn-serve-content-from-cdn-in-your-web-application.md)
+ [Integrieren eines Clouddiensts in Azure CDN](cdn-cloud-service-with-cdn.md)
+ [Bewährte Methoden für das Azure Content Delivery Network](http://azure.microsoft.com/blog/2011/03/18/best-practices-for-the-windows-azure-content-delivery-network/)

<!---HONumber=62-->