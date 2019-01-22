---
title: 'Início rápido: Criar um cliente de moderação para o .NET – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Como retornar um cliente do Content Moderator usando o SDK do Content Moderator do Azure para .NET
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: aa3c2d5c408969aa3127562607ca8d4e89f44f2e
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262261"
---
# <a name="quickstart-helper-code-to-return-a-content-moderator-client"></a>Início Rápido: Código auxiliar para retornar um cliente do Content Moderator

Este artigo fornece informações e exemplos de código para ajudá-lo a começar a usar o SDK do Content Moderator para .NET para criar um cliente de Content Moderator para a sua assinatura.

A biblioteca é usada por outros tutoriais nesta seção.

Este artigo pressupõe que você já esteja familiarizado com Visual Studio e C#.

> [!IMPORTANT]
> Essa biblioteca de classe contém código destinado apenas para fins de demonstração.
> Se você adaptar este código para uso em produção, use um método seguro de armazenamento e use sua chave de assinatura do Content Moderator.

## <a name="sign-up-for-content-moderator-services"></a>Inscreva-se para serviços do Content Moderator

Uma chave de assinatura é necessária antes de usar os serviços do Content Moderator através da API REST ou do SDK.
Consulte o início rápido [Experimentar o Content Moderator na Web](quick-start.md) para saber como você pode obter a chave.

## <a name="create-your-visual-studio-project"></a>Criar seu projeto do Visual Studio

1. Criar um novo projeto **Biblioteca de classes (.NET Framework)**.

   No código de exemplo, chamei o projeto **ModeratorHelper**.

1. Adicionar uma referência ao assembly do Framework **System.Configuration**.

### <a name="install-required-packages"></a>Instalar os pacotes necessários

Instale os seguintes pacotes NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="create-the-content-moderator-client"></a>Criar o cliente do Content Moderator

Substitua o conteúdo do arquivo ModeratorHelper.cs pelo seguinte código:

    using Microsoft.CognitiveServices.ContentModerator;

    namespace ModeratorHelper
    {
    /// <summary>
    /// Wraps the creation and configuration of a Content Moderator client.
    /// </summary>
    /// <remarks>This class library contains insecure code. If you adapt this 
    /// code for use in production, use a secure method of storing and using
    /// your Content Moderator subscription key.</remarks>
    public static class Clients
    {
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "myRegion";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "myKey";

        /// <summary>
        /// Returns a new Content Moderator client for your subscription.
        /// </summary>
        /// <returns>The new client.</returns>
        /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
        /// When you have finished using the client,
        /// you should dispose of it either directly or indirectly. </remarks>
        public static ContentModeratorClient NewClient()
        {
            // Create and initialize an instance of the Content Moderator API wrapper.
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

            client.BaseUrl = AzureBaseURL;
            return client;
        }
    }
    }


> [!IMPORTANT]
> Atualize os campos **AzureRegion** e **CMSubscriptionKey** com os valores de sua chave de assinatura e o identificador de região.

Agora você tem uma maneira rápida de criar um cliente de Content Moderator para a sua assinatura.

## <a name="next-steps"></a>Próximas etapas

[Fazer o download da a solução do Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para este e outros guias de início rápido do Content Moderator para .NET e começar a integração.
