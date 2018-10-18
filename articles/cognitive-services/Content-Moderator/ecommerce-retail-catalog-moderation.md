---
title: 'Tutorial: Moderação de catálogo de comércio eletrônico – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Modere automaticamente catálogos de comércio eletrônico com o aprendizado de máquina e a IA.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: sajagtap
ms.openlocfilehash: 0bd61c3f1a4f660076be4e87bb5443302e5dc013
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363987"
---
# <a name="tutorial-ecommerce-catalog-moderation-with-machine-learning"></a>Tutorial: Moderação de catálogo de comércio eletrônico com o aprendizado de máquina

Neste tutorial, aprendemos como implementar a moderação de catálogo de comércio eletrônico inteligente com base em aprendizado de máquina, combinando tecnologias de AI assistido por computador com moderação humana para fornecer um sistema de catálogo inteligente.

![Imagens de produto classificado](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="business-scenario"></a>Cenário empresarial

Use tecnologias assistidas por computador para classificar e moderar imagens de produtos nessas categorias:

1. Adulto (nudez)
2. Obsceno (sugestivo)
3. Celebridades
4. Sinalizadores EUA
5. Brinquedos
6. Canetas

## <a name="tutorial-steps"></a>Etapas do tutorial

Este tutorial orienta você pelas etapas a seguir:

1. Inscrever-se e criar um agrupamento do Content Moderator.
2. Configure marcas de moderação (rótulos) para conteúdo de sinalizador e celebridade potencial.
3. Use o API de imagem do Content Moderator para verificar possível conteúdo adulto e obsceno.
4. Use a API da Pesquisa Visual Computacional para verificar se há possíveis celebridades.
5. Use o Serviço de Visão Personalizada para verificar se há possíveis presença de sinalizadores.
6. Apresente os resultados da verificação matizados para análise humana e tomada de decisão final.

## <a name="create-a-team"></a>Criar uma equipe

Consulte a página de [Início Rápido](quick-start.md) para se inscrever ao Content Moderator e criar uma equipe. Observe a **ID de equipe** da página **Credenciais**.


## <a name="define-custom-tags"></a>Definir marcas personalizadas

Consulte o artigo [marcas](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) para adicionar marcas personalizadas. Além das marcas **adulto** e **obsceno** integradas, as novas marcas permitem que a ferramenta de análise exiba nomes descritivos para as marcas.

No nosso caso, definimos essas marcas personalizadas (**celebridade**, **sinalizador**, **EUA**, **brinquedos**, **caneta**):

![Configurar marcas personalizadas](images/tutorial-ecommerce-tags2.PNG)

## <a name="list-your-api-keys-and-endpoints"></a>Lista de suas chaves de API e os pontos de extremidade

1. O tutorial usa três APIs e as chaves correspondentes e os pontos de extremidade de API.
2. Seus pontos de extremidade de API são diferentes com base em suas regiões de assinatura e a ID da equipe de análise do Content Moderator.

> [!NOTE]
> O tutorial foi desenvolvido para usar chaves de assinatura nas regiões visíveis nos seguintes pontos de extremidade. Certifique-se de corresponder suas chaves de API com a região de Uris; caso contrário, as chaves podem não funcionar com os pontos de extremidade a seguir:

         // Your API keys
        public const string ContentModeratorKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string ComputerVisionKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string CustomVisionKey = "XXXXXXXXXXXXXXXXXXXX";

        // Your end points URLs will look different based on your region and Content Moderator Team ID.
        public const string ImageUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/moderate/v1.0/ProcessImage/Evaluate";
        public const string ReviewUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0/teams/YOURTEAMID/reviews";
        public const string ComputerVisionUri = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0";
        public const string CustomVisionUri = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/Prediction/XXXXXXXXXXXXXXXXXXXX/url";

## <a name="scan-for-adult-and-racy-content"></a>Procurar conteúdo adulto e obsceno

1. A função usa uma URL de imagem e uma matriz de pares chave-valor como parâmetros.
2. Ele chama uma API de imagem do Content Moderator para obter as pontuações de adulto e obsceno.
3. Se o resultado for maior que 0,4 (intervalo é de 0 a 1), ele define o valor na matriz **ReviewTags** para **True**.
4. A matriz **ReviewTags** é usada para realçar a marca correspondente na ferramenta de análise.

        public static bool EvaluateAdultRacy(string ImageUrl, ref KeyValuePair[] ReviewTags)
        {
            float AdultScore = 0;
            float RacyScore = 0;

            var File = ImageUrl;
            string Body = $"{{\"DataRepresentation\":\"URL\",\"Value\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(ImageUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // {“answers”:[{“answer”:“Hello”,“questions”:[“Hi”],“score”:100.0}]}
                // Parse the response body. Blocking!
                GetAdultRacyScores(response.Content.ReadAsStringAsync().Result, out AdultScore, out RacyScore);
            }

            ReviewTags[0] = new KeyValuePair();
            ReviewTags[0].Key = "a";
            ReviewTags[0].Value = "false";
            if (AdultScore > 0.4)
            {
                ReviewTags[0].Value = "true";
            }

            ReviewTags[1] = new KeyValuePair();
            ReviewTags[1].Key = "r";
            ReviewTags[1].Value = "false";
            if (RacyScore > 0.3)
            {
                ReviewTags[1].Value = "true";
            }
            return response.IsSuccessStatusCode;
        }

## <a name="scan-for-celebrities"></a>Procurar as celebridades

