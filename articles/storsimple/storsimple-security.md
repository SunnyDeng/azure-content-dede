<properties 
   pageTitle="StorSimple-Sicherheit | Microsoft Azure" 
   description="In diesem Abschnitt werden die Sicherheits- und Datenschutzfunktionen zum Schutz Ihres StorSimple-Diensts und -Geräts sowie Ihrer lokalen Daten und Daten in der Cloud beschrieben." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="Carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="01/22/2016"
   ms.author="v-sharos"/>

# StorSimple-Sicherheit und -Datenschutz

## Übersicht

Sicherheit ist für alle, die eine neue Technologie implementieren, ein zentrales Anliegen. Dies gilt insbesondere, wenn die Technologie mit vertraulichen oder proprietären Daten verwendet wird. Bei der Bewertung der verschiedenen Technologien müssen Sie die erhöhten Risiken und Kosten für den Datenschutz berücksichtigen. Microsoft Azure StorSimple bietet sowohl eine Sicherheits- als auch Datenschutzlösung Ihre Daten, wodurch Folgendes sichergestellt werden kann:

- **Vertraulichkeit** – Nur autorisierte Entitäten können Ihre Daten anzeigen. 
- **Integrität** – Nur autorisierte Entitäten können Ihre Daten ändern oder löschen.

Die Microsoft Azure StorSimple-Lösung besteht aus vier Hauptkomponenten, die miteinander interagieren:

- **In Microsoft Azure gehosteter StorSimple-Manager-Dienst** – Der Verwaltungsdienst, den Sie zum Konfigurieren und Bereitstellen des StorSimple-Geräts verwenden.
- **StorSimple-Gerät** – Ein physisches Gerät, das in Ihrem Datencenter installiert ist. Alle Hosts und Clients, die Daten generieren, stellen eine Verbindung zum StorSimple-Gerät her, während das Gerät die Daten verwaltet und entsprechend in die Azure-Cloud verschiebt.
- **Mit dem Gerät verbundene Clients/Hosts** – Die Clients in Ihrer Infrastruktur, die eine Verbindung zum StorSimple-Gerät herstellen und zu schützende Daten generieren.
- **Cloud-Speicher** – Der Speicherort in der Azure-Cloud, an dem die Daten gespeichert werden.

In den folgenden Abschnitten werden die in der StorSimple-Sicherheitsfunktionen beschrieben, mit denen alle diese Komponenten und die in ihnen gespeicherten Daten geschützt werden. Außerdem finden Sie eine Liste von Fragen, die Sie ggf. hinsichtlich der Sicherheit von Microsoft Azure StorSimple haben, und die entsprechenden Antworten.

## Schutz des StorSimple-Manager-Diensts

Der StorSimple-Manager-Dienst ist ein in Microsoft Azure gehosteter Verwaltungsdienst, der zum Verwalten aller StorSimple-Geräte verwendet wird, die Ihre Organisation beschafft hat. Sie können auf den StorSimple Manager-Dienst zugreifen, indem Sie sich beim klassischen Azure-Portal mithilfe eines Webbrowsers unter Verwendung Ihrer Organisationsanmeldeinformationen anmelden.

Für den Zugriff auf den StorSimple-Manager-Dienst ist es erforderlich, dass Ihre Organisation über ein Azure-Abonnement verfügt, das StorSimple einschließt. Ihr Abonnement bestimmt die Funktionen, auf die Sie im klassischen Azure-Portal zugreifen können. Wenn Ihre Organisation kein Azure-Abonnement besitzt, und Sie weitere Informationen dazu erhalten möchten, lesen Sie [Als Unternehmen für Azure registrieren](../active-directory/sign-up-organization.md).

Da der StorSimple-Manager-Dienst in Azure gehostet wird, ist er durch die Azure-Sicherheitsfeatures geschützt. Weitere Informationen zu den Sicherheitsfeatures von Microsoft Azure finden Sie im [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/security/).

## StorSimple-Geräteschutz

Das StorSimple-Gerät ist ein lokales Hybridspeicherarray mit Solid-State-Laufwerken (SSDs) und Festplattenlaufwerken (HDDs) sowie redundanten Controllern und automatischen Failoverfunktionen. Die Controller verwalten die Speicherstaffelung, wobei derzeit verwendete d. h. aktiv genutzte) Daten in lokalem Speicher (auf dem StorSimple-Gerät oder lokalen Server) abgelegt werden, während weniger häufig verwendete Daten in die Cloud verschoben werden.

