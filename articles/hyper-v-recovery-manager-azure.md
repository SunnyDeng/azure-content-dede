<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Configure Azure Site Recovery to protect virtual machines on Hyper-V server located in VMM clouds" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in VMM clouds from one on-premises site to another. Azure Site Recovery can also replicate, failover, and recover Hyper-V virtual machine data between VMM clouds and Microsoft Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to Azure Protection" editor="jimbe" manager="cfreeman" authors="" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="" />

# Erste Schritte mit Azure Site Recovery: Schutz „von lokal an Azure“

<div class="dev-callout"> 

<p>Verwenden Sie Azure Site Recovery zum Schutz virtueller Computer, die auf Hyper-V-Hosts in Clouds mit System Center Virtual Machine Manager (VMM) ausgef&uuml;hrt werden. Sie k&ouml;nnen Folgendes konfigurieren:</p>

<ul>
<li><b>Schutz &bdquo;von lokal an Azure&ldquo;</b> &ndash; Replizieren Sie lokale virtuelle Maschinen auf Hyper-V-Hostservern in VMM-Clouds auf Azure. Sie konfigurieren und aktivieren Schutzeinstellungen in Azure Site Recovery-Tresoren. Die Daten der virtuellen Computer werden vom lokalen Hyper-V-Server an Azure-Speicher repliziert.</li>

<li><b>Schutz &bdquo;von lokal an lokal&ldquo;</b> &ndash; Replizieren Sie lokale virtuelle Computer auf Hyper-V-Hostservern in VMM-Clouds von einer lokalen Site an eine andere. Sie konfigurieren und aktivieren Schutzeinstellungen in Azure Site Recovery-Tresoren. Die Daten der virtuellen Computer werden von einem lokalen Hyper-V-Server an einen anderen repliziert. Der Prozess wird von Azure Site Recovery nur orchestriert.
Weitere Informationen zu diesem Szenario finden Sie in <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Erste Schritte mit Azure Site Recovery: Schutz &bdquo;von lokal an lokal&ldquo;</a>.</li>

</ul>
 
<h2><a id="about"></a>Informationen zu diesem Lernprogramm</h2>


<P>In diesem Lernprogramm wird gezeigt, wie Sie Azure Site Recovery f&uuml;r eine rasche Machbarkeitsstudie bereitstellen. Wo m&ouml;glich, werden der schnellste Pfad und die Standardeinstellungen verwendet. Der Vorgang umfasst folgende Schritte:
<ul>
<li>Einrichten eines Azure Site Recovery-Tresors &ndash; Laden Sie ein Zertifikat in den Tresor hoch und richten Sie es auf dem VMM-Quellserver ein, und generieren Sie einen Tresorschl&uuml;ssel. </li>
<li>Einrichten des VMM-Quellservers und der Hyper-V-Hostserver &ndash; Installieren Sie den Azure Site Recovery-Anbieter auf dem VMM-Quellserver und den Azure Recovery Services-Agent auf den Hyper-V-Hostservern.</li>
<li>Konfigurieren der VMM-Clouds &ndash; Konfigurieren Sie Schutzeinstellungen f&uuml;r Clouds auf dem VMM-Quellserver.</li>
<li>Aktivieren von virtuellen Computern &ndash; Aktivieren Sie Schutz f&uuml;r virtuelle Computer.</li>
<li>Ausf&uuml;hren eines Failover &ndash; Erstellen Sie einen Wiederherstellungsplan, und f&uuml;hren Sie ein Test-Failover aus.</li>
</ul>


Informationen &uuml;ber eine vollst&auml;ndige Bereitstellung finden Sie in folgenden Abschnitten:</P>

<UL>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planen der Azure Site Recovery-Bereitstellung</a> &ndash; Beschreibt die Planungsschritte, die ausgef&uuml;hrt werden m&uuml;ssen, bevor Sie eine vollst&auml;ndige Bereitstellung starten.</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=402679">Bereitstellen von Azure Site Recovery: Schutz &bdquo;von lokal an Azure&ldquo;</a> &ndash; Enth&auml;lt schrittweise Anleitungen f&uuml;r eine vollst&auml;ndige Bereitstellung.</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=378272">Verwalten und &Uuml;berwachen von Azure Site Recovery</a> &ndash; Beschreibt das Durchf&uuml;hren von Failovers und die Verwaltung und &Uuml;berwachung der Bereitstellung.</LI>
</UL>
<P>Wenn Sie w&auml;hrend dieses Lernprogramms Probleme haben, lesen Sie den Wiki-Artikel <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: Common Error Scenarios and Resolutions</a>, oder posten Sie Ihre Fragen im <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services-Forum</a>.</P>

</div>

## <span id="before"></span></a>Voraussetzungen

<div class="dev-callout"> 
<P>Pr&uuml;fen Sie die Voraussetzungen, bevor Sie mit diesem Lernprogramm beginnen.</P>

<h3><a id="HVRMPrereq"></a>Voraussetzungen f&uuml;r Azure</h3>

