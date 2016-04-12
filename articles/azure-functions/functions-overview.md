<properties
   pageTitle="Visão geral das Funções do Azure | Microsoft Azure"
   description="Entenda como as Funções do Azure podem otimizar cargas de trabalho assíncronas criando funções simples que podem ser escritas em minutos."
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
   ms.date="03/09/2016"
   ms.author="cfowler;mahender"/>
   
   
# Visão geral das Funções do Azure

## Uma maneira mais rápida de conferir as funções

Escreva qualquer função em um minuto, seja ao precisar de um trabalho simples para limpar um banco de dados ou ao criar uma funcionalidade que processa milhões de mensagens de dispositivos conectados. Use sua linguagem de desenvolvimento preferida (C#, Node.js, Python e muito mais). Pague somente pelo tempo de execução do seu código e confie no Azure para dimensioná-lo conforme a necessidade.

As Funções do Azure são uma solução para executar facilmente pequenos trechos de código, ou "funções", na nuvem. Você pode simplesmente escrever o código de que necessita para o problema em questão, sem se preocupar com todo o aplicativo ou a infraestrutura para executá-lo. Isso pode tornar o desenvolvimento ainda mais produtivo, e você pode [começar sua primeira função](functions-create-first-azure-function.md) em apenas alguns minutos.

## O que posso fazer com as Funções?

As Funções do Azure são uma ótima solução para processamento de dados e integração de sistemas, que trabalha com a IoT (Internet das coisas) e cria APIs e microsserviços simples.

As Funções possuem uma galeria de modelos para cenários comuns, incluindo:

* Responder a uma solicitação de webhook do GitHub
* Redimensionar uma imagem que foi carregada para o Armazenamento do Azure
* Trabalhar com filas de processamento de pedidos
* E muito mais! 

Para uma análise mais profunda sobre como funcionam as Funções e alguns exemplos de configurações, confira a [Referência do desenvolvedor](functions-reference.md).

## Recursos

As Funções do Azure são uma plataforma empresarial completa que vem com recursos para facilitar as tarefas complicadas de integração e conectividade. Com esse conjunto básico de recursos, os desenvolvedores que usam as Funções do Azure podem se tornar ainda mais produtivos ao se concentrarem apenas em sua meta em vez de montarem partes da infraestrutura.

Os recursos abaixo estão incluídos nas Funções do Azure:
    
* **Opção de idioma** - escreva funções usando c#, Node.js, Python, F#, PHP, batch, bash, Java ou qualquer executável.  
* **Modelo de preços de pagamento por uso** - pague somente o tempo gasto na execução de seu código. Consulte a opção Plano de serviço de aplicativo dinâmico na [seção Preço](#pricing) abaixo.  
* **Traga suas próprias dependências** - as Funções dão suporte a NuGet e NPM e, portanto, você pode usar suas bibliotecas favoritas.  
* **Segurança integrada** -proteja funções disparadas por HTTP com provedores de OAuth como Azure Active Directory, Facebook, Google, Twitter e Conta da Microsoft.  
* **Integração sem código** - aproveite facilmente as ofertas dos serviços do Azure e de SaaS (software como um serviço). Confira a [seção Integrações](#integrations) abaixo para obter alguns exemplos.  
* **Desenvolvimento flexível** - modifique as funções com um editor do portal ou configure a integração contínua e implante seu código por meio do GitHub, do Visual Studio Team Services e muito mais.  
* **Software livre** - As Funções são um software livre e [estão disponíveis no GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

### <a name="integrations"></a>Integrações

As Funções do Azure dão suporte a diversas integrações com o Azure e com serviços de terceiros. Você pode usá-los para disparar a sua função e iniciar a execução ou para servir como entrada e saída para seu código. A tabela abaixo mostra algumas integrações de exemplo com suporte das Funções do Azure.

[AZURE.INCLUDE [computação dinâmica](../../includes/functions-bindings.md)]

## <a name="pricing"></a>Quanto custam as Funções?

Há duas maneiras de executar as Funções do Azure: usando um Plano de Serviço de Aplicativo dinâmico e um plano de Serviço de Aplicativo clássico.

Em um **Plano de Serviço de Aplicativo dinâmico**, você não precisa se preocupar com o gerenciamento de recursos. Sempre que a função é executada, o Azure fornece todos os recursos computacionais necessários. Você paga apenas o tempo gasto com seu código em execução. Os detalhes de preços completos estão disponíveis na [página Preço das Funções](/pricing/details/functions).

Um **Plano de Serviço de Aplicativo clássico** permite que você execute funções como seus aplicativos Web, móveis e de API. Isso é uma boa solução se você já está usando o serviço de aplicativo para outros aplicativos, pois suas funções podem ser executadas no mesmo plano sem nenhum custo adicional. Os detalhes completos podem ser encontrados na [página Preço do Serviço de Aplicativo](/pricing/details/app-service/).

<!---HONumber=AcomDC_0406_2016-->