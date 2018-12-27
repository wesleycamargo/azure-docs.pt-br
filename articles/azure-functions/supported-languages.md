---
title: Linguagens com suporte no Azure Functions
description: Saiba quais linguagens são compatíveis (GA) e quais são experimentais ou estão na versão prévia.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: 5f55122b3bf4bb7160459d524b20dd1303cc0fd8
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835413"
---
# <a name="supported-languages-in-azure-functions"></a>Linguagens com suporte no Azure Functions

Este artigo explica os níveis de suporte oferecido para linguagens que você pode usar com o Azure Functions.

## <a name="levels-of-support"></a>Níveis de suporte

Há três níveis de suporte:

* **Geralmente disponível (GA)** – com suporte total e aprovado para uso em produção.
* **Versão prévia** – ainda não tem suporte, mas é esperado para alcançar o status de GA no futuro.
* **Experimental** – não tem suporte e pode ser abandonado no futuro; nenhuma garantia de status de GA ou de versão prévia eventual.

## <a name="languages-in-runtime-1x-and-2x"></a>Linguagens no tempo de execução 1.x e 2.x

[Duas versões do tempo de execução do Azure Functions](functions-versions.md) estão disponíveis. A tabela a seguir indica quais linguagens têm suporte em cada versão de tempo de execução.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Linguagens experimentais

As linguagens experimentais na versão 1.x não escalam bem e não oferecem suporte a todas as associações. Por exemplo, o suporte experimental para o PowerShell em 1.x é limitado à versão 5.1, por ser instalado por padrão nas VMs nas quais os aplicativos de função são executados. Se desejar executar scripts do PowerShell, considere a [Automação do Azure](https://azure.microsoft.com/services/automation/).

Não use recursos experimentais para algo em que você confia, pois não há suporte oficial para eles. Não devem ser abertos casos de suporte para problemas com linguagens experimentais. 

O tempo de execução versão 2.x não oferece suporte a linguagens experimentais. O suporte para novos idiomas é adicionado somente quando o idioma pode ter suporte em produção. 

### <a name="language-extensibility"></a>Extensibilidade de linguagem

O tempo de execução 2.x é projetado para oferecer [extensibilidade de linguagem](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). As linguagens JavaScript e Java no tempo de execução 2.x são criadas com essa extensibilidade.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar uma das linguagens GA ou em versão prévia no Azure Functions, consulte os seguintes recursos:

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)

> [!div class="nextstepaction"]
> [Python](functions-reference-python.md)
