<properties
   pageTitle="Voraussetzungen für Azure Active Directory Connect | Microsoft Azure"
   description="Artikelbeschreibung, die auf Angebotsseiten und für die meisten Suchergebnisse angezeigt wird."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor="curtand"/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/13/2015"
   ms.author="andkjell;billmath"/>

# Voraussetzungen für Azure Active Directory Connect (Azure AD Connect)
Dieses Thema beschreibt die Voraussetzungen und die Hardwareanforderungen für Azure AD Connect.

## Vor der Installation von Azure AD Connect
Vor der Installation von Azure AD Connect gibt es einige Dinge, die Sie benötigen.

**Azure AD**

- Ein Azure-Abonnement oder ein [Azure-Testabonnement](http://azure.microsoft.com/pricing/free-trial/) – Dies ist nur erforderlich für den Zugriff auf das Azure-Portal und nicht für die Verwendung von Azure AD Connect. Bei Verwendung von PowerShell oder Office 365 benötigen Sie für Azure AD Connect kein Azure-Abonnement. Wenn Sie über eine Office 365-Lizenz verfügen, können Sie auch das Office 365-Portal verwenden. Mit einer kostenpflichtigen Office 365-Lizenz können Sie auch über das Office 365-Portal auf das Azure-Portal zugreifen.
- Überprüfen Sie die Domäne, die Sie in Azure AD verwenden möchten. Wenn Sie beispielsweise planen, "contoso.com" für Ihre Benutzer zu verwenden, sollten Sie sicherstellen, dass diese Domäne überprüft wurde und nicht nur die Standarddomäne "contoso.onmicrosoft.com" verwendet wird.
- Ein Azure AD-Verzeichnis lässt standardmäßig 50.000 Objekte zu. Beim Überprüfen der Domäne wird die Beschränkung auf 300.000 Objekte erhöht. Wenn Sie noch mehr Objekte in Azure AD benötigen, müssen Sie eine Supportanfrage eröffnen, um den Grenzwert noch weiter erhöhen zu lassen. Wenn Sie mehr als 500.000 Objekte benötigen, benötigen Sie eine Lizenz, z. B. Office 365, Azure AD Basic, Azure AD Premium oder Enterprise Mobility Suite.

**Lokale Server und Umgebung**

- Die AD-Schemaversion und Funktionsebene der Gesamtstruktur müssen Windows Server 2003 oder höher entsprechen. Die Domänencontroller können eine beliebige Version ausführen, solange die Anforderungen an Schema und Gesamtstrukturebene erfüllt werden.
- Azure AD Connect muss unter Windows Server 2008 oder höher installiert werden. Dieser Server kann bei Verwendung der Expresseinstellungen ein Domänencontroller oder ein Mitgliedsserver sein. Wenn Sie benutzerdefinierte Einstellungen verwenden, kann der Server auch eigenständig sein und muss nicht mit einer Domäne verknüpft werden.
- Wenn Sie die Kennwortsynchronisierung verwenden möchten, muss der Server unter Windows Server 2008 R2 SP1 oder höher ausgeführt werden.
- Wenn Active Directory-Verbunddienste bereitgestellt werden, müssen die Server, auf denen die Active Directory-Verbunddienste oder der Webanwendungsproxy installiert werden, Windows Server 2012 R2 oder höher ausführen. Windows-Remoteverwaltung muss auf diesen Servern zur Remoteinstallation aktiviert werden.
- Azure AD Connect erfordert eine SQL Server-Datenbank zum Speichern von Identitätsdaten. Standardmäßig wird SQL Server 2012 Express LocalDB (eine Light-Version von SQL Server Express) installiert, und das Dienstkonto für den Dienst wird auf dem lokalen Computer erstellt. Für SQL Server Express gilt ein 10-GB-Limit, mit dem Sie etwa 100.000 Objekte verwalten können. Wenn Sie eine höhere Anzahl von Verzeichnisobjekten verwalten möchten, müssen Sie während der Installation auf eine andere Version von SQL Server verweisen. Azure AD Connect unterstützt sämtliche Versionen von Microsoft SQL Server – von SQL Server 2008 (mit SP4) bis hin zu SQL Server 2014.

**Konten**

- Ein globales Azure AD-Administratorkonto für das Azure AD-Verzeichnis, das Sie integrieren möchten.
- Ein Enterprise-Administratorkonto für Ihr lokales Active Directory, wenn Sie Expresseinstellungen verwenden oder von DirSync upgraden.
- [Konten in Active Directory](active-directory-aadconnect-accounts-permissions.md), wenn Sie den Installationspfad für benutzerdefinierte Einstellungen verwenden.

**Konnektivität**

- Wenn Sie einen ausgehenden Proxy für die Verbindung mit dem Internet verwenden, muss die folgende Einstellung in der Datei **C:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config** für den Installations-Assistenten und Azure AD-Synchronisierung hinzugefügt werden, um die Internetverbindung zu ermöglichen.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

Dieser Text muss am Ende der Datei eingegeben werden. In diesem Code stellt &lt;PROXYADRESS&gt; die tatsächliche Proxy-IP-Adresse oder den Hostnamen dar. Wenn Ihr Proxy den Zugriff auf bestimmte URLs beschränkt, müssen die unter [URLs und IP-Adressbereiche von Office 365](https://support.office.com/de-DE/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) dokumentierten URLs im Proxy geöffnet werden.

**Andere**

- Optional: Ein Testbenutzerkonto zur Überprüfung der Synchronisierung.

## Voraussetzungen an Komponenten

Azure AD Connect ist abhängig von PowerShell und .NET 4.5.1. Führen Sie abhängig von Ihrer Windows Server-Version folgende Schritte aus:


- Windows Server 2012 R2
  - PowerShell ist standardmäßig installiert, es ist keine Aktion erforderlich.
  - .NET 4.5.1 und neuere Versionen werden über Windows Update angeboten. Stellen Sie in der Systemsteuerung sicher, dass die neuesten Updates von Windows Server installiert sind.
- Windows Server 2008 R2 und Windows Server 2012
  - Die neueste Version von PowerShell steht im **Windows Management Framework 4.0** im [Microsoft Download Center](http://www.microsoft.com/downloads) zur Verfügung.
  - .NET 4.5.1 und neuere Versionen sind im [Microsoft Download Center](http://www.microsoft.com/downloads) verfügbar.
- Windows Server 2008
  - Die neueste unterstützte Version von PowerShell steht im **Windows Management Framework 3.0** im [Microsoft Download Center](http://www.microsoft.com/downloads) zur Verfügung.
 - .NET 4.5.1 und neuere Versionen sind im [Microsoft Download Center](http://www.microsoft.com/downloads) verfügbar.

## Azure AD Connect unterstützende Komponenten

Nachfolgend finden Sie eine Liste der Komponenten, die Azure AD Connect auf dem Server installiert, auf dem Azure AD Connect installiert ist. Diese Liste gilt für eine einfache Expressinstallation. Wenn Sie eine andere SQL Server-Version auf der Installationsseite für Synchronisierungsdienste verwenden möchten, wird die SQL Express LocalDB nicht lokal installiert.

- Microsoft SQL Server 2012 – Befehlszeilenprogramme
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Azure Active Directory-Modul für Windows PowerShell
- Microsoft Online Services-Anmelde-Assistent für IT-Experten
- Microsoft Visual C++ 2013 Redistributionspaket


## Hardwareanforderungen für Azure AD Connect
Die folgende Tabelle zeigt die Mindestanforderungen für den Azure AD Connect-Synchronisierungscomputer.

| Anzahl der Objekte in Active Directory | CPU | Arbeitsspeicher | Festplattengröße |
| ------------------------------------- | --- | ------ | --------------- |
| Weniger als 10.000 | 1,6 GHz | 4 GB | 70 GB |
| 10\.000 bis 50.000 | 1,6 GHz | 4 GB | 70 GB |
| 50\.000 bis 100.000 | 1,6 GHz | 16 GB | 100 GB |
| Für 100.000 oder mehr Objekte ist die Vollversion von SQL Server erforderlich| | | |
| 100\.000 bis 300.000 | 1,6 GHz | 32 GB | 300 GB |
| 300\.000 bis 600.000 | 1,6 GHz | 32 GB | 450 GB |
| Mehr als 600.000 | 1,6 GHz | 32 GB | 500 GB |

Im Folgenden sind die Mindestanforderungen für Computer mit AD FS oder Webanwendungsservern aufgeführt:

- CPU: Doppelkern mit 1,6 GHz oder mehr
- Arbeitsspeicher: 2 GB oder mehr
- Azure-VM: A2-Konfiguration oder höher


## Nächste Schritte
Weitere Informationen zum [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=Nov15_HO1-->