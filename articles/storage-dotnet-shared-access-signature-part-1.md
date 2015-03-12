<properties 
	pageTitle="Shared Access Signatures (SAS): Grundlagen zum SAS-Modell | Microsoft Azure" 
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
	ms.date="11/10/2014" 
	ms.author="tamram"/>



# Shared Access Signatures, Teil 1: Grundlagen zum SAS-Modell

Shared Access Signatures (SAS) sind eine leistungsstarke Methode, um anderen Clients eingeschränkten Zugriff auf Blobs, Tabellen und Warteschlangen in Ihrem Speicherkonto zu bieten, ohne Ihren Kontoschlüssel weitergeben zu müssen. Teil 1 dieses Lernprogramms zu Shared Access Signatures bietet eine Übersicht über das SAS-Modell und bewährte Methoden für SAS. [Teil 2](../storage-dotnet-shared-access-signature-part-2/) des Lernprogramms beschreibt die Erstellung von Shared Access Signatures mit dem Blob-Dienst.

## Was ist eine Shared Access Signature? ##

Shared Access Signatures bieten delegierten Zugriff auf Ressourcen in Ihren Speicherkonto. Sie haben die Möglichkeit, einem Client für einen bestimmten Zeitraum spezielle eingeschränkte Berechtigungen für Ihre Blobs, Warteschlangen oder Tabellen erteilen, ohne Ihre Konto-Zugriffsschlüssel weitergeben zu müssen. Die SAS ist eine URI, deren Abfrageparameter alle erforderlichen Informationen für den authentifizierten Zugriff auf eine Speicherressource enthalten. Für den Zugriff auf Speicherressourcen mit der SAS braucht der Client diese nur an den entsprechenden Konstruktor bzw. die entsprechende Methode zu übergeben.

## Wann sollten Sie eine Shared Access Signature verwenden? ##

Sie können eine SAS verwenden, um einem Client Zugriff auf Ressourcen in Ihrem Speicherkonto zu bieten, dem Sie Ihren Kontoschlüssel nicht anvertrauen möchten. Ihre Speicherkontoschlüssel bestehen aus Primär- und Sekundärschlüssel. Beide bieten Administratorzugriff auf Ihr Konto und alle enthaltenen Ressourcen. Wenn Sie Ihre Kontoschlüssel weitergeben, besteht die Gefahr von böswilliger oder fahrlässiger Nutzung. Shared Access Signatures bieten eine sichere alternative, um anderen Clients Lese-, Schreib- und Löschzugriff auf Daten in Ihrem Speicherkonto gemäß der von Ihnen definierten Berechtigungen zu bieten, ohne den Kontoschlüssel weitergeben zu müssen.

SAS sind zum Beispiel dann hilfreich, wenn Benutzer ihre eigenen Daten in Ihrem Speicherkonto ablegen und von dort abrufen. Für den Fall, dass ein Speicherkonto Benutzerdaten enthält, existieren zwei typische Designmuster:


1\. Clients laden Daten über einen Front-End-Proxydienst hoch und herunter, der die Authentifizierung übernimmt. Dieser Front-End-Proxydienst hat den Vorteil, dass auch Geschäftsregeln validiert werden können. Allerdings kann die Erstellung eines skalierbaren Diensts für große Datenmengen oder Transaktionen mit großem Volumen teuer und aufwändig sein.

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]

2\.	Ein vereinfachter Dienst authentifiziert den Client bei Bedarf und generiert anschließend die SAS. Sobald der Client die SAS erhält, kann er direkt gemäß der in der SAS definierten Berechtigungen für den definierten Zeitraum auf die Speicherkonto-Ressourcen zugreifen. Dank der SAS müssen nicht mehr alle Daten durch einen Front-End-Proxydienst geleitet werden.

![sas-storage-provider-service][sas-storage-provider-service]

Viele tatsächliche Dienste verwenden je nach Szenario eine Mischung aus beiden Ansätzen, verarbeiten und validieren einige Daten im Front-End-Proxy und speichern und/oder lesen andere Daten direkt per SAS.

## Funktionsweise von Shared Access Signatures ##

Shared Access Signatures sind URIs, die auf Speicherressourcen verweisen und verschiedene Abfrageparameter enthalten, mit denen angegeben wird, in welcher Form der Client auf die Ressource zugreifen darf. Einer dieser Parameter ist die Signatur. Sie besteht aus den SAS-Parametern und wird mit dem Kontoschlüssel signiert. Der Azure-Speicher verwendet die Signatur, um die SAS zu authentifizieren.

