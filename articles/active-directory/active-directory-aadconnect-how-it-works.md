<properties 
	pageTitle="Funktionsweise von Azure AD Connect" 
	description="Weitere Informationen zur Funktionsweise von Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Funktionsweise von Azure AD Connect


<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/documentation/articles/active-directory-aadconnect/" title="Worum handelt es sich?" class="current">Worum handelt es sich?</a> <a href="/de-de/documentation/articles/active-directory-aadconnect-how-it-works/" title="Funktionsweise">Funktionsweise</a> <a href="/de-de/documentation/articles/active-directory-aadconnect-get-started/" title="Erste Schritte">Erste Schritte</a> <a href="/de-de/documentation/articles/active-directory-aadconnect-whats-next/" title="Wie geht es weiter?">Wie geht es weiter?</a> <a href="/de-de/documentation/articles/active-directory-aadconnect-learn-more/" title="Weitere Informationen">Weitere Informationen</a>
</div>

Azure Active Directory Connect besteht aus drei Hauptbestandteilen. Diese sind die Synchronisierungsdienste, die optionalen Active Directory-Verbunddienste und die Überwachung mithilfe von Azure AD Connect Health.


<center>![Azure AD Connect Stack](./media/active-directory-aadconnect-how-it-works/AADConnectStack.png) </center>

- Synchronisierung – Dieser Teil umfasst die Komponenten und Funktionen, die zuvor als Dirsync und AAD Sync veröffentlicht wurden. Dieser Teil ist für das Erstellen von Benutzern und Gruppen zuständig. Er stellt ebenfalls sicher, dass Benutzer- und Gruppeninformationen in Ihrer lokalen Umgebung denen in der Cloud entsprechen.
- AD FS – Dies ist eine optionale Komponente von Azure AD Connect und kann zum Einrichten einer Hybridumgebung mithilfe einer lokalen AD FS-Infrastruktur verwendet werden. Dieser Teil kann von Organisationen verwendet werden, um sich mit komplexen Bereitstellungen zu befassen, zum Beispiel Domänenbeitritts-SSO, Erzwingen von AD-Login-Richtlinien und Smartcard- bzw. Drittanbieter-MFA. Weitere Informationen zum Konfigurieren von SSO finden Sie unter [DirSync mit Single Sign-On](https://msdn.microsoft.com/library/azure/dn441213.aspx).
- Überwachung der Integrität – Für komplexe Bereitstellungen mithilfe von AD FS bietet Azure AD Connect Health eine stabile Überwachung Ihrer Verbundservern und einen zentralen Speicherort im Azure-Portal, um diese Aktivität anzuzeigen. Weitere Informationen finden Sie unter [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).






**Zusätzliche Ressourcen**

* [Verwenden Sie Ihre lokale Identitätsinfrastruktur in der Cloud](active-directory-aadconnect.md)
* [Erste Schritte mit Azure AD Connect](active-directory-aadconnect-get-started.md)
* [Wie geht es weiter mit Azure AD Connect?](active-directory-aadconnect-whats-next.md)
* [Weitere Informationen](active-directory-aadconnect-learn-more.md)
* [Azure AD Connect auf MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58--> 