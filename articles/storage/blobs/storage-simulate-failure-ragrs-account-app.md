---
title: Simular uma falha ao acessar o armazenamento redundante com acesso de leitura no Azure | Microsoft Docs
description: "Simular um erro ao acessar o armazenamento com redundância geográfica com acesso de leitura"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/05/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: f709e216f9308c4405776b25ca44b0aaddd3d3f8
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2017
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Simular uma falha ao acessar o armazenamento com acesso de leitura

Este tutorial é a parte dois de uma série. Neste tutorial, você insere uma resposta com falha com o Fiddler para solicitações para uma conta de armazenamento [com redundância geográfica com acesso de leitura](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) para simular uma falha e fazer com que o aplicativo leia a partir do ponto de extremidade secundário.

![Aplicativo do cenário](media/storage-simulate-failure-ragrs-account-app/scenario.png)

Na segunda parte da série, você aprenderá como:

> [!div class="checklist"]
> * Executar e pausar o aplicativo
> * Simular uma falha
> * Simular a restauração do ponto de extremidade primário

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Baixe e instale o [Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Para concluir este tutorial, você deve concluir o tutorial anterior de armazenamento: [Tornar os dados do aplicativo altamente disponíveis com o armazenamento do Azure][previous-tutorial].

## <a name="launch-fiddler"></a>Iniciar o Fiddler

Abra o Fiddler, selecione **Regras** e **Personalizar regras**.

![Personalizar as regras do Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

O Fiddler ScriptEditor inicia mostrando o arquivo **SampleRules.js**. Esse arquivo é usado para personalizar o Fiddler. Cole o seguinte exemplo de código noa função `OnBeforeResponse`. O novo código é comentado para garantir que a lógica que ele cria não é implementada imediatamente. Após a conclusão selecione **Arquivo** e **Salvar** para salvar suas alterações.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![Cole a regra personalizada](media/storage-simulate-failure-ragrs-account-app/figure2.png)

## <a name="start-and-pause-the-application"></a>Inicie e pause o aplicativo

No Visual Studio, pressione **F5** ou selecione **Iniciar** para iniciar a depuração do aplicativo. Depois que o aplicativo começa a ler o ponto de extremidade primário, pressione **qualquer tecla** na janela do console para pausar o aplicativo.

## <a name="simulate-failure"></a>Simular falhas

Com o aplicativo pausado você agora pode remover a regra personalizada salva em Fiddler em uma etapa anterior. Esse exemplo de código procura solicitações para a conta de armazenamento de RA-GRS e se o caminho contém o nome da imagem, `HelloWorld`, ele retorna um código de resposta `503 - Service Unavailable`.

Navegue até o Fiddler e selecione **regras** -> **Personalizar regras...** .  Remova a marca de comentário das linhas a seguir, substitua `STORAGEACCOUNTNAME` pelo nome da sua conta de armazenamento. Selecione **Arquivo** -> **Salvar** para salvar as alterações.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

Para continuar a usar o aplicativo, pressione **qualquer tecla** .

Depois que o aplicativo começar a ser executado novamente, as solicitações para o ponto de extremidade primário começam a falhar. O aplicativo tenta se reconectar ao ponto de extremidade primário 5 vezes. Após o limite de falha de cinco tentativas, ele solicita a imagem do ponto de extremidade secundário de somente leitura. Depois que o aplicativo recupera com sucesso a imagem do ponto de extremidade secundário 20 vezes, o aplicativo tenta se conectar ao ponto de extremidade primário. Se o ponto de extremidade primário ainda estiver inacessível, o aplicativo retoma a leitura a partir do ponto de extremidade secundário. Esse é o padrão [Disjuntor](/azure/architecture/patterns/circuit-breaker.md) descrito no tutorial anterior.

![Cole a regra personalizada](media/storage-simulate-failure-ragrs-account-app/figure3.png)

## <a name="simulate-primary-endpoint-restoration"></a>Simular a restauração do ponto de extremidade primário

Com a regra personalizada do Fiddler definida na etapa anterior, as solicitações para o ponto de extremidade primário falham. Para simular o funcionamento do ponto de extremidade primário novamente, você deve remover a lógica para injetar o erro `503`.

Para pausar o aplicativo, pressione **qualquer tecla**.

### <a name="remove-the-custom-rule"></a>Remover a regra personalizada

Navegue até o Fiddler e selecione **Regras** e **Personalizar regras...**.  Comente ou remova a lógica personalizada na função `OnBeforeResponse`, deixando a função padrão. Selecione **Arquivo** e **Salvar** para salvar as alterações.

![Remova a regra personalizada](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Após concluir essa etapa, pressione **qualquer tecla** para continuar a usar o aplicativo. O aplicativo continua a leitura a partir do ponto de extremidade primário até atingir 999 leituras.

![Retomar o aplicativo](media/storage-simulate-failure-ragrs-account-app/figure4.png)

## <a name="next-steps"></a>Próximas etapas

Na parte dois da série, você aprendeu como simular uma falha de teste de armazenamento com redundância geográfica com acesso de leitura, tais como:

> [!div class="checklist"]
> * Executar e pausar o aplicativo
> * Simular uma falha
> * Simular a restauração do ponto de extremidade primário

Siga este link para ver amostras de armazenamento pré-compiladas.

> [!div class="nextstepaction"]
> [Amostras de script do Armazenamento do Azure](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md