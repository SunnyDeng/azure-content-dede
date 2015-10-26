<properties
	pageTitle="Ermöglichen des Hybrid-Zugriffs mit App-Proxy | Microsoft Azure"
	description="Ermöglichen Sie den Zugriff auf Apps, die in Ihrem privaten Netzwerk ausgeführt werden, von außerhalb des Netzwerks mit Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/06/2015"
	ms.author="femila"/>

#Ermöglichen des Hybrid-Zugriffs mit App-Proxy
Mit dem Microsoft Azure AD-Anwendungsproxy können Sie Anwendungen veröffentlichen, die in Ihrem privaten Netzwerk ausgeführt werden, damit darauf sicher und mit jedem Gerät von außerhalb Ihres Netzwerks zugegriffen werden kann. Sie können Azure AD-App-Proxy zum Veröffentlichen von Anwendungen in Ihrem privaten lokalen Netzwerk oder Cloudnetzwerk verwenden. Hierbei ist die Konfiguration als fertiger Dienst in Azure AD möglich, und Sie müssen lediglich einen Anwendungsproxy-Connector auf einem Server in der Umgebung installieren.

##So funktioniert's

Die folgenden Schritte veranschaulichen, wie Anwendungen in einer Hybrid-Umgebung veröffentlicht werden können.
 
1.	Connectors werden unter corpnet bereitgestellt. (Aus Redundanz- und Skalierungsgründen können mehrere Connectors bereitgestellt werden.)
2.	Der Connector stellt automatisch eine Verbindung mit dem Clouddienst her.
3.	Der Benutzer stellt eine Verbindung mit dem Clouddienst her, mit dem der Datenverkehr über die Connectors an die Ressourcen weitergeleitet wird.

Das folgende Diagramm enthält weitere Informationen zur Funktionsweise des Anwendungsproxy:

 ![Funktionsweise von AzureAD-App-Proxy](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

 
1.	Der Benutzer greift auf die Anwendung über den Anwendungsproxy zu und wird zur Authentifizierung an die Azure AD-Anmeldeseite geleitet.
2.	Nach der erfolgreichen Anmeldung wird ein Token generiert und an den Benutzer gesendet.
3.	Der Benutzer sendet das Token an den Anwendungsproxy, der den Benutzerprinzipalnamen (UPN) und den Sicherheitsprinzipalnamen (SPN) aus dem Token abruft und die Anforderung an den Connector leitet.
4.	Der Connector nimmt die Identität des Benutzers an, um ein Kerberos-Ticket anzufordern, das für die interne Authentifizierung (Windows) verwendet werden kann. Eingeschränkte Kerberos-Delegierung
5.	 Ein Kerberos-Ticket wird von Active Directory abgerufen.
6.	Das abgerufene Ticket wird an den Anwendungsserver gesendet, wo es überprüft wird.
7.	 Die Antwort wird über den Anwendungsproxy an den Endbenutzer gesendet. Für die Veröffentlichung einer Webanwendung gibt es nur eine Anforderung: Die Webanwendung im privaten Netzwerk muss für den Server zugänglich sein, auf dem der Connector installiert ist. Anders ausgedrückt: Sie können den Connector entweder direkt auf den Anwendungsservern oder auf beliebigen anderen Servern in der Umgebung installieren. Dies ist problemlos möglich, solange der Connector auf die Webanwendung zugreifen kann.

##Integrieren von lokalen Anwendungen über den Azure AD-Anwendungsproxy
Mit dem Zugriffsbereich in Azure AD können Sie Ihre lokalen Anwendungen in Azure AD veröffentlichen. Führen Sie hierzu die folgenden Schritte aus:

1. Aktivieren Sie den Anwendungsproxy in Azure AD, und installieren und registrieren Sie den Connector. Ausführliche Informationen finden Sie unter [Aktivieren des Azure AD-Anwendungsproxys](active-directory-application-proxy-enable/#step-1-enable-application-proxy-in-azure-ad.md).
2. Veröffentlichen von Anwendungen mit dem Azure AD-Anwendungsproxy: Ausführliche Informationen finden Sie unter [Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy](active-directory-application-proxy-publish.md).

<!---HONumber=Oct15_HO3-->