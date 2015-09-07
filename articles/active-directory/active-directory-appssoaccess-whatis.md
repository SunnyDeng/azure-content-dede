<properties
	pageTitle="Was sind Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory? | Microsoft Azure"
	description="Verwenden Sie Azure Active Directory, um die einmalige Anmeldung für alle SaaS- und Webanwendungen zu aktivieren, die Sie für Ihr Unternehmen benötigen."
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="asmalser-msft"/>

#Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?

Einmaliges Anmelden bedeutet, dass Sie Zugriff auf sämtliche für Ihre Geschäftsaktivitäten benötigten Anwendungen und Ressourcen erhalten, indem Sie sich nur einmal mit einem einzigen Benutzerkonto anmelden. Nach der Anmeldung können Sie auf alle benötigten Anwendungen zugreifen, ohne sich ein zweites Mal (z. B. durch Eingabe eines Kennworts) authentifizieren zu müssen.

Viele Organisationen nutzen SaaS-Anwendungen (Software as a Service) wie z. B. Office 365, Box und Salesforce, um die Endbenutzerproduktivität zu steigern. In der Vergangenheit mussten das IT-Personal Benutzerkonten in jeder SaaS-Anwendung individuell erstellen und aktualisieren, und Benutzer mussten sich für jede SaaS-Anwendung ein Kennwort merken.

Azure Active Directory weitet das lokale Active Directory auf die Cloud aus und ermöglicht es den Benutzern auf diese Weise, sich mit ihrem primären Organisationskonto nicht nur bei den mit ihrer Domäne verbundenen Geräten und Unternehmensressourcen anzumelden, sondern auch bei sämtlichen Web- und SaaS-Anwendungen, die sie für ihre Arbeit benötigen.

Benutzer müssen sich also keine Vielzahl von Benutzernamen und Kennwörtern mehr merken, und der Anwendungszugriff für Benutzer kann basierend auf ihrer Gruppenmitgliedschaft in der Organisation sowie ihrem Mitarbeiterstatus automatisch bereitgestellt oder deaktiviert werden. Azure Active Directory stellt Funktionen zum Steuern von Sicherheit und Zugriff bereit, mit denen Sie den Benutzerzugriff auf SaaS-Anwendungen zentral verwalten können.

Azure AD ermöglicht die einfache Integration in zahlreiche beliebte SaaS-Anwendungen. Es bietet eine Identitäts- und Zugriffsverwaltung und ermöglicht es Benutzern, sich einmalig direkt bei Anwendungen anzumelden, oder diese über ein Portal wie Office 365 oder den Azure AD-Zugriffsbereich zu ermitteln und zu starten.

Die Architektur der Integration umfasst die folgenden vier Hauptbausteine:

* Die einmalige Anmeldung ermöglicht es Benutzern, basierend auf ihrem Organisationskonto in Azure AD auf ihre SaaS-Anwendungen zuzugreifen. Die einmalige Anmeldung ermöglicht es Benutzern, sich mit einem einzelnen Organisationskonto bei einer Anwendung zu authentifizieren.

* Die Benutzerbereitstellung ermöglicht das Bereitstellen von Benutzern und das Aufheben der Bereitstellung in der SaaS-Zielanwendung, basierend auf Änderungen in Windows Server Active Directory und/oder Azure AD. Ein bereitgestelltes Konto ermöglicht es, Benutzer zur Verwendung einer Anwendung zu autorisieren, nachdem sie sich durch einmaliges Anmelden authentifiziert haben.

* Die zentralisierte Verwaltung des Anwendungszugriffs im Azure-Verwaltungsportal ermöglicht SaaS-Anwendungszugriff und -Verwaltung von einem einzelnen Punkt aus. Sie bietet unter anderem die Möglichkeit, die Entscheidungsfindung für den Anwendungszugriff und Genehmigungen an beliebige Benutzer in der Organisation zu delegieren.

* Einheitliche Berichterstellung und Überwachung der Benutzeraktivität in Azure AD

##Wie funktioniert das einmalige Anmelden mit Azure Active Directory?

Wenn Benutzer sich bei einer Anwendung "anmelden", durchlaufen sie einen Authentifizierungsprozess, in dem sie ihre Identität belegen müssen. Ohne die einmalige Anmeldung erfolgt dies in der Regel durch die Eingabe eines in der Anwendung gespeicherten Kennworts, und die Benutzer müssen dieses Kennwort kennen.

Azure AD unterstützt drei verschiedene Methoden für die Anwendungsanmeldung:

