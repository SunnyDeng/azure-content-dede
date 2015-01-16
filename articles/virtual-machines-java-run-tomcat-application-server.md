<properties urlDisplayName="Tomcat on Virtual Machine" pageTitle="Tomcat auf einem virtuellen Computer - Azure-Lernprogramm" metaKeywords="Azure vm, creating vm Tomcat, configuring vm Tomcat" description="Erfahren Sie, wie Sie einen virtuellen Computer unter Windows erstellen und den Computer konfigurieren können, damit darauf ein Apache Tomcat-Anwendungsserver ausgeführt wird." metaCanonical="" services="virtual-machines" documentationCenter="Java" title="How to run a Java application server on a virtual machine" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-windows" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Ausführen eines Java-Anwendungsservers auf einem virtuellen Computer

Mit Azure können Sie einen virtuellen Computer nutzen, um Serverfunktionen bereitzustellen. Beispielsweise kann ein unter Azure ausgeführter virtueller Computer so konfiguriert werden, dass er einen Java-Anwendungsserver wie Apache Tomcat hostet. Nachdem Sie diesen Leitfaden durchgearbeitet haben, werden Sie besser verstehen, wie ein virtueller Computer unter Azure erstellt wird und wie er für die Ausführung eines Java-Anwendungsservers konfiguriert wird.

Sie erhalten Informationen zu folgenden Themen:

* Erstellen eines virtuellen Computers, auf dem bereits ein JDK installiert ist.
* Remoteanmeldung an Ihrem virtuellen Computer.
* Installieren eines Java-Anwendungsservers auf dem virtuellen Computer.
* Erstellen eines Endpunkts für den virtuellen Computer.
* Öffnen eines Ports in der Firewall für den Anwendungsserver.

Für dieses Lernprogramm wird ein Apache Tomcat-Anwendungsserver auf einem virtuellen Computer installiert. Das Ergebnis des Installationsvorgangs ist eine Tomcat-Installation, die der Folgenden ähnelt.

![Virtual machine running Apache Tomcat][virtual_machine_tomcat]

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## So erstellen Sie einen virtuellen Computer

