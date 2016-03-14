<properties 
	pageTitle="Integrieren einer App in ein Azure Virtual Network" 
	description="Sie erfahren, wie Sie eine Verbindung zwischen einer App in Azure App Service und einem neuen oder vorhandenen Azure Virtual Network herstellen." 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor="cephalin"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/22/2016" 
	ms.author="ccompy"/>

# Integrieren Ihrer App in ein Azure Virtual Network #

In diesem Dokument wird die Azure App Service-Funktion für die Integration in ein Virtual Network beschrieben und veranschaulicht, wie Sie diese mit Apps in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) einrichten. Falls Sie sich mit Azure Virtual Networks (VNETs) noch nicht auskennen, hilft Ihnen vielleicht diese Beschreibung weiter: Es handelt sich um eine Funktion, mit der Sie viele Azure-Ressourcen in einem nicht über das Internet routbaren Netzwerk anordnen können, für das Sie den Zugriff kontrollieren. Diese Netzwerke können dann mit verschiedenen VPN-Technologien mit Ihren lokalen Netzwerken verbunden werden. Beginnen Sie mit dem folgenden Thema, um weitere Informationen zu Azure Virtual Networks zu erhalten: [Übersicht über Azure Virtual Network][VNETOverview].

Es gibt zwei Methoden, um Azure App Service zu nutzen. Die erste und am häufigsten verwendete Methode umfasst die mehrinstanzenfähigen Systeme, die den gesamten Bereich der Tarife unterstützen. Die zweite Methode ist die Premium-Funktion „App Service-Umgebung“ (App Service Environment, ASE), die im VNET von Kunden bereitgestellt wird. Bei einer App Service-Umgebung müssen Sie normalerweise keine VNET-Integration verwenden, da sich das System bereits in Ihrem VNET befindet und Zugriff auf alle Ressourcen des VNET hat. Es gibt eigentlich nur einen Grund, warum Sie die Funktion für die VNET-Integration noch für ein ASE verwenden können: Wenn Sie auf Ressourcen in einem anderen VNET zugreifen möchten, das nicht mit dem VNET verbunden ist, in dem Ihre ASE gehostet wird.

Die VNET-Integration ermöglicht Ihrer Web-App den Zugriff auf Ressourcen in Ihrem virtuellen Netzwerk, gewährt aber keinen privaten Zugriff auf Ihre Web-App aus dem virtuellen Netzwerk. Ein häufiges Szenario für die Verwendung dieses Features besteht darin, für Ihre Web-App den Zugriff auf eine Datenbank oder Webdienste zu ermöglichen, die in Ihrem Azure Virtual Network auf einer virtuellen Maschine ausgeführt werden. Bei der VNET-Integration müssen Sie keinen öffentlichen Endpunkt für Anwendungen auf Ihrer VM verfügbar machen, sondern Sie können stattdessen die privaten nicht über das Internet routbaren Adressen verwenden.

Für die Funktion für die VNET-Integration gilt Folgendes:

- Standard- oder Premium-Tarif erforderlich 
- Funktioniert derzeit nur mit V1- oder klassischen VNETs 
- TCP und UDP wird unterstützt
- Funktioniert mit Web-Apps, Mobile Apps und API-Apps
- Eine App kann nur jeweils mit einem VNET eine Verbindung herstellen
- Bis zu fünf VNETs können in einen App Service-Plan integriert werden 
- Ein VNET kann im Rahmen eines App Service-Plans von mehreren Apps verwendet werden
- Unterstützt dank der Nutzung des VNET-Gateways ein SLA mit einer Verfügbarkeit von 99,9 %

Einige Dinge werden von der VNET-Integration nicht unterstützt, z. B.:

- Bereitstellung eines Laufwerks
- AD-Integration 
- NetBios
- Privater Websitezugriff

### Erste Schritte ###
Beachten Sie Folgendes, bevor Sie Ihre Web-App mit einem virtuellen Netzwerk verbinden:

