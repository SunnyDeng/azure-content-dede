<properties
	pageTitle="Azure AD Connect-Synchronisierung: Übersicht über Szenarien mit mehreren Gesamtstrukturen"
    description="Ziel dieses Themas ist, einige allgemeine Szenarien und die Art und Weise zu beschreiben, in der sie im Azure AD Connect-Synchronisierungsdienst dargestellt werden."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect-Synchronisierung: Übersicht über Szenarien mit mehreren Gesamtstrukturen

Viele Organisationen verfügen über Umgebungen, die mehrere lokale Active Directory-Gesamtstrukturen umfassen. Es gibt verschiedene Gründe, mehrere lokale Active Directory-Gesamtstrukturen bereitzustellen. Typische Beispiele sind Entwürfe mit Kontoressourcengesamtstruktur, Gesamtstrukturen in Zusammenhang mit Unternehmensfusionen oder -übernahmen oder Gesamtstrukturen, die zum Auslagern von Daten verwendet werden.

Microsoft bietet zwei Lösungen: DirSync für Szenarien mit einer Gesamtstruktur und Forefront Identity Manager (FIM) für Szenarien mit mehreren Gesamtstrukturen. Allerdings kann das Konfigurieren von FIM eine Herausforderung sein und sehr viel Zeit beanspruchen.

Mit der Azure AD Connect-Synchronisierung können Sie die Konfiguration erheblich vereinfachen und vorhersehbarer machen.

Die Standardkonfiguration der Azure AD Connect-Synchronisierung basiert auf folgenden Annahmen:

1. Benutzer haben nur ein einziges aktiviertes Konto, und die Gesamtstruktur, in der sich dieses Konto befindet, wird verwendet, um den Benutzer einem Verbund zuzuordnen.
2. Benutzer haben nur ein einziges Postfach.
3. Die Gesamtstruktur, die das Postfach des Benutzers enthält, hat die beste Datenqualität für Attribute, die in der globalen Exchange-Adressliste (Global Address List, GAL) sichtbar sind. Wenn kein Postfach für den Benutzer vorhanden ist, kann jede Gesamtstruktur verwendet werden, um diese Attributwerte beizutragen.


Ziel dieses Themas ist, einige allgemeine Szenarien und die Art und Weise zu beschreiben, in der sie im Azure AD Connect-Synchronisierungsdienst dargestellt werden:

1. Separate Technologien 
2. Vollständiges Netz mit optionaler GALSync 
3. Kontoressourcengesamtstruktur 


## Separate Technologien

In dieser Umgebung werden alle Gesamtstrukturen lokal als separate Entitäten behandelt, und kein Benutzer ist in einer anderen Gesamtstruktur vorhanden.<br> Jede Gesamtstruktur verfügt über eine eigene Exchange-Organisation, und es gibt keine GALSync zwischen den Gesamtstrukturen. Dies könnte die Situation nach einer Fusion/Übernahme oder in einer Organisation sein, in der die einzelnen Geschäftseinheiten isoliert voneinander operieren.

![Separate Technologien][1]

In dieser Abbildung wird jedes Objekt in jeder Gesamtstruktur einmal im Metaverse dargestellt und im Azure AD-Zielverzeichnis aggregiert. Aus der Verbindung eines DirSync-Servers mit jeder AD-Quellgesamtstruktur würde das gleiche Ergebnis resultieren.
 




## Vollständiges Netz mit optionaler GALSync

Mit einer vollständig vernetzten Topologie können Benutzer und Ressourcen in jeder Gesamtstruktur ermittelt werden, und in der Regel bestehen bidirektionale Vertrauensstellungen zwischen den Gesamtstrukturen.

Wenn Exchange in mehreren Gesamtstrukturen vorhanden ist, kann optional eine GALSync-Lösung einen Benutzer aus einer Gesamtstruktur in jeder anderen Gesamtstruktur als Kontakt darstellen.

In diesem Szenario werden in der Regel Identitätsobjekte über das E-Mail-Attribut verknüpft. In der Folge wird ein Benutzer mit einem Postfach in einer Gesamtstruktur mit den Kontakten in den anderen Gesamtstrukturen verknüpft. Verteiler- und Sicherheitsgruppen befinden sich in jeder Gesamtstruktur und können eine Kombination aus Benutzern, Kontakten und FSPs (Foreign Security Principals, fremde Sicherheitsprinzipale) enthalten.

Die folgende Abbildung zeigt dieses Szenario.

![Vollständiges Netz mit optionaler GALSync][2]


## Kontoressourcengesamtstruktur
In einer Kontoressourcengesamtstruktur-Topologie befinden sich eine oder mehrere Kontogesamtstrukturen mit aktiven Benutzerkonten.<br> Dieses Szenario besteht aus einer Gesamtstruktur, die allen Kontogesamtstrukturen vertraut. Diese Gesamtstruktur verfügt in der Regel über ein erweitertes AD-Schema mit Exchange und Lync. Alle Dienste von Exchange und Lync sowie andere freigegebene Dienste befinden sich in dieser Gesamtstruktur. Benutzer haben in dieser Gesamtstruktur ein deaktiviertes Benutzerkonto, und das Postfach ist mit der Kontogesamtstruktur verknüpft.

Die folgende Abbildung zeigt dieses Szenario mit nur einem Konto.

![Kontoressourcengesamtstruktur][3]


## Zusätzliche Ressourcen

* [Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](active-directory-aadconnectsync-whatis.md)
* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)

 
<!--Image references-->
[1]: ./media/active-directory-aadsync-scenario-overview/ic750599.png
[2]: ./media/active-directory-aadsync-scenario-overview/ic750600.png
[3]: ./media/active-directory-aadsync-scenario-overview/ic750601.png

<!---HONumber=August15_HO6-->