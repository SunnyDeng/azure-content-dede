<properties linkid="dev-net-how-to-autoscaling" urlDisplayName="Autoscaling" pageTitle="Use the autoscaling application block (.NET) - Azure" metaKeywords="Azure autoscaling, Azure autoscaling C#, Azure autoscaling .NET" description="Learn how to use the Autoscaling Application for Azure. Code samples are written in C# and use the .NET API." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="How to Use the Autoscaling Application Block" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Verwenden des Anwendungsblocks für die automatische Skalierung

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem
Anwendungsblock für die automatische Skalierung im [Microsoft Enterprise Library 5.0
Integration Pack for Azure][Microsoft Enterprise Library 5.0
Integration Pack for Azure] demonstriert. Die Beispiele sind in C# geschrieben
und verwenden die .NET-API. Die behandelten Szenarien umfassen das **Hosten des
Blocks**, das **Verwenden von Einschränkungsregeln** und das **Verwenden von reaktiven Regeln**. Weitere
Informationen zum Anwendungsblock für die automatische Skalierung finden Sie im Abschnitt [Nächste Schritte][Nächste Schritte].

## Inhaltsverzeichnis

[Was ist der Anwendungsblock für die automatische Skalierung?][Was ist der Anwendungsblock für die automatische Skalierung?]
 [Konzepte][Konzepte]
 [Sammeln von Leistungsindikatordaten in der Azure-Zielanwendung][Sammeln von Leistungsindikatordaten in der Azure-Zielanwendung]
 [Einrichten einer Hostanwendung für den Anwendungsblock für die automatische Skalierung][Einrichten einer Hostanwendung für den Anwendungsblock für die automatische Skalierung]
 [Gewusst wie: Instanziieren und Ausführen der automatischen Skalierung][Gewusst wie: Instanziieren und Ausführen der automatischen Skalierung] [Gewusst wie: Definieren des Dienstmodells][Gewusst wie: Definieren des Dienstmodells]
 [Gewusst wie: Definieren der Regeln für die automatische Skalierung][Gewusst wie: Definieren der Regeln für die automatische Skalierung]
 [Gewusst wie: Konfigurieren des Anwendungsblocks für die automatische Skalierung][Gewusst wie: Konfigurieren des Anwendungsblocks für die automatische Skalierung]
 [Nächste Schritte][Nächste Schritte]

## <span id="WhatIs"></span> </a>Was ist der Anwendungsblock für die automatische Skalierung?

Mit dem Anwendungsblock für die automatische Skalierung können Sie Ihre
Windows Azure-Anwendung mit speziell dafür definierten Regeln
skalieren. Mithilfe dieser Regeln sorgen Sie dafür, dass
der Durchsatz Ihrer Azure-Anwendung bei Änderungen der
Arbeitsauslastung gleich bleibt, und steuern zugleich die Kosten, die mit dem
Hosten Ihrer Anwendung in Azure verknüpft sind. Der Block ermöglicht
nicht nur die Skalierung durch Erhöhen oder Verringern der
Anzahl der Rolleinstanzen in Ihrer Anwendung, sondern auch die
Verwendung anderer Skalierungsaktionen wie das Drosseln
bestimmter Funktionen in Ihrer Anwendung oder die Verwendung benutzerdefinierter Aktionen.

Sie können festlegen, dass der Block in einer Azure-Rolle
oder in einer lokalen Anwendung gehostet wird.

Der Anwendungsblock für die automatische Skalierung ist Teil des [Microsoft Enterprise Library 5.0 Integration Pack for Azure][Microsoft Enterprise Library 5.0
Integration Pack for Azure].

## <span id="Concepts"></span> </a>Konzepte

Im folgenden Diagramm stellt die grüne Linie die Anzahl der ausgeführten
Instanzen einer Azure-Rolle über einen Zeitraum von zwei Tagen dar. Die Anzahl der
Instanzen ändert sich mit der Zeit automatisch entsprechend einer
Reihe von Regeln für die automatische Skalierung.

![Diagramm mit Beispiel für automatische Skalierung][Diagramm mit Beispiel für automatische Skalierung]

Im Block wird das Verhalten der automatischen Skalierung für Ihre
Anwendung mit zwei Arten von Regeln definiert:

-   **Einschränkungsregeln:** Verwenden Sie eine **Einschränkungsregel**,
    wenn Sie eine obere und untere Grenze für die Anzahl
    der Instanzen festlegen möchten, z. B. wenn Sie zwischen 8:00 und
    10:00 Uhr morgens mindestens vier und maximal sechs Instanzen wünschen. Im Diagramm stellen die rote und
    die blaue Linie die Einschränkungsregeln dar. An Punkt **A** im Diagramm
    nimmt die Mindestanzahl an Rolleninstanzen z. B. von zwei auf vier zu,
    damit die während dieser Zeit zu erwartende Zunahme der
    Arbeitsauslastung der Anwendung bewältigt werden kann. An Punkt **B** im Diagramm
    wird verhindert, dass die Anzahl an Rolleninstanzen auf mehr als fünf
    zunimmt, um die laufenden Kosten der Anwendung zu steuern.

-   **Reaktive Regeln:** Um die Anzahl der Rolleninstanzen an
    unvorhersehbare Änderungen der Nachfrage anpassen zu können, verwenden Sie
    **reaktive Regeln**. An Punkt **C** im Diagramm reduziert der Block
    die Anzahl an Rolleninstanzen entsprechend der verringerten
    Arbeitsauslastung automatisch von vier auf drei. An Punkt **D** erkennt
    der Block eine Zunahme der Arbeitsauslastung und erhöht
    die Anzahl ausgeführter Rolleninstanzen automatisch von drei auf vier.

Die Konfigurationseinstellungen des Blocks werden an zwei Orten gespeichert:

-   **Regelspeicher:** Der Regelspeicher enthält die Unternehmenskonfiguration,
    eine Liste aller Regeln für die automatische
    Skalierung, die Sie für Ihre Azure-Anwendung definiert haben. Bei diesem Speicher handelt es sich
    in der Regel um eine XML-Datei, die sich an einem Ort befindet,
    wo sie vom Anwendungsblock für die automatische Skalierung gelesen werden kann, z. B. im Azure-Blob-Speicher oder in einer lokalen Datei.

-   **Dienstinformationsspeicher:** Im Dienstinformationsspeicher wird
    die Betriebskonfiguration, das Dienstmodell der
    Azure-Anwendung, abgelegt. Dieses Dienstmodell umfasst alle
    Informationen über Ihre Azure-Anwendung (wie Rollennamen
    und Speicherkontodetails), die der Block
    benötigt, um Datenpunkte in der Azure-Zielanwendung
    sammeln und Skaliervorgänge durchführen zu können.

## <span id="PerfCounter"></span> </a>Sammeln von Leistungsindikatordaten in der Azure-Zielanwendung

Bei der Definition reaktiver Regeln können Leistungsindikatordaten
verwendet werden. Eine reaktive Regel kann z. B. die CPU-Auslastung
einer Azure-Rolle überwachen, um festzustellen, ob der
Block einen Skaliervorgang auslösen soll. Der Block liest Leistungsindikatordaten
aus der Azure-Diagnosetabelle mit dem Namen
**WADPerformanceCountersTable** im Azure-Speicher.

Standardmäßig schreibt Azure keine Leistungsindikatordaten in die
Azure-Diagnosetabelle im Azure-Speicher. Um diese Daten
zu speichern, müssen Sie daher die Rollen ändern, deren
Leistungsindikatordaten Sie sammeln möchten. Ausführliche Informationen zum
Aktivieren von Leistungsindikatoren in Ihrer Anwendung finden Sie unter [Verwenden von Leistungsindikatoren in Azure][Verwenden von Leistungsindikatoren in Azure].

## <span id="CreateHost"></span> </a>Einrichten einer Hostanwendung für den Anwendungsblock für die automatische Skalierung

Sie können festlegen, dass der Anwendungsblock für die
automatische Skalierung in einer Azure-Rolle oder in einer lokalen Anwendung gehostet wird. Der Anwendungsblock für die
automatische Skalierung wird in der Regel in einer von der
automatisch zu skalierenden Zielanwendung separaten Anwendung gehostet. Dieser Abschnitt enthält
Richtlinien zum Konfigurieren der Hostanwendung.

### Abrufen des NuGet-Pakets für den Anwendungsblock für die automatische Skalierung

