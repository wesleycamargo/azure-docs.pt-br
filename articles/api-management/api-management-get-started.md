<properties
	pageTitle="Gerenciar sua primeira API no Gerenciamento de API do Azure"
	description="Saiba como criar APIs, operações e obtenha uma introdução ao Gerenciamento de API."
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="sdanie"/>

# Gerenciar sua primeira API no Gerenciamento de API do Azure

## <a name="overview"> </a>Visão geral

Este guia mostra como iniciar rapidamente a usar o Gerenciamento de API e como fazer sua primeira chamada à API.

## <a name="concepts"> </a>O que é o Gerenciamento de API do Azure?

O Gerenciamento de API do Azure permite usar qualquer back-end e lançar um programa completo de API com base nele.

Cenários comuns incluem:

* **Proteção de infraestrutura móvel** com a retenção de acesso às chaves de API, impedindo ataques DOS ao usar limitação ou políticas avançadas de segurança como validação de token JWT
* **Habilitação de ecossistemas de parceiro ISV** oferecendo integração rápida de parceiro através do portal do desenvolvedor e criando uma fachada de API para desassociação de implementações internas não prontas para consumo de parceiro
* **Execução de um programa de API interno** oferecendo um local centralizado para a organização se comunicar sobre a disponibilidade e as alterações mais recentes de APIs, retenção de acesso com base em contas organizacionais, tudo baseado em um canal protegido entre o gateway de API e o back-end


O sistema é composto pelos seguintes componentes:

* O **gateway de API** é o ponto de extremidade que:
  * aceita chamadas de API e as direciona para seu back-ends
  * verifica chaves de API, tokens JWT, certificados e outras credenciais
  * impõe o uso de cotas e limites de taxa
  * transforma sua API imediatamente sem modificações no código
  * armazena respostas de back-end em cache em que a instalação
  * registra logs de metadados de chamadas para fins de análise

* O **portal do editor** é a interface administrativa na qual você configura seu programa de API:
	* definir ou importar esquema de API
	* empacotar APIs em produtos
	* políticas de configuração como cotas ou transformações em APIs
	* obter insights por meio de análises
	* gerenciar usuários

* O **portal do desenvolvedor** serve como a presença da web principal para desenvolvedores, na qual eles podem:
	* ler a documentação da API
	* experimentar uma API por meio do console interativo
	* criar uma conta e fazer uma assinatura para obter chaves de API
	* acessar a análise do seu próprio uso


## <a name="create-service-instance"> </a>Criar uma instância de Gerenciamento de API

> Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][].

Esta etapa sobre como trabalhar com o Gerenciamento de API serve para criar uma instância de serviço. Faça logon no [Portal de Gerenciamento][] e clique em **Novo**, **Serviços de Aplicativos**, **Gerenciamento de API**, **Criar**.

![Nova instância do Gerenciamento de API][api-management-create-instance-menu]

Para obter a **URL**, especifique um nome de subdomínio exclusivo para usar na URL do serviço.

Selecione a **Assinatura** e a **Região** da sua instância de serviço. Após fazer suas seleções, clique no botão Avançar.

![Novo serviço de Gerenciamento de API][api-management-create-instance-step1]

Insira **Contoso Ltd.** como o **Nome da Organização** e insira seu endereço de email no campo de email do administrador.

>Esse endereço de email é usado para notificações do sistema de Gerenciamento de API. Para obter mais informações, consulte [Configurar Notificações][].

![Novo serviço de Gerenciamento de API][api-management-create-instance-step2]

As instâncias de serviço de Gerenciamento de API estão disponíveis em três camadas: Developer, Standard e Premium. Por padrão, as novas instâncias de serviço de gerenciamento de API são criadas usando a camada do desenvolvedor. Para selecionar a camada Standard ou Premium, marque a caixa de seleção **Configurações avançadas** e selecione a camada desejada na próxima tela.

>O Microsoft Azure oferece três camadas em que você pode executar o serviço de Gerenciamento de API: Developer, Standard e Premium. A Camada do Desenvolvedor é para programas pilotos de API, teste e desenvolvimento, onde a alta disponibilidade não é uma preocupação. Nas camadas Standard e Premium, você pode dimensionar sua contagem de unidade reservada para lidar com mais tráfego. As camadas Standard e Premium fornecem um serviço de Gerenciamento de API com o maior poder de processamento e desempenho. Este tutorial pode ser concluído com qualquer camada. Para obter mais informações sobre as camadas de Gerenciamento de API, consulte [Preços de Gerenciamento de API][].

Clique na caixa de seleção para criar sua instância de serviço.

![Novo serviço de Gerenciamento de API][api-management-instance-created]

Após criar a instância de serviço, a próxima etapa é criar ou importar a API.

## <a name="create-api"> </a>Importar uma API

Uma API consiste em um conjunto de operações que podem ser iniciadas a partir de uma aplicativo cliente. Todas as operações de API são colocadas em proxies de serviços Web existentes.

APIs podem ser criadas e as operações podem ser adicionadas manualmente, podendo também ser importadas. Neste tutorial, importaremos uma API para um serviço Web de Calculadora de exemplo fornecida pela Microsoft e hospedada no Azure.

>Para obter diretrizes sobre como criar uma API e adicionar operações manualmente, consulte [Como criar APIs](api-management-howto-create-apis.md) e [Como adicionar operações a uma API](api-management-howto-add-operations.md).

APIs são configuradas no portal do editor de API, que pode ser acessado no portal de gerenciamento do Azure. Para acessar o portal do editor, clique em **Gerenciar** no Portal do Azure para acessar o serviço de Gerenciamento de API.

![Portal do editor][api-management-management-console]

