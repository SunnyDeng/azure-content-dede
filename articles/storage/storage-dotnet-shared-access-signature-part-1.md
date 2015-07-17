<properties 
	pageTitle="Shared Access Signatures: Grundlagen zum SAS-Modell | Microsoft Azure" 
	description="Erfahren Sie mehr über das Delegieren des Zugriffs auf Blob-, Warteschlangen- und Tabellenressourcen mit Shared Access Signatures" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="tamram"/>



# Shared Access Signatures, Teil 1: Grundlagen zum SAS-Modell

## Übersicht

Shared Access Signatures (SAS) sind eine leistungsstarke Methode, um anderen Clients eingeschränkten Zugriff auf Blobs, Tabellen und Warteschlangen in Ihrem Speicherkonto zu bieten, ohne Ihren Kontoschlüssel weitergeben zu müssen. Teil 1 dieses Lernprogramms zu Shared Access Signatures bietet eine Übersicht über das SAS-Modell und bewährte Methoden für SAS. [Teil 2](storage-dotnet-shared-access-signature-part-2.md) des Lernprogramms beschreibt die Erstellung von Shared Access Signatures mit dem Blob-Dienst.

## Was ist eine Shared Access Signature? ##

Shared Access Signatures bieten delegierten Zugriff auf Ressourcen in Ihren Speicherkonto. Sie haben die Möglichkeit, einem Client für einen bestimmten Zeitraum spezielle eingeschränkte Berechtigungen für Ihre Blobs, Warteschlangen oder Tabellen erteilen, ohne Ihre Konto-Zugriffsschlüssel weitergeben zu müssen. Die SAS ist ein URI, dessen Abfrageparameter alle erforderlichen Informationen für den authentifizierten Zugriff auf eine Speicherressource enthalten. Für den Zugriff auf Speicherressourcen mit der SAS braucht der Client diese nur an den entsprechenden Konstruktor bzw. die entsprechende Methode zu übergeben.

## Wann sollten Sie eine Shared Access Signature verwenden? ##

Sie können eine SAS verwenden, um einem Client Zugriff auf Ressourcen in Ihrem Speicherkonto zu bieten, dem Sie Ihren Kontoschlüssel nicht anvertrauen möchten. Ihre Speicherkontoschlüssel bestehen aus Primär- und Sekundärschlüssel. Beide bieten Administratorzugriff auf Ihr Konto und alle enthaltenen Ressourcen. Wenn Sie Ihre Kontoschlüssel weitergeben, besteht die Gefahr von böswilliger oder fahrlässiger Nutzung. Shared Access Signatures bieten eine sichere alternative, um anderen Clients Lese-, Schreib- und Löschzugriff auf Daten in Ihrem Speicherkonto gemäß der von Ihnen definierten Berechtigungen zu bieten, ohne den Kontoschlüssel weitergeben zu müssen.

SAS sind zum Beispiel dann hilfreich, wenn Benutzer ihre eigenen Daten in Ihrem Speicherkonto ablegen und von dort abrufen. Für den Fall, dass ein Speicherkonto Benutzerdaten enthält, existieren zwei typische Designmuster:


1. Clients laden Daten über einen Front-End-Proxydienst hoch und herunter, der die Authentifizierung übernimmt. Dieser Front-End-Proxydienst hat den Vorteil, dass auch Geschäftsregeln validiert werden können. Allerdings kann die Erstellung eines skalierbaren Dienstes für große Datenmengen oder Transaktionen mit großem Volumen teuer und aufwändig sein.

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]

2. Ein vereinfachter Dienst authentifiziert den Client bei Bedarf und generiert anschließend die SAS. Sobald der Client die SAS erhält, kann er direkt gemäß der in der SAS definierten Berechtigungen für den definierten Zeitraum auf die Speicherkonto-Ressourcen zugreifen. Dank der SAS müssen nicht mehr alle Daten durch einen Front-End-Proxydienst geleitet werden.

