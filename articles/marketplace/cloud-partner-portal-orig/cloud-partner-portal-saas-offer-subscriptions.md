---
title: SaaS – venda de SaaS por meio do Azure | Microsoft Docs
description: Descreve o modelo de cobrança de assinaturas para aplicativos SaaS e como implementá-lo.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: f193138fbc5e779c3a6671757320d8918e08db46
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805155"
---
<a name="saas---sell-through-azure"></a>SaaS – venda por meio do Azure
=========================

Este artigo descreve o modelo de cobrança de assinaturas para aplicativos SaaS e como implementá-lo no Portal do Microsoft Cloud Partner.


<a name="overview"></a>Visão geral
--------

O Azure Marketplace permite publicar e gerar receita com suas ofertas de SaaS através do próprio Azure Marketplace. Os clientes agora podem ser cobrados diretamente pela cobrança do Azure e receberão apenas uma fatura do Azure de todos os recursos e serviços que ativaram. Para publicação, as assinaturas SaaS têm os seguintes benefícios:

-   **Experiência de publicação consistente** – se você já publica ofertas de SaaS ou qualquer outra oferta no Azure Marketplace, a experiência de publicação será realizada pelo mesmo portal de publicação.
-   **Nova vitrine para descoberta**: todas as ofertas publicadas ficarão visíveis na vitrine externa do Azure Marketplace e também na extensão do Azure Marketplace dentro do portal do Azure.
-   **Cobrança integrada** – agora você pode vender em todas as regiões onde o Azure é comercializado e permitir que o Azure trate da cobrança para você.
-   **Geração de leads integrada** – agora você pode receber automaticamente leads do Azure em seu CRM preferido sempre que um usuário do Azure assinar seu serviço SaaS usando o Azure Marketplace.
-   **Realize vendas conjuntas com vendedores da Microsoft** – Habilite-se para compartilhamento de leads bidirecional, listas de catálogos prioritários e a oportunidade de interagir e fechar transações conjuntas lado a lado com vendedores da Microsoft.

<a name="billing-models"></a>Modelos de cobrança
--------------

O único modelo de cobrança atualmente suportado é um valor mensal fixo por assinatura de seu serviço SaaS.

**Observação:** modelos de cobrança adicionais podem estar disponíveis no futuro.

Cada oferta de SaaS pode ter um ou mais planos (por exemplo, Básico ou Premium). Cada plano tem alguns metadados básicos associados a ele, juntamente com o preço associado ao plano.

Você tem controle total sobre a definição de um plano. Por exemplo, de que é formado um plano Básico? É você que define o plano e pode fornecer o texto necessário para descrevê-lo como parte da publicação do plano.

O preço associado ao plano é um valor mensal fixo que os usuários do Azure pagam para usar o serviço.

### <a name="what-is-not-supported-today"></a>O que não tem suporte atualmente?

-   Cobrança com base em unidades personalizadas – por exemplo, um preço definido com base no número de solicitações.
-   Cobrança com base na alocação de licenças – por exemplo, permitir que os usuários do Azure comprem licenças com base no número de usuários.

<a name="end-to-end-flow"></a>Fluxo de ponta a ponta
---------------

A oferta da assinatura SaaS parte de uma perspectiva que coloca o usuário final em primeiro lugar

**Observação:** essa descrição de fluxo pressupõe que você tenha publicado sua oferta de SaaS no Azure Marketplace chamado 'SaaS de demonstração da Contoso'.

![Fluxo de usuário de assinatura SaaS](media/saas-offer-subscription/saas-subscription-user-flow.png)

Um usuário do Azure pode ter as seguintes interações com seu serviço SaaS:

-   Descubra seu serviço SaaS no Azure Marketplace
-   Inscrever-se no serviço SaaS no Azure
-   Navegue para o serviço SaaS a partir do portal do Azure
-   Criar uma conta no serviço SaaS e gerenciar (alterar plano/excluir) a conta SaaS
-   Cancelar a inscrição do serviço SaaS a partir do portal do Azure

<a name="discover-your-saas-service-in-azure-marketplace"></a>Descubra seu serviço SaaS no Azure Marketplace
-----------------------------------------------

Quando os usuários iniciam o Azure Marketplace no portal do Azure, eles verão uma categoria chamada 'SaaS (software como serviço)'.

![Descobrir o SaaS usando o menu de categoria](media/saas-offer-subscription/saas-category-menu.png)

Você também pode pesquisar o serviço SaaS.

![Descobrir o SaaS usando a pesquisa](media/saas-offer-subscription/saas-cpp-search.png)

Os usuários podem visualizar os detalhes do serviço e, em seguida, clicar em **Criar**.

![Como criar uma assinatura SaaS](media/saas-offer-subscription/saas-create-subscription.png)

### <a name="subscribe-to-saas-service-in-azure"></a>Inscrever-se no serviço SaaS no Azure

O usuário pode se inscrever no serviço SaaS a partir do Azure.

