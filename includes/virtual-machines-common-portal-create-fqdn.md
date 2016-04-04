


Beim Erstellen eines virtuellen Computers im [Azure-Portal](https://portal.azure.com) mit dem Bereitstellungsmodell **Ressourcen-Manager** erstellt das Portal eine öffentliche IP als Ressource für den virtuellen Computer. Mit dieser IP-Adresse können Sie per Remotezugriff auf den virtuellen Computer zugreifen. Obwohl das Portal standardmäßig keinen [vollständig qualifizierten Domänennamen](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (Fully Qualified Domain Name, FQDN) erstellt, ist es sehr einfach, nach der Erstellung des virtuellen Computers einen zu erzeugen. Dieser Artikel demonstriert die einzelnen Schritte um einen DNS-Name oder einen FQDN zu erstellen.

Im Artikel wird vorausgesetzt, dass Sie sich an Ihrem Abonnement im Portal angemeldet und mit dem **Ressourcen-Manager** einen virtuellen Computer mit den verfügbaren Images erstellt haben. Führen Sie die folgenden Schritte aus, nachdem die Ausführung des virtuellen Computers begonnen hat:

1.  Zeigen Sie die Einstellungen des virtuellen Computers im Portal an, und klicken Sie auf die öffentliche IP-Adresse.

    ![IP-Ressource suchen](./media/virtual-machines-common-portal-create-fqdn/locatePublicIP.PNG)

2.  Beachten Sie, dass der DNS-Name für die öffentliche IP-Adresse leer ist. Klicken Sie für das Blatt der öffentlichen IP auf **Alle Einstellungen**.

    ![Einstellungen IP](./media/virtual-machines-common-portal-create-fqdn/settingsIP.PNG)

3.  Öffnen Sie in den Einstellungen für die öffentliche IP die Registerkarte **Konfiguration**. Geben Sie den gewünschten DNS-Namen ein, und **speichern** Sie diese Konfiguration.

    ![DNS-Name eingeben](./media/virtual-machines-common-portal-create-fqdn/dnsNameLabel.PNG)

    Die öffentliche IP-Ressource zeigt nun diesen neuen DNS-Namen in ihrem Blatt an.

4.  Schließen Sie die Blätter der öffentlichen IP und gehen Sie zurück zum Blatt des virtuellen Computers im Portal. Überprüfen Sie, ob der DNS-Name/ der FQDN für die öffentliche IP-Ressource neben der IP-Adresse angezeigt wird.

    ![FQDN wird erstellt](./media/virtual-machines-common-portal-create-fqdn/fqdnCreated.PNG)


    Sie können jetzt eine Remoteverbindung mit dem virtuellen Computer herstellen, indem Sie diesen DNS-Namen verwenden. Verwenden Sie beispielsweise `SSH adminuser@testdnslabel.centralus.cloudapp.azure.com` beim Herstellen einer Verbindung mit einem virtuellen Linux-Computer, der über den vollqualifizierten Domänennamen `testdnslabel.centralus.cloudapp.azure.com` und den Benutzernamen `adminuser` verfügt.

<!---HONumber=AcomDC_0323_2016-->