- Die VNET-Integration funktioniert nur mit Apps mit dem Tarif **Standard** oder **Premium**. Wenn Sie die Funktion aktivieren und Ihren App Service-Plan dann auf einen nicht unterstützten Tarif skalieren, verlieren Ihre Apps ihre Verbindungen mit den genutzten VNETs.  
- Wenn Ihr virtuelles Zielnetzwerk bereits vorhanden ist, muss es über ein Punkt-zu-Standort-VPN mit einem dynamischen Routing-Gateway aktiviert werden, bevor es mit einer App verbunden werden kann. Sie können Punkt-zu-Standort-VPN (Virtual Private Network) nicht aktivieren, wenn Ihr Gateway mit statischem Routing konfiguriert ist.
- Das VNET muss sich in demselben Abonnement wie Ihr App Service-Plan (ASP) befinden.  
- Die Apps, die in ein VNET integriert sind, nutzen das DNS, das für das VNET angegeben ist.
- Standardmäßig leiten die integrierten Apps Datenverkehr nur basierend auf den Routen, die im VNET definiert sind, in Ihr VNET weiter.  


## Aktivieren der VNET-Integration ##

Sie haben die Möglichkeit, sich mit einem neuen oder bereits vorhandenen virtuellen Netzwerk zu verbinden. Wenn Sie ein neues Netzwerk erstellen, wird zusätzlich zur alleinigen Erstellung des VNET ein Gateway mit dynamischem Routing für Sie vorkonfiguriert, und das Punkt-zu-Standort-VPN wird aktiviert.

>[AZURE.NOTE] Das Konfigurieren einer neuen Integration eines Virtual Network kann mehrere Minuten dauern.

Öffnen Sie zum Aktivieren der VNET-Integration die App-Einstellungen, und wählen Sie „Netzwerk“. Auf der Benutzeroberfläche, die geöffnet wird, werden drei Netzwerkoptionen zur Auswahl angeboten. In diesem Leitfaden geht es hauptsächlich um die VNET-Integration, aber auf Hybridverbindungen und App Service-Umgebungen wird später in diesem Dokument eingegangen.

Wenn Ihre App nicht den richtigen Tarif aufweist, können Sie die Benutzeroberfläche verwenden, um Ihren Tarif auf einen höheren Tarif Ihrer Wahl zu skalieren.


![][1]
 
###Aktivieren der VNET-Integration mit einem bereits vorhandenen VNET###
Die Benutzeroberfläche der VNET-Integration ermöglicht Ihnen die Auswahl aus einer Liste mit V1 VNETs. In der Abbildung unten sehen Sie, dass nur ein VNET ausgewählt werden kann. Es gibt mehrere Gründe dafür, warum ein VNET abgeblendet sein kann, z. B.:

- Das VNET befindet sich in einem anderen Abonnement, auf das Ihr Konto Zugriff hat.
- Für das VNET ist „Punkt-zu-Standort“ nicht aktiviert.
- Das VNET hat kein Gateway für dynamisches Routing.

Beachten Sie auch Folgendes: Da die Integration in V2 VNETs noch nicht unterstützt wird, werden sie nicht aufgeführt.

![][2]

Klicken Sie zum Aktivieren der Integration einfach auf das VNET, das Sie für die Integration verwenden möchten. Nach dem Auswählen des VNET wird Ihre App automatisch neu gestartet, damit die Änderungen wirksam werden.

Wenn Ihr VNET kein Gateway aufweist und auch nicht über „Punkt-zu-Standort“ verfügt, müssen Sie dies zuerst einrichten. Rufen Sie hierfür im Azure-Portal die Liste mit den Virtual Networks (klassisch) auf. Klicken Sie darin auf das Netzwerk, das Sie für die Integration verwenden möchten, und klicken Sie unter „Essentials“ in das große Feld mit der Bezeichnung „VPN-Verbindungen“. Hier können Sie Ihr Punkt-zu-Standort-VPN erstellen und dafür sogar ein Gateway erstellen lassen. Nachdem Sie die Schritte für die Erstellung des Punkt-zu-Standort-VPN mit Gateway ausgeführt haben, dauert es ca. 30 Minuten, bevor es betriebsbereit ist.

![][8]

### Erstellen eines VNET und Durchführen der Integration ###
Wenn Sie ein neues VNET erstellen möchten, sollten Sie daran danken, dass Sie derzeit nur ein V1 VNET oder ein klassisches VNET erstellen können. Zum Erstellen eines V1 VNET über die Benutzeroberfläche für die VNET-Integration wählen Sie einfach die Option „Neues virtuelles Netzwerk erstellen“ und geben den Namen an, den Sie für das VNET und den dazugehörigen Adressraum verwenden möchten.

