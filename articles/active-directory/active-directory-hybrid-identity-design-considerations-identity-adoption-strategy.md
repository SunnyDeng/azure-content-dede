<properties
	pageTitle="Überlegungen zum Entwurf der Azure Active Directory-Hybrididentität – Definieren einer Strategie zur Hybrididentitätsübernahme | Microsoft Azure"
	description="Mit der bedingten Zugriffssteuerung überprüft Azure Active Directory die besonderen Bedingungen, die Sie beim Authentifizieren des Benutzers und vor dem Gewähren des Zugriffs auf die Anwendung auswählen. Nachdem diese Bedingungen erfüllt sind, wird der Benutzer authentifiziert und erhält Zugriff auf die Anwendung."
	documentationCenter=""
	services="active-directory"
	authors="billmath"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="11/11/2015"
	ms.author="billmath"/>


# Definieren einer Strategie zur Hybrididentitätsübernahme

In dieser Aufgabe definieren Sie die Strategie zur Hybrididentitätsübernahme für Ihre Hybrididentitätslösung, um die geschäftlichen Anforderungen zu erfüllen, die hier erörtert wurden:

- [Ermitteln von Geschäftsanforderungen](active-directory-hybrid-identity-design-considerations-business-needs.md)
- [Ermitteln der Anforderungen an die Verzeichnissynchronisierung](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
- [Ermitteln der Anforderungen für die Multi-Factor Authentication](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## Definieren der Strategie für Geschäftsanforderungen
Die erste Aufgabe ist, die Geschäftsanforderungen der Organisation zu ermitteln. Dies kann sehr umfangreich sein, und Sie müssen darauf achten, sich auf das Wesentliche zu konzentrieren. Der Entwurf sollte zu Anfang einfach sein, jedoch planen Sie stets ein, dass er in der Zukunft leicht geändert werden kann. Für einfache und äußerst komplexe Entwürfe ist Azure Active Directory die Microsoft Identity-Plattform, die Office 365, Microsoft Online Services und cloudtaugliche Anwendungen unterstützt.

## Definieren einer Integrationsstrategie
Die drei wichtigsten Integrationsszenarien von Microsoft sind Cloudidentitäten, synchronisierte Identitäten und verbundene Identitäten. Sie sollten eine dieser Integrationsstrategien anwenden. Die Strategie, die Sie auswählen, kann variieren, und die Auswahlentscheidungen können davon abhängen, welche Art von Benutzeroberfläche Sie bereitstellen möchten, ob ein Teil der vorhandenen Infrastruktur bereits eingerichtet und welche Lösung die kostengünstigste ist.
 
![](./media/hybrid-id-design-considerations/integration-scenarios.png)

In der obigen Abbildung sind folgende Szenarien definiert:

- **Cloudidentitäten**: Hierbei handelt es sich um Identitäten, die ausschließlich in der Cloud vorhanden sind. Im Fall von Azure AD würden sie sich speziell in Ihrem Azure AD-Verzeichnis befinden.
- **Synchronisiert**: Hierbei handelt es sich um Identitäten, die lokal vorhanden sind und in der Cloud. Mithilfe von Azure AD Connect werden diese Benutzer entweder erstellt oder mit vorhandenen Azure AD-Konten verknüpft. Das Kennworthash des Benutzers in der lokalen Umgebung wird mit der Cloud synchronisiert. Beim Synchronisieren ist allerdings Folgendes zu beachten: Wenn ein Benutzer in der lokalen Umgebung deaktiviert ist, kann es bis zu 3 Stunden dauern, bis der Kontostatus in Azure AD angezeigt wird. Dies liegt am Zeitintervall für die Synchronisierung.
- **Im Verbund**: Diese Identitäten sind sowohl lokal als auch in der Cloud vorhanden. Mithilfe von Azure AD Connect werden diese Benutzer entweder erstellt oder mit vorhandenen Azure AD-Konten verknüpft.  
 
>[AZURE.NOTE]Weitere Informationen zu den Optionen für die Synchronisierung finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](https://azure.microsoft.com/de-DE/documentation/articles/active-directory-aadconnect/).

Die folgende Tabelle hilft Ihnen bei der Bestimmung der Vor- und Nachteile der folgenden Strategien:

| Strategie | Vorteile | Nachteile |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Cloudidentitäten** | Für kleine Organisationen einfacher zu verwalten. <br> Keine lokale Installation – keine zusätzliche Hardware erforderlich<br>Problemlos deaktivierbar, wenn der Benutzer das Unternehmen verlässt | Benutzer müssen sich für den Zugriff auf Workloads in der Cloud anmelden <br> Kennwörter können, müssen aber nicht für Cloud- und lokale Identitäten identisch sein |
| **Synchronisiert** | Lokales Kennwort dient sowohl für lokale als auch Cloudverzeichnisse zur Authentifizierung <br>Einfacher zu verwalten für kleine, mittlere und große Organisationen <br>Benutzer können einmaliges Anmelden (Single Sign-On, SSO) für einige Ressourcen nutzen <br> Von Microsoft bevorzugte Methode für die Synchronisierung <br> Einfacher zu verwalten | Einige Kunden sind aufgrund spezifischer Unternehmensrichtlinien möglicherweise zurückhaltend bezüglich der Synchronisierung ihrer Verzeichnisse mit der Cloud |
| **Im Verbund** | Benutzer können einmaliges Anmelden (Single Sign-On, SSO) nutzen <br>Wenn ein Benutzer ausscheidet oder das Unternehmen verlässt, kann das Konto sofort deaktiviert und der Zugriff aufgehoben werden <br> Unterstützt erweiterte Szenarien, die mit Synchronisierung nicht möglich sind | Mehr Schritte können eingerichtet und konfiguriert werden <br> Höherer Wartungsaufwand <br> Erfordert eventuell zusätzliche Hardware für die STS-Infrastruktur <br> Erfordert eventuell zusätzliche Hardware zur Installation des Verbundservers. Wenn AD FS verwendet wird, ist zusätzliche Software erforderlich <br> Erfordert umfangreiches Setup für SSO <br> Entscheidende Fehlerstelle, wenn der Verbundserver ausfällt; Benutzer können sich nicht authentifizieren |

### Clienterfahrung
Von der Strategie, die Sie verwenden, hängt die Benutzeranmeldung ab. Die folgenden Tabellen zeigen Ihnen, wie die jeweilige Benutzeranmeldung aussieht. Beachten Sie, dass nicht alle Anbieter von Verbundidentität SSO in allen Szenarien unterstützen.

**Einer Domäne angehörende und private Netzwerkanwendungen**:
 

| | Synchronisierte Identität | Verbundidentität |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Webbrowser | Formularbasierte Authentifizierung | Einmaliges Anmelden, manchmal muss die Organisations-ID angegeben werden |
| Outlook | Aufforderung zur Eingabe von Anmeldeinformationen | Aufforderung zur Eingabe von Anmeldeinformationen |
| Skype for Business (Lync) | Aufforderung zur Eingabe von Anmeldeinformationen | Einmaliges Anmelden für Lync, Aufforderung zur Eingabe von Anmeldeinformationen für Exchange |
| Skydrive Pro | Aufforderung zur Eingabe von Anmeldeinformationen | Einmaliges Anmelden |
| Office Pro Plus-Abonnement | Aufforderung zur Eingabe von Anmeldeinformationen | Einmaliges Anmelden |

**Externe oder nicht vertrauenswürdige Quellen**:

| | Synchronisierte Identität | Verbundidentität |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Webbrowser | Formularbasierte Authentifizierung | Formularbasierte Authentifizierung |
| Outlook, Skype for Business (Lync), Skydrive Pro, Office-Abonnement| Aufforderung zur Eingabe von Anmeldeinformationen | Aufforderung zur Eingabe von Anmeldeinformationen |
| Exchange ActiveSync | Aufforderung zur Eingabe von Anmeldeinformationen | Einmaliges Anmelden für Lync, Aufforderung zur Eingabe von Anmeldeinformationen für Exchange |
| Mobile Apps | Aufforderung zur Eingabe von Anmeldeinformationen | Aufforderung zur Eingabe von Anmeldeinformationen |

Wenn Sie in Aufgabe 1 ermittelt haben, dass Sie einen Drittanbieter-IdP haben oder verwenden werden, um einen Verbund mit Azure AD bereitzustellen, müssen Sie die folgenden unterstützten Funktionen kennen: - Jeder SAML 2.0-Anbieter, der mit dem SP-Lite-Profil kompatibel ist, kann die Authentifizierung bei Azure AD und zugehörigen Anwendungen unterstützen - Passive Authentifizierung wird unterstützt, was die Authentifizierung bei OWA, SPO usw. erleichtert – Exchange Online-Clients können über das SAML 2.0 Enhanced Client Profile (ECP) unterstützt werden

Sie müssen auch wissen, welche Funktionen nicht zur Verfügung stehen:

- Ohne Unterstützung von WS-Trust/-Federation fallen alle anderen aktiven Clients weg
 - Das bedeutet, kein Lync-Client, OneDrive-Client, Office-Abonnement, Office Mobile vor Office 2016
- Der Übergang von Office zur passiven Authentifizierung erlaubt die Unterstützung reiner SAML 2.0-IdPs, aber die Unterstützung erfolgt immer noch auf „Client-by-Client“-Basis


>[AZURE.NOTE]Die aktuellste Liste finden Sie im Artikel http://aka.ms/ssoproviders.

## Definieren der Strategie für die Synchronisierung
In dieser Aufgabe definieren Sie, welche Tools verwendet werden, um die lokalen Daten der Organisation mit der Cloud zu synchronisieren, und welche Topologie Sie verwenden sollten. Da die meisten Organisationen Active Directory verwenden, werden Informationen zur Verwendung von Azure AD Connect zur Behandlung der obigen Fragen detailliert dargestellt. Für Umgebungen, die nicht über Active Directory verfügen, stehen Informationen zur Verwendung von FIM 2010 R2 oder MIM 2016 zur Verfügung, um die Planung dieser Strategie zu unterstützen. Zukünftige Versionen von Azure AD Connect werden jedoch LDAP-Verzeichnisse unterstützen – je nachdem, wie Ihr Zeitplan aussieht, können diese Informationen möglicherweise hilfreich sein.

###Synchronisierungstools
Im Laufe der Jahre wurden mehrere Synchronisierungstools für verschiedene Szenarien verwendet. Derzeit ist Azure AD Connect das Tool der Wahl für alle unterstützten Szenarien. AAD Sync und DirSync werden auch noch eingesetzt und könnten sogar jetzt in Ihrer Umgebung vorhanden sein.

>[AZURE.NOTE]Aktuelle Informationen zu den unterstützten Funktionen der einzelnen Tools finden Sie im Artikel [Vergleich von Tools für die Verzeichnisintegration](active-directory-aadconnect-get-started-tools-comparison.md).

### Unterstützte Topologien
Bei der Definition einer Strategie für die Synchronisierung muss die verwendete Topologie bestimmt werden. Je nach den Informationen, die in Schritt 2 ermittelt wurden, können Sie bestimmen, welche Topologie die richtige ist. Die einzelne Gesamtstruktur, einzelne Azure AD-Topologie wird am häufigsten verwendet und besteht aus einer einzelnen Active Directory-Gesamtstruktur und einer einzelnen Instanz von Azure AD. Sie wird in der Mehrzahl der Szenarien verwendet und ist die erwartete Topologie beim Einsatz der Expressinstallation von Azure AD Connect, wie in der folgenden Abbildung dargestellt.
 
![](./media/hybrid-id-design-considerations/single-forest.png) Das Szenario der einzelnen Gesamtstruktur wird sehr häufig für große und auch kleine Organisationen gewählt, um mehrere Gesamtstrukturen zu haben, wie in Abbildung 5 dargestellt.

>[AZURE.NOTE]Weitere Informationen zu den verschiedenen lokalen und Azure AD-Topologien mit Azure AD Connect-Synchronisierung finden Sie im Artikel [Topologien für Azure AD Connect](active-directory-aadconnect-topologies.md).


![](./media/hybrid-id-design-considerations/multi-forest.png)

Szenario mit mehreren Gesamtstrukturen

Wenn dies der Fall ist, sollte die einzelne Azure AD-Topologie mit mehreren Gesamtstrukturen erwogen werden, wenn Folgendes zutrifft:

- Benutzer haben nur 1 Identität in allen Gesamtstrukturen – weiter unten im Abschnitt zu sich eindeutig identifizierenden Benutzern wird dies ausführlich beschrieben.
- Der Benutzer authentifiziert sich bei der Gesamtstruktur, in der seine Identität gespeichert ist.
- UPN und Quellanker (unveränderliche ID) stammen aus dieser Gesamtstruktur.
- Auf alle Gesamtstrukturen kann Azure AD Connect zugreifen – dies bedeutet, dass kein Domänenbeitritt erforderlich ist und ggf. zur Erleichterung die Platzierung in einer DMZ möglich ist.
- Benutzer haben nur ein einziges Postfach.
- Die Gesamtstruktur, die das Postfach eines Benutzers hostet, hat die beste Datenqualität für Attribute, die in der globalen Adressliste von Exchange (Global Address List, GAL) sichtbar sind.
- Wenn kein Postfach für den Benutzer vorhanden ist, kann jede Gesamtstruktur verwendet werden, um diese Werte beizutragen.
- Wenn Sie ein verknüpftes Postfach haben, dann wird auch ein anderes Konto in einer anderen Gesamtstruktur für die Anmeldung verwendet.

>[AZURE.NOTE]Objekte, die sowohl lokal als auch in der Cloud vorhanden sind, werden über einen eindeutigen Bezeichner „verbunden“. Dieser eindeutige Bezeichner wird im Kontext der Verzeichnissynchronisierung als „SourceAnchor“ bezeichnet. Im Kontext des einmaligen Anmeldens wird er als „ImmutableId“ bezeichnet. In [Entwurfskonzepte für Azure AD Connect](https://azure.microsoft.com/de-DE/documentation/articles/active-directory-aadconnect-design-concepts/#sourceanchor) finden Sie weitere Überlegungen zur Verwendung von „SourceAnchor“.

Wenn die obigen nicht zutreffen und Sie mehrere aktive Konten oder Postfächer haben, wählt Azure AD Connect eines von ihnen aus und ignoriert die anderen. Wenn Sie verknüpfte Postfächer, jedoch kein anderes Konto haben, werden diese Konten nicht in Azure AD exportiert, und dieser Benutzer wird nicht Mitglied einer Gruppe. Dies ist ein beabsichtigter Unterschied zur früheren Situation mit DirSync und soll diese Szenarien mit mehreren Gesamtstrukturen besser unterstützen. Ein Szenario mit mehreren Gesamtstrukturen ist in der folgenden Abbildung dargestellt.
 
![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png)
 
**Szenario mit mehreren Gesamtstrukturen und mehreren Azure AD-Verzeichnissen**

In Azure AD sollte nur ein einzelnes Verzeichnis für eine Organisation vorhanden sein, doch eine 1:1-Beziehung zwischen einem Azure AD Connect-Synchronisierungsserver und einem Azure AD-Verzeichnis wird unterstützt. Für jede Instanz von Azure AD benötigen Sie eine Installation von Azure AD Connect. Darüber hinaus ist Azure AD vom Konzept her isoliert, und Benutzer in einer Instanz von Azure AD können Benutzer in anderen Instanzen nicht sehen.

Eine lokale Instanz von Active Directory wie in der folgenden Abbildung dargestellt mit mehreren Azure AD-Verzeichnissen zu verbinden, ist möglich und wird unterstützt:

![](./media/hybrid-id-design-considerations/single-forest-flitering.png)
 

**Szenario einer einzelnen Gesamtstruktur mit Filterung**

Hierfür gelten folgende Voraussetzungen:

- Die Azure AD Connect-Synchronisierungsserver müssen für das Filtern konfiguriert werden, sodass sie über jeweils exklusive Gruppen von Objekten verfügen. Hierzu wird z. B. der Gültigkeitsbereich jedes Servers auf eine bestimmte Domäne oder OE festgelegt.
- Eine DNS-Domäne kann nur in einem einzelnen Azure AD-Verzeichnis registriert werden, sodass die UPNs der Benutzer im lokalen AD separate Namespaces verwenden müssen.
- Benutzer in einer Instanz von Azure AD können nur Benutzer aus ihrer Instanz sehen. Sie können keine Benutzer in anderen Instanzen sehen.
- Nur eines der Azure AD-Verzeichnisse kann den Exchange-Hybrid mit dem lokalen AD aktivieren.
- Gegenseitige Ausschließlichkeit gilt auch für das Zurückschreiben. Folglich werden einige Zurückschreibfeatures mit dieser Topologie nicht unterstützt, da sie eine lokale Einzelkonfiguration voraussetzen. Dies umfasst:
 - Gruppenzurückschreibung mit Standardkonfiguration
 - Gerätezurückschreibung


Achten Sie darauf, dass Folgendes nicht unterstützt wird und nicht als Implementierung ausgewählt werden sollte:

- Die Verbindung mehrerer Azure AD Connect-Synchronisierungsserver mit dem gleichen Azure AD-Verzeichnis wird auch dann nicht unterstützt, wenn sie dazu konfiguriert sind, jeweils exklusive Gruppen von Objekten zu synchronisieren.
- Die Synchronisierung des gleichen Benutzers mit mehreren Azure AD-Verzeichnissen wird nicht unterstützt. 
- Es wird auch nicht unterstützt, die Konfiguration so zu ändern, dass Benutzer in einem Azure AD in einem anderen Azure AD-Verzeichnis als Kontakte angezeigt werden. 
- Es wird auch nicht unterstützt, die Azure AD Connect-Synchronisierung so zu ändern, dass Verbindungen zu mehreren Azure AD-Verzeichnissen möglich sind.
- Azure AD-Verzeichnisse sind als isoliert konzipiert. Es wird nicht unterstützt, die Konfiguration der Azure AD Connect-Synchronisierung so zu ändern, dass in einem Versuch, eine gemeinsame und einheitliche GAL zwischen den Verzeichnissen zu erstellen, Daten aus einem anderen Azure AD-Verzeichnis gelesen werden. Es wird auch nicht unterstützt, mithilfe der Azure AD Connect-Synchronisierung Benutzer als Kontakte nach einem anderen lokalen AD zu exportieren.


>[AZURE.NOTE]Für den Fall, dass Ihre Organisation die Verbindung von Computern Ihres Netzwerks mit dem Internet einschränkt, werden in diesem Artikel die Endpunkte aufgeführt (FQDNs, IPv4- und IPv6-Adressbereiche), die Sie in Ihre Ausgangszulassungslisten und Zone vertrauenswürdiger Sites von Clientcomputern im Internet Explorer einbeziehen sollten, um sicherzustellen, dass Ihre Computer Office 365 erfolgreich verwenden können. Weitere Informationen finden Sie unter [URLs und IP-Adressbereiche von Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=de-DE&rs=de-DE&ad=US).

## Definieren der Multi-Factor Authentication-Strategie
In dieser Aufgabe definieren Sie die Strategie der zu verwendenden mehrstufigen Authentifizierung. Azure Multi-Factor Authentication ist in zwei verschiedenen Versionen verfügbar. Eine ist cloudbasiert und die andere lokal basiert mit Einsatz des Azure MFA-Servers. Basierend auf der Auswertung, die Sie oben ausgeführt haben, können Sie die richtige Lösung für Ihre Strategie ermitteln. Bestimmen Sie mithilfe der folgenden Tabelle, welche Entwurfsoption für die Sicherheitsanforderungen Ihres Unternehmens optimal ist:

Mehrstufige Entwurfsoptionen:

| Zu sichernde Ressourcen | MFA in der Cloud | MFA lokal |
|---------------------------------------------------------------|------------------|----------------|
| Microsoft-Apps | Ja | Ja |
| SaaS-Apps im Appkatalog | Ja | Ja |
| Über den Azure AD-App-Proxy veröffentlichte IIS-Anwendungen | Ja | Ja |
| Nicht über den Azure AD-App-Proxy veröffentlichte IIS-Anwendungen | no | Ja |
| Remotezugriff wie VPN, RDG | no | Ja |

Selbst wenn Sie sich für eine Lösung für Ihre Strategie entschieden haben, müssen Sie noch mit der obigen Auswertung feststellen, wo sich die Benutzer befinden. Dies kann zu einer Änderung der Lösung führen. Verwenden Sie für diese Feststellung die folgende Tabelle:

| Benutzerstandort | Bevorzugte Entwurfsoption |
|---------------------------------------------------------------------|-----------------------------------------|
| Azure Active Directory | Multi-Factor Authentication in der Cloud |
| Azure AD und lokales AD über einen Verbund mit AD FS | Beides |
| Azure AD und lokales AD mit Azure AD Connect ohne Kennwortsynchronisierung | Beides |
| Azure AD und lokales AD mit Azure AD Connect mit Kennwortsynchronisierung | Beides |
| Lokales AD | Multi-Factor Authentication-Server |

>[AZURE.NOTE]Sie sollten außerdem sicherstellen, dass die ausgewählte Entwurfsoption für die mehrstufige Authentifizierung die Features unterstützt, die für Ihren Entwurf erforderlich sind. Weitere Informationen finden Sie unter [Auswählen der richtigen mehrstufigen Sicherheitslösung](https://azure.microsoft.com/documentation/articles/multi-factor-authentication-get-started/#what-am-i-trying-to-secure).

## Multi-Factor Authentication-Anbieter
Multi-Factor Authentication ist standardmäßig für globale Administratoren verfügbar, die über einen Azure Active Directory-Mandanten verfügen. Wenn Sie jedoch Multi-Factor Authentication für alle Benutzer verfügbar machen möchten und/oder wenn Sie möchten, dass die globalen Administratoren Features wie das Verwaltungsportal, benutzerdefinierte Grußformeln und Berichte nutzen können, müssen Sie einen Multi-Factor Authentication-Anbieter erwerben und konfigurieren.

>[AZURE.NOTE]Sie sollten außerdem sicherstellen, dass die ausgewählte Entwurfsoption für die mehrstufige Authentifizierung die Features unterstützt, die für Ihren Entwurf erforderlich sind.

##Nächste Schritte
[Bestimmen der Datenschutzanforderungen](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## Weitere Informationen
[Design considerations overview]((active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=Nov15_HO4-->