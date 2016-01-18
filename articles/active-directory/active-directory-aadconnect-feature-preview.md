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
   ms.date="12/30/2015"
   ms.author="andkjell;billmath"/>

# Weitere Informationen zu den Funktionen in der Vorschau
In diesem Thema wird beschrieben, wie Sie Funktionen verwenden, die sich derzeit in der Vorschau befinden.

## Verzeichniserweiterungen
Verzeichniserweiterungen ermöglichen Ihnen das Erweitern des Schemas in Azure AD mit Ihren eigenen Attributen aus dem lokalen Active Directory. Dadurch können Sie Branchen-Apps erstellen, die weiterhin lokal verwaltete Attribute nutzen. Diese Attribute können über [Azure AD Graph-Verzeichniserweiterungen](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) oder über [Microsoft Graph](https://graph.microsoft.io/) genutzt werden. Sie können die verfügbaren Attribute je nach Lösung mithilfe von [Azure AD Graph-Explorer](https://graphexplorer.cloudapp.net) oder [Microsoft Graph-Explorer](https://graphexplorer2.azurewebsites.net/) anzeigen.

Derzeit können diese Attribute nicht von Office 365-Workloads genutzt werden.

Während der Installation von Azure AD Connect wird für eine Anwendung registriert, wo diese Attribute zur Verfügung stehen. Sie können diese Anwendung im Azure-Portal sehen. ![Schemaerweiterungs-App](./media/active-directory-aadconnect-feature-preview/extension3.png)

Diese Attribute sind jetzt über Graph verfügbar: ![Grafik](./media/active-directory-aadconnect-feature-preview/extension4.png)

Die Attribute haben das Präfix „extension\_{AppClientId}\_“. Die AppClientId hat den gleichen Wert für alle Attribute in Ihrem Azure AD-Verzeichnis.

Es werden nur einwertige Attribute unterstützt, und die Werte in den Attributen können nicht länger als 250 Zeichen sein.

## Gruppenrückschreiben
Mit der Option zum Gruppenrückschreiben, die unter den optionalen Features aufgeführt ist, können Sie „Office 365-Gruppen“ in eine Gesamtstruktur zurückschreiben, sofern Exchange installiert ist. Dies ist eine Gruppe, die immer in der Cloud verwaltet wird (Master in der Cloud). Wenn Sie lokal über Exchange verfügen, können Sie diese Gruppen zur lokalen Installation zurückschreiben, damit Benutzer mit einem lokalen Exchange-Postfach E-Mails von diesen Gruppen empfangen und an sie senden können.

Weitere Informationen zu Office 365-Gruppen und zu deren Verwendung finden Sie [hier](http://aka.ms/O365g).

Diese Gruppe wird als Verteilergruppe im lokalen AD DS dargestellt. Auf dem lokalen Exchange-Server muss das kumulative Update 8 für Exchange Server 2013 (vom März 2015) oder Exchange 2016 installiert sein, damit dieser neue Gruppentyp erkannt wird.

**Hinweise während der Vorschau**

- Das Adressbuchattribut ist in der Vorschau derzeit nicht aufgefüllt. Ohne dieses Attribut ist diese Gruppe in der GAL (Global Address List, globale Adressliste) nicht sichtbar. Am einfachsten können Sie dieses Attribut mit dem Exchange PowerShell-Cmdlet `update-recipient` auffüllen.
- Nur Gesamtstrukturen mit dem Exchange-Schema sind gültige Ziele für Gruppen. Wenn kein Exchange-Schema erkannt wurde, kann das Gruppenrückschreiben nicht aktiviert werden.
- Derzeit werden nur Bereitstellungen unterstützt, bei der eine Exchange-Organisation eine einzelne Gesamtstruktur darstellt. Wenn Sie lokal über mehrere Exchange-Organisationen verfügen, benötigen Sie eine lokale GAL-Synchronisierungslösung, damit diese Gruppen in Ihren anderen Gesamtstrukturen angezeigt werden können.
- Das Feature „Gruppenrückschreiben“ verarbeitet derzeit keine Sicherheitsgruppen oder Verteilergruppen.

>[AZURE.NOTE]Für das Gruppenrückschreiben ist ein Azure AD Premium-Abonnement erforderlich.

## Rückschreiben von Benutzern
> [AZURE.IMPORTANT]Die Vorschaufunktion „Rückschreiben von Benutzern“ wurde im Azure AD Connect-Update vom August 2015 vorübergehend entfernt. Wenn Sie diese Funktion aktiviert haben, sollten Sie sie deaktivieren.

„Rückschreiben von Benutzern“ befindet sich in einem frühen Vorschaustadium. Die Funktion kann nur verwendet werden, wenn Azure AD die Quelle für alle Benutzerobjekte ist und das lokale Active Directory leer ist, bevor Sie die Funktion aktivieren (Bereitstellung „auf der grünen Wiese“).

> [AZURE.WARNING]Diese Funktion sollte nur in einer Testumgebung evaluiert und nicht in einem Azure AD-Verzeichnis verwendet werden, das für die Produktion verwendet wird.

.

>[AZURE.NOTE]Für das Rückschreiben von Benutzern ist ein Azure AD Premium-Abonnement erforderlich.

## Nächste Schritte
Fahren Sie mit Ihrer [benutzerdefinierten Installation von Azure AD Connect](active-directory-aadconnect-get-started-custom.md) fort.

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0107_2016-->