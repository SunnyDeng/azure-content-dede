<properties
   pageTitle="Azure AD Connect: Unterstützte Topologien | Microsoft Azure"
   description="In diesem Thema werden unterstützte und nicht unterstützte Topologien für Azure AD Connect behandelt."
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
   ms.date="12/02/2015"
   ms.author="andkjell"/>

# Topologien für Azure AD Connect

Das Ziel dieses Themas ist, verschiedene lokale und Azure AD-Topologien mit Azure AD Connect-Synchronisierung als Schlüsselintegrationslösung zu beschreiben. Es werden sowohl unterstützte als auch nicht unterstützte Konfigurationen beschrieben.

Legende für Bilder im Dokument:

| Beschreibung | Symbol |
|-----|-----|
| Lokale Active Directory-Gesamtstruktur | ![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png)|
| Active Directory mit gefiltertem Import | ![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png)|
| Azure AD Connect-Synchronisierungsserver | ![Synchronisierung](./media/active-directory-aadconnect-topologies/LegendSync1.png)|
| Azure AD Connect-Synchronisierungsserver "Stagingmodus" | ![Synchronisierung](./media/active-directory-aadconnect-topologies/LegendSync2.png)|
| GALSync mit FIM2010 oder MIM2016 | ![Synchronisierung](./media/active-directory-aadconnect-topologies/LegendSync3.png)|
| Azure AD Connect-Synchronisierungsserver, detailliert |![Synchronisierung](./media/active-directory-aadconnect-topologies/LegendSync4.png)|
| Azure AD-Verzeichnis |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png)|
| Nicht unterstütztes Szenario | ![Nicht unterstützt](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)



