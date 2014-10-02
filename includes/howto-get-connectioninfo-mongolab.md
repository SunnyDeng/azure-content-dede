Wenn Sie eine MongoLab-Datenbank bereitstellen, sendet MongoLab einen Verbindungs-URI im MongoLab-Standardformat für Verbindungszeichenfolgen an Azure. Mithilfe dieses Werts wird eine MongoDB-Verbindung über den von Ihnen ausgewählten MongoDB-Treiber hergestellt. Weitere Informationen über Verbindungszeichenfolgen erhalten Sie auf mongodb.org unter [Verbindungen][].

**Dieser URI enthält Ihren Benutzernamen und Ihr Kennwort für die Datenbank. Behandeln Sie diese Angaben als kritische Informationen, und teilen Sie sie niemandem mit.**

Gehen Sie folgendermaßen vor, um diesen URI im Azure-Portal abzurufen:

1.  Wählen Sie **Add-Ons**.  
    ![AddonsButton][]
2.  Suchen Sie in Ihrer Add-On-Liste nach Ihrem MongoLab-Dienst.  
    ![MongolabEntry][]
3.  Klicken Sie auf den Namen Ihres Add-Ons, um auf die Add-On-Seite zu gelangen.
4.  Klicken Sie auf **Verbindungsinformationen**.  
    ![ConnectionInfoButton][]  
    Ihr MongoLab-URI wird angezeigt:  
    ![ConnectionInfoScreen][]  
5.  Klicken Sie auf die Schaltfläche rechts neben dem Wert MONGOLAB\_URI, um den vollständigen Wert in die Zwischenablage zu kopieren.

  [Verbindungen]: http://www.mongodb.org/display/DOCS/Connections
  [AddonsButton]: ./media/howto-get-connectioninfo-mongolab/button-addons.png
  [MongolabEntry]: ./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png
  [ConnectionInfoButton]: ./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png
  [ConnectionInfoScreen]: ./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png
