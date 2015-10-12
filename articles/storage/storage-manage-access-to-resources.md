<properties 
	pageTitle="Verwalten des anonymen Zugriffs auf Container und Blobs | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Container und Blobs für den anonymen Zugriff verfügbar machen können." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="jdial" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/28/2015" 
	ms.author="micurd;tamram"/>

# Verwalten des Zugriffs auf Azure Storage-Ressourcen

## Übersicht

Standardmäßig kann nur der Besitzer eines Speicherkontos auf Speicherressourcen in diesem Konto zugreifen. Wenn Ihr Dienst oder Ihre Anwendung diese Ressourcen für andere Clients zur Verfügung stellen muss, ohne den Zugriffsschlüssel freizugeben, stehen folgende Optionen zum Ermöglichen des Zugriffs zur Verfügung:

- Sie können die Berechtigungen eines Containers festlegen, um anonymen Lesezugriff auf den Container und seine Blobs zuzulassen. Der anonyme Lesezugriff ist nur für Container und Blobs verfügbar. 

- Sie können eine Ressource über eine SAS (Shared Access Signature, Signatur für freigegebenen Zugriff) verfügbar machen, die es Ihnen ermöglicht, beschränkten Zugriff auf einen Container, einen Blob, eine Tabelle, eine Warteschlange, eine Dateifreigabe oder eine Datei zu delegieren, indem Sie das Intervall, für das die Ressourcen verfügbar sind, und die Berechtigungen, die ein Client haben muss, angeben.

- Sie können eine gespeicherte Zugriffsrichtlinie verwenden, um Shared Access Signatures für einen Container oder dessen Blobs, für eine Warteschlange, für eine Tabelle oder für eine Dateifreigabe oder dessen Dateien zu verwalten. Über die gespeicherte Zugriffsrichtlinie haben Sie zusätzliche Kontrolle über die Shared Access Signatures und zudem eine direkte Möglichkeit, diese aufzuheben.

## Erteilen von anonymen Benutzerberechtigungen für Container und Blobs

Standardmäßig kann nur der Besitzer eines Speicherkontos auf einen Container und alle darin enthaltenen Blobs zugreifen. Wenn anonyme Benutzer Leseberechtigungen für einen Container und die enthaltenen Blobs erhalten sollen, können Sie den öffentlichen Zugriff durch Festlegen der Containerberechtigungen gestatten. Anonyme Benutzer können Blobs innerhalb eines öffentlich zugänglichen Containers lesen, ohne dass die Anforderung authentifiziert werden muss.

Container stellen die folgenden Optionen zum Verwalten des Containerzugriffs bereit:

- **Vollständiger öffentlicher Lesezugriff:** Container- und Blobdaten können über anonyme Anforderungen gelesen werden. Clients können Blobs innerhalb des Containers über eine anonyme Anforderung aufzählen, können aber keine Container innerhalb des Speicherkontos aufzählen.

- **Öffentlicher Lesezugriff nur für Blobs:** Blobdaten innerhalb dieses Containers können über anonyme Anforderungen gelesen werden, Containerdaten sind aber nicht verfügbar. Clients können keine Blobs innerhalb des Containers über anonyme Anforderungen aufzählen.

- **Kein öffentlicher Lesezugriff:** Container- und Blobdaten können nur vom Kontobesitzer gelesen werden.

>[AZURE.NOTE]Wenn Sie für Ihren Dienst eine genauere Kontrolle über die Blob-Ressourcen benötigen, oder wenn Sie Berechtigungen für andere Vorgänge als Lesevorgänge angeben möchten, verwenden Sie eine SAS, um eine Ressource für Benutzer zugänglich zu machen.

## Für anonyme Benutzer verfügbare Funktionen

Die folgende Tabelle zeigt, welche Vorgänge von anonymen Benutzern aufgerufen werden können, wenn der öffentliche Zugriff in der Zugriffssteuerungsliste (Access Control List, ACL) eines Containers gestattet wurde.

| REST-Vorgang | Berechtigung mit vollständigem öffentlichen Lesezugriff | Berechtigung mit öffentlichem Lesezugriff nur für Blobs |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| List Containers | Nur Besitzer | Nur Besitzer |
| Create Container | Nur Besitzer | Nur Besitzer |
| Get Container Properties | Alle | Nur Besitzer |
| Get Container Metadata | Alle | Nur Besitzer |
| Set Container Metadata | Nur Besitzer | Nur Besitzer |
| Get Container ACL | Nur Besitzer | Nur Besitzer |
| Set Container ACL | Nur Besitzer | Nur Besitzer |
| Delete Container | Nur Besitzer | Nur Besitzer |
| List Blobs | Alle | Nur Besitzer |
| Put Blob | Nur Besitzer | Nur Besitzer |
| Get Blob | Alle | Alle |
| Get Blob Properties | Alle | Alle |
| Set Blob Properties | Nur Besitzer | Nur Besitzer |
| Get Blob Metadata | Alle | Alle |
| Set Blob Metadata | Nur Besitzer | Nur Besitzer |
| Put Block | Nur Besitzer | Nur Besitzer |
| Get Block List (nur Blöcke mit ausgeführtem Commit) | Alle | Alle |
| Get Block List (nur Blöcke ohne ausgeführten Commit oder alle Blöcke) | Nur Besitzer | Nur Besitzer |
| Put Block List | Nur Besitzer | Nur Besitzer |
| Delete Blob | Nur Besitzer | Nur Besitzer |
| Kopieren von Blobs | Nur Besitzer | Nur Besitzer |
| Snapshot Blob | Nur Besitzer | Nur Besitzer |
| Lease Blob | Nur Besitzer | Nur Besitzer |
| Put Page | Nur Besitzer | Nur Besitzer |
| Get Page Ranges | Alle | Alle |


## Weitere Informationen

- [Authentifizierung für Azure Storage-Dienste](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [Shared Access Signatures: Grundlagen zum SAS-Modell](storage-dotnet-shared-access-signature-part-1.md)
- [Delegieren des Zugriffs mit einer Shared Access Signature](https://msdn.microsoft.com/library/azure/ee395415.aspx) 

<!---HONumber=Oct15_HO1-->