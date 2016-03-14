<properties
	pageTitle="Erweiterte Anforderungsbegrenzung mit Azure API Management"
	description="Erfahren Sie, wie Sie mit Azure API Management flexibel Richtlinien zur Kontingents- und Ratenbegrenzung erstellen und anwenden."
	services="api-management"
	documentationCenter=""
	authors="darrelmiller"
	manager=""
	editor=""/>

<tags
	ms.service="api-management"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="02/19/2016"
	ms.author="v-darmi"/>


# Erweiterte Anforderungsbegrenzung mit Azure API Management

Die Fähigkeit, eingehende Anforderungen zu begrenzen oder zu drosseln, ist für Azure API Management von großer Bedeutung. Mit API Management lässt sich entweder die Rate der Anforderungen oder die Gesamtzahl der Anforderungen bzw. die Gesamtmenge der übertragenen Daten steuern. Dies ermöglicht es den API-Anbietern, ihre APIs vor Missbrauch zu schützen und mit verschiedenen API-Produkttarifen Mehrwert zu schaffen.

## Produktbasierte Drosselung
Bisher konnte die Rate nur auf der Basis eines bestimmten Produktabonnements begrenzt werden, das im API Management-Herausgeberportal definiert wurde (also im Wesentlichen auf Basis eines Schlüssels). Dies ist nützlich, wenn der API-Anbieter Beschränkungen für Entwickler einführen möchte, die sich zur Nutzung der API registriert haben. Es hilft aber z. B. nicht bei der Drosselung einzelner Endbenutzer der API. Daher ist es möglich, dass ein einzelner Benutzer der Anwendung eines Entwicklers das gesamte Kontingent in Anspruch nimmt und dadurch andere Kunden des Entwicklers an der Nutzung der Anwendung hindert. Außerdem können verschiedene Kunden gemeinsam ein so hohes Anforderungsvolumen generieren, dass der Zugriff für gelegentliche Benutzer beschränkt wird.

## Benutzerdefinierte Drosselung auf der Basis von Schlüsseln
Die neuen Richtlinien [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) und [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) bieten deutlich mehr Flexibilität bei der Steuerung des Datenverkehrs. Mit diesen neuen Richtlinien können Sie in selbst definierten Ausdrücken Schlüssel angeben, die zum Nachverfolgen der Datenverkehrsnutzung verwendet werden. Wie das funktioniert, lässt sich am einfachsten anhand eines Beispiels veranschaulichen.

## Drosselung nach IP-Adresse
Die folgenden Richtlinien beschränken eine einzelne Client-IP-Adresse auf nur 10 Aufrufe pro Minute bei einer Gesamtanzahl von 1.000.000 Aufrufen und 10.000 Kilobyte Bandbreite pro Monat.

    <rate-limit-by-key  calls="10"
              renewal-period="60"
              counter-key="@(context.Request.IpAddress)" />

    <quota-by-key calls="1000000"
              bandwidth="10000"
              renewal-period="2629800"
              counter-key="@(context.Request.IpAddress)" />

Wenn alle Clients im Internet eine eindeutige IP-Adresse verwenden würden, wäre dies vielleicht eine effektive Möglichkeit zur Nutzungsbegrenzung nach Benutzer. Allerdings ist es recht wahrscheinlich, dass mehrere Benutzer eine einzige öffentliche IP-Adresse gemeinsam nutzen, weil sie über ein NAT-Gerät auf das Internet zugreifen. Für APIs, die einen nicht authentifizierten Zugriff zulassen, ist `IpAddress` dennoch unter Umständen die beste Option.

## Drosselung nach Benutzeridentität
Wenn ein Endbenutzer authentifiziert ist, kann auf Basis der eindeutigen Identitätsinformationen dieses Benutzers ein Drosselungsschlüssel generiert werden.

    <rate-limit-by-key calls="10"
        renewal-period="60"
        counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />

In diesem Beispiel extrahieren wir den „Authorization“-Header, konvertieren ihn in ein `JWT`-Objekt und verwenden den Antragsteller des Tokens, um den Benutzer zu identifizieren. Diesen verwenden wir als Schlüssel zum Begrenzen der Rate. Wenn die Benutzeridentität in `JWT` als einer von anderen Ansprüchen gespeichert ist, kann anstelle dessen dieser Wert verwendet werden.

## Kombinierte Richtlinien
Obwohl die neuen Drosselungsrichtlinien mehr Steuerungsmöglichkeiten bieten als die bisher vorhandenen, lohnt es sich weiterhin, beide Fähigkeiten zu kombinieren. Die Drosselung nach Produktabonnementschlüssel ([Aufrufrate nach Abonnement einschränken](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) und [Nutzungskontingent nach Abonnement festlegen](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)) gibt Ihnen die Möglichkeit, nach Nutzungsstufen gestaffelte Gebühren für eine API festzulegen und sie so zu monetarisieren. Die feiner differenzierten Steuerungsmöglichkeiten einer Drosselung nach Benutzer können ergänzend genutzt werden, um zu verhindern, dass das Verhalten eines Benutzers die Erfahrung eines anderen beeinträchtigt.

## Clientgesteuerte Drosselung
Wenn der Drosselungsschlüssel mithilfe eines [Richtlinienausdrucks](https://msdn.microsoft.com/library/azure/dn910913.aspx) definiert wird, legt der API-Anbieter Art und Umfang der Drosselung fest. Ein Entwickler möchte jedoch möglicherweise selbst steuern, wie er die Raten für seine Kunden begrenzt. Der API-Anbieter kann dies einrichten. Dazu führt er einen benutzerdefinierten Header ein, der es der Clientanwendung des Entwicklers ermöglicht, den Schlüssel an die API zu kommunizieren.

    <rate-limit-by-key calls="100"
              renewal-period="60"
              counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>

Auf diese Weise kann die Clientanwendung des Entwicklers festlegen, wie der Schlüssel zur Ratenbegrenzung erstellt werden soll. Mit etwas Einfallsreichtum kann ein Cliententwickler seine eigenen Ratenstufen erstellen, indem er den Benutzern Gruppen von Schlüsseln zuweist und die Schlüsselnutzung rotieren lässt.

## Zusammenfassung
Azure API Management ermöglicht Drosselungen bzw. Begrenzungen nach Rate und Kontingent, um einen API-Dienst zu schützen und Mehrwert daraus zu generieren. Die neuen Drosselungsrichtlinien ermöglichen durch ihre benutzerdefinierten Umfangsregeln eine feinere Steuerung der Drosselung, sodass Ihre Kunden noch bessere Anwendungen erstellen können. Die Beispiele in diesem Artikel veranschaulichen die Verwendung dieser neuen Richtlinien. Sie zeigen, wie Client-IP-Adressen, Benutzeridentitäten und vom Client generierte Werte zum Erstellen von Schlüsseln zur Ratenbegrenzung genutzt werden können. Es gibt jedoch noch viele andere Teile einer Nachricht, die für diesen Zweck verwendet werden können, z. B. Benutzer-Agent, URL-Pfad-Fragmente und Nachrichtengröße.

## Nächste Schritte
Bitte geben Sie uns im Disqus-Thread Feedback zu diesem Thema. Wir würden gern von Ihnen hören, welche anderen möglichen Schlüsselwerte sich in Ihren Szenarien anbieten oder bewährt haben.

## Überblicksvideo zu diesen Richtlinien
Weitere Informationen zu den in diesem Artikel behandelten Richtlinien [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) und [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) finden Sie in folgendem Video.

> [AZURE.VIDEO advanced-request-throttling-with-azure-api-management]

<!---HONumber=AcomDC_0302_2016-->