Bevor Sie den Anwendungsblock für die automatische Skalierung in
Ihrem Visual Studio-Projekt verwenden können, müssen Sie die Binärdateien
für den Anwendungsblock für die automatische Skalierung beziehen und Verweise darauf in Ihr Projekt einfügen. Die
NuGet Visual Studio-Erweiterung ermöglicht eine problemlose Installation
und Aktualisierung von Bibliotheken und Tools in Visual Studio und Visual Web Developer. Mit dem
NuGet-Paket für den Anwendungsblock für die automatische Skalierung kommen
Sie am bequemsten an die entsprechenden APIs. Weitere Informationen über **NuGet** sowie die
Installation und Verwendung der **NuGet** Visual Studio-Erweiterung finden Sie auf der [NuGet][NuGet]
-Website.

Gehen Sie nach der Installation des NuGet-Paket-Managers wie folgt vor, um
das NuGet-Paket für die automatische Skalierung in Ihrer Anwendung zu installieren:

1.  Öffnen Sie das Fenster der **NuGet-Paket-Manager-Konsole**. Wählen Sie im Menü **Extras**
     den Eintrag **Bibliothekspaket-Manager**
     und danach **Paket-Manager-Konsole** aus.

2.  Geben Sie im Fenster der NuGet-Paket-Manager-Konsole den folgenden
    Befehl ein:

        PM> Install-Package EnterpriseLibrary.WindowsAzure.Autoscaling

Wenn Sie das NuGet-Paket installieren, wird Ihr Projekt mit allen
Assemblys und Referenzen aktualisiert, die für die Verwendung des
Anwendungsblocks für die automatische Skalierung benötigt werden. Ihr Projekt umfasst jetzt die XML-Schemadateien
mit Regeldefinitionen für die automatische Skalierung und Informationen für den Dienst für die automatische Skalierung.
Zum Projekt gehört jetzt auch eine Infodatei mit wichtigen
Informationen über den Anwendungsblock für die automatische Skalierung:

![Vom NuGet-Paket für die automatische Skalierung konfigurierte Dateien][Vom NuGet-Paket für die automatische Skalierung konfigurierte Dateien]

### Festlegen des Zielframeworks auf .NET Framework 4

Ihr Projekt muss .NET Framework 4 als Ziel haben. So ändern oder
überprüfen Sie das Zielframework:

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen, und wählen Sie
    **Eigenschaften** aus.

2.  Stellen Sie im Fenster "Eigenschaften" auf der Registerkarte **Anwendung** sicher, dass als Zielframework **.NET Framework 4** eingestellt ist.

    ![image][image]

### Hinzufügen von Namespace-Verweisen

Fügen Sie am Anfang aller C#-Dateien, in denen Sie programmgesteuert
auf den Anwendungsblock für die automatische Skalierung zugreifen
möchten, die folgenden Namespace-Codedeklarationen hinzu:

    using Microsoft.Practices.EnterpriseLibrary.Common.Configuration;
    using Microsoft.Practices.EnterpriseLibrary.WindowsAzure.Autoscaling;

## <span id="Instantiate"></span> </a>Gewusst wie: Instanziieren und Ausführen der automatischen Skalierung

Verwenden Sie die Methode **IServiceLocator.GetInstance**, um
die automatische Skalierung zu instanziieren, und rufen Sie anschließend die
Methode **Autoscaler.Start** auf, um die **automatische Skalierung** auszuführen.

    Autoscaler scaler =
        EnterpriseLibraryContainer.Current.GetInstance<Autoscaler>();
    scaler.Start();

## <span id="DefineServiceModel"></span> </a>Gewusst wie: Definieren des Dienstmodells

In der Regel speichern Sie Ihr Dienstmodell (eine Beschreibung Ihrer
Windows Azure-Umgebung, die Informationen über Abonnements, gehostete
Dienste, Rollen und Speicherkonten umfasst) in einer XML-Datei. Eine Kopie des
Schemas dieser XML-Datei finden Sie in
Ihrem Projekt in der Datei **AutoscalingServiceModel.xsd**. In Visual Studio
stellt dieses Schema IntelliSense und Prüfung bereit, wenn
Sie die XML-Datei des Dienstmodells bearbeiten.

Erstellen Sie in Ihrem Projekt eine neue XML-Datei mit dem Namen **services.xml**.

In Visual Studio müssen Sie sicherstellen, dass die Dienstmodelldatei
in den Ausgabeordner kopiert wird. Gehen Sie dazu folgendermaßen vor:

1.  Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie **Eigenschaften** aus.

2.  Legen Sie im Bereich "Eigenschaften" den Wert für **In Ausgabeverzeichnis kopieren**
     auf **Immer kopieren**
     fest.

    ![Wert für "In Ausgabeverzeichnis kopieren" festlegen][Wert für "In Ausgabeverzeichnis kopieren" festlegen]

    Das folgende Codebeispiel enthält ein Beispiel für ein Dienstmodell in der Datei **services.xml**:

    <?xml version="1.0" encoding="utf-8" ?>
    <serviceModel xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/serviceModel">
      <subscriptions>
        <subscription name="[subscriptionname]"
                      certificateThumbprint="[managementcertificatethumbprint]"
                      subscriptionId="[subscriptionid]"
                      certificateStoreLocation="CurrentUser"
                      certificateStoreName="My">
          <services>
            <service dnsPrefix="[hostedservicednsprefix]" slot="Staging">
              <roles>
                <role alias="AutoscalingApplicationRole"
                      roleName="[targetrolename]"
                      wadStorageAccountName="targetstorage"/>
              </roles>
            </service>
          </services>
          <storageAccounts>
            <storageAccount alias="targetstorage"
              connectionString="DefaultEndpointsProtocol=https;AccountName=[storageaccountname];AccountKey=[storageaccountkey]">
            </storageAccount>
          </storageAccounts>
        </subscription>
      </subscriptions>
    </serviceModel>

Ersetzen Sie die Werte in eckigen Klammern durch spezifische Werte
für Ihre Umgebung und Zielanwendung. Viele dieser Werte finden Sie,
wenn Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] anmelden.

Melden Sie sich beim Verwaltungsportal an.

-   **[subscriptionname]:** Wählen Sie einen Anzeigenamen,
    um auf das Azure-Abonnement zu verweisen, das die
    Anwendung enthält, in der Sie die automatische Skalierung verwenden möchten.

-   **[subscriptionid]:** Die eindeutige ID
    des Azure-Abonnements, das die Anwendung enthält,
    in der Sie die automatische Skalierung verwenden möchten.

    1.  Klicken Sie im Azure-Verwaltungsportal auf
        **Cloud-Dienste**.

    2.  Klicken Sie in der Liste der Cloud-Dienste auf den Dienst, der
        die Anwendung hostet, in der Sie die automatische Skalierung verwenden möchten. Im
        Bereich "Auf einen Blick" auf der rechten Seite
        wird die **Abonnement-ID** angezeigt.

        ![image][1]

    -   **[hostedservicednsprefix]:** Das DNS-Präfix des gehosteten Diensts, in dem Sie die automatische Skalierung verwenden möchten.

    1.  Klicken Sie im Azure-Verwaltungsportal auf
        **Cloud-Dienste**.

    2.  Suchen Sie in der Liste der Cloud-Dienste den Dienst, der die
        Anwendung hostet, in der Sie die automatische Skalierung verwenden möchten. Der Name des
        Cloud-Diensts ist das **DNS-Präfix**.

        ![image][2]

    -   **[targetrolename]:** Der Name der Rolle, die das Ziel der Regeln für die automatische Skalierung ist.

    1.  Klicken Sie im Azure-Verwaltungsportal auf
        **Cloud-Dienste**.

    2.  Klicken Sie in der Liste der Cloud-Dienste auf den Dienst, der die
        Anwendung hostet, in der Sie die automatische Skalierung
        verwenden möchten. Klicken Sie anschließend auf **Instanzen**. In der Spalte \*\*Rolle\*\* wird der Name der
        Zielrolle angezeigt.

        ![image][3]

    -   **[storageaccountname]** und **[storageaccountkey]:** Der Name des Azure-Speicherkontos, das Sie für die Azure-Zielanwendung verwenden.

    1.  Klicken Sie im Azure-Verwaltungsportal auf
        **Speicher**.

    2.  Wählen Sie in der Liste der Speicherkonten das Speicherkonto aus, das Sie verwenden. In der Spalte
        **Name** wird der Name
        **Name** angezeigt.

    3.  Klicken Sie unten auf dem Bildschirm auf die Schaltfläche **Schlüssel verwalten**,
        um den primären Zugriffsschlüssel zu erhalten.

        ![image][4]

    -   **[managementcertificatethumbprint]:** Der **Fingerabdruck** des Verwaltungszertifikats, mit dem der Block Skalierungsanfragen für die Zielanwendung sichert.

    1.  Klicken Sie im Azure-Verwaltungsportal auf
        **Einstellungen**.

    2.  In der Spalte **Fingerabdruck** wird der **Fingerabdruck** angezeigt.

        ![image][5]

