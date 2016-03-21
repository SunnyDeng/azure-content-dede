<properties
   pageTitle="Azure AD Connect-Synchronisierung: Connector Versionsveröffentlichungsverlauf | Microsoft Azure"
   description="Dieses Thema listet alle Versionen des Connectors für Forefront Identity Manager (FIM) und Microsoft Identity Manager (MIM) auf"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/08/2016"
   ms.author="andkjell"/>

# Azure AD Connect-Synchronisierung: Connector – Versionsveröffentlichungsverlauf
Die Connectors für Forefront Identity Manager (FIM) und Microsoft Identity Manager (MIM) werden regelmäßig aktualisiert.

Dieser Artikel soll Ihnen helfen, die Versionen zu verfolgen, die veröffentlicht wurden, und zu wissen, ob Sie auf die neueste Version aktualisieren müssen oder nicht.

Verwandte Links:

- [Die neuesten Connectors herunterladen](http://go.microsoft.com/fwlink/?LinkId=717495)
- [Generischer LDAP-Connector](active-directory-aadconnectsync-connector-genericldap.md) -Referenzdokumentation
- [Generischer SQL-Connector](active-directory-aadconnectsync-connector-genericsql.md) -Referenzdokumentation
- [Web Services-Connector](http://go.microsoft.com/fwlink/?LinkID=226245) -Referenzdokumentation
- [PowerShell-Connector](active-directory-aadconnectsync-connector-powershell.md) -Referenzdokumentation
- [Lotus Domino-Connector](active-directory-aadconnectsync-connector-domino.md) -Referenzdokumentation

## 1\.1.117.0
Veröffentlicht März 2016

**Neuen Connector** erste Version des [generischen SQL-Connectors](active-directory-aadconnectsync-connector-genericsql.md).

**Neue Features:**

- Generischer LDAP-Connector:
    - Zusätzliche Unterstützung für den Deltaimport mit Isode.
- Webdienst-Connector:
    - Die csEntryChangeResult- und setImportErrorCode-Aktivität wurde aktualisiert, um das Zurückgeben von Fehlern der Objektebene an das Synchronisierungsmodul zu ermöglichen.
    - Aktualisierte die „SAP6“ und „SAP6User“ Vorlagen dahingehend, das die neue Objektebenenfehler-Funktionalität verwendet wird.
- Lotus Domino-Connector:
    - Beim Exportieren benötigen Sie einen Zertifizierer pro Adressbuch. Sie können jetzt dasselbe Kennwort für alle Zertifizierer verwenden, um die Verwaltung zu vereinfachen.

**Behobene Probleme:**

- Generischer LDAP-Connector:
    - Für IBM Tivoli DS wurden einige Verweisattribute nicht richtig erkannt.
    - Für Open LDAP wurden während eines Deltaimports Leerzeichen am Anfang und Ende der Zeichenfolge abgeschnitten.
    - Fehler beim Export für Novell und NetIQ bei dem ein Objekt sowohl zwischen Organisationseinheiten/Containern verschoben, als auch gleichzeitig umbenannt wurde.
- Webdienst-Connector:
    - Wenn der Webdienst mehrere Endpunkte für dieselbe Bindung hat, hat der Connector diese Endpunkte nicht ordnungsgemäß ermittelt.
- Lotus Domino-Connector:
    - Fehler beim Export des Attributs fullName in eine „Mail-in-Datenbank“.
    - Ein Export, der einer Gruppe Elemente sowohl hinzufügte als auch aus ihr entfernte, exportierte nur die hinzugefügten Elemente.
    - Ist ein Notes-Dokument ungültig (weil für das Attribut „isValid“ FALSE festgelegt ist), so kann der Connector keine Verbindung herstellen.

## Ältere Versionen
Vor März 2016 wurden die Connectors als Support-Themen veröffentlicht.

**Generisches LDAP**

- [KB3078617](https://support.microsoft.com/kb/3078617) -1.0.0597, September 2015
- [KB3044896](https://support.microsoft.com/kb/3044896) -1.0.0549, 2015 März
- [KB3031009](https://support.microsoft.com/kb/3031009) -1.0.0534, Januar 2015
- [KB3008177](https://support.microsoft.com/kb/3008177) -1.0.0419, September 2014
- [KB2936070](https://support.microsoft.com/kb/2936070) -4.3.1082, 2014 März

**WebServices**

- [KB3008178](https://support.microsoft.com/kb/3008178) -1.0.0419, September 2014

**PowerShell**

- [KB3008179](https://support.microsoft.com/kb/3008179) -1.0.0419, September 2014

**Lotus Domino**

- [KB3096533](https://support.microsoft.com/kb/3096533) -1.0.0597, September 2015
- [KB3044895](https://support.microsoft.com/kb/3044895) -1.0.0549, 2015 März
- [KB2977286](https://support.microsoft.com/kb/2977286) -5.3.0712, August 2014
- [KB2932635](https://support.microsoft.com/kb/2932635) -5.3.1003, Februar 2014  
- [KB2899874](https://support.microsoft.com/kb/2899874) -5.3.0721, Oktober 2013
- [KB2875551](https://support.microsoft.com/kb/2875551) -5.3.0534, August 2013

## Nächste Schritte
Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](active-directory-aadconnectsync-whatis.md).

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0309_2016-->