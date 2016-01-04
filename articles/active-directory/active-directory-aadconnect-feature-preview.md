<properties
   pageTitle="Azure AD Connect: Funktionen in der Vorschau | Microsoft Azure"
   description="In diesem Thema werden Funktionen detaillierter beschrieben, die sich in Azure AD Connect in der Preview befinden."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="12/02/2015"
   ms.author="andkjell;billmath"/>

# Weitere Informationen zu den Funktionen in der Preview
In diesem Thema wird beschrieben, wie Sie Funktionen verwenden, die sich derzeit in der Preview befinden.

## Rückschreiben von Benutzern
> [AZURE.IMPORTANT]Die Preview-Funktion "Rückschreiben von Benutzern" wurde im August-Update von AAD Connect vorübergehend entfernt. Wenn Sie diese Funktion aktiviert haben, sollten Sie sie deaktivieren.

"Rückschreiben von Benutzern" befindet sich in einem frühen Preview-Stadium. Die Funktion kann nur verwendet werden, wenn Azure AD die Quelle für alle Benutzerobjekte ist und das lokale Active Directory leer ist, bevor Sie die Funktion aktivieren.

> [AZURE.IMPORTANT]Diese Funktion sollte nur in einer Testumgebung getestet werden und nicht in einem Azure AD-Verzeichnis verwendet werden, das für die Produktion verwendet wird.

## Gruppenrückschreiben
Mit der Option zum Gruppenrückschreiben, die unter den optionalen Features aufgeführt ist, können Sie "Office 365-Gruppen" in eine Gesamtstruktur zurückschreiben, wenn Exchange installiert ist. Dies ist ein neuer Gruppentyp, der immer in der Cloud verwaltet wird. Dies finden Sie unter "outlook.office365.com" oder "myapps.microsoft.com", wie hier gezeigt:


![Synchronisierungsfilterung](./media/active-directory-aadconnect-feature-preview/office365.png)

![Synchronisierungsfilterung](./media/active-directory-aadconnect-feature-preview/myapps.png)

Diese Gruppe wird als Verteilergruppe im lokalen AD DS dargestellt. Auf dem lokalen Exchange-Server muss das kumulative Update 8 für Exchange Server 2013 (vom März 2015) installiert sein, damit dieser neue Gruppentyp erkannt wird.

**Hinweis**

- Das Adressbuchattribut ist in der Preview derzeit nicht aufgefüllt. Verwenden Sie hierzu einfach das Exchange PowerShell-Cmdlet "update-recipient".
- Nur Gesamtstrukturen mit dem Exchange-Schema sind gültige Ziele für Gruppen. Wenn kein Exchange-Schema erkannt wurde, kann das Gruppenrückschreiben nicht aktiviert werden.
- Das Feature "Gruppenrückschreiben" verarbeitet derzeit keine Sicherheitsgruppen oder Verteilergruppen.

Weitere Informationen zu Office 365-Gruppen finden Sie [hier](http://aka.ms/O365g).

## Verzeichniserweiterungen
Verzeichniserweiterungen ermöglichen Ihnen das Erweitern des Schemas in Azure AD mit Ihrer eigenen Attribute aus dem lokalen Active Directory.

Es werden nur einwertige Attribute unterstützt, und die Werte in den Attributen können nicht länger als 250 Zeichen sein. Das Metaverse- und Azure AD-Schema werden mit den ausgewählten Attributen erweitert. In Azure AD wird eine neue Anwendung mit den Attributen hinzugefügt.

![Synchronisierungsfilterung](./media/active-directory-aadconnect-feature-preview/extension3.png)

Diese Attribute sind jetzt über Graph verfügbar:

![Synchronisierungsfilterung](./media/active-directory-aadconnect-feature-preview/extension4.png)

## Nächste Schritte
Fahren Sie mit Ihrer [benutzerdefinierten Installation von Azure AD Connect](active-directory-aadconnect-get-started-custom.md) fort.

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_1203_2015-->