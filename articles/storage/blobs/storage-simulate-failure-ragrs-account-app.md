---
title: Simular uma falha ao acessar o armazenamento redundante com acesso de leitura no Azure | Microsoft Docs
description: "Simular um erro ao acessar o armazenamento com redundância geográfica com acesso de leitura"
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: tutorial
ms.date: 12/23/2017
ms.author: v-ruogun
ms.openlocfilehash: 9ebf773cf39d832416dce820e67201c21a679296
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Simular uma falha ao acessar o armazenamento com acesso de leitura

Este tutorial é a parte dois de uma série.  Neste tutorial, é possível usar o [Fiddler](#simulate-a-failure-with-fiddler) ou o [Roteamento Estático](#simulate-a-failure-with-an-invalid-static-route) para simular uma falha de solicitações ao ponto de extremidade primário da sua conta de armazenamento [read-access geo-redundant](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) e fazer com que o aplicativo leia a partir do ponto de extremidade secundário.

![Aplicativo do cenário](media/storage-simulate-failure-ragrs-account-app/scenario.png)

Para concluir este tutorial, você deve concluir o tutorial anterior de armazenamento: [Tornar os dados do aplicativo altamente disponíveis com o armazenamento do Azure][previous-tutorial].

Na segunda parte da série, você aprenderá como:

> [!div class="checklist"]
> * Executar e pausar o aplicativo
> * Simular uma falha com o [Fiddler](#simulate-a-failure-with-fiddler) ou [uma rota estática inválida](#simulate-a-failure-with-an-invalid-static-route) 
> * Simular a restauração do ponto de extremidade primário


## <a name="prerequisites"></a>pré-requisitos

Para simular uma falha usando o Fiddler: 

* Baixe e [instale o Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="simulate-a-failure-with-fiddler"></a>Simular uma falha com o Fiddler

Para simular falha com o Fiddler, insira uma resposta de falha para solicitações do ponto de extremidade primário da sua conta de armazenamento RA-GRS.

Siga as etapas a seguir para simular uma falha e a restauração de ponto de extremidade primário com o Fiddler.

### <a name="launch-fiddler"></a>Iniciar o Fiddler

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

### <a name="start-and-pause-the-application"></a>Inicie e pause o aplicativo

Execute o aplicativo em seu IDE ou editor de texto. Depois que o aplicativo começa a ler o ponto de extremidade primário, pressione **qualquer tecla** na janela do console para pausar o aplicativo.

### <a name="simulate-failure"></a>Simular falhas

Com o aplicativo pausado, agora é possível remover a marca de comentário da regra personalizada salva no Fiddler em uma etapa anterior. O exemplo de código procura solicitações para a conta de armazenamento RA-GRS e, se o caminho contiver o nome da imagem, `HelloWorld`, ele retorna um código de resposta `503 - Service Unavailable`.

Navegue até o Fiddler e selecione **regras** -> **Personalizar regras...** .  Remova a marca de comentário das linhas a seguir, substitua `STORAGEACCOUNTNAME` pelo nome da sua conta de armazenamento. Selecione **Arquivo** -> **Salvar** para salvar as alterações. 

> [!NOTE]
> Caso esteja executando o aplicativo de exemplo no Linux, você precisará reiniciar o Fiddler sempre que editar o arquivo **CustomRule.js** para que o Fiddler instale a lógica personalizada. 
> 
> 


```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

Para continuar a usar o aplicativo, pressione **qualquer tecla**.

Depois que o aplicativo começar a ser executado novamente, as solicitações para o ponto de extremidade primário começam a falhar. O aplicativo tenta se reconectar ao ponto de extremidade primário 5 vezes. Após o limite de falha de cinco tentativas, ele solicita a imagem do ponto de extremidade secundário de somente leitura. Depois que o aplicativo recupera com sucesso a imagem do ponto de extremidade secundário 20 vezes, o aplicativo tenta se conectar ao ponto de extremidade primário. Se o ponto de extremidade primário ainda estiver inacessível, o aplicativo retoma a leitura a partir do ponto de extremidade secundário. Esse é o padrão [Disjuntor](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) descrito no tutorial anterior.

![Cole a regra personalizada](media/storage-simulate-failure-ragrs-account-app/figure3.png)

### <a name="simulate-primary-endpoint-restoration"></a>Simular a restauração do ponto de extremidade primário

Com a regra personalizada do Fiddler definida na etapa anterior, as solicitações para o ponto de extremidade primário falham. Para simular o funcionamento do ponto de extremidade primário novamente, você deve remover a lógica para injetar o erro `503`.

Para pausar o aplicativo, pressione **qualquer tecla**.

Navegue até o Fiddler e selecione **Regras** e **Personalizar regras...**.  Comente ou remova a lógica personalizada na função `OnBeforeResponse`, deixando a função padrão. Selecione **Arquivo** e **Salvar** para salvar as alterações.

![Remova a regra personalizada](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Após concluir essa etapa, pressione **qualquer tecla** para continuar a usar o aplicativo. O aplicativo continua a leitura a partir do ponto de extremidade primário até atingir 999 leituras.

![Retomar o aplicativo](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simular uma falha com uma rota estática inválida 
É possível criar uma rota estática inválida para todas as solicitações do ponto de extremidade primário da sua conta de armazenamento [read-access geo-redundant](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS). Neste tutorial, o host local é usado como o gateway para rotear solicitações à conta de armazenamento. Usando o host local como o gateway faz com que todas as solicitações ao seu ponto de extremidade primário da conta de armazenamento executem um loop dentro do host, o que leva à falha como consequência. Siga as etapas a seguir para simular uma falha e a restauração de ponto de extremidade primário com uma rota estática inválida. 

### <a name="start-and-pause-the-application"></a>Inicie e pause o aplicativo

Execute o aplicativo em seu IDE ou editor de texto. Depois que o aplicativo começa a ler o ponto de extremidade primário, pressione **qualquer tecla** na janela do console para pausar o aplicativo. 

### <a name="simulate-failure"></a>Simular falhas

Com o aplicativo em pausa, inicie o prompt de comando no Windows como administrador ou execute o terminal como raiz no Linux. Para obter informações sobre o domínio do ponto de extremidade primário da conta de armazenamento, insira o seguinte comando em um prompt de comando ou terminal.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 Substitua `STORAGEACCOUNTNAME` com o nome da sua conta de armazenamento. Copie o endereço IP da sua conta de armazenamento para um editor de texto para uso posterior. Para obter o endereço IP do seu host local, digite `ipconfig` no prompt de comando do Windows ou `ifconfig` no terminal do Linux. 

Para adicionar uma rota estática a um host de destino, digite o comando a seguir em um prompt de comando do Windows ou terminal do Linux. 


# <a name="linuxtablinux"></a>[Linux](#tab/linux)

  route add <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

  route add <destination_ip> <gateway_ip>

---
 
Substitua `<destination_ip>` pelo seu endereço IP da conta de armazenamento e `<gateway_ip>` pelo seu endereço IP do host local. Para continuar a usar o aplicativo, pressione **qualquer tecla**.

Depois que o aplicativo começar a ser executado novamente, as solicitações para o ponto de extremidade primário começam a falhar. O aplicativo tenta se reconectar ao ponto de extremidade primário 5 vezes. Após o limite de falha de cinco tentativas, ele solicita a imagem do ponto de extremidade secundário de somente leitura. Depois que o aplicativo recupera com sucesso a imagem do ponto de extremidade secundário 20 vezes, o aplicativo tenta se conectar ao ponto de extremidade primário. Se o ponto de extremidade primário ainda estiver inacessível, o aplicativo retoma a leitura a partir do ponto de extremidade secundário. Esse é o padrão [Disjuntor](/azure/architecture/patterns/circuit-breaker.md) descrito no tutorial anterior.

### <a name="simulate-primary-endpoint-restoration"></a>Simular a restauração do ponto de extremidade primário

Para simular a função do ponto de extremidade primário novamente, exclua a rota estática do ponto de extremidade primário da tabela de roteamento. Isso permite que todas as solicitações ao ponto de extremidade primário sejam roteadas por meio do gateway padrão. 

Para excluir uma rota estática de um host de destino, a conta de armazenamento, digite o comando a seguir em um prompt de comando do Windows ou terminal do Linux. 
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)

route del <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

route delete <destination_ip> <gateway_ip>

---

Pressione **qualquer tecla** para continuar a usar o aplicativo. O aplicativo continua a leitura a partir do ponto de extremidade primário até atingir 999 leituras.

![Retomar o aplicativo](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="next-steps"></a>Próximas etapas

Na parte dois da série, você aprendeu a simular uma falha para testar o armazenamento com redundância geográfica com acesso de leitura, por exemplo, como:

> [!div class="checklist"]
> * Executar e pausar o aplicativo
> * Simular uma falha com o [Fiddler](#simulate-a-failure-with-fiddler) ou [uma rota estática inválida](#simulate-a-failure-with-an-invalid-static-route) 
> * Simular a restauração do ponto de extremidade primário

Siga este link para ver amostras de armazenamento pré-compiladas.

> [!div class="nextstepaction"]
> [Amostras de script do Armazenamento do Azure](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md