Shared Access Signatures werden durch die folgenden Einschränkungen definiert. Jede der Einschränkungen wird als Parameter der URI dargestellt:

- **Die Speicherressource.** Sie können den Zugriff auf Speicherressourcen wie z. B. Container, Blobs, Warteschlangen, Tabellen und Bereiche von Tabellenentitäten setzen.
- **Startzeit.** Dies ist die Zeit, zu der die SAS gültig wird. Die Startzeit für eine Shared Access Signature ist optional; wird dieser Parameter nicht angegeben, so wird die SAS sofort wirksam.
- **Ablaufzeit.** Dies ist die Zeit, ab der die SAS nicht mehr gültig sind. Als bewährte Methode empfiehlt sich, entweder eine Ablaufzeit für eine SAS anzugeben oder diese mit einer gespeicherten Zugriffsrichtlinie zu verknüpfen (weitere Informationen siehe unten).
- **Berechtigungen.** Die in der SAS angegebenen Berechtigungen geben an, welche Vorgänge der Client mit der SAS auf der Speicherressource ausführen kann.

Dies ist ein Beispiel für eine SAS-URI, die Lese- und Schreibzugriff für einen Blob verleiht. Die Tabelle analysiert die einzelnen Teile der URI, um deren Zusammenhang mit der SAS zu erläutern:

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
                    Version des Speicherdiensts
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

- **Ad-hoc-SAS:** Bei der Erstellung von Ad-Hoc-SAS werden Startzeit, Ablaufzeit und Berechtigungen für die SAS allesamt in der SAS-URI angegeben (bzw. implizit angegeben, falls die Startzeit ausgelassen wird). Dieser SAS-Typ kann in einem Container, Blob, einer Tabelle oder einer Warteschlange erstellt werden.
- **SAS mit gespeicherter Zugriffsrichtlinie:** Eine gespeicherte Zugriffsrichtlinie wird in einem Ressourcencontainer (einem Blob-Container, einer Tabelle oder Warteschlange) definiert und kann zur Verwaltung von Einschränkungen für eine oder mehrere Shared Access Signatures verwendet werden. Wenn Sie eine SAS mit einer gespeicherten Zugriffsrichtlinie verknüpfen, übernimmt die SAS die für die gespeicherte Zugriffsrichtlinie definierten Einschränkungen (Startzeit, Ablaufzeit und Berechtigungen).

Der Unterschied zwischen diesen beiden Formen ist wichtig für ein Schlüsselszenario: Widerruf. Eine SAS ist eine URL und kann daher von beliebiger Stelle verwendet werden, unabhängig davon, wer die SAS ursprünglich angefordert hatte. Wenn eine SAS veröffentlicht wird, kann diese von beliebiger Stelle weltweit verwendet werden. Auf diese Weise verteilte SAS sind gültig, bis eines von vier Ereignissen eintritt:

1.	Die Ablaufzeit der SAS wird erreicht.
2.	Die Ablaufzeit der von der SAS referenzierten gespeicherten Zugriffsrichtlinie wird erreicht (falls eine gespeicherte Zugriffsrichtlinie referenziert wurde und diese eine Ablaufzeit definiert). Dies geschieht entweder, wenn das Zeitintervall abläuft, oder wenn Sie die Ablaufzeit der gespeicherten Zugriffsrichtlinie auf einen Zeitpunkt in der Vergangenheit gesetzt haben, um die SAS zu widerrufen.
3.	Die von der SAS referenzierte gespeicherte Zugriffsrichtlinie wird gelöscht, wodurch die SAS ebenfalls widerrufen wird. Wenn Sie die gespeicherte Zugriffsrichtlinie mit demselben Namen erneut erstellen, sind alle existierenden SAS-Tokens gemäß der Berechtigungen der gespeicherten Zugriffsrichtlinie wieder gültig (sofern die Ablaufzeit der SAS noch nicht erreicht wurde). Falls Sie die SAS widerrufen möchten, müssen Sie einen anderen Namen verwenden, wenn Sie die gespeicherte Zugriffsrichtlinie mit einer Ablaufzeit in der Zukunft erneut erstellen.
4.	Der Kontoschlüssel, mit dem die SAS erstellt wurde, wird erneut generiert.  Dies führt dazu, dass die Authentifikation aller Anwendungskomponenten mit diesem Kontoschlüssel fehlschlägt, bis diese entweder mit dem anderen gültigen Kontoschlüssel oder dem neu generierten Kontoschlüssel aktualisiert werden.
 
## Bewährte Methoden für die Verwendung von Shared Access Signatures ##

