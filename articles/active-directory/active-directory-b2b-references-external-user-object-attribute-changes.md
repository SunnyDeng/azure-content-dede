<properties
   pageTitle="Objektattributänderungen für externe Benutzer bei der Vorschau der Azure Active Directory B2B-Zusammenarbeit | Microsoft Azure"
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
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Objektattributänderungen für externe Benutzer bei der Vorschau der Azure Active Directory (Azure AD) B2B-Zusammenarbeit
Jeder Benutzer in einem Azure AD-Verzeichnis wird durch ein Benutzerobjekt repräsentiert. Das Benutzerobjekt in Azure AD unterliegt Attributänderungen während der verschiedenen Phasen von der Einladung zur B2B-Zusammenarbeit bis zur Einlösung dieser Einladung. Das Benutzerobjekt, das im Verzeichnis den Partnerbenutzer repräsentiert, besitzt Attribute, die sich zur Einlösungszeit ändern können, wenn der Partnerbenutzer auf den Link in der Einladungs-E-Mail klickt. Dies gilt insbesondere in folgenden Fällen:

- Die Attribute **SignInName** und **AltSecId** werden aufgefüllt.
- Das Attribut **DisplayName** ändert sich vom Benutzerprinzipalnamen (user\_fabrikam.com#EXT#@contoso.com) zum Anmeldenamen (user@fabrikam.com).

Wenn Sie diese Attribute in Azure AD verfolgen, können Sie bei der Problembehandlung feststellen, ob ein Partner seine Einladung zur B2B-Zusammenarbeit eingelöst hat oder nicht.

## Verwandte Artikel
Durchsuchen Sie unsere anderen Artikel zur Azure B2B-Zusammenarbeit:

- [Was ist die Azure AD B2B-Zusammenarbeit?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [So funktioniert's](active-directory-b2b-how-it-works.md)
- [Ausführliche exemplarische Vorgehensweise](active-directory-b2b-detailed-walkthrough.md)
- [Referenz zum CSV-Dateiformat](active-directory-b2b-references-csv-file-format.md)
- [Tokenformat für externe Benutzer](active-directory-b2b-references-external-user-token-format.md)
- [Aktuelle Einschränkungen der Vorschau](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=Nov15_HO1-->