<properties
   pageTitle="Tokenformat für externe Benutzer bei der Vorschau der Azure Active Directory B2B-Zusammenarbeit | Microsoft Azure"
   description="Azure Active Directory B2B ermöglicht Geschäftspartnern den gezielten Zugriff auf Ihre Unternehmensanwendungen und unterstützt so Ihre unternehmensübergreifenden Beziehungen."
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="02/03/2016"
   ms.author="viviali"/>

# Vorschau der Azure AD B2B-Zusammenarbeit: Tokenformat für externe Benutzer

Die Ansprüche für ein standardmäßiges Azure AD-Token werden im Artikel [Unterstützte Token- und Anspruchstypen](active-directory-token-and-claims.md) auf azure.microsoft.com beschrieben.

Die Ansprüche, die für einen für die B2B-Zusammenarbeit authentifizierten externen Benutzer anders sind, lauten wie folgt:<br/> - **OID:** Die Objekt-ID aus dem Ressourcenmandanten<br/> - **TID**: Mandanten-ID aus dem Ressourcenmandanten<br/> - **Aussteller**: Dies ist der Ressourcenmandant<br/> - **IDP**: Hierbei handelt es sich um den Home-Mandanten des Benutzers<br/> - **AltSecId**: Dies ist die alternative Sicherheits-ID, die für Sie nicht transparent ist.<br/>

## Verwandte Artikel
Durchsuchen Sie unsere anderen Artikel zur Azure B2B-Zusammenarbeit:

- [Was ist die Azure AD B2B-Zusammenarbeit?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [So funktioniert's](active-directory-b2b-how-it-works.md)
- [Ausführliche exemplarische Vorgehensweise](active-directory-b2b-detailed-walkthrough.md)
- [Referenz zum CSV-Dateiformat](active-directory-b2b-references-csv-file-format.md)
- [Objektattributänderungen für externe Benutzer](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Aktuelle Einschränkungen der Vorschau](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=AcomDC_0204_2016-->