<UL>
<LI><b>Azure-Konto</b> &ndash; Sie ben&ouml;tigen ein Azure-Konto. Wenn Sie kein Azure-Konto haben, gehen Sie zu <a href="http://aka.ms/try-azure">Kostenloses Azure-Testkonto</a>. Informationen zu Preisen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery-Manager &ndash; Preisdetails</a>.</LI>
<LI><b>Zertifikat</b> &ndash; Sie m&uuml;ssen ein Verwaltungszertifikat (.cer) mit einem &ouml;ffentlichen Schl&uuml;ssel in den Tresor hochladen. Dieses Zertifikat wird als PFX-Datei (mit privatem Sch&uuml;ssel) exportiert und an jeden VMM-Server importiert, den Sie im Tresor registrieren m&ouml;chten. F&uuml;r dieses Lernprogramm verwenden Sie ein selbstsigniertes Zertifikat. F&uuml;r eine vollst&auml;ndige Bereitstellung k&ouml;nnen Sie ein g&uuml;ltiges SSL-Zertifikat verwenden, das den im Planungshandbuch beschriebenen <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Zertifikatanforderungen</a> entspricht</a>. </LI>


</LI>

<LI><b>Azure-Speicherkonto</b> &ndash; Sie ben&ouml;tigen ein Azure-Speicherkonto, um an Azure replizierte Daten zu speichern. F&uuml;r das Konto muss Georeplikation aktiviert sein. Es muss sich in der gleichen Region wie der Azure Site Recovery-Dienst befinden und dem gleichen Abonnement zugeordnet sein. Weitere Informationen zum Einrichten von Azure-Speicher finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=398704">Einf&uuml;hrung in Microsoft Azure Storage</a>.</LI>
</UL>

<h3><a id="VMMPrereq"></a>VMM-Voraussetzungen</h3>

<UL>
<LI><b>VMM-Server</b> &ndash; Ein VMM-Server, der auf System Center 2012 R2 ausgef&uuml;hrt wird.</LI>
<LI><b>VMM-Clouds</b> &ndash; Mindestens eine Cloud auf dem VMM-Server. Die Cloud muss Folgendes enthalten:
    <UL>
    <LI>Eine oder mehrere VMM-Hostgruppen</LI>
    <LI>Einen oder mehrere Hyper-V-Hostserver oder Cluster in jeder Hostgruppe.</LI>
    <li>Einen oder mehrere virtuelle Computer auf dem Hyper-V-Quellserver in der Cloud. Es muss sich um virtuelle Computer der 1. Generation handeln.</li>
        </UL></LI>  
</UL>

<h3><a id="VMPrereq"></a>Voraussetzungen f&uuml;r virtuelle Computer</h3>

<UL>
<LI><b>Generation</b> Azure unterst&uuml;tzt nur virtuelle Computer der 1. Generation.</LI>
<LI>Eine vollst&auml;ndige Liste der Supportanforderungen f&uuml;r virtuelle Computer f&uuml;r ein Failover an Azure finden Sie im Planungshandbuch unter <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Voraussetzungen und Support</a>. </LI>  
</UL>

<h2><a id="tutorial"></a>Schritte im Lernprogramm</h2> 

Gehen Sie nach der Pr&uuml;fung der Voraussetzungen folgenderma&szlig;en vor:
<UL>
<LI><a href="#createcert">Schritt 1: Abrufen und Konfigurieren von Zertifikaten</a> &ndash; Rufen Sie ein CER-Zertifikat ab, exportieren Sie es als PFX-Datei, und importieren Sie die PFX-Datei an die entsprechenden VMM-Server.</LI>
<LI><a href="#vault">Schritt 2: Erstellen eines Tresors</a> &ndash; Erstellen Sie einen Azure Site Recovery-Tresor.</LI>
<LI><a href="#upload">Schritt 3: Konfigurieren des Tresors</a> &ndash; Laden Sie ein Verwaltungszertifikat in den Tresor hoch, und generieren Sie einen Schl&uuml;ssel f&uuml;r den Tresor. Der Schl&uuml;ssel wird verwendet, um sicherzustellen, dass der Anbieter auf dem VMM-Server nur von Azure Site Recovery gesendete Befehle ausf&uuml;hrt.</LI>
<LI><a href="#download">Schritt 4: Installieren der Anbieteranwendung</a> &ndash; F&uuml;hren Sie die Microsoft Azure Site Recovery-Anbieteranwendung auf dem VMM-Server aus. Dadurch wird der Anbieter installiert und der VMM-Server im Tresor registriert.</LI>
<LI><a href="#agent">Schritt 5: Installieren der Agent-Anwendung</a> &ndash; Installieren Sie den Microsoft Azure Recovery Services-Agent auf jedem Hyper-V-Host.</LI>
<LI><a href="#clouds">Schritt 6: Konfigurieren des Cloud-Schutzes</a> &ndash; Konfigurieren Sie die Schutzeinstellungen f&uuml;r VMM-Clouds.</LI>
<LI><a href="#NetworkMapping">Schritt 7: Konfigurieren der Netzwerkzuordnung</a> &ndash; Optional k&ouml;nnen Sie die Netzwerkzuordnung konfigurieren, um VM-Quellnetzwerke zu Azure-Zielnetzwerken zuzuordnen.</LI>
<LI><a href="#virtualmachines">Schritt 8: Aktivieren von Schutz f&uuml;r virtuelle Computer</a> &ndash; Aktivieren Sie Schutz f&uuml;r virtuelle Computer in gesch&uuml;tzten VMM-Clouds.</LI>
<LI><a href="#recovery plans">Schritt 9: Konfigurieren und Ausf&uuml;hren von Wiederherstellungspl&auml;nen</a> &ndash; Erstellen Sie einen Wiederherstellungsplan und f&uuml;hren Sie ein Test-Failover f&uuml;r den Plan aus.</LI>
</UL>



<a name="createcert"></a> <h2>Schritt 1: Abrufen und Konfigurieren von Zertifikaten</h2>

