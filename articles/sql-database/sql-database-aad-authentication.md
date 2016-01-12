<properties
   pageTitle="Herstellen einer Verbindung mit SQL-Datenbank unter Verwendung der Azure Active Directory-Authentifizierung | Microsoft Azure"
   description="Erfahren Sie, wie Sie unter Verwendung der Azure Active Directory-Authentifizierung eine Verbindung mit SQL-Datenbank herstellen."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="10/08/2015"
   ms.author="rick.byham@microsoft.com"/>

# Herstellen einer Verbindung mit SQL-Datenbank unter Verwendung der Azure Active Directory-Authentifizierung 

Die Azure Active Directory-Authentifizierung ist ein Mechanismus zum Herstellen einer Verbindung mit Microsoft Azure SQL-Datenbank unter Verwendung von Identitäten in Azure Active Directory (Azure AD). Mithilfe der Azure Active Directory-Authentifizierung ist es möglich, die Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten an einer zentralen Stelle zu verwalten. Die zentrale ID-Verwaltung ermöglicht eine einheitliche Verwaltung von SQL-Datenbankbenutzern und vereinfacht die Berechtigungsverwaltung. Daraus ergeben sich u. a. die folgenden Vorteile:

- Es wird eine Alternative zur SQL Server-Authentifizierung bereitgestellt.
- Es wird einer unkontrollierten Ausbreitung von Benutzeridentitäten über Datenbankserver hinweg Einhalt geboten.
- Es wird eine Kennwortrotation über eine zentrale Stelle ermöglicht.
- Kunden können Datenbankberechtigungen mithilfe von externen Gruppen (AAD) verwalten.
- Durch das Aktivieren der integrierten Windows-Authentifizierung und andere von Azure Active Directory unterstützte Authentifizierungsformen wird das Speichern von Kennwörtern überflüssig.
- Die Azure Active Directory-Authentifizierung verwendet eigenständige Datenbankbenutzer zum Authentifizieren von Identitäten auf Datenbankebene.