*	**Einmalige Verbundanmeldung**: Ermöglicht Anwendungen die Umleitung zu Azure AD für die Benutzerauthentifizierung, statt zur Eingabe eines eigenen Kennworts aufgefordert zu werden. Diese Methode kann für Anwendungen verwendet werden, die Protokolle wie SAML 2.0, WS-Verbund oder OpenID Connect unterstützen, und ist der umfassendste Modus bei der einmaligen Anmeldung.

*	**Kennwortbasierte einmalige Anmeldung**: Ermöglicht die sichere Speicherung des Anwendungskennworts und dessen Wiedergabe mit einer Webbrowsererweiterung oder einer mobilen App. Damit wird der von der Anwendung bereitgestellte vorhandene Anmeldevorgang genutzt, die Kennwortverwaltung übernimmt jedoch der Administrator, sodass der Benutzer das Kennwort nicht kennen muss.

*	**Vorhandenes einmaliges Anmelden**: Ermöglicht Azure AD, eine beliebige Methode für die einmalige Anmeldung zu nutzen, die für die Anwendung eingerichtet wurde. Es ist auch möglich, diese Anwendungen mit Office 365- oder dem Azure AD-Zugriffsbereichsportal zu verknüpfen und darüber hinaus die Berichterstellung in Azure AD zu aktivieren, wenn die Anwendungen auf diese Weise gestartet werden.

Sobald ein Benutzer bei einer Anwendung authentifiziert wurde, muss zu der Anwendung auch ein Kontodatensatz vorhanden sein, dem zu entnehmen ist, wo die Zugriffsberechtigungen und die Zugriffsebene in der Anwendung zu finden sind. Die Bereitstellung dieses Kontodatensatzes kann entweder automatisch oder manuell durch einen Administrator erfolgen, bevor dem Benutzer Zugriff mit einmaliger Anmeldung gewährt wird.

 Weitere Informationen zu den Modi der einmaligen Anmeldung und zur Bereitstellung erhalten Sie in den folgenden Abschnitten.

###Einmalige Verbundanmeldung

Die einmalige Verbundanmeldung ermöglicht die automatische Anmeldung der Benutzer in Ihrer Organisation bei einer Drittanbieter-SaaS-Anwendung durch Azure AD mithilfe der Benutzerkontoinformationen aus Azure AD.

Wenn Sie bereits bei Azure AD angemeldet sind und auf Ressourcen zugreifen möchten, die von einer Drittanbieter-SaaS-Anwendung gesteuert werden, macht in diesem Szenario der Verbund die erneute Authentifizierung eines Benutzers überflüssig.

Azure AD kann die einmalige Verbundanmeldung für Anwendungen unterstützen, die Protokolle wie SAML 2.0, WS-Verbund oder OpenID Connect unterstützen.

Siehe auch: [Verwalten von Zertifikaten für die einmalige Verbundanmeldung](active-directory-sso-certs.md)

###Kennwortbasierte einmalige Anmeldung

Das Konfigurieren der kennwortbasierten einmaligen Anmeldung ermöglicht die automatische Anmeldung der Benutzer in Ihrer Organisation bei einer Drittanbieter-SaaS-Anwendung durch Azure AD mithilfe der Benutzerkontoinformationen aus der Drittanbieter-SaaS-Anwendung. Wenn Sie dieses Feature aktivieren, sammelt Azure AD die Benutzerkontoinformationen und das zugehörige Kennwort und speichert sie sicher.

Azure AD kann die kennwortbasierte einmalige Anmeldung für jede cloudbasierte App unterstützen, die über eine HTML-basierte Anmeldeseite verfügt. Mithilfe eines benutzerdefinierten Browser-Plug-Ins automatisiert AAD die Anmeldevorgänge der Benutzer über den sicheren Abruf von Anmeldeinformationen der Anwendung, z. B. Benutzername und Kennwort, aus dem Verzeichnis, und trägt diese Anmeldeinformationen für die Benutzer in die Anmeldeseite der Anwendung ein. Es gibt zwei Anwendungsfälle:

