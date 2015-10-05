<properties
   pageTitle="Entwicklerhandbuch zu Schlüsseltresor | Microsoft Azure"
   description="Mit Azure-Schlüsseltresor können Entwickler kryptografische Schlüssel in der Microsoft Azure-Umgebung verwalten."
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/22/2015"
   ms.author="mbaldwin" />

# Entwicklerhandbuch zu Azure-Schlüsseltresor

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

Mit Azure-Schlüsseltresor können Entwickler kryptografische Schlüssel in der Microsoft Azure-Umgebung verwalten. Schlüsseltresor unterstützt mehrere Schlüsseltypen und Algorithmen und kann mit Hardwaresicherheitsmodulen (HSM) für hochwertige Kundenschlüssel verwendet werden. Darüber hinaus können Sie Schlüsseltresor zum sicheren Speichern von geheimen Schlüsseln verwenden, bei denen es sich um Oktett-Objekte mit einer begrenzten Größe ohne bestimmte Semantik handelt. Ein Schlüsseltresor kann eine Mischung aus Schlüsseln und geheimen Schlüsseln enthalten. Die Verwaltung der Zugriffskontrolle für die Objekttypen erfolgt unabhängig voneinander.

Benutzer können nach erfolgreicher Autorisierung Folgendes tun:

- Verwalten kryptografischer Schlüssel unter Verwendung von [Create](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Import](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Update](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Delete](https://msdn.microsoft.com/library/azure/dn903611.aspx) und anderer Befehle

- Verwalten geheimer Schlüssel unter Verwendung von [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Update](https://msdn.microsoft.com/library/azure/dn986818.aspx, [Delete](https://msdn.microsoft.com/library/azure/dn903613.aspx) und anderer Befehle

- Verwenden kryptografischer Schlüssel unter von [Sign](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verify](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) und [Encrypt](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Decrypt](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Vorgänge für Schlüsseltresore werden mithilfe von Azure Active Directory authentifiziert und autorisiert.

## Programmierung für den Schlüsseltresor

Das Schlüsseltresore-Verwaltungssystem für Programmierer besteht aus mehreren Schnittstellen auf der Grundlage von REST.

### REST

Die REST-API bildet die Grundlage für die gesamte programmgesteuerte Interaktion mit dem Schlüsseltresor.

Der Schlüsseltresor hat einen eigenen REST-Endpunkt, der in der [Referenz für die Schlüsseltresor-REST-API](https://msdn.microsoft.com/library/azure/dn903609.aspx) beschrieben wird.

### .NET

Die .NET API ist eine Gruppe von Wrappern, die eine Implementierung über das C#-Programmiermodell ohne direkte Interaktion mit dem REST-Endpunkt ermöglicht. Hier finden Sie die [Referenz zur .NET-Client-API für Azure-Schlüsseltresor](https://msdn.microsoft.com/library/azure/dn903301.aspx).

### Node.js

Die Node.js-API ist eine Gruppe von Wrappern, die eine Implementierung über das JavaScript-Programmiermodell ohne direkte Interaktion mit dem REST-Endpunkt ermöglicht. Hier finden Sie die [Schlüsseltresorverwaltung für das Microsoft Azure SDK für Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest/).

## Verwalten des Schlüsseltresors mit PowerShell und CLI

Schlüssel und geheime Schlüssel von Azure-Schlüsseltresor können auch mithilfe von PowerShell und über die Befehlszeilenschnittstelle verwaltet werden, wie in den folgenden Artikeln beschrieben:

- [Erstellen und Verwalten von Schlüsseltresoren mit PowerShell](key-vault-get-started.md)
- [Erstellen und Verwalten von Schlüsseltresoren über die Befehlszeilenschnittstelle](key-vault-manage-with-cli.md)
- [Generieren und Übertragen von HSM-geschützten Schlüsseln für den Azure-Schlüsseltresor](key-vault-hsm-protected-keys.md)
- [Informationen zu Schlüsseln und geheimen Schlüsseln](https://msdn.microsoft.com/library/azure/dn903623.aspx)

## Weitere Informationen

- [Codebeispiele für Azure-Schlüsseltresor](http://www.microsoft.com/download/details.aspx?id=45343)

<!---HONumber=Sept15_HO4-->