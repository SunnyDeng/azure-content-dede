<properties
   pageTitle="Konfigurieren eines Azure-Clouddienstprojekts mit mehreren Dienstkonfigurationen | Microsoft Azure"
   description="Erfahren Sie, wie Sie ein Azure-Clouddienstprojekt konfigurieren, indem Sie die Dateien ";ServiceDefinition.csdef"; und ";ServiceConfiguration.cscfg"; ändern."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/08/2015"
   ms.author="kempb" />

# Konfigurieren eines Azure-Clouddienstprojekts mit mehreren Dienstkonfigurationen

Ein Azure-Clouddienstprojekt enthält zwei Konfigurationsdateien: "Servicedefinition.csdef" und "ServiceConfiguration.cscfg". Diese Dateien sind im Paket mit Ihrer Azure-Cloudienstanwendung enthalten und werden in Azure bereitgestellt.

- Die Datei **ServiceDefinition.csdef** enthält die von der Azure-Umgebung für die Anforderungen der Azure-Cloudienstanwendung benötigten Metadaten, einschließlich der enthaltenen Rollen. Diese Datei enthält auch Konfigurationseinstellungen, die für alle Instanzen gelten. Diese Konfigurationseinstellungen können zur Laufzeit mit der Azure Service Hosting Runtime-API gelesen werden. Diese Datei kann nicht aktualisiert werden, während der Dienst in Azure ausgeführt wird.

- Von der Datei **ServiceConfiguration.cscfg** werden Werte für die Konfigurationseinstellungen festgelegt, die in der Dienstdefinitionsdatei definiert sind. Außerdem wird die Anzahl der für jede Rolle auszuführenden Instanzen angegeben. Diese Datei kann aktualisiert werden, während Ihr Clouddienst in Azure ausgeführt wird.

Die Azure Tools für Visual Studio verfügen über Eigenschaftenseiten, mit denen Sie in diesen Dateien gespeicherte Konfigurationseinstellungen festlegen können. Um auf die Eigenschaftenseiten zuzugreifen, klicken Sie im Kontextmenü des Rollenverweises im **Projektmappen-Explorer** auf **Eigenschaften**, wie in der folgenden Abbildung dargestellt. Sie können auch auf den Rollenverweis doppelklicken.

![VS\_Solution\_Explorer\_Roles\_Properties](./media/vs-azure-tools-configuring-an-azure-project/IC784076.png)