Weitere Informationen zum Inhalt der Dienstmodelldatei finden
Sie unter [Speichern der Dienstinformationsdaten][Speichern der Dienstinformationsdaten].

## <span id="DefineAutoscalingRules"></span> </a>Gewusst wie: Definieren der Regeln für die automatische Skalierung

Normalerweise speichern Sie die Regeln für die automatische Skalierung,
die die Anzahl der Rolleninstanzen in der Zielanwendung steuern, in einer XML-Datei. Eine Kopie des
Schemas dieser XML-Datei finden Sie in Ihrem Projekt in der Datei
**AutoscalingRules.xsd**. In Visual Studio stellt dieses Schema
IntelliSense und Prüfung bereit, wenn Sie die XML-Datei bearbeiten.

Erstellen Sie in Ihrem Projekt eine neue XML-Datei mit dem Namen **rules.xml**.

In Visual Studio müssen Sie sicherstellen, dass die Regeldatei in den
Ausgabeordner kopiert wird. Gehen Sie dazu folgendermaßen vor:

1.  Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie **Eigenschaften** aus.

2.  Legen Sie im Bereich "Eigenschaften" den Wert für **In Ausgabeverzeichnis kopieren**
     auf **Immer kopieren**
     fest.

Das folgende Codebeispiel enthält ein Beispiel für einen Regelsatz in der Datei **rules.xml**
:

    <?xml version="1.0" encoding="utf-8" ?>
    <rules xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/rules">
      <constraintRules>
        <rule name="default" enabled="true" rank="1" description="The default constraint rule">
          <actions>
            <range min="2" max="6" target="AutoscalingApplicationRole"/>
          </actions>
        </rule>
      </constraintRules>
      <reactiveRules>
        <rule name="ScaleUpOnHighUtilization" rank="10" description="Scale up the web role" enabled="true" >
          <when>
            <any>
               <greaterOrEqual operand="WebRoleA_CPU_Avg_5m" than="60"/>
            </any>
          </when>
          <actions>
            <scale target="AutoscalingApplicationRole" by="1"/>
          </actions>
        </rule>
        <rule name="ScaleDownOnLowUtilization" rank="10" description="Scale up the web role" enabled="true" >
          <when>
            <all>
              <less operand="WebRoleA_CPU_Avg_5m" than="60"/>
            </all>
          </when>
          <actions>
            <scale target="AutoscalingApplicationRole" by="-1"/>
          </actions>
        </rule>
      </reactiveRules>
      <operands>
        <performanceCounter alias="WebRoleA_CPU_Avg_5m"
          performanceCounterName="\Processor(_Total)\% Processor Time"
          source ="AutoscalingApplicationRole"
          timespan="00:05:00" aggregate="Average"/>
      </operands>
    </rules>

Dieses Beispiel enthält drei Regeln für die automatische Skalierung (eine
**Einschränkungsregel** und zwei **reaktive Regeln**),
die ein Ziel namens **AutoscalingApplicationRole** bearbeiten. Dabei
handelt es sich um den Alias einer Rolle, die im **Dienstmodell** definiert ist:

-   Die Einschränkungsregel ist immer aktiv und legt die Mindestanzahl
    an Rolleninstanzen auf zwei und die Höchstanzahl an Rolleninstanzen
    auf sechs fest.

-   Die beiden reaktiven Regeln verwenden einen **Operanden**
    namens **WebRoleA\_CPU\_Avg\_5m**, der die
    durchschnittliche CPU-Auslastung während der letzten
    fünf Minuten für eine Azure-Rolle mit dem Namen **AutoscalingApplicationRole** prüft. Diese Rolle ist im
    **Dienstmodell** definiert.

-   Die reaktive Rolle mit dem Namen **ScaleUpOnHighUtilization** erhöht
    die Zahl der Instanzen der Zielrolle um eins, wenn die
    durchschnittliche CPU-Auslastung während der letzten fünf Minuten
    größer als oder gleich 60 % war.

