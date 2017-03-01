---
title: Trabalhando com proxies no Azure Functions | Microsoft Docs
description: "Visão geral de como usar Proxies do Azure Functions"
services: functions
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/19/2017
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: facdd2abbb90a85535b8936ea655a824134c4c8b
ms.openlocfilehash: 3e8b8f9908cf24baf7a5d70521c79dbd470001f8
ms.lasthandoff: 02/22/2017


---
# <a name="working-with-azure-functions-proxies-preview"></a>Trabalho com Proxies do Azure Functions (visualização)

> [!Note] 
> Proxies do Azure Functions está atualmente em visualização. É gratuito enquanto na visualização, mas as funções padrão cobrança se aplica para execuções de proxy. Veja [Preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/) para saber mais.

Este artigo explica como configurar e trabalhar com proxies do Azure Functions. Esse recurso permite que você especifique os pontos de extremidade em seu aplicativo de funções que são implementadas por outro recurso. Você pode usar esses proxies para dividir uma API grande em vários aplicativos de função (como uma arquitetura de microsserviços), enquanto ainda apresenta uma única superfície de API para clientes.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]


## <a name="a-nameenableaenabling-azure-functions-proxies"></a><a name="enable"></a>Habilitação de Proxies do Azure Functions

Os proxies não estão habilitados por padrão. Você pode criar proxies enquanto o recurso estiver desativado, mas não será executado. As etapas a seguir mostrarão como habilitar proxies:

1. Abra o [portal do Azure] e navegue até seu aplicativo de funções.
2. Selecione **Configurações do aplicativo de funções**.
3. Ative **Habilitar Proxies do Azure Functions (visualização)** em diante.

Você também pode retornar aqui para atualizar o tempo de execução do proxy medida que novos recursos forem disponibilizados.


## <a name="creating-a-proxy"></a>Criação de um proxy

Esta seção mostra como criar um proxy no portal de funções.

1. Abra o [portal do Azure] e navegue até seu aplicativo de funções.
2. No painel de navegação esquerdo, selecione **novo proxy**.
3. Forneça um nome para seu proxy.
4. Configurar o ponto de extremidade exposto no aplicativo função especificando o **modelo de rota** e **métodos HTTP**. Esses parâmetros se comportam de acordo com as regras para [gatilhos HTTP]
5. Defina a **URL de back-end** para outro ponto de extremidade. Isso pode ser uma função em outro aplicativo de funções, ou pode ser qualquer outra API.
6. Clique em Criar.

Seu proxy agora existe como um novo ponto de extremidade em seu aplicativo de funções. Da perspectiva do cliente, é equivalente a um HttpTrigger no Azure Functions. Você pode testar seu novo proxy copiando a URL do Proxy e testá-lo com seu cliente HTTP favorito.


## <a name="a-namemodify-requestsamodifying-backend-requests"></a><a name="modify-requests"></a>Modificando solicitações de back-end

O parâmetro de URL do back-end não precisa ser estático. Você pode condição ele na entrada nas configurações do aplicativo ou a solicitação.


### <a name="using-request-parameters"></a>Uso de parâmetros da solicitação

Parâmetros usados no modelo de rota podem ser usados como entradas para a propriedade de URL do back-end. Os valores são referenciados pelo nome entre chaves "{}".

Por exemplo, se um proxy tem um modelo de rota como `/pets/{petId}`, a URL do back-end pode incluir o valor de `{petId}`, como em `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Se o modelo de rota termina em um caractere curinga, como `/api/{*restOfPath}`, o valor `{restOfPath}` será uma representação de cadeia de caracteres dos segmentos de caminho restantes da solicitação de entrada.


### <a name="using-application-settings"></a>Uso de configurações do aplicativo

Você também pode consultar [configurações do aplicativo](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop), envolvendo o nome da configuração entre sinais de porcentagem '%'.

Por exemplo, uma URL de back-end do `https://%ORDER_PROCESSING_HOST%/api/orders` terá "% ORDER_PROCESSING_HOST %" substituído pelo valor da configuração ORDER_PROCESSING_HOST.

> [!TIP] 
> Usar configurações do aplicativo para hosts de back-end quando você tem várias implantações ou ambientes de teste. Dessa forma, você pode garantir que você sempre está se comunicando com o back-end à direita para esse ambiente.



## <a name="deployment-methods"></a>Métodos de implantação

Os proxies que você configura são armazenados em um arquivo proxies.json, localizado na raiz do diretório de aplicativo função. Você pode editar esse arquivo manualmente e implantá-lo como parte do seu aplicativo ao usar qualquer um do [métodos de implantação](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) que ofereça suporte a funções.

O recurso deve ser habilitado para que o arquivo a ser processado. Você pode fazer isso seguindo as instruções em [Como habilitar Proxies do Azure Functions](#enable).

Proxies.json é definido por um objeto de proxies, composto de proxies nomeados e suas definições. Um arquivo de exemplo pode parecer com o seguinte:

```json
{
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Cada proxy tem um nome amigável, como "proxy1", no exemplo acima. O objeto de definição de proxy correspondente é definido pelas seguintes propriedades:

* **matchCondition**: obrigatório - um objeto que define as solicitações que irá disparar a execução desse proxy. Ele contém duas propriedades compartilhadas com [gatilhos HTTP]:
    * _methods_: essa é uma matriz dos métodos HTTP para a qual proxy responderá. Se não for especificado, o proxy responderá a todos os métodos HTTP na rota.
    * _route_: obrigatório - define o modelo da rota, controlando para quais URLs de solicitação seu proxy responderá. Ao contrário de disparadores HTTP, não há nenhum valor padrão.
* **backendUri**: a URL do recurso de back-end ao qual a solicitação deve ser transmitida por proxy. Esse valor pode ser modelado, como descrito em [Modificação de solicitações de back-end](#modify-requests). Se esta propriedade não for incluída, o Azure Functions responderá com um HTTP 200 OK.

> [!Note] 
> Os Proxies do Azure Functions da propriedade route não respeitam a propriedade routePrefix da configuração de host do Functions. Se você quiser incluir um prefixo, como /api, ela deve ser incluída na propriedade de rota.



[portal do Azure]: https://portal.azure.com
[gatilhos HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
