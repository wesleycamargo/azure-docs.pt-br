---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-accelerators
author: v-krghan
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/25/2019
ms.author: v-krghan
ms.custom: include file
ms.openlocfilehash: 81590a4d686d85482bee38c4391b8ac24b25658e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58114073"
---
## <a name="download-the-source-code"></a>Fazer o download do código-fonte

Os repositórios de código-fonte de Monitoramento Remoto incluem o código-fonte e os arquivos de configuração do Docker necessários para executar as imagens do Docker dos microsserviços.

Para clonar e criar uma versão local do repositório, use o ambiente da linha de comando para navegar até uma pasta adequada no computador local. Em seguida, execute um dos seguintes conjuntos de comandos para clonar o repositório Java:

Para baixar a versão mais recente das implementações do microsserviço Java, execute:


```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

> [!NOTE]
> Esses comandos baixam o código-fonte para todos os microsserviços, bem como os scripts usados para executar os microsserviços localmente. Embora o código-fonte não seja necessário para executar os microsserviços no Docker, ele é útil se você planeja modificar o acelerador de solução e testar suas alterações localmente no futuro.

## <a name="deploy-the-azure-services"></a>Implantar os serviços do Azure

Embora este artigo mostre como executar os microsserviços localmente, eles dependem de serviços do Azure em execução na nuvem. Use o seguinte script a seguir para implantar os serviços do Azure. Os exemplos de script a seguir pressupõem que você esteja usando o repositório Java em um computador Windows. Se estiver trabalhando em outro ambiente, ajuste os caminhos, extensões de arquivo e separadores de caminho adequadamente.

### <a name="create-new-azure-resources"></a>Criar novos recursos do Azure

Se você ainda não criou os recursos do Azure necessários, siga estas etapas:

1. No ambiente da linha de comando, navegue até a pasta **\services\scripts\local\launch** em sua cópia clonada do repositório.

1. Execute os seguintes comandos para instalar as ferramentas da CLI **pcs** e entrar em sua conta do Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Execute o script **start.cmd**. O script solicitará as seguintes informações:
   * Um nome de solução.
   * A assinatura do Azure a utilizar.
   * O local do datacenter do Azure para usar.

     O script cria um grupo de recursos no Azure com o nome da solução. Esse grupo de recursos contém os recursos do Azure usados pelo acelerador de solução. Depois que você não precisar mais dos recursos correspondentes, você pode excluir esse grupo de recursos.

     O script também adiciona um conjunto de variáveis de ambiente com um prefixo **PCS** em seu computador local. Quando você inicia os contêineres do Docker ou os projetos de microsserviço localmente, eles leem seus valores de configuração dessas variáveis de ambiente.

     > [!TIP]
     > Quando o script é concluído, ele também salva as variáveis de ambiente para um arquivo chamado **\<sua pasta base\>\\.pcs\\\<nome da solução\>.env**. Você pode usá-las para implantações futuras de acelerador de solução. Observe que quaisquer variáveis de ambiente definidas em seu computador local substituem os valores no arquivo **serviços\\scripts\\local\\.env** ao executar **docker-compose**.

1. Saia do ambiente de linha de comando.

### <a name="use-existing-azure-resources"></a>Usar recursos existentes do Azure

Se você já tiver criado os recursos necessários do Azure, crie as variáveis de ambiente correspondentes no seu computador local. Elas podem ser salvas no arquivo **\<sua pasta base\>\\.pcs\\\<nome da solução\>.env** da implantação. Observe que quaisquer variáveis de ambiente definidas em seu computador local substituem os valores no arquivo **serviços\\scripts\\local\\.env** ao executar **docker-compose**.