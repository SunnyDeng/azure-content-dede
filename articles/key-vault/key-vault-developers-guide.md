<properties
   pageTitle="Entwicklerhandbuch zu Schlüsseltresor | Microsoft Azure"
   description="Mit Azure-Schlüsseltresor können Entwickler kryptografische Schlüssel in der Microsoft Azure-Umgebung verwalten."
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/19/2016"
   ms.author="bruceper" />

# Entwicklerhandbuch zu Azure-Schlüsseltresor

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

Als Entwickler können Sie mit Azure Key Vault kryptografische Schlüssel in der Microsoft Azure-Umgebung verwalten. Key Vault unterstützt mehrere Schlüsseltypen und Algorithmen und kann mit Hardwaresicherheitsmodulen (HSM) für hochwertige Schlüssel verwendet werden. Darüber hinaus können Sie Schlüsseltresor zum sicheren Speichern von geheimen Schlüsseln verwenden, bei denen es sich um Oktett-Objekte mit einer begrenzten Größe ohne bestimmte Semantik handelt. Die Verwaltung der Zugriffskontrolle für die Objekttypen erfolgt unabhängig voneinander.

Nach erfolgreicher Autorisierung können Sie folgende Aufgaben durchführen:

- Verwalten kryptografischer Schlüssel unter Verwendung von [Create](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Import](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Update](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Delete](https://msdn.microsoft.com/library/azure/dn903611.aspx) und anderer Befehle

- Verwalten geheimer Schlüssel unter Verwendung von [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Update](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Delete](https://msdn.microsoft.com/library/azure/dn903613.aspx) und anderen Befehlen

- Verwenden kryptografischer Schlüssel unter von [Sign](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verify](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) und [Encrypt](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Decrypt](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Vorgänge für Schlüsseltresore werden über Azure Active Directory authentifiziert und autorisiert.

## Programmierung für den Schlüsseltresor

Das Schlüsseltresore-Verwaltungssystem für Programmierer besteht aus mehreren Schnittstellen auf der Grundlage von REST. [Referenz für die Key Vault-REST-API](https://msdn.microsoft.com/library/azure/dn903609.aspx)

|[![.NET](./media/key-vault-developers-guide/net.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Dokumentation zum .NET SDK](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Dokumentation zum Node.js SDK](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[.NET SDK-Paket](https://azure.microsoft.com/de-DE/documentation/api/)|[Node.js SDK-Paket](https://www.npmjs.com/package/azure-keyvault)|

## Verwalten von Schlüsseltresoren

Azure Key Vault-Container (Tresore) können mithilfe von REST, PowerShell oder über die Befehlszeilenschnittstelle verwaltet werden, wie in den folgenden Artikeln beschrieben:

- [Erstellen und Verwalten von Schlüsseltresoren über REST](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Erstellen und Verwalten von Schlüsseltresoren mit PowerShell](key-vault-get-started.md)
- [Erstellen und Verwalten von Schlüsseltresoren über die Befehlszeilenschnittstelle](key-vault-manage-with-cli.md)


## Vorgehensweisen

Die folgenden Artikel bieten aufgabenspezifische Anleitungen:

- [Generieren und Übertragen von HSM-geschützten Schlüsseln für den Azure-Schlüsseltresor](key-vault-hsm-protected-keys.md)

## Beispiele

- Dieser Download enthält sowohl die Beispielanwendung „HelloKeyVault“ als auch ein Azure-Webdienstbeispiel. [Codebeispiele für Azure-Schlüsseltresor](http://www.microsoft.com/download/details.aspx?id=45343)
- In diesem Lernprogramm erfahren Sie, wie Sie den Azure-Schlüsseltresor aus einer Webanwendung in Azure verwenden. [Verwenden des Azure-Schlüsseltresors aus einer Webanwendung](key-vault-use-from-web-application.md)

## Unterstützung von Bibliotheken

- Die [Microsoft Azure-Schlüsseltresor-Kernbibliothek](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0) bietet IKey- und IKeyResolver-Schnittstellen, um Schlüssel von Bezeichnern zu ermitteln und mit diesen Schlüsseln Operationen auszuführen.

- [Microsoft Azure-Schlüsseltresorerweiterungen](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0) bieten erweiterte Funktionen für den Azure-Schlüsseltresor.

<!----HONumber=AcomDC_0121_2016-->