Beachten Sie folgende Warnung: Wenn Sie für dieses VNET eine Verbindung mit einem Ihrer anderen Netzwerke herstellen möchten, sollten Sie die Auswahl eines IP-Adressbereichs vermeiden, der sich mit diesen Netzwerken überlappt.

>[AZURE.NOTE] Es kann bis zu 30 Minuten dauern, bis ein neues VNET mit funktionierenden Gateways bereitgestellt wird. Wenn der Vorgang abgeschlossen ist, wird die Benutzeroberfläche aktualisiert.

![][3]

Azure-VNETs werden normalerweise innerhalb der Adressbereiche privater Netzwerke erstellt. Standardmäßig leitet die Funktion für die VNET-Integration den gesamten Datenverkehr, der für diese IP-Adressbereiche bestimmt ist, in Ihr VNET weiter. Die privaten IP-Adressbereiche lauten:

- 10\.0.0.0/8: entspricht 10.0.0.0 - 10.255.255.255
- 172\.16.0.0/12: entspricht 172.16.0.0 - 172.31.255.255 
- 192\.168.0.0/16: entspricht 192.168.0.0 - 192.168.255.255
 
Der VNET-Adressraum muss per CIDR-Notation angegeben werden. Falls Sie mit der CIDR-Notation nicht vertraut sind, hilft Ihnen vielleicht diese Beschreibung weiter: Es handelt sich dabei um ein Verfahren zum Angeben von Adressblöcken mit einer IP-Adresse und einer Ganzzahl, die für die Netzwerkmaske steht. Hinweis: 10.1.0.0/24 ergibt 256 Adressen, und 10.1.0.0/25 ergibt 128 Adressen. Eine IPv4-Adresse mit „/32“ ergibt nur eine Adresse.

Wenn Sie die DNS-Serverinformationen hier angeben, werden diese für Ihr VNET festgelegt. Nach der Erstellung des VNET können Sie diese Informationen über die VNET-Benutzeroberflächen bearbeiten.

Wenn Sie mit der Benutzeroberfläche für die VNET-Integration ein V1 VNET erstellen, wird ein VNET in derselben Ressourcengruppe wie Ihre App erstellt.

## Wie das System funktioniert ##
Im Hintergrund baut diese Funktion auf Punkt-zu-Standort-VPN-Technologie auf, um die App mit Ihrem VNET zu verbinden. Apps in Azure App Service nutzen eine mehrinstanzenfähige Systemarchitektur, die eine Bereitstellung einer App direkt in einem VNET, wie dies bei virtuellen Maschinen der Fall ist, ausschließt. Durch die Verwendung der Punkt-zu-Standort-Technologie als Grundlage beschränken wir den Netzwerkzugriff auf die virtuelle Maschine, auf dem die App gehostet wird. Der Zugriff auf das Netzwerk bleibt auf diese App-Hosts beschränkt, sodass Ihre Apps nur auf Netzwerke zugreifen können, für die Sie den Zugriff konfiguriert haben.

![][4]
 
Wenn Sie keinen DNS-Server mit Ihrem virtuellen Netzwerk konfiguriert haben, müssen Sie eine IP-Adresse verwenden. Beachten Sie beim Verwenden von IP-Adressen, dass der Hauptvorteil dieser Funktion darin besteht, dass Sie damit in Ihrem privaten Netzwerk private Adressen nutzen können. Wenn Sie Ihre App so einrichten, dass sie öffentliche IP-Adressen für eine Ihrer VMs verwendet, nutzen Sie die Funktion für die VNET-Integration nicht und kommunizieren über das Internet.



##Verwalten von VNET-Integrationen##

Die Möglichkeit, eine Verbindung mit einem VNET herzustellen und zu trennen, ist auf der App-Ebene angesiedelt. Vorgänge, die sich auf die VNET-Integration über mehrere Apps auswirken können, sind auf Ebene des App Service-Plans angesiedelt. Über die Benutzeroberfläche, die auf App-Ebene angezeigt wird, können Sie die Details Ihres VNET anzeigen. Die meisten Informationen werden auch auf Ebene des App Service-Plans angezeigt.

![][5]