Gehen Sie folgenderma&szlig;en vor, um Zertifikate abzurufen und zu konfigurieren:
<OL>
<LI><a href="#obtaincert">Abrufen eines selbstsignierten Zertifikats f&uuml;r die Anleitung</a> &ndash; Rufen Sie mithilfe des MakeCert-Tools ein Zertifikat ab.</LI>
<LI><a href="#exportcert">Exportieren des Zertifikats im PFX-Format</a> &ndash; Exportieren Sie auf dem Server, auf dem das Zertifikat erstellt wurde, die CER-Datei als PFX-Datei (mit dem privaten Schl&uuml;ssel). </LI>
<LI><a href="#importcert">Importieren des PFX-Zertifikats auf dem VMM-Server</a> &ndash; Nach dem Export importieren Sie die PFX-Datei in den pers&ouml;nlichen Ordner im Speicher f&uuml;r den lokalen Computer auf dem VMM-Server, der f&uuml;r den Tresor registriert werden soll.</LI>
</OL>


<h3><a id="obtaincert"></a>Abrufen eines selbstsignierten Zertifikats (.cer)</h3>
<P>Erstellen Sie ein &bdquo;.cer x.509&ldquo;-Zertifikat, das alle Zertifikatsanforderungen erf&uuml;llt:</P>
<ol>
<LI>
Laden Sie auf dem Computer, auf dem Sie MakeCert ausf&uuml;hren m&ouml;chten, die neueste Version des <a href="http://go.microsoft.com/fwlink/?LinkId=378269">Windows SDK</a> herunter. Es ist nicht erforderlich, das ganze SDK zu installieren.</LI>
<ol>
<LI>W&auml;hlen Sie auf der Seite &bdquo;Speicherort angeben&ldquo; die Option <b>Install the Windows Software Development Kit for Windows 8.1 to this computer</b> aus.</LI>
<LI>Deaktivieren Sie auf der Seite &bdquo;Features zur Installation ausw&auml;hlen&ldquo; alle Optionen au&szlig;er <b>Windows Software Development Kit</b>.</LI>
<LI>Pr&uuml;fen Sie nach der Installation, ob makecert.exe im Ordner C:\ProgramFiles (x86)\Windows Kits\<i>WindowsVersion</i>\bin\x64 aufgef&uuml;hrt ist.</LI>
<LI>&Ouml;ffnen Sie eine Eingabeaufforderung (cmd.exe) mit Administratorrechten, und navigieren Sie zum Ordner mit der Datei makecert.exe.</LI> 
<LI>F&uuml;hren Sie den folgenden Befehl aus, um das selbstsignierte Zertifikat zu erstellen. Ersetzen Sie &quot;CertificateName&quot; durch den gew&uuml;nschten Namen f&uuml;r das Zertifikat, und geben Sie hinter -e das tats&auml;chliche Ablaufdatum f&uuml;r das Zertifikat an:</LI>
<code>
makecert.exe -r -pe -n CN=CertificateName -ss my -sr localmachine -eku 1.3.6.1.5.5.7.3.2 -len 2048 -e 01/01/2016 CertificateName.cer</code>
</ol>
<P>Ein Erfolgsstatus gibt an, dass das Zertifikat erstellt wurde. Es wird im gleichen Ordner wie makecert.exe gespeichert. F&uuml;r den Export k&ouml;nnen Sie es an einen g&uuml;nstigeren Speicherort verschieben.</P>

<h3><a id="exportcert"></a>Exportieren des Zertifikats im PFX-Format</h3>
<P>Gehen Sie die in diesem Verfahren beschriebenen Schritte aus, um die CER-Datei im PFX-Format zu exportieren.</P>
<ol>
<li>Ein Erfolgsstatus gibt an, dass das Zertifikat erstellt wurde. Es wird im gleichen Ordner wie makecert.exe gespeichert. F&uuml;r den Export k&ouml;nnen Sie es an einen g&uuml;nstigeren Speicherort verschieben.</li>
<li> Klicken Sie im Detailbereich auf das Zertifikat, das Sie verwalten m&ouml;chten.</li>
<li>Zeigen Sie im Men&uuml; <b>Aktion</b> auf <b>Alle Aufgaben</b>, und klicken Sie dann auf <b>Exportieren</b>. Der Zertifikatsexport-Assistent wird ge&ouml;ffnet. Klicken Sie auf <b>Weiter</b>.</li>
<li>Klicken Sie auf der Seite <b>Privaten Schl&uuml;ssel exportieren</b> auf <b>Ja</b>, um den privaten Schl&uuml;ssel zu exportieren. Klicken Sie auf <b>Weiter</b>. Bitte beachten Sie, dass dies nur dann erforderlich ist, wenn Sie den privaten Schl&uuml;ssel nach der Installation auf andere Server exportieren m&ouml;chten.</li>
<li>W&auml;hlen Sie auf der Seite &bdquo;Format der zu exportierenden Datei&ldquo; die Option <b>Privater Informationsaustausch &ndash; PKCS #12 (.PFX)</b> aus. Klicken Sie auf <b>Weiter</b>.</li>
<li>Geben Sie auf der Seite <b>Kennwort</b> das f&uuml;r die Verschl&uuml;sselung des privaten Schl&uuml;ssels verwendete Kennwort ein, und best&auml;tigen Sie es. Klicken Sie auf <b>Weiter</b>.</li>
<li>F&uuml;hren Sie die Schritte auf den &uuml;brigen Seiten des Assistenten aus, um das Zertifikat im PFX-Format zu exportieren.</li>
</ol>

