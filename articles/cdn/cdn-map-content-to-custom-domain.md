<properties 
	 pageTitle="Zuordnen von CDN-Inhalten (Content Delivery Network) zu einer benutzerdefinierten Domäne" 
	 description="In diesem Thema wird veranschaulicht, wie Sie CDN-Inhalte einer benutzerdefinierten Domäne zuordnen." 
	 services="cdn" 
	 documentationCenter="" 
	 authors="camsoper" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="12/02/2015" 
	 ms.author="casoper"/>

#Zuordnen einer benutzerdefinierten Domäne zu einem CDN-Endpunkt (Content Delivery Network, Netzwerk für die Inhaltsübermittlung)

Sie können einem CDN-Endpunkt (Content Delivery Network, Netzwerk für die Inhaltsübermittlung) eine benutzerdefinierte Unterdomäne zuordnen, damit in URLs für zwischengespeicherte Inhalte anstatt des bereitgestellten CDN-Endpunkts Ihr eigener Domänenname verwendet wird. Dafür erstellen Sie einen CNAME-Eintrag bei Ihrer Domänenregistrierungsstelle und ordnen Ihre benutzerdefinierte Domäne und Unterdomäne zum CDN-Endpunkt zu. Ein CNAME-Datensatz ist eine DNS-Funktion, die eine Quelldomäne einer Zieldomäne zuordnet. In diesem Fall ist die Quelldomäne Ihre benutzerdefinierte Domäne und Unterdomäne (die Unterdomäne ist immer erforderlich). Die Zieldomäne entspricht Ihrem CDN-Endpunkt.

> [AZURE.NOTE]– Sie müssen einen CNAME-Eintrag bei der Domänenregistrierungsstelle registrieren, um die Domäne dem CDN-Endpunkt zuzuordnen. CNAME-Einträge werden bestimmten Unterdomänen wie "www.mydomain.com" oder "myblog.mydomain.com" zugeordnet. Es ist nicht möglich, einen CNAME-Eintrag einer Stammdomäne wie "mydomain.com" zuzuordnen. – Eine Unterdomäne kann nur einem CDN-Endpunkt zugeordnet werden. Der erstellte CNAME-Eintrag leitet sämtlichen Datenverkehr für die Unterdomäne an den angegebenen Endpunkt weiter. Wenn Sie dem CDN-Endpunkt beispielsweise die Unterdomäne "www.mydomain.com" zuordnen, können Sie diese Unterdomäne keinem anderen Microsoft Azure-Endpunkt, z. B. einem Speicherkonto-Endpunkt oder einem Cloud-Dienst-Endpunkt, zuordnen. Innerhalb einer Domäne können Sie jedoch verschiedene Unterdomänen für unterschiedliche Dienstendpunkte verwenden. Außerdem haben Sie die Möglichkeit, demselben CDN-Endpunkt verschiedene Unterdomänen zuzuordnen.

In den Verfahren in diesem Thema werden die folgenden Schritte erläutert:

-	[Registrieren einer benutzerdefinierten Domäne für einen Azure CDN-Endpunkt](#subheading1)
-	[Überprüfen, ob die benutzerdefinierte Unterdomäne auf den CDN-Endpunkt verweist](#subheading3) 

##<a name="subheading1"></a>Registrieren einer benutzerdefinierten Domäne für einen Azure CDN-Endpunkt

1.	Melden Sie sich beim [Azure-Portal](http://portal.azure.com/) an.
2.	Klicken Sie auf **Durchsuchen**, auf **CDN-Profile** und anschließend auf das CDN-Profil mit dem Endpunkt, das Sie einer benutzerdefinierten Domäne zuordnen möchten.  
3.	Klicken Sie auf dem Blatt **CDN-Profil** auf den CDN-Endpunkt, den Sie der Unterdomäne zuordnen möchten.
4.	Klicken Sie am oberen Rand des Blatts für den Endpunkt auf die Schaltfläche **Benutzerdefinierte Domäne hinzufügen**. Auf dem Blatt **Benutzerdefinierte Domäne hinzufügen** sehen Sie den vom CDN-Endpunkt abgeleiteten Hostnamen, den Sie zum Erstellen eines neuen CNAME-Eintrags verwenden können. Die Hostnamensadresse wird im Format **&lt;EndpointName>.azureedge.net** angezeigt. Sie können diesen Hostnamen kopieren, um ihn für den CNAME-Eintrag zu verwenden.  

5.	Navigieren Sie zur Website der Domänenregistrierungsstelle, und suchen Sie den Abschnitt für die Erstellung von DNS-Einträgen. Diese finden Sie z. B. in einem Abschnitt mit der Bezeichnung **Domänenname**, **DNS** oder **Namenserververwaltung**.
6.	Suchen Sie den Abschnitt zur Verwaltung von CNAMEs. Öffnen Sie ggf. die Seite mit erweiterten Einstellungen, und suchen Sie nach den Wörtern CNAME, Alias oder Unterdomänen.
7.	Erstellen Sie einen neuen CNAME-Eintrag, der die ausgewählte Unterdomäne (z. B. **www** oder **cdn**) dem im Blatt **Benutzerdefinierte Domänen hinzufügen** bereitgestellten Hostnamen zuordnet.
8.	Kehren Sie zum Blatt **Benutzerdefinierte Domänen hinzufügen** zurück, und geben Sie den Namen der benutzerdefinierten Domäne einschließlich Unterdomäne in das Dialogfeld ein. Geben Sie z. B. den Domänennamen im Format www.mydomain.com oder cdn.mydomain.com ein.   

	Azure überprüft, ob der CNAME-Eintrag für den eingegebenen Domänennamen vorhanden ist. Wenn der CNAME-Eintrag richtig ist, wird die benutzerdefinierte Domäne überprüft und kann dann für CDN-Inhalte verwendet werden.

	Es kann eine Weile dauern, bis der CNAME-Eintrag an alle Namensserver im Internet weitergegeben wird. Wenn die Domäne nicht sofort erkannt wird, Sie aber sicher sind, dass der CNAME-Eintrag richtig ist, warten Sie einige Minuten, und überprüfen Sie die Domäne dann erneut.

##<a name="subheading3"></a>Überprüfen, ob die benutzerdefinierte Unterdomäne auf den CDN-Endpunkt verweist

-	Nachdem Sie die Registrierung der benutzerdefinierten Domäne abgeschlossen haben, können Sie über die benutzerdefinierte Domäne auf den im CDN-Endpunkt zwischengespeicherten Inhalt zugreifen. Stellen Sie zunächst sicher, dass öffentliche Inhalte zum Zwischenspeichern auf dem Endpunkt verfügbar sind. Wenn der CDN-Endpunkt beispielsweise einem Speicherkonto zugeordnet ist, wird der Inhalt vom CDN in öffentlichen Blobcontainern zwischengespeichert. Zum Testen der benutzerdefinierten Domäne müssen Sie sicherstellen, dass der Container den öffentlichen Zugriff zulässt und dass er mindestens ein Blob enthält.
-	Navigieren Sie in Ihrem Browser mithilfe der benutzerdefinierten Domäne zur Adresse des Blobs. Wenn die benutzerdefinierte Domäne beispielsweise **www.mydomain.com** lautet, ähnelt die URL eines zwischengespeicherten Blobs der folgenden URL:  
	
		http://www.mydomain.com/mypubliccontainer/acachedblob.jpg
-	Wenn der CDN-Endpunkt einem Clouddienst zugeordnet ist, ähnelt die Adresse des zwischengespeicherten Inhalts der folgenden URL:

		http://www.mydomain.com/mycloudservice

##Siehe auch


[Aktivieren des CDN (Content Delivery Network) für Azure](./cdn-create-new-endpoint.md)

 

<!---HONumber=AcomDC_1203_2015-->