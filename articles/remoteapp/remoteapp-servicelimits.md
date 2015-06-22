<properties 
    pageTitle="Diensteinschränkungen für RemoteApp"
    description="Erfahren Sie mehr über die Einschränkungen und Standardwerte für Azure RemoteApp" 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/07/2015" 
    ms.author="elizapo" />


# Diensteinschränkungen und Standardwerte für RemoteApp

In diesem Thema werden die Diensteinschränkungen für Azure RemoteApp beschrieben.

  
## Sammlungseinschränkungen
Alle Sammlungen:

- Sammlungen pro Benutzer: 1
- Veröffentlichte Apps pro Sammlung: 5.000 

Testsammlungen:

- Testzeitraum: 30 Tage
- Testsammlungen: 2 pro Abonnement
- Testbenutzer pro Sammlung: 10 
- Testvorlagenimages: 25
 
Aktivierte (kostenpflichtige) Sammlungen:

- Kostenpflichtige Sammlungen: 3 (Sie können eine Erhöhung dieser Begrenzung anfordern.)
- Kostenpflichtige Vorlagenimages: 25

 
## Benutzerbegrenzungen

Die aufgeführten Sammlungseinschränkungen sind weiche Grenzen (d. h., sie können geändert werden). Die maximale Anzahl von Benutzern pro Sammlung ist jedoch eine harte Grenze und kann nicht geändert werden. Allerdings wird die Anzahl der Benutzer wie folgt durch den Tarif der Sammlung bestimmt:


- Basic – 800 Benutzer
- Standard – 500 Benutzer

(Die Anzahl der Benutzer, die Sie unterstützen können, wird durch die Anzahl der für die Sammlung verwendeten virtuellen Computer bestimmt. Der Tarif "Basic" umfasst 16 Benutzer pro virtuellem Computer, während der Tarif "Standard" 10 Benutzer pro virtuellem Computer umfasst.)

Um die maximale Anzahl der zugewiesenen Benutzer zu ermitteln, die Sie unterstützen können, multiplizieren Sie die Anzahl der kostenpflichtigen Benutzer pro Sammlung mit der Anzahl Ihrer Sammlungen.
  
Diese Einschränkungen ermöglichen Ihnen, in Ihrem Konto mehrere Sammlungen zu erstellen und für alle Sammlungen zusammen bis zu 5.000 gleichzeitige Benutzerverbindungen herzustellen. Wenn Sie eine größere Anzahl benötigen, können Sie eine Erhöhung anfordern.

## Weitere Werte und Einschränkungen:

- Speicherung von Benutzerdaten pro Benutzer pro Sammlung: 50 GB
- Leerlauftimeout: 4 Stunden
- Timeout nach Trennung: 4 Stunden

## Anfordern einer Erhöhung der Einschränkung
Sie können eine Erhöhung der Sammlungseinschränkungen sowie der Anzahl gleichzeitiger Benutzer anfordern. Hierzu öffnen Sie ein Supportticket und erläutern, was Sie benötigen.


Um ein Supportticket zu öffnen, gehen Sie folgendermaßen vor:

1.	Klicken Sie im Verwaltungsportal auf [Get Support](https://manage.windowsazure.com/?getsupport=true). Wenn Sie nicht angemeldet sind, werden Sie zur Eingabe Ihrer Anmeldeinformationen aufgefordert.
2.	Wählen Sie Ihr Abonnement aus.
3.	Wählen Sie als Supporttyp **Technischer** aus.
4.	Klicken Sie auf **Ticket erstellen**. 
5.	Wählen Sie in der Produktliste auf der nächsten Seite **Azure RemoteApp** aus.
6.	Wählen Sie einen **Problemtyp** aus, der Ihr Problem beschreibt.
7.	Klicken Sie auf **Continue**.
8.	Befolgen Sie die Anweisungen auf der nächsten Seite, und geben Sie dann die Details zur benötigten Erhöhung der Einschränkung ein. Wenn Sie beispielsweise Ihren Testzeitraum verlängern möchten, geben Sie ein: "Bitte erweitern Sie meinen Testzeitraum um X Tage". 
9.	Klicken Sie auf **Submit**, um das Ticket zu öffnen.

<!---HONumber=58--> 