<properties urlDisplayName="Monitor with AppDynamics" pageTitle="Verwenden von AppDynamics mit Azure" metaKeywords="" description="Hier erfahren Sie, wie Sie AppDynamics f&uuml;r Azure verwenden" metaCanonical="" services="cloud-services" documentationCenter="" title="Verwenden von AppDynamics f&uuml;r Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi" />

# Verwenden von AppDynamics für Azure

In diesem Thema wird beschrieben, wie Sie mit AppDynamics für Azure beginnen können.

## Inhaltsverzeichnis

-   [Was ist AppDynamics?][Was ist AppDynamics?]
-   [Voraussetzungen][Voraussetzungen]
-   [Registrieren für ein AppDynamics-Konto][Registrieren für ein AppDynamics-Konto]
-   [Herunterladen des .NET Agent von AppDynamics][Herunterladen des .NET Agent von AppDynamics]
-   [Hinzufügen des .NET Agent zu Azure-Rollen und Modifizieren des Starts][Hinzufügen des .NET Agent zu Azure-Rollen und Modifizieren des Starts]
-   [Veröffentlichen der AppDynamics-instrumentierten Anwendung für Azure][Veröffentlichen der AppDynamics-instrumentierten Anwendung für Azure]
-   [Überwachen der Anwendung][Überwachen der Anwendung]

## <span id="what"></span></a>Was ist AppDynamics?

AppDynamics stellt eine Lösung zur Überwachung der Anwendungsleistung dar, mit der Sie Folgendes erreichen können:

-   Probleme, wie etwa langsame und ausgesetzte Benutzeranforderungen oder Fehler, in einer Produktionsumgebung erkennen

-   Hauptursache derartiger Probleme beheben und isolieren

In AppDynamics liegen zwei Komponenten vor:

-   Anwendungsserver-Agent: Der .NET Agent des Anwendungsservers sammelt Daten von Ihren Servern. Sie führen einen separaten Agent zu jeder Rolleninstanz aus, die Sie überwachen möchten. Sie können den Agent vom AppDynamics-Portal herunterladen.

-   AppDynamics-Controller: Der Agent sendet Informationen an einen per AppDynamics-Controller gehosteten Dienst unter Azure. Melden Sie sich mit einer webbrowserbasierten Konsole an einem Controller an, um die Anwendung zu überwachen, zu analysieren und einer Fehlerbehebung zu unterziehen.

    ![AppDynamics-Diagramm][AppDynamics-Diagramm]

## <span id="prereq"></span></a>Voraussetzungen

-   Visual Studio 2010 oder höher
-   Eine zu überwachende Visual Studio-Lösung
-   Azure SDK
-   Azure-Konto

## <span id="register"></span></a>Registrieren für ein AppDynamics-Konto

So registrieren Sie sich für ein Azure-Konto zu AppDynamics:

1.  Klicken Sie auf **Try Free** oder **Sign Up** für AppDynamics im Azure Marketplace unter [][]<https://datamarket.azure.com/browse/Applications></a>.

    Wenn Sie **Sign Up** wählen, erhalten Sie eine kostenlose Version von AppDynamics Pro für Azure mit vollständiger Funktionalität. Diese wird nach 30 Tagen in eine kostenlose Version von AppDynamics Lite für Azure mit begrenzter Funktionalität herabgestuft. Bei dieser Option brauchen Sie keine Kreditkarte bereithalten. Sie können jederzeit ein Upgrade auf AppDynamics Pro für Azure durchführen.

    Wenn Sie **Try Free** wählen, erhalten Sie eine kostenlose Version von AppDynamics Pro für Azure mit vollständiger Funktionalität. Bei dieser Option müssen Sie eine Kreditkarte bereithalten. Nach 30 Tagen wird Ihr Kreditkartenkonto für die dauerhafte Benutzung von AppDynamics Pro für Azure belastet, es sei denn, Sie kündigen Ihr Abonnement.

    Sie benötigen je eine Agent-Lizenz für jede Rolleninstanz, die Sie überwachen möchten. Eine Site, auf der 2 Web-Rolleninstanzen und 2 Worker-Rolleninstanzen ausgeführt werden, erfordert beispielsweise 4 Agent-Lizenzen.