1. Inscreva-se para uma [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) da [API da Pesquisa Visual Computacional](https://azure.microsoft.com/services/cognitive-services/computer-vision/).
2. Clique no botão **Obter chave de API**.
3. Aceite os termos.
4. Para fazer logon, escolha na lista de contas de Internet disponíveis.
5. Observe as chaves de API exibidas na página de serviço.
    
   ![Chaves da API da Pesquisa Visual Computacional](images/tutorial-computer-vision-keys.PNG)
    
6. Consulte o código de origem do projeto para a função que examina a imagem com a API da Pesquisa Visual Computacional.

         public static bool EvaluateComputerVisionTags(string ImageUrl, string ComputerVisionUri, string ComputerVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage Response = CallAPI(ComputerVisionUri, ComputerVisionKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (Response.IsSuccessStatusCode)
            {
                ReviewTags[2] = new KeyValuePair();
                ReviewTags[2].Key = "cb";
                ReviewTags[2].Value = "false";

                ComputerVisionPrediction CVObject = JsonConvert.DeserializeObject<ComputerVisionPrediction>(Response.Content.ReadAsStringAsync().Result);

                if ((CVObject.categories[0].detail != null) && (CVObject.categories[0].detail.celebrities.Count() > 0))
                {                 
                    ReviewTags[2].Value = "true";
                }
            }

            return Response.IsSuccessStatusCode;
        }

## <a name="classify-into-flags-toys-and-pens"></a>Classificar em sinalizadores, brinquedos e canetas

1. [Fazer logon](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) na [versão prévia da API de Visão Personalizada](https://www.customvision.ai/).
2. Use o [Início Rápido](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) para criar a classificação personalizada para detectar a presença potencial de sinalizadores, brinquedos e canetas.
   ![Imagens de Treinamento de Visão Personalizada](images/tutorial-ecommerce-custom-vision.PNG)
3. [Obter a URL de ponto de extremidade de previsão](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api) para sua classificação personalizada.
4. Consulte o código-fonte para ver a função que chama seu ponto de extremidade de previsão de classificação personalizada para verificar sua imagem.

        public static bool EvaluateCustomVisionTags(string ImageUrl, string CustomVisionUri, string CustomVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(CustomVisionUri, CustomVisionKey, CallType.POST,
                                                   "Prediction-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // Parse the response body. Blocking!
                SaveCustomVisionTags(response.Content.ReadAsStringAsync().Result, ref ReviewTags);
            }
            return response.IsSuccessStatusCode;
        }       
 
## <a name="reviews-for-human-in-the-loop"></a>Revisões humanas no loop

1. Nas seções anteriores, você verificou as imagens de entrada para adulto e obsceno (Content Moderator), as celebridades (Pesquisa Visual Computacional) e sinalizadores (Visão Personalizada).
2. Com base em nossos limites de correspondência para cada exame, verifique os casos matizados disponíveis para revisão humana na ferramenta de análise.
        bool estático público CreateReview (cadeia de caracteres ImageUrl, KeyValuePair [] metadados) {

            ReviewCreationRequest Review = new ReviewCreationRequest();
            Review.Item[0] = new ReviewItem();
            Review.Item[0].Content = ImageUrl;
            Review.Item[0].Metadata = new KeyValuePair[MAXTAGSCOUNT];
            Metadata.CopyTo(Review.Item[0].Metadata, 0);

            //SortReviewItems(ref Review);

            string Body = JsonConvert.SerializeObject(Review.Item);

            HttpResponseMessage response = CallAPI(ReviewUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            return response.IsSuccessStatusCode;
        }

## <a name="submit-batch-of-images"></a>Enviar lote de imagens

1. Este tutorial pressupõe um diretório "C:Test" com um arquivo de texto que tem uma lista de Urls de imagem.
2. O código a seguir verifica a existência do arquivo e lê todas as Urls na memória.
            // Check for a test directory for a text file with the list of Image URLs to scan var topdir = @"C:\test\"; var Urlsfile = topdir + "Urls.txt";

            if (!Directory.Exists(topdir))
                return;

            if (!File.Exists(Urlsfile))
            {
                return;
            }

            // Read all image URLs in the file
            var Urls = File.ReadLines(Urlsfile);

## <a name="initiate-all-scans"></a>Iniciar todas as verificações

1. Essa função de nível superior percorre todas as URLs de imagem no arquivo de texto que mencionamos anteriormente.
2. Ela verifica-os com cada API e se a pontuação de confiabilidade de correspondência cair dentro de nossos critérios, cria uma revisão para moderadores humanos.
             // for each image URL in the file... foreach (var Url in Urls) { // Initiatize a new review tags array ReviewTags = new KeyValuePair[MAXTAGSCOUNT];

                // Evaluate for potential adult and racy content with Content Moderator API
                EvaluateAdultRacy(Url, ref ReviewTags);

                // Evaluate for potential presence of celebrity (ies) in images with Computer Vision API
                EvaluateComputerVisionTags(Url, ComputerVisionUri, ComputerVisionKey, ref ReviewTags);

                // Evaluate for potential presence of custom categories other than Marijuana
                EvaluateCustomVisionTags(Url, CustomVisionUri, CustomVisionKey, ref ReviewTags);

                // Create review in the Content Moderator review tool
                CreateReview(Url, ReviewTags);
            }

## <a name="license"></a>Licença

Todos os SDKs dos Serviços Cognitivos da Microsoft e exemplos são licenciados com a licença do MIT. Para obter mais detalhes, consulte [LICENÇA](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Código de Conduta do Desenvolvedor

Os desenvolvedores que usam os Serviços Cognitivos, inclusive essa biblioteca de cliente e exemplo, devem seguir o "Código de Conduta do Desenvolvedor para os Serviços Cognitivos da Microsoft", encontrado em http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Próximas etapas

Criar e estender o tutorial usando os [arquivos de origem do projeto](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) no Github.