Auf der Seite „Netzwerkfunktionsstatus“ können Sie sehen, ob für die App eine Verbindung mit Ihrem VNET besteht. Falls Ihr VNET-Gateway ausgefallen ist, wird der Status „Nicht verbunden“ angezeigt.

Die Informationen, die für Sie jetzt auf der Benutzeroberfläche für die VNET-Integration auf App-Ebene verfügbar sind, entsprechen den Detailinformationen, die Sie über den App Service-Plan erhalten. Dies sind die folgenden Elemente:

- VNET-Name: Mit diesem Link öffnen Sie die Benutzeroberfläche des Netzwerks.
- Standort: Gibt den Standort Ihres VNET an. Es ist möglich, ein VNET in einen anderen Standort zu integrieren.
- Zertifikatstatus: Es sind Zertifikate vorhanden, die zum Schützen der VPN-Verbindung zwischen der App und dem VNET verwendet werden. Mit dieser Funktion wird ein Test durchgeführt, um sicherzustellen, dass beide synchron sind.
- Gatewaystatus: Wenn die Gateways ausgefallen sind, kann Ihre App nicht auf Ressourcen im VNET zugreifen.  
- VNET-Adressraum: Dies ist der Adressraum für Ihr VNET.  
- Punkt-zu-Standort-Adressraum: Dies ist der Punkt-zu-Standort-IP-Adressraum für Ihr VNET. Ihre App zeigt an, dass die Kommunikation von einer der IP-Adressen in diesem Adressraum stammt.  
- Standort-zu-Standort-Adressraum: Sie können Standort-zu-Standort-VPNs verwenden, um für Ihr VNET eine Verbindung mit lokalen Ressourcen oder den anderen VNETs herzustellen. Falls Sie dies konfiguriert haben, werden die IP-Bereiche, die für die jeweilige VPN-Verbindung definiert wurden, hier angezeigt.
- DNS-Server: Wenn Sie für Ihr VNET DNS-Server konfiguriert haben, werden diese hier aufgeführt.
- IPs mit Routing zum VNET: Es ist eine Liste mit IP-Adressen vorhanden, für die im VNET das Routing definiert ist. Diese Adressen werden hier angezeigt.  

Der einzige Vorgang, den Sie in der App-Ansicht Ihrer VNET-Integration durchführen können, ist das Trennen Ihrer App von dem VNET, mit dem derzeit eine Verbindung besteht. Klicken Sie hierzu oben einfach auf „Trennen“. Durch diese Aktion wird Ihr VNET nicht beeinträchtigt. Das VNET und dessen Konfiguration, einschließlich der Gateways, bleibt unverändert. Wenn Sie Ihr VNET dann löschen möchten, müssen Sie zuerst die darin enthaltenen Ressourcen löschen, z. B. auch die Gateways.

Die Ansicht „App Service-Plan“ enthält einige zusätzliche Vorgänge. Außerdem erfolgt der Zugriff anders als über die App. Öffnen Sie einfach die Benutzeroberfläche des App Service-Plans, und scrollen Sie nach unten, um zur ASP-Netzwerk-Benutzeroberfläche zu gelangen. Darin ist das Benutzeroberflächenelement „Netzwerkfunktionsstatus“ enthalten. Hier sind einige Details Ihrer VNET-Integration angegeben. Wenn Sie auf dieses Benutzeroberflächenelement klicken, wird der Bereich „Netzwerkfunktionsstatus“ geöffnet. Wenn Sie dann auf „Zum Verwalten hier klicken“ klicken, wird die Benutzeroberfläche angezeigt, auf der die VNET-Integrationen dieses ASP angezeigt werden.

![][6]

Wenn Sie sich die Standorte der VNETs für die Integration ansehen, können Sie den ASP-Standort gut erkennen. Wenn sich das VNET an einem anderen Standort befindet, treten eher Probleme mit der Latenzzeit auf.

„VNET-Integration: Anzahl“ gibt an, in wie viele VNETs Ihre Apps unter diesem ASP integriert sind und wie viele möglich sind.

Klicken Sie einfach auf das gewünschte VNET, um weitere Informationen dazu anzuzeigen. Zusätzlich zu den oben erwähnten Details wird auch eine Liste der Apps des ASP angezeigt, die dieses VNET nutzen.

