<properties  writer="josephd" editor="tysonn" manager="dongill" />

# Lastenausgleich virtueller Computer

Alle virtuellen Computer, die Sie in Azure erstellen, können automatisch über einen privaten Netzwerkkanal mit anderen virtuellen Computern im gleichen Clouddienst oder virtuellen Netzwerk kommunizieren. Sämtliche eingehende Kommunikation wie Datenverkehr von Internethosts oder virtuellen Computern in anderen Clouddiensten oder virtuellen Netzwerken benötigt einen Endpunkt.

Endpunkte können auf unterschiedliche Weise genutzt werden. Die Endpunkte auf virtuellen Computern, die Sie mit dem Azure-Verwaltungsportal erstellen, werden hauptsächlich für das Remotedesktopprotokoll (RDP) und Windows PowerShell-Sitzungsverkehr verwendet. Über diese Endpunkte können Sie die virtuellen Computer über das Internet verwalten.

Endpunkte können auch für die Konfiguration des Azure-Lastenausgleichsmoduls verwendet werden, um eine bestimmte Art von Netzwerkverkehr zwischen mehreren virtuellen Computern oder Diensten zu verteilen. Sie können zum Beispiel die Netzwerklast von Webanfragen auf mehrere Webserver oder Webrollen verteilen.

Jedem auf einem virtuellen Computer definierten Endpunkt wird ein öffentlicher und ein privater Port zugewiesen, entweder TCP oder UDP. Internethosts schicken ihren eingehenden Datenverkehr an die öffentliche IP-Adresse des Clouddienstes und einen öffentlichen Port. Virtuelle Computer und Dienste innerhalb des Clouddienstes sind empfangsbereit auf ihren privaten IP-Adressen und privaten Ports. Das Lastenausgleichsmodul führt die Zuordnung zwischen öffentlicher IP-Adresse und Portnummer des eingehenden Datenverkehrs zu den privaten IP-Adressen und Portnummern der virtuellen Computer durch und umgekehrt für den Antwortverkehr vom virtuellen Computer.

Beo der Konfiguration von Lastverteilung für Datenverkehr zwischen mehreren virtuellen Computern oder Diensten verteilt Azure den eingehenden Datenverkehr nach Zufallsprinzip.

Für einen Clouddienst, der Instanzen von Web- oder Workerrollen enthält, können Sie einen öffentlichen Endpunkt in der Dienstdefinition definieren. Für einen Clouddienst, der virtuelle Computer enthält, können Sie einen Endpunkt zu einem virtuellen Computer bei dessen Erstellung oder zu einem späteren Zeitpunkt hinzufügen.

Die folgende Abbildung zeigt einen Endpunkt für Standard-Datenverkehr (unverschlüsselt) mit Lastenausgleich, der von drei virtuellen Computern für den öffentlichen und privaten TCP-Port 80 genutzt wird. Diese drei virtuellen Computer bilden einen Satz mit Lastenausgleich.

![Lastenausgleich](./media/load-balancing-vms/LoadBalancing.png)

Wenn Internetclients Webseitenanfragen an die öffentliche IP-Adresse und TCP-Port 80 des Clouddienstes schicken, verteilt das Lastenausgleichsmodul diese Anfragen nach dem Zufallsprinzip auf die drei virtuellen Computer im Satz mit Lastenausgleich.

Führen Sie die folgenden Schritte aus, um einen Satz mit Lastenausgleich aus virtuellen Azure-Computern zu erstellen:

