<properties
   pageTitle="Azure AD Connect-Synchronisierung: Generischer LDAP-Connector | Microsoft Azure"
   description="Dieser Artikel beschreibt die Konfiguration des generischen LDAP-Connectors von Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="01/21/2016"
   ms.author="andkjell"/>

# Technische Referenz für den generischen LDAP-Connector

Dieser Artikel beschreibt den generischen LDAP-Connector. Der Artikel bezieht sich auf folgende Produkte:

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Mindestens Hotfix 4.1.3461.0 ([KB2870703](https://support.microsoft.com/kb/2870703)) erforderlich.

Der Connector steht für MIM2016 und FIM2010R2 im [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495) zum Download bereit.

Bei Verweisen auf IETF-RFCs wird in diesem Dokument folgendes Format verwendet: (RFC [RFC-Nummer]/[Abschnitt des RFC-Dokuments]), also beispielsweise: (RFC 4512/4.3). Weitere Informationen finden Sie unter http://tools.ietf.org/html/rfc4500. (4500 muss durch die korrekte RFC-Nummer ersetzt werden.)

## Übersicht über den generischen LDAP-Connector

Der generische LDAP-Connector ermöglicht die Integration des Synchronisierungsdiensts in einen LDAP-Server (Version 3).

Bestimmte Vorgänge und Schema-Elemente (etwa zum Ausführen eines Deltaimports) werden in den IETF-RFCs nicht behandelt. Für diese Vorgänge werden nur explizit angegebene LDAP-Verzeichnisse unterstützt.

Im Anschluss finden Sie einen allgemeinen Überblick über die von der aktuellen Connectorversion unterstützten Features:

| Funktion | Support |
| --- | --- |
| Verbundene Datenquelle | Der Connector wird für alle (RFC 4510-konformen) LDAP v3-Server unterstützt. Getestet wurde er mit Folgendem: <li>Microsoft Active Directory Lightweight Directory Services (AD LDS)</li><li>Microsoft Active Directory – Globaler Katalog (AD GC)</li><li>389 Directory Server</li><li>Apache Directory Server</li><li>IBM Tivoli DS</li><li>Isode Directory</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>Open DJ</li><li>Open DS</li><li>Open LDAP (openldap.org)</li><li>Oracle (ehemals Sun) Directory Server Enterprise Edition</li><li>RadiantOne Virtual Directory Server (VDS)</li><li>Sun One Directory Server</li>Relevante nicht unterstützte Verzeichnisse: <li>Microsoft Active Directory Domain Services (AD DS) [Verwenden Sie stattdessen den integrierten Active Directory-Connector.]</li><li>Oracle Internet Directory (OID)</li> |
| Szenarios | <li>Objektlebenszyklusverwaltung</li><li>Gruppenverwaltung</li><li>Kennwortverwaltung</li> |
| Vorgänge |Die folgenden Vorgänge werden für alle LDAP-Verzeichnisse unterstützt: <li>Vollständiger Import</li><li>Export</li>Folgende Vorgänge werden nur für angegebene Verzeichnisse unterstützt:<li>Deltaimport</li><li>Kennwort festlegen, Kennwort ändern</li> |
| Schema | <li>Das Schema wird auf der Grundlage des LDAP-Schemas erkannt (RFC3673 und RFC4512/4.2).</li><li>Unterstützt strukturelle Klassen, Erweiterungsklassen und die Objektklasse „extensibleObject“ (RFC4512/4.3).</li>

### Unterstützung von Deltaimport und Kennwortverwaltung

Unterstützte Verzeichnisse für Deltaimport und Kennwortverwaltung:

- Microsoft Active Directory Lightweight Directory Services (AD LDS)
    - Unterstützt alle Vorgänge für den Deltaimport
    - Unterstützt Kennwort festlegen
- Microsoft Active Directory – Globaler Katalog (AD GC)
    - Unterstützt alle Vorgänge für den Deltaimport
    - Unterstützt Kennwort festlegen
- 389 Directory Server
    - Unterstützt alle Vorgänge für den Deltaimport
    - Unterstützt Kennwort festlegen und Kennwort ändern
- Apache Directory Server
    - Unterstützt keine Deltaimporte, da dieses Verzeichnis über kein dauerhaftes Änderungsprotokoll verfügt
    - Unterstützt Kennwort festlegen
- IBM Tivoli DS
    - Unterstützt alle Vorgänge für den Deltaimport
    - Unterstützt Kennwort festlegen und Kennwort ändern
- Isode Directory
    - Unterstützt alle Vorgänge für den Deltaimport
    - Unterstützt Kennwort festlegen und Kennwort ändern
- Novell eDirectory und NetIQ eDirectory
    - Unterstützt das Hinzufüge-, Aktualisierungs- und Umbenennungsvorgänge für den Deltaimport
    - Unterstützt keine Löschvorgänge für den Deltaimport
    - Unterstützt Kennwort festlegen und Kennwort ändern
- Open DJ
    - Unterstützt alle Vorgänge für den Deltaimport
    - Unterstützt Kennwort festlegen und Kennwort ändern
- Open DS
    - Unterstützt alle Vorgänge für den Deltaimport
    - Unterstützt Kennwort festlegen und Kennwort ändern
- Open LDAP (openldap.org)
    - Unterstützt alle Vorgänge für den Deltaimport
    - Unterstützt Kennwort festlegen
    - Kennwortänderungen werden nicht unterstützt
- Oracle (ehemals Sun) Directory Server Enterprise Edition
    - Unterstützt alle Vorgänge für den Deltaimport
    - Unterstützt Kennwort festlegen und Kennwort ändern
- RadiantOne Virtual Directory Server (VDS)
    - Mindestens Version 7.1.1 erforderlich
    - Unterstützt alle Vorgänge für den Deltaimport
    - Unterstützt Kennwort festlegen und Kennwort ändern
-  Sun One Directory Server
    - Unterstützt alle Vorgänge für den Deltaimport
    - Unterstützt Kennwort festlegen und Kennwort ändern

### Voraussetzungen

Zur Verwendung des Connectors muss auf dem Synchronisierungsserver neben den weiter oben ggf. erwähnten Hotfixes Folgendes vorhanden sein:

- Microsoft .NET 4.5.2 Framework oder eine höhere Version

### Erkennen des LDAP-Servers

Der Connector greift zur Erkennung und Identifizierung des LDAP-Servers auf eine Reihe von Techniken zurück. Der Connector ermittelt anhand des Stamm-DSE den Anbieternamen und die Version und durchsucht das Schema nach eindeutigen Objekten und Attributen, die für bestimmte LDAP-Server typisch sind. Die gefundenen Daten werden vorab in die Konfigurationsoptionen des Connectors eingefügt.

### Berechtigungen für die verbundene Datenquelle

Zur Durchführung von Import- und Exportvorgängen für die Objekte im verbundenen Verzeichnis muss das Connectorkonto über ausreichende Berechtigungen verfügen. Der Connector benötigt Schreibberechtigungen für den Export und Leseberechtigungen für den Import. Die Berechtigungen werden in der Verwaltungsumgebung des Zielverzeichnisses konfiguriert.

### Ports und Protokolle

Der Connector verwendet die in der Konfiguration angegebene Portnummer. Diese ist standardmäßig auf 389 (LDAP) bzw. auf 636 (LDAPS) festgelegt.

Bei Verwendung von LDAPS muss SSL 3.0 oder TLS verwendet werden. SSL 2.0 wird nicht unterstützt und kann nicht aktiviert werden.

### Erforderliche Steuerelemente und Features

Damit der Connector ordnungsgemäß verwendet werden kann, müssen auf dem LDAP-Server folgende LDAP-Steuerelemente/-Features verfügbar sein:

- 1\.3.6.1.4.1.4203.1.5.3 True/False-Filter

Der True/False-Filter wird oftmals nicht als von LDAP-Verzeichnissen unterstützt gemeldet und möglicherweise auf der globalen Seite unter **Nicht gefundene erforderliche Features** angezeigt. Er dient zum Erstellen von OR-Filtern in LDAP-Abfragen (beispielsweise beim Importieren mehrerer Objekttypen). Wenn Sie mehrere Objekttypen importieren können, wird dies von Ihrem LDAP-Server unterstützt.

Wenn Sie ein Verzeichnis verwenden, bei dem ein eindeutiger Bezeichner als Anker fungiert, muss auch Folgendes verfügbar sein. (Weitere Informationen finden Sie weiter unten in diesem Artikel unter der [Konfigurieren von Ankern](#configure-anchors).)

- 1\.3.6.1.4.1.4203.1.5.1 Alle Betriebsattribute

Falls das Verzeichnis mehr Objekte enthält als in einem einzelnen Verzeichnisaufruf Platz finden, empfiehlt sich der Einsatz von Paginierung. Die Paginierung setzt eine der folgenden Optionen voraus:

**Option 1:**

- 1\.2.840.113556.1.4.319 pagedResultsControl

**Option 2:**

- 2\.16.840.1.113730.3.4.9 VLVControl
- 1\.2.840.113556.1.4.473 SortControl

Sind in der Konfiguration des Connectors beide Optionen aktiviert, wird „pagedResultsControl“ verwendet.

- 1\.2.840.113556.1.4.417 ShowDeletedControl

„ShowDeletedControl“ wird nur mit der USNChanged-Deltaimportmethode verwendet, um gelöschte Objekte anzeigen zu können.

Der Connector versucht zu erkennen, ob die Optionen auf dem Server vorhanden sind. Falls die Optionen nicht erkannt werden können, wird in den Connectoreigenschaften auf der globalen Seite eine Warnung angezeigt. Nicht alle LDAP-Server geben sämtliche unterstützte Steuerelemente/Features an, und der Connector kann unter Umständen ungeachtet dieser Warnung problemlos verwendet werden.

### Deltaimport

Der Deltaimport ist nur verfügbar, wenn ein Supportverzeichnis erkannt wurde. Momentan werden folgende Methoden verwendet:

- LDAP-Zugriffsprotokoll. Siehe „http://www.openldap.org/doc/admin24/overlays.html#Access Logging“ (http://www.openldap.org/doc/admin24/overlays.html#Access Logging)
- LDAP-Änderungsprotokoll. Siehe [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
- Zeitstempel. Für Novell/NetIQ eDirectory verwendet der Connector den letzten Datums-/Uhrzeitwert zum Abrufen erstellter und aktualisierter Objekte. Novell/NetIQ eDirectory bietet keine entsprechende Möglichkeit zum Abrufen gelöschter Objekte. Diese Option kann auch verwendet werden, wenn auf dem LDAP-Server keine andere Deltaimportmethode aktiv ist. Mit dieser Option können keine gelöschten Objekte importiert werden.
- USNChanged. Siehe [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### Nicht unterstützt

Folgende LDAP-Features werden nicht unterstützt:

- LDAP-Verweise zwischen Servern (RFC 4511/4.1.10)

## Erstellen eines neuen Connectors

Wählen Sie zum Erstellen eines generischen LDAP-Connectors im Synchronisierungsdienst die Option **Verwaltungs-Agent** und anschließend **Erstellen** aus. Wählen Sie den Connector **Generisch, LDAP (Microsoft)** aus.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### Konnektivität

Auf der Konnektivitätsseite müssen Informationen zu Host, Port und Bindung angegeben werden. Abhängig von der ausgewählten Bindung müssen in den folgenden Abschnitten ggf. zusätzliche Informationen angegeben werden.

![Konnektivität](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

- Die Timeouteinstellung für die Verbindung wird nur bei der ersten Verbindung mit dem Server verwendet, bei der auch die Schema-Erkennung stattfindet.
- Bei einer anonymen Bindung werden weder Benutzername und Kennwort noch ein Zertifikat verwendet.
- Geben Sie bei anderen Bindungen entweder Benutzername und Kennwort an, oder wählen Sie ein Zertifikat aus.
- Geben Sie bei Verwendung der Kerberos-Authentifizierung außerdem den Bereich und die Domäne des Benutzers an.

Das Textfeld für Attributaliase wird für Attribute verwendet, die im Schema mit der RFC4522-Syntax definiert sind. Diese können bei der Schema-Erkennung nicht erkannt werden, und der Connector kann sie nicht selbstständig identifizieren. Damit etwa das userCertificate-Attribut korrekt als binäres Attribut identifiziert wird, muss das Feld für Attributaliase Folgendes enthalten:

`userCertificate;binary`

Hier sehen Sie, wie das beispielsweise aussehen kann:

![Konnektivität](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

Aktivieren Sie das Kontrollkästchen **Betriebsattribute in Schema einschließen**, um auch vom Server erstellte Attribute einzuschließen. Dazu zählen etwa Attribute wie der Zeitpunkt der Objekterstellung und der letzten Aktualisierung.

Aktivieren Sie das Kontrollkästchen **Erweiterbare Attribute in Schema einschließen**, wenn erweiterbare Objekte (RFC4512/4.3) verwendet werden. Dadurch kann jedes Attribut für alle Objekte verwendet werden. Bei Verwendung dieser Option wird das Schema ziemlich groß. Daher wird empfohlen, die Option deaktiviert zu lassen, sofern das Feature nicht vom verbundenen Dienst verwendet wird.

### Globale Parameter

Die Seite mit den globalen Parametern dient zum Konfigurieren des DN für das Delta-Änderungsprotokoll sowie zusätzlicher LDAP-Features. Die Seite wird vorab mit den Informationen des LDAP-Servers aufgefüllt.

![Konnektivität](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

Die Informationen im oberen Abschnitt stammen vom Server selbst (etwa der Name des Servers). Der Connector überprüft außerdem, ob die erforderlichen Steuerelemente im Stamm-DSE vorhanden sind. Falls nicht, wird eine Warnung angezeigt. Einige LDAP-Verzeichnisse geben nicht alle Features im Stamm-DSE an. Es kann also sein, dass der Connector trotz vorhandener Warnung problemlos verwendet werden kann.

Die Kontrollkästchen für unterstützte Steuerelemente steuern das Verhalten für bestimmte Vorgänge:

- Bei aktivierter Strukturlöschung wird eine Hierarchie mit einem einzelnen LDAP-Aufruf gelöscht. Bei deaktivierter Strukturlöschung führt der Connector bei Bedarf einen rekursiven Löschvorgang durch.
- Wenn die Option für paginierte Ergebnisse aktiviert ist, führt der Connector paginierte Importe mit der in den Ausführungsschritten angegebenen Größe durch.
- „VLVControl“ und „SortControl“ sind Alternativen zu „pagedResultsControl“ und dienen zum Lesen von Daten aus dem LDAP-Verzeichnis.
- Sind alle drei Optionen (also „pagedResultsControl“, „VLVControl“ und „SortControl“) deaktiviert, importiert der Connector alle Objekte in einem einzelnen Vorgang. Dieser ist allerdings bei einem umfangreichen Verzeichnis unter Umständen nicht erfolgreich.
- „ShowDeletedControl“ wird nur verwendet, wenn die Deltaimportmethode auf „USNChanged“ festgelegt ist.

Der Änderungsprotokoll-DN ist der vom Delta-Änderungsprotokoll verwendete Namenskontext (beispielsweise **cn=changelog**). Dieser Wert muss angegeben werden, um Deltaimporte ausführen zu können.

Die folgende Liste gibt Aufschluss über die standardmäßigen Änderungsprotokoll-DNs:

| Verzeichnis | Delta-Änderungsprotokoll |
| --- | --- |
| Microsoft AD LDS und AD GC | Automatische Erkennung. USNChanged. |
| Apache Directory Server | Nicht verfügbar. |
| Directory 389 | Änderungsprotokoll. Zu verwendender Standardwert: **cn=changelog** |
| IBM Tivoli DS | Änderungsprotokoll. Zu verwendender Standardwert: **cn=changelog** |
| Isode Directory | Änderungsprotokoll. Zu verwendender Standardwert: **cn=changelog**
| Novell/NetIQ eDirectory | Nicht verfügbar. Zeitstempel. Der Connector verwendet den Datums-/Uhrzeitwert der letzten Aktualisierung, um hinzugefügte und aktualisierte Datensätze abzurufen. |
| Open DJ/DS | Änderungsprotokoll. Zu verwendender Standardwert: **cn=changelog** |
| Open LDAP | Zugriffsprotokoll. Zu verwendender Standardwert: **cn=accesslog** |
| Oracle DSEE | Änderungsprotokoll. Zu verwendender Standardwert: **cn=changelog** |
| RadiantOne VDS | Virtuelles Verzeichnis. Abhängig vom mit VDS verbundenen Verzeichnis. |
| Sun One Directory Server | Änderungsprotokoll. Zu verwendender Standardwert: **cn=changelog** |

Das Kennwortattribut ist der Name des Attributs, das der Connector bei Kennwortänderungen/-festlegungen zum Festlegen des Kennworts verwenden soll. Er ist standardmäßig auf **userPassword** festgelegt, kann jedoch bei Bedarf für ein bestimmtes LDAP-System angepasst werden.

In der Liste mit zusätzlichen Partitionen können weitere Namespaces hinzugefügt werden, die nicht automatisch erkannt wurden. Dies kann beispielsweise hilfreich sein, wenn mehrere Server einen logischen Cluster bilden und alle gleichzeitig importiert werden sollen. Active Directory kann mehrere Domänen in einer einzelnen Gesamtstruktur enthalten, wobei alle Domänen das gleiche Schema verwenden. Dies kann durch Eingabe zusätzlicher Namespaces in das Feld simuliert werden. Jeder Namespace kann Daten von verschiedenen Servern importieren und wird auf der Seite zum Konfigurieren von Partitionen und Hierarchien weiter konfiguriert. Drücken Sie STRG+EINGABETASTE, um eine neue Zeile zu erhalten.

### Konfigurieren der Bereitstellungshierarchie

Auf dieser Seite können Sie die DN-Komponente (beispielsweise die Organisationseinheit) dem bereitzustellenden Objekttyp (z. B. „organizationalUnit“) zuordnen.

![Bereitstellungshierarchie](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

Durch Konfigurieren der Bereitstellungshierarchie können Sie den Connector so konfigurieren, dass er bei Bedarf automatisch eine Struktur erstellt. Wenn also beispielsweise der Namespace „dc=contoso,dc=com“ vorhanden ist und das neue Objekt „cn=Joe, ou=Seattle, c=US, dc=contoso, dc=com“ bereitgestellt wird, kann der Connector für die USA ein neues Objekt vom Typ „Land“ und eine Organisationseinheit für Seattle erstellen, falls diese noch nicht im Verzeichnis vorhanden sind.

### Konfigurieren von Partitionen und Hierarchien

Wählen Sie auf der Seite für Partitionen und Hierarchien alle Namespaces mit Objekten aus, die Sie importieren und exportieren möchten.

![Partitionen](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

Für jeden Namespace können außerdem Konnektivitätseinstellungen konfiguriert werden. Diese haben dann Vorrang vor den Werten, die im Konnektivitätsbildschirm angegeben wurden. Wenn diese Werte unverändert (also leer) gelassen werden, werden die Informationen aus dem Konnektivitätsbildschirm verwendet.

Sie können auch auswählen, welche Container und Organisationseinheiten der Connector als Quelle für Importvorgänge bzw. als Ziel für Exportvorgänge verwenden soll.

### Konfigurieren von Ankern

Diese Seite besitzt immer einen vorkonfigurierten Wert und kann nicht geändert werden. Wenn Anbieter und Version des Servers erkannt wurden, wird unter Umständen ein unveränderliches Attribut (etwa die GUID für ein Objekt) verwendet. Falls die Informationen nicht erkannt wurden oder kein unveränderliches Attribut verfügbar ist, verwendet der Connector den DN (Distinguished Name) als Anker.

![Anker](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)

Im Anschluss finden Sie eine Liste mit LDAP-Servern und verwendetem Anker:

| Verzeichnis | Ankerattribut |
| --- | --- |
| Microsoft AD LDS und AD GC | objectGUID |
| 389 Directory Server | dn |
| Apache Directory | dn |
| IBM Tivoli DS | dn |
| Isode Directory | dn |
| Novell/NetIQ eDirectory | GUID |
| Open DJ/DS | dn |
| Open LDAP | dn |
| Oracle ODSEE | dn |
| RadiantOne VDS | dn |
| Sun One Directory Server | dn |

## Sonstige Hinweise

Dieser Abschnitt enthält Angaben zu spezifischen Aspekten des Connectors sowie andere wissenswerte Informationen.

### Deltaimport

Das Deltawasserzeichen in Open LDAP ist ein Datums-/Uhrzeitwert im UTC-Format. Daher müssen die Uhren zwischen dem FIM-Synchronisierungsdienst und Open LDAP synchronisiert werden. Andernfalls werden unter Umständen einige Einträge im Delta-Änderungsprotokoll ausgelassen.

Bei Novell eDirectory erkennt der Deltaimport keine Objektlöschungen. Aus diesem Grund muss in regelmäßigen Abständen ein vollständiger Import ausgeführt werden, um alle gelöschten Objekte zu finden.

Bei Verzeichnissen mit einem datums-/uhrzeitbasierten Delta-Änderungsprotokoll wird dringend empfohlen, in regelmäßigen Abständen einen vollständigen Import auszuführen, um Abweichungen zwischen dem LDAP-Server und dem aktuellen Inhalt des Connectorbereichs zu ermitteln.

## Problembehandlung

-	Informationen zum Aktivieren der Protokollierung für die Behandlung von Connectorproblemen finden Sie unter [Vorgehensweise: Aktivieren der ETW-Ablaufverfolgung für Connectors](http://go.microsoft.com/fwlink/?LinkId=335731).

<!---HONumber=AcomDC_0128_2016-->