1.	**Administrator verwaltet Anmeldeinformationen** – Administratoren können Anmeldeinformationen von Anwendungen erstellen und verwalten und weisen diese Anmeldeinformationen Benutzern oder Gruppen zu, die auf die Anwendung zugreifen müssen. In diesen Fällen müssen die Endbenutzer die Anmeldeinformationen nicht kennen, erhalten aber weiterhin einfach durch Klicken auf die Anwendung in ihrem Zugriffsbereich oder über einen bereitgestellten Link durch einmaliges Anmelden Zugriff auf die Anwendung. Dies ermöglicht zum einen die Lebenszyklusverwaltung der Anmeldeinformationen durch den Administrator und bietet zum anderen den Endbenutzern den Komfort, dass sie sich App-spezifische Kennwörter weder merken noch sie verwalten müssen. Die Anmeldeinformationen werden während des automatisierten Anmeldevorgangs vor dem Endbenutzer verborgen; der Benutzer kann sie jedoch mithilfe von Webdebugtools technisch sichtbar machen, und Benutzer und Administratoren sollten die gleichen Sicherheitsrichtlinien befolgen, die auch für direkt vom Benutzer eingegebene Anmeldeinformationen gelten. Vom Administrator bereitgestellte Anmeldeinformationen sind sehr nützlich beim Bereitstellen von Kontozugriff bei Anwendungen, die von vielen Benutzern gemeinsam genutzt werden, wie z. B. bei sozialen Medien oder Anwendungen zur gemeinsamen Nutzung von Dokumenten.

2.	**Benutzer verwaltet Anmeldeinformationen** – Administratoren können Endbenutzern oder Gruppen Anwendungen zuweisen und Endbenutzern die direkte Eingabe ihrer Anmeldeinformationen beim erstmaligen Zugriff auf die Anwendung in ihrem Zugriffsbereich ermöglichen. Dies stellt einen Komfort für Endbenutzer dar, da sie nicht ständig für jeden Zugriff auf die Anwendung die App-spezifischen Kennwörter eingeben müssen. Dieser Anwendungsfall kann auch als Hilfe zum Einstieg in die administrative Verwaltung der Anmeldeinformationen dienen, wobei der Administrator neue Anmeldeinformationen für die Anwendung zu einem späteren Zeitpunkt festlegen kann, ohne die Benutzerfreundlichkeit für den App-Zugriff einzuschränken.

In beiden Fällen werden Anmeldeinformationen verschlüsselt im Verzeichnis gespeichert und nur beim automatisierten Anmelden über HTTPS übergeben. Mit der kennwortbasierten einmaligen Anmeldung bietet Azure AD eine bequeme Lösung zur Identitäts- und Zugriffsverwaltung für Apps, die keine Verbundprotokolle unterstützen können.

Die kennwortbasierte einmalige Anmeldung nutzt eine Browsererweiterung, um die Anwendung und benutzerspezifische Informationen sicher aus Azure AD abzurufen und sie auf den Dienst anzuwenden. Die meisten von Azure AD unterstützten Drittanbieter-SaaS-Anwendungen unterstützen dieses Feature.

Für kennwortbasiertes SSO eignen sich folgende Browser des Endbenutzers:

- Internet Explorer 8, 9 und 10 – unter Windows 7 oder höher
- Chrome – unter Windows 7 oder höher und MacOS x oder höher
- Firefox 26.0 oder höher – unter Windows XP SP2 oder höher und Mac OS X 10.6 oder höher

**Hinweis:** Die Erweiterung für die kennwortbasierte einmalige Anmeldung steht für Edge in Windows 10 zur Verfügung, sobald Browsererweiterungen von Edge unterstützt werden.

###Vorhandenes einmaliges Anmelden

Beim Konfigurieren der einmaligen Anmeldung für eine Anwendung bietet das Azure-Verwaltungsportal als dritte Möglichkeit die Option "Vorhandenes einmaliges Anmelden". Diese Option ermöglicht es dem Administrator, einfach eine Verknüpfung zu einer Anwendung zu erstellen, und sie für ausgewählte Benutzer im Zugriffsbereich zu platzieren.

Ist eine Anwendung beispielsweise zum Authentifizieren von Benutzern mit Active Directory Federation Services 2.0 konfiguriert, kann ein Administrator mit der Option "Vorhandenes einmaliges Anmelden" einen entsprechenden Link im Zugriffsbereich erstellen. Wenn Benutzer auf den Link zugreifen, werden sie mit Active Directory Federation Services 2.0 oder einer beliebigen anderen Lösung für einmaliges Anmelden authentifiziert, die von der Anwendung bereitgestellt wird.

###Benutzerbereitstellung

