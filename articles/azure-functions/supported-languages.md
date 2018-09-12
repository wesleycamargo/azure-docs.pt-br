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
ms.openlocfilehash: b735f93b2d7ad093ef752fd5f26be729a1157b37
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44090669"
---
# <a name="supported-languages-in-azure-functions"></a>Linguagens com suporte no Azure Functions

Este artigo explica os níveis de suporte oferecido para linguagens que você pode usar com o Azure Functions.

## <a name="levels-of-support"></a>Níveis de suporte

Há três níveis de suporte:

* **Geralmente disponível (GA)** – com suporte total e aprovado para uso em produção.
* **Versão prévia** – ainda não tem suporte, mas é esperado para alcançar o status de GA no futuro.
* **Experimental** – não tem suporte e pode ser abandonado no futuro; nenhuma garantia de status de GA ou de versão prévia eventual.

## <a name="languages-in-runtime-1x-and-2x"></a>Linguagens no tempo de execução 1.x e 2.x

[Duas versões do tempo de execução do Azure Functions](functions-versions.md) estão disponíveis. O tempo de execução 1.x é GA. É o único tempo de execução que é aprovado para aplicativos de produção. O tempo de execução 2.x está atualmente na versão prévia, portanto, as linguagens com as quais ele é compatível estão na versão prévia. A tabela a seguir indica quais linguagens têm suporte em cada versão de tempo de execução.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Linguagens experimentais

As linguagens experimentais na versão 1.x não escalam bem e não oferecem suporte a todas as associações. Por exemplo, o Python é lento porque o tempo de execução do Functions executa *python.exe* com cada chamada de função. E, embora o Python seja compatível com associações de HTTP, não é possível acessar o objeto de solicitação.

O suporte experimental para o PowerShell é limitado à versão 5.1 porque é a que está instalada nas máquinas virtuais por padrão em que as máquinas virtuais no aplicativo de funções está sendo executado. Se desejar executar scripts do PowerShell, considere a [Automação do Azure](https://azure.microsoft.com/services/automation/).

Se você quiser usar uma das linguagens que estão disponíveis somente no 1.x, permaneça no tempo de execução 1.x. Mas não use linguagens experimentais para qualquer coisa da qual você dependa, pois não há suporte oficial para elas. Você pode solicitar ajuda [criando problemas de GitHub](https://github.com/Azure/azure-webjobs-sdk-script/issues), mas não devem ser abertos casos de suporte para problemas com linguagens experimentais. 

O tempo de execução versão 2.x não oferece suporte a linguagens experimentais. O suporte para novos idiomas é adicionado somente quando o idioma pode ter suporte em produção. 

### <a name="language-extensibility"></a>Extensibilidade de linguagem

O tempo de execução 2.x é projetado para oferecer [extensibilidade de linguagem](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Entre as primeiras linguagens que devem se basear nesse modelo de extensibilidade está Java, que está na versão prévia no 2.x.

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
