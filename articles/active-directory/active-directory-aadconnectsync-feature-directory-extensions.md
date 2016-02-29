<properties
   pageTitle="Azure AD Connect Sync: Verzeichniserweiterungen | Microsoft Azure"
   description="Dieses Thema beschreibt das Verzeichniserweiterungsfeature in Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/16/2016"
   ms.author="andkjell"/>

# Azure AD Connect Sync: Verzeichniserweiterungen
Verzeichniserweiterungen ermöglichen Ihnen das Erweitern des Schemas in Azure AD mit Ihren eigenen Attributen aus dem lokalen Active Directory. Dadurch können Sie Branchen-Apps erstellen, die weiterhin lokal verwaltete Attribute nutzen. Diese Attribute können über [Azure AD Graph-Verzeichniserweiterungen](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) oder über [Microsoft Graph](https://graph.microsoft.io/) genutzt werden. Sie können die verfügbaren Attribute je nach Lösung mithilfe von [Azure AD Graph-Explorer](https://graphexplorer.cloudapp.net) oder [Microsoft Graph-Explorer](https://graphexplorer2.azurewebsites.net/) anzeigen.

Derzeit können diese Attribute nicht von Office 365-Workloads genutzt werden.

Sie konfigurieren im Installations-Assistenten im Pfad der benutzerdefinierten Einstellungen, welche zusätzlichen Attribute synchronisiert werden sollen. ![Schemaerweiterungs-Assistent](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png) Bei der Installation werden als zulässige Kandidaten die folgenden Attribute angezeigt:

- Benutzer- und Gruppenobjekttypen
- Einwertige Attribute
- Zeichenfolgen, ganze Zahlen, Binärdaten

Ein Objekt kann bis zu 100 Attribute für Verzeichniserweiterungen aufweisen. Die maximale Länge beträgt 250 Zeichen. Wenn ein Attributwert länger ist, wird er durch das Synchronisierungsmodul gekürzt.

Während der Installation von Azure AD Connect wird für eine Anwendung registriert, wo diese Attribute zur Verfügung stehen. Sie finden diese Anwendung im Azure-Portal. ![Schemaerweiterungs-App](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

Diese Attribute sind jetzt über Graph verfügbar: ![Grafik](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Die Attribute haben das Präfix „extension\_{AppClientId}\_“. Die AppClientId hat den gleichen Wert für alle Attribute in Ihrem Azure AD-Verzeichnis.

## Nächste Schritte
Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](active-directory-aadconnectsync-whatis.md).

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->