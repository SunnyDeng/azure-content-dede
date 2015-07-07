<properties 
	pageTitle="Clientseitige Verschlüsselung für Microsoft Azure Storage | Microsoft Azure" 
	description="Die Azure Storage-Clientvorschaubibliothek für .NET bietet Unterstützung für die clientseitige Verschlüsselung und die Integration in Azure Key Vault. Die clientseitige Verschlüsselung bietet maximale Sicherheit für Ihre Azure Storage-Anwendungen, da Ihre Zugriffsschlüssel im Dienst niemals verfügbar sind. Die clientseitige Verschlüsselung ist für Blobs, Warteschlangen und Tabellen verfügbar." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/10/2015" 
	ms.author="tamram"/>


# Clientseitige Verschlüsselung für Microsoft Azure Storage (Vorschau)

## Übersicht

Willkommen bei der [Vorschau der neuen Azure Storage-Clientbibliothek für .NET](https://www.nuget.org/packages/WindowsAzure.Storage/4.4.1-preview). Diese Vorschaubibliothek enthält neue Funktionen, mit denen Entwickler Daten in Clientanwendungen vor dem Hochladen in Azure Storage verschlüsseln und beim Herunterladen entschlüsseln können. Die Vorschaubibliothek unterstützt außerdem die Integration in Azure [Key Vault](http://azure.microsoft.com/services/key-vault/) für die Schlüsselverwaltung von Speicherkonten.

## Gründe für die Verwendung der clientseitigen Verschlüsselung

Die clientseitige Verschlüsselung bietet einen erheblichen Vorteil gegenüber der serverseitigen Verschlüsselung: Sie haben die volle Kontrolle über Ihre Kontozugriffsschlüssel. Die clientseitige Verschlüsselung bietet maximale Sicherheit für Ihre Anwendungen, da Ihre Schlüssel in Azure Storage niemals sichtbar sind und Ihre Daten nie entschlüsselt werden können. Die Vorschaubibliothek ist unter [GitHub](https://github.com/Azure/azure-storage-net/tree/preview) frei verfügbar, sodass Sie sehen können, wie die Bibliothek Ihre Daten verschlüsselt, um sicherzustellen, dass Ihre Standards erfüllt werden.

## Gründe für die Bereitstellung einer Bibliothek mit Unterstützung der clientseitigen Verschlüsselung

Jeder Entwickler kann zwar seine Daten vor dem Hochladen auf dem Client verschlüsseln, dies erfordert aber Fachkenntnisse in der Verschlüsselung. Außerdem müssen Leistung und Sicherheit berücksichtigt werden. Mehrere Entwickler müssten ihre eigene Verschlüsselungslösung entwerfen. Die Lösungen würden sich jedoch unterscheiden und könnten nicht zusammen eingesetzt werden.

Die Vorschaubibliothek wurde zu folgenden Zwecken konzipiert:

- Implementierung bewährter Sicherheitsmethoden für Sie.
- Maximierung der Leistung.
- Einfache Handhabung häufig verwendeter Szenarios.
- Unterstützung der Interoperabilität zwischen verschiedenen Sprachen. Die mithilfe der .NET-Clientbibliothek verschlüsselten Daten können in Zukunft von Clientbibliotheken für andere unterstützte Sprachen entschlüsselt werden, z. B. für Java, Node.js und C++ (und umgekehrt).

## Welche Funktionalität ist jetzt verfügbar?

Die Vorschaubibliothek unterstützt derzeit die Verschlüsselung für Blobs, Tabellen und Warteschlangen unter Verwendung des Umschlagverfahrens. Die Verschlüsselung und Entschlüsselung mit asymmetrischen Schlüsseln ist rechenintensiv. Aus diesem Grund werden beim Umschlagverfahren die eigentlichen Daten nicht direkt mit solchen Schlüsseln, sondern stattdessen mit einem zufälligen symmetrischen Schlüssel verschlüsselt. Dieser symmetrische Schlüssel wird dann mit einem öffentlichen Schlüssel verschlüsselt. Über die Unterstützung für Azure Key Vault können Sie Ihre Schlüssel effizient verwalten.

Die Verwendung der clientseitigen Verschlüsselung ist einfach. Sie können Anforderungsoptionen mit der entsprechenden Verschlüsselungsrichtlinie (Blob, Warteschlange oder Tabelle) angeben und an die APIs zum Hoch- und Herunterladen von Daten übergeben. Die Clientbibliothek verschlüsselt die Daten automatisch auf dem Client, wenn sie in Azure Storage hochgeladen werden, und entschlüsselt sie, wenn sie abgerufen werden. Ausführliche Informationen und Codebeispiele finden Sie im Blogbeitrag [Getting Started with Client-Side Encryption for Microsoft Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx) (in englischer Sprache).

Weitere Details zur clientseitigen Verschlüsselung:

- **Sicherheit**: Die verschlüsselten Daten können nicht gelesen werden, selbst wenn die Speicherkontoschlüssel von Kunden gefährdet sind.
- **Feste Overhead-Verschlüsselung**: Die verschlüsselten Daten verfügen über eine vorhersagbare Größe, die auf der ursprünglichen Größe basiert.
- **Eigenständige Verschlüsselung**: Jeder Blob, jede Tabellenentität und jede Warteschlangenmeldung speichert alle Verschlüsselungsinformationen entweder im Objekt selbst oder in seinen Metadaten. Als einziger externer Wert ist lediglich Ihr Verschlüsselungsschlüssel erforderlich.
- **Schlüsselrotation**: Benutzer können selbst die Schlüssel rotieren. Zudem werden bei der Schlüsselrotation mehrere Schlüssel unterstützt.
- **Sauberer Upgradepfad**: In Zukunft werden weitere Verschlüsselungsalgorithmen und Protokollversionen unterstützt, ohne dass wesentliche Änderungen am Code vorgenommen werden müssen.
- **Unterstützung von Blob-Verschlüsselung**:
	- **Upload des vollständigen Blobs**: Sie können Dateien wie z. B. Dokumente, Fotos und Videos in ihrer Gesamtheit verschlüsseln und hochladen.
	- **Download des vollständigen oder bereichsbasierten Blobs**: Sie können einen Blob in seiner Gesamtheit oder in Bereichen herunterladen und entschlüsseln.


>[AZURE.IMPORTANT]Beachten Sie bei Verwendung der Vorschaubibliothek die folgenden wichtigen Punkte:
>
>- Verwenden Sie die Vorschaubibliothek nicht für Produktionsdaten. In Zukunft wirken sich Änderungen an der Bibliothek auf die verwendeten Schemas aus. Die Entschlüsselung von Daten, die mit der Vorschaubibliothek verschlüsselt wurden, wird in zukünftigen Versionen nicht gewährleistet.  
>- Verwenden Sie beim Lesen aus einem verschlüsselten Blob oder beim Schreiben in diesen Befehle zum Hochladen des vollständigen Blobs und zum Herunterladen des bereichsbasierten oder vollständigen Blobs. Vermeiden Sie beim Schreiben in einen verschlüsselten Blob Protokollvorgänge wie z. B. "Put Block", "Put Block List", "Write Pages" oder "Clear Pages". Andernfalls wird der verschlüsselte Blob möglicherweise beschädigt und kann nicht mehr gelesen werden.
>- Für Tabellen gilt eine ähnliche Einschränkung. Achten Sie darauf, dass Sie beim Aktualisieren verschlüsselter Eigenschaften auch die Verschlüsselungsmetadaten aktualisieren.
>- Wenn Sie Metadaten für den verschlüsselten Blob festlegen, werden die für die Entschlüsselung erforderlichen verschlüsselungsbezogenen Metadaten möglicherweise überschrieben, da das Festlegen von Metadaten kein additiver Vorgang ist. Dies gilt auch für Momentaufnahmen: Geben Sie während der Erstellung einer Momentaufnahme eines verschlüsselten Blobs keine Metadaten an.

## Weitere Informationen

- [Getting Started with Client-Side Encryption for Microsoft Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/29/getting-started-with-client-side-encryption-for-microsoft-azure-storage.aspx) (in englischer Sprache)  
- [Azure Storage Client Library for .NET NuGet package (Preview)](http://www.nuget.org/packages/WindowsAzure.Storage/4.4.0-preview) (in englischer Sprache)  
- [Azure Storage Client Library for .NET Source Code (Preview)](https://github.com/Azure/azure-storage-net/tree/preview) (in englischer Sprache)
 

<!---HONumber=62-->