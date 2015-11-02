<properties 
    pageTitle="Wie werden USB-Geräte in Azure RemoteApp umgeleitet? | Microsoft Azure" 
    description="Erfahren Sie, wie USB-Geräte in Azure RemoteApp umgeleitet werden." 
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
    ms.date="10/19/2015" 
    ms.author="elizapo" />



# Wie werden USB-Geräte in Azure RemoteApp umgeleitet?

Die Geräteumleitung ermöglicht Benutzern das Verwenden der an ihren Computer oder ihr Tablet angeschlossenen USB-Geräte mit den Apps in Azure RemoteApp. Wenn Sie z. B. Skype über Azure RemoteApp freigegeben haben, müssen die Benutzer ihre Gerätekameras verwenden können.

Bevor Sie fortfahren, lesen Sie zunächst die Informationen zur USB-Umleitung unter [Verwenden von Umleitungen in Azure RemoteApp](remoteapp-redirection.md). Der empfohlene Befehl „nusbdevicestoredirect:s:*“ funktioniert jedoch nicht bei USB-Webcams und funktioniert möglicherweise auch nicht bei einigen USB-Druckern oder multifunktionalen USB-Geräten. Es ist vorgesehen, dass der Azure RemoteApp-Administrator aus Sicherheitsgründen die Umleitung entweder über die Geräteklassen-GUID oder Geräte-Instanz-ID aktivieren muss, ehe die Benutzer diese Geräte nutzen können.

Obwohl in diesem Artikel von der Umleitung von Webcams die Rede ist, können Sie einen ähnlichen Ansatz befolgen, um USB-Drucker und andere multifunktionale USB-Geräte umzuleiten, die nicht mit dem Befehl **nusbdevicestoredirect:s:*** umgeleitet werden.

## Umleitungsoptionen für USB-Geräte
Azure RemoteApp verwendet sehr ähnliche Mechanismen zum Umleiten von USB-Geräten wie diejenigen, die für Remotedesktopdienste verfügbar sind. Die zugrunde liegende Technologie ermöglicht das Auswählen der ordnungsgemäßen Umleitungsmethode für ein bestimmtes Gerät, um mit dem Befehl **usbdevicestoredirect:s:** das Beste bei sowohl der allgemeinen als auch der RemoteFX-USB-Geräteumleitung herauszuholen. Dieser Befehl hat vier Elemente:

| Verarbeitungsreihenfolge | Parameter | Beschreibung |
|------------------|---------------------|----------------------------------------------------------------------------------------------------------------------------|
| 1 | * | Wählt alle Geräte aus, die von der allgemeinen Umleitung nicht ausgewählt werden. Hinweis: Standardmäßig funktioniert „*“ nicht für USB-Webcams. |
| | {Geräteklassen-GUID} | Wählt alle Geräte aus, die der angegebenen Gerätesetupklasse entsprechen. |
| | USB\\Instanz-ID | Wählt ein USB-Gerät aus, das für die jeweilige Instanz-ID angegeben ist. |
| 2 | -USB\\Instanz-ID | Entfernt die Umleitungseinstellungen für das angegebene Gerät. |

## Umleiten eines USB-Geräts mithilfe der Geräteklassen-GUID
Es gibt zwei Möglichkeiten, die Geräteklassen-GUID zu finden, die für die Umleitung verwendet werden kann.

Die erste Möglichkeit sind die vom [System definierten Gerätesetupklassen, die für Anbieter verfügbar sind](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx). Wählen Sie die Klasse, die am ehesten dem Gerät entspricht, das am lokalen Computer angeschlossen ist. Für Digitalkameras kann dies eine Bildverarbeitungsgeräte- oder Videoaufnahmegeräte-Klasse sein. Sie müssen ein wenig mit den Geräteklassen experimentieren, um die ordnungsgemäße Klassen-GUID zu finden, die für das lokal angeschlossene USB-Gerät (in unserem Fall die Webcam) funktioniert.

Eine bessere Möglichkeit bzw. die zweite Option ist, diese Schritte zu befolgen, um die spezifische Geräteklassen-GUID zu finden:

1. Öffnen Sie den Geräte-Manager, und suchen Sie das Gerät, das umgeleitet werden soll. Klicken Sie mit der rechten Maustaste darauf, und öffnen Sie dann die Eigenschaften. ![Öffnen des Geräte-Managers](./media/remoteapp-usbredir/ra-devicemanager.png)
2. Wählen Sie auf der Registerkarte **Details** die Eigenschaft **Geräteklassen-GUID**. Der angezeigte Wert ist die Klassen-GUID für diesen Gerätetyp. ![Kameraeigenschaften](./media/remoteapp-usbredir/ra-classguid.png)
3. Verwenden Sie den Wert der Klassen-GUID, um entsprechende Geräte umzuleiten.

Beispiel:

		Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s:<Class Guid value>"

Sie können mehrere Geräteumleitungen im selben Cmdlet kombinieren. Beispiel: Zum Umleiten von lokalem Speicher und einer USB-Webcam sieht das Cmdlet folgendermaßen aus:

		Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:<Class Guid value>"

Wenn Sie die Geräteumleitung anhand der Klassen-GUID festlegen, werden alle Geräte umgeleitet, die der Klassen-GUID in der angegebenen Sammlung entsprechen. Wenn es beispielsweise mehrere Computer im lokalen Netzwerk gibt, die die gleiche USB-Webcam haben, können Sie mithilfe eines einzelnen Cmdlets alle Webcams umleiten.

## Umleiten eines USB-Geräts mithilfe der Geräte-Instanz-ID

Wenn Sie eine präzisere Kontrolle und die Umleitung gerätebezogen steuern möchten, können Sie den „**USB\\InstanceID **redirection“-Parameter verwenden.

Der schwierigste Teil dieser Methode ist das Ermitteln der USB-Geräte-Instanz-ID. Sie benötigen Zugriff auf den Computer und das jeweilige USB-Gerät. Führen Sie dann die folgenden Schritte durch:

1. Aktivieren Sie die Umleitung in der Remotedesktopsitzung wie unter [Wie kann ich meine Geräte und Ressourcen in einer Remotedesktopsitzung verwenden?](http://windows.microsoft.com/de-DE/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session) beschrieben.
2. Öffnen Sie eine Remotedesktopverbindung, und klicken Sie auf **Optionen anzeigen**.
3. Klicken Sie auf **Speichern unter**, um die aktuellen Verbindungseinstellungen in einer RDP-Datei zu speichern. ![Speichern der Einstellungen als RDP-Datei](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. Wählen Sie einen Dateinamen und Speicherort, z. B. „MyConnection.rdp“ und „Dieser PC\\Dokumente“, und speichern Sie die Datei.
5. Öffnen Sie die Datei „MyConnection.rdp“ mit einem Texteditor, und suchen Sie die Instanz-ID des Geräts, das Sie umleiten möchten.

Verwenden Sie anschließend die Instanz-ID im folgenden Cmdlet:

	Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s: USB<Device InstanceID value>"

<!---HONumber=Oct15_HO4-->