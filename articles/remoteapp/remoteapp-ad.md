
<properties 
    pageTitle="Azure AD + Active Directory-Anforderungen für Azure RemoteApp | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Active Directory für Azure RemoteApp einrichten können." 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="01/07/2016" 
    ms.author="elizapo" />



# Azure AD + Active Directory-Anforderungen für Azure RemoteApp



Wenn Sie mittels AD Connect einen Verbund für Ihre Azure RemoteApp-Hybrid-Sammlung oder eine Cloudsammlung erstellen möchten, müssen Sie folgende Aktionen ausführen.

### Verbinden von Azure AD und Active Directory

Wenn Sie Ihren Azure AD-Mandanten und Ihre lokalen Active Directory-Umgebungen verbinden möchten, verwenden Sie AD Connect. Sie benötigen nur [4 Klicks](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx), um die beiden Verzeichnisse zu verbinden.

Hinweis: Für Hybrid-Sammlungen ist Verzeichnissynchronisierung erforderlich.

### Sicherstellen der Übereinstimmung von "@domain.com"
Bevor Sie beginnen, stellen Sie sicher, dass der UPN für die lokale Gesamtstruktur mit dem Suffix Ihrer Azure AD-Domäne übereinstimmt.

Nachdem Sie das UPN-Domänensuffix in Azure AD eingerichtet haben, werden alle Benutzer, die sich bei Azure RemoteApp anmelden, als "User@<the suffix you set up>" angemeldet. Stellen Sie sicher, dass Benutzer sich mit derselben user@suffix auch bei der lokalen Domäne anmelden können. In bestimmten Fällen wurde möglicherweise ein Domänenname in Azure AD eingerichtet und für den lokalen Benutzer ein anderer Domänensuffix festgelegt. In diesem Fall können Ihre Benutzer über Azure RemoteApp keine Verbindung mit Computern oder Ressourcen herstellen, die der Domäne angehören.

Wenn Sie Ihr UPN-Domänensuffix in AAD z. B. als "contoso.com" einrichten, einige Benutzer aber lokal bzw. in AD so konfiguriert sind, dass die Anmeldung mit "@contoso.uk" erfolgt, können diese Benutzer sich bei der ARA-Sammlung nicht richtig anmelden. Der UPN der Benutzer muss in AAD und AD identisch sein, damit die Anmeldung möglich ist.

### Erstellen von Objekten für Azure RemoteApp
Sie müssen außerdem die folgenden lokalen Active Directory-Objekte erstellen:

- Ein Dienstkonto für den Zugriff auf Domänenressourcen für RemoteApp-Programme durch Verknüpfen von RDSH-Endpunkten mit der lokalen Domäne.
- Eine Organisationseinheit (OE), die RemoteApp-Computerobjekte enthält. Die Verwendung einer Organisationseinheit ist empfehlenswert (jedoch nicht unbedingt erforderlich), um die Konten und Richtlinien, die Sie mit RemoteApp verwenden, zu isolieren.

Sie benötigen diese beiden Objekte beim Erstellen Ihrer RemoteApp-Sammlung, daher führen Sie diese Schritte unbedingt zuerst aus.

<!---HONumber=AcomDC_0114_2016-->