<properties 
	pageTitle="Benutzerdefinierte Installation von Azure AD Connect" 
	description="In diesem Dokument werden die Optionen für die benutzerdefinierte Installation für Azure AD Connect aufgeführt." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory"  
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Benutzerdefinierte Installation von Azure AD Connect


Die folgende Dokumentation stellt Informationen zur Verwendung der benutzerdefinierten Installation für Azure AD Connect bereit. Sie können diese Option verwenden, wenn Sie zusätzliche Konfigurationsoptionen oder optionale Features benötigen, die von der Express-Installation nicht abgedeckt werden.

Weitere Informationen zur Express-Installation finden Sie unter [Express-Installation](active-directory-aadconnect-get-started/#express-installation-of-azure-ad-connect). Informationen zum Upgrade von DirSync auf Azure AD Connect finden Sie unter [Upgrade von DirSync auf Azure Active Directory Connect](active-directory-aadconnect-dirsync-upgrade-get-started.md).



## Installieren der erforderlichen Komponenten

Bei der Installation der Synchronisierungsdienste können Sie den optionalen Konfigurationsabschnitt deaktiviert lassen, sodass Azure AD Connect alles automatisch einrichtet. Dies umfasst das Einrichten einer SQL Server 2012 Express-Instanz und die Erstellung der entsprechenden Gruppen sowie das Zuweisen der Berechtigungen für die Gruppen. Wenn Sie die Standardeinstellungen ändern möchten, können Sie die unten stehende Tabelle verwenden, um sich die verfügbaren optionalen Konfigurationsoptionen anzusehen.

<center>![Erforderliche Komponenten](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png) </center>

Optionale Konfiguration | Beschreibung 
------------- | ------------- |
SQL Server-Name |Ermöglicht Ihnen die Angabe des SQL-Servernamens und des Instanznamens. Wählen Sie diese Option aus, wenn Sie bereits über einen Datenbankserver verfügen, den Sie verwenden möchten.
Dienstkonto |Standardmäßig erstellt Azure AD Connect ein lokales Dienstkonto für die zu verwendenden Synchronisierungsdienste. Dabei ergibt sich das Problem, dass das Kennwort automatisch generiert und der Person unbekannt ist, die Azure AD Connect installiert. In den meisten Szenarien ist dies in Ordnung, wenn Sie jedoch erweiterte Konfigurationen erstellen möchten, wie z. B. das Festlegen des Gültigkeitsbereichs der zu synchronisierenden Organisationseinheiten, sollten Sie ein Konto erstellen und Ihr eigenes Kennwort wählen. Wenn Sie jedoch einen Remotecomputer mit SQL Server verwenden, benötigen Sie ein Dienstkonto in der Domäne und müssen das Kennwort kennen. Geben Sie in diesen Fällen das zu verwendende Dienstkonto ein. |
Berechtigungen | Standardmäßig erstellt Azure AD Connect lokal auf dem Server vier Gruppen, wenn die Synchronisierungsdienste installiert sind. Diese Gruppen sind: Administratorengruppe, Operatorengruppe, Durchsuchen-Gruppe und die Gruppe "Kennwort zurücksetzen". Wenn Sie eigene Gruppen angeben möchten, können Sie das hier tun.


## Benutzeranmeldung
Nach dem Installieren der erforderlichen Komponenten werden Sie aufgefordert, die Methode für das einmalige Anmelden anzugeben, die die Benutzer verwenden. Die folgende Tabelle enthält eine kurze Beschreibung der verfügbaren Optionen.

<center>![Benutzeranmeldung](./media/active-directory-aadconnect-get-started-custom/usersignin.png) </center>


Option zum einmaligen Anmelden | Beschreibung 
------------- | ------------- |
Kennwortsynchronisierung |Die Benutzer können sich bei Microsoft Cloud Services (z. B. Office 365, Dynamics CRM und Windows InTune) mit demselben Kennwort anmelden, mit dem sie sich bei ihrem lokalen Netzwerk anmelden. Das Kennwort der Benutzer wird über ein Kennworthash mit Azure synchronisiert, und Authentifizierung erfolgt in der Cloud.
Verbund mit AD FS|Die Benutzer können sich bei Microsoft Cloud Services (z. B. Office 365, Dynamics CRM und Windows InTune) mit demselben Kennwort anmelden, mit dem sie sich bei ihrem lokalen Netzwerk anmelden. Die Benutzer werden an ihre lokale AD FS-Instanz für die Anmeldung umgeleitet, und die Authentifizierung erfolgt lokal.
Nicht konfigurieren| Keines der Features wird installiert oder konfiguriert. Wählen Sie diese Option aus, wenn Sie bereits über einen Verbundserver eines Drittanbieters verfügen oder eine andere vorhandene Lösung eingesetzt wird.



## Herstellen einer Verbindung mit Azure AD
Geben Sie im Bildschirm "Mit Azure AD verbinden" ein Konto und ein Kennwort für den globalen Administrator ein. Stellen Sie sicher, dass für dieses Konto nicht die mehrstufige Authentifizierung aktiviert ist. Andernfalls kann die Authentifizierung nicht durchgeführt werden. Beachten Sie, dass dieses Konto ausschließlich zur Erstellung eines Dienstkontos in Azure AD dient und nach Abschluss des Assistenten nicht mehr verwendet wird.

<center>![Benutzeranmeldung](./media/active-directory-aadconnect-get-started-custom/connectaad.png) </center>

### Verzeichnisse verbinden
Zum Verbinden mit Ihrem Active Directory-Domänendienst benötigt Connect von Azure AD die Anmeldeinformationen für ein Konto, das über ausreichende Berechtigungen verfügt. Dieses Konto kann ein normales Benutzerkonto sein, da nur die standardmäßigen Leseberechtigungen benötigt werden. Abhängig von Ihrem Szenario benötigen Sie jedoch möglicherweise zusätzliche Berechtigungen. Weitere Informationen finden Sie unter [Azure AD Connect-Kontozusammenfassung](active-directory-aadconnect-account-summary.md).

<center>![Benutzeranmeldung](./media/active-directory-aadconnect-get-started-custom/connectdir.png) </center>

### Eindeutige Identifizierung der Benutzer

Mit dem Feature zum Abgleich über Gesamtstrukturen können Sie definieren, wie Benutzer Ihrer AD DS-Gesamtstrukturen in Azure AD repräsentiert werden. Ein Benutzer kann entweder nur einmal in allen Gesamtstrukturen vorhanden sein oder über eine Kombination aus aktivierten und deaktivierten Konten verfügen.

<center>![Benutzeranmeldung](./media/active-directory-aadconnect-get-started-custom/unique.png) </center>

Einstellung | Beschreibung 
------------- | ------------- |
Ihre Benutzer sind nur einmal in allen Gesamtstrukturen vorhanden.| Alle Benutzer werden als einzelne Objekte in Azure AD erstellt.<br> Die Objekte werden nicht im Metaverse verknüpft.
E-Mail-Attribut | Diese Option verknüpft Benutzer und Kontakte, wenn dieses Attribut in verschiedenen Gesamtstrukturen denselben Wert aufweist. Es wird empfohlen, diese Option zu verwenden, wenn Ihre Kontakte mit GALSync erstellt wurden.
ObjectSID und msExchangeMasterAccountSID|Mit dieser Option wird ein aktivierter Benutzer in einer Kontogesamtstruktur mit einem deaktivierten Benutzer in einer Exchange-Ressourcengesamtstruktur verknüpft. Dies wird in Exchange als "verknüpftes Postfach" bezeichnet.
sAMAccountName und MailNickName|Mit dieser Option werden Attribute mit der Stelle verknüpft, an der erwartungsgemäß die Anmelde-ID für den Benutzer gefunden wird.
Ihr eigenes Attribut|Mit dieser Option können Sie Ihr eigenes Attribut auswählen. **Einschränkung:** Wählen Sie unbedingt ein Attribut aus, das bereits im Metaverse vorhanden ist. Wenn Sie ein benutzerdefiniertes Attribut auswählen, kann der Assistent nicht abgeschlossen werden.

- **Quellanker** – Das Attribut "sourceAnchor" ist ein Attribut, das während der Lebensdauer eines Benutzerobjekts unveränderlich ist. Das Attribut ist der Primärschlüssel, der den lokalen Benutzer mit dem Benutzer in Azure AD verknüpft. Da das Attribut nicht geändert werden kann, müssen Sie sorgfältig planen, welches Attribut Sie verwenden möchten. Hier empfiehlt sich "objectGUID". Dieses Attribut wird nicht geändert, es sei denn, das Benutzerkonto wird zwischen Gesamtstrukturen/Domänen verschoben. In einer Umgebung mit mehreren Gesamtstrukturen, in der Sie Konten zwischen Gesamtstrukturen verschieben, muss ein anderes Attribut verwendet werden, z. B. ein Attribut mit der Mitarbeiter-ID. Sie sollten Attribute vermeiden, die sich ändern, wenn eine Person heiratet oder den Aufgabenbereich wechselt. Sie können keine Attribute mit einem @-Zeichen verwenden, daher sind E-Mail-Adressen und Benutzerprinzipalnamen ungeeignet. Bei dem Attribut wird die Groß-/Kleinschreibung beachtet. Wenn Sie also ein Objekts zwischen Gesamtstrukturen verschieben, müssen Sie darauf achten, die Groß-/Kleinschreibung beizubehalten. Bei binären Attributen ist der Wert base64-codiert. Für andere Attributtypen bleibt der Wert uncodiert. In Verbundszenarien und bei einigen Azure AD-Schnittstellen ist dieses Attribut auch als "immutableID" bekannt.

- **UserPrincipalName** – Das Attribut "userPrincipalName" ist das Attribut, das Benutzer verwenden, wenn sie sich bei Azure AD und Office 365 anmelden. Die verwendeten Domänen, auch als UPN-Suffix bezeichnet, sollte in Azure AD überprüft werden, bevor die Benutzer synchronisiert werden. Es wird dringend empfohlen, das Standardattribut "userPrincipalName" beizubehalten. Wenn dieses Attribut nicht routingfähig ist und nicht überprüft werden kann, können Sie ein anderes Attribut als das Attribut mit der Anmelde-ID auswählen, beispielsweise "email". Warnung: Eine alternative ID ist nicht mit allen Office 365-Workloads kompatibel. Weitere Informationen finden Sie unter https://technet.microsoft.com/de-de/library/dn659436.aspx.





### Synchronisierungsfilterung anhand von Gruppen
Mithilfe des Features zur Filterung anhand von Gruppen können Sie ein kleines Pilotprojekt ausführen, für das nur eine kleine Teilmenge von Objekten in Azure AD und Office 365 erstellt werden sollte. Um dieses Feature verwenden zu können, erstellen Sie eine Gruppe in Active Directory und fügen die Benutzer und Gruppen hinzu, die mit Azure AD als direkte Mitglieder synchronisiert werden sollen. Später können Sie Benutzer hinzufügen und entfernen, um die Liste der Objekte zu verwalten, die in Azure AD vorhanden sein sollten. Zur Verwendung dieses Features wird bei der benutzerdefinierten Installation diese Seite angezeigt:

<center>![Synchronisierungsfilterung](./media/active-directory-aadconnect-get-started-custom/filter2.png) </center>

### Optionale Features

Über diesen Bildschirm können Sie die optionalen Features für Ihre spezifischen Szenarios auswählen. Die einzelnen Features werden im Folgenden erläutert.

<center>![Express-Installation](./media/active-directory-aadconnect-get-started/of.png)</center>


Optionale Features | Beschreibung
-------------------    | ------------- | 
Exchange-Hybridbereitstellung |Die Exchange-Hybridbereitstellungsfunktion ermöglicht die Koexistenz von Exchange-Postfächern sowohl lokal und als auch in Azure durch die Synchronisierung eines bestimmten Attributsatzes von Azure AD zurück in Ihr lokales Verzeichnis.
Azure AD-App- und Attributfilterung|Durch Aktivieren von Azure AD-App- und Attributfilterung kann die Sammlung der synchronisierten Attribute auf einer nachfolgenden Seite des Assistenten auf eine bestimmte Menge festgelegt werden. Zwei zusätzliche Konfigurationsseiten werden daraufhin im Assistenten geöffnet.  
Rückschreiben von Kennwörtern|Durch Aktivieren des Rückschreibens von Kennwörtern werden Kennwortänderungen aus Azure AD in Ihr lokales Verzeichnis zurückgeschrieben.
Rückschreiben von Benutzern|Durch Aktivieren des Rückschreibens von Benutzern werden in Azure AD erstellte Benutzer in Ihr lokales Verzeichnis zurückgeschrieben. Dadurch wird eine zusätzliche Konfigurationsseite im Assistenten geöffnet.  
Verzeichniserweiterungen-Attributsynchronisierung|Durch Aktivieren der Verzeichniserweiterungen-Attributsynchronisierung werden angegebene Attribute mit Azure AD synchronisiert. Dadurch wird eine zusätzliche Konfigurationsseite im Assistenten geöffnet.  

Zusätzliche Konfigurationsoptionen, z. B. das Ändern der Standardkonfiguration, die Verwendung des Synchronisierungsregel-Editors und die deklarative Bereitstellung, finden Sie unter [Verwalten von Azure AD Connect](active-directory-aadconnect-whats-next.md)




Fügen Sie den Namen der Gruppe mit den Benutzern und Gruppen hinzu. Nur Mitglieder dieser Gruppe werden mit Azure AD synchronisiert.

## Verzeichniserweiterungen-Attributsynchronisierung
Mit Verzeichniserweiterungen können Sie das Schema in Azure AD durch von Ihrer Organisation hinzugefügte benutzerdefinierte Attribute oder durch andere Attribute in Active Directory erweitern. Wählen Sie auf der Seite "Optionale Features" die Option "Verzeichniserweiterungen-Attributsynchronisierung" aus, um dieses Feature zu verwenden. Daraufhin wird Ihnen diese Seite angezeigt, auf der Sie zusätzliche Attribute auswählen können.

<center>![Synchronisierungsfilterung](./media/active-directory-aadconnect-get-started-custom/extension2.png) </center>

Nur einwertige Attribute werden unterstützt, und der Wert darf nicht mehr als 250 Zeichen aufweisen. Das Metaverse- und Azure AD-Schema werden mit den ausgewählten Attributen erweitert. In Azure AD wird eine neue Anwendung mit den Attributen hinzugefügt.

<center>![Synchronisierungsfilterung](./media/active-directory-aadconnect-get-started-custom/extension3.png) </center>

Diese Attribute sind jetzt über Graph verfügbar:

<center>![Synchronisierungsfilterung](./media/active-directory-aadconnect-get-started-custom/extension4.png) </center>

## Benutzerrückschreiben (Vorschau)

> [AZURE.WARNING]Wenn derzeit DirSync oder Azure AD Sync aktiv sind, aktivieren Sie keine der Features zum Rückschreiben in Azure AD Connect.

Benutzerrückschreiben ermöglicht es Ihnen, einen Benutzer in Azure AD auszuwählen (über das Portal, Graph, PowerShell oder eine beliebige andere Methode) und den Benutzer wieder in den lokalen AD DS zurückzuschreiben. Um das Feature zu aktivieren, wählen Sie auf der Seite "Optionale Features" die Option "Benutzerrückschreiben" aus. Daraufhin wird Ihnen der Speicherort angezeigt, an dem diese Benutzer erstellt werden sollen. Bei der Standardkonfiguration werden alle Benutzern am selben Speicherort in AD DS erstellt.

<center>![Synchronisierungsfilterung](./media/active-directory-aadconnect-get-started-custom/writeback2.png) </center>
Die Benutzer werden mit einem zufälligen Kennwort erstellt. Daher müssen Sie das Kennwort in AD DS zurücksetzen, damit sich die Benutzer anmelden können.

>[AZURE.NOTE]Die Kennwortsynchronisierung und das Rückschreiben von Kennwörtern sind nicht kompatibel mit diesem Vorschaufeature.

## Gruppenrückschreiben (Vorschau)

> [AZURE.WARNING]Wenn derzeit DirSync oder Azure AD Sync aktiv sind, aktivieren Sie keine der Features zum Rückschreiben in Azure AD Connect.

Mit der Option zum Gruppenrückschreiben, die unter den optionalen Features aufgeführt ist, können Sie Gruppen in Office 365 in eine Gesamtstruktur zurückschreiben, wenn Exchange installiert ist. Dies ist ein neuer Gruppentyp, der immer in der Cloud verwaltet wird. Dies finden Sie unter "outlook.office365.com" oder "myapps.microsoft.com", wie hier gezeigt:


<center>![Synchronisierungsfilterung](./media/active-directory-aadconnect-get-started-custom/office365.png) </center>


<center>![Synchronisierungsfilterung](./media/active-directory-aadconnect-get-started-custom/myapps.png) </center>

Diese Gruppe wird als Verteilergruppe im lokalen AD DS dargestellt. Auf dem lokalen Exchange-Server muss das kumulative Update 8 für Exchange Server 2013 (vom März 2015) installiert sein, damit dieser neue Gruppentyp erkannt wird.

**Hinweis**

- Die Angaben für das Adressbuchattribut sind derzeit nicht angegeben. Sie können einfach nach der Adressbucheigenschaft einer anderen Gruppe in Ihrer Organisation suchen und diese außerhalb des Synchronisierungsmoduls auffüllen. **Verwenden Sie hierzu einfach das update-recipient-PowerShell-Cmdlet.**
- Nur Gesamtstrukturen mit dem Exchange-Schema sind gültige Ziele für Gruppen. Wenn kein Exchange-Schema erkannt wurde, kann das Gruppenrückschreiben nicht aktiviert werden.
- Das Feature "Gruppenrückschreiben" verarbeitet derzeit keine Sicherheitsgruppen oder Verteilergruppen.

Weitere Informationen finden Sie [hier](http://blogs.office.com/2014/09/25/delivering-first-chapter-groups-office-365/).

## Geräterückschreiben (Vorschau)

> [AZURE.WARNING]Wenn derzeit DirSync oder Azure AD Sync aktiv ist, aktivieren Sie keine der Features zum Rückschreiben in Azure AD Connect.

Mit dem Feature "Geräterückschreiben" können Sie ein in der Cloud registriertes Gerät, z. B. in Intune, in AD DS für bedingten Zugriff auswählen. Um das Feature zu aktivieren, muss AD DS vorbereitet sein. Wenn Sie AD FS und den Geräteregistrierungsdienst installieren, stellt der Geräteregistrierungsdienst PowerShell-Cmdlets bereit, um Active Directory für das Geräterückschreiben vorzubereiten. Wenn Sie keinen Geräteregistrierungsdienst installiert haben, können Sie "C:\Programme\Microsoft Azure Active Directory Connect\AdPrep\AdSyncAdPrep.psm1" als Unternehmensadministrator ausführen.

Vor dem Ausführen des PowerShell-Cmdlets müssen Sie dieses zunächst importieren.

	Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'

Hierzu müssen Active Directory und MSOnline PowerShell lokal installiert sein.



## Stagingmodus
Mit dem Stagingmodus ist es möglich, einen neuen Synchronisierungsservers parallel zu einem vorhandenen Server einzurichten. Es wird nur ein mit einem Verzeichnis in der Cloud verbundener Synchronisierungsserver unterstützt. Aber wenn Sie eine Verschiebung von einem anderen Server durchführen möchten, z. B. einem Server, auf dem DirSync ausgeführt wird, kann Azure AD Connect im Stagingmodus aktiviert werden. Bei Aktivierung importiert und synchronisiert das Synchronisierungsmodul Daten wir gewohnt. Allerdings werden keine Daten nach Azure AD exportiert, und die Kennwortsynchronisierung und das Kennwortrückschreiben werden deaktiviert.

<center>![Synchronisierungsfilterung](./media/active-directory-aadconnect-get-started-custom/stagingmode.png) </center>

Im Stagingmodus können Sie die erforderlichen Änderungen am Synchronisierungsmodul vornehmen und überprüfen, was exportiert werden soll. Wenn die Konfiguration Ihren Vorstellungen entspricht, führen Sie erneut den Installations-Assistenten aus, und deaktivieren Sie den Stagingmodus. Die Daten können dann nach Azure AD exportiert werden. Stellen Sie sicher, das der andere Server währenddessen deaktiviert ist, sodass nur ein Server aktiv exportieren kann.

### Schutz vor unbeabsichtigtem Löschen
Bei der Installation von Azure AD Connect wird das Feature zum Schutz vor unbeabsichtigtem Löschen standardmäßig aktiviert und so konfiguriert, das Exporte mit mehr als 500 Löschungen unterbunden werden. Die Angabe "500" ist ein Standardwert und kann geändert werden. Wenn dieses Feature aktiviert ist und zu viele Löschungen erfolgen sollen, wird der Export nicht fortgesetzt, und Sie erhalten Sie die folgende E-Mail-Nachricht:

<center>![Synchronisierungsfilterung](./media/active-directory-aadconnect-get-started-custom/email.png) </center>

Wenn Sie diese Nachricht unerwartet erhalten haben, untersuchen Sie die Grunde dafür, und ergreifen Sie die nötigen Maßnahmen, um das Problem zu beheben.

Um den Schutz vorübergehend zu deaktivieren und diese Löschvorgänge zuzulassen, führen Sie Folgendes aus: "Disable-ADSyncExportDeletionThreshold".

Um den Schutz wieder zu aktivieren oder die Einstellung für den standardmäßigen Schwellenwert zu ändern, führen Sie Folgendes aus: "Enable-ADSyncExportDeletionThreshold".


## Konfigurieren des Verbunds mit AD FS
Das Konfigurieren von AD FS mit Azure AD Connect ist mit nur wenigen Mausklicks erledigt. Für die Einrichtung ist Folgendes erforderlich:

- Ein Windows Server 2012 R2-Server für den Verbundserver mit aktivierter Remoteverwaltung
- Ein Windows Server 2012 R2-Server für den Webanwendungsproxy-Server mit aktivierter Remoteverwaltung
- Ein SSL-Zertifikat für den Verbunddienstnamen, den Sie verwenden möchten (z. B. "adfs.contoso.com")

### Erstellen einer neuen AD FS-Farm oder Verwenden einer vorhandenen AD FS-Farm
Sie können eine vorhandene AD FS-Farm oder eine neue AD FS-Farm erstellen. Wenn Sie eine neue Farm erstellen, müssen Sie ein SSL-Zertifikat bereitstellen. Wenn das SSL-Zertifikat kennwortgeschützt ist, werden Sie aufgefordert, das Kennwort einzugeben.

<center>![AD FS-Farm](./media/active-directory-aadconnect-get-started-custom/adfs1.png) </center>
**Hinweis:** Wenn Sie eine vorhandene AD FS-Farm verwenden möchten, überspringen Sie einige Seiten und werden direkt zu einem Bildschirm zum Konfigurieren der Vertrauensstellung zwischen AD FS und Azure AD geleitet.

### Angeben der AD FS-Server

An dieser Stelle geben Sie die spezifischen Server ein, auf denen Sie AD FS installieren möchten. Sie können je nach Ihren Kapazitätsplanungsanforderungen einen oder mehrere Server hinzufügen. Diese Server müssen vor diese Konfiguration mit einer Active Directory-Domäne verknüpft werden. Es empfiehlt sich, einen einzelnen AD FS-Server für Test- und Pilotumgebungen zu installieren und zusätzliche Server bereitzustellen, indem Sie Azure AD Connect öffnen und AD FS auf zusätzlichen Servern bereitstellen, um Ihre Skalierungsanforderungen zu erfüllen.


> [AZURE.NOTE]Stellen Sie vor dem Konfigurieren sicher, dass alle Server mit einer AD-Domäne verknüpft sind.

<center>![AD FS-Server](./media/active-directory-aadconnect-get-started-custom/adfs2.png) </center>
### Angeben von Webanwendungsproxy-Servern
An dieser Stelle geben Sie die spezifischen Server ein, die Sie als Webanwendungsproxy-Server verwenden möchten. Der Webanwendungsproxy-Server wird in Ihrer DMZ bereitgestellt (Extranetzugriff) und unterstützt Authentifizierungsanforderungen aus dem Extranet. Sie können je nach Ihren Kapazitätsplanungsanforderungen einen oder mehrere Server hinzufügen. Es empfiehlt sich, einen einzelnen Webanwendungs-Proxyserver für Test- und Pilotumgebungen zu installieren und zusätzliche Server bereitzustellen, indem Sie Azure AD Connect öffnen und den Webanwendungsproxy auf zusätzlichen Servern bereitstellen. Erfahrungsgemäß sollte eine entsprechende Anzahl an Proxyservern bereitgestellt werden, um die Authentifizierung für den Internetzugriff zu bewältigen.

> [AZURE.NOTE]<li>Wenn es sich bei dem Konto, das Sie für die Installation von Azure AD Connect verwenden, nicht um ein lokales Administratorkonto für die AD FS-Server handelt, werden Sie aufgefordert, die Anmeldeinformationen für ein Konto einzugeben, das über ausreichende Berechtigungen verfügt.</li><li>Stellen Sie sicher, dass eine HTTP/HTTPS-Verbindung zwischen dem Azure AD Connect-Server und dem Webanwendungsproxy-Server besteht, bevor Sie diesen Schritt durchführen.</li><li> Stellen Sie darüber hinaus sicher, dass eine HTTP/HTTPS-Verbindung zwischen dem Webanwendungsserver und dem AD FS-Server besteht, um Authentifizierungsanforderungen zu unterstützen.</li>


<center>![Web-App](./media/active-directory-aadconnect-get-started-custom/adfs3.png) </center>

Sie werden aufgefordert, Anmeldeinformationen einzugeben, damit der Webanwendungsserver eine sichere Verbindung mit dem AD FS-Server herstellen kann. Dabei muss es sich um die Anmeldeinformationen für einen lokalen Administrator für den AD FS-Server sein.

<center>![Proxy](./media/active-directory-aadconnect-get-started-custom/adfs4.png) </center>
### Angeben eines Dienstkontos für den AD FS-Dienst
Der AD FS-Dienst erfordert ein Domänendienstkonto zur Authentifizierung von Benutzern und zur Suche nach Benutzerinformationen in Active Directory. Es werden zwei Dienstkontotypen unterstützt:

- **Gruppenverwaltete Dienstkonten** – Dies ist ein Dienstkonto, das im Active Directory-Domänendienst mit Windows Server 2012 eingeführt wurde. Dieser Kontotyp ermöglicht es für Dienste wie AD FS, ein einzelnes Konto zu verwenden, ohne das Kontokennwort in regelmäßigen Abständen aktualisieren zu müssen. Verwenden Sie diese Option, wenn Sie bereits über Windows Server 2012-Domänencontroller in der Domäne verfügen, der die AD FS-Servern angehören werden.
- **Domänenbenutzerkonto** – Für diesen Kontotyp müssen Sie ein Kennwort angeben und das Kennwort regelmäßig aktualisieren. Verwenden Sie diesen Kontotyp nur, wenn Sie nicht über Windows Server 2012-Domänencontroller in der Domäne verfügen, der die AD FS-Servern angehören werden.

Azure AD Connect erstellt automatisch das gruppenverwaltete Dienstkonto, wenn Sie als Domänenadministrator angemeldet sind.
 
<center>![AD FS-Dienstkonto](./media/active-directory-aadconnect-get-started-custom/adfs5.png) </center>

### Auswählen der zu verbindenden Azure AD-Domäne
Diese Konfiguration wird verwendet, um die Verbundbeziehung zwischen AD FS und Azure AD einzurichten. Damit wird AD FS zur Ausstellung von Sicherheitstoken an Azure AD konfiguriert, und Azure AD wird so konfiguriert, dass es den Token dieser spezifischen Instanz von AD FS vertraut. Auf dieser Seite lässt sich zunächst nur eine einzige Domäne konfigurieren. Sie können jederzeit zusätzliche Domänen konfigurieren, indem Sie Azure AD Connect erneut öffnen und diese Konfiguration vornehmen.

 
<center>![Azure AD-Domäne](./media/active-directory-aadconnect-get-started-custom/adfs6.png) </center>
### Zusätzliche Aufgaben zum Abschließen der Verbundkonfiguration
Sie müssen die folgenden zusätzlichen Aufgaben durchführen, um die Verbundkonfiguration abzuschließen.

- Richten Sie DNS-Einträge für den AD FS-Verbunddienstnamen (z. B. "adfs.contoso.com") für das Intranet (Ihr interner DNS-Server) sowie für das Extranet (öffentlicher DNS durch Ihre Domänenregistrierung) ein. Stellen Sie sicher, dass Sie für den Intranet-DNS-Eintrag A-Einträge verwenden und keine CNAME-Datensätze. Dies ist notwendig, damit die Windows-Authentifizierung ordnungsgemäß von dem mit der Domäne verknüpften Computer funktioniert.
- Wenn Sie mehr als einen AD FS-Server oder Webanwendungsproxy-Server bereitstellen, vergewissern Sie sich, dass Sie Ihren Load Balancer konfiguriert haben und dass die DNS-Einträge für den AD FS-Verbunddienstnamen (z. B. "adfs.contoso.com") auf den Load Balancer verweisen.
- Damit die integrierte Windows-Authentifizierung für Browser-Anwendungen mithilfe von Internet Explorer in Ihrem Intranet genutzt werden kann, müssen Sie sicherstellen, dass der AD FS-Verbunddienstname (z. B. "adfs.contoso.com") der Intranetzone in Internet Explorer hinzugefügt wurde. Diese kann über die Gruppenrichtlinie gesteuert und für alle Ihre mit der Domäne verknüpften Computer bereitgestellt werden. 

### Überprüfen der Verbundkonfiguration

Azure AD Connect prüft die DNS-Einstellungen, wenn Sie auf die Schaltfläche "Überprüfen" klicken.

<center>![Abschließen](./media/active-directory-aadconnect-get-started-custom/adfs7.png) </center>
Führen Sie darüber hinaus die folgenden Überprüfungsschritte aus:

- Überprüfen Sie die Browser-Anmeldung von einem mit der Domäne verknüpften Computer über Internet Explorer aus dem Intranet: Stellen Sie eine Verbindung mit https://myapps.microsoft.com her, und überprüfen Sie die Anmeldung, währen Sie mit Ihrem Konto angemeldet sind.
- Überprüfen Sie die Browser-Anmeldung von einem beliebigen Gerät aus dem Extranet: Stellen Sie mit Ihrem Computer daheim oder mit einem mobilen Gerät eine Verbindung mit https://myapps.microsoft.com her, und geben Sie Ihre Anmelde-ID und Ihr Kennwort ein.
- Überprüfen Sie die Rich-Client-Anmeldung: Stellen Sie eine Verbindung mit https://testconnectivity.microsoft.com her, wechseln Sie zur Registerkarte "Office 365", und wählen Sie die "Office 365-Test für einmaliges Anmelden" aus.

### Optionale Konfiguration für den AD FS-Dienst
Sie können die Abbildung und das Logo für Ihre AD FS-Anmeldeseiten anpassen, indem Sie sich bei den AD FS anmelden und PSH verwenden, um diese Konfiguration vorzunehmen.
	
	Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.png"} –Illustration @{path=”c:\Contoso\illustration.png”}






**Zusätzliche Ressourcen**

* [Verwenden Sie Ihre lokale Identitätsinfrastruktur in der Cloud](active-directory-aadconnect.md)
* [Funktionsweise von Azure AD Connect](active-directory-aadconnect-how-it-works.md)
* [Wie geht es weiter mit Azure AD Connect?](active-directory-aadconnect-whats-next.md)
* [Weitere Informationen](active-directory-aadconnect-learn-more.md)
* [Azure AD Connect auf MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=July15_HO4-->