<h3><a id="importcert"></a>Importieren des PFX-Zertifikats auf VMM-Server</h3>
<p>Kopieren Sie nach dem Export die PFX-Datei auf den VMM-Server und importieren Sie sie. Bitte beachten Sie: Wenn Sie MakeCert.exe auf dem VMM-Server ausgef&uuml;hrt haben, ist es f&uuml;r diesen Server nicht erforderlich, das Zertifikat zu importieren.</p>
 
<ol>
<li>Kopieren Sie die Zertifikatsdateien des privaten Schl&uuml;ssels (.pfx) an einen Speicherort auf dem lokalen Server.</li>
<li>W&auml;hlen Sie im MMC-Snap-in &bdquo;Zertifikate&ldquo; die Option <b>Computerkonto</b> aus, und klicken Sie auf <b>Weiter</b>.</li>
<li>W&auml;hlen Sie <b>Lokaler Computer</b> aus, und klicken Sie auf <b>Fertig stellen</b>.</li>
<li>Erweitern Sie in der MMC die Option <b>Zertifikate</b>, klicken Sie mit der rechten Maustaste auf <b>Pers&ouml;nlich</b>, zeigen Sie auf <b>Alle Aufgaben</b>, und klicken Sie anschlie&szlig;end auf <b>Importieren</b>, um den Zertifikatimport-Assistenten zu starten.</li>
<li>Klicken Sie auf der Seite &bdquo;Zu importierende Datei&ldquo; auf <b>Durchsuchen</b>, und wechseln Sie zu dem Ordner, in dem die PFX-Zertifikatsdatei mit dem zu importierenden Zertifikat enthalten ist. W&auml;hlen Sie die passende Datei aus, und klicken Sie auf <b>&Ouml;ffnen</b>.</li>
<li>Geben Sie auf der Kennwortseite im Feld <b>Kennwort</b> das Kennwort f&uuml;r die PFX-Datei des privaten Schl&uuml;ssels ein, und klicken Sie auf <b>Weiter</b>.</li>
<li>W&auml;hlen Sie auf der Seite &bdquo;Zertifikatspeicher&ldquo; die Option <b>Alle Zertifikate in folgendem Speicher platzieren</b> aus, klicken Sie auf <b>Durchsuchen</b>, w&auml;hlen Sie den Speicher <b>Pers&ouml;nlich</b> aus, und klicken Sie auf <b>OK</b> sowie auf <b>Weiter</b>. Beenden Sie den Assistenten.</li>
</ol>

Nach der Ausf&uuml;hrung dieser Schritte k&ouml;nnen Sie das CER-Zertifikat ausw&auml;hlen, das Sie beim Konfigurieren in den Tresor hochladen m&ouml;chten, und Sie k&ouml;nnen das PFX-Zertifikat ausw&auml;hlen, wenn Sie w&auml;hrend der Anbieter-Installation einen VMM-Server registrieren.
</div>

<a name="vault"></a>

## Schritt 2: Erstellen eines Tresors

</p>
1.  Melden Sie sich im [Verwaltungsportal][Verwaltungsportal] an.

2.  Erweitern Sie **Data Services**, erweitern Sie **Recovery Services**, und klicken Sie auf **Site Recovery-Tresor**.

3.  Klicken Sie auf **Neu erstellen** und anschließend auf **Schnellerfassung**.

4.  Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird.

5.  Wählen Sie unter **Region** die geografische Region für den Tresor aus. Zu den verfügbaren geografischen Regionen zählen Ostasien, Westeuropa, USA (Westen), USA (Osten), Nordeuropa und Südostasien.
6.  Klicken Sie auf **Tresor erstellen**.

    ![Neuer Tresor][Neuer Tresor]

Überprüfen Sie auf der Statusleiste, ob der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite von Recovery Services als **aktiv** aufgelistet.

<a name="upload"></a>

## Schritt 3: Konfigurieren des Tresors

1.  Klicken Sie auf der Seite **Recovery Services** auf den Tresor, um die Seite „Schnellstart“ zu öffnen. Schnellstart kann auch jederzeit über das Symbol geöffnet werden.

    ![Schnellstart-Symbol][Schnellstart-Symbol]

2.  Wählen Sie in der Dropdown-Liste **Setup Recovery** die Option **Zwischen einer lokalen Seite und Microsoft Azure** aus.
3.  Um das Zertifikat (.cer) zum Tresor hochzuladen, klicken Sie auf **Zertifikat verwalten**.

    ![Schnellstart][Schnellstart]

4.  Klicken Sie im Dialogfeld **Zertifikat verwalten** auf **Nach Datei suchen**, und wählen Sie die CER-Datei aus.

    ![Zertifikat verwalten][Zertifikat verwalten]

5.  Um einen Schlüssel für den Tresor zu generieren, klicken Sie auf **Tresorschlüssel abrufen**. Der Schlüssel wird automatisch generiert. Wenn Sie einen Schlüssel erneut generieren, wird der vorherige Schlüssel überschrieben. Beachten Sie, dass Sie diesen Schlüssel später benötigen, wenn Sie den Azure Site Recovery-Anbieter auf dem VMM-Server installieren.

    ![Zertifikat verwalten][1]

<a name="download"></a>

## Schritt 4: Installieren des Azure Site Recovery-Anbieters

1.  Klicken Sie auf der Seite **Schnellstart** auf **Anbieter herunterladen**, um die neueste Version der Anbieterinstallationsdatei abzurufen.

    ![Anbieter-Datei herunterladen][Anbieter-Datei herunterladen]

