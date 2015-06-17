<properties 
   pageTitle="Erstellen eines B2B-Prozesses in Microsoft Azure App Service" 
   description="Übersicht über die Erstellung eines Business-to-Business-Prozesses" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="harishkragarwal" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/22/2015"
   ms.author="hariag"/>


# Erstellen eines B2B-Prozesses


## Geschäftsszenario 
Contoso und Northwind sind Geschäftspartner. Contoso (der Einzelhändler) sendet Bestellungen über einen branchenüblichen Transportweg wie AS2 an Northwind (den Lieferanten). Bei Northwind werden alle eingehenden Bestellungen im eigenen Cloud-Speicher gespeichert. Die Bestellungen sind XML-Nachrichten zwischen diesen beiden Partnern. Sobald die Nachricht im Cloud-Speicher von Northwind gespeichert ist, wird die Bestellung ab diesem Punkt mithilfe interner Prozesse bei Northwind verarbeitet.
 
Ziel dieses Lernprogramms ist das Vermitteln, wie Northwind einen Geschäftsprozess einrichten kann, über den das Unternehmen Nachrichten (Bestellungen im XML-Format) von seinem Partner Contoso über AS2 empfangen und danach im eigenen Cloud-Speicher beständig speichern kann.


## Veranschaulichte Funktionen 
In diesem Lernprogramm werden die folgenden Funktionen veranschaulicht:

- **Nachrichtentransport**: Einzelhändler und Lieferant können sich auf verschiedenen Plattformen befinden, aber dennoch eine Kommunikation einrichten. In diesem Lernprogramm kommunizieren sie über AS2 (Applicability Statement 2). AS2 ist eine beliebte Möglichkeit zur Übertragung von Daten zwischen Handelspartnern bei der Business-to-Business-Kommunikation.
- **Datenpersistenz:** Nachdem die Nachricht über AS2 empfangen wurde, möchte Northwind sie vor der weiteren Verarbeitung beständig speichern. Mithilfe eines Connectors können Nachrichten beständig im eigenen Cloud-Speicher gespeichert werden. In diesem Lernprogramm werden Azure-Blobs als Cloud-Speicher für Northwind genutzt.
- **Erstellen eines Geschäftsprozesses**: Zum Errichten einer Lösung können (wie hier gezeigt) mehrere API-Anwendungen in einem Datenfluss zusammengefügt werden.


## Voraussetzungen
In diesem Lernprogramm wird davon ausgegangen, dass Sie ein grundlegendes Verständnis von Azure App Services haben und wissen, wie API-Apps erstellt und in einem Datenfluss zusammengefügt werden.


## Schritte zum Realisieren des Geschäftsszenarios
**Erstellen und Konfigurieren der erforderlichen API-Apps**

1. Erstellen Sie eine Instanz des **Azure Storage-Blobconnectors**. Dies erfordert die Anmeldeinformationen für ein Azure Storage-Konto. Stellen Sie sicher, dass es bereit ist, bevor Sie beginnen, dieses Szenario zu erstellen.
2. Erstellen Sie eine Instanz der **BizTalk-Handelspartnerverwaltung**. Hierfür ist eine leere SQL-Datenbank erforderlich. Stellen Sie sicher, dass sie bereit ist, bevor Sie beginnen, diese Instanz zu erstellen.
3. Erstellen Sie eine Instanz des **AS2-Connectors**. Hierfür ist auch eine leere SQL-Datenbank erforderlich. Stellen Sie sicher, dass sie bereit ist, bevor Sie beginnen, diese Instanz zu erstellen. Wenn Sie außerdem Nachrichten im Rahmen der AS2-Verarbeitung archivieren möchten, können Sie Anmeldeinformationen für ein Azure-Blob bei dessen Erstellung angeben.
4. Konfigurieren Sie den TPM-Dienst (Trading Partner Management, Handelspartnerverwaltung), der erstellt wird:
	1. Navigieren Sie zur Instanz des TPM-Diensts, die als Teil der oben genannten Schritte erstellt wird.
	2. Verwenden Sie die Option **Partner** unter *Komponenten* zum **Hinzufügen** eines neuen Partners mit dem Namen **Contoso**, und fügen Sie die erforderliche AS2-Identität in seinem Profil hinzu.
	3. Verwenden Sie die Option **Partner** unter *Komponenten* zum **Hinzufügen** eines neuen Partners mit dem Namen **Northwind**, und fügen Sie die erforderliche AS2-Identität in seinem Profil hinzu.
	4. Verwenden Sie die Option **Vereinbarungen** unter *Komponenten* zum **Hinzufügen** einer neuen AS2-Vereinbarung zwischen Northwind und Contoso. Northwind ist hierbei der gehostete Partner, Contoso der Gastpartner. Konfigurieren Sie nach Bedarf während der Erstellung dieser Vereinbarung die Signatur, Verschlüsselung, Komprimierung und Bestätigungen. Für den Fall, dass Zertifikate verwendet werden müssen, können sie über die Option **Zertifikate** hochgeladen werden, wenn zum TPM-Dienst, der erstellt wird, navigiert wird.


## Erstellen eines Datenflusses/Geschäftsprozesses
1. Erstellen Sie einen neuen Datenfluss, in dem AS2 der erste Schritt ist. Sie müssen den **AS2-Connector** ziehen und ablegen und dann die bereits erstellte Instanz wählen. Wählen Sie "Trigger" als Funktion aus.

![][1]

2. Sie müssen als Nächstes den **Azure Storage-Blobconnector** ziehen und ablegen und dann die bereits erstellte Instanz wählen. Wählen Sie "Aktion" als Funktion und dann "Blob hochladen" als gewünschte Funktionalität aus. Konfigurieren Sie nach Bedarf.

3. Dann müssen Sie den Datenfluss erstellen bzw. bereitstellen.


## Nachrichtenverarbeitung und Fehlerbehebung
1. Nun ist es Zeit, den Datenfluss zu testen, den wir bereitgestellt haben. Senden Sie von AS2 umschlossene XML-Nachrichten (gemäß der zuvor erstellten AS2-Vereinbarung) an den AS2-Endpunkt, der von der erstellten AS2-Connector-Instanz verfügbar gemacht wurde. Sie müssen möglicherweise die Authentifizierung für den Endpunkt so konfigurieren, dass er öffentlich zugänglich ist.
2. Informationen zur Ausführung des Datenflusses werden verfügbar gemacht, indem Sie zum Datenfluss navigieren und die ausgeführte Datenflussinstanz schrittweise durchlaufen.
3. AS2-Verarbeitungsinformationen erhalten Sie, indem Sie zur beteiligten AS2-Connector-Instanz navigieren und den Teil "Nachverfolgung" schrittweise durchlaufen. Sie können die verfügbaren Filter nutzen, um die Ansicht auf die gewünschten Informationen zu beschränken.

![][2]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-b2b-process/Flow.jpg
[2]: ./media/app-service-logic-create-a-b2b-process/Tracking.jpg
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->