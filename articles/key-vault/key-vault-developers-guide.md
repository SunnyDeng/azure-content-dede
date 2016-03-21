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
   ms.date="03/07/2016"
   ms.author="bruceper" />

# Entwicklerhandbuch zu Azure-Schlüsseltresor

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

Mithilfe des Schlüsseltresors können Sie in Ihren Anwendungen sicher auf vertrauliche Daten zugreifen:

- Schlüssel und geheime Schlüssel werden geschützt, ohne dass Sie den Code selbst schreiben müssen, und können auf einfache Weise in Ihren Anwendungen verwendet werden.
- Ihre Kunden können die Verantwortung für ihre eigenen Schlüssel übernehmen und diese selbst verwalten, sodass Sie sich auf die Bereitstellung der Hauptfunktionen der Software konzentrieren können. Auf diese Weise müssen Ihre Anwendungen weder die Verantwortung noch die Haftung für die Mandantenschlüssel und geheimen Mandantenschlüssel Ihrer Kunden übernehmen.
- Ihre Anwendung kann Signatur- und Verschlüsselungsschlüssel verwenden, wobei die Schlüssel außerhalb der Anwendung verwaltet werden, damit die Lösung auch für eine geografisch verteilte Anwendung geeignet ist.

Allgemeine Informationen zu Azure Key Vault finden Sie unter [Was ist der Schlüsseltresor?](key-vault-whatis.md).

## Erstellen und Verwalten von Schlüsseltresoren

Bevor Sie mit Azure Key Vault in Ihrem Code arbeiten, können Sie Tresore über REST, Resource Manager-Vorlagen, PowerShell oder die Befehlszeilenschnittstelle (CLI) erstellen und verwalten, wie in den folgenden Artikeln beschrieben:

- [Erstellen und Verwalten von Schlüsseltresoren über REST](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Erstellen und Verwalten von Schlüsseltresoren mit PowerShell](key-vault-get-started.md)
- [Erstellen und Verwalten von Schlüsseltresoren über die Befehlszeilenschnittstelle](key-vault-manage-with-cli.md)
- [Erstellen eines Schlüsseltresors und Hinzufügen eines geheimen Schlüssels über eine ARM-Vorlage](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] Vorgänge für Schlüsseltresore werden über AAD authentifiziert und über eine eigene Zugriffsrichtlinie des Schlüsseltresors autorisiert, die pro Tresor definiert wird.

## Programmieren mit dem Schlüsseltresor

Das Schlüsseltresor-Verwaltungssystem für Programmierer besteht aus mehreren Schnittstellen auf der Grundlage von REST: [Key Vault REST API Reference](https://msdn.microsoft.com/library/azure/dn903609.aspx) (Referenz für die Key Vault-REST-API).

Nach erfolgreicher Autorisierung können Sie folgende Aufgaben ausführen:

- Verwalten kryptografischer Schlüssel unter Verwendung von [Create](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Import](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Update](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Delete](https://msdn.microsoft.com/library/azure/dn903611.aspx) und anderer Befehle

- Verwalten geheimer Schlüssel unter Verwendung von [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Update](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Delete](https://msdn.microsoft.com/library/azure/dn903613.aspx) und anderen Befehlen

- Verwenden kryptografischer Schlüssel unter von [Sign](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verify](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) und [Encrypt](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Decrypt](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Die folgenden SDKs sind für die Arbeit mit dem Schlüsseltresor verfügbar:

|[![.NET](./media/key-vault-developers-guide/net.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Dokumentation zum .NET SDK](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Dokumentation zum Node.js SDK](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[.NET SDK-Paket](https://azure.microsoft.com/documentation/api/)|[Node.js SDK-Paket](https://www.npmjs.com/package/azure-keyvault)|


Vollständige Beispiele für die Verwendung des Schlüsseltresors mit Ihren Anwendungen finden Sie hier:

- .NET-Beispielanwendung *HelloKeyVault* und ein Azure-Webdienstbeispiel. [Codebeispiele für Azure-Schlüsseltresor](http://www.microsoft.com/download/details.aspx?id=45343)
- Tutorial zur Verwendung von Azure Key Vault aus einer Webanwendung in Azure. [Verwenden des Azure-Schlüsseltresors aus einer Webanwendung](key-vault-use-from-web-application.md)

## Vorgehensweisen

Die folgenden Artikel und Szenarios bieten aufgabenspezifische Anleitungen:

- [Generieren und Übertragen von HSM-geschützten Schlüsseln für den Azure-Schlüsseltresor](key-vault-hsm-protected-keys.md)
- [Übergeben sicherer Werte (z. B. Kennwörter) während der Bereitstellung](../resource-manager-keyvault-parameter.md).
- Aufgabenspezifische Anleitungen zum Integrieren und Verwenden von Schlüsseltresoren mit Azure finden Sie unter [Ryan Jones ARM template examples for Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples) (ARM-Vorlagenbeispiele von Ryan Jones für den Schlüsseltresor).

## Unterstützung von Bibliotheken

- Die [Microsoft Azure Key Vault-Kernbibliothek](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0) bietet `IKey`- und `IKeyResolver`-Schnittstellen, um Schlüssel von Bezeichnern zu ermitteln und mit diesen Schlüsseln Vorgänge auszuführen.

- [Microsoft Azure-Schlüsseltresorerweiterungen](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0) bieten erweiterte Funktionen für den Azure-Schlüsseltresor.

<!---HONumber=AcomDC_0309_2016-->