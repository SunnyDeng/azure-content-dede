<properties title="How to create web test" pageTitle="How to create web test" description="Learn how to create web tests in Azure." authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-29" ms.author="awills"></tags>

# Webtests

Steht Ihre Azure-Website noch zur Verfügung? Reagiert sie ordnungsgemäß und schnell genug? Testen Sie Ihre Website regelmäßig durch Konfiguration eines Webtests. Wenn die Website ausfällt oder langsam bzw. falsch reagiert, erhalten Sie eine E-Mail-Warnung. Außerdem erhalten Sie Diagramme, aus denen Sie die Verfügbarkeit und Reaktionsfähigkeit der Site über einen längeren Zeitraum ersehen können.

![Hub durchsuchen][Hub durchsuchen]

Für jede Azure-Website, die einen Basic- oder Standard-Plan verwendet, können Sie die Überwachung der Verfügbarkeit einrichten. Sie können bis zu 3 Webtests erstellen und jeden dieser Tests von bis zu 3 geografischen Standorten aus ausführen. Änderungen der Website sind hierzu nicht erforderlich.

Außerdem können Sie Webtests während einer Bereitstellung oder während geplanter Ausfälle anhalten, so dass die Verfügbarkeit insgesamt dadurch nicht beeinträchtigt wird. Die Gesamtverfügbarkeit wird unter Einbeziehung aller Webtests und der verschiedenen ausgewählten Standorte berechnet.

## Einrichten eines Webtests

1.  Vergewissern Sie vor der Konfiguration eines Webtests, dass Ihre Website sich im Modus **Basic** oder **Standard** befindet.
2.  Wählen Sie dann den Teilbereich **Web Test** im Fenster **Web site** aus:  
    ![Webtests konfigurieren][Webtests konfigurieren]
3.  Vergeben Sie im Fenster **Create web test** einen Namen für den Webtest, und geben Sie eine URL ein, für die dieser Test ausgeführt werden soll.
    ![Webtest erstellen][Webtest erstellen]
4.  Wählen Sie dann aus den insgesamt 8 Standorten bis zu 3 aus.
5.  Spezifizieren Sie die Kriterien für einen erfolgreichen Test, insbesondere HTTP-Statuscode-Überprüfungen oder die Inhalte von Zeichenfolgen auf der Site selbst.
6.  Wählen Sie dann die Warnungseinstellungen aus, unter anderem die Empfindlichkeit und den E-Mail-Empfänger.
    ![Warnungen][Warnungen]

    -   Bei hoher Empfindlichkeit wird jedes Mal eine Warnung erzeugt, wenn an nur einem Standort ein Testfehler erkannt wird.
    -   Bei mittlerer Empfindlichkeit müssen an zumindest der Hälfte der Standorte innerhalb von 10 Minuten Fehler erkannt worden sein.
    -   Bei niedriger Empfindlichkeit müssen die Tests innerhalb von 15 Minuten an allen Standorten fehlgeschlagen sein.

Wenn Sie alle Einstellungen eingegeben haben, klicken Sie auf die Schaltfläche **Create**. Sobald der Webtest erstellt worden ist, wird er von dem (den) angegebenen Speicherort(en) aus jeweils 5 Minuten lang ausgeführt. Es kann also eine Weile dauern, bevor Daten angezeigt werden.

### Welche Funktion haben die Standorte?

Von den ausgewählten Standorten aus wird eine Anfrage an die Website gesendet, genauso, als wenn Benutzer aus verschiedenen Teilen der Welt auf die Site zugreifen würden. Wenn Ihre Site in den USA nicht mehr verfügbar, aber in Europa noch erreichbar ist, wissen Sie, dass die Problemursache im Netzwerk und nicht in Ihrem Server zu suchen ist.

### Verwenden der Erfolgskriterien

Üblicherweise werden Sie prüfen, ob der HTTP-Statuscode 200 lautet, d. h. der Server hat die URL erkannt und eine Seite übergeben.

Zur Überprüfung des Inhalts einer Zeichenfolge können Sie zwar keine Platzhalter verwenden, aber jeden beliebigen unformatierten Text eingeben.

## Meine Site ist ausgefallen!

Wenn Ihr Webtest keine Erfolgskriterien nicht übergibt, wird er als fehlgeschlagener Test gekennzeichnet und die Gesamtverfügbarkeit Ihrer Website reduziert. Fehlgeschlagene Tests (aber auch erfolgreiche Tests) werden in dem betreffenden Webtestfenster in Form eines Streudiagramms dargestellt.

![Fehlgeschlagener Test][Fehlgeschlagener Test]

Fehlgeschlagene Tests können analysiert werden, um die Fehlerursache festzustellen. Rufen Sie die Details eines fehlgeschlagenen Tests auf, laden Sie die Visual Studio-Datei mit den Webtestergebnissen herunter, öffnen Sie sie, und analysieren Sie den Test, um die Ursache für das Fehlschlagen zu ermitteln.

![In VS öffnen][In VS öffnen]

  [Hub durchsuchen]: ./media/insights-create-web-tests/Inisghts_WebTestBlade.png
  [Webtests konfigurieren]: ./media/insights-create-web-tests/Insights_ConfigurePart.png
  [Webtest erstellen]: ./media/insights-create-web-tests/Insights_CreateTest.png
  [Warnungen]: ./media/insights-create-web-tests/Inisghts_AlertCreation.png
  [Fehlgeschlagener Test]: ./media/insights-create-web-tests/Insights_FailedWebTest.png
  [In VS öffnen]: ./media/insights-create-web-tests/Insights_OpenInVS.png