2.  Auf der Registrierungsseite müssen Sie Ihre Benutzerdaten, ein Kennwort, eine E-Mail-Adresse, den Firmennamen und den Namen der Anwendung angeben, die Sie überwachen, wenn Sie sie mit Azure veröffentlichen.

3.  Klicken Sie auf **Jetzt registrieren**.

    Sie erhalten die Ihrem Konto zugeordneten AppDynamics-Anmeldeinformationen und die AppDynamics Controller-URL (Host und Port) in einer E-Mail an jene Adresse, die Sie auf der Anmeldeseite angegeben haben. Speichern Sie diese Informationen.

    Wenn Sie bereits AppDynamics-Anmeldeinformationen zu einem anderen Produkt besitzen, können Sie sich auch mit diesen Daten anmelden.

    Sie erhalten außerdem eine Homepage zum AppDynamics-Konto.

    Sie werden auf die Homepage zum Ihrem AppDynamics-Konto geleitet.

    Die Homepage zu Ihrem AppDynamics-Konto umfasst Folgendes:

    -   Controller-URL: Von hier melden Sie sich bei Ihrem Konto in dem per AppDynamics-Controller gehosteten Dienst an.

    -   AppDynamics-Anmeldeinformationen: Kontoname und Zugriffsschlüssel

    -   Link zur AppDynamics-Downloadsite: Von hier laden Sie den AppDynamics .NET Agent herunter.

    -   Anzahl der verbleibenden Tage Ihres Pro-Testabonnements

    -   Links zu Einarbeitungsvideos und -dokumentationen für AppDynamics

    Sie können jederzeit auf die Homepage Ihres AppDynamics-Kontos gelangen, indem Sie die zugehörige URL in einen Webbrowser eingeben und sich mit Ihren AppDynamics-Anmeldeinformationen anmelden.

## <span id="download"></span></a>Herunterladen des .NET Agent von AppDynamics

1.  Navigieren Sie zur AppDynamics-Download-Site. Die URL dafür finden Sie in der Begrüßungs-E-Mail und auf der Homepage Ihres AppDynamics-Kontos.

2.  Melden Sie sich mit dem Kontonamen und dem Zugriffsschlüssel von AppDynamics an.

3.  Laden Sie die Datei mit dem Namen AppDynamicsdotNetAgentSetup64.msi herunter. Führen Sie die Datei nicht aus.

## <span id="addagent"></span></a>Hinzufügen des .NET Agent zu Azure-Rollen und Modifizieren des Starts

In diesem Schritt werden die Rollen in Ihrer Visual Studio-Lösung zur Überwachung durch AppDynamics instrumentiert. Ein herkömmliches von einem Assistenten unterstütztes Windows-Installationsverfahren ist für die Verwendung von AppDynamics für Azure nicht erforderlich.

1.  Erstellen Sie entweder ein neues Azure-Projekt in Visual Studio, oder öffnen Sie ein vorhandenes Azure-Projekt.

2.  Wenn Sie ein neues Projekt erstellen, fügen Sie die Web- und/oder Worker-Rollenprojekte zur Lösung hinzu.

3.  Fügen Sie die heruntergeladene .NET Agent .msi-Datei zu jedem Web- und Worker-Rollenprojekt hinzu, welches Sie überwachen möchten.

    Beachten Sie, dass, während jedes *role project* über eine einzelne beigefügte .NET Agent .msi verfügt, jede *role instance* im Projekt eine separate .NET Agent-Lizenz erfordert.

4.  Fügen Sie zu jedem Web- und Worker-Rollenprojekt, das Sie überwachen möchten, eine Textdatei mit dem Namen startup.cmd hinzu, und fügen Sie in diese die folgenden Zeilen ein:

        if defined COR_PROFILER GOTO END 
        SETLOCAL EnableExtensions 
        REM Run the agent installer 
        AppDynamicsdotNetAgentSetup64.msi AD_Agent_Environment=Azure AD_Agent_ControllerHost=%1 AD_Agent_ControllerPort=%2 AD_Agent_AccountName=%3 AD_Agent_AccessKey=%4 AD_Agent_ControllerApplication=%5 /quiet /log d:\adInstall.log  
        SHUTDOWN /r /c "Rebooting the instance after the installation of AppDynamics Monitoring Agent" /t 0 
        GOTO END   
        :END

