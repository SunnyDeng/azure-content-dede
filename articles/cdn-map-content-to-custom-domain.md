<properties 
	 pageTitle="Zuordnen von CDN-Inhalten (Content Delivery Network, Netzwerk für die Inhaltsübermittlung) zu einer benutzerdefinierten Domäne" 
	 description="In diesem Thema wird veranschaulicht, wie Sie CDN-Inhalte einer benutzerdefinierten Domäne zuordnen." 
	 services="cdn" 
	 documentationCenter="" 
	 authors="zhangmanling" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="03/05/2015" 
	 ms.author="mazha"/>

# Zuordnen einer benutzerdefinierten Domäne zu einem CDN-Endpunkt (Content Delivery Network, Netzwerk für die Inhaltsübermittlung)

Sie können einem CDN-Endpunkt (Content Delivery Network, Netzwerk für die Inhaltsübermittlung) eine benutzerdefinierte Unterdomäne zuordnen, damit in URLs für zwischengespeicherte Inhalte anstatt des bereitgestellten CDN-Endpunkts Ihr eigener Domänenname verwendet wird. 

Es gibt zwei Möglichkeiten, einem CDN-Endpunkt eine benutzerdefinierte Domäne zuzuordnen. 

1. **Registrieren eines CNAME-Eintrags bei der Domänenregistrierungsstelle, die dem CDN-Endpunkt die benutzerdefinierte Domäne und Unterdomäne zuordnet** 
	
	Ein CNAME-Datensatz ist eine DNS-Funktion, die eine Quelldomäne einer Zieldomäne zuordnet. In diesem Fall ist die Quelldomäne Ihre benutzerdefinierte Domäne und Unterdomäne (die Unterdomäne ist immer erforderlich). Die Zieldomäne entspricht Ihrem CDN-Endpunkt.  

	Während die benutzerdefinierte Domäne dem CDN-Endpunkt zugeordnet wird, kann es jedoch zu einer kurzen Downtime der Domäne kommen, bis die Domäne im Azure-Verwaltungsportal registriert ist. 
2. **Hinzufügen eines zwischengeschalteten Registrierungsschritts mithilfe von der Azure-Unterdomäne "Cdnverify"**

	Wenn die benutzerdefinierte Domäne aktuell eine Anwendung unterstützt, die unter einer Vereinbarung zum Servicelevel (SLA, Service Level Agreement) mit 100 % Verfügbarkeit läuft, können Sie mithilfe der Microsoft Azure-Unterdomäne **cdnverify** eine Übergangslösung für die Registrierung bereitstellen, damit Benutzer während der DNS-Zuordnung Zugriff auf die Domäne haben.  

> AZURE.NOTE   
> 
-	Sie müssen einen CNAME-Eintrag bei der Domänenregistrierungsstelle registrieren, um die Domäne dem CDN-Endpunkt zuzuordnen. CNAME-Einträge werden bestimmten Unterdomänen wie "www.mydomain.com" oder "myblog.mydomain.com" zugeordnet. Es ist nicht möglich, einen CNAME-Eintrag einer Stammdomäne wie "mydomain.com" zuzuordnen.
-	Sie müssen einem CDN-Endpunkt eine dedizierte Unterdomäne zuordnen.  Der erstellte CNAME-Eintrag leitet sämtlichen Datenverkehr für die Unterdomäne an den angegebenen Endpunkt weiter. Wenn Sie dem CDN-Endpunkt beispielsweise die Unterdomäne "www.mydomain.com" zuordnen, können Sie diese Unterdomäne keinem anderen Microsoft Azure-Endpunkt, z. B. einem Speicherkonto-Endpunkt oder einem Cloud-Dienst-Endpunkt, zuordnen. Innerhalb einer Domäne können Sie jedoch verschiedene Unterdomänen für unterschiedliche Dienstendpunkte verwenden. Außerdem haben Sie die Möglichkeit, demselben CDN-Endpunkt verschiedene Unterdomänen zuzuordnen.

In den Verfahren in diesem Thema werden die folgenden Schritte erläutert:    

