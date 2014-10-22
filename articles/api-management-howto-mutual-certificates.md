<properties pageTitle="How to secure back-end services using mutual certificate authentication in Azure API Management" metaKeywords="" description="Learn how to secure back-end services using mutual certificate authentication in Azure API Management." metaCanonical="" services="api-management" documentationCenter="API Management" title="How to secure back-end services using mutual certificate authentication in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# So sichern Sie Back-End-Dienste mittels gegenseitiger Zertifikatsauthentifizierung in Azure API Management

API Management bietet die Möglichkeit, den Zugriff auf den Back-End-Dienst einer API mithilfe von gegenseitigen Zertifikaten abzusichern. Diese Anleitung beschreibt, wie Zertifikate in der API-Verwaltungskonsole verwaltet werden und eine API zur Verwendung eines Zertifikats für den Zugriff auf ihren Back-End-Dienst konfiguriert wird.

> Weitere Informationen über die Verwaltung von Zertifikaten mit der API Management REST API finden Sie unter [Azure API Management REST API Certificate entity][Azure API Management REST API Certificate entity].

## In diesem Thema

-   [Voraussetzungen][Voraussetzungen]
-   [Hochladen eines Client-Zertifikats][Hochladen eines Client-Zertifikats]
-   [Löschen eines Client-Zertifikats][Löschen eines Client-Zertifikats]
-   [Konfigurieren einer API zum Verwenden eines gegenseitigen Zertifikats zur Proxy-Authentifizierung][Konfigurieren einer API zum Verwenden eines gegenseitigen Zertifikats zur Proxy-Authentifizierung]

## <a name="prerequisites"> </a>Voraussetzungen

Diese Anleitung beschreibt, wie Sie Ihre Instanz des API-Verwaltungsdiensts konfigurieren, um für den Zugriff auf den Back-End-Dienst einer API gegenseitige Zertifikatsauthentifizierung zu verwenden. Bevor Sie nach den Schritten in diesem Thema vorgehen, müssen Sie Ihren Back-End-Dienst für gegenseitige Zertifikatsauthentifizierung konfiguriert haben und auf das Zertifikat und dessen Kennwort zum Hochladen in die API-Verwaltungskonsole zugreifen können.

## <a name="step1"> </a>Hochladen eines Client-Zertifikats

Klicken Sie zunächst in der **Verwaltungskonsole** im Azure-Portal auf Ihren API-Verwaltungsdienst. Daraufhin gelangen Sie zum Verwaltungsportal für die API-Verwaltung.

![API-Verwaltungskonsole][API-Verwaltungskonsole]

> Falls Sie noch keine API-Verwaltungs-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API-Verwaltungsinstanz][Erstellen einer API-Verwaltungsinstanz] im Lernprogramm [Erste Schritte mit der Azure API-Verwaltung][Erste Schritte mit der Azure API-Verwaltung].

Klicken Sie auf **Sicherheit** im Menü **API-Verwaltung** auf der linken Seite und dann auf **Client-Zertifikate**.

![Client-Zertifikate][Client-Zertifikate]

Um ein neues Zertifikat hochzuladen, klicken Sie auf **Hochladen des Zertifikats**.

![Hochladen des Zertifikats][Hochladen des Zertifikats]

Navigieren Sie zu Ihrem Zertifikat, und geben Sie das Kennwort für das Zertifikat ein.

> Das Zertifikat muss im **.pfx**-Format vorliegen. Selbstsignierte Zertifikate sind zulässig.

![Hochladen des Zertifikats][1]

Klicken Sie auf **Hochladen**, um das Zertifikat hochzuladen.

> Zu diesem Zeitpunkt wird das Kennwort des Zertifikats überprüft. Falls es fehlerhaft ist, wird eine Fehlermeldung angezeigt.

![Zertifikat hochgeladen][Zertifikat hochgeladen]