Nur autorisierte StorSimple-Geräte dürfen dem StorSimple-Manager-Dienst hinzugefügt werden, den Sie in Ihrem Azure-Abonnement erstellt haben. Um ein Gerät zu autorisieren, müssen Sie es beim StorSimple-Manager-Dienst registrieren, indem Sie den Dienstregistrierungsschlüssel bereitstellen. Der Dienstregistrierungsschlüssel ist ein im klassischen Azure-Portal generierter zufälliger 128-Bit-Schlüssel.

![Dienstregistrierungsschlüssel](./media/storsimple-security/ServiceRegistrationKey.png)

Infomationen zum Abrufen eines Dienstregistrierungsschlüssels erhalten Sie unter [Schritt 2: Abrufen des Dienstregistrierungsschlüssels](storsimple-deployment-walkthrough.md#step-2-get-the-service-registration-key).

Der Dienstregistrierungsschlüssel ist ein langer Schlüssel mit mehr als 100 Zeichen. Sie können den Schlüssel kopieren und ihn an einem sicheren Ort in einer Textdatei speichern, damit Sie bei Bedarf weitere Geräte mit dem Schlüssel autorisieren können. Wenn der Dienstregistrierungsschlüssel nach der Registrierung Ihres ersten Geräts verloren geht, können Sie über den StorSimple-Manager-Dienst einen neuen Schlüssel generieren. Dies wirkt sich nicht auf den Betrieb vorhandener Geräte aus.

Nachdem ein Gerät registriert wurde, verwendet es Token für die Kommunikation mit Microsoft Azure. Der Dienstregistrierungsschlüssel wird nach der Geräteregistrierung nicht verwendet.

> [AZURE.NOTE] Es wird empfohlen, den Dienstregistrierungsschlüssel nach jeder Verwendung neu zu generieren.

## Schützen der StorSimple-Lösung mittels Kennwörtern

Kennwörter sind ein wichtiger Aspekt der Computersicherheit und werden in der StorSimple-Lösung ausgiebig verwendet, um sicherzustellen, dass nur autorisierte Benutzer auf Ihre Daten zugreifen können. StorSimple ermöglicht die Konfiguration der folgenden Kennwörter:

- StorSimple-Geräteadministratorkennwort
- CHAP-Initiator- und Zielkennwörter (Challenge Handshake Authentication Protocol)
- StorSimple Snapshot Manager-Kennwort

### Windows PowerShell für StorSimple und das StorSimple-Geräteadministratorkennwort

Windows PowerShell für StorSimple ist eine Befehlszeilenschnittstelle, mit der Sie das StorSimple-Gerät verwalten können. Windows PowerShell für StorSimple bietet Features, mit denen Sie Ihr Gerät registrieren, die Netzwerkschnittstelle Ihres Geräts konfigurieren, bestimmte Arten von Updates installieren, Probleme Ihres Geräts durch Zugriff auf eine Supportsitzung beheben und den Gerätestatus ändern können. Sie können auf Windows PowerShell für StorSimple zugreifen, indem Sie eine Verbindung mit der seriellen Konsole auf dem Gerät herstellen oder Windows PowerShell-Remoting verwenden.

Das PowerShell-Remoting kann über HTTPS oder HTTP erfolgen. Wenn die Remoteverwaltung über HTTPS aktiviert ist, müssen Sie das Remoteverwaltungszertifikat vom Gerät herunterladen und es auf dem Remoteclient installieren. Weitere Informationen zum PowerShell-Remoting finden Sie unter [Herstellen einer Remoteverbindung mit dem StorSimple-Gerät](storsimple-remote-connect).

Nachdem Sie Windows PowerShell für StorSimple verwendet haben, um eine Verbindung mit dem Gerät herzustellen, müssen Sie das Geräteadministratorkennwort für die Anmeldung auf dem Gerät bereitstellen.

![Geräteadministratorkennwort](./media/storsimple-security/DeviceAdminPW.png)

Beachten Sie die folgenden bewährten Methoden:

- Die Remoteverwaltung ist standardmäßig deaktiviert. Sie können sie mithilfe des StorSimple-Manager-Diensts aktivieren. Als bewährte Sicherheitsmethode sollte der Remotezugriff nur während der tatsächlich erforderlichen Zeit aktiviert werden.
- Wenn Sie das Kennwort ändern, stellen Sie sicher, dass Sie alle Benutzer mit Remotezugriff benachrichtigen, damit es bei diesen nicht zu unerwarteten Verbindungsunterbrechungen kommt.
- Der StorSimple-Manager-Dienst kann keine vorhandenen Kennwörter abrufen, sondern nur zurücksetzen. Es wird empfohlen, alle Kennwörter an einem sicheren Ort zu speichern, damit Sie ein Kennwort nicht zurücksetzen müssen, wenn Sie es vergessen haben. Wenn Sie ein Kennwort zurücksetzen müssen, stellen Sie sicher, dass Sie alle Benutzer vor der Zurücksetzung benachrichtigen. 

Sie können über eine serielle Verbindung mit dem Gerät auf die Windows PowerShell-Benutzeroberfläche zugreifen. Sie können auch über HTTP oder HTTPS remote darauf zugreifen, was zusätzliche Sicherheit bringt. HTTPS bietet eine höhere Sicherheit als eine serielle oder HTTP-Verbindung. Für HTTPS müssen Sie allerdings zunächst auf dem Clientcomputer, der auf das Gerät zugreift, ein Zertifikat installieren. Sie können das Zertifikat für den Remotezugriff von der Konfigurationsseite des Geräts im StorSimple-Manager-Dienst herunterladen. Wenn das Zertifikat für den Remotezugriff verloren geht, müssen Sie ein neues Zertifikat herunterladen und es auf allen Clients bereitstellen, die für die Remoteverwaltung autorisiert sind.

### CHAP-Initiator- und Zielkennwörter (Challenge Handshake Authentication Protocol)

CHAP ist ein vom StorSimple-Gerät verwendetes Authentifizierungsschema zum Überprüfen der Identität von Remoteclients. Die Überprüfung basiert auf einem gemeinsam genutzten Kennwort. HAP kann unidirektional oder bidirektional verwendet werden. Beim unidirektionalen CHAP authentifiziert das Ziel (das StorSimple-Gerät) einen Initiator (Host). Beim bidirektionalen oder umgekehrten CHAP ist es erforderlich, dass das Ziel den Initiator und der Initiator dann das Ziel authentifiziert. Ihr StorSimple-Gerät kann für die Verwendung beider Methoden konfiguriert werden.

Beachten Sie beim Konfigurieren von CHAP Folgendes:

- Der CHAP-Benutzername darf maximal 233 Zeichen enthalten.
- Das CHAP-Kennwort muss zwischen 12 und 16 Zeichen umfassen. Wenn Sie versuchen, einen längeren Benutzernamen oder ein längeres Kennwort zu verwenden, führt dies auf dem Windows-Host zu einem Authentifizierungsfehler.
- Sie können für den CHAP-Initiator und das CHAP-Ziel nicht dasselbe Kennwort verwenden.
- Nachdem Sie das Kennwort festgelegt haben, kann es zwar geändert, aber nicht mehr abgerufen werden. Wenn das Kennwort geändert wird, stellen Sie sicher, dass Sie alle Benutzer mit Remotezugriff benachrichtigen, damit diese erfolgreich Verbindungen mit dem StorSimple-Gerät herstellen können.

Weitere Informationen zu CHAP und zum Konfigurieren von CHAP für StorSimple-Lösungen finden Sie unter [Konfigurieren von CHAP für Ihr StorSimple-Gerät](storsimple-configure-chap.md).

### StorSimple Snapshot Manager-Kennwort

StorSimple Snapshot Manager ist ein MMC-Snap-In (Microsoft Management Console), das Volumegruppen und den Volumeschattenkopie-Dienst von Windows verwendet, um anwendungskonsistente Sicherungen zu generieren. Darüber hinaus können Sie den StorSimple-Momentaufnahmen-Manager zum Erstellen von Sicherungszeitplänen und Klonen oder Wiederherstellen von Volumes einsetzen.

Wenn Sie ein Gerät für die Verwendung von StorSimple Snapshot Manager konfigurieren, müssen Sie das Kennwort für den StorSimple Snapshot Manager angeben. Dieses Kennwort wird erstmalig in Windows PowerShell für StorSimple während der Registrierung festgelegt. Das Kennwort kann auch über den StorSimple-Manager-Dienst festgelegt und geändert werden. Mit diesem Kennwort wird das Gerät im StorSimple-Momentaufnahme-Manager authentifiziert.

![StorSimple Snapshot Manager-Kennwort](./media/storsimple-security/SnapshotMgrPassword.png)

Das Kennwort für StorSimple Snapshot Manager muss 14 bis 15 Zeichen lang sein und mindestens drei der folgenden Elemente miteinander kombinieren: Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen. Nachdem Sie das StorSimple-Momentaufnahmen-Manager-Kennwort festgelegt haben, kann es zwar geändert, aber nicht mehr abgerufen werden. Wenn Sie das Kennwort ändern, stellen Sie sicher, dass alle Remotebenutzer benachrichtigt werden.

Weitere Informationen zu StorSimple Snapshot Manager finden Sie unter [Was ist der StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### Bewährte Methoden für Kennwörter

Es wird empfohlen, die folgenden Richtlinien zu befolgen, um sicherzustellen, dass die StorSimple-Kennwörter sicher und ausreichend geschützt sind:

- Ändern Sie Ihre Kennwörter alle drei Monate. Ein Ändern der Kennwörter wird jährlich erzwungen.
- Verwenden Sie sichere Kennwörter. Weitere Informationen finden Sie unter [Create stronger passwords and protect them](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/) (in englischer Sprache).
- Wählen Sie für verschiedene Zugriffsmechanismen stets unterschiedliche Kennwörter. Jedes gewählte Kennwort muss eindeutig sein.
- Teilen Sie Kennwörter nicht mit Personen, die nicht für den Zugriff auf das StorSimple-Gerät autorisiert sind.
- Erwähnen Sie Kennwörter nicht in Gegenwart anderer Personen, und geben Sie keine Hinweise zum Format eines Kennworts.
- Wenn Sie den Verdacht haben, dass ein Konto oder Kennwort gefährdet ist, melden Sie den Vorfall der für Informationssicherheit zuständigen Abteilung.
- Behandeln Sie alle Kennwörter als sensible, vertrauliche Informationen. 

## StorSimple-Datenschutz

In diesem Abschnitt werden die StorSimple-Sicherheitsfunktionen für in der Übertragung befindliche und gespeicherte Daten beschrieben.

Wie in anderen Abschnitten erläutert, werden Kennwörter verwendet, um Benutzer zu autorisieren und zu authentifizieren, bevor diese Zugriff auf Ihre StorSimple-Lösung erhalten. Ein weiterer Sicherheitsaspekt ist der Schutz der Daten vor nicht autorisierten Benutzern, während die Daten zwischen Speichersystemen übertragen oder gespeichert werden. In den folgenden Abschnitten werden die Datenschutzfunktionen von StorSimple beschrieben.

> [AZURE.NOTE] Eine Deduplizierung bietet zusätzlichen Schutz für Daten, die auf dem StorSimple-Gerät und in Microsoft Azure-Speicher gespeichert. Bei einer Deduplizierung der Daten werden die Datenobjekte getrennt auf den Metadaten gespeichert, die für Zuordnung und Zugriff genutzt werden. Es gibt keinen verfügbaren Speicherebenenkontext zum Rekonstruieren der Daten basierend auf Volumestruktur, Dateisystem oder Dateiname.

## Schützen von durch den Dienst geleiteten Daten

Der Hauptzweck des StorSimple-Manager-Diensts ist die Verwaltung und Konfiguration des StorSimple-Geräts. Der StorSimple Manager-Dienst wird in Microsoft Azure ausgeführt. Sie können Gerätekonfigurationsdaten über das klassische Azure-Portal eingeben. Anschließend verwendet Microsoft Azure den StorSimple Manager-Dienst, um die Daten an das Gerät zu senden. StorSimple verwendet ein System aus asymmetrischen Schlüsselpaaren, um sicherzustellen, dass eine Gefährdung des Azure-Diensts nicht zu einer Gefährdung der gespeicherten Daten führt.

![Datenverschlüsselung in Aktion](./media/storsimple-security/DataEncryption.png)

Das asymmetrische Schlüsselsystem schützt die durch den Dienst geleiteten Daten wie folgt:

1. Ein Datenverschlüsselungszertifikat, das ein asymmetrisches öffentliches und privates Schlüsselpaar verwendet, wird auf dem Gerät generiert und dient zum Schutz der Daten. Die Schlüssel werden bei der Registrierung des ersten Geräts generiert. 
2. Die Zertifikatschlüssel für die Datenverschlüsselung werden in eine PFX-Datei (Personal Information Exchange) exportiert, die über den Verschlüsselungsschlüssel für Dienstdaten geschützt wird. Dies ist ein sicherer 128-Bit-Schlüssel, der vom ersten Gerät während der Registrierung zufällig generiert wird.
3. Der öffentliche Schlüssel des Zertifikats wird für den StorSimple-Manager-Dienst auf sichere Weise zur Verfügung gestellt. Der private Schlüssel verbleibt beim Gerät.
4. Die im Dienst eingehenden Daten werden mithilfe des öffentlichen Schlüssels verschlüsselt und mithilfe des privaten Schlüssels, der auf dem Gerät gespeichert wird, wieder entschlüsselt.

Der Verschlüsselungsschlüssel für Dienstdaten wird nur auf dem ersten Gerät generiert, das beim Dienst registriert wird. Alle nachfolgenden Geräte, die beim Dienst registriert werden, müssen denselben Verschlüsselungsschlüssel für Dienstdaten verwenden.

> [AZURE.IMPORTANT] 
> 
> Es ist sehr wichtig, dass Sie eine Kopie des Verschlüsselungsschlüssels für Dienstdaten erstellen und diese an einem sicheren Ort aufbewahren. Die Kopie des Verschlüsselungsschlüssels für Dienstdaten muss so gespeichert werden, dass autorisierte Personen darauf zugreifen und er problemlos dem Geräteadministrator mitgeteilt werden kann.
>
> Wenn der Verschlüsselungsschlüssel für Dienstdaten verloren geht, kann Ihnen ein Microsoft-Supportmitarbeiter helfen, ihn abzurufen, vorausgesetzt, mindestens ein Gerät ist online. Wir empfehlen, den Verschlüsselungsschlüssel für Dienstdaten zu ändern, nachdem dieser abgerufen wurde. Anweisungen hierzu finden Sie unter [Ändern des Verschlüsselungsschlüssels für Dienstdaten](storsimple-service-dashboard.md#change-the-service-data-encryption-key).

Sie können den Verschlüsselungsschlüssel für Dienstdaten und das entsprechende Datenverschlüsselungszertifikat ändern, indem Sie im Dashboard des Diensts die Option **Verschlüsselungsschlüssel für Dienstdaten ändern** auswählen. Um sicherzustellen, dass die Datensicherheit nicht gefährdet ist, müssen Sie ein physisches StorSimple-Gerät verwenden, um den Verschlüsselungsschlüssel für Dienstdaten zu ändern. Die Änderung der Verschlüsselungsschlüssel erfordert, dass alle Geräte mit dem neuen Schlüssel aktualisiert werden. Daher wird empfohlen, den Schlüssel zu ändern, wenn alle Geräte online sind. Wenn Geräte offline sind, können ihre Schlüssel zu einem anderen Zeitpunkt geändert werden. Die Geräte mit abgelaufenen Schlüsseln können weiterhin Sicherungen ausführen, aber Daten erst nach der Aktualisierung des Schlüssels wiederherstellen. Weitere Informationen finden Sie unter [Verwenden des StorSimple-Manager-Dienstdashboards](storsimple-service-dashboard.md).

Der Verschlüsselungsschlüssel für Dienstdaten und das Datenverschlüsselungszertifikat haben kein Ablaufdatum. Wir empfehlen jedoch, den Verschlüsselungsschlüssel für Dienstdaten jährlich zu ändern, um eine Gefährdung des Schlüssels zu vermeiden.

## Schutz gespeicherter Daten

Das StorSimple-Gerät verwaltet Daten, indem diese lokal und in der Cloud abhängig von der Häufigkeit ihrer Nutzung auf Ebenen gespeichert werden. Alle Hostcomputer, die mit dem Gerät verbunden sind, senden Daten an das Gerät, das die Daten dann ggf. in die Cloud verschiebt. Die Daten werden vom Gerät über das Internet sicher in die Cloud gesendet. Jedes Gerät verfügt über ein iSCSI-Ziel, das alle freigegebenen Volumes auf diesem Gerät zum Vorschein bringt. Alle Daten werden verschlüsselt, bevor sie zum Cloud-Speicher gesendet werden.

![Verschlüsselungsschlüssel für Cloudspeicher](./media/storsimple-security/CloudStorageEncryption.png)

Um die Sicherheit und Integrität von Daten sicherzustellen, die in die Cloud verschoben werden, gestattet es StorSimple, Verschlüsselungsschlüssel für Cloudspeicher wie folgt zu definieren:

- Sie geben bei der Erstellung eines Volumecontainers den Verschlüsselungsschlüssel für Cloud-Speicher an. Der Schlüssel kann nicht später geändert oder hinzugefügt werden. 
- Alle Volumes in einem Volumecontainer teilen sich denselben Verschlüsselungsschlüssel. Wenn Sie für ein bestimmtes Volume eine andere Form der Verschlüsselung wünschen, wird empfohlen, einen neuen Volumecontainer als Host für dieses Volume zu erstellen.
- Wenn Sie den Verschlüsselungsschlüssel für Cloud-Speicher im StorSimple-Manager-Dienst eingeben, wird der Schlüssel mit dem öffentlichen Teil des Verschlüsselungsschlüssels für Dienstdaten verschlüsselt und an das Gerät gesendet.
- Der Verschlüsselungsschlüssel für Cloud-Speicher wird nicht innerhalb des Diensts gespeichert und ist ausschließlich dem Gerät bekannt.
- Die Angabe eines Verschlüsselungsschlüssels für Cloud-Speicher ist optional. Sie können die auf dem Host verschlüsselten Daten zum Gerät senden.

### Zusätzliche bewährte Methoden für die Sicherheit

- Aufteilen des Datenverkehrs: Isolieren Sie den iSCSI-SAN-Datenverkehr vom Benutzerdatenverkehr in einem Unternehmens-LAN, indem Sie ein vollständig getrenntes Netzwerk bereitstellen und VLANs einsetzen, wenn eine physische Isolation nicht möglich ist. Ein dediziertes Netzwerk für die iSCSI-Speicherung garantiert die Sicherheit und Leistung Ihrer unternehmenskritischen Daten. Die Mischung von Speicher- und Benutzerdatenverkehr in einem Unternehmens-LAN wird nicht empfohlen und kann die Latenz erhöhen sowie zu Netzwerkfehlern führen.

- Zur Netzwerksicherheit auf Hostseite verwenden Sie Netzwerkschnittstellen, die TOE (TCP/IP Offload Engine) unterstützen. TOE reduziert die CPU-Auslastung, da TCP-Datenverkehr über den Netzwerkadapter verarbeitet wird.

## Schützen von Daten über Speicherkonten

Bei jedem Microsoft Azure-Abonnement kann mindestens ein Speicherkonto erstellt werden. (Ein Speicherkonto bietet einen eindeutigen Namespace für die Arbeit mit Daten, die in der Azure-Cloud gespeichert werden.) Der Zugriff auf ein Speicherkonto wird über die Abonnement- und Zugriffsschlüssel gesteuert, die dem Speicherkonto zugeordnet sind.

Wenn Sie ein Speicherkonto erstellen, generiert Microsoft Azure zwei 512-Bit-Speicherzugriffsschlüssel, von denen einer für die Authentifizierung verwendet wird, wenn das StorSimple-Gerät auf das Speicherkonto zugreift. Beachten Sie, dass nur einer dieser Schlüssel verwendet wird. Der andere Schlüssel dient als Reserve und gestattet Ihnen, die Schlüssel regelmäßig zu rotieren. Um die Schlüssel zu rotieren, aktivieren Sie den sekundären Schlüssel und löschen dann den primären Schlüssel. Sie können dann einen neuen Schlüssel erstellen, der während der nächsten Rotation verwendet werden kann. (Aus Sicherheitsgründen ist in vielen Datencentern eine Schlüsselrotation erforderlich.)

Es wird empfohlen, diese bewährten Methoden für die Schlüsselrotation zu befolgen:

- Sie sollten die Speicherkontoschlüssel regelmäßig rotieren, um sicherzustellen, dass keine nicht autorisierten Benutzer auf Ihr Speicherkonto zugreifen.
- Der Azure-Administrator sollte den primären oder sekundären Schlüssel regelmäßig über den Bereich „Storage“ des klassischen Azure-Portals neu generieren, um direkt auf das Speicherkonto zuzugreifen.


## Schützen von Daten durch Verschlüsselung

In der folgenden Tabelle werden die Verschlüsselungsalgorithmen beschrieben, die von der Azure StorSimple-Lösung zum Schutz von gespeicherten oder in der Übertragung befindlichen Daten verwendet werden.

| Algorithmus | Schlüssellänge | Protokolle/Anwendungen/Kommentare |
| --------- | ---------- | ------------------------------- |
| RSA | 2048 | RSA PKCS 1 v1.5 wird vom klassischen Azure-Portal verwendet, um Konfigurationsdaten zu verschlüsseln, die an das Gerät gesendet werden. Beispiel: Anmeldeinformationen für Speicherkonten, StorSimple-Gerätekonfiguration und Verschlüsselungsschlüssel für Cloudspeicher. |
| AES | 256 | AES mit CBC wird zum Verschlüsseln des öffentlichen Teils des Verschlüsselungsschlüssels für Dienstdaten verwendet, bevor dieser vom StorSimple-Gerät zum klassischen Azure-Portal gesendet wird. Er wird auch vom StorSimple-Gerät verwendet, um Daten zu verschlüsseln, bevor diese zum Cloud-Speicherkonto gesendet werden. |


## Sicherheit für virtuelle Geräte bei StorSimple

[AZURE.INCLUDE [Sicherheit für virtuelle Geräte bei StorSimple](../../includes/storsimple-virtual-device-security.md)]

## Häufig gestellte Fragen (FAQ)

Die folgenden Fragen und Antworten beziehen sich auf die Sicherheit und Microsoft Azure StorSimple.

**F:** Mein Dienst ist gefährdet. Was ist als Nächstes zu tun?

**A:** Sie sollten sofort den Verschlüsselungsschlüssel für Dienstdaten und die Speicherkontoschlüssel des Speicherkontos ändern, das für die Datenstaffelung verwendet wird. Anweisungen dazu finden Sie unter:

- [Ändern des Verschlüsselungsschlüssels für Dienstdaten](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [Schlüsselrotation von Speicherkonten](storsimple-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**F:** Ich habe ein neues StorSimple-Gerät, für das ich den Dienstregistrierungsschlüssel eingeben muss. Wie kann ich diesen abrufen?

**A:** Dieser Schlüssel wurde erstellt, als Sie den StorSimple Manager-Dienst erstmals angelegt haben. Wenn Sie eine Verbindung mit dem Gerät mithilfe des StorSimple-Manager-Diensts herstellen, können Sie den Dienstregistrierungsschlüssel auf der Seite "Schnellstart" des Diensts anzeigen oder erneut generieren. Das Generieren eines neuen Dienstregistrierungsschlüssels wirkt sich nicht auf die vorhandenen registrierten Geräte aus. Anweisungen dazu finden Sie unter:

- [Anzeigen oder Neugenerieren des Dienstregistrierungsschlüssels](storsimple-service-dashboard.md#view-or-regenerate-the-service-registration-key)

**F:** Ich habe meinen Verschlüsselungsschlüssel für Dienstdaten verloren. Wie gehe ich vor?

**A:** Wenden Sie sich an den Microsoft-Support. Der Support kann sich in einer Supportsitzung bei Ihrem Gerät anmelden und Ihnen helfen, den Schlüssel abzurufen (vorausgesetzt, dass mindestens ein Gerät online ist). Sie sollten den Verschlüsselungsschlüssel für Dienstdaten sofort nach Erhalt ändern, damit der neue Schlüssel nur Ihnen bekannt ist. Anweisungen dazu finden Sie unter:

- [Ändern des Verschlüsselungsschlüssels für Dienstdaten](storsimple-service-dashboard.md#change-the-service-data-encryption-key)

**F:** Ich habe ein Gerät für die Änderung des Verschlüsselungsschlüssels für Dienstdaten autorisiert, aber den Änderungsvorgang nicht gestartet. Wie sollte ich vorgehen?

**A:** Wenn der Timeoutzeitraum abgelaufen ist, müssen Sie das Gerät zur Änderung des Verschlüsselungsschlüssels für Dienstdaten erneut autorisieren und den Vorgang neu starten.

**F:** Ich habe den Verschlüsselungsschlüssel für Dienstdaten geändert, konnte die anderen Geräte aber nicht innerhalb von 4 Stunden aktualisieren. Muss ich jetzt von vorn beginnen?

**A:** Der Zeitraum von 4 Stunden bezieht sich nur auf das Einleiten der Änderung. Nachdem die Änderung auf dem autorisierten StorSimple-Gerät gestartet wurde, ist sie gültig, bis alle Geräte aktualisiert wurden.

**F:** Unser StorSimple-Administrator hat das Unternehmen verlassen. Wie sollte ich vorgehen?

**A:** Sie sollten die Kennwörter für den Zugriff auf das StorSimple-Gerät ändern und zurücksetzen und den Verschlüsselungsschlüssel für Dienstdaten ändern, um zu gewährleisten, dass die neuen Informationen unbefugten Dritten nicht bekannt sind. Anweisungen dazu finden Sie unter:

- [Verwenden des StorSimple Manager-Diensts, um StorSimple-Kennwörter zu ändern](storsimple-change-passwords.md)
- [Ändern des Verschlüsselungsschlüssels für Dienstdaten](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [Konfigurieren von CHAP für Ihr StorSimple-Gerät](storsimple-configure-chap.md)

**F:** Ich möchte das Kennwort für den StorSimple Snapshot Manager auf einem Host bereitstellen, der eine Verbindung mit dem StorSimple-Gerät herstellt, aber das Kennwort ist nicht verfügbar. Was kann ich tun?

**A:** Wenn Sie das Kennwort vergessen haben, müssen Sie ein neues erstellen. Stellen Sie anschließend sicher, dass Sie alle vorhandenen Benutzer über die Kennwortänderung sowie darüber informieren, dass sie ihre Clients aktualisieren müssen, damit diese das neue Kennwort verwenden. Anweisungen dazu finden Sie unter:

- [Ändern des StorSimple Snapshot Manager-Kennworts](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)
- [Authentifizieren eines Geräts](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**F:** Das Zertifikat für den Remotezugriff auf Windows PowerShell für StorSimple wurde auf dem Gerät geändert. Wie kann ich meine Remotezugriffsclients aktualisieren?

**A:** Sie können das neue Zertifikat aus dem StorSimple Manager-Dienst herunterladen und dann für die Installation im Zertifikatspeicher der Remotezugriffsclients bereitstellen. Anweisungen dazu finden Sie unter:

- [Cmdlet "Import-Certificate"](https://technet.microsoft.com/library/hh848630.aspx)

**F:** Sind meine Daten im Fall einer Gefährdung des StorSimple Manager-Diensts geschützt?

**A:** Die Dienstkonfigurationsdaten werden stets mit Ihrem öffentlichen Schlüssel verschlüsselt, wenn Sie diese in einem Webbrowser anzeigen. Da der Dienst keinen Zugriff auf den privaten Schlüssel hat, ist er nicht in der Lage, Daten offenzulegen. Die Gefährdung des StorSimple-Manager-Diensts hat keine weiteren Auswirkungen, da im StorSimple-Manager-Dienst keine Schlüssel gespeichert sind.

**F:** Sind meine Daten gefährdet, falls jemand an das Datenverschlüsselungszertifikat gelangt?

**A:** Der Datenverschlüsselungsschlüssel (PFX-Datei) von Kunden wird in Microsoft Azure in einem verschlüsselten Format gespeichert. Da die PFX-Datei verschlüsselt ist und der StorSimple-Dienst nicht über den Verschlüsselungsschlüssel für Dienstdaten verfügt und folglich die PFX-Datei nicht entschlüsseln kann, reicht der bloße Zugriff auf die PFX-Datei nicht aus, um vertrauliche Daten offenzulegen.

**F:** Was geschieht, wenn Microsoft von einer Behörde aufgefordert wird, meine Daten offenzulegen?

**A:** Da alle Daten im Dienst verschlüsselt werden und der private Schlüssel im Gerät verbleibt, muss die Behörde die Daten direkt vom Kunden anfordern.

## Nächste Schritte

[Bereitstellen von StorSimple-Geräten](storsimple-deployment-walkthrough.md).
 

<!---HONumber=AcomDC_0128_2016-->