5.  Stellen Sie bei jeder Web- und Worker-Rolle, die Sie überwachen möchten, die Eigenschaft **In Ausgabeverzeichnis kopieren** für die AppDynamics agent .msi-Datei ein, und setzen Sie sie für die Datei startup.cmd auf **Immer kopieren**.

    ![Immer kopieren][Immer kopieren]

6.  Fügen Sie in der Datei ServiceDefinition.csdef für das Azure-Projekt ein Startaufgabenelement hinzu, das die startup.cmd mit Parametern für jedes WorkerRole- und WebRole-Element aufruft.

    Fügen Sie die folgenden Zeilen hinzu:

        <Startup>
        <Task commandLine="startup.cmd [your_controller_host] [your_controller_port] [your_account_name] [your_access_key] [your_application_name]" executionContext="elevated" taskType="simple"/>
        </Startup>

    Hinweis:

    -   *your controller host* und *your controller port* stehen für den Controller-Host und den Controller-Port, die Ihrem Konto zugeordnet sind. Außerdem stehen *your account name* und *your access key* für die Anmeldeinformationen, die Ihnen von AppDynamics zugewiesen wurden. Diese Informationen sind in der E-Mail, die Ihnen beim Registrieren für AppDynamics gesendet wurde, und ebenso auf Ihrer AppDynamics-Homepage enthalten. Weitere Informationen finden Sie unter [Registrieren für ein AppDynamics-Konto][Registrieren für ein AppDynamics-Konto].

    -   *your application name* steht für den Namen, den Sie für die Anwendung ausgewählt haben. Dieser Name kennzeichnet die Anwendung in der AppDynamics Controller-Schnittstelle.

    Ihre Datei ServiceDefinition.csdef sieht in etwa wie folgt aus:

    ![Dienstdefinition][Dienstdefinition]

## <a name="publish"></a>Veröffentlichen der AppDynamics-instrumentierten Anwendung für Azure

Bei jedem AppDynamics-instrumentierten Rollenprojekt gilt:

1.  Wählen Sie das Rollenprojekt in Visual Studio.

2.  Wählen Sie die Option zum Veröffentlichen in Azure.

## <a name="monitor"></a>Überwachen der Anwendung

1.  Melden Sie sich am AppDynamics-Controller unter der URL an, die in der Begrüßungs-E-Mail und auf der Homepage Ihres AppDynamics-Kontos aufgeführt ist.

2.  Senden Sie einige Anforderungen an die Anwendung, sodass ein gewisses Maß an zu überwachendem Datenverkehr entsteht. Warten Sie einige Minuten.

3.  Wählen Sie die Anwendung im AppDynamics-Controller aus.

4.  Überwachen Sie die Anwendung.

## <a name="learn"></a>Weitere Informationen

Suchen Sie auf der Homepage Ihres AppDynamics-Kontos nach Links zu Dokumentationen und Videos.

Die neuesten Updates zu diesem Dokument finden Sie in der WIKI-Version unter [][1]<http://docs.appdynamics.com/display/ADAZ/How+To+Use+AppDynamics+for+Windows+Azure></a>.

  [Was ist AppDynamics?]: #what
  [Voraussetzungen]: #prereq
  [Registrieren für ein AppDynamics-Konto]: #register
  [Herunterladen des .NET Agent von AppDynamics]: #download
  [Hinzufügen des .NET Agent zu Azure-Rollen und Modifizieren des Starts]: #addagent
  [Veröffentlichen der AppDynamics-instrumentierten Anwendung für Azure]: #publish
  [Überwachen der Anwendung]: #monitor
  [AppDynamics-Diagramm]: ./media/cloud-services-how-to-appdynamics/addiagram.png
  []: https://datamarket.azure.com/browse/Applications
  [Immer kopieren]: ./media/cloud-services-how-to-appdynamics/adcopyalways.png
  [Dienstdefinition]: ./media/cloud-services-how-to-appdynamics/adscreen.png
  [1]: http://docs.appdynamics.com/display/ADAZ/How+To+Use+AppDynamics+for+Windows+Azure