2.  Führen Sie diese Datei auf dem VMM-Quellserver aus.

    ![Agent herunterladen][Agent herunterladen]

3.  Nachdem der Anbieter installiert ist, fahren Sie mit der Einrichtung fort, um den Server im Tresor zu registrieren.
    ![Setup abgeschlossen][Setup abgeschlossen]
4.  Geben Sie auf der Seite „Internetverbindung“ an, wie der Anbieter auf dem VMM-Sever die Verbindung mit dem Internet herstellen soll. Klicken Sie auf **Weiter**, um die Standard-Internetverbindungseinstellungen zu verwenden, die auf dem Server konfiguriert sind.
    ![Interneteinstellungen][Interneteinstellungen]
5.  Gehen Sie auf der Tresorregistrierungsseite wie folgt vor:

    -   Wählen Sie den privaten Schlüssel (PFX) aus, den Sie an den VMM-Server importiert haben.
    -   Wählen Sie den Tresor aus, in dem Sie den Server registrieren möchten.
    -   Geben Sie den Tresorschlüssel an. Dies ist der Tresorschlüssel, den Sie zuvor generiert haben. Schneiden Sie den Schlüsselwert auf der Seite „Schnellstart“ aus, und fügen Sie ihn ein.

    ![Zertifikatregistrierung][Zertifikatregistrierung]

6.  Auf der Seite „Datenverschlüsselung“ können Sie angeben, ob Sie die Option zur Datenverschlüsselung während der Replikation für eine bestimmte Cloud zulassen möchten. Wenn Sie diese Option auswählen, wird automatisch ein SSL-Zertifikat generiert. Wenn Sie ein Failover ausführen, müssen Sie dieses Zertifikat auswählen. Nachdem Sie diese Einstellung aktiviert haben, können Sie die Datenverschlüsselung für eine Cloud im Azure Site Recovery-Portal aktivieren oder deaktivieren. Belassen Sie für dieses Lernprogramm die Standardeinstellung, und klicken Sie auf **Weiter**.

    ![Zertifikattresor][Zertifikattresor]

7.  Gehen Sie auf der Seite „VMM-Server“ wie folgt vor:

    -   Geben Sie einen Anzeigenamen für den VMM-Server an. Diese Name wird verwendet, um den Server in der Azure Site Recovery-Konsole zu identifizieren.
    -   Wählen Sie **Cloudmetadaten mit Tresor synchronisieren** aus, um Informationen über VMM-Clouds mit dem Azure Site Recovery-Tresor zu synchronisieren. Diese Aktion muss für jeden VMM-Server nur einmal ausgeführt werden. Wenn Sie nicht alle Clouds synchronisieren möchten, können Sie jede Cloud einzeln zur Synchronisierung veröffentlichen, bevor Sie die Schutzeinstellungen für die Clouds konfigurieren.

8.  Klicken Sie auf **Registrieren**, um den Prozess abzuschließen.

    ![PublishCloud][PublishCloud]

Nachdem ein Server erfolgreich registriert wurde, wird sein Anzeigename auf der Registerkarte **Ressourcen** der Seite „Server“ im Tresor angezeigt.

## <span id="storage"></span></a>Schritt 5: Installieren des Azure Recovery Services-Agent

Installieren Sie den Azure Recovery Services-Agent auf jedem Hyper-V-Hostserver in den VMM-Clouds, die Sie schützen möchten.

1.  Klicken Sie auf der Seite „Schnellstart“ auf **Azure Site Recovery Services-Agent herunterladen und auf Hosts installieren**, um die neueste Version der Agent-Installationsdatei abzurufen.

    ![Recovery Services-Agent installieren][Recovery Services-Agent installieren]

2.  Führen Sie die Installationsdatei auf jedem Hyper-V-Hostserver aus, der sich in den den VMM-Clouds befindet, die Sie schützen möchten.
3.  Klicken Sie auf der Seite „Voraussetzungsüberprüfung“ auf **Weiter**. Alle fehlenden Komponenten, die Voraussetzung sind, werden automatisch installiert.

![Voraussetzungen Recovery Services-Agent][Voraussetzungen Recovery Services-Agent]

1.  Geben Sie auf der Seite „Installationseinstellungen“ an, wo Sie den Agent installieren möchten, und wählen Sie den Cachespeicherort aus, in dem die Sicherungsmetadaten installiert werden. Klicken Sie dann auf **Weiter**.

Geben Sie auf der Seite „Internetverbindung“ an, wie der Anbieter auf dem VMM-Sever die Verbindung mit dem Internet herstellen soll. Klicken Sie auf **Weiter**, um die Standard-Internetverbindungseinstellungen zu verwenden, die auf dem Server konfiguriert sind.![Speicherort des Recovery Services-Agent][Speicherort des Recovery Services-Agent]

## <span id="clouds"></span></a>Schritt 6: Konfigurieren der Cloudschutzeinstellungen

Nachdem Sie die VMM-Server registriert haben, können Sie Schutzeinstellungen für die Clouds konfigurieren. Sie haben bei der Anbieterinstallation die Option **Clouddaten mit Tresor synchronisieren** aktiviert. Daher werden alle Clouds auf dem VMM-Server auf der Registerkarte **Geschützte Elemente** im Tresor angezeigt.

![Veröffentlichte Cloud][Veröffentlichte Cloud]

Konfigurieren Sie die Schutzeinstellungen wie folgt:

1.  Klicken Sie auf der Seite „Schnellstart“ auf **Schutz für VMM-Clouds einrichten**.