In Bezug auf Aktionen gibt es zwei Hauptaktionen. Erstens die Möglichkeit, Routen hinzuzufügen, auf denen Datenverkehr von der App in Ihr VNET geleitet wird. Zweitens die Möglichkeit, Zertifikate und Netzwerkinformationen zu synchronisieren.

![][7]

**Routing** Wie bereits erwähnt werden die Routen, die in Ihrem VNET definiert sind, zum Leiten des Datenverkehrs aus der App in Ihr VNET verwendet. Es kann aber sein, dass Kunden weiteren ausgehenden Datenverkehr aus einer App in das VNET senden möchten. Hierfür ist diese Funktion bestimmt. Was anschließend mit dem Datenverkehr passiert, richtet sich danach, wie der Kunde das VNET konfiguriert.

**Zertifikate** Mit dem Zertifikatstatus wird angegeben, dass von App Service überprüft wird, ob die für die VPN-Verbindung verwendeten Zertifikate noch geeignet sind. Wenn es sich für Apps des ASP um die erste Integration in das VNET handelt, ist bei aktivierter VNET-Integration ein Zertifikataustausch erforderlich, um die Sicherheit der Verbindung sicherzustellen. Zusammen mit den Zertifikaten erhalten wir die DNS-Konfiguration, Routen und anderen Elemente, mit denen das Netzwerk beschrieben wird. Wenn diese Zertifikate oder Netzwerkinformationen geändert werden, müssen Sie auf „Netzwerk synchronisieren“ klicken. **HINWEIS**: Wenn Sie auf „Netzwerk synchronisieren“ klicken, bewirkt dies einen kurzen Ausfall der Verbindung zwischen der App und dem VNET. Die App wird nicht neu gestartet, aber der Verbindungsverlust kann dazu führen, dass Ihre Website nicht richtig funktioniert.

##Zugriff auf Betriebsressourcen##

Einer der Vorteile der Funktion für die VNET-Integration ist, dass für Ihre Apps Zugriff auf Ihre lokalen Ressourcen bestehen kann, sofern das VNET per Standort-zu-Standort-VPN mit Ihrem lokalen Netzwerk verbunden ist. Damit dies funktioniert, müssen Sie unter Umständen das lokale VPN Gateway mit den Routen für den Punkt-zu-Standort-IP-Bereich aktualisieren. Nach dem ersten Einrichten des Standort-zu-Standort-VPN sollten mit den Skripts, die für die Konfiguration verwendet werden, Routen eingerichtet werden, z. B. das Punkt-zu-Standort-VPN. Wenn Sie das Punkt-zu-Standort-VPN nach dem Erstellen des Standort-zu-Standort-VPN erstellen, müssen Sie die Routen manuell aktualisieren. Die Details zur Vorgehensweise variieren je nach Gateway und sind hier nicht beschrieben.

>[AZURE.NOTE] Die Funktion für die VNET-Integration funktioniert mit einem Standort-zu-Standort-VPN, was den Zugriff auf lokale Ressourcen betrifft, aber mit einem ExpressRoute-VPN ist dies derzeit nicht möglich.

##Preisübersicht##
Bei der Verwendung der Funktion für die VNET-Integration sollten Sie einige Details in Bezug auf die Preise beachten. Die Nutzung der Funktion ist mit drei Gebühren verbunden:

- ASP-Tarifanforderungen
- Datenübertragungskosten
- Kosten für VPN Gateway

Damit Ihre Apps diese Funktion verwenden können, müssen sie im Rahmen des App Service-Plans „Standard“ oder „Premium“ genutzt werden. Weitere Informationen zu diesen Kosten finden Sie hier: [App Service-Preisdetails][ASPricing].

Aufgrund der Art und Weise, wie Punkt-zu-Standort-VPNs behandelt werden, fällt für Sie immer eine Gebühr für ausgehende Daten über die Verbindung der VNET-Integration an. Dies gilt auch, wenn sich das VNET in demselben Rechenzentrum befindet. Sie können sich hier über diese Gebühren informieren: [Datenübertragungen – Preisdetails][DataPricing].

Das letzte Element sind die Kosten für die VNET-Gateways. Wenn Sie die Gateways nicht für etwas anderes benötigen, z. B. Standort-zu-Standort-VPNs, bezahlen Sie für Gateways als Unterstützung der Funktion für die VNET-Integration. Details zu diesen Kosten finden Sie hier: [VPN Gateway – Preise][VNETPricing].

