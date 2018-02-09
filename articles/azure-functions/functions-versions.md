---
title: "Visão geral de versões de tempo de execução do Azure Functions"
description: "O Azure Functions é compatível com várias versões do tempo de execução. Aprenda as diferenças entre elas e como escolher a certa para você."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 9f916aaa8032ff519709d73a1c1f51195f811686
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-functions-runtime-versions-overview"></a>Visão geral de versões de tempo de execução do Azure Functions

 Há duas versões principais do tempo de execução do Azure Functions.: 1.x e 2.x. Este artigo explica como escolher qual versão principal deve ser usada.

> [!IMPORTANT] 
> O tempo de execução 1.x é a única versão aprovada para uso em produção.

| Tempo de execução | Status |
|---------|---------|
|1.x|Disponível de forma geral (GA)|
|2. x|Visualização|

Para obter informações sobre como configurar um aplicativo de função ou ambiente de desenvolvimento para uma versão específica, consulte [Como escolher versões de tempo de execução do Azure Functions](set-runtime-version.md) e [Codificar e testar o Azure Functions localmente](functions-run-local.md).

## <a name="cross-platform-development"></a>Desenvolvimento multiplataforma

O tempo de execução 1.x oferece suporte a desenvolvimento e hospedagem apenas no portal ou no Windows. O tempo de execução 2.x é executado no .NET Core, o que significa que ele pode ser executado em todas as plataformas com suporte no .NET Core, incluindo macOS e Linux. Isso permite cenários de desenvolvimento e hospedagem de plataforma cruzada que não são possíveis com a 1.x.

## <a name="languages"></a>Linguagens

O tempo de execução 2.x usa um novo modelo de extensibilidade de linguagem. Inicialmente, JavaScript e Java levam vantagem nesse novo modelo. Linguagens experimentais do Azure Functions 1.x não foram atualizadas para usar o novo modelo, por isso eles não são compatíveis na 2.x. A tabela a seguir indica quais linguagens de programação têm suporte em cada versão de tempo de execução.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Para obter mais informações, consulte [Linguagens com suporte](supported-languages.md).

## <a name="bindings"></a>Associações 

O tempo de execução 2.x usa um novo [modelo de extensibilidade de associação](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) que oferece estas vantagens:

* Suporte para extensões de associação de terceiros.
* Desacoplamento de tempo de execução e associações. Isso permite que extensões de associação sejam lançadas independentemente e com controle de versão. Você pode, por exemplo, optar por atualizar para uma versão de uma extensão que se baseia em uma versão mais recente de um SDK subjacente.
* Um ambiente de execução mais leve, onde apenas as associações em uso são conhecidas e carregadas pelo tempo de execução.

Todas as associações do Azure Functions internas adotaram este modelo e não são mais incluídas por padrão, exceto para o gatilho Temporizador e o gatilho HTTP. Essas extensões são instaladas automaticamente quando você cria funções por meio de ferramentas como o Visual Studio ou por meio do portal.

A tabela a seguir indica quais associações têm suporte em cada versão de tempo de execução.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="known-issues-in-2x"></a>Problemas conhecidos na 2.x

Para obter informações sobre o suporte a associações e outras lacunas funcionais no 2.x, consulte [Problemas conhecidos do Runtime 2.0](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Direcionar para o tempo de execução 2.0 em seu ambiente de desenvolvimento local](functions-run-local.md)

> [!div class="nextstepaction"]
> [Consulte as notas de versão para versões de tempo de execução](https://github.com/Azure/azure-webjobs-sdk-script/releases)