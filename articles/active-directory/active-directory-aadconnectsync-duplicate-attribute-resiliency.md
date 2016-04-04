<properties 
	pageTitle="Identitätssynchronisierung und Resilienz bei doppelten Attributen | Microsoft Azure" 
	description="Neues Verhalten zur Behandlung von Objekten mit UPN- oder ProxyAddress-Konflikten bei der Verzeichnissynchronisierung mit Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/18/2016" 
	ms.author="markusvi"/>




# Identitätssynchronisierung und Resilienz bei doppelten Attributen

Die Resilienz bei doppelten Attributen ist ein neues Feature von Azure Active Directory und dient zur Beseitigung von Problemen durch Konflikte zwischen **UserPrincipalName** und **ProxyAddress**, die beim Ausführen eines Synchronisierungstools von Microsoft auftreten können. Das Feature befindet sich momentan in der Vorschauphase.

Die beiden Attribute müssen generell für alle Objekte vom Typ **User**, **Group** oder **Contact** eines bestimmten Azure Active Directory-Mandanten eindeutig sein.

> [AZURE.NOTE] Nur Benutzer können über UPNs verfügen.
 

Das durch dieses neue Feature ermöglichte Verhalten befindet sich im Cloudteil der Synchronisierungspipeline und ist damit clientunabhängig und für jedes Synchronisierungsprodukt von Microsoft relevant – einschließlich Azure AD Connect, DirSync und MIM + Connector. Diese Produkte werden hier unter dem allgemeinen Begriff „Synchronisierungsclient“ zusammengefasst.

## Aktuelles Verhalten

Beim Versuch, ein neues Objekt mit einem UPN- oder ProxyAddress-Wert bereitzustellen, der die Eindeutigkeitsanforderung nicht erfüllt, wird die Erstellung des Objekts durch Azure Active Directory blockiert. Analog dazu gilt: Ein Objekt kann nicht mit einem nicht eindeutigen UPN- oder ProxyAddress-Wert aktualisiert werden. Der Bereitstellungs- oder Aktualisierungsversuch wird vom Synchronisierungsclient bei jedem Exportzyklus wiederholt und kann erst nach Beseitigung des Konflikts erfolgreich abgeschlossen werden. Bei jedem Versuch wird eine E-Mail mit einem Fehlerbericht generiert, und vom Synchronisierungsclient wird ein Fehler protokolliert.

## Verhalten mit Resilienz bei doppelten Attributen

Das Bereitstellen oder Aktualisieren eines Objekts mit doppeltem Attribut ist nicht einfach ohne Erfolg. Stattdessen wird das doppelte Attribut, das gegen die Eindeutigkeitsanforderung verstößt, von Azure Active Directory isoliert. Ist dieses Attribut für die Bereitstellung erforderlich (wie etwa im Falle von UserPrincipalName), weist der Dienst einen Platzhalterwert zu. Diese temporären Werte haben folgendes Format: <br> ***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>.onmicrosoft.com***.<br> Ist das Attribut nicht erforderlich (etwa im Falle von **ProxyAddress**), wird das Konfliktattribut einfach von Azure Active Directory isoliert und die Objekterstellung oder -aktualisierung fortgesetzt.
 
Im Falle einer Attributisolierung werden Informationen zum Konflikt in der gleichen Fehlerbericht-E-Mail gesendet, die auch im Rahmen des alten Verhaltens verwendet wurde. Diese Informationen werden jedoch nur einmal (zum Zeitpunkt der Isolierung) in den Fehlerbericht aufgenommen und in zukünftigen E-Mails nicht immer wieder erneut protokolliert. Da der Export für das Objekt erfolgreich war, protokolliert der Synchronisierungsclient keinen Fehler, und es wird in den folgenden Synchronisierungszyklen nicht erneut versucht, die Erstellung/Aktualisierung durchzuführen.

Zur Unterstützung dieses Verhaltens wurde den Objektklassen für Benutzer, Gruppen und Kontakte ein neues Attribut hinzugefügt: <br> **DirSyncProvisioningErrors**.