![Cloud-Schutz aktivieren][Cloud-Schutz aktivieren]

1.  Klicken Sie auf der Registerkarte **Geschützte Elemente** auf die Cloud, die Sie konfigurieren möchten, und gehen Sie zur Registerkarte **Konfiguration**.
2.  Wählen Sie unter **Ziel** die Option **Microsoft Azure** aus.
3.  Wählen Sie unter **Speicherkonto** den Azure-Speicher aus, den Sie zum Speichern der virtuellen Azure-Computer verwenden möchten.
4.  Legen Sie für **Gespeicherte Daten verschlüsseln** die Option **Aus** fest. Diese Einstellung gibt an, dass die Daten beim Replizieren zwischen dem lokalen Standort und Azure verschlüsselt werden sollen.
5.  Lassen Sie unter **Kopierhäufigkeit** die Standardeinstellung stehen. Dieser Wert gibt an, wie oft die Daten zwischen dem Quell- und dem Zielspeicherort synchronisiert werden sollen.
6.  Lassen Sie unter **Wiederherstellungspunkte beibehalten für** die Standardeinstellung stehen. Wenn die Standardeinstellung 0 (Null) aktiviert ist, wird nur der letzte Wiederherstellungspunkt für einen primären virtuellen Computer auf einem Replikathostserver gespeichert.
7.  Lassen Sie unter **Häufigkeit von anwendungskonsistenten Momentaufnahmen** die Standardeinstellung stehen. Dieser Wert gibt an, wie oft Momentaufnahmen erstellt werden. Momentaufnahmen verwenden VSS (Volume Shadow Copy Service, Volumenschattenkopie-Dienst), um sicherzustellen, dass Anwendungen zum Zeitpunkt der Momentaufnahme konsistent sind. Wenn Sie einen Wert festlegen möchten, stellen Sie sicher, dass er geringer als die Anzahl der konfigurierten zusätzlichen Wiederherstellungspunkte ist.

    ![Cloudkonfiguration][Cloudkonfiguration]

8.  Geben Sie unter **Startzeit für Replikation** an, wann die erste Datenreplikation an Azure starten sollte. Die Zeitzone auf dem Hyper-V-Hostserver wird verwendet. Es wird empfohlen, die erste Replikation außerhalb der Spitzenzeiten zu planen.

    ![Cloud-Replikationseinstellungen][Cloud-Replikationseinstellungen]

Nach dem Speichern der Einstellungen wird ein Auftrag erstellt und kann auf der Registerkarte **Aufträge** überwacht werden. Alle Hyper-V-Hostserver in der VMM-Quell-Cloud werden für die Replikation konfiguriert. Nach dem Speichern können die Cloudeinstellungen auf der Registerkarte **Konfigurieren** bearbeitet werden. Um den Zielspeicherort bzw. Zielspeicher zu ändern, müssen Sie die Cloudkonfiguration entfernen und dann die Cloud neu konfigurieren. Beachten Sie: Wenn Sie das Speicherkonto ändern, wird die Änderung nur für virtuelle Computer angewandt, für die der Schutz nach Änderung des Speicherkontos aktiviert wird. Vorhandene virtuelle Computer werden nicht an das neue Speicherkonto migriert.

## <span id="networkmapping"></span></a>Schritt 7: Konfigurieren der Netzwerkzuordnung

Optional können Sie Netzwerkzuordnung aktivieren, um VM-Quellnetzwerke zu virtuellen Azure-Zielnetzwerken zuzuordnen. Wenn Sie keine Netzwerkzuordnung erstellen, dann können nur die virtuellen Computer, die innerhalb des gleichen Wiederherstellungsplans ein Failover ausführen, in Azure eine Verbindung miteinander herstellen. Wenn Sie eine Netzwerkzuordnung erstellen, dann können alle virtuellen Computer, die im gleichen Netzwerk ein Failover ausführen, eine Verbindung miteinander herstellen, unabhängig vom Wiederherstellungsplan, zu dem sie gehören. Wenn ein Netzwerk-Gateway auf dem Azure-Zielnetzwerk eingerichtet ist, können die virtuellen Computer zudem eine Verbindung zu den lokalen virtuellen Computern herstellen. Wenn Sie die Netzwerkzuordnung im Rahmen dieses Lernprogramms konfigurieren möchten, finden Sie Informationen unter [Konfigurieren der Netzwerkzuordnung][Konfigurieren der Netzwerkzuordnung] im Bereitstellungshandbuch.

## <span id="virtualmachines"></span></a>Schritt 8: Aktivieren des Schutzes für virtuelle Computer

Nach der korrekten Konfiguration von Servern, Clouds und Netzwerken können Sie den Schutz für die virtuellen Computer in der Cloud aktivieren.

Bevor Sie Schutz für einen virtuellen Computer aktivieren, überprüfen und aktualisieren Sie ggf. die Einstellungen. So muss z. B. das Gastbetriebssystem auf dem virtuellen Computer Windows Server 2008 oder höher bzw. Linux sein. Es muss sich um einen virtuellen Computer der 1. Generation handeln. Eine vollständige Liste der Anforderungen für Azure Site Recovery finden Sie unter [Voraussetzungen und Support][Voraussetzungen und Support] im Planungshandbuch.

Prüfen und aktualisieren Sie die Einstellungen in der VMM-Konsole. Sie ändern die Betriebssystemeinstellungen für den virtuellen Computer auf der Seite „Allgemein“ der Eigenschaften des virtuellen Computers. Die Betriebssystem-Festplatteneinstellungen werden auf der Seite „Hardwarekonfiguration“ aktualisiert.

