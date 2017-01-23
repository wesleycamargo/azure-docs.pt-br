---
title: "Atualizar os Serviços de Mídia após implantar as chaves de acesso de armazenamento | Microsoft Docs"
description: "Este artigo fornece diretrizes sobre como atualizar os Serviços de Mídia após implantar chaves de acesso de armazenamento."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: milanga;cenkdin;juliako
translationtype: Human Translation
ms.sourcegitcommit: 63669ba827771c75937568276f32b130980f3f65
ms.openlocfilehash: e236c31e7fbce304ceaa069235b23efb180fb111


---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Atualizar os Serviços de Mídia após implantar chaves de acesso de armazenamento
Ao criar uma nova conta dos Serviços de Mídia do Azure, você também é solicitado a selecionar uma conta de Armazenamento do Azure, que é usada para armazenar o conteúdo de mídia. Observe que você pode [adicionar mais de uma conta de armazenamento](meda-services-managing-multiple-storage-accounts.md) à sua conta dos Serviços de Mídia.

Quando uma nova conta de armazenamento é criada, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticar o acesso à sua conta de armazenamento. Para manter as conexões de armazenamento mais seguro, é recomendável regenerar periodicamente e fazer uma rotação de sua chave de acesso de armazenamento. Duas chaves de acesso (primária e secundária) são fornecidas para habilitá-lo a manter conexões com a conta de armazenamento usando uma chave de acesso enquanto regenera a outra chave de acesso. Esse procedimento também é chamado de "implantação de chaves de acesso".

Os Serviços de Mídia dependem de uma chave de armazenamento fornecida a eles. Especificamente, os localizadores que são usados para transmitir ou baixar os ativos dependem da chave de acesso de armazenamento especificada. Quando uma conta AMS é criada, ela usa uma dependência na chave de acesso de armazenamento primário por padrão; no entanto, como um usuário, você pode atualizar a chave de armazenamento que o AMS tem. Você deve garantir que os Serviços de Mídia saibam qual chave usar seguindo as etapas descritas neste tópico. Além disso, durante a implantação das chaves de acesso de armazenamento, você precisa atualizar seus localizadores para que não haja interrupção do serviço de streaming (essa etapa também é descrita no tópico).

> [!NOTE]
> Se tiver várias contas de armazenamento, você deverá executar esse procedimento com cada conta de armazenamento.
>
> Antes de executar as etapas descritas neste tópico em uma conta de produção, teste-as em uma conta de pré-produção.
>
>

## <a name="step-1-regenerate-secondary-storage-access-key"></a>Etapa 1: Regenerar a chave de acesso de armazenamento secundária
Comece com a regeneração da chave de armazenamento secundária. Por padrão, a chave secundária não é usada pelos Serviços de Mídia.  Para obter informações sobre chaves de acesso do armazenamento, consulte [Como exibir, copiar e regenerar chaves de acesso de armazenamento](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys).

## <a name="a-idstep2astep-2--update-media-services-to-use-the-new-secondary-storage-key"></a><a id="step2"></a>Etapa 2: Atualizar os Serviços de Mídia para usar a nova chave de armazenamento secundária
Atualize os Serviços de Mídia para usar a chave de acesso de armazenamento secundária. Você pode usar um dos dois métodos a seguir para sincronizar a chave de armazenamento regenerada com os Serviços de Mídia.

* Use o portal do Azure: Para localizar os valores de Nome e Chave, vá para o portal do Azure e selecione sua conta. A janela Configurações aparece à direita. Na janela Configurações, selecione Chaves. Dependendo de qual chave de armazenamento você desejar sincronizar com os Serviços de Mídia, selecione o botão para sincronizar a chave primária ou sincronizar a chave secundária. Nesse caso, use a chave secundária.
* Use a API REST de gerenciamento dos Serviços de Mídia.

