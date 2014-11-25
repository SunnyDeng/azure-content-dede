<properties title="Monitor a DocumentDB Account" pageTitle="Monitor a DocumentDB account | Azure" description="Learn how to monitor your DocumentDB account for performance metrics (such as requests and server errors) and usage metrics (such as storage consumption)." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, monitor, accounts" services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="brradsev" />

# Überwachen eines DocumentDB-Kontos

Sie können Ihre DocumentDB-Konten im [Azure-Vorschauportal][Azure-Vorschauportal] überwachen. Es sind für jedes DocumentDB-Konto sowohl Leistungsmetriken (wie Anfragen und Serverfehler) als auch Nutzungsmetriken (wie verbrauchter Speicher) verfügbar.

## Themen in diesem Artikel

-   [Gewusst wie: Anzeigen von Leistungsmetriken für ein DocumentDB-Konto][Gewusst wie: Anzeigen von Leistungsmetriken für ein DocumentDB-Konto]
-   [Gewusst wie: Anpassen der Leistungsmetrikansichten für ein DocumentDB-Konto][Gewusst wie: Anpassen der Leistungsmetrikansichten für ein DocumentDB-Konto]
-   [Gewusst wie: Erstellen von nebeneinander liegenden Diagrammen mit Leistungsmetriken][Gewusst wie: Erstellen von nebeneinander liegenden Diagrammen mit Leistungsmetriken]
-   [Gewusst wie: Anzeigen von Nutzungsmetriken für ein DocumentDB-Konto][Gewusst wie: Anzeigen von Nutzungsmetriken für ein DocumentDB-Konto]
-   [Gewusst wie: Einrichten von Warnungen bei Leistungsmetriken für ein DocumentDB-Konto][Gewusst wie: Einrichten von Warnungen bei Leistungsmetriken für ein DocumentDB-Konto]
-   [Nächste Schritte][Nächste Schritte]

## <span id="metrics"></span></a>Gewusst wie: Anzeigen von Leistungsmetriken für ein DocumentDB-Konto

1.  Klicken Sie im [Azure-Vorschauportal][Azure-Vorschauportal] auf **Durchsuchen**, dann auf **DocumentDB-Konten** und anschließend auf den Namen des DocumentDB-Kontos, dessen Leistungsmetriken Sie anzeigen möchten.
2.  Innerhalb des Bereichs **Überwachung** sehen Sie standardmäßig Folgendes:

    -   Gesamtanforderungen des aktuellen Tags.
    -   Durchschnittliche Anfragen pro Sekunde des aktuellen Tags.

    ![][0]

3.  Wenn Sie auf den Bereich **Gesamtanforderungen oder durchschnittliche Anforderungen pro Sekunde** klicken, wird ein detaillierte Fenster **Metrik** geöffnet.
4.  Das Fenster „Metrik“ enthält Details zu den ausgewählten Metriken. Im oberen Teil des Fensters befindet sich ein Diagramm und darunter eine Tabelle mit einer Aggregation der Werte der ausgewählten Metriken, also beispielsweise Durchschnitt, Minimum und Maximum. Das Fenster „Metrik“ zeigt auch eine Liste von Warnung, die definiert wurden. Sie ist gefiltert nach den im aktuellen Fenster „Metrik“ anzeigten Metriken (wenn Sie eine Reihe von Warnungen haben, dann werden auf diese Weise hier nur die relevanten gezeigt).

    ![][1]

## <span id="custom"></span></a>Gewusst wie: Anpassen der Leistungsmetrikansichten für ein DocumentDB-Konto

1.  Klicken Sie zur Anpassung der in einem bestimmten Bereich angezeigten Metriken mit der rechten Maustaste auf das Metrikdiagramm und wählen Sie dann **Diagramm bearbeiten**.
    ![][2]

2.  Im Fenster **Diagramm bearbeiten** gibt es Optionen zum Ändern der in diesem Bereich angezeigt Metriken sowie deren Zeitspanne.
    ![][3]

3.  Die im Bereich angezeigten Metriken können Sie verändern, indem Sie einfach die verfügbaren Leistungsmetriken aktivieren/deaktivieren und dann unten im Fenster auf **Speichern** klicken.
4.  Die Zeitspanne ändern Sie, indem Sie eine andere wählen (z. B. **Letzte Stunder**) und dann unten im Fenster auf **Speicherne** klicken.
    ![][4]

5.  Mit der benutzerdefinierten Zeitspanne können Sie einen beliebigen Zeitraum in den letzten zwei Wochen auswählen.
6.  Wenn Sie auf **Speichern** klicken, bleiben Ihre Änderungen erhalten, bis Sie das Fenster „DocumentDB-Konto“ verlassen. Wenn Sie später zurückkehren, werden wieder die ursprünglichen Metriken und der ursprüngliche Zeitbereich angezeigt.

## <span id="create"></span></a>Gewusst wie: Erstellen von nebeneinander liegenden Diagrammen