##Problembehandlung##

Die Funktion ist zwar einfach einzurichten, aber dies bedeutet nicht, dass für Ihre Benutzeroberfläche keinerlei Probleme auftreten. Falls beim Zugreifen auf den gewünschten Endpunkt Probleme auftreten, können Sie einige Hilfsprogramme verwenden, um die Verbindung über die App-Konsole zu testen. Sie können zwei Konsolenbenutzeroberflächen verwenden. Eine Möglichkeit ist die Kudu-Konsole, und auf die andere Konsole können Sie über das Azure-Portal zugreifen. Greifen Sie in der App auf „Tools -> Kudu“ zu, um zur Kudu-Konsole zu gelangen. Dies entspricht dem Zugreifen auf „[Websitename].scm.azurewebsites.net“. Wechseln Sie nach dem Öffnen einfach zur Registerkarte „Debuggen“ der Konsole. Um auf die über das Azure-Portal gehostete Konsole zuzugreifen, greifen Sie in der App auf „Tools“ -> „Konsole“ zu.


####Tools####

Die Tools „ping“, „nslookup“ und „tracert“ funktionieren aufgrund von Sicherheitseinschränkungen nicht über die Konsole. Es wurden zwei separate Tools hinzugefügt, um diese Lücke zu füllen. Zum Testen der DNS-Funktionalität haben wir ein Tool mit dem Namen „nameresolver.exe“ hinzugefügt. Die Syntax ist:

    nameresolver.exe hostname [optional: DNS Server]

Sie können „nameresolver“ verwenden, um die Hostnamen zu überprüfen, von denen Ihre App abhängig ist. So können Sie testen, ob für das DNS etwas falsch konfiguriert ist oder ob ggf. kein Zugriff auf Ihren DNS-Server besteht.

Mit dem nächsten Tool können Sie die TCP-Verbindung mit einer Host/Port-Kombination testen. Dieses Tool hat den Namen „tcpping.exe“ und verfügt über die folgende Syntax:

    tcpping.exe hostname [optional: port]

Mit diesem Tool können Sie ermitteln, ob Sie einen bestimmten Host und Port erreichen können. Es bietet aber nicht die gleiche Leistung wie das Hilfsprogramm „ping“ auf ICMP-Basis. Mit dem ICMP-Hilfsprogramm „ping“ können Sie ermitteln, ob der Host ausgeführt wird. Mit „tcpping“ können Sie herausfinden, ob Sie auf einen bestimmten Port auf einem Host zugreifen können.


####Debuggen des Zugriffs auf im VNET gehostete Ressourcen####

Es kann verschiedene Ursachen haben, warum Ihre App einen bestimmten Host und Port nicht erreichen kann. Beginnen Sie mit den einfachen Punkten, um das Problem aufzuschlüsseln, z. B.:

- Wird im Portal angezeigt, dass das Gateway ausgeführt wird?
- Wird angezeigt, dass die Zertifikate synchron sind?
- Wurde die Netzwerkkonfiguration geändert, ohne für die betroffenen ASPs die Funktion „Netzwerk synchronisieren“ auszuführen?

Fahren Sie das Gateway hoch, wenn es heruntergefahren ist. Falls Ihre Zertifikate nicht synchron sind, müssen Sie in der ASP-Ansicht Ihrer VNET-Integration die Funktion „Netzwerk synchronisieren“ ausführen. Falls Sie vermuten, dass eine Änderung an der VNET-Konfiguration vorgenommen und keine Synchronisierung mit den ASPs durchgeführt wurde, greifen Sie auf die ASP-Ansicht der VNET-Integration zu, und führen Sie „Netzwerk synchronisieren“ aus. Zur Erinnerung: Hierbei kommt es zu einem kurzen Ausfall der VNET-Verbindung und Ihrer Apps.

Wenn dies nicht die Lösung des Problems ist, müssen Sie weiterforschen:

- Sind andere Apps vorhanden, von denen dieses VNET zum Erreichen einer Remoteressource verwendet wird? 
- Können Sie in der App-Konsole „tcpping“ verwenden, um Ressourcen in Ihrem VNET zu erreichen?  