O exemplo de código a seguir mostra como criar a solicitação https://endpoint/*subscriptionId*/services/mediaservices/Accounts/*accountName*/StorageAccounts/*storageAccountName*/Key para sincronizar a chave de armazenamento especificada com os Serviços de Mídia. Nesse caso, o valor da chave de armazenamento secundária é usado. Para obter mais informações, veja [Como: usar a API REST de Gerenciamento dos Serviços de Mídia](http://msdn.microsoft.com/library/azure/dn167656.aspx).

    public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
    {
        var clientCert = GetCertificate(CertThumbprint);

        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
        request.Method = "PUT";
        request.ContentType = "application/json; charset=utf-8";
        request.Headers.Add("x-ms-version", "2011-10-01");
        request.Headers.Add("Accept-Encoding: gzip, deflate");
        request.ClientCertificates.Add(clientCert);


        using (var streamWriter = new StreamWriter(request.GetRequestStream()))
        {
            streamWriter.Write("\"");
            streamWriter.Write(storageAccountKey);
            streamWriter.Write("\"");
            streamWriter.Flush();
        }

        using (var response = (HttpWebResponse)request.GetResponse())
        {
            string jsonResponse;
            Stream receiveStream = response.GetResponseStream();
            Encoding encode = Encoding.GetEncoding("utf-8");
            if (receiveStream != null)
            {
                var readStream = new StreamReader(receiveStream, encode);
                jsonResponse = readStream.ReadToEnd();
            }
        }
    }

Após essa etapa, atualize os localizadores existentes (que têm dependência em relação à chave de armazenamento antiga) conforme demonstrado na etapa a seguir.

> [!NOTE]
> Aguarde 30 minutos antes de executar quaisquer operações com os Serviços de Mídia (por exemplo, criar novos localizadores) para impedir que haja impacto em relação a trabalhos pendentes.
>
>

## <a name="step-3-update-locators"></a>Etapa 3: Atualizar localizadores
> [!NOTE]
> Durante a implantação de chaves de acesso de armazenamento, você precisa atualizar seus localizadores existentes para que não haja interrupção do serviço de streaming.
>
>

Aguarde pelo menos 30 minutos após a sincronização a nova chave de armazenamento com o AMS. Então, é possível atualizar os localizadores OnDemand para que eles tenham dependência em relação à chave de armazenamento especificada e mantenham a URL existente.

Observe que ao atualizar (ou recriar) um localizador SAS, a URL sempre vai mudar.

> [!NOTE]
> Para certificar-se de preservar as URLs existentes de seus localizadores OnDemand, você precisa excluir o localizador existente e criar um novo com a mesma ID.
>
>

O exemplo .NET a seguir mostra como recriar um localizador com a mesma ID.

    private static ILocator RecreateLocator(CloudMediaContext context, ILocator locator)
    {
    // Save properties of existing locator.
    var asset = locator.Asset;
    var accessPolicy = locator.AccessPolicy;
    var locatorId = locator.Id;
    var startDate = locator.StartTime;
    var locatorType = locator.Type;
    var locatorName = locator.Name;

    // Delete old locator.
    locator.Delete();

    if (locator.ExpirationDateTime <= DateTime.UtcNow)
        {
            throw new Exception(String.Format(
                "Cannot recreate locator Id={0} because its locator expiration time is in the past",
                locator.Id));
        }

        // Create new locator using saved properties.
        var newLocator = context.Locators.CreateLocator(
            locatorId,
            locatorType,
            asset,
            accessPolicy,
            startDate,
            locatorName);



        return newLocator;
    }


## <a name="step-5-regenerate--primary-storage-access-key"></a>Etapa 5: Regenerar a chave de acesso de armazenamento primária
Regenere a chave de acesso de armazenamento primária. Para obter informações sobre chaves de acesso do armazenamento, consulte [Como exibir, copiar e regenerar chaves de acesso de armazenamento](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys).

## <a name="step-6-update-media-services-to-use-the-new-primary-storage-key"></a>Etapa 6: Atualizar os Serviços de Mídia para usar a nova chave de armazenamento primária
Use o mesmo procedimento, conforme descrito na [etapa 2](media-services-roll-storage-access-keys.md#step2) , mas, desta vez, sincronize a nova chave de acesso de armazenamento primária com a conta dos Serviços de Mídia.

> [!NOTE]
> Aguarde 30 minutos antes de executar quaisquer operações com os Serviços de Mídia (por exemplo, criar novos localizadores) para impedir que haja impacto em relação a trabalhos pendentes.
>
>

## <a name="step-7-update-locators"></a>Etapa 7: Atualizar localizadores
Após 30 minutos, é possível atualizar os localizadores OnDemand para que eles tenham dependência em relação à nova chave de armazenamento primária e mantenham a URL existente.

Use o mesmo procedimento, conforme descrito na [etapa 3](media-services-roll-storage-access-keys.md#step-3-update-locators).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Agradecimentos
Gostaríamos de agradecer às pessoas que contribuíram para a criação deste documento: Cenk Dingiloglu, Milan Gada, Seva Titov.



<!--HONumber=Dec16_HO2-->


