
<properties 
    pageTitle="Benutzerprofildaten in Azure RemoteApp | Microsoft Azure"
	description="Erfahren Sie, wie Benutzerdaten in Azure RemoteApp gespeichert und abgerufen werden können."
	services="remoteapp"
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/29/2015" 
    ms.author="elizapo" />



# Wie speichert Azure RemoteApp Benutzerdaten und -einstellungen?

Azure RemoteApp speichert die Benutzeridentität und -anpassungen über alle Geräte und Sitzungen hinweg. Diese Benutzerdaten werden auf einem benutzer- und sammlungsspezifischen Datenträger gespeichert, der sogenannte Benutzerprofil-Datenträger. Der Datenträger gilt für den Benutzer und stellt sicher, dass er eine einheitliche Umgebung erfährt, unabhängig davon, wo er sich anmeldet.

Benutzerprofil-Datenträger sind für den Benutzer vollständig transparent – Benutzer speichern Dokumente in ihrem Dokumentenordner (scheinbar auf einem lokalen Laufwerk), und ändern ihre App-Einstellungen wie gewohnt. Gleichzeitig bleiben alle persönlichen Einstellungen beim Verbinden mit Azure RemoteApp von jedem Gerät aus bestehen. Der Benutzer sieht lediglich seine Daten am selben Ort.

Jeder Benutzerprofil-Datenträger verfügt über 50 GB permanenten Speicher und enthält sowohl Benutzerdaten als auch Anwendungseinstellungen.

Lesen Sie weiter, um mehr über die Besonderheiten von Benutzerprofildaten zu erfahren.

## Wie kann ein Administrator auf die Daten zugreifen?

Wenn Sie Zugriff auf die Daten für einen Ihrer Benutzer benötigen (für die Wiederherstellung im Notfall oder wenn der Benutzer das Unternehmen verlässt), wenden Sie sich an den Support von [Azure RemoteApp](mailto:remoteappforum@microsoft.com), und stellen Sie die Abonnementinformationen für die Sammlung und die Identität des Benutzers bereit. Vom Azure RemoteApp-Team erhalten Sie eine URL, mit der Sie auf die Daten zugreifen können. Von dort aus können Sie zum Speicherort navigieren und alle benötigten Dokumente oder Dateien abrufen.


## Werden die Daten gesichert?

Ja, wir speichern einen Backup der Benutzerdaten je nach geografischem Standort. Die Daten sind schreibgeschützt und auf die gleiche Weise zugänglich wie die regulären Daten (kontaktieren Sie dafür das Azure RemoteApp-Team), wenn das primäre Rechenzentrum ausgefallen ist. Die Daten werden in Echtzeit in den Sicherungsspeicherort kopiert, und wir behalten keine Kopien der verschiedenen Versionen bei. Bei einer Beschädigung von Daten sind wir also nicht in der Lage, diese auf eine zuvor funktionierende Version wiederherzustellen. Wenn allerdings das primäre Rechenzentrum ausfällt, können Sie die Benutzerdaten von einem anderen Speicherort abrufen.

## Wie wird ein Benutzerprofil-Datenträger dem Benutzer auf der Serverseite angezeigt?

Jeder Benutzer hat sein eigenes Verzeichnis auf dem Server, das dem Benutzerprofil-Datenträger zugeordnet ist: c:\\Users\\username.

## Was ist die beste Möglichkeit, Outlook und den Benutzerprofil-Datenträger zu verwenden?

Azure RemoteApp speichert den Outlook-Zustand (Postfächer, PSTs) zwischen den Sitzungen. Um dies zu ermöglichen, müssen PSTs in den Benutzerprofildaten gespeichert werden (c:\\users<Benutzername>). Dies ist der Standardspeicherort für die Daten, und so lange dieser Speicherort nicht geändert wird, werden die Daten zwischen den Sitzungen beibehalten.

Außerdem wird empfohlen, dass Sie den "Cache"-Modus in Outlook und den "Server/online"-Modus für die Suche verwenden.

## Können Lösungen für freigegebene Daten genutzt werden?
Ja, Azure RemoteApp unterstützt Lösungen für freigegebene Daten, insbesondere OneDrive for Business und Dropbox. Beachten Sie jedoch, dass OneDrive Consumer (persönliche Version) und Box nicht unterstützt werden.

## Was ist mit Umleitungen?
Sie können konfigurieren, dass Azure RemoteApp Benutzern durch Einrichten einer [Umleitung](remoteapp-redirection.md) Zugriff auf lokale Geräte gewährt. Lokale Geräte können dann auf die Daten auf dem Benutzerprofil-Datenträger zugreifen.

## Kann ich meinen Benutzerprofil-Datenträger als Netzwerkfreigabe verwenden?
Nein. Benutzerprofil-Datenträger können nicht als Netzwerkfreigabe verwendet werden. Ein Benutzerprofil-Datenträger steht dem Bentuzer nur zur Verfügung, wenn er aktiv mit Azure RemoteApp verbunden ist.

## Wird beim Löschen eines Benutzers aus einer Sammlung dessen Benutzerprofil-Datenträger gelöscht?

Nein. Wenn Sie einen Benutzer löschen, wird der Benutzerprofil-Datenträger nicht automatisch gelöscht. Stattdessen speichern wir die Daten, bis Sie die Sammlung löschen. 90 Tage nach dem Löschen der Sammlung löschen wir alle Benutzerprofil-Datenträger.

Wenn Sie einen Benutzerprofil-Datenträger aus einer Sammlung löschen müssen, kontaktieren Sie das Azure RemoteApp-Team. Wir können den Datenträger von unserer Seite aus löschen.

