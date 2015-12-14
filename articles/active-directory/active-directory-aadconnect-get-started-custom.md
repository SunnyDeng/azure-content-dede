<properties
	pageTitle="Benutzerdefinierte Installation von Azure AD Connect | Microsoft Azure"
	description="In diesem Dokument werden die Optionen für die benutzerdefinierte Installation für Azure AD Connect aufgeführt."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/16/2015"
	ms.author="billmath;andkjell"/>

# Benutzerdefinierte Installation von Azure AD Connect


Die folgende Dokumentation stellt Informationen zur Verwendung der benutzerdefinierten Installation für Azure AD Connect bereit. Sie können diese Option verwenden, wenn Sie zusätzliche Konfigurationsoptionen oder optionale Features benötigen, die von der Express-Installation nicht abgedeckt werden.

## Verwandte Dokumentation
Wenn Sie die Dokumentation zum [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md) nicht gelesen haben, finden Sie in der folgenden Tabelle Links zu verwandten Themen. Sie müssen die ersten drei, fett hervorgehobenen Themen gelesen haben, bevor Sie mit der Installation beginnen.

| Thema | |
| --------- | --------- |
| **Azure AD Connect herunterladen** | [Azure AD Connect herunterladen](http://go.microsoft.com/fwlink/?LinkId=615771) |
| **Hardware und Voraussetzungen** | [Azure AD Connect: Hardware und Voraussetzungen](active-directory-aadconnect-prerequisites.md) |
| **Für die Installation verwendete Konten** | [Azure AD Connect-Konten und -Berechtigungen](active-directory-aadconnect-accounts-permissions.md) |
| Installieren mit den Express-Einstellungen | [Expressinstallation von Azure AD Connect](active-directory-aadconnect-get-started-express.md) |
| Upgrade von DirSync | [Upgrade von Azure AD-Synchronisierungstools (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) |
| Nach der Installation | [Überprüfen der Installation und Zuweisen von Lizenzen ](active-directory-aadconnect-whats-next.md) |




## Installieren der erforderlichen Komponenten

Bei der Installation der Synchronisierungsdienste können Sie den optionalen Konfigurationsabschnitt deaktiviert lassen, sodass Azure AD Connect alles automatisch einrichtet. Dies umfasst das Einrichten einer SQL Server 2012 Express-Instanz und die Erstellung der entsprechenden Gruppen sowie das Zuweisen der Berechtigungen für die Gruppen. Wenn Sie die Standardeinstellungen ändern möchten, können Sie die unten stehende Tabelle verwenden, um sich die verfügbaren optionalen Konfigurationsoptionen anzusehen.

![Erforderliche Komponenten](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png)


Optionale Konfiguration | Beschreibung
------------- | ------------- |
SQL Server-Name | Ermöglicht Ihnen die Angabe des SQL-Servernamens und des Instanznamens. Wählen Sie diese Option aus, wenn Sie bereits über einen Datenbankserver verfügen, den Sie verwenden möchten.
Dienstkonto | Standardmäßig erstellt Azure AD Connect ein lokales Dienstkonto für die zu verwendenden Synchronisierungsdienste. Das Kennwort wird automatisch generiert und ist der Person, die Azure AD Connect installiert, nicht bekannt. Wenn Sie einen Remote-SQL Server verwenden, benötigen Sie ein Dienstkonto in der Domäne und müssen das Kennwort kennen. Geben Sie in diesen Fällen das zu verwendende Dienstkonto ein. Stellen Sie sicher, dass der die Installation ausführende Benutzer ein SA in SQL ist, damit das Dienstkonto erstellt werden kann. Siehe [Azure AD Connect-Konten und -Berechtigungen](active-directory-aadconnect-accounts-permissions.md#custom-settings-installation) |
Berechtigungen | Standardmäßig erstellt Azure AD Connect lokal auf dem Server vier Gruppen, wenn die Synchronisierungsdienste installiert sind. Diese Gruppen sind: Administratorengruppe, Operatorengruppe, Durchsuchen-Gruppe und die Gruppe "Kennwort zurücksetzen". Wenn Sie eigene Gruppen angeben möchten, können Sie das hier tun. Die Gruppen müssen sich lokal auf dem Server befinden und dürfen nicht in der Domäne sein. |


## Benutzeranmeldung
Nach dem Installieren der erforderlichen Komponenten werden Sie aufgefordert, die Methode für das einmalige Anmelden anzugeben, die die Benutzer verwenden. Die folgende Tabelle enthält eine kurze Beschreibung der verfügbaren Optionen. Eine vollständige Beschreibung der Anmeldemethoden finden Sie unter [Benutzeranmeldung](active-directory-aadconnect-user-signin.md).

![Benutzeranmeldung](./media/active-directory-aadconnect-get-started-custom/usersignin.png)



Option zum einmaligen Anmelden | Beschreibung
------------- | ------------- |
Kennwortsynchronisierung |Die Benutzer können sich bei Microsoft Cloud Services (z. B. Office 365, Dynamics CRM und Windows InTune) mit demselben Kennwort anmelden, mit dem sie sich bei ihrem lokalen Netzwerk anmelden. Das Kennwort des Benutzers wird per Kennworthash mit Azure synchronisiert, und die Authentifizierung erfolgt in der Cloud. Weitere Informationen finden Sie unter [Kennwortsynchronisierung](active-directory-aadconnectsync-implement-password-synchronization.md).
Verbund mit AD FS|Die Benutzer können sich bei Microsoft Cloud Services (z. B. Office 365, Dynamics CRM und Windows InTune) mit demselben Kennwort anmelden, mit dem sie sich bei ihrem lokalen Netzwerk anmelden. Die Benutzer werden an ihre lokale AD FS-Instanz für die Anmeldung umgeleitet, und die Authentifizierung erfolgt lokal.
Nicht konfigurieren| Keines der Features wird installiert oder konfiguriert. Wählen Sie diese Option aus, wenn Sie bereits über einen Verbundserver eines Drittanbieters verfügen oder eine andere vorhandene Lösung eingesetzt wird.



## Herstellen einer Verbindung mit Azure AD
Geben Sie im Bildschirm "Mit Azure AD verbinden" ein Konto und ein Kennwort für den globalen Administrator ein. Stellen Sie sicher, dass für dieses Konto nicht die mehrstufige Authentifizierung aktiviert ist. Andernfalls kann die Authentifizierung nicht durchgeführt werden. Dieses Konto dient ausschließlich zur Erstellung eines Dienstkontos in Azure AD und wird nach Abschluss des Assistenten nicht mehr verwendet.

![Benutzeranmeldung](./media/active-directory-aadconnect-get-started-custom/connectaad.png)


## Seiten im Abschnitt "Synchronisierung"

### Verzeichnisse verbinden
Zum Verbinden mit Ihrem Active Directory-Domänendienst benötigt Connect von Azure AD die Anmeldeinformationen für ein Konto, das über ausreichende Berechtigungen verfügt. Dieses Konto kann ein normales Benutzerkonto sein, da nur die standardmäßigen Leseberechtigungen benötigt werden. Abhängig von Ihrem Szenario benötigen Sie jedoch möglicherweise zusätzliche Berechtigungen. Weitere Informationen finden Sie unter [Azure AD Connect-Konten und -Berechtigungen](active-directory-aadconnect-accounts-permissions.md#create-the-ad-ds-account).

![Benutzeranmeldung](./media/active-directory-aadconnect-get-started-custom/connectdir.png)


### Eindeutige Identifizierung der Benutzer

Mit dem Feature zum Abgleich über Gesamtstrukturen können Sie definieren, wie Benutzer Ihrer AD DS-Gesamtstrukturen in Azure AD repräsentiert werden. Ein Benutzer kann entweder nur einmal in allen Gesamtstrukturen vorhanden sein oder über eine Kombination aus aktivierten und deaktivierten Konten verfügen.

![Benutzeranmeldung](./media/active-directory-aadconnect-get-started-custom/unique.png)


Einstellung | Beschreibung
------------- | ------------- |
[Ihre Benutzer sind nur einmal in allen Gesamtstrukturen vorhanden.](active-directory-aadconnect-topologies.md#multiple-forests-separate-topologies) | Alle Benutzer werden als einzelne Objekte in Azure AD erstellt.<br> Die Objekte werden nicht im Metaverse verknüpft.
[E-Mail-Attribut](active-directory-aadconnect-topologies.md#multiple-forests-full-mesh-with-optional-galsync) | Diese Option verknüpft Benutzer und Kontakte, wenn dieses Attribut in verschiedenen Gesamtstrukturen denselben Wert aufweist. Es wird empfohlen, diese Option zu verwenden, wenn Ihre Kontakte mit GALSync erstellt wurden.
[ObjectSID und msExchangeMasterAccountSID](active-directory-aadconnect-topologies.md#multiple-forests-account-resource-forest)|Mit dieser Option wird ein aktivierter Benutzer in einer Kontogesamtstruktur mit einem deaktivierten Benutzer in einer Exchange-Ressourcengesamtstruktur verknüpft. Dies wird in Exchange als "verknüpftes Postfach" bezeichnet. Diese Option kann auch verwendet werden, wenn Sie nur Lync verwenden und Exchange in der Ressourcengesamtstruktur nicht vorhanden ist.
sAMAccountName und MailNickName|Mit dieser Option werden Attribute mit der Stelle verknüpft, an der erwartungsgemäß die Anmelde-ID für den Benutzer gefunden wird.
Ihr eigenes Attribut|Mit dieser Option können Sie Ihr eigenes Attribut auswählen. **Einschränkung:** Wählen Sie unbedingt ein Attribut aus, das bereits im Metaverse vorhanden ist. Wenn Sie ein benutzerdefiniertes Attribut auswählen, kann der Assistent nicht abgeschlossen werden.

- **Quellanker** – Das Attribut "sourceAnchor" ist ein Attribut, das während der Lebensdauer eines Benutzerobjekts unveränderlich ist. Das Attribut ist der Primärschlüssel, der den lokalen Benutzer mit dem Benutzer in Azure AD verknüpft. Da das Attribut nicht geändert werden kann, müssen Sie sorgfältig planen, welches Attribut Sie verwenden möchten. Hier empfiehlt sich "objectGUID". Dieses Attribut wird nicht geändert, es sei denn, das Benutzerkonto wird zwischen Gesamtstrukturen/Domänen verschoben. In einer Umgebung mit mehreren Gesamtstrukturen, in der Sie Konten zwischen Gesamtstrukturen verschieben, muss ein anderes Attribut verwendet werden, z. B. ein Attribut mit der Mitarbeiter-ID. Sie sollten Attribute vermeiden, die sich ändern, wenn eine Person heiratet oder den Aufgabenbereich wechselt. Sie können keine Attribute mit einem @-Zeichen verwenden, daher sind E-Mail-Adressen und Benutzerprinzipalnamen ungeeignet. Bei dem Attribut wird die Groß-/Kleinschreibung beachtet. Wenn Sie also ein Objekts zwischen Gesamtstrukturen verschieben, müssen Sie darauf achten, die Groß-/Kleinschreibung beizubehalten. Bei binären Attributen ist der Wert base64-codiert. Für andere Attributtypen bleibt der Wert uncodiert. In Verbundszenarien und bei einigen Azure AD-Schnittstellen ist dieses Attribut auch als "immutableID" bekannt. Weitere Informationen über den Quellanker finden Sie unter [Entwurfskonzepte](active-directory-aadconnect-design-concepts.md#sourceAnchor).

- **UserPrincipalName** – Das Attribut "userPrincipalName" ist das Attribut, das Benutzer verwenden, wenn sie sich bei Azure AD und Office 365 anmelden. Die verwendeten Domänen, auch als UPN-Suffix bezeichnet, sollte in Azure AD überprüft werden, bevor die Benutzer synchronisiert werden. Es wird dringend empfohlen, das Standardattribut "userPrincipalName" beizubehalten. Wenn dieses Attribut nicht routingfähig ist und nicht überprüft werden kann, können Sie ein anderes Attribut als das Attribut mit der Anmelde-ID auswählen, beispielsweise "email". Dies wird als **alternative ID** bezeichnet. Der Attributwert der alternativen ID muss dem RFC822-Standard entsprechen. Eine alternative ID kann sowohl mit Kennwort-SSO als auch mit Verbund-SSO (Single Sign-On, einmalige Anmeldung) als Anmeldungslösung verwendet werden.

>[AZURE.WARNING]Eine alternative ID ist nicht mit allen Office 365-Workloads kompatibel. Weitere Informationen finden Sie unter [Konfigurieren der alternativen Anmelde-ID](https://technet.microsoft.com/library/dn659436.aspx).



### Synchronisierungsfilterung anhand von Gruppen
Mithilfe des Features zur Filterung anhand von Gruppen können Sie ein kleines Pilotprojekt ausführen, für das nur eine kleine Teilmenge von Objekten in Azure AD und Office 365 erstellt werden sollte. Um dieses Feature verwenden zu können, erstellen Sie eine Gruppe in Active Directory und fügen die Benutzer und Gruppen hinzu, die mit Azure AD als direkte Mitglieder synchronisiert werden sollen. Später können Sie Benutzer hinzufügen und entfernen, um die Liste der Objekte zu verwalten, die in Azure AD vorhanden sein sollten. Zur Verwendung dieses Features wird bei der benutzerdefinierten Installation diese Seite angezeigt:

![Synchronisierungsfilterung](./media/active-directory-aadconnect-get-started-custom/filter2.png)

>[AZURE.WARNING]Diese Funktion dient nur dazu, eine Pilotbereitstellung zu unterstützen, und sollte nicht in einer umfassend genutzten Produktionsbereitstellung verwendet werden.

### Optionale Features

Über diesen Bildschirm können Sie die optionalen Features für Ihre spezifischen Szenarios auswählen. Die einzelnen Features werden im Folgenden erläutert.

![Optionale Features](./media/active-directory-aadconnect-get-started-custom/optional.png)

> [AZURE.WARNING]Wenn derzeit DirSync oder Azure AD Sync aktiv sind, aktivieren Sie keine der Features zum Rückschreiben in Azure AD Connect.

Optionale Features | Beschreibung
-------------------    | ------------- |
Exchange-Hybridbereitstellung |Die Exchange-Hybridbereitstellungsfunktion ermöglicht die Koexistenz von Exchange-Postfächern sowohl lokal als auch in Azure durch die Synchronisierung eines bestimmten [Attributsatzes](active-directory-aadconnectsync-attributes-synchronzied.md#exchange-hybrid-writeback) von Azure AD zurück in Ihr lokales Verzeichnis.
Azure AD-App- und Attributfilterung|Durch Aktivieren von Azure AD-App- und Attributfilterung kann die Sammlung der synchronisierten Attribute auf einer nachfolgenden Seite des Assistenten auf eine bestimmte Menge festgelegt werden. Zwei zusätzliche Konfigurationsseiten werden daraufhin im Assistenten geöffnet.  
Kennwortsynchronisierung | Bei Auswahl von Verbund als Anmeldungslösung können Sie diese Option aktivieren. Die Synchronisierung von Kennwörtern kann dann als Sicherungsoption verwendet werden. Weitere Informationen finden Sie unter [Kennwortsynchronisierung](active-directory-aadconnectsync-implement-password-synchronization.md).
Rückschreiben von Kennwörtern|Durch Aktivieren des Rückschreibens von Kennwörtern werden Kennwortänderungen aus Azure AD in Ihr lokales Verzeichnis zurückgeschrieben. Weitere Informationen finden Sie unter [Erste Schritte mit der Kennwortverwaltung](active-directory-passwords-getting-started.md).
Gruppenrückschreiben |Bei der Verwendung des Features **Gruppen in Office 365** können Sie diese Gruppen in Ihrem lokalen Active Directory als Verteilergruppe nutzen. Diese Option ist nur verfügbar, wenn Exchange in Ihrem lokalen Active Directory vorhanden ist. Weitere Informationen finden Sie unter [Gruppenrückschreiben](active-directory-aadconnect-feature-preview.md#group-writeback).
Geräterückschreiben | Ermöglicht für bedingte Szenarios das Rückschreiben von Geräteobjekten in Azure AD auf Ihr lokales Active Directory. Weitere Informationen finden Sie unter [Aktivieren des Geräterückschreibens in Azure AD Connect](active-directory-aadconnect-get-started-custom-device-writeback.md).
Verzeichniserweiterungen-Attributsynchronisierung|Durch Aktivieren der Verzeichniserweiterungen-Attributsynchronisierung werden zusätzliche angegebene Attribute mit Azure AD synchronisiert. Weitere Informationen finden Sie unter [Verzeichniserweiterungen](active-directory-aadconnect-feature-preview.md#directory-extensions).

### Azure AD-App- und Attributfilterung
Wenn Sie einschränken möchten, welche Attribute mit Azure AD synchronisiert werden, wählen Sie zunächst die verwendeten Dienste aus. Wenn Sie diese Seite konfigurieren, muss jeder neue Dienst explizit ausgewählt werden, indem Sie den Installations-Assistenten erneut ausführen.

![Optionale Features](./media/active-directory-aadconnect-get-started-custom/azureadapps2.png)

Auf Grundlage der im vorherigen Schritt ausgewählten Dienste werden auf dieser Seite alle Attribute angezeigt, die synchronisiert werden. Diese Liste ist eine Kombination aller Objekttypen, die synchronisiert werden. Wenn einige bestimmte Attribute nicht synchronisiert werden sollen, können Sie deren Auswahl aufheben. In der Abbildung unten wurde das Attribut "homePhone" deaktiviert, und es wird nicht mit dem Azure AD synchronisiert.

![Optionale Features](./media/active-directory-aadconnect-get-started-custom/azureadattributes2.png)

### Verzeichniserweiterungen-Attributsynchronisierung (Vorschau)
Mit Verzeichniserweiterungen können Sie das Schema in Azure AD durch von Ihrer Organisation hinzugefügte benutzerdefinierte Attribute oder durch andere Attribute in Active Directory erweitern. Wählen Sie auf der Seite "Optionale Features" die Option "Verzeichniserweiterungen-Attributsynchronisierung" aus, um dieses Feature zu verwenden. Daraufhin wird Ihnen diese Seite angezeigt, auf der Sie zusätzliche Attribute auswählen können.

![Synchronisierungsfilterung](./media/active-directory-aadconnect-get-started-custom/extension2.png)

Weitere Informationen finden Sie unter [Verzeichniserweiterungen](active-directory-aadconnect-feature-preview.md#directory-extensions).

## Konfigurieren des Verbunds mit AD FS
Das Konfigurieren von AD FS mit Azure AD Connect ist mit nur wenigen Mausklicks erledigt. Für die Einrichtung ist Folgendes erforderlich:

- Ein Windows Server 2012 R2-Server für den Verbundserver mit aktivierter Remoteverwaltung
- Ein Windows Server 2012 R2-Server für den Webanwendungsproxy-Server mit aktivierter Remoteverwaltung
- Ein SSL-Zertifikat für den Verbunddienstnamen, den Sie verwenden möchten (z. B. "adfs.contoso.com")

### Erstellen einer neuen AD FS-Farm oder Verwenden einer vorhandenen AD FS-Farm
Sie können eine vorhandene AD FS-Farm verwenden oder eine neue AD FS-Farm erstellen. Wenn Sie eine neue Farm erstellen, müssen Sie ein SSL-Zertifikat bereitstellen. Wenn das SSL-Zertifikat kennwortgeschützt ist, werden Sie aufgefordert, das Kennwort einzugeben.

![AD FS-Farm](./media/active-directory-aadconnect-get-started-custom/adfs1.png)

**Hinweis:** Wenn Sie eine vorhandene AD FS-Farm verwenden möchten, überspringen Sie einige Seiten und werden direkt zu einem Bildschirm zum Konfigurieren der Vertrauensstellung zwischen AD FS und Azure AD geleitet.

### Angeben der AD FS-Server

An dieser Stelle geben Sie die spezifischen Server ein, auf denen Sie AD FS installieren möchten. Sie können je nach Ihren Kapazitätsplanungsanforderungen einen oder mehrere Server hinzufügen. Diese Server müssen vor diese Konfiguration mit einer Active Directory-Domäne verknüpft werden. Es empfiehlt sich, einen einzelnen AD FS-Server für Test- und Pilotumgebungen zu installieren und zusätzliche Server bereitzustellen, indem Sie Azure AD Connect öffnen und AD FS auf zusätzlichen Servern bereitstellen, um Ihre Skalierungsanforderungen zu erfüllen.


> [AZURE.NOTE]Stellen Sie vor dem Konfigurieren sicher, dass alle Server mit einer AD-Domäne verknüpft sind.

![AD FS-Server](./media/active-directory-aadconnect-get-started-custom/adfs2.png)



### Angeben von Webanwendungsproxy-Servern
An dieser Stelle geben Sie die spezifischen Server ein, die Sie als Webanwendungsproxy-Server verwenden möchten. Der Webanwendungsproxy-Server wird in Ihrer DMZ bereitgestellt (Extranetzugriff) und unterstützt Authentifizierungsanforderungen aus dem Extranet. Sie können je nach Ihren Kapazitätsplanungsanforderungen einen oder mehrere Server hinzufügen. Es empfiehlt sich, einen einzelnen Webanwendungs-Proxyserver für Test- und Pilotumgebungen zu installieren und zusätzliche Server bereitzustellen, indem Sie Azure AD Connect öffnen und den Webanwendungsproxy auf zusätzlichen Servern bereitstellen. Erfahrungsgemäß sollte eine entsprechende Anzahl an Proxyservern bereitgestellt werden, um die Authentifizierung für den Internetzugriff zu bewältigen.


> [AZURE.NOTE]- Wenn es sich bei dem Konto, das Sie für die Installation von Azure AD Connect verwenden, nicht um ein lokales Administratorkonto für die AD FS-Server handelt, werden Sie aufgefordert, die Anmeldeinformationen für ein Konto einzugeben, das über ausreichende Berechtigungen verfügt. - Stellen Sie sicher, dass eine HTTP/HTTPS-Verbindung zwischen dem Azure AD Connect-Server und dem Webanwendungsproxy-Server besteht, bevor Sie diesen Schritt durchführen. - Stellen Sie darüber hinaus sicher, dass eine HTTP/HTTPS-Verbindung zwischen dem Webanwendungsserver und dem AD FS-Server besteht, um Authentifizierungsanforderungen zu unterstützen.


![Web App](./media/active-directory-aadconnect-get-started-custom/adfs3.png)


Sie werden aufgefordert, Anmeldeinformationen einzugeben, damit der Webanwendungsserver eine sichere Verbindung mit dem AD FS-Server herstellen kann. Dabei muss es sich um die Anmeldeinformationen für einen lokalen Administrator für den AD FS-Server sein.

![Proxy](./media/active-directory-aadconnect-get-started-custom/adfs4.png)


### Angeben eines Dienstkontos für den AD FS-Dienst
Der AD FS-Dienst erfordert ein Domänendienstkonto zur Authentifizierung von Benutzern und zur Suche nach Benutzerinformationen in Active Directory. Es werden zwei Dienstkontotypen unterstützt:

- **Gruppenverwaltete Dienstkonten** – Dies ist ein Dienstkonto, das im Active Directory-Domänendienst mit Windows Server 2012 eingeführt wurde. Dieser Kontotyp ermöglicht es für Dienste wie AD FS, ein einzelnes Konto zu verwenden, ohne das Kontokennwort in regelmäßigen Abständen aktualisieren zu müssen. Verwenden Sie diese Option, wenn Sie bereits über Windows Server 2012-Domänencontroller in der Domäne verfügen, der die AD FS-Servern angehören werden.
- **Domänenbenutzerkonto** – Für diesen Kontotyp müssen Sie ein Kennwort angeben und das Kennwort regelmäßig aktualisieren. Verwenden Sie diesen Kontotyp nur, wenn Sie nicht über Windows Server 2012-Domänencontroller in der Domäne verfügen, der die AD FS-Servern angehören werden.

Azure AD Connect erstellt automatisch das gruppenverwaltete Dienstkonto, wenn Sie als Domänenadministrator angemeldet sind.

![AD FS-Dienstkonto](./media/active-directory-aadconnect-get-started-custom/adfs5.png)


### Auswählen der zu verbindenden Azure AD-Domäne
Diese Konfiguration wird verwendet, um die Verbundbeziehung zwischen AD FS und Azure AD einzurichten. Damit wird AD FS zur Ausstellung von Sicherheitstoken an Azure AD konfiguriert, und Azure AD wird so konfiguriert, dass es den Token dieser spezifischen Instanz von AD FS vertraut. Auf dieser Seite lässt sich zunächst nur eine einzige Domäne konfigurieren. Sie können jederzeit zusätzliche Domänen konfigurieren, indem Sie Azure AD Connect erneut öffnen und diese Konfiguration vornehmen.


![Azure AD-Domäne](./media/active-directory-aadconnect-get-started-custom/adfs6.png)


### Zusätzliche Aufgaben zum Abschließen der Verbundkonfiguration
Sie müssen die folgenden zusätzlichen Aufgaben durchführen, um die Verbundkonfiguration abzuschließen.

- Richten Sie DNS-Einträge für den AD FS-Verbunddienstnamen (z. B. "adfs.contoso.com") für das Intranet (Ihr interner DNS-Server) sowie für das Extranet (öffentlicher DNS durch Ihre Domänenregistrierung) ein. Stellen Sie sicher, dass Sie für den Intranet-DNS-Eintrag A-Einträge verwenden und keine CNAME-Datensätze. Dies ist notwendig, damit die Windows-Authentifizierung ordnungsgemäß von dem mit der Domäne verknüpften Computer funktioniert.
- Wenn Sie mehr als einen AD FS-Server oder Webanwendungsproxy-Server bereitstellen, vergewissern Sie sich, dass Sie Ihren Load Balancer konfiguriert haben und dass die DNS-Einträge für den AD FS-Verbunddienstnamen (z. B. "adfs.contoso.com") auf den Load Balancer verweisen.
- Damit die integrierte Windows-Authentifizierung für Browser-Anwendungen mithilfe von Internet Explorer in Ihrem Intranet genutzt werden kann, müssen Sie sicherstellen, dass der AD FS-Verbunddienstname (z. B. "adfs.contoso.com") der Intranetzone in Internet Explorer hinzugefügt wurde. Diese kann über die Gruppenrichtlinie gesteuert und für alle Ihre mit der Domäne verknüpften Computer bereitgestellt werden.


### Optionale Konfiguration für den AD FS-Dienst
Sie können die Abbildung und das Logo für Ihre AD FS-Anmeldeseiten anpassen, indem Sie sich bei den AD FS anmelden und PSH verwenden, um diese Konfiguration vorzunehmen.

	Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.png"} –Illustration @{path=”c:\Contoso\illustration.png”}

## Konfigurieren und Überprüfen von Seiten
Auf dieser Seite wird die tatsächliche Konfiguration durchgeführt.

### Stagingmodus
Mit dem Stagingmodus ist es möglich, einen neuen Synchronisierungsservers parallel zu einem vorhandenen Server einzurichten. Es wird nur ein mit einem Verzeichnis in der Cloud verbundener Synchronisierungsserver unterstützt. Wenn Sie jedoch eine Verschiebung von einem anderen Server durchführen möchten, z. B. einem Server, auf dem DirSync ausgeführt wird, kann Azure AD Connect im Stagingmodus aktiviert werden. Bei Aktivierung importiert und synchronisiert das Synchronisierungsmodul Daten wie gewohnt. Allerdings werden keine Daten nach Azure AD exportiert, und die Kennwortsynchronisierung und das Kennwortrückschreiben werden deaktiviert.

![Synchronisierungsfilterung](./media/active-directory-aadconnect-get-started-custom/stagingmode.png)


Im Stagingmodus können Sie die erforderlichen Änderungen am Synchronisierungsmodul vornehmen und überprüfen, was exportiert werden soll. Wenn die Konfiguration Ihren Vorstellungen entspricht, führen Sie erneut den Installations-Assistenten aus, und deaktivieren Sie den Stagingmodus. Die Daten können dann nach Azure AD exportiert werden. Stellen Sie sicher, das der andere Server währenddessen deaktiviert ist, sodass nur ein Server aktiv exportieren kann.

 Weitere Informationen finden Sie unter [Stagingmodus](active-directory-aadconnectsync-operations.md#staging-mode).

### Überprüfen der Verbundkonfiguration

Azure AD Connect prüft die DNS-Einstellungen, wenn Sie auf die Schaltfläche "Überprüfen" klicken.

![Abgeschlossen](./media/active-directory-aadconnect-get-started-custom/adfs7.png)


Führen Sie darüber hinaus die folgenden Überprüfungsschritte aus:

- Überprüfen Sie die Browser-Anmeldung von einem mit der Domäne verknüpften Computer über Internet Explorer aus dem Intranet: Stellen Sie eine Verbindung mit https://myapps.microsoft.com her, und überprüfen Sie die Anmeldung, währen Sie mit Ihrem Konto angemeldet sind.
- Überprüfen Sie die Browser-Anmeldung von einem beliebigen Gerät aus dem Extranet: Stellen Sie mit Ihrem Computer daheim oder mit einem mobilen Gerät eine Verbindung mit https://myapps.microsoft.com her, und geben Sie Ihre Anmelde-ID und Ihr Kennwort ein.
- Überprüfen Sie die Rich-Client-Anmeldung: Stellen Sie eine Verbindung mit https://testconnectivity.microsoft.com her, wechseln Sie zur Registerkarte "Office 365", und wählen Sie die "Office 365-Test für einmaliges Anmelden" aus.


## Nächste Schritte
Melden Sie sich nach Abschluss der Installation von Windows ab und erneut wieder an, ehe Sie den Synchronisierungsdienst-Manager oder Synchronisierungsregel-Editor verwenden.

Nachdem Sie Azure AD Connect installiert haben, können Sie [die Installation überprüfen und Lizenzen zuweisen](active-directory-aadconnect-whats-next.md).

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_1203_2015-->