Für bestimmte Anwendungen aktiviert Azure AD die automatisierte Benutzerbereitstellung und Aufhebung der Bereitstellung von Konten in Drittanbieter-SaaS-Anwendungen über das Azure-Verwaltungsportal. Hierbei werden Identitätsinformationen aus Windows Server Active Directory oder Azure AD genutzt. Wenn ein Benutzer Berechtigungen für eine dieser Anwendungen in Azure AD erhält, kann automatisch ein Konto in der SaaS-Zielanwendung erstellt (bereitgestellt) werden.

Wenn ein Benutzer gelöscht wird oder sich seine Informationen in Azure AD ändern, spiegeln sich diese Änderungen auch in der SaaS-Anwendung wider. Dies bedeutet, dass die Konfiguration der automatisierten Verwaltung des Identitätslebenszyklus es Administratoren ermöglicht, die automatische Bereitstellung und Aufhebung der Bereitstellung von SaaS-Anwendungen zu steuern und bereitzustellen. In Azure AD wird diese Automatisierung der Verwaltung des Identitätslebenszyklus durch die Benutzerbereitstellung aktiviert.

Weitere Informationen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](active-directory-saas-app-provisioning.md).

##Erste Schritte mit dem Azure AD-Anwendungskatalog

Wollen Sie loslegen? Um das einmalige Anmelden zwischen Azure AD und in Ihrer Organisation verwendeten SaaS-Anwendungen bereitzustellen, befolgen Sie diese Richtlinien.

###Verwenden des Azure AD-Anwendungskatalogs