## Kann ich auf die Benutzerprofil-Datenträger meiner Benutzer zugreifen (aktuelle oder gelöschte Benutzer)?

Ja, wenn Sie sich an das [Azure RemoteApp](mailto:remoteappforum@microsoft.com)-Team wenden, können wir eine URL für den Datenzugriff für Sie generieren. Sie haben etwa 10 Stunden Zeit, um Daten oder Dateien vom Benutzerprofil-Datenträger herunterzuladen, bis der Zugriff abläuft.

## Sind Benutzerprofil-Datenträger offline verfügbar?

Momentan bieten wir über das zehnstündige Zugriffsfenster hinaus keinen Offline-Zugriff auf Benutzerprofil-Datenträger. Dies bedeutet, dass wir derzeit keine Möglichkeit haben, Ihnen längeren Zugriff für komplexere Aufgaben wie dem Ausführen von Antivirus-Software auf Benutzerprofil-Datenträgern oder dem Zugriff auf die Daten für eine Überwachung zu gewähren.

## Werden Einstellungen für Registrierungsschlüssel beibehalten?
Ja. Alles, was in „HKEY\_Current\_User“ geschrieben ist, ist Teil des Benutzerprofil-Datenträgers.

## Kann ich Benutzerprofil-Datenträger für eine Sammlung deaktivieren?

Ja, Sie können das Azure RemoteApp-Team darum bitten, Benutzerprofil-Datenträger für ein Abonnement zu löschen. Sie können dies nicht selbst tun. Das bedeutet, dass Benutzerprofil-Datenträger für alle Sammlungen im Abonnement deaktiviert werden.

## Kann ich verhindern, dass Benutzer Daten auf dem Systemlaufwerk speichern?

Ja, aber Sie müssen dies im Vorlagenimage einrichten, bevor Sie die Sammlung erstellen. Führen Sie die folgenden Schritte aus, um den Zugriff auf das Systemlaufwerk zu blockieren:

1. Führen Sie **gpedit.msc** auf dem Vorlagenimage aus.
2. Navigieren Sie zu **Benutzerkonfiguration > Administrative Vorlagen > Windows-Komponenten > Explorer**.
3. Wählen Sie die folgenden Optionen:
	- **Ausblenden angegebener Datenträger vom Arbeitsplatz**
	- **Verhindern des Zugriffs auf Laufwerke vom Arbeitsplatz**

## Kann bei Benutzerprofil-Datenträgern Seeding ausgeführt werden? Ich möchte dem Benutzerprofil-Datenträger einige Daten hinzufügen, die dem Benutzer zur Verfügung stehen, wenn er sich zum ersten Mal anmeldet.

Ja, wenn Sie das Vorlagenimage erstellen, können Sie Informationen zum Standardprofil hinzufügen. Diese Informationen werden dann zum Benutzerprofil-Datenträger hinzugefügt.

## Kann ich die Größe des Benutzerprofil-Datenträgers je nach gespeicherter Datenmenge ändern?

Nein, alle Benutzerprofil-Datenträger verfügen über 50 GB Speicher. Wenn Sie unterschiedliche Mengen an Daten speichern möchten, versuchen Sie Folgendes:

1. Deaktivieren Sie den Benutzerprofil-Datenträger für die Sammlung.
2. Richten Sie eine Dateifreigabe ein, damit Benutzer Zugriff erhalten.
3. Laden Sie die Dateifreigabe mithilfe eines Skripts zum Starten. Informationen zu Skripts zum Starten in Azure RemoteApp finden Sie weiter unten.
4. Weisen Sie die Benutzer an, alle Daten in der Dateifreigabe zu speichern.

Sie können auch Apps zur Datensynchronisierung wie OneDrive for Business verwenden.

## Wie führe ich ein Startskript in Azure RemoteApp aus?

Wenn Sie ein Startskript ausführen möchten, beginnen Sie mit dem Erstellen einer geplanten Aufgabe im Vorlagenimage, das Sie für die Sammlung verwenden möchten. (Tun Sie dies, *bevor* Sie "Sysprep" ausführen.)

![Erstellen Sie eine Systemaufgabe.](./media/remoteapp-upd/upd1.png)

![Erstellen Sie eine Systemaufgabe, die ausgeführt wird, wenn sich ein Benutzer anmeldet.](./media/remoteapp-upd/upd2.png)

Die geplante Aufgabe wird mit den Anmeldeinformationen des Benutzers Ihr Startskript starten. Planen Sie die Ausführung der Aufgabe jedes Mal, wenn sich ein Benutzer anmeldet.

![Legen Sie den Trigger für die Aufgabe auf "Bei Anmeldung" fest.](./media/remoteapp-upd/upd3.png)

Sie können auch [auf Gruppenrichtlinien basierte Startskripts](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx) verwenden.

## Kann das Startskript auch im Startmenü platziert werden? Funktioniert das?

Mit anderen Worten, kann ich eine BAT-Datei erstellen, die ein Config-Fenster-Skript ausführt, es im Ordner "c:\\ProgramData\\Microsoft\\Windows\\Start" speichern, und das Skript dann immer dann ausführen lassen, wenn ein Benutzer eine RemoteApp-Sitzung startet?

Nein, dies wird von Azure RemoteApp nicht unterstützt, da der RDSH genutzt wird, der Startskripts im Startmenü ebensowenig unterstützt.

## Kann ich "mstsc.exe" (das Remotedesktop-Programm) zum Konfigurieren von Anmeldeskripts verwenden?

Nein, dies wird von Azure RemoteApp nicht unterstützt.

<!---HONumber=Oct15_HO3-->