-   Die reaktive Rolle mit dem Namen **ScaleDownOnLowUtilization** verringert
    die Zahl der Instanzen der Zielrolle um eins, wenn die
    durchschnittliche CPU-Auslastung während der letzten fünf Minuten unter 60 % lag.

## <span id="Configure"></span> </a>Gewusst wie: Konfigurieren des Anwendungsblocks für die automatische Skalierung

Nachdem Sie das Dienstmodell und die Regeln für die automatische
Skalierung definiert haben, müssen Sie den Anwendungsblock für die automatische Skalierung konfigurieren, um das Modell und die Regeln verwenden zu können. Diese
Informationen zur Betriebskonfiguration werden in der
Anwendungskonfigurationsdatei gespeichert.

Standardmäßig erwartet der Anwendungsblock für die automatische
Skalierung, dass die Regeln für die automatische Skalierung und das Dienstmodell in Azure-Blobs gespeichert sind. In diesem
Beispiel konfigurieren Sie den Block so, dass sie aus dem lokalen
Dateisystem geladen werden.

### Konfigurieren des Anwendungsblocks für die automatische Skalierung in der Hostanwendung

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Datei
    **App.config**, und klicken Sie anschließend auf **Edit Configuration File**.

2.  Klicken Sie im Menü **Blocks** auf **Add Autoscaling Settings**:
    ![Bild][Bild]

3.  Erweitern Sie **Autoscaling Settings**, und klicken Sie anschließend
    auf die Auslassungszeichen (...) neben **Data Points Store Storage Account**,
    fügen Sie den **Kontonamen** und den **Kontoschlüssel**
    des Azure-Speicherkontos hinzu, in dem
    der Block die gesammelten Datenpunkte speichert (siehe [Gewusst wie: Definieren des Dienstmodells][Gewusst wie: Definieren des Dienstmodells], wenn Sie nicht wissen, wo
    Sie diese Werte finden), und klicken Sie dann auf **OK**:

    ![image][6]

4.  Erweitern Sie den Abschnitt **Autoscaling Settings**,
    sodass die Abschnitte **Rules Store** und **Service Information Store** angezeigt werden. Standardmäßig
    sind sie für die Verwendung des Azure-Blob-Speichers konfiguriert:
    ![Bild][7]

5.  Klicken Sie auf das Pluszeichen (+) neben **Rules Store**, zeigen
    Sie auf **Set Rules Store**, und klicken Sie dann auf
    **Use Local File Rules Store** und anschließend auf **Yes**.

6.  Geben Sie in das Feld **File Name** den Dateinamen **rules.xml** ein. Dies ist der Name
    der Datei, welche die Regeln für die automatische Skalierung enthält:
    ![Bild][8]

7.  Klicken Sie auf das Pluszeichen (+) neben **Service Information Store**,
    zeigen Sie auf **Set Service Information Store**,
    und klicken Sie dann auf **Use Local File Service Information Store** und anschließend auf **Yes**.

8.  Geben Sie in das Feld **File Name** den Dateinamen **services.xml** ein. Dies ist der Name
    der Datei, welche die Regeln für die automatische Skalierung enthält:
    ![Bild][9]

9.  Klicken Sie im Fenster "Enterprise Library Configuration" im Menü **File**
     auf **Save**, um die Änderungen an der Konfiguration zu speichern. Klicken Sie
    danach im Fenster "Enterprise Library Configuration" im Menü
    **File** auf **Exit**.

Um detaillierte Informationen über die Aktionen zu erhalten, die vom
Anwendungsblock für die automatische Skalierung ausgeführt
werden, müssen Sie die von diesem geschriebenen Protokollmeldungen erfassen. Wenn Sie den Block z. B. in einer Konsolenanwendung
hosten, können Sie die Protokollmeldungen in Visual Studio im
Ausgabefenster anzeigen. In den folgenden Abschnitten wird erläutert, wie Sie dieses
Verhalten konfigurieren.

### Konfigurieren der Protokollierung in der Hostanwendung des Anwendungsblocks für die automatische Skalierung

1.  Doppelklicken Sie in Visual Studio im Projektmappen-Explorer auf die
    Datei **App.config**, um sie im Editor zu öffnen. Fügen Sie dann wie im folgenden Beispiel den Abschnitt
    **system.diagnostics** hinzu:

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
          ...
          <system.diagnostics>
            <sources>
              <sourcename="Autoscaling General" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" />
              <sourcename="Autoscaling Updates" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" />
            </sources>
            <switches>
              <addname="SourceSwitch" value="Verbose, Information, Warning, Error, Critical" />
            </switches>
          </system.diagnostics>
        </configuration>