Sobald das Zertifikat hochgeladen ist, wird es auf der Registerkarte **Client-Zertifikate** angezeigt. Falls Sie über mehrere Zertifikate verfügen, legen Sie einen Hinweis zum Thema an, oder verwenden Sie die letzten vier Zeichen des Fingerabdrucks, die zur Auswahl eines Zertifikats beim Konfigurieren einer API zum Verwenden von Zertifikaten dienen; siehe folgender Abschnitt [Konfigurieren einer API zum Verwenden eines gegenseitigen Zertifikats zur Proxy-Authentifizierung][Konfigurieren einer API zum Verwenden eines gegenseitigen Zertifikats zur Proxy-Authentifizierung].

## <a name="step1a"> </a>Löschen eines Client-Zertifikats

Um ein Zertifikat zu löschen, klicken Sie neben dem betreffenden Zertifikat auf **Löschen**.

![Löschen eines Zertifikats][Löschen eines Zertifikats]

Klicken Sie zur Bestätigung auf **Ja, löschen**.

![Bestätigen des Löschens][Bestätigen des Löschens]

Falls das Zertifikat von einer API verwendet wird, wird ein Warnbildschirm angezeigt. Um das Zertifikat zu löschen, müssen Sie es zunächst aus allen APIs entfernen, die zu seiner Verwendung konfiguriert sind.

![Bestätigen des Löschens][2]

## <a name="step2"> </a>Konfigurieren einer API zum Verwenden eines gegenseitigen Zertifikats zur Proxy-Authentifizierung

Klicken Sie auf **APIs** im Menü **API-Verwaltung** auf der linken Seite, klicken Sie auf die gewünschte API und dann auf die Registerkarte **Sicherheit**.

![API-Sicherheit][API-Sicherheit]

Wählen Sie **Gegenseitige Zertifikate** aus der Dropdownliste **Mit Anmeldeinformationen** aus.

![Gegenseitige Zertifikate][Gegenseitige Zertifikate]

Wählen Sie in der Dropdownliste **Client-Zertifikate** das gewünschte Zertifikat aus. Falls mehrere Zertifikate vorhanden sind, können Sie sich bei der Bestimmung des richtigen Zertifikats am angegebenen Thema oder den letzten vier Zeichen des Fingerabdrucks orientieren, wie im vorherigen Abschnitt beschrieben.

![Zertifikat auswählen][Zertifikat auswählen]

Klicken Sie auf **Speichern**, um die Konfigurationsänderungen der API zu speichern.

> Die Änderungen werden unmittelbar übernommen, und Funktionsaufrufe dieser API verwenden das Zertifikat für die Authentifizierung beim Back-End-Server.

![API-Änderungen speichern][API-Änderungen speichern]

> Wenn für den Back-End-Dienst einer API ein Zertifikat zur Proxy-Authentifizierung angegeben ist, wird es Teil der Richtlinie dieser API und kann im Richtlinieneditor angezeigt werden.

![Zertifikatsrichtlinie][Zertifikatsrichtlinie]

  [Azure API Management REST API Certificate entity]: http://msdn.microsoft.com/library/azure/dn783483.aspx
  [Voraussetzungen]: #prerequisites
  [Hochladen eines Client-Zertifikats]: #step1
  [Löschen eines Client-Zertifikats]: #step1a
  [Konfigurieren einer API zum Verwenden eines gegenseitigen Zertifikats zur Proxy-Authentifizierung]: #step2
  [API-Verwaltungskonsole]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
  [Erstellen einer API-Verwaltungsinstanz]: ../api-management-get-started/#create-service-instance
  [Erste Schritte mit der Azure API-Verwaltung]: ../api-management-get-started
  [Client-Zertifikate]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
  [Hochladen des Zertifikats]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
  [1]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
  [Zertifikat hochgeladen]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
  [Löschen eines Zertifikats]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
  [Bestätigen des Löschens]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
  [2]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png
  [API-Sicherheit]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
  [Gegenseitige Zertifikate]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
  [Zertifikat auswählen]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
  [API-Änderungen speichern]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
  [Zertifikatsrichtlinie]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
