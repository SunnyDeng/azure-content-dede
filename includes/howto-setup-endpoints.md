<properties writer="kathydav" editor="tysonn" manager="jeffreyg" />

# Einrichten von Endpunkten für einen virtuellen Computer

**Hinweis**: Informationen zur direkten Verbindung mit virtuellen Computern anhand des Hostnamens oder zur Einrichtung standortübergreifender Verbindungen finden Sie unter [Überblick über virtuelle Azure-Netzwerke][Überblick über virtuelle Azure-Netzwerke].

Alle virtuellen Computer, die Sie in Azure erstellen, können automatisch über einen privaten Netzwerkkanal mit anderen virtuellen Computern im gleichen Clouddienst oder virtuellen Netzwerk kommunizieren. Für andere Ressourcen im Internet oder anderen virtuellen Netzwerken sind allerdings Endpunkte erforderlich, um den auf dem virtuellen Computer eingehenden Netzwerkverkehr verarbeiten zu können.

Wenn Sie einen virtuellen Computer im Verwaltungsportal erstellen, können Sie dabei auch diese Endpunkte erstellen, z. B. für Remote Desktop, Windows PowerShell Remoting oder Secure Shell (SSH). Nach dem Erstellen des virtuellen Computers können Sie bei Bedarf noch zusätzliche Endpunkte erstellen. Sie können auch am öffentlichen Port eingehenden Netzwerkverkehr verwalten, indem Sie Regeln für die Netzwerk-Zugriffssteuerungsliste (access control list, ACL) für den Endpunkt festlegen. In diesem Artikel wird beschrieben, wie diese beiden Aufgaben ausgeführt werden.

Jeder Endpunkt verfügt über einen öffentlichen und einen privaten Port.

-   Der private Port wird vom virtuellen Computer intern verwendet, um auf den Verkehr an diesem Endpunkt zu lauschen.

-   Der öffentliche Port wird vom Azure-Lastenausgleichsmodul zur Kommunikation mit dem virtuellen Computer über externe Ressourcen verwendet. Nach der Erstellung eines Endpunkts können Sie die Netzwerk-Zugriffssteuerungsliste verwenden, um Regeln zu definieren, mit deren Hilfe Sie eingehenden Verkehr auf dem öffentlichen Port isolieren und steuern können. Weitere Informationen erhalten Sie unter [Netzwerk-Zugriffssteuerungslisten][Netzwerk-Zugriffssteuerungslisten].

Standardwerte für die Ports und ein Protokoll für diese Endpunkte werden bereitgestellt, wenn die Endpunkte über das Verwaltungsportal erstellt werden. Bei allen anderen Endpunkten müssen Sie die Ports und das Protokoll selbst festlegen, wenn Sie den Endpunkt erstellen. Ressourcen können entweder über das TCP- oder das UDP-Protokoll mit einem Endpunkt verbunden werden. Das TCP-Protokoll umfasst HTTP- und HTTPS-Kommunikation.

**Wichtig**: Die Firewall-Konfiguration erfolgt für Remote Desktop und Secure Shell (SSH) zugeordnete Ports und in den meisten Fällen auch für Windows PowerShell Remoting automatisch. Für allen anderen Endpunkten zugeordnete Ports erfolgt keine automatische Konfiguration für die Firewall im Gastbetriebssystem. Wenn Sie einen Endpunkt erstellen, müssen Sie die entsprechenden Ports in der Firewall konfigurieren, damit der gewünschte Verkehr durch den Endpunkt geleitet werden kann.

### Erstellen eines Endpunkts