## Einzelne Gesamtstruktur, einzelnes Azure AD-Verzeichnis
![SingleForestSingleDirectory](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

Die häufigste Topologie ist eine einzelne lokale Gesamtstruktur mit einer oder mehreren Domänen und einem einzelnen Azure AD-Verzeichnis (auch bekannt als Mandant). Azure AD-Authentifizierung erfolgt mit Kennwortsynchronisierung. Diese Topologie wird durch die Expressinstallation von Azure AD Connect unterstützt.

### Einzelne Gesamtstruktur, mehrere Synchronisierungsserver zu einem Azure AD-Verzeichnis
![SingleForestFilteredUnsupported](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Die Verbindung mehrerer Azure AD Connect-Synchronisierungsserver mit dem gleichen Azure AD-Verzeichnis wird auch dann nicht unterstützt, wenn sie dazu konfiguriert sind, wechselseitig exklusive Gruppen von Objekten zu synchronisieren (mit Ausnahme eines [Stagingservers](#staging-server)). Dies könnte versucht werden, weil eine Domäne in einer Gesamtstruktur von einem gemeinsamen Speicherort im Netzwerk aus nicht erreichbar ist, oder bei einem Versuch, die Synchronisierungslast auf mehrere Server zu verteilen.

## Mehrere Gesamtstrukturen, einzelnes Azure AD-Verzeichnis
![MultiForestSingleDirectory](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Viele Organisationen verfügen über Umgebungen, die mehrere lokale Active Directory-Gesamtstrukturen umfassen. Es gibt verschiedene Gründe, mehrere lokale Active Directory-Gesamtstrukturen bereitzustellen. Typische Beispiele sind Entwürfe mit Kontoressourcengesamtstruktur, Gesamtstrukturen in Zusammenhang mit Unternehmensfusionen oder -übernahmen oder Gesamtstrukturen, die zum Auslagern von Daten verwendet werden.

Wenn Sie mehrere Gesamtstrukturen haben, müssen alle Gesamtstrukturen von einem einzelnen Azure AD Connect-Synchronisierungsserver aus erreichbar sein. Der Server muss nicht mit einer Domäne verknüpft werden und kann, wenn erforderlich, in einer Netzwerk-DMZ platziert werden, um alle Gesamtstrukturen erreichen zu können.

Der Azure AD Connect-Assistent bietet mehrere Optionen für das Konsolidieren von Benutzern. Auch wenn der gleiche Benutzer mehrere Male in verschiedenen Gesamtstrukturen dargestellt wird, so wird er in Azure AD nur einmal dargestellt. Unten werden einige allgemeinen Topologien erläutert. Welche Topologie Sie haben, konfigurieren Sie mit dem benutzerdefinierten Installationspfad im Installations-Assistenten, und Sie wählen die entsprechende Option auf der Seite "Eindeutige Identifizierung der Benutzer". Die Konsolidierung wird nur für Benutzer ausgeführt. Wenn Gruppen dupliziert werden, werden diese nicht mit der Standardkonfiguration konsolidiert.

Allgemeine Topologien werden im nächsten Abschnitt erläutert: [Separate Topologien](#multiple-forests-separate-topologies), [Vollständig vermaschtes Netz](#multiple-forests-full-mesh-with-optional-galsync) und [Kontoressource](#multiple-forests-account-resource-forest).

Die Standardkonfiguration der Azure AD Connect-Synchronisierung basiert auf folgenden Annahmen: 1. Benutzer haben nur ein einziges aktiviertes Konto, und die Gesamtstruktur, in der sich dieses Konto befindet, wird verwendet, um den Benutzer zu authentifizieren. Dies gilt für Kennwortsynchronisierung und Verbund; userPrincipalName und sourceAnchor/immutableID stammen aus dieser Gesamtstruktur. 2. Benutzer haben nur ein einziges Postfach. 3. Die Gesamtstruktur, die das Postfach des Benutzers enthält, hat die beste Datenqualität für Attribute, die in der globalen Exchange-Adressliste (Global Address List, GAL) sichtbar sind. Wenn kein Postfach für den Benutzer vorhanden ist, kann jede Gesamtstruktur verwendet werden, um diese Attributwerte beizutragen. 4. Wenn Sie ein verknüpftes Postfach haben, dann wird auch ein anderes Konto in einer anderen Gesamtstruktur für die Anmeldung verwendet.

Wenn Ihre Umgebung mit diesen Annahmen nicht übereinstimmt, geschieht Folgendes: – Wenn Sie mehrere aktive Konten oder mehrere Postfächer haben, wählt das Synchronisierungsmodul eines aus und ignoriert die anderen. – Wenn Sie verknüpfte Postfächer, jedoch kein anderes Konto haben, werden diese Konten nicht in Azure AD exportiert, und der Benutzer wird nicht Mitglied einer Gruppe. In DirSync würde ein verknüpftes Postfach als normales Postfach dargestellt, darum ist dies absichtlich ein anderes Verhalten, um Szenarien mit mehreren Gesamtstrukturen besser zu unterstützen.

### Mehrere Gesamtstrukturen, mehrere Synchronisierungsserver zu einem Azure AD-Verzeichnis
![MultiForestMultiSyncUnsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Das Verbinden mehrerer Azure AD Connect-Synchronisierungsserver mit einem einzelnen Azure AD-Verzeichnis wird nicht unterstützt (mit Ausnahme eines [Stagingservers](#staging-server)).

### Mehrere Gesamtstrukturen – separate Topologien
"Benutzer werden nur einmal für alle Verzeichnisse dargestellt."

![MultiForestUsersOnce](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![MultiForestSeperateTopologies](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

In dieser Umgebung werden alle lokalen Gesamtstrukturen als separate Entitäten behandelt, und kein Benutzer ist in einer anderen Gesamtstruktur vorhanden. Jede Gesamtstruktur verfügt über eine eigene Exchange-Organisation, und es gibt keine GALSync zwischen den Gesamtstrukturen. Dies könnte die Situation nach einer Fusion/Übernahme oder in einer Organisation sein, in der die einzelnen Geschäftseinheiten isoliert voneinander operieren. In Azure AD gehören diese Gesamtstrukturen zu der gleichen Organisation und werden in einer einheitlichen GAL angezeigt. In dieser Abbildung wird jedes Objekt in jeder Gesamtstruktur einmal im Metaverse dargestellt und im Azure AD-Zielverzeichnis aggregiert.

### Mehrere Gesamtstrukturen – Benutzer abgleichen
In allen Szenarien mit mehreren Gesamtstrukturen, in denen Sie eine der Optionen unter "Benutzeridentitäten sind in mehreren Verzeichnissen vorhanden" auswählen, sind Verteilungs- und Sicherheitsgruppen in jeder Gesamtstruktur zu finden und können eine Kombination von Benutzern, Kontakten und FSPs (fremden Sicherheitsprinzipalen) enthalten.

FSPs werden in ADDS verwendet, um Mitglieder aus anderen Gesamtstrukturen in einer Sicherheitsgruppe darzustellen. Das Synchronisierungsmodul löst den FSP zu einem echten Benutzer auf und stellt die Sicherheitsgruppe in Azure AD dar, wobei alle FSPs zu dem eigentlichen Objekt aufgelöst werden.

### Mehrere Gesamtstrukturen – vollständiges Netz mit optionaler GALSync
"Benutzeridentitäten sind in mehreren Verzeichnisse vorhanden. Abgleich über: Mail-Attribut"

![MultiForestUsersMail](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![MultiForestFullMesh](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Mit einer vollständig vernetzten Topologie können Benutzer und Ressourcen in jeder Gesamtstruktur ermittelt werden, und in der Regel bestehen bidirektionale Vertrauensstellungen zwischen den Gesamtstrukturen.

Wenn Exchange in mehreren Gesamtstrukturen vorhanden ist, kann optional eine lokale GALSync-Lösung einen Benutzer aus einer Gesamtstruktur in jeder anderen Gesamtstruktur als Kontakt darstellen. GALSync wird häufig mithilfe von Forefront Identity Manager 2010 oder Microsoft Identity Manager 2016 implementiert. Azure AD Connect kann nicht für lokale GALSync verwendet werden.

In diesem Szenario werden Identitätsobjekte über das E-Mail-Attribut verknüpft. In der Folge wird ein Benutzer mit einem Postfach in einer Gesamtstruktur mit den Kontakten in den anderen Gesamtstrukturen verknüpft.

### Mehrere Gesamtstrukturen – Kontoressourcengesamtstruktur
"Benutzeridentitäten sind in mehreren Verzeichnisse vorhanden. Abgleich über: ObjectSID- und msExchMasterAccountSID-Attribute"

![MultiForestUsersObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![MultiForestAccountResource](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

In einer Kontoressourcengesamtstruktur-Topologie befinden sich eine oder mehrere Kontogesamtstrukturen mit aktiven Benutzerkonten.

Dieses Szenario besteht aus einer Gesamtstruktur, die allen Kontogesamtstrukturen vertraut. Diese Gesamtstruktur verfügt in der Regel über ein erweitertes AD-Schema mit Exchange und Lync. Alle Dienste von Exchange und Lync sowie andere freigegebene Dienste befinden sich in dieser Gesamtstruktur. Benutzer haben ein deaktiviertes Benutzerkonto in dieser Gesamtstruktur, und das Postfach ist mit der Kontengesamtstruktur verknüpft.

## Office 365 und Überlegungen zur Netzwerktopologie
Für einige Office 365-Workloads gelten bestimmte Einschränkungen für unterstützte Topologien. Wenn Sie diese verwenden möchten, informieren Sie sich bitte auf den Seiten zu unterstützten Topologien für die jeweilige Workload.

| Workload | |
| --------- | --------- |
| Exchange Online |	Wenn mehrere Exchange-Organisationen lokal vorhanden sind (d. h. Exchange für mehrere Gesamtstrukturen bereitgestellt wurde), müssen Sie Exchange 2013 SP1 oder höher verwenden. Weitere Informationen finden Sie hier: [Hybrid-Bereitstellungen mit mehreren Active Directory-Gesamtstrukturen](https://technet.microsoft.com/de-DE/library/jj873754.aspx) |
| Skype for Business | Bei Verwendung mehrerer lokaler Gesamtstrukturen wird nur die Kontoressourcengesamtstruktur-Topologie unterstützt. Weitere Informationen zu unterstützten Topologien finden Sie hier: [Anforderungen an die Umgebung für Skype for Business Server 2015](https://technet.microsoft.com/de-DE/library/dn933910.aspx) |

## Stagingserver
![StagingServer](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect unterstützt die Installation eines zweiten Servers im "Stagingmodus". Ein Server in diesem Modus liest nur Daten aus allen verbundenen Verzeichnissen und verfügt daher über eine aktualisierte Kopie der Identitätsdaten. Bei einem Notfall, in dem der primäre Server ausfällt, kann mühelos mit dem Azure AD Connect-Assistenten ein Failover zu dem zweiten Server ausgeführt werden. Dieser zweite Server kann sich vorzugsweise in einem anderen Datencenter befinden, da keine Infrastruktur mit dem primären Server gemeinsam genutzt wird. Jede am primären Server vorgenommene Konfigurationsänderung müssen Sie nach dem zweiten Server kopieren.

Ein Stagingserver kann auch verwendet werden, wenn Sie eine neue benutzerdefinierte Konfiguration und deren Auswirkung auf die Daten testen möchten. Sie können eine Vorschau der Änderungen anzeigen und die Konfiguration anpassen. Wenn Sie mit der neuen Konfiguration zufrieden sind, können Sie den Stagingserver zum aktiven Server machen und den alten aktiven Server in den Stagingmodus setzen.

Diese Methode kann auch verwendet werden, wenn Sie den Synchronisierungsserver ersetzen und den neuen vorbereiten möchten, bevor Sie den derzeit aktiven Server herunterfahren.

Sie können mehrere Stagingserver haben, wenn Sie mehrere Backups in verschiedenen Datencentern haben möchten.

## Mehrere Azure AD-Verzeichnisse
Microsoft empfiehlt, für eine Organisation über ein einzelnes Verzeichnis in Azure AD zu verfügen. Bevor Sie planen, mehrere Azure AD-Verzeichnisse zu verwenden, lesen Sie diese Themen zu gängigen Szenarien, in denen Sie ein einzelnes Verzeichnis verwenden können.

| Thema | |
| --------- | --------- |
| Delegieren mithilfe administrativer Einheiten | [Verwaltung administrativer Einheiten in Azure AD](active-directory-administrative-units-management.md)

![MultiForestMultiDirectory](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Es besteht eine 1:1-Beziehung zwischen einem Azure AD Connect-Synchronisierungsserver und einem Azure AD-Verzeichnis. Für jedes Azure AD-Verzeichnis benötigen Sie eine Azure AD Connect-Synchronisierungsserverinstallation. Azure AD-Verzeichnisinstanzen sind als isoliert konzipiert, und Benutzer in einer Instanz können Benutzer im anderen Verzeichnis nicht sehen. Wenn dies beabsichtigt ist, handelt es sich um eine unterstützte Konfiguration, aber andernfalls sollten Sie die oben beschriebenen Azure AD-Einzelverzeichnismodelle verwenden.

### Jedes Objekt nur einmal in einem Azure AD-Verzeichnis
![SingleForestFiltered](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

In dieser Topologie ist ein AAD Connect-Synchronisierungsserver mit jedem Azure AD-Verzeichnis verbunden. Die Azure AD Connect-Synchronisierungsserver müssen für das Filtern konfiguriert werden, sodass sie wechselseitig an exklusiven Gruppen von Objekten aktiv sind, z. B. durch Beschränkung jedes Servers auf eine bestimmte Domäne oder Organisationseinheit. Eine DNS-Domäne kann nur in einem einzelnen Azure AD-Verzeichnis registriert werden, sodass die UPNs der Benutzer im lokalen AD auch separate Namespaces verwenden müssen. Im obigen Bild sind z. B. drei separate UPN-Suffixe im lokalen AD registriert: contoso.com, fabrikam.com und wingtiptoys.com. Die Benutzer in jeder lokalen AD-Domäne verwenden einen anderen Namespace.

In dieser Topologie gibt es keine "GALsync" zwischen den Azure AD-Verzeichnisinstanzen, sodass das Adressbuch in Exchange Online und Skype for Business nur Benutzer im gleichen Verzeichnis anzeigt.

Mit dieser Topologie kann nur eines der Azure AD-Verzeichnisse den Exchange-Hybrid mit dem lokalen Active Directory aktivieren.

Die Anforderung für wechselseitig exklusive Gruppen von Objekten gilt auch für das Zurückschreiben. Damit werden einige Zurückschreibfeatures mit dieser Topologie nicht unterstützt, da sie eine lokale Einzelkonfiguration voraussetzen. Dazu gehören: - Gruppenzurückschreiben mit Standardkonfiguration – Gerätezurückschreiben

### Jedes Objekt mehrmals in einem Azure AD-Verzeichnis
![SingleForestMultiDirectoryUnsupported](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![SingleForestMultiConnectorsUnsupported](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

Die Synchronisierung des gleichen Benutzers mit mehreren Azure AD-Verzeichnissen wird nicht unterstützt. Es wird auch nicht unterstützt, die Konfiguration so zu ändern, dass Benutzer in einem Azure AD in einem anderen Azure AD-Verzeichnis als Kontakte angezeigt werden. Es wird auch nicht unterstützt, die Azure AD Connect-Synchronisierung so zu ändern, dass Verbindungen zu mehreren Azure AD-Verzeichnissen möglich sind.

### GALsync mithilfe von Rückschreiben
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD-Verzeichnisse sind als isoliert konzipiert. Es wird nicht unterstützt, die Konfiguration der Azure AD Connect-Synchronisierung so zu ändern, dass in einem Versuch, eine gemeinsame und einheitliche GAL zwischen den Verzeichnissen zu erstellen, Daten aus einem anderen Azure AD-Verzeichnis gelesen werden. Es wird auch nicht unterstützt, mithilfe der Azure AD Connect-Synchronisierung Benutzer als Kontakte nach einem anderen lokalen AD zu exportieren.

### GALsync mit lokalem Server
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

Es wird unterstützt, FIM2010/MIM2016 lokal für GALsync zum Synchronisieren von Benutzern zwischen zwei Exchange-Organisationen zu verwenden. Die Benutzer in der einen Organisation werden in der anderen Organisation als fremde Benutzer/Kontakte angezeigt. Diese anderen lokalen ADs können dann mit ihren eigenen Azure AD-Verzeichnissen synchronisiert werden.


## Nächste Schritte
Weitere Informationen zum Installieren von Azure AD-Connect für diese Szenarios finden Sie unter [Benutzerdefinierte Installation von Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](active-directory-aadconnectsync-whatis.md)

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_1203_2015-->