![sas-storage-provider-service][sas-storage-provider-service]

Viele tatsächliche Dienste verwenden je nach Szenario eine Mischung aus beiden Ansätzen, verarbeiten und validieren einige Daten im Front-End-Proxy und speichern und/oder lesen andere Daten direkt per SAS.

## Funktionsweise von Shared Access Signatures ##

Shared Access Signatures sind URIs, die auf Speicherressourcen verweisen und verschiedene Abfrageparameter enthalten, mit denen angegeben wird, in welcher Form der Client auf die Ressource zugreifen darf. Einer dieser Parameter ist die Signatur. Sie besteht aus den SAS-Parametern und wird mit dem Kontoschlüssel signiert. Der Azure-Speicher verwendet die Signatur, um die SAS zu authentifizieren.

Shared Access Signatures werden durch die folgenden Einschränkungen definiert. Jede der Einschränkungen wird als Parameter des URI dargestellt:

- **Die Speicherressource.** Sie können den Zugriff auf Speicherressourcen wie z. B. Container, Blobs, Warteschlangen, Tabellen und Bereiche von Tabellenentitäten delegieren.
- **Startzeit.** Dies ist der Zeitpunkt, ab dem die SAS gültig ist. Die Startzeit für eine Shared Access Signature ist optional. Wenn die Startzeit nicht angegeben wird, ist die SAS sofort gültig. 
- **Ablaufzeit.** Dies ist der Zeitpunkt, ab dem die SAS nicht mehr gültig ist. Sie sollten nach Möglichkeit entweder eine Ablaufzeit für die SAS angeben oder diese mit einer gespeicherten Zugriffsrichtlinie verknüpfen (siehe unten).
- **Berechtigungen.** Die in der SAS angegebenen Berechtigungen geben an, welche Operationen der Client mit der SAS auf der Speicherressource ausführen kann. 

Dies ist ein Beispiel für einen SAS-URI, der Lese- und Schreibzugriff für einen Blob gewährt. Die Tabelle analysiert die einzelnen Teile des URI, um dessen Zusammenhang mit der SAS zu erläutern:

https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-29T22%3A18%3A26Z&se=2013-04-30T02%3A23%3A26Z&sr=b&sp=rw&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

<table border="1" cellpadding="0" cellspacing="0">
    <tbody>
        <tr>
            <td valign="top" width="213">
                <p>
                    Blob-URI
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Die Blob-Adresse. Sie sollten unbedingt HTTPS verwenden.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Version des Speicherdienstes
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sv=2012-02-12
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Für Speicherdienste der Version 2012-02-12 und später gibt dieser Parameter die zu verwendende Version an.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Startzeit
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    st=2013-04-29T22%3A18%3A26Z
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Angegeben im ISO 8061-Format. Lassen Sie diesen Parameter aus, wenn die SAS sofort gültig sein soll.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Ablaufzeit
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    se=2013-04-30T02%3A23%3A26Z
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Angegeben im ISO 8061-Format.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Ressource
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sr=b
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Die Ressource ist ein Blob.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Berechtigungen
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sp=rw
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Die SAS verleiht die Berechtigungen zum Lesen (r) und Schreiben (w).
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top" width="213">
                <p>
                    Signatur
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
                </p>
            </td>
            <td valign="top" width="213">
                <p>
                    Wird zur Zugriffsauthentifizierung für den Blob verwendet. Die Signatur ist ein HMAC, der mithilfe des SHA256-Algorithmus über StringToSign-Zeichenfolge und Schlüssel erstellt und anschließend mit Base64 codiert wird.
                </p>
            </td>
        </tr>
    </tbody>
</table>


## Steuerung von Shared Access Signatures mit gespeicherten Zugriffsrichtlinien ##

Shared Access Signatures können zwei unterschiedliche Formen haben:

- **Ad-Hoc-SAS:** Beim Erstellen von Ad-Hoc-SAS werden Startzeit, Ablaufzeit und Berechtigungen für die SAS direkt im SAS-URI angegeben (bzw. impliziert, falls die Startzeit ausgelassen wird). Diese Art von SAS kann für Container, Blobs, Tabellen oder Warteschlangen erstellt werden.
- **SAS mit gespeicherten Zugriffsrichtlinien:** Gespeicherte Zugriffsrichtlinien werden für Ressourcencontainer – Blobcontainer, Tabellen oder Warteschlangen – definiert und dienen zur Verwaltung von Einschränkungen für eine oder mehrere Shared Access Signatures. Wenn Sie eine SAS mit einer gespeicherten Zugriffsrichtlinie verknüpfen, erbt die SAS die Einschränkungen (Startzeit, Ablaufzeit und Berechtigungen) dieser gespeicherten Zugriffsrichtlinie.

Der Unterschied zwischen diesen beiden Formen ist wichtig für ein Schlüsselszenario: Widerruf. Eine SAS ist eine URL und kann daher von beliebiger Stelle verwendet werden, unabhängig davon, wer die SAS ursprünglich angefordert hatte. Wenn eine SAS veröffentlicht wird, kann diese von beliebiger Stelle weltweit verwendet werden. Auf diese Weise verteilte SAS sind gültig, bis eines von vier Ereignissen eintritt:

1.	Die Ablaufzeit der SAS wird erreicht.
2.	Die Ablaufzeit der von der SAS referenzierten gespeicherten Zugriffsrichtlinie wird erreicht (falls eine gespeicherte Zugriffsrichtlinie referenziert wurde und diese eine Ablaufzeit definiert). Dies geschieht entweder, wenn das Zeitintervall abläuft, oder wenn Sie die Ablaufzeit der gespeicherten Zugriffsrichtlinie auf einen Zeitpunkt in der Vergangenheit gesetzt haben, um die SAS zu widerrufen.
3.	Die von der SAS referenzierte gespeicherte Zugriffsrichtlinie wird gelöscht, wodurch die SAS ebenfalls widerrufen wird. Wenn Sie die gespeicherte Zugriffsrichtlinie mit demselben Namen erneut erstellen, sind alle existierenden SAS-Tokens gemäß der Berechtigungen der gespeicherten Zugriffsrichtlinie wieder gültig (sofern die Ablaufzeit der SAS noch nicht erreicht wurde). Falls Sie die SAS widerrufen möchten, müssen Sie einen anderen Namen verwenden, wenn Sie die gespeicherte Zugriffsrichtlinie mit einer Ablaufzeit in der Zukunft erneut erstellen.
4.	Der Kontoschlüssel, mit dem die SAS erstellt wurde, wird erneut generiert. Dies führt dazu, dass die Authentifikation aller Anwendungskomponenten mit diesem Kontoschlüssel fehlschlägt, bis diese entweder mit dem anderen gültigen Kontoschlüssel oder dem neu generierten Kontoschlüssel aktualisiert werden.
 
## Bewährte Methoden für die Verwendung von Shared Access Signatures ##

Wenn Sie Shared Access Signatures in Ihren Anwendungen verwenden, müssen Sie sich der potenziellen Risiken bewusst sein:

- Weitergegebene SAS können von beliebiger Stelle verwendet werden, was die Sicherheit Ihres Speicherkontos gefährden kann.
- Wenn die SAS einer Clientanwendung abläuft und die Anwendung keine neue SAS von Ihrem Dienst abrufen kann, wird die Funktionsweise der Anwendung möglicherweise eingeschränkt.  

Mit den folgenden Empfehlungen für die Verwendung von Shared Access Signatures können Sie diese Risiken mindern:

1. **Verwenden Sie immer HTTPS** für die Erstellung und Verteilung von SAS. Wenn eine SAS über HTTP weitergegeben und abgefangen wird, kann ein Angreifer mit einem Man-in-the-Middle-Angriff diese auslesen, anschließend im Namen des Benutzers verwenden und somit möglicherweise sensible Daten gefährden oder böswillig beschädigen.
2. **Verweisen Sie nach Möglichkeit auf gespeicherte Zugriffsrichtlinien.** Mit gespeicherten Zugriffsrichtlinien können Sie Berechtigungen widerrufen, ohne die Schlüssel für das Speicherkonto neu generieren zu müssen. Setzen Sie das Ablaufdatum dieser Richtlinien weit in die Zukunft (oder unendlich) und stellen Sie sicher, dass das Datum regelmäßig aktualisiert und weiter in die Zukunft verschoben wird.
3. **Verwenden Sie Ablaufdaten in naher Zukunft für Ad-Hoc-SAS.** Auf diese Weise sind SAS nur für kurze Zeit gültig, selbst wenn ihre Sicherheit auf unbekannte Weise gefährdet ist. Dies ist besonders dann wichtig, wenn Sie nicht auf eine gespeicherte Zugriffsrichtlinie verweisen können. Auf diese Weise schränken Sie auch die Datenmenge ein, die in einen Blob geschrieben werden kann, indem Sie die verfügbare Uploadzeit verkürzen.
4. **Sorgen Sie dafür, dass die Clients die SAS bei Bedarf automatisch erneuern müssen.** Die Clients sollten ihre SAS rechtzeitig vor der erwarteten Ablaufzeit erneuern, um Zeit für Wiederholungsversuche zu bieten, falls der entsprechende Dienst nicht verfügbar sein sollte. Falls Ihre SAS für eine kleine Anzahl sofortiger und kurzfristiger Operationen gilt, die normalerweise innerhalb der gegebenen Ablaufzeit abgeschlossen werden, ist dies möglicherweise nicht notwendig, da die SAS nicht erneuert werden müssen. Wenn Ihre Clients jedoch immer wieder Anfragen über die SAS stellen, müssen Sie sich mit dem Ablaufmechanismus auseinander setzen. Dabei müssen Sie einen Ausgleich zwischen der Notwendigkeit einer kurzlebigen SAS (wie oben beschrieben) und dem Bedarf des Clients schaffen, diese rechtzeitig zu erneuern, um zu verhindern, dass die SAS vor der erfolgreichen Erneuerung abläuft.
5. **Seien Sie vorsichtig mit der SAS-Startzeit.** Wenn Sie die Startzeit einer SAS auf **jetzt** setzen, können aufgrund von Zeitunterschieden zwischen unterschiedlichen Computern in den ersten Minuten Probleme auftreten. Daher sollten Sie die Startzeit mindestens 15 Minuten in der Vergangenheit setzen oder auch auslassen, damit diese in allen Fällen sofort gültig ist. Dasselbe gilt für die Ablaufzeit. Rechnen Sie immer mit Zeitunterschieden von bis zu 15 Minuten in beide Richtungen. Für Clients mit einer REST-Version vor 2012-02-12 ist die maximale Dauer von SAS, die keine gespeicherte Zugriffsrichtlinie referenzieren, eine Stunde. Alle Richtlinien, die längere Zeiträume verwenden, schlagen fehl.
6.	**Geben Sie die freigegebene Ressource exakt an.** Normalerweise sollten Sie Benutzern immer ein Minimum an benötigten Privilegien erteilen. Wenn ein Benutzer nur Lesezugriff auf eine einzige Entität benötigt, dann geben Sie auch nur Lesezugriff auf diese Entität, und nicht Lese-/Schreib-/Löschzugriff auf alle Entitäten. Auf diese Weise mindern Sie auch die Missbrauchsgefahr für Ihre SAS, da diese in den Händen eines Angreifers weniger mächtig ist.
7.	**Beachten Sie, dass Ihnen jegliche Nutzung Ihres Kontos berechnet wird, inklusive der Nutzung über SAS.** Wenn Sie Schreibzugriff für einen Blob vergeben, können Benutzer Blobs mit bis zu 200GB hochladen. Falls Sie außerdem noch Lesezugriff vergeben, können die Benutzer die Daten bis zu 10 mal herunterladen und Gebühren für den Datenausgang von bis zu 2TB verursachen. Vergeben Sie also eingeschränkte Berechtigungen, um das Schadenspotenzial böswilliger Benutzer einzugrenzen. Verwenden Sie kurzlebige SAS, um diese Bedrohung zu mindern (beachten Sie jedoch mögliche Zeitunterschiede bei der Ablaufzeit).
8.	**Überprüfen Sie Daten, die per SAS geschrieben wurden.** Wenn Clientanwendungen Daten in Ihr Speicherkonto schreiben, müssen Sie stets beachten, dass diese Daten problembehaftet sein können. Wenn Ihre Anwendung diese Daten vor der Verwendung validieren oder autorisieren muss, sollten Sie diese Validierung durchführen, nachdem die Daten geschrieben und bevor sie von Ihrer Anwendung verwendet werden. Auf diese Weise schützen Sie Ihr Konto auch vor beschädigten oder bösartigen Daten, sowohl von tatsächlich berechtigten SAS-Benutzern als auch von Angreifern, die eine abgefangene SAS verwenden.
9. **Verwenden Sie SAS nicht in jedem Fall.** Manchmal überwiegen die Risiken für eine bestimmte Operation auf Ihrem Speicherkonto den Nutzen von SAS. Für solche Operation sollten Sie einen Dienst auf der mittleren Ebene erstellen, der zunächst Geschäftsregeln validiert sowie Authentifizierung und Überwachung durchführt und die Daten anschließend in Ihr Speicherkonto schreibt. Manchmal gibt es auch einfachere Möglichkeiten der Zugriffsverwaltung. Wenn Sie z. B. alle Blobs in einem Container öffentlich lesbar machen möchten, können Sie auch den Container öffentlich machen, anstatt jedem Client für den Zugriff eine SAS zu geben.
10.	**Überwachen Sie Ihrer Anwendung mithilfe der Speicheranalyse.** Sie können Häufungen von Authentifizierungsfehlern aufgrund von Ausfällen Ihres SAS-Dienstes oder einer unbeabsichtigt gelöschten gespeicherten Zugriffsrichtlinie mithilfe von Protokollierung und Metriken beobachten. Weitere Informationen finden Sie im [Blog des Azure-Speicherteams](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx).