-	[Registrieren einer benutzerdefinierten Domäne für einen Azure CDN-Endpunkt](#subheading1)
-	[Registrieren einer benutzerdefinierten Domäne für einen Azure CDN-Endpunkt mithilfe der übergangsweise verwendeten Unterdomäne "cdnverify" ](#subheading2)
-	[Überprüfen, ob die benutzerdefinierte Unterdomäne auf den CDN-Endpunkt verweist](#subheading3) 

## <a name="subheading1"></a>Registrieren einer benutzerdefinierten Domäne für einen Azure CDN-Endpunkt

1.	Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com/) an.
2.	Klicken Sie im Navigationsbereich auf **CDN**.
3.	Klicken Sie in der Listenansicht auf den Namen des CDN-Endpunkts, dem Sie die Unterdomäne zuordnen möchten, um zur Detailseite dieses Endpunkts zu navigieren.
4.	Klicken Sie auf dem Menüband auf **Domänen verwalten**, um das Dialogfeld **Benutzerdefinierte Domänen** verwalten zu öffnen. m Text des Dialogfelds sehen Sie den vom CDN-Endpunkt abgeleiteten Hostnamen, den Sie zum Erstellen eines neuen CNAME-Eintrags verwenden können. Die Hostnamensadresse hat das folgende Format: **az#####.vo.msecnd.net** (wobei **az#####** dem Bezeichner des CDN-Endpunkts entspricht). Sie können diesen Hostnamen kopieren, um ihn für den CNAME-Eintrag zu verwenden.  
Ignorieren Sie bei diesem Verfahren den Text, der sich auf die Unterdomäne **cdnverify** bezieht.
5.	Navigieren Sie zur Website der Domänenregistrierungsstelle, und suchen Sie den Abschnitt für die Erstellung von DNS-Einträgen. Dieser ist möglicherweise in einem Abschnitt namens **Domänenname**, **DNS** oder **Namensserververwaltung** enthalten.
6.	Suchen Sie den Abschnitt zur Verwaltung von CNAMEs. Öffnen Sie ggf. die Seite mit erweiterten Einstellungen, und suchen Sie nach den Wörtern CNAME, Alias oder Unterdomänen.
7.	Erstellen Sie einen neuen CNAME-Eintrag, der die ausgewählte Unterdomäne (z. B. **www** oder **cdn**) dem im Dialogfeld **Benutzerdefinierte Domänen verwalten** bereitgestellten Hostnamen zuordnet.
8.	Kehren Sie zum Dialogfeld **Benutzerdefinierte Domänen verwalten** zurück, und geben Sie den Namen der benutzerdefinierten Domäne einschließlich Unterdomäne in das Dialogfeld ein. Geben Sie z. B. den Domänennamen beispielsweise im Format "www.mydomain.com" oder "cdn.mydomain.com" ein.   

	Azure überprüft, ob der CNAME-Eintrag für den eingegebenen Domänennamen vorhanden ist. Wenn der CNAME-Eintrag richtig ist, wird die benutzerdefinierte Domäne überprüft und kann dann für CDN-Inhalte verwendet werden.  

	Es kann eine Weile dauern, bis der CNAME-Eintrag an alle Namensserver im Internet weitergegeben wird. Wenn die Domäne nicht sofort erkannt wird, Sie aber sicher sind, dass der CNAME-Eintrag richtig ist, warten Sie einige Minuten, und überprüfen Sie die Domäne dann erneut.

## <a name="subheading2"></a>Registrieren einer benutzerdefinierten Domäne für einen Azure CDN-Endpunkt mithilfe der übergangsweise verwendeten Unterdomäne "cdnverify"  


1.	Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com/) an.
2.	Klicken Sie im Navigationsbereich auf **CDN**.
3.	Klicken Sie in der Listenansicht auf den Namen des CDN-Endpunkts, dem Sie die Unterdomäne zuordnen möchten, um zur Detailseite dieses Endpunkts zu navigieren.
4.	Klicken Sie auf dem Menüband auf **Domänen verwalten**, um das Dialogfeld **Benutzerdefinierte Domänen** verwalten zu öffnen. Im Text des Dialogfelds sehen Sie den vom CDN-Endpunkt abgeleiteten Hostnamen. Dieser wird verwendet, um einen neuen CNAME-Eintrag mithilfe der übergangsweise verwendeten Unterdomäne **cdnverify** zu erstellen. Die Hostnamensadresse hat das folgende Format: **cdnverify.az#####.vo.msecnd.net**. Sie können diesen Hostnamen kopieren, um ihn für den CNAME-Eintrag zu verwenden.
5.	Navigieren Sie zur Website der Domänenregistrierungsstelle, und suchen Sie den Abschnitt für die Erstellung von DNS-Einträgen. Dieser ist möglicherweise in einem Abschnitt namens **Domänenname**, **DNS** oder **Namensserververwaltung** enthalten.
6.	Suchen Sie den Abschnitt zur Verwaltung von CNAMEs. Öffnen Sie ggf. die Seite mit erweiterten Einstellungen, und suchen Sie nach den Wörtern **CNAME**, **Alias** oder **Unterdomänen**.
7.	Erstellen Sie einen neuen CNAME-Eintrag, und geben Sie einen Unterdomänenalias ein, der die Unterdomäne **cdnverify** enthält.  Die angegebene Unterdomäne kann beispielsweise das Format **cdnverify.www** oder **cdnverify.cdn** aufweisen.  Geben Sie dann den Hostnamen, der Ihrem CDN-Endpunkt entspricht, im folgenden Format ein: **cdnverify.az#####.vo.msecnd.net** (dabei ist **az#####** der Bezeichner des CDN-Endpunkts). Das Format des Hostnamens wird im Dialogfeld **Benutzerdefinierte Domänen verwalten** für Sie bereitgestellt.
8.	Kehren Sie zum Dialogfeld **Benutzerdefinierte Domänen verwalten** zurück, und geben Sie den Namen der benutzerdefinierten Domäne einschließlich Unterdomäne in das Dialogfeld ein. Geben Sie z. B. den Domänennamen im Format **www.mydomain.com** oder **cdn.mydomain.com** ein. In diesem Schritt ist es nicht erforderlich, der Unterdomäne de**cdnverify** voranzustellen.  

	Azure überprüft, ob der CNAME-Eintrag für den eingegebenen Domänennamen "cdnverify" vorhanden ist.
9.	Die benutzerdefinierte Domäne wurde jetzt zwar von Azure überprüft, aber der an die Domäne gerichtete Datenverkehr wird noch nicht an Ihren CDN-Endpunkt weitergeleitet. Um den Prozess abzuschließen, kehren Sie zur Website der DNS-Registrierung zurück, und erstellen Sie einen weiteren CNAME-Datensatz, der Ihre Unterdomäne Ihrem CDN-Endpunkt zuordnet. Geben Sie die Unterdomäne beispielsweise als **www** oder **cdn** und den Hostnamen als **az#####.vo.msecnd.net** an. Mit diesem Schritt ist die Registrierung Ihrer benutzerdefinierten Domäne abgeschlossen. 
10.	Zuletzt können Sie den mit **cdnverify** erstellten CNAME-Eintrag löschen, da er nur als Übergangslösung benötigt wurde.  


## <a name="subheading3"></a>Überprüfen, ob die benutzerdefinierte Unterdomäne auf den CDN-Endpunkt verweist

-	Nachdem Sie die Registrierung der benutzerdefinierten Domäne abgeschlossen haben, können Sie über die benutzerdefinierte Domäne auf den im CDN-Endpunkt zwischengespeicherten Inhalt zugreifen.
Stellen Sie zunächst sicher, dass öffentliche Inhalte zum Zwischenspeichern auf dem Endpunkt verfügbar sind.  Wenn der CDN-Endpunkt beispielsweise einem Speicherkonto zugeordnet ist, wird der Inhalt vom CDN in öffentlichen BLOB-Containern zwischengespeichert. Zum Testen der benutzerdefinierten Domäne müssen Sie sicherstellen, dass der Container den öffentlichen Zugriff zulässt und dass er mindestens ein BLOB enthält.
-	Navigieren Sie in Ihrem Browser mithilfe der benutzerdefinierten Domäne zur Adresse des BLOBs. Wenn die benutzerdefinierte Domäne **www.mydomain.com** lautet, ähnelt die URL eines zwischengespeicherten BLOBs der folgenden URL:  
	
		http://www.mydomain.com/mypubliccontainer/acachedblob.jpg
-	Wenn der CDN-Endpunkt einem Cloud-Dienst zugeordnet ist, ähnelt die Adresse des zwischengespeicherten Inhalts der folgenden URL:

		http://www.mydomain.com/cdn/mycloudservice

# Siehe auch


[Aktivieren von des CDN (Content Delivery Network) für Azure ](./cdn-create-new-endpoint.md)  
**Übersicht über das Azure CDN**


<!--HONumber=49-->