Hierbei handelt es sich um ein mehrwertiges Attribut zum Speichern der in Konflikt stehenden Attribute, die gegen die Eindeutigkeitsanforderung verstoßen würden, wenn sie normal hinzugefügt würden. In Azure Active Directory wurde eine Timer-Hintergrundaufgabe hinzugefügt, um stündlich nach behobenen Konflikten mit doppelten Attributen zu suchen und automatisch die Isolation der betreffenden Attribute aufzuheben.



###Aktivieren der Resilienz bei doppelten Attributen

Wie weiter oben in diesem Thema erwähnt, befindet sich dieses neue Verhalten derzeit in der Vorschauphase. Sobald es allgemein verfügbar ist, wird es automatisch für alle Mandanten aktiviert. In der Vorschauphase kann es nach dem Herunterladen der neuesten Version des PowerShell-Moduls von Azure Active Directory durch Ausführen folgender Befehle aktiviert werden:

`Set-MsolDirSyncFeature -Feature DuplicateUPNResiliency -Enable $true`

`Set-MsolDirSyncFeature -Feature DuplicateProxyAddressResiliency -Enable $true`

Während der Vorschauphase kann die UPN- und ProxyAddress-Resilienz einzeln aktiviert und deaktiviert werden. Sobald das Verhalten allgemein verfügbar ist, wird beides für alle Mandanten aktiviert und kann nicht mehr deaktiviert werden.



## Ermitteln von Objekten mit DirSyncProvisioningErrors

Objekte mit Fehlern aufgrund von Konflikten mit doppelten Eigenschaften können derzeit auf zwei Arten ermittelt werden: über Azure Active Directory PowerShell und über das Office 365-Verwaltungsportal. Es ist geplant, dieses Feature im Laufe der Zeit um weitere Portale zu erweitern.

### Azure Active Directory PowerShell

Für die PowerShell-Cmdlets in diesem Thema gilt Folgendes:

- Bei allen der folgenden Cmdlets muss die Groß-/Kleinschreibung beachtet werden. 
- **–ErrorCategory PropertyConflict** muss immer eingeschlossen werden. Derzeit sind keine anderen Arten von **ErrorCategory** vorhanden, dies kann sich jedoch noch ändern.

Führen Sie zunächst **Connect-MsolService** aus, und geben Sie Anmeldeinformationen für einen Mandantenadministrator ein.

Verwenden Sie anschließend die folgenden Cmdlets und Operatoren, um Fehler auf verschiedene Arten anzuzeigen:

1. [Alle anzeigen](#see-all)

2. [Nach Eigenschaftstyp](#by-property-type)

3. [Nach Konfliktwert](#by-conflicting-value)

4. [Mithilfe einer Zeichenfolgensuche](#using-a-string-search)

5. [Sortiert](#sorted)

6. [Eingeschränkte Menge oder alle](#in-a-limited-quantity-or-all)

<br>

#### Alle anzeigen 

Führen Sie nach dem Herstellen der Verbindung den folgenden Befehl aus, um eine allgemeine Liste mit Attributbereitstellungsfehlern für den Mandanten anzuzeigen:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Dadurch erhalten Sie ein Ergebnis wie das folgende:

<br> ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError") <br>


 
#### Nach Eigenschaftstyp

Wenn Sie Fehler nach Eigenschaftstyp anzeigen möchten, fügen Sie das Flag **-PropertyName** mit dem Argument **UserPrincipalName** oder **ProxyAddresses** hinzu:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Oder

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### Nach Konfliktwert

Wenn Sie Fehler für eine bestimmte Eigenschaft anzeigen möchten, fügen Sie das Flag **-PropertyValue** hinzu (zusätzlich zu **-PropertyName**):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`


#### Mithilfe einer Zeichenfolgensuche

Verwenden Sie für eine allgemeine Zeichenfolgensuche das Flag **-SearchString**. Dieses Flag kann unabhängig von den weiter oben genannten Flags verwendet werden (mit Ausnahme des immer erforderlichen **-ErrorCategory PropertyConflict**):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### Sortiert

Die Ergebnisse einer Abfrage können mithilfe von zwei Flags sortiert werden:

1.	**SortField**: Gültige Argumente sind DisplayName und UserPrincipalName.
 
2.	**SortDirection**: Gültige Argumente sind „Ascending“ und „Descending“.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SortField UserPrincipalName -SortDirection Ascending`


#### Eingeschränkte Menge oder alle

1. Mit **MaxResults <Int>** kann die Abfrage auf eine bestimmte Anzahl von Werten beschränkt werden. 
 
2. Bei einer großen Anzahl von Fehlern können Sie mithilfe von **All** sicherstellen, dass alle Ergebnisse abgerufen werden.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## Verwaltungsportal von Office 365

Der Bericht im Office 365-Portal enthält nur Objekte vom Typ **User**, für die dieser Fehler vorliegt. Er enthält keine Informationen zu Konflikten zwischen Objekten vom Typ **Group**, **Contact** oder **PublicFolder**.

**Diese Fehler können im Verwaltungsportal von Office 365 wie folgt angezeigt werden**:

1.	Melden Sie sich unter **portal.office.com** als Mandantenadministrator an.

2.	Klicken Sie auf **Benutzer > Aktive Benutzer**. <br>


    ![Aktive Benutzer](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/2.png "Aktive Benutzer") <br>



 
3.	Falls für Objekte des Mandanten Fehler vorliegen, die auf doppelte Attribute zurückzuführen sind, wird am oberen Seitenrand eine Warnung angezeigt: <br> ![Aktive Benutzer](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/3.png "Aktive Benutzer") <br>
 
4.	Wählen Sie in der Dropdownliste „Ansicht auswählen“ die Option „Benutzer mit Fehlern“ aus, um objektspezifische Details anzuzeigen: <br> ![Aktive Benutzer](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/4.png "Aktive Benutzer") <br>
 
5.	Klicken Sie auf ein Objekt, um in der rechten unteren Bildschirmecke weitere Konfliktdetails anzuzeigen: <br> ![Aktive Benutzer](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/5.png "Aktive Benutzer") <br>
 
### Fehlerbericht für die Identitätssynchronisierung

Wenn dieses neue Verhalten bei einem Objekt mit einem Konflikt aufgrund eines doppelten Attributs angewendet wird, enthält die standardmäßige Fehlerberichts-E-Mail für die Identitätssynchronisierung, die an den Kontakt für technische Benachrichtigungen des Mandanten gesendet wird, eine entsprechende Benachrichtigung. Bei diesem Verhalten gibt es jedoch eine wichtige Änderung. In der Vergangenheit wurden Informationen zu einem Konflikt aufgrund eines doppelten Attributs in jeden nachfolgenden Fehlerbericht einbezogen, bis der Konflikt behoben wurde. Bei Verwendung des neuen Verhaltens erscheint die Fehlerbenachrichtigung für einen bestimmten Konflikt lediglich einmal (zu dem Zeitpunkt, zu dem das in Konflikt stehende Attribut isoliert wird).

Hier sehen Sie ein Beispiel für eine E-Mail-Benachrichtigung bei einem ProxyAddress-Konflikt:

<br> ![Aktive Benutzer](./media/active-directory-aadconnectsync-duplicate-attribute-resiliency/6.png "Aktive Benutzer") <br>
 
## Beheben von Konflikten

Zur Behandlung von Fehlern aufgrund von doppelten Attributen werden die gleichen Strategien und Vorgehensweisen verwendet wie zuvor. Der einzige Unterschied besteht darin, dass für den Mandanten automatisch dienstseitig die Timer-Aufgabe ausgeführt wird, um dem entsprechenden Objekt nach Behebung des Konflikts das betreffende Attribut hinzuzufügen.

Der folgende Artikel beschäftigt sich mit verschiedenen Strategien zur Problembehandlung und -behebung: [Doppelte oder ungültige Attribute verhindern Verzeichnissynchronisierung in Office 365](https://support.microsoft.com/de-DE/kb/2647098).

## Bekannte Probleme 

Keines dieser bekannten Probleme führt zu Datenverlusten oder Dienstbeeinträchtigungen. Einige sind kosmetischer Natur, andere führen dazu, dass das in Konflikt stehende Attribut nicht isoliert und stattdessen ein Standardfehler wie vor der Resilienz bei doppelten Attributen ausgelöst wird, und wieder ein anderer bewirkt, dass bei bestimmten Fehlern zusätzliche manuelle Korrekturen erforderlich sind.

**Grundverhalten:**

1. Bei einem Benutzer mit einer bestimmten Attributkonfiguration treten immer wieder Exportfehler auf, obwohl Attribute eigentlich isoliert werden sollten. <br>Beispiel:

    a. In Active Directory wird ein neuer Benutzer mit dem UPN-Wert ****Joe@contoso.com** und dem ProxyAddress-Wert **smtp:Joe@contoso.com** erstellt.

    b. Die Eigenschaften dieses Objekts stehen mit einer vorhandenen Gruppe mit dem ProxyAddress-Wert **SMTP:Joe@contoso.com** in Konflikt.

    c. Beim Exportieren werden die in Konflikt stehenden Attribute nicht isoliert. Stattdessen tritt ein Fehler vom Typ **ProxyAddress-Konflikt** auf. Der Vorgang wird wie vor der Aktivierung des Resilienzfeatures in jedem nachfolgenden Synchronisierungszyklus wiederholt.

2. Die Timer-Aufgabe, die nach behobenen Konflikten mit doppelten Attributen sucht, vergleicht nur UPN-Konflikte mit anderen UPN-Konflikten. Dadurch kommt es zu dem Problem, das in Schritt 4 des folgenden Szenarios beschrieben ist:

    a. ****UserA@contoso.com** hat einen nicht eindeutigen UPN-Wert, da ein anderes Objekt diesen Wert als ProxyAddress-Wert besitzt.

    b. UserA erhält einen temporären Wert vom Typ **MOERA UPN** (****UserA1234@contoso.onmicrosoft.com**), und der tatsächliche UPN-Wert wird isoliert (wie erwartet).

    c. Der ProxyAddress-Wert des anderen in Konflikt stehenden Objekts wird später entfernt.

    d. Der UPN-Wert von UserA wird nicht automatisch korrigiert und muss manuell aktualisiert werden.

3. Wenn zwei Gruppen lokal mit der gleichen SMTP-Adresse erstellt werden, kann eine davon beim ersten Versuch nicht erfolgreich bereitgestellt werden, und es tritt ein Standardfehler für doppelte Werte vom Typ **ProxyAddress** auf. Der doppelte Wert wird beim nächsten Synchronisierungszyklus jedoch ordnungsgemäß isoliert.

**PowerShell-Cmdlets**:

1. **ImmutableId**/**LastDirSyncTime** werden für die Objektklasse „User“ nicht angezeigt. 

2. Die Flags **SortField** und **SortDirection** haben keine Auswirkung auf die Ergebnisse.

3. Wenn **PropertyValue** ohne **PropertyName** verwendet wird, tritt ein mehrdeutiger Fehler auf.

4. Das Flag **SearchString** gibt zusätzliche Ergebnisse zurück, wenn es ohne **PropertyValue** und **PropertyName** verwendet wird.



**Bericht des Office-Portals**:

1. Die ausführliche Fehlermeldung für zwei Objekte in einem UPN-Konfliktsatz ist identisch. Sie gibt an, dass bei beiden der UPN-Wert geändert/isoliert wurde, obwohl sich eigentlich nur die Daten eines der Objekte geändert haben.

2. Die ausführliche Fehlermeldung für einen UPN-Konflikt enthält den falschen displayName-Wert für einen Benutzer, dessen UPN geändert/isoliert wurde. Beispiel:

    a. Zunächst wird **Benutzer A** mit **UPN = User@contoso.com** synchronisiert.

    b. Anschließend wird versucht, **Benutzer B** mit **UPN = User@contoso.com** zu synchronisieren.

    c. Der UPN-Wert von **Benutzer B** wird in ****User1234@contoso.onmicrosoft.com** geändert, und ****User@contoso.com** wird **DirSyncProvisioningErrors** hinzugefügt.

    d. Die Fehlermeldung für **Benutzer B** sollte angeben, dass **Benutzer A** bereits den UPN-Wert ****User@contoso.com** besitzt, enthält aber den displayName-Wert von **Benutzer B**.

3. Der Bericht enthält unter Umständen nur ausführliche Fehlerinformationen zu Benutzern mit Konflikten vom Typ **UPN**, nicht zu Benutzern mit Fehlern vom Typ **ProxyAddress**. (Es wird noch untersucht, ob dies immer so ist oder von der Umgebung abhängt.)




##Weitere Informationen

- [Azure AD Connect-Synchronisierung](active-directory-aadconnectsync-whatis.md)

- [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0323_2016-->