> [AZURE.IMPORTANT]Die Azure Active Directory-Authentifizierung ist ein Vorschaufeature und unterliegt den Vorschaubestimmungen in Ihrer Lizenzvereinbarung (z. B. Enterprise Agreement, Microsoft Azure-Vertrag oder Microsoft Online-Abonnementvertrag) sowie allen anwendbaren Bestimmungen unter [Ergänzende Nutzungsbedingungen für Microsoft Azure-Vorschauen](http://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Die Konfigurationsschritte schließen die folgenden Verfahren zum Konfigurieren und Verwenden der Azure Active Directory-Authentifizierung ein:

1. Erstellen und Auffüllen eines Azure Active Directory-Verzeichnisses
2. Sicherstellen, dass eine Datenbank in Azure SQL-Datenbank V12 vorliegt
3. Optional: Zuordnen oder Ändern des aktiven Verzeichnisses für Ihr Azure-Abonnement
4. Erstellen eines Azure Active Directory-Administrators für Azure SQL Server
5. Konfigurieren der Clientcomputer
6. Erstellen eigenständiger Datenbankbenutzer in der Datenbank, die Azure AD-Identitäten zugeordnet sind
7. Herstellen einer Verbindung mit Ihrer Datenbank unter Verwendung von Azure AD-Identitäten


## Architektur von Vertrauensstellungen
 
Das folgende allgemeine Diagramm fasst die Lösungsarchitektur unter Verwendung der Azure AD-Authentifizierung mit Azure SQL-Datenbank zusammen. Die Pfeile zeigen die Kommunikationswege.

![Diagramm zur AAD-Authentifizierung][1]

Das folgende Diagramm zeigt die Verbund-, Vertrauensstellungs- und Hostbeziehungen, die einem Client die Verbindungsherstellung mit einer Datenbank ermöglichen, indem ein von Azure AD authentifiziertes Token übermittelt wird, dem die Datenbank vertraut. Es ist wichtig zu verstehen, dass für den Zugriff auf eine Datenbank mithilfe der Azure AD-Authentifizierung das Hostabonnement Azure Active Directory zugeordnet sein muss.

![Abonnementbeziehung][2]

## Administratorstruktur
 
Bei Verwendung der Azure AD-Authentifizierung sind zwei Administratorkonten für den SQL-Datenbankserver vorhanden: der ursprüngliche SQL Server-Administrator und der Azure AD-Administrator. Nur der auf einem Azure AD-Konto basierende Administrator kann den ersten eigenständigen Azure AD-Datenbankbenutzer in einer Benutzerdatenbank erstellen. Das Konto für die Azure AD-Administratoranmeldung kann ein Azure AD-Benutzer oder eine Azure AD-Gruppe sein. Wenn es sich bei dem Administrator um ein Gruppenkonto handelt, kann es von einem beliebigen Gruppenmitglied verwendet werden, sodass mehrere Azure AD-Administratoren die SQL Server-Instanz verwalten können. Die Verwendung eines Gruppenkontos für den Administrator ermöglicht es Ihnen, Gruppenmitglieder in Azure AD zentral hinzuzufügen und zu entfernen, ohne die Benutzer oder Berechtigungen in SQL-Datenbank zu ändern. Es kann jeweils nur ein Azure AD-Administrator (ein Benutzer oder eine Gruppe) konfiguriert werden.

![Administratorstruktur][3]

## Berechtigungen
 
Um neue Benutzer zu erstellen, müssen Sie über die Berechtigung **BELIEBIGEN BENUTZER ÄNDERN** in der Datenbank verfügen. Die Berechtigung **BELIEBIGEN BENUTZER ÄNDERN** kann jedem Datenbankbenutzer gewährt werden. Die Berechtigung **BELIEBIGEN BENUTZER ÄNDERN** haben auch Serveradministratorkonten inne, ebenso wie Datenbankbenutzer mit der Berechtigung **STEUERUNG FÜR DATENBANK** oder **DATENBANK ÄNDERN** für diese Datenbank sowie Mitglieder der Datenbankrolle **db\_owner**.

Um eigenständige Datenbankbenutzer in Azure SQL-Datenbank zu erstellen, müssen Sie unter Verwendung einer Azure AD-Identität eine Verbindung mit der Datenbank herstellen. Um den ersten eigenständigen Datenbankbenutzer zu erstellen, müssen Sie unter Verwendung eines Azure AD-Administrators (dieser ist der Besitzer der Datenbank) eine Verbindung mit der Datenbank herstellen. Dies wird nachstehend in den Schritten 4 und 5 gezeigt.

## Funktionen und Einschränkungen von Azure AD 

In Azure SQL-Datenbank können die folgenden Mitglieder von Azure Active Directory bereitgestellt werden: – Systemeigene Mitglieder: Ein Mitglied, das in Azure AD in der verwalteten Domäne oder in einer benutzerdefinierten Domäne erstellt wurde. Weitere Informationen finden Sie unter [Fügen Sie Ihren eigenen Domänennamen in Azure AD hinzu](active-directory-add-domain.md). – Mitglieder von Verbunddomänen: Ein Mitglied, das in Azure AD mit einer Verbunddomäne erstellt wurde. Weitere Informationen finden Sie unter [Microsoft Azure now supports federation with Windows Server Active Directory](http://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (in englischer Sprache). – Importierte Mitglieder aus anderen Azure Active Directory-Verzeichnissen, bei denen es sich um systemeigene Mitglieder oder um Mitglieder von Verbunddomänen handelt. – Active Directory-Gruppen, die als Sicherheitsgruppen erstellt wurden.

Microsoft-Konten (beispielsweise "outlook.com", "hotmail.com", "live.com") oder andere Gastkonten (z. B. "gmail.com", "yahoo.com") werden nicht unterstützt. Wenn Sie sich bei [https://login.live.com](https://login.live.com) mit dem Konto und einem Kennwort anmelden können, verwenden Sie ein Microsoft-Konto, das zur Azure AD-Authentifizierung für Azure SQL-Datenbank nicht eingesetzt werden kann.

### Zusätzliche Überlegungen 

- Um die Verwaltungsmöglichkeiten zu verbessern, wird empfohlen, eine dedizierte Azure Active Directory-Gruppe als Administrator bereitzustellen.
- Es kann jeweils nur ein Azure AD-Administrator (ein Benutzer oder eine Gruppe) für eine Azure SQL Server-Instanz konfiguriert werden.
- Nur ein Azure Active Directory-Administrator kann sich anfänglich unter Verwendung eines Azure Active Directory-Kontos mit der Azure SQL Server-Instanz verbinden. Der Active Directory-Administrator kann weitere Azure Active Directory-Datenbankbenutzer konfigurieren.
- Es wird empfohlen, das Verbindungstimeout auf 30 Sekunden festzulegen.
- Einige Tools wie BI und Excel werden nicht unterstützt.
- Die Azure Active Directory-Authentifizierung unterstützt ausschließlich den **.NET Framework-Datenanbieter für SqlServer** (mindestens .NET Framework 4.6). Deshalb können über Management Studio (verfügbar mit SQL Server 2016) und Datenebenenanwendungen (DAC und .bacpac) Verbindungen hergestellt werden, mit **sqlcmd.exe** ist jedoch keine Verbindung möglich, da **sqlcmd** den ODBC-Anbieter verwendet.
- Eine zweistufige Authentifizierung oder andere Formen der interaktiven Authentifizierung werden nicht unterstützt.


## 1\. Erstellen und Auffüllen eines Azure Active Directory-Verzeichnisses
 
Erstellen Sie ein Azure Active Directory-Verzeichnis, und füllen Sie es mit Benutzern und Gruppen. Dies umfasst:

- Erstellen der anfänglichen, von Azure AD verwalteten Domäne
- Konfigurieren eines Verbunds aus lokalen Active Directory-Domänendiensten und Azure Active Directory

Weitere Informationen finden Sie unter [Fügen Sie Ihren eigenen Domänennamen in Azure AD hinzu](active-directory-add-domain.md), [Microsoft Azure now supports federation with Windows Server Active Directory](http://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (in englischer Sprache), [Verwalten Ihres Azure AD-Verzeichnisses](https://msdn.microsoft.com/library/azure/hh967611.aspx) und [Verwalten von Azure AD mit Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

## 2\. Sicherstellen, dass eine Datenbank in Azure SQL-Datenbank V12 vorliegt
 
Die Azure Active Directory-Authentifizierung wird in der aktuellen Version unterstützt, SQL-Datenbank V12. Informationen zu SQL-Datenbank V12 und darüber, ob diese Version in Ihrer Region verfügbar ist, finden Sie unter [Neuerungen in SQL-Datenbank V12](sql-database-v12-whats-new.md).

Wenn Sie über eine vorhandene Datenbank verfügen, prüfen Sie, ob diese in SQL-Datenbank V12 gehostet wird, indem Sie (beispielsweise über SQL Server Management Studio) eine Verbindung mit der Datenbank herstellen und `SELECT @@VERSION;` ausführen. Die erwartete Ausgabe für eine Datenbank in SQL-Datenbank V12 ist mindestens **Microsoft SQL Azure (RTM) – 12.0**.

Wenn Ihre Datenbank nicht in SQL-Datenbank V12 gehostet wird, finden Sie weitere Informationen unter [Planen und Vorbereiten des Upgrades auf die SQL-Datenbank V12](sql-database-v12-plan-prepare-upgrade.md). Besuchen Sie dann das klassische Azure-Portal, um die Datenbank nach SQL-Datenbank V12 zu migrieren.

Alternativ können Sie eine neue Datenbank in SQL-Datenbank V12 erstellen, indem Sie die unter [Erstellen einer ersten Azure SQL-Datenbank](sql-database-get-started.md) aufgeführten Schritte ausführen. **Tipp**: Lesen Sie die Informationen im nächsten Schritt, bevor Sie ein Abonnement für Ihre neue Datenbank auswählen.

## 3\. Optional: Zuordnen oder Ändern des aktiven Verzeichnisses für Ihr Azure-Abonnement

Um Ihre Datenbank dem Azure AD-Verzeichnis für Ihre Organisation zuzuordnen, kennzeichnen Sie das Verzeichnis für das Azure-Abonnement, das die Datenbank hostet, als vertrauenswürdiges Verzeichnis. Weitere Informationen zu Azure-Abonnements finden Sie unter [Wie Azure-Abonnements mit Azure AD verknüpft sind](https://msdn.microsoft.com/library/azure/dn629581.aspx).

**Zusätzliche Informationen:** Jedes Azure-Abonnement weist eine Vertrauensstellung mit einer Azure AD-Instanz auf. Dies bedeutet, dass es diesem Verzeichnis bei der Authentifizierung von Benutzern, Diensten und Geräten vertraut. Mehrere Abonnements können dem gleichen Verzeichnis vertrauen, ein Abonnement vertraut jedoch nur einem Verzeichnis. Sie können unter [https://manage.windowsazure.com/](https://manage.windowsazure.com/) auf der Registerkarte **Einstellungen** ermitteln, welchem Verzeichnis Ihr Abonnement vertraut. Diese Vertrauensstellung, die ein Abonnement mit einem Verzeichnis aufweist, unterscheidet sich von der Beziehung, die ein Abonnement mit allen anderen Ressourcen in Azure (Websites, Datenbanken usw.) hat. Diese ähneln eher den untergeordneten Ressourcen eines Abonnements. Wenn ein Abonnement abläuft, wird der Zugriff auf die anderen Ressourcen, die dem Abonnement zugeordnet sind, ebenfalls beendet. Das Verzeichnis verbleibt jedoch in Azure, und Sie können ihm ein anderes Abonnement zuordnen und weiterhin die Benutzer des Verzeichnisses verwalten. Weitere Informationen zu Ressourcen finden Sie unter [Grundlegendes zur Zugriffssteuerung in Azure sowie zur Integration in Azure Active Directory](https://msdn.microsoft.com/library/azure/dn584083.aspx).

Die folgenden Verfahren zeigen Schrittanleitungen dazu, wie Sie das zugeordnete Verzeichnis für ein Abonnement ändern.

1. Melden Sie sich als Azure-Abonnementadministrator beim [klassischen Azure-Portal](https://manage.windowsazure.com/) an.
2. Wählen Sie auf dem Banner links die Option **EINSTELLUNGEN** aus. 
3. Ihre Abonnements werden im Bildschirm mit den Einstellungen angezeigt. Wenn das gewünschte Abonnement nicht angezeigt wird, klicken Sie oben auf **Abonnements**, klicken Sie auf die Dropdownliste **NACH VERZEICHNIS FILTERN**, und wählen Sie das Verzeichnis aus, das Ihre Abonnements enthält. Klicken Sie dann auf **ANWENDEN**.

	![Abonnement auswählen][4]
4. Klicken Sie im Bereich **Einstellungen** auf Ihr Abonnement, und klicken Sie dann im unteren Seitenbereich auf **VERZEICHNIS BEARBEITEN**.

	![Einstellungen im Portal][5]
5. Wählen Sie im Feld **VERZEICHNIS BEARBEITEN** das Azure Active Directory-Verzeichnis aus, das Ihrer SQL Server-Instanz zugeordnet ist, und klicken Sie dann auf den Pfeil zum Fortfahren.

	![Verzeichnis auswählen][6]
6. Bestätigen Sie im Dialogfeld **BESTÄTIGEN** für die Verzeichniszuordnung, die Meldung **Alle Co-Administratoren werden entfernt.**

	![Verzeichniszuordnung bestätigen][7]
7. Klicken Sie auf das Häkchen, um das Portal neu zu laden.

> [AZURE.NOTE]Wenn Sie das Verzeichnis ändern, wird der Zugriff für alle Co-Administratoren, Azure AD-Benutzer und -Gruppen und alle verzeichnisgestützten Ressourcenbenutzer entfernt. Ein Zugriff auf dieses Abonnement oder die zugehörigen Ressourcen ist durch diese Benutzer anschließend nicht mehr möglich. Nur Sie als Dienstadministrator können den Zugriff für Prinzipale basierend auf dem neuen Verzeichnis konfigurieren. Es kann längere Zeit dauern, bis diese Änderung an alle Ressourcen weitergegeben wurde. Die Änderung des Verzeichnisses führt auch zu einer Änderung des Azure AD-Administrators für SQL-Datenbank und deaktiviert den SQL-Datenbankzugriff für alle vorhandenen Azure AD-Benutzer. Der Azure AD-Administrator muss zurückgesetzt werden (siehe Beschreibung unten), und es müssen neue Azure AD-Benutzer erstellt werden.

## 4\. Erstellen eines Azure Active Directory-Administrators für Azure SQL Server
 
Jede Azure SQL Server-Instanz wird zunächst mit einem einzigen Serveradministratorkonto konfiguriert, das als Administrator für die gesamte Azure SQL Server-Instanz fungiert. Anschließend muss ein zweiter Serveradministrator erstellt werden, hierbei handelt es sich um ein Azure AD-Konto. Dieser Prinzipal wird als eigenständiger Datenbankbenutzer in der Masterdatenbank erstellt. Als Administratoren sind die Serveradministratorkonten Mitglieder der Rolle **db\_owner** in jeder Benutzerdatenbank. Der Zugriff auf alle Benutzerdatenbanken erfolgt als Benutzer **dbo**. Weitere Informationen zu den Serveradministratorkonten finden Sie unter [Verwalten von Datenbanken und Anmeldungen in Azure SQL-Datenbank](sql-database-manage-logins.md) sowie im Abschnitt **Anmeldungen und Benutzer** unter [Sicherheitsrichtlinien und Einschränkungen von Azure SQL-Datenbank](sql-database-security-guidelines.md).

> [AZURE.NOTE]Benutzer, die nicht auf einem Azure AD-Konto basieren (hierzu gehört auch das Azure SQL Server-Administratorkonto) können keine Azure AD-basierten Benutzer erstellen, da sie keine Berechtigung zum Überprüfen der vorgeschlagenen Datenbankbenutzer mit Azure AD besitzen.

### Bereitstellen eines Azure Active Directory-Administrators für Azure SQL Server unter Verwendung des klassischen Azure-Portals 

1. Klicken Sie im [klassischen Azure-Portal](https://portal.azure.com/) in der oberen rechten Ecke auf Ihre Verbindung, um eine Dropdownliste mit möglichen Active Directory-Verzeichnissen zu öffnen. Wählen Sie das richtige Active Directory-Verzeichnis als Standardeinstellung für Azure AD aus. Durch diesen Schritt wird das Abonnement mit Active Directory und Azure SQL-Datenbank verknüpft. So wird sichergestellt, dass dasselbe Abonnement sowohl für Azure AD als auch für SQL Server verwendet wird.

	![Administrator auswählen][8]
2. Wählen Sie auf dem Banner links **SQL Server**, wählen Sie Ihre SQL Server-Instanz, und klicken Sie dann oben auf dem Blatt **SQL Server** auf **Einstellungen**.

	![Einstellungen][9]
3. Klicken Sie auf dem Blatt **Einstellungen** auf **Active Directory-Administrator (Vorschau)**, und akzeptieren Sie die Vorschauklausel.
4. Klicken Sie auf dem Blatt **Active Directory-Administrator (Vorschau)** auf die Vorschaubedingungen, um diese zu prüfen. Klicken Sie anschließend auf **OK**, um die Bedingungen zu akzeptieren.
5. Klicken Sie auf dem Blatt **Active Directory-Administrator (Vorschau)** auf **Active Directory-Administrator**, und klicken Sie dann oben auf **Administrator festlegen**.
6. Suchen Sie auf dem Blatt **Administrator hinzufügen** nach einem Benutzer, wählen Sie den Benutzer oder die Gruppe aus, den bzw. die Sie als Administrator festlegen möchten, und klicken Sie dann auf **Auswählen**. (Auf dem Blatt "Active Directory-Administrator" werden alle Mitglieder und Gruppen in Ihrem Active Directory-Verzeichnis angezeigt. Benutzer oder Gruppen, die abgeblendet dargestellt werden, können nicht ausgewählt werden, da sie nicht als Azure AD-Administratoren unterstützt werden. Eine Liste der unterstützten Administratoren finden Sie unter **Features und Einschränkungen von Azure AD** weiter oben.) Die rollenbasierte Zugriffskontrolle (Role-based Access Control, RBAC) gilt nur für das Portal und wird nicht an SQL Server weitergegeben.
7. Klicken Sie oben auf dem Blatt **Active Directory-Administrator** auf **SPEICHERN**. ![Administrator auswählen][10]

	Der Vorgang zum Ändern des Administrators kann einige Minuten in Anspruch nehmen. Anschließend wird der neue Administrator im Feld **Active Directory-Administrator** angezeigt.

> [AZURE.NOTE]Beim Einrichten des Azure AD-Administrators darf der Name des neuen Administrators (Benutzer oder Gruppe) in der Masterdatenbank noch nicht als SQL Server-Authentifizierungsanmeldung vorhanden sein. Wenn er vorhanden ist, schlägt die Einrichtung des Azure AD-Administrators fehl, wobei die Erstellung rückgängig gemacht und angegeben wird, dass ein solcher Administrator (Name) bereits vorhanden ist. Da diese SQL Server-Authentifizierungsanmeldung nicht Teil von Azure AD ist, schlagen alle Versuche fehl, unter der Azure AD-Authentifizierung eine Verbindung zum Server herzustellen.

Um einen Administrator zu einem späteren Zeitpunkt zu entfernen, klicken Sie oben auf dem Blatt **Active Directory-Administrator** auf **Administrator entfernen**.

### Bereitstellen eines Azure AD-Administrators für Azure SQL Server unter Verwendung von PowerShell 



Zum Ausführen von PowerShell-Cmdlets muss Azure PowerShell installiert sein und ausgeführt werden. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

Um einen Azure AD-Administrator bereitzustellen, müssen Sie die folgenden Azure PowerShell-Befehle ausführen:

- Add-AzureRmAccount
- Select-AzureRmSubscription


Verwenden Sie folgende Cmdlets zum Bereitstellen und Verwalten des Azure AD-Administrators:

| Cmdlet-Name | Beschreibung |
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx) | Stellt einen Azure Active Directory-Administrator für Azure SQL Server bereit. (Muss aus dem aktuellen Abonnement stammen.) |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) | Entfernt einen Azure Active Directory-Administrator für Azure SQL Server. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx) | Gibt Informationen zu dem Azure Active Directory-Administrator zurück, der aktuell für Azure SQL Server konfiguriert ist. |

Verwenden Sie den PowerShell-Befehl "get-help", um weitere Informationen zu diesen Befehlen anzuzeigen. Verwenden Sie z. B. ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

Das folgende Skript stellt eine Azure AD-Administratorengruppe namens **DBA\_Group** (Objekt-ID `40b79501-b343-44ed-9ce7-da4c8cc7353f`) für den Server **demo\_server** in einer Ressourcengruppe mit dem Namen **Group-23** bereit:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" 
–ServerName "demo_server" -DisplayName "DBA_Group"
```

Der Eingabeparameter **DisplayName** akzeptiert entweder den Azure AD-Anzeigenamen oder den Benutzerprinzipalnamen (UPN). Beispiel: ``DisplayName="John Smith"`` und ``DisplayName="johns@contoso.com"``. Für Azure AD-Gruppen wird nur der Azure AD-Anzeigename unterstützt.

> [AZURE.NOTE]Der Azure PowerShell-Befehl ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` verhindert nicht, dass nicht unterstützte Benutzer als Azure AD-Administratoren bereitgestellt werden. Ein nicht unterstützter Benutzer kann bereitgestellt werden, dieser kann aber keine Verbindung mit einer Datenbank herstellen. (Eine Liste der unterstützten Administratoren finden Sie unter **Features und Einschränkungen von Azure AD** weiter oben.)

Im folgenden Beispiel wird der optionale Wert für **ObjectID** verwendet:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" 
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE]Ein Wert für **ObjectID** ist in Azure AD erforderlich, wenn **DisplayName** nicht eindeutig ist. Um die Werte für **ObjectID** und **DisplayName** abzurufen, verwenden Sie den Active Directory-Abschnitt im klassischen Azure-Portal, um die Eigenschaften für einen Benutzer oder eine Gruppe anzuzeigen.

Das folgende Beispiel gibt Informationen zum aktuellen Azure AD-Administrator für Azure SQL Server zurück:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

Mit dem folgenden Beispiel wird ein Azure AD-Administrator entfernt:
```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

## 5\. Konfigurieren der Clientcomputer
 
Sie müssen auf allen Clientcomputern, von denen aus Ihre Anwendungen oder Benutzer mithilfe von Azure AD-Identitäten eine Verbindung mit Azure SQL-Datenbank herstellen, die folgende Software installieren:

- .NET Framework 4.6 oder höher, erhältlich unter [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Die Azure Active Directory-Authentifizierungsbibliothek für SQL Server (**ADALSQL.DLL**) steht in verschiedenen Sprachen (sowohl x86 als auch amd64) im Downloadcenter unter [Microsoft Active Directory-Authentifizierungsbibliothek für Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742) bereit.

### Tools

- Durch das Installieren von [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) oder [SQL Server Data Tools für Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) wird die Anforderung erfüllt, dass .NET Framework 4.6 vorhanden sein muss. 
- SSMS installiert die x86-Version von **ADALSQL.DLL**. (Zu diesem Zeitpunkt kann SSMS nicht zum erforderlichen Neustart nach der Installation auffordern. Dies sollte in einer künftigen CTP behoben werden.)
- SSDT installiert die amd64-Version von **ADALSQL.DLL**. Die Azure AD-Authentifizierung wird von SSDT nur teilweise unterstützt.
- Die neueste Visual Studio-Version unter [Visual Studio-Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) erfüllt die .NET Framework 4.6-Anforderung, die erforderliche amd64-Version von **ADALSQL.DLL** wird jedoch nicht installiert.

## 6\. Erstellen eigenständiger Datenbankbenutzer in der Datenbank, die Azure AD-Identitäten zugeordnet sind 

### Grundlegendes zu eigenständigen Datenbankbenutzern

Für die Azure Active Directory-Authentifizierung ist es erforderlich, dass Datenbankbenutzer als eigenständige Datenbankbenutzer erstellt werden. Ein eigenständiger Datenbankbenutzer basierend auf einer Azure AD-Identität ist ein Datenbankbenutzer, der über keine Anmeldung für die Masterdatenbank verfügt, und der einer Identität im Azure AD-Verzeichnis zugeordnet ist, das mit der Datenbank verknüpft ist. Bei der Azure AD-Identität kann es sich entweder um ein einzelnes Benutzerkonto oder um eine Gruppe handeln. Weitere Informationen zu eigenständigen Datenbankbenutzern finden Sie unter [Eigenständige Datenbankbenutzer – machen Sie Ihre Datenbank portabel](https://msdn.microsoft.com/library/ff929188.aspx). Datenbankbenutzer (mit der Erwartung von Administratoren) können nicht mithilfe des Portals erstellt werden und RBAC-Rollen werden nicht an SQL Server weitergegeben.

### Herstellen einer Verbindung mit der Benutzerdatenbank über SQL Server Management Studio
 
Um zu bestätigen, dass der Azure AD-Administrator ordnungsgemäß eingerichtet ist, stellen Sie mit dem Azure AD-Administratorkonto eine Verbindung mit der Datenbank **master** her. Um einen Azure AD-basierten eigenständigen Datenbankbenutzer bereitzustellen (bei dem es sich nicht um den Serveradministrator handelt, der Besitzer der Datenbank ist), stellen Sie mithilfe einer Azure AD-Identität, die Zugriff auf die Datenbank hat, eine Verbindung mit der Datenbank her.

> [AZURE.IMPORTANT] [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) bietet Unterstützung für die Azure Active Directory-Authentifizierung.

#### Herstellen einer Verbindung mithilfe der integrierten Active Directory-Authentifizierung 

Verwenden Sie diese Methode, wenn Sie von einer Verbunddomäne aus mit Ihren Azure Active Directory-Anmeldeinformationen bei Windows angemeldet sind.

1. Starten Sie Management Studio, und wählen Sie im Dialogfeld **Verbindung mit Datenbankmodul herstellen** (oder **Verbindung mit dem Server herstellen**) im Feld **Authentifizierung** die Einstellung **Integrierte Active Directory-Authentifizierung** aus. Es ist kein Kennwort erforderlich bzw. es kann kein Kennwort eingegeben werden, weil Ihre vorhandenen Anmeldeinformationen zur Verbindungsherstellung verwendet werden.
2. Klicken Sie auf die Schaltfläche **Optionen**, und geben Sie auf der Seite **Verbindungseigenschaften** im Feld **Mit Datenbank verbinden** den Namen der Benutzerdatenbank ein, mit der Sie sich verbinden möchten.

#### Herstellen einer Verbindung mithilfe der Active Directory-Kennwortauthentifizierung 

Verwenden Sie diese Methode, wenn Sie unter Verwendung der verwalteten Azure AD-Domäne und mit einem Azure AD-Prinzipalnamen eine Verbindung herstellen. Sie können diese Authentifizierungsmethode auch für ein Verbundkonto ohne Zugriff auf die Domäne verwenden, beispielsweise wenn Sie an einem Remotestandort arbeiten.

Verwenden Sie diese Methode, wenn Sie mit Ihren Windows-Anmeldeinformationen von einer Domäne aus angemeldet sind, die nicht im Verbund mit Azure konfiguriert ist, oder wenn Sie die Azure AD-Authentifizierung mit Azure AD basierend auf Anfangs- oder Clientdomäne nutzen.

1. Starten Sie Management Studio, und wählen Sie im Dialogfeld **Verbindung mit Datenbankmodul herstellen** (oder **Verbindung mit dem Server herstellen**) im Feld **Authentifizierung** die Einstellung **Active Directory-Kennwortauthentifizierung** aus.
2. Geben Sie im Feld **Benutzername** Ihren Azure Active Directory-Benutzernamen im Format **username@domain.com** ein. Es muss sich um ein Konto aus Azure Active Directory oder um ein Konto aus einer Domäne handeln, die im Verbund mit Azure Active Directory konfiguriert ist.
3. Geben Sie im Feld **Kennwort** Ihr Benutzerkennwort für das Azure Active Directory-Konto oder das Verbunddomänenkonto ein.
4. Klicken Sie auf die Schaltfläche **Optionen**, und geben Sie auf der Seite **Verbindungseigenschaften** im Feld **Mit Datenbank verbinden** den Namen der Benutzerdatenbank ein, mit der Sie sich verbinden möchten.


### Erstellen eines eigenständigen Azure AD-Datenbankbenutzers in einer Benutzerdatenbank

Um einen Azure AD-basierten eigenständigen Datenbankbenutzer zu erstellen (bei dem es sich nicht um den Serveradministrator handelt, der Besitzer der Datenbank ist), stellen Sie mit einer Azure AD-Identität, die mindestens über die Berechtigung **BELIEBIGEN BENUTZER ÄNDERN** verfügt, eine Verbindung mit der Datenbank her (siehe Beschreibung im vorherigen Verfahren). Verwenden Sie anschließend die folgende Transact-SQL-Syntax:

	CREATE USER Azure_AD_principal_name 
	FROM EXTERNAL PROVIDER;


*Azure\_AD\_principal\_name* kann der Benutzerprinzipalname eines Azure AD-Benutzers oder der Anzeigename einer Azure AD-Gruppe sein.

**Beispiele:** So erstellen Sie einen eigenständigen Datenbankbenutzer, der einen Benutzer der Azure AD-Verbunddomäne oder einen Benutzer der verwalteten Azure AD-Domäne repräsentiert:

	CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
	CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

So erstellen Sie einen eigenständigen Datenbankbenutzer, der eine Azure AD-Gruppe oder eine Gruppe der Verbunddomäne repräsentiert:

	CREATE USER [Nurses] FROM EXTERNAL PROVIDER;


Weitere Informationen zum Erstellen eigenständiger Datenbankbenutzer basierend auf Azure Active Directory-Identitäten finden Sie unter [CREATE USER (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).

Wenn Sie einen Datenbankbenutzer erstellen, erhält dieser Benutzer die Berechtigung **VERBINDEN** und kann sich als Mitglied der Rolle **ÖFFENTLICH** mit dieser Datenbank verbinden. Anfänglich stehen dem Benutzer nur die Berechtigungen zur Verfügung, die der Rolle **ÖFFENTLICH** gewährt wurden, sowie sämtliche Berechtigungen, die einer Windows-Gruppe erteilt wurden, denen der Benutzer angehört. Nachdem Sie einen Azure AD-basierten eigenständigen Datenbankbenutzer bereitgestellt haben, können Sie dem Benutzer zusätzliche Berechtigungen gewähren. Die Vorgehensweise ist hierbei mit der Berechtigungszuweisung für alle weiteren Benutzertypen identisch. Typischerweise gewähren Sie Datenbankrollen die gewünschten Berechtigungen, und weisen diesen Rollen anschließend Benutzer zu. Weitere Informationen finden Sie unter [Database Engine Permission Basics](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx) (in englischer Sprache). Weitere Informationen zu besonderen SQL-Datenbankrollen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in Azure SQL-Datenbank](sql-database-manage-logins.md). Ein Verbunddomänenbenutzer, der in eine verwaltete Domäne importiert wird, muss die Identität der verwalteten Domäne verwenden.

> [AZURE.NOTE]Azure AD-Benutzer werden in den Datenbankmetadaten mit dem Typ E (EXTERNAL\_USER) gekennzeichnet, Gruppen mit dem Typ X (EXTERNAL\_GROUPS). Weitere Informationen finden Sie unter [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx).


## 7\. Herstellen einer Verbindung mit Ihrer Datenbank unter Verwendung von Azure AD-Identitäten
 
Die Azure Active Directory-Authentifizierung unterstützt die folgenden Methoden der Verbindungsherstellung mit einer Datenbank unter Verwendung von Azure AD-Identitäten:

- Integrierte Windows-Authentifizierung
- Azure AD-Prinzipalname und Kennwort

### 7\.1. Verbindungsherstellung über die integrierte Authentifizierung (Windows)
 
Um die integrierte Windows-Authentifizierung zu verwenden, muss das Active Directory-Verzeichnis Ihrer Domäne im Verbund mit Azure Active Directory konfiguriert sein, und Ihre Clientanwendung (oder ein Dienst), der eine Verbindung mit der Datenbank herstellt, muss unter Verwendung der Domänenanmeldeinformationen eines Benutzers auf einem Computer ausgeführt werden, der der Domäne angehört.

Um mithilfe der integrierten Authentifizierung und einer Azure AD-Identität eine Verbindung mit einer Datenbank herzustellen, muss das Authentifizierungsschlüsselwort in der Verbindungszeichenfolge für die Datenbank auf "Active Directory Integrated" festgelegt sein. Im folgenden C#-Codebeispiel wird ADO .NET verwendet.

	string ConnectionString = 
	@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated;";
	SqlConnection conn = new SqlConnection(ConnectionString);
	conn.Open();

Beachten Sie, dass das Schlüsselwort ``Integrated Security=True`` in der Verbindungszeichenfolge nicht für die Verbindungsherstellung mit Azure SQL-Datenbank unterstützt wird.

### 7\.2. Herstellen einer Verbindung mit einem Azure AD-Prinzipalnamen und einem Kennwort 
Um mithilfe der integrierten Authentifizierung und einer Azure AD-Identität eine Verbindung mit einer Datenbank herzustellen, muss das Authentifizierungsschlüsselwort in der Verbindungszeichenfolge für die Datenbank auf "Active Directory Password" festgelegt sein, und die Verbindungszeichenfolge muss User ID/UID- und Password/PWD-Schlüsselwörter und -Werte enthalten. Im folgenden C#-Codebeispiel wird ADO .NET verwendet.

	string ConnectionString =
	  @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
	SqlConnection conn = new SqlConnection(ConnectionString);
	conn.Open();

Spezifische Codebeispiele für die Azure AD-Authentifizierung finden Sie im [SQL Server Security Blog](http://blogs.msdn.com/b/sqlsecurity/) (in englischer Sprache) auf MSDN.

## Weitere Informationen

[Verwalten von Datenbanken und Anmeldungen in Azure SQL-Datenbank](sql-database-manage-logins.md)

[Eigenständige Datenbankbenutzer](https://msdn.microsoft.com/library/ff929071.aspx)

[CREATE USER (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png

<!----HONumber=AcomDC_1210_2015--->