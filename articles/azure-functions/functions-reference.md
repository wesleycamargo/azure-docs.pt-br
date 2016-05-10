<properties
	pageTitle="Referência do desenvolvedor do Azure Functions | Microsoft Azure"
	description="Entenda os conceitos e componentes do Azure Functions que são comuns a todas as linguagens e associações."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="04/07/2016"
	ms.author="chrande"/>

# Referência do desenvolvedor do Azure Functions

As funções do Azure Functions compartilham alguns conceitos técnicos e componentes principais, independentemente do linguagem ou da associação usada. Antes de ir para o aprendizado de detalhes específicos de um determinado linguagem ou associação, leia esta visão geral que se aplica a todos eles.

Este artigo pressupõe que você já tenha lido a [Visão geral do Azure Functions](functions-overview.md) e está familiarizado com [conceitos do SDK de WebJobs como gatilhos, associações e tempo de execução do JobHost](../app-service-web/websites-dotnet-webjobs-sdk.md). O Azure Functions é baseado no SDK de WebJobs.

## função

Uma *Função* é o principal conceito no Azure Functions. Grave um código para uma função em uma linguagem de sua escolha e salve os arquivos de código e um arquivo de configuração na mesma pasta. A configuração é em JSON e o arquivo é denominado `function.json`. Há suporte a várias linguagens e cada uma tem uma experiência ligeiramente diferente otimizada para funcionar melhor nessa linguagem. Exemplo de estrutura de pasta:

```
mynodefunction
| - function.json
| - index.js
| - node_modules
| | - ... packages ...
| - package.json
mycsharpfunction
| - function.json
| - run.csx
```

## function.JSON e associações

O arquivo `function.json` contém uma configuração específica para uma função, incluindo suas associações. O tempo de execução lê esse arquivo para determinar quais eventos disparar, quais dados serão incluídos no chamamento da função e para onde enviar os dados passados pela própria função.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Você pode impedir que o tempo de execução execute a função definindo a propriedade `disabled` como `true`.

A propriedade `bindings` é onde você configura gatilhos e associações. Cada associação compartilha algumas configurações comuns e outras que são específicas para um determinado tipo de associação. Todas as associações exigem as seguintes configurações:

|Propriedade|Valores/Tipos|Comentários|
|---|-----|------|
|`type`|string|Tipo de associação. Por exemplo: `queueTrigger`.
|`direction`|'in', 'out'| Indica se a associação é para receber dados na função ou enviar dados a partir da função.
| `name` | string | O nome que será usado para os dados associados na função. Em C#, ele será o nome de um argumento. Em JavaScript, será a chave em uma lista de chave/valor.

## Tempo de execução (host de script e host Web)

O tempo de execução, também conhecido como o host de script, é o host do SDK de WebJobs subjacente que escuta eventos, coleta e envia dados e, no fim das contas, executa seu código.

Para facilitar gatilhos HTTP, há também um host Web que foi desenvolvido para ficar na frente do host de script em cenários de produção. Isso ajuda a isolar o host de script a partir do tráfego de front-end gerenciado pelo host Web.

## Estrutura de pastas

Um host de script aponta para uma pasta que contém um arquivo de configuração e uma ou mais funções.

```
parentFolder (for example, wwwroot)
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

O arquivo *host.json* contém uma configuração específica de host de script e fica na pasta pai.

Cada função tem uma pasta que contém arquivos de código, *function.json* e outras dependências.

Ao configurar um projeto para implantar funções em um aplicativo de função no Serviço de Aplicativo do Azure, você poderá tratar essa estrutura de pastas como o código do site. Você pode usar ferramentas como implantação e integração contínuas ou scripts de implantação personalizados para implantar a instalação do pacote de tempo ou a transpilação do código.

## Execução paralela

Quando vários eventos de gatilho ocorrem mais rápido do que um tempo de execução single-threaded de função pode processar, o tempo de execução pode invocar a função várias vezes em paralelo. Se um aplicativo de função estiver usando o [Plano de Serviço Dinâmico](functions-scale.md#dynamic-service-plan), ele pode escalar horizontalmente de modo automático para até dez instâncias simultâneas. Cada instância do aplicativo de função, quer seja executada no Plano de Serviço Dinâmico ou em um [Plano de Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) comum, pode processar chamadas simultâneas de função em paralelo usando vários threads. O número máximo de chamadas simultâneas de função em cada instância do aplicativo de função varia com base no tamanho da memória do aplicativo de função.

## Azure Functions Pulse  

O Pulse é um fluxo de evento dinâmico que mostra quantas vezes sua função é executada, os êxitos e as falhas. Você também pode monitorar o tempo médio de execução. Incluiremos mais recursos e personalização ao longo do tempo. Você pode acessar a página **Pulso** na guia **Monitoramento**.

## Associações

Veja uma tabela de todas as associações com suporte.

[AZURE.INCLUDE [computação dinâmica](../../includes/functions-bindings.md)]

## Problemas de relatórios

[AZURE.INCLUDE [Problemas de relatórios](../../includes/functions-reporting-issues.md)]

## Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Referência do desenvolvedor de C# do Azure Functions](functions-reference-csharp.md)
* [Referência do desenvolvedor de NodeJS do Azure Functions](functions-reference-node.md)
* [Gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0427_2016-->