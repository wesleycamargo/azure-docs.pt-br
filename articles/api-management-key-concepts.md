<properties 
	pageTitle="Principais conceitos de Gerenciamento de API" 
	description="Saiba mais sobre APIs, produtos, funções, grupos e outros conceitos principais do Gerenciamento de API." 
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
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

#Principais conceitos de Gerenciamento de API

O Gerenciamento de API ajuda as organizações a publicar APIs para parceiros externos e desenvolvedores internos a fim de desbloquear o potencial de seus dados e serviços. Empresas em todos os lugares estão procurando estender suas operações para uma plataforma digital, criando novos canais, encontrando novos clientes e estimulando uma interação mais profunda com os clientes já existentes. O Gerenciamento de API fornece as competências essenciais para garantir um programa de API de sucesso através do envolvimento do desenvolvedor, ideias de negócios, análises, segurança e proteção.

Para usar o Gerenciamento de API, os administradores criaram as APIs. Cada API consiste em uma ou mais operações e cada uma pode ser adicionada a um ou mais produtos. Para usar uma API, os desenvolvedores assinam um produto que contém essa API e, em seguida, eles podem chamar a operação da API, estando sujeitos a quaisquer políticas que possam estar em vigor.

Esse tópico fornece uma visão geral dos principais conceitos de Gerenciamento de API.

## Neste tópico

-   [APIs e operações][]
-   [Produtos][]
-   [Grupos][]
-   [Desenvolvedores][]
-   [Políticas][]
-	[Portal do desenvolvedor][]


## <a name="apis"> </a>APIs e operações

As APIs são a fundação de uma instância de serviço de Gerenciamento de API. Cada API representa um conjunto de operações disponíveis para desenvolvedores. Cada API contém uma referência para serviço back-end que implementa a API, e suas operações são mapeadas para as operações implementadas pelo serviço de back-end. As operações no Gerenciamento de API são altamente configuráveis, com controle sobre o mapeamento de URL, parâmetros de consulta e caminho, conteúdo de solicitação e resposta e caching de resposta de operação. As políticas de limite de taxa, cotas e restrições de IP podem também ser implementadas no nível de operação individual ou da API.

Para obter mais informações, consulte [Como criar APIs][] e [Como adicionar operações a uma API][].


## <a name="products"> </a> Produtos

Os produtos são como as APIs são exibidas para os desenvolvedores. Os produtos no Gerenciamento de API têm uma ou mais APIs e são configurados com título, descrição e termos de uso. Quando um produto fica pronto para uso pelo desenvolvedor ele pode ser publicado. Quando ele é publicado, pode ser visualizado e assinado pelos desenvolvedores. A aprovação de assinatura é configurada no nível do produto e pode requere a aprovação do administrador ou ser aprovada automaticamente.

Os grupos são usados para gerenciar a visibilidade dos produtos para os desenvolvedores. Os produtos dão visibilidade aos grupos e os desenvolvedores podem exibir e assinar os produtos visíveis para os grupos aos quais pertencem. 

Para obter mais informações, consulte [Como criar e publicar um produto][].

## <a name="groups"> </a> Grupos

Os grupos são usados para gerenciar a visibilidade dos produtos para os desenvolvedores. O gerenciamento de API tem os seguintes grupos internos.

-	**Administradores** - Os administradores gerenciam instâncias de serviço de Gerenciamento de API, criando as APIs, operações e produtos que são usados pelos desenvolvedores.
-	**Desenvolvedores** - Desenvolvedores são clientes que compilam aplicativos usando suas APIs. Os desenvolvedores têm acesso ao [portal do desenvolvedor][] e criam aplicativos que chamam as operações de uma API.
-	**Convidados** - Os usuários não autenticados, tais como potenciais clientes visitando o portal do desenvolvedor de uma instância de Gerenciamento de API pertencem a esse grupo. Eles podem receber certos acessos somente leitura, como a capacidade de exibir APIs, mas não de chamá-las.

Além desses grupos internos, os administradores podem criar grupos personalizados. Os grupos personalizados têm os mesmos privilégios que o grupo de desenvolvedores internos e podem ser usados para gerenciar vários grupos de desenvolvedores. Por exemplo, você pode criar um grupo personalizado para desenvolvedores que usará as APIs de um produto e um outro grupo de desenvolvedores que usará as APIs de um produto diferente.

Para obter mais informações, consulte [Como criar e utilizar grupos][].

## <a name="developers"> </a> Desenvolvedores

Os desenvolvedores representam as contas de usuários em uma instância de serviço de Gerenciamento de API. Os desenvolvedores podem ser criados ou convidados para se juntar aos administradores ou podem fazer a inscrição no [Portal do desenvolvedor][]. Cada desenvolvedor é um membro de um ou mais grupos e pode assinar os produtos que tornam visíveis esses grupos.

Quando os desenvolvedores assinam um produto, recebem as chaves principal e secundária para esse produto. Essa chave é usada quando eles fazem chamadas às APIs dos produtos.

Para obter mais informações, consulte [Como criar ou convidar desenvolvedores][] e [Como associar grupos aos desenvolvedores][].

## <a name="policies"> </a> Políticas

As políticas são um poderoso recurso de Gerenciamento de API que permite ao editor alterar o comportamento da API através da configuração. As políticas são um conjunto de instruções executadas em sequência, na solicitação ou na resposta de uma API. Instruções populares incluem a conversão do formato de XML para JSON e limite de taxa de chamada para restringir a quantidade de chamadas recebidas de um desenvolvedor, além de várias outras políticas disponíveis.

Para obter uma lista completa de políticas de Gerenciamento de API, consulte [Referência de política][]. Para obter mais informações sobre como usar e configurar políticas, consulte [Políticas de Gerenciamento de API][]. Para obter um tutorial sobre como criar um produto com políticas de cota e limite de taxa, consulte [Como criar e definir configurações avançadas de produto][].


## <a name="developer-portal"> </a> Portal do desenvolvedor

O portal do desenvolvedor é onde os desenvolvedores podem aprender sobre suas operações de APIs, visualização e atendimento e assinar produtos. Clientes potenciais podem visitar o portal do desenvolvedor, exibir APIs e operações e fazer inscrição. A URL para o seu portal do desenvolvedor está localizada no painel no portal do Azure da sua instância de serviço de Gerenciamento de API.

Você pode personalizar a aparência do portal do desenvolvedor adicionando conteúdo personalizado, personalização de estilo e adicionando sua marca.

[APIs e operações]: #apis
[Produtos]: #products
[Grupos]: #groups
[Desenvolvedores]: #developers
[Políticas]: #policies
[Portal do desenvolvedor]: #developer-portal

[Como criar APIs]: ../api-management-howto-create-apis
[Como adicionar operações a uma API]: ../api-management-howto-add-operations
[Como criar e publicar um produto]: ../api-management-howto-add-products
[Como criar e utilizar grupos]: ../api-management-howto-create-groups
[Como associar grupos a desenvolvedores]: ../api-management-howto-create-groups/#associate-group-developer
[Como criar e definir configurações avançadas de produto]: ../api-management-howto-product-with-rules
[Como criar ou convidar desenvolvedores]: ../api-management-howto-create-or-invite-developers
[Referência de política]: ../api-management-policy-reference
[Políticas de Gerenciamento de API]: ../api-management-howto-policies
[Criar uma instância do serviço de Gerenciamento de API]: ../api-management-get-started/#create-service-instance




<!--HONumber=35.2-->

<!--HONumber=46--> 