Para importar a API de calculadora, clique em **APIs** no menu **Gerenciamento de API** à esquerda e depois clique em **Importar API**.

![Botão Importar API][api-management-import-api]

![Adicionar nova API][api-management-import-new-api]

Execute as seguintes etapas para configurar a API de calculadora.

1. Clique em **Da URL**, digite **http://calcapi.cloudapp.net/calcapi.json** na caixa de texto **URL do documento de especificação** e clique no botão de opção **Swagger**. 2. Digite **calc** na caixa de texto **Sufixo da URL da API Web**.
3. Clique na caixa **Produtos (opcional)** e escolha **Inicial**.
4. Clique em **Salvar** para importar a API.

Depois que a API é importada, a página de resumo para a API é exibida no portal do editor.

![Resumo da API][api-management-imported-api-summary]

A seção API tem várias guias. A guia **Resumo** exibe as métricas básicas e as informações sobre a API. A guia [Configurações](api-management-howto-create-apis.md#configure-api-settings) é usada para exibir e editar a configuração de uma API. A guia [Operações](api-management-howto-add-operations.md) é usada para gerenciar as operações da API. A guia **Segurança** pode ser usada para configurar a autenticação de proxy para o servidor de back-end usando a autenticação Básica ou [autenticação mútua de certificados](api-management-howto-mutual-certificates.md), bem como para configurar a [autorização do usuário usando OAuth 2.0](api-management-howto-oauth2.md). A guia **Problemas** é usada para exibir os problemas relatados pelos desenvolvedores usando suas APIs e a guia **Produtos** é usada para configurar os produtos que contêm essa API.

Por padrão, cada instância de Gerenciamento de API é fornecida com dois produtos função Web:

-	**Inicial**
-	**Ilimitado**

Neste tutorial, a API Calculadora Básica foi adicionada ao produto Inicial quando a API foi importada.

Para fazer chamadas para uma API, os desenvolvedores precisam primeiro assinar um produto que concede acesso a ela. Os desenvolvedores podem assinar os produtos no Portal do Desenvolvedor ou os administradores podem inscrever os desenvolvedores nos produtos no portal do editor. Você é o administrador porque criou a instância de Gerenciamento de API nas etapas anteriores do tutorial, por isso já se inscreveu em todos os produtos por padrão.

## <a name="call-operation"> </a>Chamar uma operação no Portal do Desenvolvedor

As operações podem ser chamadas diretamente do portal do desenvolvedor, que fornece uma forma conveniente de exibir e testar as operações de uma API. Nesta etapa do tutorial, você chamará a operação **Adicionar dois inteiros** da API da **Calculadora Básica**. Clique em **Portal do Desenvolvedor** no menu da parte superior direita do portal do editor.

![Portal do desenvolvedor][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e depois clique em **Calculadora Básica** para ver as operações disponíveis.

![Portal do desenvolvedor][api-management-developer-portal-calc-api]

Observe as descrições e parâmetros de exemplo que foram importados juntamente com a API e operações, fornecendo documentação para os desenvolvedores que usarão esta operação. Essas descrições também podem ser adicionadas quando as operações são adicionadas manualmente.

Para chamar a operação **Adicionar dois inteiros**, clique em **Experimente**.

![Experimente][api-management-developer-portal-calc-api-console]

Você pode inserir alguns valores para os parâmetros ou manter os padrões, depois clique em **Enviar**.

![HTTP Get][api-management-invoke-get]

Após invocar uma operação, o portal do desenvolvedor exibe o **Status de resposta**, os **Cabeçalhos de resposta** e o **Conteúdo de resposta**.

![Resposta][api-management-invoke-get-response]

## <a name="view-analytics"> </a>Exibir análise

Para exibir a análise da **Calculadora Básica**, alterne para o portal do editor selecionando **Gerenciar** no menu na parte superior direita do portal do desenvolvedor.

![Gerenciar][api-management-manage-menu]

A exibição padrão do portal do editor é o **Painel**, que fornece uma visão geral da instância de Gerenciamento de API.

![Painel][api-management-dashboard]

Passe o mouse sobre o gráfico da **Calculadora Básica** para ver as métricas específicas de utilização da API durante um período de tempo específico.

>Se você não vir linhas no gráfico, volte ao portal do desenvolvedor e faça algumas chamadas à API, aguarde alguns momentos e volte para o painel.

![Análise][api-management-mouse-over]

Clique em **Exibir detalhes** para exibir a página de resumo da API, incluindo uma versão maior das métricas exibidas.

![Resumo][api-management-api-summary-metrics]

Para obter métricas e relatórios detalhados, clique em **Análise** no menu **Gerenciamento de API** à esquerda.

![Visão geral][api-management-analytics-overview]

A seção **Análise** possui as quatro guias a seguir.

-	**Em um relance** fornece a utilização e as métricas de integridade gerais, bem como desenvolvedores, APIs e operações principais.
-	**Uso** fornece uma visão mais detalhada das chamadas à API e da largura de banda, incluindo uma representação geográfica.
-	**Integridade** abrange os códigos de status, taxas de êxito do cache, tempos de resposta e tempos de resposta de serviço e da API.
-	**Atividade** fornece os relatórios que fazer o detalhamento até a atividade específica por desenvolvedor, produto, API e operação.

## <a name="next-steps"> </a>Próximas etapas

-	Verifique os outros tópicos no tutorial [Introdução à configuração avançada de API][].

[Avaliação gratuita do Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Configurar Notificações]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[Introdução à configuração avançada de API]: api-management-get-started-advanced.md
[Preços de Gerenciamento de API]: http://azure.microsoft.com/pricing/details/api-management/

[Portal de Gerenciamento]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png
 

<!---HONumber=July15_HO3-->