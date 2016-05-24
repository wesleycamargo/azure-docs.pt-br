<properties
   pageTitle="Visão geral das Funções do Azure | Microsoft Azure"
   description="Compreenda como usar o Azure Functions para otimizar cargas de trabalho assíncronas em minutos."
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="05/08/2016"
   ms.author="cfowler;mahender;glenga"/>
   
   
# Visão geral das Funções do Azure

O Azure Functions é uma solução para executar facilmente pequenos trechos de código, ou "funções", na nuvem. Você pode simplesmente escrever o código de que necessita para o problema em questão, sem se preocupar com todo o aplicativo ou a infraestrutura para executá-lo. Isso pode tornar o desenvolvimento ainda mais produtivo, e você pode usar a linguagem de desenvolvimento de sua escolha, como C#, Node.js, Python ou PHP. Pague somente pelo tempo de execução do seu código e confie no Azure para dimensioná-lo conforme a necessidade.

Este tópico fornece uma visão geral de alto nível do Azure Functions. Se você quiser começar a usar imediatamente o Azure Functions, comece com [Criar seu primeiro Azure Function](functions-create-first-azure-function.md). Se você estiver procurando informações mais técnicas sobre o Functions, confira a [referência do desenvolvedor](functions-reference.md).

## Recursos

Aqui estão alguns dos principais recursos do Azure Functions:
    
* **Opção de idioma** - escreva funções usando C#, Node.js, Python, F#, PHP, batch, bash, Java ou qualquer executável.  
* **Modelo de preços de pagamento por uso** - pague somente o tempo gasto na execução de seu código. Consulte a opção Plano de Serviço de Aplicativo Dinâmico na [seção Preço](#pricing) abaixo.  
* **Traga suas próprias dependências** - o Functions dá suporte a NuGet e NPM e, portanto, você pode usar suas bibliotecas favoritas.  
* **Segurança integrada** -proteja funções disparadas por HTTP com provedores de OAuth como Azure Active Directory, Facebook, Google, Twitter e Conta da Microsoft.  
* **Integração simplificada** - aproveite facilmente as ofertas dos serviços do Azure e de SaaS (software como um serviço). Confira a [seção Integrações](#integrations) abaixo para obter alguns exemplos.  
* **Desenvolvimento flexível** - escreva suas funções diretamente no portal ou configure a integração contínua e implante seu código por meio do GitHub, do Visual Studio Team Services e de outras [ferramentas de desenvolvimento com suporte](../app-service-web/web-sites-deploy.md#deploy-using-an-ide).  
* **Software livre** - O Functions é um software livre e [está disponível no GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## O que posso fazer com as Funções?

O Azure Functions é uma ótima solução para processamento de dados, integração de sistemas, trabalho com a IoT (Internet das coisas) e criação de APIs e microsserviços simples. Considere o Functions para tarefas como processamento de imagens ou pedidos, manutenção de arquivo, tarefas de longa duração que você deseja executar em um thread de segundo plano ou tarefas que você deseja executar em uma agenda.

O Functions fornece modelos para você poder começar a usar os principais cenários, incluindo o seguinte:

* **BlobTrigger** -processa blobs do Armazenamento do Azure quando são adicionados a contêineres. Você pode usar isso para o redimensionamento de imagens.
* **EventHubTrigger** -responde a eventos que ocorrem nos seus dispositivos IoT.
* **Webhook genérico** -processa solicitações de webhook HTTP de qualquer serviço que dá suporte a webhooks.
* **Webhook GitHub** -responde a eventos que ocorrem em seus repositórios GitHub. Para obter um exemplo, confira [Criar uma função de API ou webhook](functions-create-a-web-hook-or-api-function.md).
* **HTTPTrigger** -dispara a execução do seu código usando uma solicitação HTTP.
* **QueueTrigger** -responde às mensagens que chegam em uma fila do Armazenamento do Azure. Para ver um exemplo, confira [Criar um Azure Function vinculado a um serviço do Azure](functions-create-an-azure-connected-function.md)
* **ServiceBusQueueTrigger** - conecte seu código a outros serviços do Azure ou serviços no local escutando filas de mensagens. 
* **ServiceBusTopicTrigger** - conecte seu código a outros serviços do Azure ou serviços no local assinando tópicos. 
* **TimerTrigger** -execute limpeza ou outras tarefas em lotes em uma programação predefinida. Para ver um exemplo, confira [Criar um função de processamento de eventos](functions-create-an-event-processing-function.md).

O Azure Functions dá suporte a *gatilhos*, que são maneiras de iniciar a execução do seu código, e *associações*, que são maneiras de simplificar a codificação de dados de entrada e saídas. Para obter uma descrição detalhada dos gatilhos e associações que o Azure Functions fornece, confira a [Referência do desenvolvedor de disparadores e associações do Azure Functions](functions-triggers-bindings.md).


## <a name="integrations"></a>Integrações

O Azure Functions integra-se com uma variedade de serviços do Azure e de terceiros. Você pode usá-los para disparar a sua função e iniciar a execução ou para servir como entrada e saída para seu código. As integrações de serviço a seguir têm suporte do Azure Functions.

* Banco de Dados de Documentos do Azure
* Hubs de eventos do Azure 
* Aplicativos Móveis do Azure (tabelas)
* Hubs de Notificação do Azure
* Barramento de Serviço do Azure (filas e tópicos)
* Armazenamento do Azure (blob, filas e tabelas) 
* GitHub (webhooks)
* No local (usando o Barramento de Serviço)

## <a name="pricing"></a>Quanto custa o Functions?

O Azure Functions tem dois tipos de planos de preços. Escolha a opção que melhor atende às suas necessidades:

* **Plano de hospedagem dinâmico** - quando sua função é executada, o Azure fornece todos os recursos computacionais necessários. Você não precisa se preocupar com o gerenciamento de recursos e paga apenas pelo tempo de execução do seu código. Os detalhes de preços completos estão disponíveis na [página Preço do Functions](/pricing/details/functions). 

* **Plano do Serviço de Aplicativo** -executa suas funções como aplicativos Web, móveis e de API. Quando você já estiver usando o Serviço de Aplicativo para os outros aplicativos, poderá executar suas funções no mesmo plano sem custo adicional. Os detalhes completos podem ser encontrados na [página Preço do Serviço de Aplicativo](/pricing/details/app-service/).

Para saber mais sobre o dimensionamento de suas funções, confira [Como dimensionar o Azure Functions](functions-scale.md).

##Próximas etapas

+ [Criar seu primeiro Azure Function](functions-create-first-azure-function.md) Crie diretamente sua primeira função usando o início rápido do Azure Functions. 
+ [Referência do desenvolvedor do Azure Functions](functions-reference.md) Fornece informações mais técnicas sobre o tempo de execução do Azure Functions e uma referência para funções de codificação e definição de associações e gatilhos.
+ [Testando o Azure Functions](functions-test-a-function.md) Descreve várias ferramentas e técnicas para testar suas funções.
+ [Como escalar o Azure Functions](functions-scale.md) Discute os planos de serviço disponíveis com o Azure Functions, incluindo o plano de serviço Dinâmico e como escolher o plano certo. 
+ [Saiba mais sobre o Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) O Azure Functions utiliza a plataforma Serviço de Aplicativo do Azure para a funcionalidade básica, como implantações, variáveis de ambiente e diagnóstico. 

<!---HONumber=AcomDC_0518_2016-->