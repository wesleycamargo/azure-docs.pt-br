<properties
pageTitle="Objetos de entidade de serviço e aplicativo do Azure Active Directory | Microsoft Azure"
description="Uma discussão sobre a relação entre objetos de aplicativo e de entidade de serviço no Azure Active Directory"
documentationCenter="dev-center-name"
authors="bryanla"
manager="mbaldwin"
services="active-directory"
editor=""/>

<tags
ms.service="active-directory"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="identity"
ms.date="08/10/2016"
ms.author="bryanla;mbaldwin"/>

# Objetos de entidade de serviço e aplicativo no Azure Active Directory
Quando você lê sobre um "aplicativo" do Azure Active Directory (AD), nem sempre é exatamente claro a que o autor se refere. A meta deste artigo é deixar isso claro, definindo os aspectos conceituais e concretos de integração de aplicativos do Azure AD, seguidos de um exemplo de registro e consentimento para um [aplicativo multilocatário](active-directory-dev-glossary.md#multi-tenant-application).

## Visão geral
Um aplicativo do Azure AD é mais amplo do que apenas um software. É um termo conceitual, que se refere não apenas ao software de aplicativo, mas também a seu registro (também conhecido como: configuração de identidade) no Azure AD, o que permite que ele participe de "conversas" de autenticação e autorização em tempo de execução. Por definição, um aplicativo pode funcionar em uma função de [cliente](active-directory-dev-glossary.md#client-application) (consumindo um recurso), uma função de [servidor de recursos](active-directory-dev-glossary.md#resource-server) (expondo APIs a clientes) ou ambos. O protocolo de conversa é definido por um [fluxo de Concessão de Autorização OAuth 2.0](active-directory-dev-glossary.md#authorization-grant), com o objetivo de permitir que o cliente/recurso acesse/proteja dados de um recurso, respectivamente. Agora vamos nos aprofundar e ver como o modelo de aplicativo do Azure AD representa um aplicativo internamente.

## Registro de aplicativo
Quando você registra um aplicativo no [portal clássico do Azure][AZURE-Classic-Portal], dois objetos são criados no locatário do Azure AD: um objeto de aplicativo e um objeto de entidade de serviço.

#### Objeto de aplicativo
Um aplicativo do Azure AD é *definido* por seu único objeto de aplicativo, que reside no locatário do Azure AD em que o aplicativo foi registrado, sendo conhecido como o locatário "inicial" do aplicativo. O objeto de aplicativo fornece informações relacionadas à identidade de um aplicativo e é o modelo do qual seus objetos de entidade de serviço correspondentes são *derivados* para uso em tempo de execução.

Você pode considerar o aplicativo como a representação *global* de seu aplicativo (para uso em todos os locatários) e a entidade de serviço como a representação *local* (para uso em um locatário específico). A [entidade de Aplicativo][AAD-Graph-App-Entity] do Azure AD Graph define o esquema para um objeto de aplicativo. Portanto, um objeto de aplicativo tem uma relação de um a um com o aplicativo de software e uma relação de um a *n* com seus *n* objetos de entidades de serviço correspondentes.

#### Objeto de entidade de serviço
O objeto de entidade de serviço define a política e as permissões para um aplicativo, fornecendo a base para que uma entidade de segurança represente o aplicativo ao acessar recursos em tempo de execução. A [entidade ServicePrincipal][AAD-Graph-Sp-Entity] do Azure AD Graph define o esquema para um objeto de entidade de serviço.

Um objeto de entidade de serviço é necessário em cada locatário para o qual uma instância do uso do aplicativo deve ser representada, habilitando o acesso seguro aos recursos pertencentes a contas de usuário do locatário. Um aplicativo único locatário terá apenas uma entidade de serviço (em seu locatário doméstico). Um [aplicativo Web](active-directory-dev-glossary.md#web-client) multilocatário também terá uma entidade de serviço em cada locatário em que um administrador ou usuários desse locatário tenham dado consentimento, permitindo que ele acesse seus recursos. Após o consentimento, o objeto de entidade de serviço será consultado para futuras solicitações de autorização.

> [AZURE.NOTE] As alterações feitas no objeto de aplicativo também são refletidas apenas no objeto de entidade de serviço do locatário inicial do aplicativo (o locatário em que ele foi registrado). Para aplicativos multilocatário, as alterações no objeto de aplicativo não serão refletidas em objetos de entidade de serviço de locatários do consumidor até que o locatário do consumidor remova o acesso e o conceda novamente.

## Exemplo
O diagrama a seguir ilustra o relacionamento entre o objeto de aplicativo de um aplicativo e os objetos de entidade de serviço correspondentes, no contexto de um aplicativo multilocatário de exemplo chamado **aplicativo de RH**. Há três locatários do Azure AD nesse cenário:

- **Adatum** - o locatário usado pela empresa que desenvolveu o **aplicativo de HR**
- **Contoso** - locatário utilizado pela empresa Contoso, que é consumidora do **aplicativo de HR**
- **Fabrikam** - o locatário usado pela organização Fabrikam, que também consome o **aplicativo de HR**

![Relação entre um objeto de aplicativo e um objeto de entidade de serviço](./media/active-directory-application-objects/application-objects-relationship.png)

No diagrama acima, a Etapa 1 é o processo de criação do aplicativo e dos objetos de entidade de serviço no locatário inicial do aplicativo.

Na Etapa 2, quando os administradores da Contoso e da Fabrikam concluem o consentimento, um objeto de entidade de serviço é criado no locatário do Azure AD da empresa e recebe as permissões concedidas pelo administrador. Observe também que o aplicativo de RH pode ser configurado/projetado para permitir o consentimento pelos usuários para uso individual.

Na etapa 3, cada um dos locatários do consumidor do aplicativo de RH (Contoso e Fabrikam) tem seu próprio objeto de entidade de serviço. Cada um deles representa o uso de uma instância do aplicativo em tempo de execução, controlado pelas permissões concedidas pelo respectivo administrador.

## Próximas etapas
Um objeto de aplicativo do aplicativo pode ser acessado por meio da API do Graph do Azure AD, conforme representado por sua [entidade de Aplicativo][AAD-Graph-App-Entity] OData

Um objeto de entidade de serviço do aplicativo pode ser acessado por meio da API do Graph do Azure AD, conforme representado por sua [entidade ServicePrincipal][AAD-Graph-Sp-Entity] OData



<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Classic-Portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0810_2016-->