Im Azure-Vorschauportal können Sie nebeneinander stehende Metrikdiagramme erstellen.

1.  Klicken Sie zunächst mit der rechten Maustaste auf das Diagramm, mit dem Sie beginnen möchten, und wählen Sie **Anpassen**.
    ![][5]

2.  Klicken Sie **Klonen** im Menü, um den Bereich zu kopieren.

    ![][6]

3.  Klicken Sie abschließend auf **Done** in der Symbolleiste oben im Bildschirm. Sie können diesen Bereich jetzt wie jeden anderen Metrikbereich behandeln und ihn sowie die dort angezeigte Zeitspanne anpassen. Wenn Sie das tun, werden zwei verschiedene Metrikdiagramme nebeneinander angezeigt.
    ![][7]

> Denken Sie aber daran, dass die Zeitspanne und die ausgewählten Metriken auf die Standardwerte zurückgesetzt werden, wenn Sie das Azure-Vorschauportal verlassen.

## <span id="view"></span></a>Gewusst wie: Anzeigen von Nutzungsmetriken für ein DocumentDB-Konto

1.  Klicken Sie im [Azure-Vorschauportal][Azure-Vorschauportal] auf **Durchsuchen**, dann auf **DocumentDB-Konten** und anschließend auf den Namen des DocumentDB-Kontos, dessen Nutzungsmetriken Sie anzeigen möchten.
2.  Innerhalb des Bereichs **Verwendung** sehen Sie standardmäßig Folgendes:

    -   Mit dem Konto verbrauchter Speicher
    -   Maximal verfügbarer Speicher des Kontos
    -   Nutzung von Anlagen
    -   Nutzung von Benutzern und Berechtigungen
    -   Zuteilung von Kapazitätseinheiten
    -   Nutzung von Anlagen im Konto
        ![][8]

## <span id="setup"></span></a>Gewusst wie: Einrichten von Warnungen bei Leistungsmetriken für ein DocumentDB-Konto

1.  Klicken Sie im [Azure-Vorschauportal][Azure-Vorschauportal] auf **Durchsuchen**, dann auf **DocumentDB-Konten** und anschließend auf den Namen des DocumentDB-Kontos, für das Sie Warnungen bei Leistungsmetriken einrichten möchten.
2.  Klicken Sie innerhalb des Bereichs **Vorgänge** auf den Bereich **Warnungsregeln**.
    ![][9]

3.  Klicken Sie im Fenster „Warnungsregeln“ auf **Warnung hinzufügen**.
    ![][10]

4.  Legen Sie im Fenster **Warnungsregel hinzufügen** Folgendes fest:

    -   Den Namen der Warnungsregel, die Sie einrichten möchten.
    -   Eine Beschreibung der neuen Warnungsregel.
    -   Die Metrik für die Warnungsregel.
    -   Bedingung, Schwellenwert und Zeitraum die festlegen, wann die Warnung aktiviert wird. Zum Beispiel mehr als 5 Serverfehler im Verlauf der letzten 15 Minuten.
    -   Ob der Dienstadministrator oder Coadministratoren bei Auslösung der Warnung eine E-Mail erhalten.
    -   Weitere E-Mail-Adressen für Warnmitteilungen.
        ![][11]

## <span id="next"></span></a>Nächste Schritte

-   Mehr über DocumentDB erfahren Sie in der Azure DocumentDB-Dokumentation auf [azure.com][azure.com]

<!--Anchors-->

  [Azure-Vorschauportal]: https://portal.azure.com/
  [Gewusst wie: Anzeigen von Leistungsmetriken für ein DocumentDB-Konto]: #metrics
  [Gewusst wie: Anpassen der Leistungsmetrikansichten für ein DocumentDB-Konto]: #custom
  [Gewusst wie: Erstellen von nebeneinander liegenden Diagrammen mit Leistungsmetriken]: #create
  [Gewusst wie: Anzeigen von Nutzungsmetriken für ein DocumentDB-Konto]: #view
  [Gewusst wie: Einrichten von Warnungen bei Leistungsmetriken für ein DocumentDB-Konto]: #setup
  [Nächste Schritte]: #next
  [0]: http://i.imgur.com/y7pigTT.png
  [1]: http://i.imgur.com/6rBNPBL.png
  [2]: http://i.imgur.com/tRZEHk1.png
  [3]: http://i.imgur.com/G4UTi5U.png
  [4]: ./media/documentdb-monitor-accounts/madocdb5.png
  [5]: http://i.imgur.com/vLXWftF.png
  [6]: ./media/documentdb-monitor-accounts/madocdb7.png
  [7]: ./media/documentdb-monitor-accounts/madocdb8.png
  [8]: http://i.imgur.com/sL5inOu.png
  [9]: ./media/documentdb-monitor-accounts/madocdb10.png
  [10]: ./media/documentdb-monitor-accounts/madocdb11.png
  [11]: http://i.imgur.com/Inra4Po.png
  [azure.com]: http://go.microsoft.com/fwlink/p/?LinkID=402319
