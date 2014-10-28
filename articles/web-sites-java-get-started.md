<properties linkid="develop-java-tutorials-web-site-get-started" urlDisplayName="Get started with Azure" pageTitle="Get started with Microsoft Azure Websites using Java" metaKeywords="" description="This tutorial shows you how to deploy a Java website to Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Get started with Azure and Java" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Erste Schritte mit Azure-Websites und Java

In diesem Lernprogramm erfahren Sie, wie Sie eine Website auf Microsoft Azure mithilfe von Java erstellen, indem Sie entweder die Azure-Anwendungsgalerie oder die Azure-Website-Konfigurationsbenutzeroberfläche verwenden.

Wenn Sie eine dieser Techniken verwenden möchten, beispielsweise wenn Sie Ihren Anwendungscontainer anpassen möchten, finden Sie weitere Informationen unter [Hochladen einer benutzerdefinierten Java-Website in Azure][Hochladen einer benutzerdefinierten Java-Website in Azure].

> [WACOM.NOTE] Um dieses Lernprogramm abzuschließen, benötigen Sie ein Microsoft Azure-Konto. Wenn Sie kein Konto haben, können Sie [Ihre MSDN-Abonnentenvorteile aktivieren][Ihre MSDN-Abonnentenvorteile aktivieren] oder [sich für eine kostenlose Testversion registrieren][sich für eine kostenlose Testversion registrieren].

# Erstellen einer Java-Website über die Azure-Anwendungsgalerie

Hier erfahren Sie, wie Sie die Azure-Anwendungsgalerie verwenden, um einen Java-Anwendungscontainer (entweder Apache Tomcat oder Jetty) für Ihre Website auszuwählen.

Im Folgenden wird gezeigt, wie eine Website aussieht, die mithilfe von Tomcat über die Anwendungsgalerie erstellt wurde:

![Website mit Apache Tomcat][Website mit Apache Tomcat]

Im Folgenden wird gezeigt, wie eine Website aussieht, die mithilfe von Jetty über die Anwendungsgalerie erstellt wurde:

![Website mit Jetty][Website mit Jetty]

1.  Melden Sie sich im Microsoft Azure-Verwaltungsportal an.
2.  Klicken Sie nacheinander auf **Neu**, **Berechnen**, **Website** und dann **Aus Galerie**.
3.  Wählen Sie in der Liste der Apps einen der Java-Anwendungsserver aus, beispielsweise **Apache Tomcat** oder **Jetty**.
4.  Klicken Sie auf **Weiter**.
5.  Legen Sie den URL-Namen fest.
6.  Wählen Sie eine Region aus. Zum Beispiel **USA (West)**.
7.  Klicken Sie auf **Fertig stellen**.

Nach einem kurzen Augenblick wird Ihre Website erstellt. Um die Website anzuzeigen, öffnen Sie im Azure-Verwaltungsportal die Ansicht **Websites** und warten, bis der Status **Wird ausgeführt** angezeigt wird. Klicken Sie dann auf die URL der Website.

Nachdem Sie nun die Website mit einem App-Container erstellt haben, finden Sie im Abschnitt **Nächste Schritte** Informationen zum Hochladen Ihrer Anwendung auf die Website.

# Erstellen einer Java-Website über die Azure-Konfigurationsbenutzeroberfläche

Hier erfahren Sie, wie Sie die Azure-Konfigurationsbenutzeroberfläche verwenden, um einen Java-Anwendungscontainer (entweder Apache Tomcat oder Jetty) für Ihre Website auszuwählen.

1.  Melden Sie sich im Microsoft Azure-Verwaltungsportal an.
2.  Klicken Sie nacheinander auf **Neu**, **Berechnen**, **Website** und dann **Schnellerfassung**.
3.  Legen Sie den URL-Namen fest.
4.  Wählen Sie eine Region aus. Zum Beispiel **USA (West)**.
5.  Klicken Sie auf **Fertig stellen**. Nach einem kurzen Augenblick wird Ihre Website erstellt. Um die Website anzuzeigen, öffnen Sie im Azure-Verwaltungsportal die Ansicht **Websites** und warten, bis der Status **Wird ausgeführt** angezeigt wird. Klicken Sie dann auf die URL der Website.
6.  Klicken Sie im Azure-Verwaltungsportal in der Ansicht **Websites** auf den Namen Ihrer Website, um das
    Dashboard zu öffnen.
7.  Klicken Sie auf **Konfigurieren**.
8.  Aktivieren Sie **Java** im Bereich **Allgemein**, indem Sie auf die verfügbare Version klicken.
9.  Die Optionen für den Web-Container werden angezeigt, beispielsweise Tomcat und Jetty. Wählen Sie den gewünschten Web-Container aus.
10. Klicken Sie auf **Speichern**.

Nach einem kurzen Augenblick ist Ihre Website Java-basiert. Um zu überprüfen, ob die Website Java-basiert ist, öffnen Sie im Azure-Verwaltungsportal die Ansicht **Websites** und warten, bis der Status **Wird ausgeführt** angezeigt wird. Klicken Sie dann auf die URL der Website. Beachten Sie, dass auf der Seite eine Nachricht angezeigt wird, dass die neue Website eine Java-basierte Website ist.

Nachdem Sie nun die Website mit einem App-Container erstellt haben, finden Sie im Abschnitt **Nächste Schritte** Informationen zum Hochladen Ihrer Anwendung auf die Website.

# Nächste Schritte

Sie führen nun einen Java-Anwendungsserver als Ihre Java-Website auf Azure aus. Informationen zum Hinzufügen Ihrer eigenen Anwendung oder Webseite finden Sie unter [Hinzufügen einer Anwendung oder Website zur Ihrer Java-Website][Hinzufügen einer Anwendung oder Website zur Ihrer Java-Website].

  [Hochladen einer benutzerdefinierten Java-Website in Azure]: ../web-sites-java-custom-upload
  [Ihre MSDN-Abonnentenvorteile aktivieren]: /de-de/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [sich für eine kostenlose Testversion registrieren]: /de-de/pricing/free-trial/?WT.mc_id=A261C142F
  [Website mit Apache Tomcat]: ./media/web-sites-java-get-started/tomcat.png
  [Website mit Jetty]: ./media/web-sites-java-get-started/jetty.png
  [Hinzufügen einer Anwendung oder Website zur Ihrer Java-Website]: ../web-sites-java-add-app