1. Melden Sie sich beim [Azure-Verwaltungsportal] an(https://manage.windowsazure.com).
2. Klicken Sie auf **Neu**, klicken Sie auf **Berechnen**, klicken Sie auf **Virtueller Computer**, und klicken Sie dann auf **Aus Katalog**.
3. Wählen Sie im Dialogfeld **Image des virtuellen Computers auswählen** die Option **JDK 7 Windows Server 2012** aus.
Beachten Sie, dass **JDK 6 Windows Server 2012** verfügbar ist, wenn Sie Legacyanwendungen haben, die noch nicht in JDK 7 ausgeführt werden können.
4. Klicken Sie **Weiter**.
5. Gehen Sie im Dialogfeld <strong>Konfiguration des virtuellen Computers</strong> wie folgt vor:
    1. Geben Sie einen Namen für den virtuellen Computer an.
    2. Geben Sie die Größe für den virtuellen Computer an.
    3. Geben Sie im Feld **Benutzername** einen Namen für den Administrator ein. Merken Sie sich diesen Namen und das als nächstes eingegebene Kennwort. Sie benötigen diese Daten, wenn Sie sich von einem Remotestandort aus an dem virtuellen Computer anmelden.
    4. Geben Sie ein Kennwort in das Feld **Neues Kennwort** Neues Kennwort **Kennwort bestätigen** ein. Dies ist das Kennwort für das Administratorkonto.
    5. Klicken Sie **Weiter**.
6. Im nächsten Dialog <strong>Konfiguration des virtuellen Computers</strong> :
    1. Verwenden Sie für den **Cloud-Dienst** die Standardeinstellung **Neuen Cloud-Dienst erstellene**.
    2. Der Wert bei **Cloud-Dienst DNS-Name** muss auf cloudapp.net eindeutig sein. Ändern Sie wenn nötig diesen Wert, sodass Azure angibt, dass er eindeutig ist.
    2. Geben Sie eine Region, eine Affinitätsgruppe oder ein virtuelles Netzwerk an. Geben Sie für dieses Lernprogramm als Region **West-USA** an.
    2. Wählen Sie unter **Speicherkonto** die Option **Automatisch generiertes Speicherkonto verwenden** aus.
    3. Wählen Sie für **Verfügbarkeitsgruppe** die Option **(Keine)**.
    4. Klicken Sie **Weiter**.
7. Im abschließenden Dialog <strong>Konfiguration des virtuellen Computers</strong> :
    1. Akzeptieren Sie die Standardeinträge für Endpunkte.
    2. Klicken Sie auf **Fertig stellen**.

## So melden Sie sich von einem Remotestandort aus an Ihrem virtuellen Computer an

1. Melden Sie sich am [Verwaltungsportal] an(https://manage.windowsazure.com).
2. Klicken Sie auf**Virtuelle Computer**.
3. Klicken Sie auf den Namen des virtuellen Computers, an dem Sie sich anmelden möchten.
4. Nachdem der virtuelle Computer gestartet wurde werden unten auf der Seite über ein Popupmenü Verbindungen zugelassen.
5. Klicken Sie auf **Verbinden**.
6. Befolgen Sie die Anweisungen, um eine Verbindung mit dem virtuellen Computer herzustellen. Diese sollte das Speichern oder Öffnen der RDP-Datei beinhalten, die die Verbindungsdetails enthält. Sie müssen möglicherweise den url:port als letzten Teil der ersten Zeile der RDP-Datei kopieren und ihn in eine Anwendung für die Remoteanmeldung einfügen.

## So installieren Sie einen Java-Anwendungsserver auf dem virtuellen Computer

Sie können einen Java-Anwendungsserver auf den virtuellen Computer kopieren oder über ein Installationsprogramm installieren. 

Für dieses Lernprogramm wird Tomcat installiert.

1. Während Sie beim virtuellen Computer angemeldet sind, öffnen Sie eine Browsersitzung und navigieren zu <http://tomcat.apache.org/download-70.cgi>.
2. Doppelklicken Sie auf den Link für den **Windows Installer-Dienst (32-Bit/64-Bit)**. Mit diesem Verfahren wird Tomcat als Windows-Dienst installiert.
3. Starten Sie das Installationsprogramm, wenn Sie dazu aufgefordert werden.
4. Befolgen Sie im Setup-Assistenten für **Apache Tomcat Setup** die Anweisungen für die Installation von Tomcat. Für dieses Lernprogramm können die Standardeinstellungen übernommen werden. Wenn das Dialogfeld **Abschließen des Setup-Assistenten für Apache Tomcat** angezeigt wird, können Sie optional das Kontrollkästchen **Apache Tomcat ausführen** aktivieren, um Tomcat jetzt zu starten. Klicken Sie auf **Finish**, um die Installation von Tomcat abzuschließen.

## So starten Sie Tomcat
Wenn Sie im Dialogfeld **Abschließen des Setup-Assistenten für Apache Tomcat** nicht angegeben haben, dass Tomcat gestartet werden soll, öffnen Sie eine Eingabeaufforderung auf dem virtuellen Computer, und führen Sie folgenden Befehl aus: **net start Tomcat7**.

Öffnen Sie im Browser des virtuellen Computers <http://localhost:8080>. Sie sollten nun sehen, dass Tomcat ausgeführt wird.

Um von externen Computern aus festzustellen, ob Tomcat ausgeführt wird, müssen Sie einen Endpunkt erstellen und einen Port öffnen.

## So erstellen Sie einen Endpunkt für den virtuellen Computer
1. Melden Sie sich am [Verwaltungsportal] an(https://manage.windowsazure.com).
2. Klicken Sie auf**Virtuelle Computer**.
3. Klicken Sie auf den Namen des virtuellen Computers, auf dem der Java-Anwendungsserver ausgeführt wird.
4. Klicken Sie auf **Endpunkte**.
5. Klicken Sie**Hinzufügen**.
6. Stellen Sie im Dialogfeld **Endpunkt hinzufügen** sicher, dass das Kontrollkästchen **Standalone-Endpunkt hinzufügen** aktiviert ist, und klicken Sie dann auf **Weiter**.
7. Gehen Sie im Dialogfeld <strong>Details des neuen Endpunkts</strong> wie folgt vor:
    1. Geben Sie einen Namen für den Endpunkt an. Beispiel: **HttpIn**.
    2. Geben Sie als Protokoll **TCP** for the protocol.
    3. Geben Sie als öffentlichen Port **80** an.
    4. Geben Sie als privaten Port **8080** an.
    5. Klicken Sie auf die Schaltfläche **Fertig stellen**, um das Dialogfeld zu schließen. Der Endpunkt wird jetzt erstellt.

## So öffnen Sie einen Port in der Firewall für den virtuellen Computer
1. Melden Sie sich an dem virtuellen Computer an.
2. Klicken Sie unter Windows auf **Start**.
3. Klicken Sie auf **Systemsteuerung**.
4. Klicken Sie auf **System und Sicherheit**, auf **Windows-Firewall** und dann auf **Erweiterte Einstellungen**.
5. Klicken Sie auf **Eingehende Regeln** und dann auf **Eingehende Regeln**.

 ![New inbound rule][NewIBRule]

6. Wählen Sie für die neue Regel die Option**Port** unter **Regeltyp** aus, und klicken Sie dann auf **Weiter**.

 ![New inbound rule port][NewRulePort]

7. Wählen Sie als Protokoll **TCP** aus, und geben Sie als Port **8080** an. Klicken Sie dann auf **Weiter**.

 ![New inbound rule ][NewRuleProtocol]

8. Wählen Sie die Option **Verbindung zulassen** aus, und klicken Sie anschließend auf **Weiter**.

 ![New inbound rule action][NewRuleAction]

9. Achten Sie darauf, dass **Domäne**, **Privat** und **Öffentlich** für das Profil angekreuzt sind, und klicken Sie dann auf **Weiter**.

 ![New inbound rule profile][NewRuleProfile]

10. Geben Sie einen Namen für die Regel an, wie beispielsweise **HttpIn** (der Name der Regel muss jedoch nicht mit dem Namen des Endpunkts übereinstimmen). Klicken Sie anschließend auf **Fertig stellen**.  

 ![New inbound rule name][NewRuleName]

An dieser Stelle sollte es nun möglich sein, die Tomcat-Website über einen externen Browser anzuzeigen. Dabei wird eine URL in der Form **http://*your\_DNS\_name*.cloudapp.net** verwendet, wobei ***your\_DNS\_name*** der DNS-Name ist, den Sie beim Erstellen des virtuellen Computers angegeben haben.

## Überlegungen zum Lebenszyklus von Anwendungen
* Sie können Ihr eigenes Webarchiv für Anwendungen (WAR) erstellen und im Ordner **webapps** hinzufügen. Erstellen Sie beispielsweise ein dynamisches JSP-Basiswebprojekt (Java Service Page), und exportieren Sie dieses als WAR-Datei. Kopieren Sie dann die WAR-Datei in den Apache Tomcat-Ordner **webapps** auf dem virtuellen Computer, und führen Sie das Projekt in einem Browser aus.
* Wenn der Tomcat-Dienst installiert ist, wird standardmäßig der manuelle Start festgelegt. Über das Snap-In "Dienste" können Sie zum automatischen Start wechseln. Starten Sie das Snap-In "Dienste", indem Sie unter Windows auf **Start**, auf **Verwaltung**und dann auf **Dienste** klicken. Um festzulegen, dass Tomcat automatisch gestartet wird, doppelklicken Sie auf den Dienst **Apache Tomcat** im Snap-In "Dienste", und wählen Sie unter **Starttyp** die Option **Automatisch** aus, wie in der folgenden Abbildung gezeigt.

    ![Setting a service to start automatically][service_automatic_startup]

    Der automatische Start von Tomcat bietet den Vorteil, dass der Dienst wieder gestartet wird, wenn ein Neustart des virtuellen Computer erfolgt (zum Beispiel nach der Installation von Softwareupdates, die einen Neustart erfordern).

## Nächste Schritte
* Weitere Informationen zu anderen Diensten wie beispielsweise Azure Storage, Service Bus und SQL-Datenbank, die Sie möglicherweise zusammen mit Ihren Java-Anwendungen verwenden möchten, finden Sie unter <http://www.windowsazure.com/de-de/develop/java/>.

[virtual_machine_tomcat]: ./media/virtual-machines-java-run-tomcat-application-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-java-run-tomcat-application-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-java-run-tomcat-application-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-java-run-tomcat-application-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProfile.png

<!--HONumber=35.1-->