Wenn Sie Shared Access Signatures in Ihren Anwendungen verwenden, müssen Sie sich der potenziellen Risiken bewusst sein:

- Weitergegebene SAS können von beliebiger Stelle verwendet werden, was die Sicherheit Ihres Speicherkontos gefährden kann.
- Wenn die SAS einer Clientanwendung abläuft und die Anwendung keine neue SAS von Ihrem Dienst abrufen kann, wird die Funktionsweise der Anwendung möglicherweise eingeschränkt.  

Mit den folgenden Empfehlungen für die Verwendung von Shared Access Signatures können Sie diese Risiken mindern:

1. **Verwenden Sie immer HTTPS** für die Erstellung und Verteilung von SAS.  Wenn eine SAS über HTTP weitergegeben und abgefangen wird, kann ein Angreifer mit einem Man-in-the-Middle-Angriff diese auslesen, anschließend im Namen des Benutzers verwenden und somit möglicherweise sensible Daten gefährden oder böswillig beschädigen.
2. **Verweisen Sie nach Möglichkeit auf gespeicherte Zugriffsrichtlinien.** Gespeicherte Zugriffsrichtlinien geben Ihnen die Möglichkeit, Berechtigungen zu widerrufen, ohne die Speicherkontoschlüssel erneut generieren zu müssen. Legen Sie als Ablaufzeit eine weit in der Zukunft liegende (oder unendliche) Zeit fest und achten Sie darauf, dass diese regelmäßig aktualisiert wird, um sie weiter in die Zukunft zu verschieben.
3. **Verwenden Sie für eine Ad-hoc-SAS in der nahen Zukunft liegende Ablaufzeiten.** Auf diese Weise wird die Ad-hoc-SAS nur für einen kurzen Zeitraum verwendet, selbst wenn sie unwissentlich gefährdet wird. Diese Methode ist besonders wichtig, wenn Sie nicht auf eine gespeicherte Zugriffsrichtlinie verweisen können. Außerdem können Sie dadurch die Datenmenge begrenzen, die in einen Blob geschrieben werden kann, indem Sie das zum Hochladen des Blobs verfügbare Zeitfenster begrenzen.
4. **Lassen Sie die SAS bei Bedarf automatisch von den Clients erneuern.** Die Clients sollten die SAS ebenfalls vor dem voraussichtlichen Ablauf erneuern, damit Zeit für erneute Versuche vorhanden ist, falls der die SAS bereitstellende Dienst nicht erreichbar ist. Wenn Ihre SAS für die Verwendung einer kleinen Zahl von sofortigen, kurzlebigen Vorgängen vorgesehen ist, die voraussichtlich innerhalb der angegebenen Ablaufzeit abgeschlossen werden, ist dies möglicherweise nicht erforderlich, weil eine Erneuerung der SAS nicht zu erwarten ist. Wenn Sie jedoch einen Client haben, der routinemäßig Abfragen über die SAS sendet, müssen Sie damit rechnen, dass die Zeit vorzeitig abläuft. Wichtig ist vor allem, ein ausgewogenes Verhältnis zwischen dem Bedarf einer kurzlebigen SAS (wie oben erwähnt) und der Notwendigkeit, eine frühzeitige Anforderung der Erneuerung durch den Client sicherzustellen, zu finden, damit es nicht durch einen Ablauf der SAS vor der erfolgreichen Erneuerung zu Unterbrechungen kommt.
5. **Achten Sie darauf, die richtige SAS-Startzeit festzulegen.** Wenn Sie die Startzeit für eine SAS auf **jetzt** festlegen, können aufgrund eines Zeitversatzes (d. h. von Unterschieden bei der aktuellen Zeit auf verschiedenen Computern) in den ersten Minuten wiederholte Ausfälle beobachtet werden. Legen Sie die Startzeit generell als mindestens 15 Minuten zurückliegend fest oder legen Sie gar keine Startzeit fest, sodass sie in jedem Fall sofort gültig wird. Dasselbe gilt generell auch für die Ablaufzeit. Beachten Sie, dass Sie für jede Anforderung einen Zeitversatz von bis zu 15 Minuten in beide Richtungen einkalkulieren müssen. Für Clients, die eine vor dem 12.02.2012 herausgegebene REST-Version verwenden, gilt eine maximale Dauer von 1 Stunde für eine SAS, die nicht auf eine gespeicherte Zugriffsrichtlinie verweist. Alle Richtlinien, die eine höhere Dauer angeben, schlagen fehl.
6.	**Geben Sie genau an, auf welche Ressourcen zugegriffen werden darf.** Als typische bewährte Sicherheitsmethode empfiehlt sich, dem Benutzer nur die minimal benötigten Berechtigungen zu erteilen. Wenn ein Benutzer nur Lesezugriff auf eine einzelne Entität benötigt, sollten Sie ihm Lesezugriff für die betreffende einzelne Entität gewähren anstatt Lese-/Schreib-/Löschzugriff für alle Entitäten. Auf diese Weise können Sie auch das Risiko einer Gefährdung der SAS mindern, weil die SAS, falls sie in die Hände eines Angreifers gerät, weniger Schaden anrichten kann.
7.	**Beachten Sie, dass Ihr Konto für jede Nutzung abgerechnet wird, einschließlich der Nutzung per SAS.** Wenn Sie Schreibzugriff für einen Blob gewähren, kann ein Benutzer womöglich einen 200-GB-Blob hochladen. Wenn Sie außerdem auch Lesezugriff gewähren, lädt der Benutzer den Blob möglicherweise zehnmal herunter und verursacht Ihnen dadurch Datenausgangskosten für 2 TB. Daher ist es so wichtig, dass Sie Berechtigungen mit Einschränkungen erteilen, um die Gefahr durch schädliche Nutzung möglichst zu mindern. Verwenden Sie kurzlebige SAS, um diese Bedrohung zu verringern (beachten Sie bei der Endzeit jedoch auch den Zeitversatz).
8.	**Validieren Sie die mit SAS geschriebenen Daten.** Wenn eine Clientanwendung Daten in Ihr Speicherkonto schreibt, sollten Sie beachten, dass diese Probleme bergen könnten. Wenn es für Ihre Anwendung erforderlich ist, dass die Daten validiert oder autorisiert werden, bevor sie verwendet werden können, sollten Sie diese Validierung ausführen, nachdem die Daten geschrieben wurden und bevor sie von Ihrer Anwendung verwendet werden. Mit dieser Methode schützen Sie Ihr Konto auch davor, dass beschädigte oder bösartige Daten dorthin geschrieben werden, entweder durch einen Benutzer, der die SAS ordnungsgemäß erworben hat, oder durch einen Benutzer, der den Verlust einer SAS ausnutzt.
9. **Verwenden Sie nicht immer SAS.** Manchmal wiegen die Vorteile von SAS die mit einem bestimmten Vorgang verbundenen Risiken für Ihr Speicherkonto nicht auf. Für solche Vorgänge sollten Sie einen Middle-Tier-Dienst erstellen, der erst in Ihr Speicherkonto schreibt, nachdem er die Validierung für die Geschäftsregeln, die Authentifizierung und die Überwachung durchgeführt hat. Manchmal ist es auch einfacher, den Zugriff auf andere Weise zu steuern. Wenn Sie beispielsweise alle Blobs in einem Container öffentlich lesbar machen möchten, können Sie den Container öffentlich machen, anstatt für jeden Client eine SAS für den Zugriff bereitzustellen.
10.	**Überwachen Sie Ihre Anwendung mit der Speicheranalyse.** Sie können Spitzen bei Authentifizierungsfehlern, die durch einen Ausfall des SAS-Anbieterdiensts oder durch unbeabsichtigtes Entfernen einer Speicherzugriffsrichtlinie verursacht werden, durch Protokollierung und Metriken beobachten. Weitere Informationen finden Sie im [Blog des Azure-Speicherteams](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx).

## Zusammenfassung ##

Shared Access Signatures sind nützlich für die Vergabe eingeschränkter Berechtigungen für Ihr Speicherkonto an Clients, die keinen Zugriff auf Ihren Kontoschlüssel haben dürfen.  Somit sind sie ein wichtiger Teil des Sicherheitsmodells für alle Anwendungen, die den Azure-Speicher verwenden.  Wenn Sie den hier genannten Empfehlungen folgen, können Sie mit SAS eine größere Flexibilität für den Zugriff auf Ressourcen in Ihrem Speicherkonto erreichen, ohne die Sicherheit Ihrer Anwendung zu gefährden.

## Next Steps ##

[Shared Access Signatures, Part 2: Create and Use a SAS with the Blob Service](../storage-dotnet-shared-access-signature-part-2/)

[Manage Access to Azure Storage Resources](http://msdn.microsoft.com/library/windowsazure/ee393343.aspx)

[Delegating Access with a Shared Access Signature (REST API)](http://msdn.microsoft.com/library/windowsazure/ee395415.aspx)

[Introducing Table and Queue SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
[sas-storage-fe-proxy-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png
[sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png


<!--HONumber=42-->
