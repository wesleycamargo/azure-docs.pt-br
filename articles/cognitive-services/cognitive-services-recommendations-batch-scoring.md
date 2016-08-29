
<properties
	pageTitle="Obtendo recomendações em lotes: API de recomendações do Aprendizado de Máquina | Microsoft Azure"
	description="Recomendações do aprendizado de máquina do Azure – obtendo recomendações em lotes"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/24/2016"
	ms.author="luisca"/>

# Obter recomendações em lotes

>[AZURE.NOTE] Obter recomendações em lotes é mais complicado do que obter recomendações uma a uma. Verifique as APIs para obter informações sobre como obter as recomendações para uma única solicitação:

> [Recomendações Item a Item](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4)<br> [Recomendações de Item e Usuário](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)
>
> A pontuação do lote funciona apenas para as compilações criadas após 21 de julho de 2016.


Há situações nas quais você precisa obter recomendações para mais de um item por vez. Por exemplo, você pode estar interessado em criar um cache de recomendações ou, até mesmo, analisar os tipos de recomendações que está obtendo.

As operações de pontuação do lote, como as chamamos, são operações assíncronas. Você precisa enviar a solicitação, aguardar que a operação termine e coletar os resultados.

Para ser mais preciso, estas são as etapas a serem seguidas:

1.	Crie um contêiner de Armazenamento do Azure, caso ainda não tenha um.
2.	Carregue um arquivo de entrada que descreva cada uma das solicitações de recomendação no armazenamento de Blobs do Azure.
3.	Inicie o trabalho em lote de pontuação.
4.	Aguarde a conclusão da operação assíncrona.
5.	Quando a operação for concluída, reúna os resultados do armazenamento de Blobs.

Vamos examinar cada uma dessas etapas.

## Crie um contêiner de Armazenamento, caso ainda não tenha um

Vá para o [portal do Azure](https://portal.azure.com) e crie uma nova conta de armazenamento se você ainda não tiver uma. Para fazer isso, navegue até **Novo** > **Dados** + **Armazenamento** > **Conta de Armazenamento**.

Com uma conta de armazenamento, você precisa criar os contêineres de blobs onde armazenará a entrada e a saída da execução do lote.

Carregue um arquivo de entrada que descreva cada uma das solicitações de recomendação no armazenamento de Blobs – aqui, vamos chamar o arquivo de input.json. Com um contêiner, você precisa carregar um arquivo que descreva cada uma das solicitações necessárias para executar a partir do serviço de recomendações.

Um lote pode executar apenas um tipo de solicitação a partir uma compilação específica. Explicaremos como definir essas informações na próxima seção. Por enquanto, iremos supor que executaremos as recomendações do item fora de uma compilação específica. O arquivo de entrada contém as informações de entrada (nesse caso, os itens de propagação) de cada uma das solicitações.

Este é um exemplo da aparência do arquivo input.json:

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }

Como você pode ver, é um arquivo JSON, em que cada uma das solicitações tem as informações necessárias para enviar uma solicitação de recomendações. Crie um arquivo JSON semelhante para as solicitações que você precisa preencher e copie-o para o contêiner que você acabou de criar no armazenamento de Blobs.

## Iniciar o trabalho em lote de pontuação

A próxima etapa é enviar um novo trabalho em lote. Para obter mais informações, consulte a [referência da API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/).

O corpo da solicitação da API precisa definir os locais onde os arquivos de entrada, saída e erro precisam ser armazenados. Também precisa definir as credenciais necessárias para acessar esses locais. Além disso, é preciso especificar alguns parâmetros que se aplicam ao lote inteiro (o tipo de recomendação a solicitar, modelo/compilação a usar, número de resultados por chamada etc.)

Este é um exemplo de como o corpo da solicitação deve se parecer:

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

Há dois aspectos importantes a observar:

-	Atualmente, **authenticationType** sempre deve ser definido para **PublicOrSas**.

-	Você precisa obter um token SAS (Assinatura de Acesso Compartilhado) para permitir que a API de Recomendações leia e grave de/em sua conta de armazenamento de Blobs. Mais informações sobre como gerar os tokens SAS podem ser encontradas na [página da API de Recomendações](../storage/storage-dotnet-shared-access-signature-part-1.md).

-	No momento, o único **apiName** suportado é **ItemRecommend**, que é usado para as recomendações Item a Item. Atualmente, o lote não oferece suporte para as recomendações de Item e Usuário.

## Aguardar a conclusão da operação assíncrona

Quando você inicia a operação em lote, a resposta retorna o cabeçalho Operation-Location que fornece as informações necessárias para rastrear a operação. Você controla a operação usando a [API para Recuperar Status da Operação](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da), exatamente como faria para rastrear a operação de uma operação de compilação.

## Obter os resultados

Depois da operação ser concluída, e supondo que não houve erros, você pode coletar os resultados do armazenamento de Blobs de saída.

O exemplo a seguir mostra como fica a saída. Neste exemplo, podemos mostrar os resultados de um lote com apenas duas solicitações (para abreviar).

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## Saiba mais sobre as limitações

-	Apenas um trabalho em lote pode ser chamado por assinatura e por vez.
-	Um arquivo de entrada do trabalho em lote não pode ter mais de 2 MB.

<!---HONumber=AcomDC_0817_2016-->