Informationen zu den zugrunde liegenden Schemas für die Dienstdefinition und die Dienstkonfigurationsdateien finden Sie unter [Schemareferenz](https://msdn.microsoft.com/library/azure/dd179398.aspx). Weitere Informationen zur Dienstkonfiguration finden Sie unter [Verwalten von Dienstkonfigurationen und Profilen](vs-azure-tools-service-configurations-and-profiles-how-to-manage.md).

## Konfigurieren von Rolleneigenschaften

Die Eigenschaftenseiten für eine Web- und Workerrolle sind ähnlich. Es bestehen jedoch einige Unterschiede, die in den folgenden Abschnitten erläutert werden. Auf der Seite **Caching** können Sie die Azure-Cachedienste konfigurieren.

### Seite "Konfiguration"

Auf der Seite **Konfiguration** können Sie die folgenden Eigenschaften festlegen.

**Instanzen**

Legen Sie die Eigenschaft **Instanzenanzahl** auf die Anzahl der Instanzen fest, die der Dienst für diese Rolle ausführen soll.

Legen Sie die Eigenschaft **Größe des virtuellen Computers** auf **Sehr klein**, **Klein**, **Mittel**, **Groß** oder **Sehr groß** fest. Weitere Informationen finden Sie unter [Konfigurieren von Größen für Clouddienste](https://msdn.microsoft.com/library/azure/ee814754.aspx).

**Startaktion (nur Webrolle)**

Legen Sie diese Eigenschaft fest, um anzugeben, dass bei Beginn des Debugvorgangs von Visual Studio ein Webbrowser für die HTTP-Endpunkte und/oder für die HTTPS-Endpunkte gestartet werden soll.

Die Option **HTTPS-Endpunkt** ist nur verfügbar, wenn Sie bereits einen HTTPS-Endpunkt für die Rolle definiert haben. Sie können einen HTTPS-Endpunkt auf der Eigenschaftenseite **Endpunkte** definieren.

Wenn Sie bereits einen HTTPS-Endpunkt hinzugefügt haben, ist die Option **HTTPS-Endpunkt** standardmäßig aktiviert. Visual Studio startet nun beim Start des Debugvorgangs zusätzlich zum Browser für den HTTP-Endpunkt einen Browser für diesen Endpunkt. Dies setzt voraus, dass beide Startoptionen aktiviert sind.

**Diagnose**

Die Diagnosefunktion ist standardmäßig für die Webrolle aktiviert. Das Azure-Clouddienstprojekt und Speicherkonto wurden auf die Verwendung des lokalen Speicheremulators festgelegt. Wenn die Bereitstellung in Azure durchgeführt werden soll, klicken Sie auf die Generatorschaltfläche (**…**), um das Speicherkonto so zu aktualisieren, dass der Azure-Speicher in der Cloud verwendet wird. Sie können die Diagnosedaten entweder bei Bedarf oder in automatisch geplanten Intervallen an das Speicherkonto übertragen. Weitere Informationen zur Azure-Diagnose finden Sie unter [Sammeln von Protokollierungsdaten mit der Azure-Diagnose](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### Seite "Einstellungen"

Auf der Seite **Einstellungen** können Sie Konfigurationseinstellungen für den Dienst hinzufügen. Konfigurationseinstellungen sind Name-Wert-Paare. Von in dieser Rolle ausgeführtem Code können die Werte der Konfigurationseinstellungen zur Laufzeit anhand von Klassen ausgelesen werden, die von der [verwalteten Azure-Bibliothek](http://go.microsoft.com/fwlink?LinkID=171026) bereitgestellt werden. Im Speziellen wird von der [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx)-Methode der Wert einer benannten Konfigurationseinstellung zur Laufzeit zurückgegeben.

**Konfigurieren einer Verbindungszeichenfolge für ein Speicherkonto**

Eine Verbindungszeichenfolge ist eine Konfigurationseinstellung, die Verbindungs- und Authentifizierungsinformationen für den Speicheremulator oder für ein Azure-Speicherkonto enthält. Wenn vom Code Zugriff auf Azure-Speicherdienstdaten – d. h. auf Blob-, Warteschlangen- oder Tabellendaten – innerhalb eines in einer Rolle ausgeführten Codes erfordert wird, muss eine Verbindungszeichenfolge für das Speicherkonto definiert werden.

Eine Verbindungszeichenfolge, die auf ein Azure-Speicherkonto verweist, muss ein definiertes Format aufweisen. Informationen zum Erstellen von Verbindungszeichenfolgen finden Sie unter [Konfigurieren von Verbindungszeichenfolgen](https://msdn.microsoft.com/library/azure/ee758697.aspx).

Wenn Sie Ihren Dienst mit den Azure-Speicherdiensten testen oder den Clouddienst in Azure bereitstellen möchten, können Sie den Wert aller Verbindungszeichenfolgen so ändern, dass sie auf Ihr Azure-Speicherkonto verweisen. Klicken Sie auf "(…)", und wählen Sie "Anmeldeinformationen für Speicherkonto eingeben" aus. Geben Sie die Kontoinformationen ein, die Ihren Kontonamen und den Kontoschlüssel enthalten. Im Dialogfeld "Verbindungszeichenfolge für das Speicherkonto" können Sie auch angeben, ob Standard-HTTPS-Endpunkte (die Standardoption), die Standard-HTTP-Endpunkte oder benutzerdefinierte Endpunkte verwendet werden sollen. Sie können ggf. benutzerdefinierte Endpunkte verwenden, wenn ein benutzerdefinierter Domänenname für den Dienst registriert wurde, wie unter [Konfigurieren eines benutzerdefinierten Domänennamens für Blobdaten in einem Azure-Speicherkonto](storage-custom-domain-name.md) beschrieben.

>[AZURE.IMPORTANT]Sie müssen die Verbindungszeichenfolgen ändern, damit sie auf ein Azure-Speicherkonto verweisen, bevor Sie den Dienst bereitstellen. Wenn diese Änderung nicht vorgenommen wird, wird die Rolle möglicherweise nicht gestartet, oder die Zustände "Initialisieren", "Ausgelastet" und "Beenden" werden nicht durchlaufen.

### Seite "Endpunkte"

Eine Workerrolle kann eine beliebige Anzahl von HTTP-, HTTPS- oder TCP-Endpunkten aufweisen. Endpunkte können für externe Clients verfügbare Eingabeendpunkte oder interne Endpunkte sein, die für andere im Dienst ausgeführte Rollen verfügbar sind.

- Ändern Sie den Endpunkttyp in einen Eingabeendpunkt, und geben Sie einen Namen und die Nummer eines öffentlichen Ports an, um einen HTTP-Endpunkt für externe Clients und Webbrowser verfügbar zu machen.

- Ändern Sie den Endpunkttyp in einen "Eingabeendpunkt", und geben Sie einen Namen, die Nummer eines öffentlichen Ports und den Namen eines Verwaltungszertifikats an, um einen HTTPS-Endpunkt für externe Clients und Webbrowser verfügbar zu machen.

  Bevor ein Verwaltungszertifikat angegeben werden kann, muss das Zertifikat auf der Eigenschaftenseite **Zertifikate** definiert werden.

- Ändern Sie den Endpunkttyp in **intern**, und geben Sie einen Namen und mögliche private Ports für den Endpunkt an, um einen Endpunkt für den internen Zugriff durch andere Rollen innerhalb des Clouddiensts verfügbar zu machen.

### Seite "Lokaler Speicher"

Sie können die Eigenschaftenseite "Lokaler Speicher" verwenden, um mindestens eine lokale Speicherressource für eine Rolle zu reservieren. Eine lokale Speicherressource ist ein reserviertes Verzeichnis im Dateisystem des virtuellen Azure-Computers, in dem eine Instanz einer Rolle ausgeführt wird. Weitere Informationen zur Verwendung lokaler Speicherressourcen finden Sie unter [Konfigurieren von lokalen Speicherressourcen](https://msdn.microsoft.com/library/azure/ee758708.aspx).

### Seite "Zertifikate"

Auf der Seite **Zertifikate** können Sie der Rolle Zertifikate zuordnen. Mit den hinzugefügten Zertifikaten können HTTPS-Endpunkte auf der Eigenschaftenseite **Endpunkte** konfiguriert werden.

Auf der Eigenschaftenseite **Zertifikate** werden der Dienstkonfiguration Informationen zu den Zertifikaten hinzugefügt. Die Zertifikate sind kein Teil des Diensts, sondern müssen über das [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=213885) gesondert in Azure hochgeladen werden.

Um der Rolle ein Zertifikat zuzuordnen, geben Sie einen Namen für das Zertifikat an. Mit diesem Namen wird beim Konfigurieren eines HTTPS-Endpunkts auf der Eigenschaftenseite **Endpunkte** auf das Zertifikat verwiesen. Geben Sie im nächsten Schritt an, ob der Zertifikatspeicher **Lokaler Computer** oder **Aktueller Benutzer** ist. Geben Sie außerdem den Namen des Speichers an. Geben Sie zuletzt den Fingerabdruck des Zertifikats ein. Wenn sich das Zertifikat im Speicher **Current User\\Personal (My)** befindet, können Sie den Fingerabdruck des Zertifikats eingeben, indem Sie das Zertifikat aus einer aufgefüllten Liste auswählen. Wenn es sich an einem beliebigen anderen Ort befindet, geben Sie den Fingerabdruckwert manuell ein.

Wenn Sie ein Zertifikat aus dem Zertifikatspeicher hinzufügen, werden den Konfigurationseinstellungen automatisch alle Zwischenzertifikate hinzugefügt. Diese Zwischenzertifikate müssen auch in Azure hochgeladen werden, um den Dienst ordnungsgemäß für SSL zu konfigurieren.

Alle dem Dienst zugeordneten Verwaltungszertifikate sind nur dann für den Dienst gültig, wenn er in der Cloud ausgeführt wird. Wenn der Dienst in der lokalen Entwicklungsumgebung ausgeführt wird, wird ein vom Serveremulator verwaltetes Standardzertifikat verwendet.

## Konfigurieren des Azure-Clouddienstprojekts

Zum Konfigurieren von für ein gesamtes Azure-Clouddienstprojekt gültigen Einstellungen öffnen Sie zunächst das Kontextmenü für diesen Projektknoten und wählen anschließend **Eigenschaften** aus, um die entsprechenden Eigenschaftenseiten zu öffnen. In der folgenden Tabelle sind die Eigenschaftenseiten abgebildet.

|Eigenschaftenseite|Beschreibung|
|---|---|
|Anwendung|Auf dieser Seite zeigen Sie Informationen zur Version der Azure Tools an, die vom Azure-Clouddienstprojekt verwendet werden. Zudem können Sie ein Upgrade auf die aktuelle Version der Tools ausführen.|
|Buildereignisse|Auf dieser Seite können Sie Präbuild-und Postbuildereignisse festlegen.|
|Entwicklung|Auf dieser Seite geben Sie Buildkonfigurationsanweisungen und die Bedingungen an, unter denen Postbuildereignisse ausgeführt werden.|
|Web|Auf dieser Seite konfigurieren Sie Einstellungen für den Webserver.|

## Nächste Schritte

Informationen zum Konfigurieren von Azure-Clouddienstprojekten finden Sie unter [Verwalten von Rollen in den Azure-Clouddienstprojekten mit Visual Studio](vs-azure-tools-cloud-service-project-managing-roles.md).

<!---HONumber=Sept15_HO3-->