2.  Speichern Sie die Änderungen.

Jetzt können Sie die Hostkonsolenanwendung für den Anwendungsblock
für die automatische Skalierung ausführen und beobachten, wie
die Regeln für die automatische Skalierung in der Azure-Zielanwendung arbeiten. Wenn Sie die Hostkonsolenanwendung ausführen,
werden in Visual Studio im Ausgabefenster Meldungen ähnlich den
folgenden angezeigt. Diese Protokollmeldungen helfen Ihnen, das Verhalten des Blocks
zu verstehen. Sie geben z. B. an, welche Regeln dem Block entsprechen
und welche Aktionen der Block ausführt.

    Autoscaling General Verbose: 1002 : Rule match.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "MatchingRules":[{"RuleName":"default","RuleDescription":"The default constraint rule",
    "Targets":["AutoscalingApplicationRole"]},{"RuleName":"ScaleUp","RuleDescription":"Scale up the web role",
    "Targets":[]},{"RuleName":"ScaleDown","RuleDescription":"Scale up the web role","Targets":[]}]}

    Autoscaling Updates Verbose: 3001 : The current deployment configuration for a hosted service is about to be checked
    to determine if a change is required (for role scaling or changes to settings).
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},"ScaleRequests":{"AutoscalingApplicationRole":{"Min":2,"Max":6,"AbsoluteDelta":0,
    "RelativeDelta":0,"MatchingRules":"default"}},"SettingChangeRequests":{}}

    Autoscaling Updates Verbose: 3003 : Role scaling requests for hosted service about to be submitted.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},
    "ScaleRequests":{"AutoscalingApplicationRole":
    {"Min":2,"Max":6,"AbsoluteDelta":0,"RelativeDelta":0,"MatchingRules":"default"}},
    "SettingChangeRequests":{},"InstanceChanges":{"AutoscalingApplicationRole":{"CurrentValue":1,"DesiredValue":2}},
    "SettingChanges":{}}

    Autoscaling Updates Information: 3002 : Role configuration changes for deployment were submitted.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},"ScaleRequests":{"AutoscalingApplicationRole":{"Min":2,"Max":6,"AbsoluteDelta":0,
    "RelativeDelta":0,"MatchingRules":"default"}},"SettingChangeRequests":{},
    "InstanceChanges":{"AutoscalingApplicationRole":{"CurrentValue":1,"DesiredValue":2}},
    "SettingChanges":{},"RequestID":"f8ca3ada07c24559b1cb075534f02d44"}

## <span id="NextSteps"></span> </a>Nächste Schritte

Da Sie jetzt die Grundlagen der Verwendung des Anwendungsblocks für
die automatische Skalierung kennengelernt haben, folgen Sie diesen
Links, um zu erfahren, wie Sie komplexere Szenarien mit automatischer Skalierung implementieren können:

-   [Hosten des Anwendungsblocks für die automatische Skalierung in einer Workerrolle][Hosten des Anwendungsblocks für die automatische Skalierung in einer Workerrolle]
-   [Implementieren des Drosselungsverhaltens][Implementieren des Drosselungsverhaltens]
-   [Grundlegendes zu Regelrängen und -abstimmung][Grundlegendes zu Regelrängen und -abstimmung]
-   [Erweitern und Ändern des Anwendungsblocks für die automatische Skalierung][Erweitern und Ändern des Anwendungsblocks für die automatische Skalierung]
-   [Verwenden des Optimizing Stabilizer zur Verhinderung hochfrequenter Oszillation und zur Optimierung von Kosten][Verwenden des Optimizing Stabilizer zur Verhinderung hochfrequenter Oszillation und zur Optimierung von Kosten]
-   [Verwenden von Benachrichtigungen und manueller Skalierung][Verwenden von Benachrichtigungen und manueller Skalierung]
-   [Definieren von Skalierungsgruppen][Definieren von Skalierungsgruppen]
-   [Verwenden der WASABiCmdlets zum Bearbeiten des Blocks über Windows PowerShell][Verwenden der WASABiCmdlets zum Bearbeiten des Blocks über Windows PowerShell]
-   [Entwicklerhandbuch für Enterprise Library 5.0 Integration Pack for Azure][Entwicklerhandbuch für Enterprise Library 5.0 Integration Pack for Azure]
-   [Wie Sage die Azure-Hostingkosten mithilfe der automatischen Skalierung reduziert][Wie Sage die Azure-Hostingkosten mithilfe der automatischen Skalierung reduziert]
-   [Verringern der TechNet- und MSDN-Hostingkosten und der Umweltbelastung mit automatischer Skalierung in Azure][Verringern der TechNet- und MSDN-Hostingkosten und der Umweltbelastung mit automatischer Skalierung in Azure]

  [Microsoft Enterprise Library 5.0 Integration Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=235134
  [Nächste Schritte]: #NextSteps
  [Konzepte]: #Concepts
  [Sammeln von Leistungsindikatordaten in der Azure-Zielanwendung]: #PerfCounter
  [Einrichten einer Hostanwendung für den Anwendungsblock für die automatische Skalierung]: #CreateHost
  [Gewusst wie: Instanziieren und Ausführen der automatischen Skalierung]: #Instantiate
  [Gewusst wie: Definieren des Dienstmodells]: #DefineServiceModel
  [Gewusst wie: Definieren der Regeln für die automatische Skalierung]: #DefineAutoscalingRules
  [Gewusst wie: Konfigurieren des Anwendungsblocks für die automatische Skalierung]: #Configure
  [Diagramm mit Beispiel für automatische Skalierung]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling01.png
  [Verwenden von Leistungsindikatoren in Azure]: http://www.windowsazure.com/de-de/develop/net/common-tasks/performance-profiling/
  [NuGet]: http://nuget.org/
  [Vom NuGet-Paket für die automatische Skalierung konfigurierte Dateien]: ./media/cloud-services-dotnet-autoscaling-application-block/auotscaling02.png
  [image]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling03.png
  [Wert für "In Ausgabeverzeichnis kopieren" festlegen]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling04.png
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [1]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling05.png
  [2]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling06.png
  [3]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling07.png
  [4]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling08.png
  [5]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling09.png
  [Speichern der Dienstinformationsdaten]: http://msdn.microsoft.com/de-de/library/hh680878(PandP.50).aspx
  [Bild]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling10.png
  [6]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling11.png
  [7]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling12.png
  [8]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling13.png
  [9]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling14.png
  [Hosten des Anwendungsblocks für die automatische Skalierung in einer Workerrolle]: http://msdn.microsoft.com/de-de/library/hh680914(PandP.50).aspx
  [Implementieren des Drosselungsverhaltens]: http://msdn.microsoft.com/de-de/library/hh680896(PandP.50).aspx
  [Grundlegendes zu Regelrängen und -abstimmung]: http://msdn.microsoft.com/de-de/library/hh680923(PandP.50).aspx
  [Erweitern und Ändern des Anwendungsblocks für die automatische Skalierung]: http://msdn.microsoft.com/de-de/library/hh680889(PandP.50).aspx
  [Verwenden des Optimizing Stabilizer zur Verhinderung hochfrequenter Oszillation und zur Optimierung von Kosten]: http://msdn.microsoft.com/de-de/library/hh680951(PandP.50).aspx
  [Verwenden von Benachrichtigungen und manueller Skalierung]: http://msdn.microsoft.com/de-de/library/hh680885(PandP.50).aspx
  [Definieren von Skalierungsgruppen]: http://msdn.microsoft.com/de-de/library/hh680902(PandP.50).aspx
  [Verwenden der WASABiCmdlets zum Bearbeiten des Blocks über Windows PowerShell]: http://msdn.microsoft.com/de-de/library/hh680938(PandP.50).aspx
  [Entwicklerhandbuch für Enterprise Library 5.0 Integration Pack for Azure]: http://msdn.microsoft.com/de-de/library/hh680949(PandP.50).aspx
  [Wie Sage die Azure-Hostingkosten mithilfe der automatischen Skalierung reduziert]: http://msdn.microsoft.com/de-de/library/jj838716(PandP.50).aspx
  [Verringern der TechNet- und MSDN-Hostingkosten und der Umweltbelastung mit automatischer Skalierung in Azure]: http://msdn.microsoft.com/de-de/library/jj838718(PandP.50).aspx
