---
title: Transmitir arquivos de vídeo com os Serviços de Mídia do Azure – Node.js | Microsoft Docs
description: Execute as etapas neste início rápido para criar uma nova conta dos Serviços de Mídia do Azure, codificar um arquivo e transferi-lo para o Player de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: serviços de mídia do azure, stream
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/26/2019
ms.author: juliako
ms.openlocfilehash: 22b7f2380b509daa4cb9931d6fc57c1297628e3d
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522737"
---
# <a name="quickstart-stream-video-files---nodejs"></a>Início Rápido: Transmitir arquivos de vídeo – Node.js

Este início rápido mostra como é fácil codificar e iniciar a transmissão de vídeos por streaming em vários navegadores e dispositivos usando os Serviços de Mídia do Azure. Um conteúdo de entrada pode ser especificado usando URLs HTTPS, URLs SAS ou caminhos para arquivos localizados no Armazenamento de Blobs do Azure.
O exemplo neste tópico codifica o conteúdo disponibilizado por meio de uma URL HTTPS. Observe que, no momento, o AMS v3 não dá suporte à codificação de transferência em partes sobre URLs HTTPS.

Ao final do início rápido, você poderá transmitir um vídeo por streaming.  

![Reproduzir o vídeo](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Instale o [Node.js](https://nodejs.org/en/download/)
- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).<br/>Lembre-se dos valores que você usou para o nome do grupo de recursos e o nome da conta de Serviços de Mídia.
- Siga as etapas em [Acessar API de Serviços de Mídia com a CLI do Azure](access-api-cli-how-to.md) e salve as credenciais. Você precisará usá-las para acessar a API.

## <a name="download-and-configure-the-sample"></a>Baixar e configurar o exemplo

Clone um repositório do GitHub que contém o exemplo de streaming de Node.js em seu computador usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

O exemplo está localizado na pasta [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample).

Abra [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) em você baixou o projeto. Substitua os `endpoint config` valores pelas credenciais que você obteve de [acesso às APIs](access-api-cli-how-to.md).

O exemplo executa as ações a seguir:

1. Cria uma **Transformação** (primeiro, verifica se a transformação especificada existe). 
2. Cria um **Ativo** de saída que é usado como a saída do **Trabalho** de codificação.
3. Cria a entrada do **Trabalho** com base em uma URL HTTPS.
4. Envia o **Trabalho** de codificação usando a entrada e a saída criadas anteriormente.
5. Verifica o status do trabalho.
6. Cria um **Localizador de Streaming**.
7. Compila as URLs de streaming.

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

1. O aplicativo baixa arquivos codificados. Crie uma pasta onde desejar para os arquivos de saída e atualize o valor da variável **outputFolder** no arquivo [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39).
1. Abra **prompt de comando**, navegue até o diretório do exemplo e execute os seguintes comandos.

    ```
    npm install 
    node index.js
    ```

Após a conclusão da execução, você deve ver saídas semelhantes:

![Executar](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Testar com o Player de Mídia do Azure

Para testar o streaming, este artigo usa o Player de Mídia do Azure. 

> [!NOTE]
> Se um player estiver hospedado em um site https, atualize a URL para "https".

1. Abra um navegador da Web e navegue até [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Na caixa **URL:**, cole um dos valores de URL de streaming que você obteve ao executar o aplicativo. 
 
     Você poderá colar a URL no formato HLS, Dash ou Smooth e o Player de Mídia do Azure alternará para um protocolo de streaming apropriado para reprodução em seu dispositivo automaticamente.
3. Pressione **Atualizar Player**.

O Player de Mídia do Azure pode ser usado para testes, mas não deve ser usado em um ambiente de produção. 

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo as contas dos Serviços de Mídia e de armazenamento que você criou neste Início Rápido, exclua o grupo de recursos.

Execute este comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Consulte também

[Códigos de erro de trabalho](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conceitos dos Serviços de Mídia](concepts-overview.md)