![Como inscrever-se em um serviço SaaS](media/saas-offer-subscription/saas-subscribe-signup.png)

Quando o usuário assina seu serviço SaaS, ele fornece as seguintes informações

-   Nome – o nome que os usuários podem descobrir ou gerenciar nessa instância de assinatura SaaS no Azure.
-   Assinatura – o contexto da assinatura no qual eles querem associar a cobrança para o serviço SaaS.
-   Plano – o plano do serviço SaaS no qual eles desejam se inscrever.

O documento de Termos de Uso fornecido como parte da publicação da oferta também é exibido ao usuário antes que ele assine o serviço SaaS.

O usuário já pode assinar o serviço clicando em **Assinar**.
O Azure enviará uma notificação no portal após a conclusão da assinatura.

### <a name="navigate-to-the-saas-service-from-azure-portal"></a>Navegar para o serviço SaaS a partir do portal do Azure

Em seguida, os usuários clicam em **Ir para o recurso** para exibir ou gerenciar as instâncias de suas assinatura SaaS.

![Exibir ou gerenciar sua instância SaaS](media/saas-offer-subscription/saas-go-to-resource.png)

O usuário é avisado que deve primeiro configurar a conta no serviço SaaS. A cobrança será iniciada depois que o serviço SaaS avisar o Azure que a cobrança deverá ser iniciada, que é quando o usuário cria uma conta no site do serviço SaaS do Azure.

![Configurar sua instância SaaS](media/saas-offer-subscription/saas-configure-account.png)

Quando o usuário clica em **Configurar Conta**, ele é redirecionados para o ponto de extremidade do serviço SaaS. Durante esse redirecionamento, um token é passado como um parâmetro de consulta. Por exemplo: 

![Token de conta SaaS](media/saas-offer-subscription/saas-account-token.png)

Observe esse valor do token. Esse token é de curta duração e precisa ser resolvido para obter um identificador de assinatura no Azure.

<a name="creating-a-saas-offer-subscription"></a>Como criar uma assinatura de Oferta de SaaS
----------------------------------

Para criar essa experiência, existem dois trabalhos que são necessários:

-   Conecte seu site do serviço SaaS usando as APIs SaaS da Microsoft. O documento [SaaS – Venda por meio do Azure – APIs](./cloud-partner-portal-saas-subscription-apis.md) explica como criar essa conexão.  
-   Habilite **Vender por meio do Azure** no Portal do Cloud Partner na seção **Informações Técnicas** e forneça todos os detalhes de configuração.

![Informações técnicas sobre a nova oferta de SaaS](media/cpp-creating-saas-offers/saas-new-offer-technical-info-2.png)

A seguir, veja uma explicação sobre todos os campos obrigatórios para a seção **Informações técnicas**:

|  **Campos de oferta**                 |  **Descrição**                                   |
|  ----------------                 |  -------------------------------------             |
| IDs de assinatura para versão prévia          | Todos os identificadores de Assinatura do Azure usados para testar sua oferta na visualização antes que ela fique disponível publicamente. |
| Instruções de introdução      | Orientações para compartilhar com seus clientes para ajudá-los a se conectar ao aplicativo SaaS. O uso de HTML básico é permitido, além das marcas `<p>`, `<h1>`, `<li>` etc.  |
| URL da página de aterrissagem                  | A URL do site para o qual você direcionará seus clientes após a aquisição no portal do Azure. Essa URL também será o ponto de extremidade que receberá as APIs de conexão para facilitar o comércio com a Microsoft.  |
| Webhook de conexão                | Para todos os eventos assíncronos que a Microsoft precisa enviar para você em nome do cliente (por exemplo: a Assinatura do Azure foi invalidada), solicitamos que você nos forneça uma conexão de webhook. Se você ainda não tiver um sistema de webhooks em vigor, a configuração mais simples é definir um Aplicativo de lógica de ponto de extremidade HTTP que atenderá todos os eventos que são postados nele e, em seguida, tratá-os adequadamente.  Para saber mais, confira [Chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTP em aplicativos lógicos](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint). |
| ID de locatário do Azure AD e ID do aplicativo     | No portal do Azure, exigimos que você crie um Aplicativo do Active Directory para que possamos validar que a conexão entre nossos dois serviços está por trás de uma comunicação autenticada. Para esses campos, crie um Aplicativo do AD e cole a ID do locatário e a ID do aplicativo correspondentes necessárias. |
|  |  |


Por fim, se você escolher **Venda por meio do Azure**, há uma seção adicional chamada **Planos**. Os planos são necessários apenas se a opção Venda por meio do Azure for marcada. Esta seção lista os planos específicos e os preços correspondentes compatíveis com seu aplicativo SaaS. A partir de hoje, permitimos preços mensais, com a capacidade de permitir um ou três meses de acesso gratuito. Esses planos e preços devem corresponder aos planos e preços exatos que você tem em seu próprio site do aplicativo SaaS.