Wenn Sie beide obigen Fragen mit „ja“ beantworten können, liegt für Ihre VNET-Integration kein Problem vor. Das Problem hat also eine andere Ursache. Es kann auch einfach sein, dass Sie für die beiden obigen Elemente keine Antwort erhalten, weil Ihr VNET keine anderen Ziele enthält. Dieser Fall ist etwas schwieriger zu lösen, weil Sie nicht leicht ermitteln können, warum eine host:port-Kombination nicht erreichbar ist. Mögliche Ursachen sind:

- Zielhost ist ausgefallen
- Anwendung ist nicht verfügbar
- Falsche IP-Adresse oder falscher Hostname
- Ihre Anwendung lauscht über andere Ports als von Ihnen erwartet. Sie können dies überprüfen, indem Sie auf den Host wechseln und an der Befehlseingabeaufforderung „netstat -aon“ verwenden. Es wird angezeigt, welche Prozess-ID über welchen Port lauscht.  
- Aktivierte Firewall auf Ihrem Host, die den Zugriff auf den Anwendungsport aus Ihrem Punkt-zu-Standort-IP-Bereich verhindert
- Netzwerksicherheitsgruppen sind so konfiguriert, dass der Zugriff auf Ihren Anwendungshost und -port aus Ihrem Punkt-zu-Standort-IP-Bereich verhindert wird

Denken Sie daran, dass Sie nicht wissen, welche IP-Adresse Ihres Punkt-zu-Standort-IP-Bereichs von der App verwendet wird. Sie müssen also den Zugriff für den gesamten Bereich zulassen.

Weitere Debugschritte:

- Melden Sie sich an einer anderen VM im VNET an, und versuchen Sie, die host:port-Kombination der Ressource von dort aus zu erreichen. Es gibt einige TCP-ping-Hilfsprogramme, die Sie zu diesem Zweck verwenden können, oder Sie können bei Bedarf auch „telnet“ nutzen. Hierbei soll bestimmt werden, ob für diese andere VM eine Verbindung besteht. 
- Rufen Sie eine Anwendung auf einer anderen VM auf, und testen Sie den Zugriff auf den Host und Port über die Konsole der App.  

####Lokale Ressourcen####
Wenn Sie keine lokalen Ressourcen erreichen können, sollten Sie als Erstes überprüfen, ob Sie eine Ressource in Ihrem VNET erreichen können. Falls dies funktioniert, sind die weiteren Schritte recht einfach. Versuchen Sie, die lokale Anwendung von einer VM in Ihrem VNET zu erreichen. Sie können hierfür „telnet“ oder ein TCP-ping-Hilfsprogramm verwenden. Wenn Sie die lokale Ressource mit der VM erreichen können, sollten Sie zuerst sicherstellen, dass Ihre Standort-zu-Standort-VPN-Verbindung funktioniert. Wenn dies der Fall ist, sollten Sie die oben angegebenen Punkte sowie die Konfiguration und den Status des lokalen Gateways überprüfen.

Wenn die mit VNET gehostete VM auf ein lokales System zugreifen kann, die App jedoch nicht, liegt wahrscheinlich einer der folgenden Gründe vor:
- Die Routen des lokalen Gateways enthalten keine Punkt-zu-Standort-IP-Bereiche.
- Die Netzwerksicherheitsgruppen blockieren den Zugriff auf den Punkt-zu-Standort-IP-Bereich.
- Ihre lokalen Firewalls blockieren den Datenverkehr des Punkt-zu-Standort-IP-Bereichs.
- Es liegt eine benutzerdefinierte Route (UDR) im VNET vor, die den Punkt-zu-Standort-basierten Datenverkehr nicht auf das lokale Netzwerk lässt.

## Hybridverbindungen und App Service-Umgebungen##
Es gibt drei Funktionen, die den Zugriff auf im VNET gehostete Ressourcen ermöglichen. Sie lauten wie folgt:

- VNET-Integration
- Hybridverbindungen
- App Service-Umgebungen