![Eigenschaften des virtuellen Computers ändern][Eigenschaften des virtuellen Computers ändern]

![Eigenschaften des virtuellen Computers ändern][2]

1.  Um Schutz zu aktivieren, klicken Sie auf der Registerkarte **Virtuelle Computer** in der Cloud, in der sich der virtuelle Computer befindet, auf **Schutz aktivieren** und dann auf **Virtuelle Computer hinzufügen**.
2.  Wählen Sie aus der Liste der virtuellen Computer in der Cloud denjenigen aus, den Sie schützen möchten.

![Schutz für virtuellen Computer aktivieren][Schutz für virtuellen Computer aktivieren]

Nachdem der Schutz aktiviert wurde, werden zwei Aufträge erstellt. Der Auftrag "Schutz aktivieren" wird ausgeführt. Nach der ersten Replikation wird der Auftrag zum Abschließen des Schutzes ausgeführt. Der virtuelle Computer ist erst dann für ein Failover bereit, wenn diese Aufträge erfolgreich abgeschlossen sind. Der Fortschritt kann auf der Registerkarte **Aufträge** überwacht werden.

![Schutzauftrag für virtuellen Computer][Schutzauftrag für virtuellen Computer]

## <span id="recoveryplans"></span></a>Schritt 9: Konfigurieren und Ausführen von Wiederherstellungsplänen

Ein Wiederherstellungsplan fasst virtuelle Computer in Gruppen zusammen, damit sie ein Failover als eine Einheit ausführen können. Gehen Sie folgendermaßen vor, um einen Wiederherstellungsplan zu erstellen:

1.  Klicken Sie auf der Registerkarte **Wiederherstellungspläne** auf **Erstellen**.
2.  Wählen Sie auf der Seite „Name und Ziel für Wiederherstellungsplan angeben“ den VMM-Quellserver und Azure als Ziel aus.

    ![Wiederherstellungsplan erstellen][Wiederherstellungsplan erstellen]

3.  Wahlen Sie auf der Seite "Virtuelle Computer auswählen" die virtuellen Computer aus, die zum Wiederherstellungsplan hinzugefügt werden sollen. Nur virtuelle Computer mit aktivierten Schutz werden angezeigt. Die virtuellen Computer werden zur Standardgruppe des Wiederherstellungsplans (Gruppe 1) hinzugefügt.
4.  Aktivieren Sie das Kontrollkästchen, um den Wiederherstellungsplan zu erstellen.

    ![Wiederherstellungspläne für virtuelle Computer][Wiederherstellungspläne für virtuelle Computer]

### <span id="run"></span></a>Testen eines Failovers

Wiederherstellungspläne können als Teil eines proaktiven Tests oder eines geplanten Failovers und während eines ungeplanten Failovers ausgeführt werden. In dieser Anleitung wird beschrieben, wie Sie einen Test-Failover von VMM an Azure ausführen, um zu überprüfen, ob die Failover-Strategie wie erwartet funktioniert. Das Test-Failover simuliert Ihre Failover- und Wiederherstellungsmechanismen in einem isolierten Netzwerk. Beachten Sie Folgendes:

-   Wenn Sie nach dem Failover eine Verbindung mit dem virtuellen Computer in Azure über Remote Desktop herstellen möchten, aktivieren Sie die Remote Desktop-Verbindung auf dem virtuellen Computer, bevor Sie das Test-Failover ausführen.
-   Nach dem Failover verwenden Sie eine öffentliche IP-Adresse, um eine Verbindung zum virtuellen Computer in Azure über Remote Desktop herzustellen. Wenn Sie dies möchten, stellen Sie sicher, dass keine Domänenrichtlinien vorhanden sind, die das Verbinden zu einem virtuellen Computer über eine öffentliche Adresse verhindern.

</p>
#### 

<p>
<span id="runtest"></span></a>Ausführung des Failovers

</h5>
Gehen Sie folgendermaßen vor, um ein Test-Failover für einen Wiederherstellungsplan durchzuführen:

</p>
1.  Bevor Sie den Wiederherstellungsplan ausführen, können Sie die Einstellungen der virtuellen Computer im Plan validieren. Klicken Sie hierzu auf der Eigenschaftenseite für die Cloud auf den virtuellen Computer. Überprüfen Sie die Einstellungen auf der Seite „Quell- und Zieleigenschaften für Failover“. Prüfen Sie insbesondere, dass die vorgeschlagene Größe für den virtuellen Zielcomputer in Azure korrekt ist und dass die Netzwerkeinstellungen stimmen. Eine vollständige Liste der Voraussetzungen für virtuelle Computer finden Sie unter [Voraussetzungen und Support][3].

![Eigenschaften für virtuelle Computer][Eigenschaften für virtuelle Computer]

1.  Wählen Sie den gewünschten Wiederherstellungsplan auf der Registerkarte **Wiederherstellungspläne** aus.
2.  Klicken Sie auf die Schaltfläche **Test-Failover**, um das Failover zu initiieren.
3.  Wählen Sie auf der Seite „Test-Failover bestätigen“ das Azure-Netzwerk aus, mit dem die virtuellen Computer nach dem Failover verbunden werden. Optional können Sie **Kein Netzwerk** auswählen. Wenn diese Einstellung ausgewählt ist, werden die virtuellen Computer nach dem Failover nicht mit einem Netzwerk verbunden.

![Test-Failover][Test-Failover]