## Zusammenfassung ##

Shared Access Signatures sind nützlich für die Vergabe eingeschränkter Berechtigungen für Ihr Speicherkonto an Clients, die keinen Zugriff auf Ihren Kontoschlüssel haben dürfen. Somit sind sie ein wichtiger Teil des Sicherheitsmodells für alle Anwendungen, die den Azure-Speicher verwenden. Wenn Sie den hier genannten Empfehlungen folgen, können Sie mit SAS eine größere Flexibilität für den Zugriff auf Ressourcen in Ihrem Speicherkonto erreichen, ohne die Sicherheit Ihrer Anwendung zu gefährden.

## Nächste Schritte ##

[Shared Access Signatures, Teil 2: Erstellen und Verwenden einer SAS mit dem Blobdienst](../storage-dotnet-shared-access-signature-part-2/)

[Verwalten des Zugriffs auf Azure Storage-Ressourcen](http://msdn.microsoft.com/library/azure/ee393343.aspx)

[Delegieren des Zugriffs mit einer Shared Access Signature (REST API)](http://msdn.microsoft.com/library/azure/ee395415.aspx)

[Einführung in Tabellen- und Warteschlangen-SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
[sas-storage-fe-proxy-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png
[sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png


 

<!----HONumber=July15_HO1-->