1.  Melden Sie sich am [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, falls noch nicht geschehen.

2.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer aus, den Sie konfigurieren möchten.

3.  Klicken Sie auf **Endpunkte**. Auf der Seite "Endpunkte" werden alle Endpunkte für den virtuellen Computer aufgelistet.

    ![Endpunkte][Endpunkte]

4.  Klicken Sie auf **Hinzufügen**.

    Das Dialogfeld **Endpunkt hinzufügen** wird angezeigt. Wählen Sie, ob Sie den Endpunkt zu einem Satz mit Lastenausgleich hinzufügen möchten, und klicken Sie auf den Pfeil, um fortzufahren.

5.  Geben Sie unter **Name** einen Namen für den Endpunkt ein.

6.  Legen Sie als Protokoll entweder **TCP** oder **UDP** fest.

7.  Geben Sie unter **Öffentlicher Port** und **Privater Port** die Portnummern ein, die Sie verwenden möchten. Diese Portnummern dürfen sich unterscheiden. Der öffentliche Port ist der Einstiegspunkt für Kommunikationen, die von außerhalb von Azure kommen, und wird vom Azure-Lastenausgleichsmodul verwendet. Sie können die Regeln für privaten Port und Firewall auf dem virtuellen Computer verwenden, um Datenverkehr entsprechend Ihren Anforderungen umzuleiten.

8.  Klicken Sie auf **Satz mit Lastenausgleich erstellen**, wenn dieser Endpunkt der erste in einem Satz mit Lastenausgleich sein soll. Geben Sie anschließend auf der Seite **Satz mit Lastenausgleich konfigurieren** einen Namen, einen Protokoll und Testdetails an. Sätze mit Lastenausgleich erfordern einen Test, so dass die Intaktheit des Satzes überprüft werden kann. Weitere Informationen finden Sie unter [Lastenausgleich zwischen virtuellen Computern][Lastenausgleich zwischen virtuellen Computern].

9.  Aktivieren Sie das Kontrollkästchen, um den Endpunkt zu erstellen.

    Der Endpunkt wird jetzt auf der Seite **Endpunkte** aufgelistet.

    ![Endpunkt erfolgreich erstellt][Endpunkt erfolgreich erstellt]

### Verwaltung der ACL für einen Endpunkt

Gehen Sie folgendermaßen vor, um eine ACL an einem Endpunkt hinzuzufügen, zu modifizieren oder zu entfernen.

**Hinweis**: Wenn der Endpunkt Teil eines Satzes mit Lastenausgleich ist, werden alle Änderungen, die Sie an der ACL oder an einem Endpunkt vornehmen, auf alle Endpunkte in diesem Satz angewendet.

1.  Melden Sie sich am [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, falls noch nicht geschehen.

2.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer aus, den Sie konfigurieren möchten.

3.  Klicken Sie auf **Endpunkte**. Auf der Seite "Endpunkte" werden alle Endpunkte für den virtuellen Computer aufgelistet.

    ![ACL][ACL]

4.  Wählen Sie den entsprechenden Endpunkt aus der Liste.

5.  Klicken Sie auf **Manage ACL**.

    Das Dialogfeld **Specify ACL details** wird angezeigt.

    ![ACL-Details festlegen][ACL-Details festlegen]

6.  Verwenden Sie Zeilen in der Liste, um Regeln für eine ACL hinzuzufügen, zu löschen oder zu bearbeiten. Der Wert "Remote Subnet" entspricht dem IP-Adressenbereich, den Sie als Regel entweder erlauben oder verweigern können. Die Regeln werden der Reihe nach, beginnend mit der ersten und endend mit der letzten Regel, bewertet. Dies bedeutet, dass die Regeln von der stärksten bis zur schwächsten Einschränkung aufgelistet werden sollten. Beispiele und weitere Informationen finden Sie unter [Netzwerk-Zugriffssteuerungslisten][Netzwerk-Zugriffssteuerungslisten].

  [Überblick über virtuelle Azure-Netzwerke]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Netzwerk-Zugriffssteuerungslisten]: http://go.microsoft.com/fwlink/p/?LinkId=303816
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Endpunkte]: ./media/howto-setup-endpoints/endpointswindows.png
  [Lastenausgleich zwischen virtuellen Computern]: http://www.windowsazure.com/de-de/manage/windows/common-tasks/how-to-load-balance-virtual-machines/
  [Endpunkt erfolgreich erstellt]: ./media/howto-setup-endpoints/endpointwindowsnew.png
  [ACL]: ./media/howto-setup-endpoints/EndpointsShowsDefaultEndpointsForVM.PNG
  [ACL-Details festlegen]: ./media/howto-setup-endpoints/EndpointACLdetails.PNG