Der [Azure Active Directory-Anwendungskatalog](http://azure.microsoft.com/marketplace/active-directory/all/) enthält eine Liste der Anwendungen, die bekanntermaßen eine Form der einmaligen Anmeldung mit Azure Active Directory unterstützen.

![][1]

Nachfolgend finden Sie einige Tipps für die Suche nach Apps unter Berücksichtigung der Funktionen, die sie unterstützen:

*	Azure AD unterstützt die automatische Bereitstellung und Aufhebung der Bereitstellung für alle "Top"-Apps im [Azure Active Directory-Anwendungskatalog](http://azure.microsoft.com/marketplace/active-directory/all/).

*	Eine Liste der Verbundanwendungen, die speziell die einmalige Verbundanmeldung mithilfe eines Protokolls wie z. B. WS-Verbund, SAML oder OpenID Connect unterstützen, finden Sie [hier](http://social.technet.microsoft.com/wiki/contents/articles/20235.azure-active-directory-application-gallery-federated-saas-apps.aspx).

Wenn Sie Ihre Anwendung gefunden haben, können Sie mithilfe der Schrittanleitungen im App-Katalog und im Azure-Verwaltungsportal die einmalige Anmeldung aktivieren.

###Die Anwendung ist nicht im Katalog enthalten?

Wenn Sie Ihre Anwendung nicht im Azure AD-Anwendungskatalog finden, haben Sie folgende Möglichkeiten:

*	**Fügen Sie eine nicht gelistete App hinzu, die Sie verwenden** – Verwenden Sie die Kategorie "Benutzerdefiniert" im App-Katalog des Azure-Verwaltungsportals, um eine nicht gelistete Anwendung zu verbinden, die in Ihrer Organisation verwendet wird. Sie können jede Anwendung hinzufügen, die SAML 2.0 als Verbund-App unterstützt, oder jede Anwendung für die kennwortbasierte einmalige Anmeldung, die über eine HTML-basierte Anmeldeseite verfügt. Weitere Informationen finden Sie in diesem Artikel unter [Hinzufügen Ihrer eigenen Anwendung](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).


*	**Hinzufügen Ihrer eigenen, selbst entwickelten App** – Wenn Sie die Anwendung selbst entwickelt haben, befolgen Sie die Richtlinien in der Azure AD-Entwicklerdokumentation, um die einmalige Verbundanmeldung oder die Bereitstellung mithilfe der Azure AD Graph-API zu implementieren. Weitere Informationen finden Sie in den folgenden Ressourcen:
  * [Authentifizierungsszenarios für Azure AD](active-directory-authentication-scenarios.md)
  * [https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore)

*	**Anfordern einer App-Integration** – Fordern Sie über das [Feedback-Forum für Azure AD](http://feedback.azure.com/forums/169401-azure-active-directory) Unterstützung für die Anwendung an.

###Verwenden des Azure-Verwaltungsportals

Sie können die Active Directory-Erweiterung im Azure-Verwaltungsportal zum Konfigurieren der einmaligen Anmeldung für die Anwendung verwenden. Im ersten Schritt müssen Sie ein Verzeichnis aus dem Active Directory-Abschnitt im Portal auswählen:

![][2]

Um Ihre SaaS-Anwendungen von Drittanbietern zu verwalten, können Sie zur Registerkarte "Anwendungen" des ausgewählten Verzeichnisses wechseln. Über diese Ansicht können Administratoren die folgenden Aufgaben ausführen:

* Hinzufügen neuer Anwendungen aus dem Azure AD-Katalog und Hinzufügen von selbst entwickelten Apps
* Löschen integrierter Anwendungen
* Verwalten der Anwendungen, die bereits integriert wurden

Typische Verwaltungsaufgaben für eine SaaS-Anwendung von Drittanbietern sind:

* Aktivieren der einmaligen Anmeldung bei Azure AD mithilfe von Kennwort-SSO oder, sofern für die SaaS-Zielanwendung verfügbar, Verbund-SSO
* Optional: Aktivieren der Benutzerbereitstellung zum Bereitstellen von Benutzern und Aufheben der Bereitstellung (Verwaltung des Identitätslebenszyklus)
* Für Anwendungen, bei denen die Benutzerbereitstellung aktiviert ist, Auswahl der Benutzer mit Zugriff auf diese Anwendung

Für Katalog-Apps, die die einmalige Verbundanmeldung unterstützen, müssen Sie bei der Konfiguration normalerweise zusätzliche Konfigurationseinstellungen bereitstellen, z. B. Zertifikate und Metadaten zum Erstellen einer Verbundvertrauensstellung zwischen der Drittanbieter-App und Azure AD. Der Konfigurations-Assistent führt Sie durch die Details und bietet einfachen Zugriff auf die spezifischen Daten und Anweisungen der SaaS-Anwendung.

Für Katalog-Apps, die eine automatische Benutzerbereitstellung unterstützen, müssen Sie die entsprechenden Azure AD-Berechtigungen erteilen, um Ihre Konten in der SaaS-Anwendung verwalten zu können. Zumindest müssen Sie Anmeldeinformationen angeben, die Azure AD bei der Authentifizierung bei der Zielanwendung verwenden soll. Ob zusätzliche Konfigurationseinstellungen bereitgestellt werden müssen, hängt von den Anforderungen der Anwendung ab.

##Bereitstellen von Azure AD-integrierte Anwendungen für Benutzer

Azure AD bietet mehrere anpassbare Möglichkeiten, um Anwendungen für Endbenutzer in Ihrer Organisation bereitzustellen:

* Azure AD-Zugriffsbereich
* Office 365-Anwendungsstartprogramm
* Direkte Anmeldung bei Verbund-Apps
* Deep-Links zu verbundenen, kennwortbasierten oder vorhandene Apps

Welche Methode(n) Sie zur Bereitstellung in Ihrer Organisation wählen, liegt in Ihrem eigenen Ermessen.

###Azure AD-Zugriffsbereich

Der "Zugriffsbereich" unter https://myapps.microsoft.com ist ein webbasiertes Portal, das einem Endbenutzer mit einem Organisationskonto im Azure Active Directory erlaubt, cloudbasierte Anwendungen anzuzeigen und zu starten, für die der Azure AD-Administrator Zugriff gewährt hat. Als [Azure Active Directory Premium](http://azure.microsoft.com/pricing/details/active-directory/)-Endbenutzer können Sie auch die Funktionen zur Self-Service-Gruppenverwaltung über den Zugriffsbereich verwenden.

![][3]

Der Zugriffsbereich ist unabhängig vom Azure-Verwaltungsportal und setzt nicht voraus, dass Benutzer über ein Azure- oder Office 365-Abonnement verfügen.

Weitere Informationen zum Azure AD-Zugriffsbereich finden Sie in der [Einführung in den Zugriffsbereich](active-directory-saas-access-panel-introduction.md).

###Office 365-Anwendungsstartprogramm

Für Organisationen, die Office 365 bereitgestellt haben, werden Anwendungen, die Benutzern über Azure AD zugewiesen werden, auch im Office 365-Portal unter https://portal.office.com/myapps angezeigt. So können Benutzer in einer Organisation auf einfache und komfortable Weise ihre Apps starten, ohne ein zweites Portal verwenden zu müssen. Diese Lösung für den App-Start wird für Unternehmen empfohlen, die Office 365 einsetzen.

![][4]

Weitere Informationen zum Office 365-Anwendungsstartprogramm finden Sie unter [Anzeige Ihrer App im Office 365-Anwendungsstartprogramm](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

###Direkte Anmeldung bei Verbund-Apps

Die meisten Verbundanwendungen, die SAML 2.0, WS-Verbund oder OpenID Connect unterstützen, bieten die Möglichkeit, dass Benutzer die Anwendung aufrufen, und dann entweder durch automatische Umleitung oder Klicken auf einen Link zur Anmeldung über Azure AD angemeldet werden. Dies bezeichnet man als vom Dienstanbieter initiierte Anmeldung, und die meisten Verbundanwendungen im Azure AD-Anwendungskatalog unterstützen diese Form der Anmeldung (weitere Informationen finden Sie in der Dokumentation, die Sie im Azure-Verwaltungsportal über einen Link im Assistenten zum Konfigurieren der einmaligen Anmeldung für eine App aufrufen können).

![][5]

###Links zur direkten Anmeldung für Verbund-Apps, kennwortbasierte oder vorhandene Apps

Azure AD unterstützt auch Direktlinks für die einmalige Anmeldung bei einzelnen Anwendungen, die eine kennwortbasierte einmalige Anmeldung, vorhandenes einmaliges Anmelden und eine beliebige Art der einmaligen Verbundanmeldung unterstützen.

Diese Links sind speziell gestaltete URLs, die einen Benutzer durch den Azure AD-Anmeldevorgang für eine bestimmte Anwendung leiten, ohne dass der Benutzer die Anwendung aus dem Azure AD-Zugriffsbereich oder Office 365 starten muss. Diese URLs für die einmalige Anmeldung finden Sie auf der Registerkarte "Dashboard" aller vorab integrierten Anwendungen im Active Directory-Abschnitt des Azure-Verwaltungsportals, wie im folgenden Screenshot gezeigt.

![][6]

Diese Links können Sie kopieren und überall dort einfügen, wo Sie einen Anmeldelink zur ausgewählten Anwendung bereitstellen möchten. Dies kann eine E-Mail oder ein beliebiges benutzerdefiniertes webbasiertes Portal sein, das Sie für den Benutzerzugriff auf eine Anwendung eingerichtet haben. Hier sehen Sie ein Beispiel für eine Azure AD-Direkt-URL zur einmaligen Anmeldung für Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Ähnlich wie organisationsspezifische URLs für den Zugriffsbereich können Sie diese URL durch Hinzufügen der aktiven oder überprüften Domänen für Ihr Verzeichnis nach der Domäne "myapps.microsoft.com" weiter anpassen. So wird sichergestellt, dass jegliches Organisationsbranding sofort auf der Anmeldeseite geladen wird, ohne dass Benutzer vorher ihre Benutzer-ID eingeben müssen:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Wenn autorisierte Benutzer auf einen dieser anwendungsspezifischen Links klicken, sehen sie zuerst die Anmeldeseite der Organisation (vorausgesetzt, dass sie noch nicht angemeldet sind), und nach dem Anmelden werden sie ohne Zwischenschritt über den Zugriffsbereich zu ihrer App umgeleitet. Wenn Voraussetzungen für den Zugriff auf die Anwendung nicht erfüllt sind, wenn z. B. die kennwortbasierte Browsererweiterung zur einmaligen Anmeldung fehlt, wird der Benutzer über den Link zur Installation der fehlenden Erweiterung aufgefordert. Die Link-URL bleibt auch dann unverändert, wenn die Konfiguration für die einmalige Anmeldung geändert wird.

Diese Links verwenden die gleichen Mechanismen der Zugriffssteuerung wie der Zugriffsbereich und Office 365, und nur die Benutzer oder Gruppen, die der Anwendung im Azure-Verwaltungsportal zugewiesen wurden, können sich authentifizieren. Alle nicht autorisierten Benutzer werden in einer Meldung darüber informiert, dass ihnen kein Zugriff gewährt wurde. Anschließend wird ein Link zum Laden des Zugriffsbereichs angezeigt, um verfügbare Anwendungen anzuzeigen, auf die zugegriffen werden kann.

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-appssoaccess-whatis/onlineappgallery.png
[2]: ./media/active-directory-appssoaccess-whatis/azuremgmtportal.png
[3]: ./media/active-directory-appssoaccess-whatis/accesspanel.png
[4]: ./media/active-directory-appssoaccess-whatis/officeapphub.png
[5]: ./media/active-directory-appssoaccess-whatis/workdaymobile.png
[6]: ./media/active-directory-appssoaccess-whatis/deeplink.png

<!---HONumber=August15_HO9-->