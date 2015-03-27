<properties 
	pageTitle="Konfigurieren von Übermittlungsrichtlinien für Medienobjekte mit .NET" 
	description="In diesem Thema wird die Konfiguration verschiedener Übermittlungsrichtlinien für Medienobjekte erläutert." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="juliako"/>

# Vorgehensweise: Konfigurieren von Übermittlungsrichtlinien für Medienobjekte
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](../media-services-video-on-demand-workflow) und [Media Services: Livestreaming-Workflow](../media-services-live-streaming-workflow) . 

Einer der Schritte im Workflow zur Inhaltsübermittlung in Media Services ist das Konfigurieren von Übermittlungsrichtlinien für Medienobjekte, die gestreamt werden sollen. Anhand der Übermittlungsrichtlinie für Medienobjekte kann Media Services ermitteln, wie das Medienobjekt übermittelt werden soll, also mit welchem Streamingprotokoll das Medienobjekt dynamisch verpackt werden soll (z. B. MPEG-DASH, HLS, Smooth Streaming oder alle) und ob und wie das Medienobjekt ggf. dynamisch verschlüsselt werden soll (Umschlag- oder allgemeine Verschlüsselung). 

In diesem Thema wird erläutert, warum und wie Übermittlungsrichtlinien für Medienobjekte erstellt und konfiguriert werden. 

>[AZURE.NOTE]Zur Verwendung der dynamischen Paketerstellung und Verschlüsselung müssen Sie über mindestens eine Skalierungseinheit (auch als Streamingeinheit bezeichnet) verfügen. Weitere Informationen finden Sie unter [Skalieren eines Mediendiensts](../media-services-manage-origins#scale_streaming_endpoints). 
>
>Darüber hinaus muss Ihr Medienobjekt einen MP4-Satz bzw. Smooth Streaming-Dateien mit adaptiver Bitrate enthalten.      

Sie können verschiedene Richtlinien auf dasselbe Medienobjekt anwenden. Sie könnten z. B. eine PlayReady-Verschlüsselung auf Smooth Streaming und AES-Umschlagverschlüsselung auf MPEG-DASH und HLS anwenden. Alle Protokolle, die nicht in einer Übermittlungsrichtlinie definiert sind (wenn Sie z. B. eine einzelne Richtlinie hinzufügen, die nur HLS als Protokoll angibt), werden vom Streaming ausgeschlossen. Die einzige Ausnahme besteht darin, wenn Sie überhaupt keine Übermittlungsrichtlinie für Medienobjekte definiert haben. In diesem Fall sind alle Protokolle ohne Verschlüsselung zulässig.

Wenn Sie ein speicherverschlüsseltes Medienobjekt übermitteln möchten, müssen Sie die Übermittlungsrichtlinie für Medienobjekte konfigurieren. Bevor das Medienobjekt gestreamt werden kann, wird die Speicherverschlüsselung vom Streamingserver entfernt und der Inhalt mithilfe der angegebenen Übermittlungsrichtlinie gestreamt. Wenn Sie ein Medienobjekt für die Übermittlung beispielsweise mit einem Schlüssel für die AES (Advanced Encryption Standard)-Umschlagverschlüsselung verschlüsseln möchten, legen Sie den Richtlinientyp auf **DynamicEnvelopeEncryption** fest. Um die Speicherverschlüsselung zu entfernen und das Medienobjekt unverschlüsselt zu streamen, legen Sie den Richtlinientyp auf **NoDynamicEncryption** fest. In den folgenden Beispielen wird die Konfiguration dieser Richtlinientypen veranschaulicht. 

Je nachdem, wie Sie die Übermittlungsrichtlinie für Medienobjekte konfigurieren, können Sie die folgenden Streamingprotokolle dynamisch verpacken, dynamisch verschlüsseln und streamen: Smooth Streaming-, HLS-, MPEG-DASH- und HDS-Streams.  

Die folgende Liste enthält die Formate, die Sie zum Streamen von Smooth, HLS, DASH und HDS verwenden.  

Smooth Streaming:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 

HDS

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

Anweisungen zum Veröffentlichen eines Medienobjekts und Erstellen einer Streaming-URL finden Sie unter [Erstellen einer Streaming-URL](../media-services-deliver-streaming-content).

## Löschen einer Übermittlungsrichtlinie für Medienobjekte 

Mit der folgenden **ConfigureClearAssetDeliveryPolicy**-Methode wird angegeben, keine dynamische Verschlüsselung anzuwenden und den Stream unter Verwendung eines der folgenden Protokolle zu übermitteln:  MPEG-DASH-, HLS- und Smooth Streaming-Protokoll. 
  
Im Abschnitt [Beim Definieren von AssetDeliveryPolicy verwendete Typen](#types) wird erläutert, welche Werte Sie beim Erstellen von AssetDeliveryPolicy angeben können. 

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption, 
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

        asset.DeliveryPolicies.Add(policy);
    }

## DynamicCommonEncryption-Übermittlungsrichtlinie für Medienobjekte 


Durch die folgende **CreateAssetDeliveryPolicy**-Methode wird **AssetDeliveryPolicy** erstellt. Die Richtlinie ist für die Anwendung der dynamischen allgemeinen Verschlüsselung (**DynamicCommonEncryption**) auf ein Smooth Streaming-Protokoll konfiguriert (andere Protokolle werden vom Streaming ausgeschlossen). Die Methode akzeptiert zwei Parameter: **Asset** (das Medienobjekt, auf das die Übermittlungsrichtlinie angewendet werden soll) und **IContentKey** (der Inhaltsschlüssel des **CommonEncryption**-Typs. Weitere Informationen finden Sie unter [Erstellen eines Inhaltsschlüssels](../media-services-dotnet-create-contentkey#common_contentkey)zur Verfügung steht).

Im Abschnitt [Beim Definieren von AssetDeliveryPolicy verwendete Typen](#types) wird erläutert, welche Werte Sie beim Erstellen von AssetDeliveryPolicy angeben können. 


    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }



## DynamicEnvelopeEncryption-Übermittlungsrichtlinie für Medienobjekte 

Durch die folgende **CreateAssetDeliveryPolicy**-Methode wird **AssetDeliveryPolicy** erstellt. Die Richtlinie ist für die Anwendung der dynamischen Umschlagverschlüsselung (**DynamicEnvelopeEncryption**) auf HLS- und DASH-Protokolle konfiguriert (andere Protokolle werden vom Streaming ausgeschlossen). Die Methode akzeptiert zwei Parameter: **Asset** (das Medienobjekt, auf das die Übermittlungsrichtlinie angewendet werden soll) und **IContentKey** (der Inhaltsschlüssel des **EnvelopeEncryption**-Typs. Weitere Informationen finden Sie unter [Erstellen eines Inhaltsschlüssels](../media-services-dotnet-create-contentkey#envelope_contentkey)zur Verfügung steht).


Im Abschnitt [Beim Definieren von AssetDeliveryPolicy verwendete Typen] wird erläutert, welche Werte Sie beim Erstellen von AssetDeliveryPolicy angeben(#types) können.   

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


## <a id="types"></a>Beim Definieren von AssetDeliveryPolicy verwendete Typen

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

### <a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp
    }

### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,
    }
<!--HONumber=47-->