Sie können den Fortschritt der Test-Failover-Aufträge auf der Registerkarte **Aufträge** anzeigen. Gehen Sie nach dem Test-Failover folgendermaßen vor:

1.  Prüfen Sie, ob die virtuellen Computer in Azure erfolgreich starten.
2.  Klicken Sie auf **Notizen**, um alle Beobachtungen im Zusammenhang mit dem Test-Failover aufzuzeichnen und zu speichern.
3.  Zusätzlich zu den Details auf der Registerkarte **Jobs** wird beim Ausführen eines Test-Failovers für einen Wiederherstellungsplan der Prozess auf der Detailseite des Wiederherstellungsplans angezeigt. Sie können Schritte und Status des Failovers anzeigen und Notizen für das Test-Failover anzeigen oder erstellen.

#### 

<p>
<span id="runtest"></span></a>Aktivität überwachen

</h5>

Verwenden Sie die Registerkarte **Jobs** und das **Dashboard**, um die wichtigsten Aufträge anzuzeigen und zu überwachen, die vom Azure Site Recovery-Tresor durchgeführt werden. Dazu zählen das Konfigurieren von Schutz für eine Cloud, Aktivieren und Deaktivieren von Schutz für einen virtuellen Computer, Ausführen eines Failovers (geplant, ungeplant oder Test) und Übernehmen eines ungeplanten Failovers.

</p>
Auf der Registerkarte **Jobs** können Sie Aufträge anzeigen, ein Drill-Down zu den Auftragsdetails und -fehlern durchführen, Auftragsabfragen ausführen, um Aufträge abzurufen, die bestimmte Kriterien erfüllen, Aufträge nach Excel exportieren und fehlgeschlagene Aufträge neu starten.

Über das **Dashboard** können Sie die neuesten Versionen der Anbieter- und Agent-Installationsdateien herunterladen, Konfigurationsinformationen für den Tresor erhalten, die Anzahl der virtuellen Computer anzeigen, deren Schutz vom Tresor verwaltet wird, kürzlich ausgeführte Aufträge anzeigen und virtuelle Computer neu synchronisieren.

Weitere Informationen über Interaktionen mit Aufträgen und dem Dashboard finden Sie im [Betriebs- und Überwachungshandbuch][Betriebs- und Überwachungshandbuch].

## <span id="next"></span></a>Nächste Schritte

-   Informationen zum Planen und Bereitstellen von Azure Site Recovery in einer vollständigen Produktionsumgebung finden Sie im [Planungshandbuch für Azure Site Recovery][Planen der Azure Site Recovery-Bereitstellung] und im [Bereitstellungshandbuch für Azure Site Recovery][Bereitstellungshandbuch für Azure Site Recovery].
-   Wenn Sie Fragen haben, besuchen Sie das [Azure Recovery Services-Forum][Azure Recovery Services-Forum]

  [Planen der Azure Site Recovery-Bereitstellung]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Azure Recovery Services-Forum]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Voraussetzungen und Support]: http://go.microsoft.com/fwlink/?LinkId=402602
  [Verwaltungsportal]: https://manage.windowsazure.com
  [Neuer Tresor]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Schnellstart-Symbol]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Schnellstart]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStart.png
  [Zertifikat verwalten]: ./media/hyper-v-recovery-manager-configure-vault/SR_ManageCert.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_VaultKey.png
  [Anbieter-Datei herunterladen]: ./media/hyper-v-recovery-manager-configure-vault/SR_DownloadProviderFile.png
  [Agent herunterladen]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderInstall1.png
  [Setup abgeschlossen]: ./media/hyper-v-recovery-manager-configure-vault/SR_InstallWiz.png
  [Interneteinstellungen]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Zertifikatregistrierung]: ./media/hyper-v-recovery-manager-configure-vault/SR_CertReg1.png
  [Zertifikattresor]: ./media/hyper-v-recovery-manager-configure-vault/SR_CertReg2.png
  [PublishCloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMServerName.png
  [Recovery Services-Agent installieren]: ./media/hyper-v-recovery-manager-configure-vault/SR_HyperVAgent.png
  [Voraussetzungen Recovery Services-Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png
  [Speicherort des Recovery Services-Agent]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentInstallSettings.png
  [Veröffentlichte Cloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_Clouds.png
  [Cloud-Schutz aktivieren]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableCloudProtection.png
  [Cloudkonfiguration]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudConfigureE2A1.png
  [Cloud-Replikationseinstellungen]: ./media/hyper-v-recovery-manager-configure-vault/SR_PublishCloudSetup2.png
  [Konfigurieren der Netzwerkzuordnung]: http://go.microsoft.com/fwlink/?LinkId=402533
  [Eigenschaften des virtuellen Computers ändern]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMVMPropsGeneral.png
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMVMPropsHW.png
  [Schutz für virtuellen Computer aktivieren]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Schutzauftrag für virtuellen Computer]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Wiederherstellungsplan erstellen]: ./media/hyper-v-recovery-manager-configure-vault/SR_RecoveryPlan1.png
  [Wiederherstellungspläne für virtuelle Computer]: ./media/hyper-v-recovery-manager-configure-vault/SR_RecoveryPlan2.png
  [3]: http://go.microsoft.com/fwlink/?LinkId=402676
  [Eigenschaften für virtuelle Computer]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMPropertiesE2A.png
  [Test-Failover]: ./media/hyper-v-recovery-manager-configure-vault/SR_TestFailover.png
  [Betriebs- und Überwachungshandbuch]: http://go.microsoft.com/fwlink/?LinkId=398534
  [Bereitstellungshandbuch für Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321295
