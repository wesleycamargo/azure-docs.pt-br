---
title: Criar um aplicativo de funções do Azure com Java e IntelliJ| Microsoft Docs
description: Guia de instruções para criar e publicar um aplicativo sem servidor disparado por HTTP simples usando Java e IntelliJ para Azure Functions.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: azure functions, funções, processamento de eventos, computação, arquitetura sem servidor, java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 5e265543e2ce5feeed095d89cdb47ede9817bad1
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40002671"
---
# <a name="create-your-first-function-with-java-and-intellij-preview"></a>Criar sua primeira função com Java e IntelliJ (versão prévia)

> [!NOTE] 
> O Java para Azure Functions está atualmente na versão prévia.

Este artigo mostra como criar um projeto de função [sem servidor](https://azure.microsoft.com/overview/serverless-computing/) com IntelliJ IDEA e Apache Maven, testar e depurar no próprio computador a partir do IDE e implantá-lo no Azure Functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Para desenvolver um aplicativo de funções com Java e IntelliJ, é necessário ter instalado:

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/), versão 8.
-  [Apache Maven](https://maven.apache.org), versão 3.0 ou posterior.
-  [IntelliJ IDEA](https://www.jetbrains.com/idea/download), Community ou Ultimate com ferramenta Maven.
-  [CLI do Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> A variável de ambiente JAVA_HOME deve ser definida como o local de instalação do JDK para concluir este guia de início rápido.

Também é altamente recomendável instalar as [Ferramentas Principais do Azure Functions, versão 2](functions-run-local.md#v2), que fornecem um ambiente de desenvolvimento local para gravar, executar e depurar no Azure Functions. 


## <a name="create-a-functions-project"></a>Criar um projeto do Functions

1. No IntelliJ IDEA, clique para **Criar Novo Projeto**.  
1. Selecione para criar a partir do **Maven**
1. Marque a caixa de seleção para **Criar do modelo** e **Adicionar Modelo** para o [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - GroupId: com.microsoft.azure
    - ArtifactId: azure-functions-archetype
    - Versão: use a última versão do [repositório central](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Criar IntelliJ Maven](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Clique em **Avançar** e insira os detalhes do projeto atual e, por fim, **Concluir**.

O Maven cria os arquivos de projeto em uma nova pasta com um nome de _artifactId_. O código gerado no projeto é uma função [disparada por HTTP](/azure/azure-functions/functions-bindings-http-webhook) simples que ecoa o corpo da solicitação HTTP disparando.

## <a name="run-functions-locally-in-the-ide"></a>Executar funções localmente no IDE

> [!NOTE]
> [As Ferramentas Principais do Azure Functions, versão 2](functions-run-local.md#v2) devem ser instaladas para executar e depurar funções localmente.

1. Selecione para importar alterações ou certifique-se de que [importação automática](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)está habilitada.
1. Abra a barra de ferramentas **Projetos Maven**
1. Em Ciclo de Vida, clique duas vezes no **pacote** para empacotar e compilar a solução e criar um diretório de destino.
1. Em Plug-ins -> clique duas vezes em azure-functions**azure-functions:run** para iniciar o tempo de execução local do Azure Functions.  
  ![Barra de ferramentas Maven para Azure Functions](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

Feche a caixa de diálogo de execução quando terminar de testar a função. Apenas um host de função pode estar ativo e em execução localmente de cada vez.

### <a name="debug-the-function-in-intellij"></a>Depurar a função no IntelliJ
Para iniciar o host de função no modo de depuração, adicione **-DenableDebug** como argumento quando ao executar sua função. Você poderia executar a linha de comando no terminal abaixo ou configurá-la no [metas goals](https://www.jetbrains.com/help/idea/maven-support.html#run_goal). Em seguida, o host de função abrirá uma porta de depuração na 5005. 

```
mvn azure-functions:run -DenableDebug
```

Para depurar no IntelliJ, no menu **Executados**, selecione **Editar Configurações**. Clique em **+** para adicionar um **Remoto**. Preencha o **Nome** e as **Configurações**, e em seguida clique em **OK** para salvar a configuração. Após a configuração, clique em **Depurar** 'Seu nome de Configuração Remota' ou pressione **SHIFT+F9** para iniciar a depuração.

![Depurar funções no IntelliJ](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

Quando terminar, pare o depurador e o processo em execução. Apenas um host de função pode estar ativo e em execução localmente de cada vez.

## <a name="deploy-the-function-to-azure"></a>Implantar a função no Azure

O processo de implantação no Azure Functions usa credenciais de conta da CLI do Azure. [Faça logon com a CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) antes de continuar usando o prompt de comando do computador.

```azurecli
az login
```

Implante o código em um novo aplicativo de Funções usando o `azure-functions:deploy` destino Maven ou selecione a opção azure-functions:deploy na janela Projetos Maven.

```
mvn azure-functions:deploy
```

Quando a implantação for concluída, você verá a URL que pode usar para acessar seu aplicativo de funções do Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>Próximas etapas

- Examine o [Guia do desenvolvedor de funções Java](functions-reference-java.md) para saber mais sobre o desenvolvimento de funções Java.
- Adicione outras funções com gatilhos diferentes ao seu projeto usando o destino Maven `azure-functions:add`.
