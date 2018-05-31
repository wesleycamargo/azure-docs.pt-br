---
title: Solução de problemas | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço do Kubernetes do Azure, contêineres
manager: douge
ms.openlocfilehash: 371bb9195266f3511d115de2532e6b64f49ef26f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199289"
---
# <a name="troubleshooting-guide"></a>Guia de Solução de Problemas

Este guia contém informações sobre problemas comuns que você pode ter ao usar o Azure Dev Spaces.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Erro 'erro de conexão de upstream ou desconectar/reiniciar antes dos cabeçalhos'
É possível ver esse erro ao tentar acessar o serviço. Por exemplo, ao acessa a URL do serviço em um navegador. 

### <a name="reason"></a>Motivo 
A porta do contêiner não está disponível. Isso pode ocorrer porque: 
* O contêiner ainda está em processo de ser compilado e implantado. Esse poderá ser o caso, se você executar `azds up` ou iniciar o depurador e, em seguida, tentar acessar o contêiner antes de ser implantado com êxito.
* A configuração de porta não é consistente em todo o Dockerfile, Helm Chart e qualquer código de servidor que abra uma porta.

### <a name="try"></a>Experimente:
1. Se o contêiner estiver sendo compilado/implantado, você poderá aguardar de 2 a 3 segundos e tentar acessar o serviço novamente. 
1. Verifique a configuração de porta. Os números de porta especificados devem ser **idênticos** em todos os ativos abaixo:
    * **Dockerfile:** especificado pela instrução `EXPOSE`.
    * **[Gráfico Helm](https://docs.helm.sh):** especificado pelos valores `externalPort` e `internalPort` para um serviço (geralmente localizado em um arquivo `values.yml`),
    * Quaisquer portas sendo abertas no código do aplicativo, por exemplo, no Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Arquivo de configuração não encontrado
Você executa `azds up` e obtém o erro a seguir: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Motivo
É necessário executar `azds up` a partir do diretório raiz do código que deseja executar e inicializar a pasta de código para execução com o Azure Dev Spaces.

### <a name="try"></a>Experimente:
1. Altere o diretório atual para a pasta raiz que contém o código de serviço. 
1. Caso não tenha um arquivo azds.yaml na pasta de código, execute `azds prep` para gerar ativos do Azure Dev Spaces, Docker e Kubernetes.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Erro: 'o programa do pipe 'azds' foi encerrado inesperadamente com o código de 126.'
Iniciar o depurador de VS Code pode, às vezes, resultar nesse erro. Esse é um problema conhecido.

### <a name="try"></a>Experimente:
1. Feche e abra novamente o VS Code.
2. Pressione F5 novamente.


## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Erro de depuração 'não há suporte para tipo de depuração configurado 'coreclr' '
Executar o depurador de VS Code relata o erro: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Motivo
Você não tem a extensão do VS Code para Azure Dev Spaces instalados no computador de desenvolvimento.

### <a name="try"></a>Experimente:
Instale a [Extensão do VS Code para Azure Dev Spaces](get-started-netcore.md).

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>O tipo ou o nome do namespace 'MyLibrary' não foi encontrado

### <a name="reason"></a>Motivo 
O contexto de compilação está no nível de projeto/serviço por padrão, portanto, um projeto de biblioteca que você está usando não será encontrado.

### <a name="try"></a>Experimente:
O que precisa ser feito:
1. Modificar o arquivo azds.yaml para configurar o contexto de compilação para o nível da solução.
2. Modificar os arquivos Dockerfile e Dockerfile.develop para referir aos arquivos csproj corretamente, relativos ao novo contexto de compilação.
3. Implantar um arquivo .dockerignore ao lado do arquivo .sln e modificar, conforme necessário.

Você pode encontrar um exemplo em https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftconnectedenvironmentregisteraction-authorization-error"></a>Erro de autorização 'Microsoft.ConnectedEnvironment/register/action'
Você pode ver o seguinte erro quando estiver gerenciando um Azure Dev Space e trabalhando em uma assinatura do Azure para a qual não possui acesso de Proprietário ou Colaborador.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.ConnectedEnvironment/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Motivo
A assinatura do Azure selecionada não registrou o namespace Microsoft.ConnectedEnvironment.

### <a name="try"></a>Experimente:
Alguém com acesso Proprietário ou Colaborador à assinatura do Azure pode executar o comando CLI do Azure a seguir para registrar manualmente o namespace Microsoft.ConnectedEnvironment:

```cmd
az provider register --namespace Microsoft.ConnectedEnvironment
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>O Azure Dev Spaces parece não usar o Dockerfile existente para criar um contêiner 

### <a name="reason"></a>Motivo
O Azure Dev Spaces pode ser configurado para apontar para um Dockerfile específico no projeto. Se aparecer que o Azure Dev Spaces não está usando o Dockerfile que você espera compilar seus contêineres, talvez seja necessário informar explicitamente ao Azure Dev Spaces onde ele está. 

### <a name="try"></a>Experimente:
Abra o arquivo `azds.yaml` que foi gerado pelo Azure Dev Spaces no projeto. Use a diretiva `configurations->develop->build->dockerfile` para apontar para o Dockerfile que você deseja usar:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```