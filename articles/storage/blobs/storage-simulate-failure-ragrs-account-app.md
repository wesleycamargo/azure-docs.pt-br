---
title: 'Tutorial: Simular uma falha ao acessar o armazenamento redundante com acesso de leitura no Azure | Microsoft Docs'
description: Simular um erro ao acessar o armazenamento com redundância geográfica com acesso de leitura
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.openlocfilehash: 0cbb4d2bc6449dc1cf12a374085b429743224995
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650233"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Tutorial: Simular uma falha ao acessar o armazenamento com acesso de leitura

Este tutorial é a parte dois de uma série. Nele, você aprenderá sobre os benefícios de uma conta de armazenamento [RA-GRS](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (com redundância geográfica com acesso de leitura) simulando uma falha.

Para simular uma falha, use o [Fiddler](#simulate-a-failure-with-fiddler) ou o [Roteamento Estático](#simulate-a-failure-with-an-invalid-static-route). Qualquer um dos métodos permitirá simular uma falha de solicitações para o ponto de extremidade primário de sua conta de armazenamento [RA-GRS](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (com redundância geográfica com acesso de leitura), fazendo com que o aplicativo faça a leitura do ponto de extremidade secundário.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Na segunda parte da série, você aprenderá como:

> [!div class="checklist"]
> * Executar e pausar o aplicativo
> * Simular uma falha com o [Fiddler](#simulate-a-failure-with-fiddler) ou [uma rota estática inválida](#simulate-a-failure-with-an-invalid-static-route) 
> * Simular a restauração do ponto de extremidade primário

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, conclua o tutorial anterior: [Tornar os dados de seu aplicativo altamente disponíveis com o Armazenamento do Azure][previous-tutorial].

Para simular uma falha usando o Fiddler: 

* Baixe e [instale o Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-fiddler"></a>Simular uma falha com o Fiddler

Para simular uma falha com o Fiddler, injete uma resposta com falha para solicitações para o ponto de extremidade primário de sua conta de armazenamento RA-GRS.

As seções a seguir explicam como simular uma falha e a restauração do ponto de extremidade primário com o Fiddler.

### <a name="launch-fiddler"></a>Iniciar o Fiddler

Abra o Fiddler, selecione **Regras** e **Personalizar regras**.

![Personalizar as regras do Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

O ScriptEditor do Fiddler é iniciado e exibe o arquivo **SampleRules.js**. Esse arquivo é usado para personalizar o Fiddler.

Cole o seguinte exemplo de código noa função `OnBeforeResponse`. O novo código é comentado para garantir que a lógica que ele cria não é implementada imediatamente.

Após a conclusão, selecione **Arquivo** e **Salvar** para salvar as alterações.

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

### <a name="interrupting-the-application"></a>Interrompendo o aplicativo

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python e Java v7](#tab/dotnet-python-java-v7)

Execute o aplicativo no IDE ou no shell.

Depois que o aplicativo começa a ler o ponto de extremidade primário, pressione **qualquer tecla** na janela do console para pausar o aplicativo.

![Aplicativo do cenário](media/storage-simulate-failure-ragrs-account-app/scenario.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Execute o aplicativo no IDE ou no shell.

Como você controla a amostra, você não precisa interrompê-la para simular uma falha. Apenas verifique se o arquivo foi carregado em sua conta de armazenamento executando a amostra e inserindo **P**.

![Aplicativo do cenário](media/storage-simulate-failure-ragrs-account-app/Java-put-list-output.png)

---

### <a name="simulate-failure"></a>Simular falhas

Enquanto o aplicativo está em pausa, remova a marca de comentário da regra personalizada que salvamos no Fiddler.

O exemplo de código procura solicitações para a conta de armazenamento RA-GRS e, se o caminho contiver o nome do arquivo `HelloWorld`, retornará um código de resposta igual a `503 - Service Unavailable`.

Navegue até o Fiddler e selecione **regras** -> **Personalizar regras...** .

Remova a marca de comentário das linhas a seguir e substitua `STORAGEACCOUNTNAME` pelo nome de sua conta de armazenamento. Selecione **Arquivo** -> **Salvar** para salvar as alterações. 

> [!NOTE]
> Caso esteja executando o aplicativo de exemplo no Linux, você precisará reiniciar o Fiddler sempre que editar o arquivo **CustomRule.js** para que o Fiddler instale a lógica personalizada.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python e Java v7](#tab/dotnet-python-java-v7)

Para continuar a usar o aplicativo, pressione **qualquer tecla**.

Depois que o aplicativo começar a ser executado novamente, as solicitações para o ponto de extremidade primário começam a falhar. O aplicativo tenta se reconectar ao ponto de extremidade primário 5 vezes. Após o limite de falha de cinco tentativas, ele solicita a imagem do ponto de extremidade secundário de somente leitura. Quando o aplicativo recuperar com êxito a imagem do ponto de extremidade secundário 20 vezes, ele tentará se conectar ao ponto de extremidade primário. Se o ponto de extremidade primário ainda estiver inacessível, o aplicativo retoma a leitura a partir do ponto de extremidade secundário.

Esse é o padrão [Disjuntor](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) descrito no tutorial anterior.

![Cole a regra personalizada](media/storage-simulate-failure-ragrs-account-app/figure3.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Agora que você introduziu a falha, insira **G** para testá-la.

Ela informará que está usando o pipeline secundário em vez do pipeline primário.

---

### <a name="simulate-primary-endpoint-restoration"></a>Simular a restauração do ponto de extremidade primário

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python e Java v7](#tab/dotnet-python-java-v7)

Com a regra personalizada do Fiddler definida na etapa anterior, as solicitações para o ponto de extremidade primário falham.

Para simular o funcionamento do ponto de extremidade primário novamente, você deve remover a lógica para injetar o erro `503`.

Para pausar o aplicativo, pressione **qualquer tecla**.

Navegue até o Fiddler e selecione **Regras** e **Personalizar regras...**. 

Comente ou remova a lógica personalizada na função `OnBeforeResponse`, deixando a função padrão.

Selecione **Arquivo** e **Salvar** para salvar as alterações.

![Remova a regra personalizada](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Após concluir essa etapa, pressione **qualquer tecla** para continuar a usar o aplicativo. O aplicativo continua a leitura a partir do ponto de extremidade primário até atingir 999 leituras.

![Retomar o aplicativo](media/storage-simulate-failure-ragrs-account-app/figure4.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Com a regra personalizada do Fiddler definida na etapa anterior, as solicitações para o ponto de extremidade primário falham.

Para simular o funcionamento do ponto de extremidade primário novamente, você deve remover a lógica para injetar o erro `503`.

Navegue até o Fiddler e selecione **Regras** e **Personalizar regras...**.  Comente ou remova a lógica personalizada na função `OnBeforeResponse`, deixando a função padrão.

Selecione **Arquivo** e **Salvar** para salvar as alterações.

Após a conclusão, insira **G** para testar o download. O aplicativo relatará que agora está usando o pipeline primário novamente.

---

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simular uma falha com uma rota estática inválida

É possível criar uma rota estática inválida para todas as solicitações do ponto de extremidade primário da sua conta de armazenamento [read-access geo-redundant](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS). Neste tutorial, o host local é usado como o gateway para rotear solicitações à conta de armazenamento. Usando o host local como o gateway faz com que todas as solicitações ao seu ponto de extremidade primário da conta de armazenamento executem um loop dentro do host, o que leva à falha como consequência. Siga as etapas a seguir para simular uma falha e a restauração de ponto de extremidade primário com uma rota estática inválida. 

### <a name="start-and-pause-the-application"></a>Inicie e pause o aplicativo

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python e Java v7](#tab/dotnet-python-java-v7)

Execute o aplicativo no IDE ou no shell. Depois que o aplicativo começa a ler o ponto de extremidade primário, pressione **qualquer tecla** na janela do console para pausar o aplicativo.

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Como você controla a amostra, você não precisa interrompê-la para testar a falha.

Verifique se o arquivo foi carregado em sua conta de armazenamento executando a amostra e inserindo **P**.

---

### <a name="simulate-failure"></a>Simular falhas

Com o aplicativo em pausa, inicie o prompt de comando no Windows como administrador ou execute o terminal como raiz no Linux.

Obtenha informações sobre o domínio do ponto de extremidade primário da conta de armazenamento inserindo o comando a seguir em um prompt de comando ou um terminal.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 Substitua `STORAGEACCOUNTNAME` com o nome da sua conta de armazenamento. Copie o endereço IP da sua conta de armazenamento para um editor de texto para uso posterior.

Para obter o endereço IP do seu host local, digite `ipconfig` no prompt de comando do Windows ou `ifconfig` no terminal do Linux. 

Para adicionar uma rota estática a um host de destino, digite o comando a seguir em um prompt de comando do Windows ou terminal do Linux. 

#### <a name="linux"></a>Linux

`route add <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a> Windows

`route add <destination_ip> <gateway_ip>`

Substitua `<destination_ip>` pelo seu endereço IP da conta de armazenamento e `<gateway_ip>` pelo seu endereço IP do host local.

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python e Java v7](#tab/dotnet-python-java-v7)

Para continuar a usar o aplicativo, pressione **qualquer tecla**.

Depois que o aplicativo começar a ser executado novamente, as solicitações para o ponto de extremidade primário começam a falhar. O aplicativo tenta se reconectar ao ponto de extremidade primário cinco vezes. Após o limite de falha de cinco tentativas, ele solicita a imagem do ponto de extremidade secundário de somente leitura. Depois que o aplicativo recupera com sucesso a imagem do ponto de extremidade secundário 20 vezes, o aplicativo tenta se conectar ao ponto de extremidade primário. Se o ponto de extremidade primário ainda estiver inacessível, o aplicativo retoma a leitura a partir do ponto de extremidade secundário. Esse é o padrão [Disjuntor](/azure/architecture/patterns/circuit-breaker) descrito no tutorial anterior.

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Agora que você introduziu a falha, insira **G** para testá-la. Ela informará que está usando o pipeline secundário em vez do pipeline primário.

---

### <a name="simulate-primary-endpoint-restoration"></a>Simular a restauração do ponto de extremidade primário

Para simular a função do ponto de extremidade primário novamente, exclua a rota estática do ponto de extremidade primário da tabela de roteamento. Isso permite que todas as solicitações ao ponto de extremidade primário sejam roteadas por meio do gateway padrão.

Para excluir uma rota estática de um host de destino, a conta de armazenamento, digite o comando a seguir em um prompt de comando do Windows ou terminal do Linux.

#### <a name="linux"></a>Linux

`route del <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a> Windows

`route delete <destination_ip>`

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python e Java v7](#tab/dotnet-python-java-v7)

Pressione **qualquer tecla** para continuar a usar o aplicativo. O aplicativo continua a leitura a partir do ponto de extremidade primário até atingir 999 leituras.

![Retomar o aplicativo](media/storage-simulate-failure-ragrs-account-app/figure4.png)


# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Insira **G** para testar o download. O aplicativo relatará que agora está usando o pipeline primário novamente.

![Retomar o aplicativo](media/storage-simulate-failure-ragrs-account-app/java-get-pipeline-example-v10.png)

---

## <a name="next-steps"></a>Próximas etapas

Na segunda parte da série, você aprendeu a simular uma falha para testar o armazenamento com redundância geográfica com acesso de leitura.

Para saber mais sobre como funciona o armazenamento RA-GRS, bem como os riscos associados, leia o seguinte artigo:

> [!div class="nextstepaction"]
> [Criando aplicativos de alta disponibilidade com o RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