Für Hybridverbindungen müssen Sie in Ihrem Netzwerk einen Relay-Agent installieren, und zwar den Hybrid Connection Manager (HCM). Für den HCM muss eine Verbindung mit Azure und auch mit Ihrer Anwendung hergestellt werden können. Diese Lösung eignet sich besonders gut für ein Remotenetzwerk, z. B. Ihr lokales Netzwerk, oder auch ein anderes in der Cloud gehostetes Netzwerk, weil dafür kein über das Internet zugänglicher Endpunkt erforderlich ist. Der HCM kann nur unter Windows ausgeführt werden, und Sie können bis zu fünf Instanzen verwenden, um für eine hohe Verfügbarkeit zu sorgen. Für Hybridverbindungen wird aber nur TCP unterstützt, und für jeden Endpunkt einer Hybridverbindung muss eine bestimmte host:port-Kombination verwendet werden.

Mit der Funktion für die App Service-Umgebung können Sie eine Instanz von Azure App Service in Ihrem VNET ausführen. Hiermit können Ihre Apps ohne weitere Schritte auf Ressourcen in Ihrem VNET zugreifen. Ein Beispiel für einen weiteren Vorteil einer App Service-Umgebung ist, dass Sie acht dedizierte Kernspeicher-Worker mit 14 GB RAM verwenden können. Ein weiterer Vorteil ist, dass Sie das System je nach Ihren Anforderungen skalieren können. Im Gegensatz zu den mehrinstanzenfähigen Umgebungen, in denen für Ihren ASP eine Größenbeschränkung gilt, können Sie in einer App Service-Umgebung steuern, wie viele Ressourcen Sie für das System zuteilen möchten. Da der Schwerpunkt in diesem Dokument auf dem Netzwerk liegt, sollten Sie folgenden Hinweis beachten: Eine App Service-Umgebung können Sie mit einem ExpressRoute VPN verwenden, was für die VNET-Integration nicht möglich ist.

Bei den Anwendungsfällen gibt es zwar einige Überschneidungen, aber diese Funktionen können sich nicht gegenseitig ersetzen. Das Wissen, welche Funktion verwendet werden sollte, richtet sich nach Ihren Anforderungen und der gewünschten Nutzung. Beispiel:

- Wenn Sie Entwickler sind, einfach eine Website unter Azure ausführen möchten und dafür den Zugriff auf die Datenbank auf der Arbeitsstation unter Ihrem Schreibtisch ermöglichen möchten, ist es am einfachsten, Hybridverbindungen zu verwenden.  
- Wenn Sie in einem großen Unternehmen arbeiten, das eine größere Zahl von Webeigenschaften in der Public Cloud anordnen und im eigenen Netzwerk verwalten möchte, ist es ratsam, die App Service-Umgebung zu verwenden.  
- Falls Sie über einige unter App Service gehostete Apps verfügen und einfach auf Ressourcen in Ihrem VNET zugreifen möchten, ist die VNET-Integration am besten geeignet.  

Über die Anwendungsfälle hinaus sind noch einige Aspekte in Bezug auf die Einfachheit zu beachten. Wenn Ihr VNET bereits mit dem lokalen Netzwerk verbunden ist, stellen die VNET-Integration oder eine App Service-Umgebung eine einfache Möglichkeit zur Nutzung der lokalen Ressourcen dar. Wenn Ihr VNET dagegen nicht mit dem lokalen Netzwerk verbunden ist, bedeutet es gegenüber der Installation des HCM deutlich mehr Aufwand, ein Standort-zu-Standort-VPN mit Ihrem VNET einzurichten.

Über die Unterschiede bei den Funktionen hinaus gibt es auch Unterschiede bei den Preisen. Die Funktion „App Service-Umgebung“ ist ein Premium-Dienstangebot, umfasst zusätzlich zu anderen hervorragenden Features aber auch die meisten Möglichkeiten zur Netzwerkkonfiguration. Die VNET-Integration kann mit den App Service-Plänen „Standard“ oder „Premium“ verwendet werden und eignet sich ideal zum sicheren Nutzen von Ressourcen in Ihrem VNET per App Service mit Mehrinstanzenfähigkeit. Hybridverbindungen sind derzeit von einem BizTalk-Konto abhängig. Die Preise reichen von der anfänglichen kostenlosen Nutzung bis zu den unterschiedlichen höheren Preisstufen, die sich nach der benötigten Menge richten. Was die Nutzung über viele Netzwerke hinweg betrifft, stehen aber Hybridverbindungen an erster Stelle. Hiermit können Sie auf Ressourcen in deutlich mehr als 100 separaten Netzwerken zugreifen.


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/

<!---HONumber=AcomDC_0302_2016-->