* [Schritt 1: Den ersten virtuellen Computer erstellen](#firstmachine)
* [Schritt 2: Weitere virtuelle Computer im gleichen Clouddienst
  erstellen](#addmachines)
* [Schritt 3: Einen Satz mit Lastenausgleich mit dem ersten virtuellen
  Computer erstellen](#loadbalance)
* [Schritt 4: Virtuelle Computer zum Satz mit Lastenausgleich
  hinzufügen](#addtoset)

## <a id="firstmachine"> </a>Schritt 1: Den ersten virtuellen Computer erstellen

Melden Sie sich am [Azure-Verwaltungsportal][1] an, falls noch nicht geschehen. Sie können den ersten virtuellen Computer mit der Methode Aus Galerie oder Schnellerfassung erstellen.

* **Aus Galerie**: Mit der Methode **Aus Galerie** können Sie bei der Erstellung des virtuellen Computers Endpunkte erstellen. Sie können einen Namen für den Clouddienst angeben, der bei der Erstellung des virtuellen Computers erzeugt wird. Anweisungen finden Sie unter
  [Erstellen eines virtuellen Linux-Computers](../virtual-machines-linux-tutorial) oder [Erstellen eines virtuellen Windows Server-Computers](../virtual-machines-windows-tutorial).

* **Schnellerfassung**: Erstellen Sie einen virtuellen Computer, indem Sie ein Bild aus der Bildergalerie auswählen und grundlegende Angaben machen. Wenn Sie diese Methode verwenden, müssen Sie den Endpunkt nach Erstellung des virtuellen Computers hinzufügen. Bei dieser Methode wird außerdem ein Clouddienst mit einem Standardnamen erstellt.
  Weitere Informationen finden Sie unter [Schnelles Erstellen eines virtuellen Computers](../virtual-machines-quick-create).

**Hinweis**: Nach der Erstellung des virtuellen Computers per Schnellerfassung wird auf der Seite Clouddienste auf dem Verwaltungsportal der Name des neuen Clouddiensts mit weiteren Informationen zum Dienst aufgeführt.

## <a id="addmachines"> </a>Schritt 2: Weitere virtuelle Computer im gleichen Clouddienst erstellen

Erstellen Sie weitere virtuelle Computer im Clouddienst des ersten virtuellen Computers, indem Sie die Methode Aus Galerie verwenden.

## <a id="loadbalance"> </a>Schritt 3: Einen Satz mit Lastenausgleich mit dem ersten virtuellen Computer erstellen

1. Klicken Sie im Azure-Verwaltungsportal auf **Virtueller Computer** und anschließend auf den Namen Ihres neuen virtuellen Computers.

2. Klicken Sie auf **Endpunkte** und anschließend auf **Hinzufügen**.

3. Klicken Sie auf der Seite Endpunkt zu virtuellem Computer hinzufügen auf den Pfeil nach rechts.

4. Auf der Seite Geben Sie die Details des Endpunktes ein:

    * Geben Sie unter **Name** einen Namen für den Endpunkt ein oder wählen Sie einen der vordefinierten Endpunkte für gängige Protokolle aus.
    * Wählen Sie unter **Protokoll** das Protokoll aus, das für den Endpunkttyp erforderlich ist: TCP oder UDP.
    * Geben Sie unter **Öffentlicher Port** und **Privater Port** die Portnummern ein, die der virtuelle Computer verwenden soll. Sie können die Regeln für privaten Port und Firewall auf dem virtuellen Computer verwenden, um Datenverkehr entsprechend Ihren Anforderungen umzuleiten. Der private Port kann mit dem öffentlichen Port identisch sein. Für einen Endpunkt für Webverkehr (HTTP) könnten Sie zum Beispiel Port 80 sowohl für den öffentlichen als auch für den privaten Port verwenden.

5. Klicken Sie auf **Satz mit Lastenausgleich erstellen** und klicken Sie auf den Pfeil nach rechts.

6. Geben Sie auf der Seite Satz mit Lastenausgleich konfigurieren einen Namen für den Satz mit Lastenausgleich ein, und weisen Sie dann die Werte für das Testverhalten des Azure-Lastenausgleichsmoduls zu. Das Lastenausgleichsmodul führt Tests durch, um zu ermitteln, ob die virtuellen Computer in einem Satz mit Lastenausgleich empfangsbereit für eingehenden Datenverkehr sind.

7. Klicken Sie auf das Häkchen, um den Endpunkt mit Lastenausgleich zu erstellen. In der Spalte **Name des Satzes mit Lastenausgleich** auf der Seite **Endpunkt** für den virtuellen Computer wird der Eintrag **Ja** angezeigt.

## <a id="addtoset"> </a>Schritt 4: Virtuelle Computer zum Satz mit Lastenausgleich hinzufügen

Nachdem Sie den Satz mit Lastenausgleich erstellt haben, fügen Sie die anderen virtuellen Computer hinzu. Für weitere virtuelle Computer im gleichen Clouddienst:

1.  Klicken Sie im Verwaltungsportal auf **Virtuelle Computer**, klicken Sie auf den Namen des virtuellen Computers, anschließend auf **Endpunkte** und auf **Hinzufügen**.

2.  Klicken Sie auf der Seite Endpunkt zu virtuellem Computer hinzufügen auf **Endpunkt zu einem vorhandenen Satz mit Lastenausgleich hinzufügen**, wählen Sie den Namen des Satzes mit Lastenausgleich aus und klicken Sie auf den Pfeil nach rechts.

3.  Füllen Sie auf der Seite Geben Sie die Details des Endpunktes ein Namen und Protokoll für den Endpunkt aus und klicken Sie auf das Häkchen.

<!-- LINKS -->



[1]: http://manage.windowsazure.com