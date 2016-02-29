<properties
   pageTitle="Azure AD Connect: Funktionen in der Vorschau | Microsoft Azure"
   description="In diesem Thema werden Funktionen detaillierter beschrieben, die sich in Azure AD Connect in der Vorschau befinden."
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
   ms.date="02/16/2016"
   ms.author="andkjell;billmath"/>

# Weitere Informationen zu den Funktionen in der Vorschau
In diesem Thema wird beschrieben, wie Sie Funktionen verwenden, die sich derzeit in der Vorschau befinden.

## Gruppenrückschreiben
Mit der Option zum Gruppenrückschreiben, die unter den optionalen Features aufgeführt ist, können Sie **Office 365-Gruppen** in eine Gesamtstruktur zurückschreiben, wenn Exchange installiert ist. Dies ist eine Gruppe, die immer in der Cloud verwaltet wird (Master in der Cloud). Wenn Sie lokal über Exchange verfügen, können Sie diese Gruppen zur lokalen Installation zurückschreiben, damit Benutzer mit einem lokalen Exchange-Postfach E-Mails von diesen Gruppen empfangen und an sie senden können.

Weitere Informationen zu Office 365-Gruppen und zu deren Verwendung finden Sie [hier](http://aka.ms/O365g).

Diese Gruppe wird als Verteilergruppe im lokalen AD DS dargestellt. Auf dem lokalen Exchange-Server muss das kumulative Update 8 für Exchange Server 2013 (vom März 2015) oder Exchange 2016 installiert sein, damit dieser neue Gruppentyp erkannt wird.

**Hinweise während der Vorschau**

- Das Adressbuchattribut ist in der Vorschau derzeit nicht aufgefüllt. Ohne dieses Attribut ist diese Gruppe in der GAL (Global Address List, globale Adressliste) nicht sichtbar. Am einfachsten können Sie dieses Attribut mit dem Exchange PowerShell-Cmdlet `update-recipient` auffüllen.
- Nur Gesamtstrukturen mit dem Exchange-Schema sind gültige Ziele für Gruppen. Wenn kein Exchange-Schema erkannt wurde, kann das Gruppenrückschreiben nicht aktiviert werden.
- Derzeit werden nur Bereitstellungen unterstützt, bei der eine Exchange-Organisation eine einzelne Gesamtstruktur darstellt. Wenn Sie lokal über mehrere Exchange-Organisationen verfügen, benötigen Sie eine lokale GAL-Synchronisierungslösung, damit diese Gruppen in Ihren anderen Gesamtstrukturen angezeigt werden können.
- Das Feature „Gruppenrückschreiben“ verarbeitet derzeit keine Sicherheitsgruppen oder Verteilergruppen.

>[AZURE.NOTE] Für das Gruppenrückschreiben ist ein Azure AD Premium-Abonnement erforderlich.

## Rückschreiben von Benutzern
> [AZURE.IMPORTANT] Die Vorschaufunktion „Rückschreiben von Benutzern“ wurde im Azure AD Connect-Update vom August 2015 vorübergehend entfernt. Wenn Sie diese Funktion aktiviert haben, sollten Sie sie deaktivieren.

„Rückschreiben von Benutzern“ befindet sich in einem frühen Vorschaustadium. Die Funktion kann nur verwendet werden, wenn Azure AD die Quelle für alle Benutzerobjekte ist und das lokale Active Directory leer ist, bevor Sie die Funktion aktivieren (Bereitstellung ohne bestehende Infrastruktur).

>[AZURE.WARNING] Diese Funktion sollte nur in einer Testumgebung evaluiert und nicht in einem Azure AD-Verzeichnis verwendet werden, das für die Produktion verwendet wird.

.

>[AZURE.NOTE] Für das Rückschreiben von Benutzern ist ein Azure AD Premium-Abonnement erforderlich.

## Nächste Schritte
Fahren Sie mit Ihrer [benutzerdefinierten Installation von Azure AD Connect](active-directory-aadconnect-get-started-custom.md) fort.

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->