<properties
   pageTitle="Graph API do Active Directory do Azure | Microsoft Azure"
   description="Uma visão geral e guia de início rápido para a Graph API que permite acesso programático ao Azure AD por meio de pontos de extremidade da API REST."
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/25/2016"
   ms.author="mbaldwin" />

# Graph API do Active Directory do Azure

> [AZURE.IMPORTANT] A funcionalidade da Graph API do AD do Azure também está disponível por meio do [Microsoft Graph](https://graph.microsoft.io/), uma API unificada que inclui APIs de outros serviços da Microsoft, como o Outlook, OneDrive, OneNote, Planner e Office Graph, e está acessível por meio de um único ponto de extremidade e com um token de acesso único.

A Graph API do Active Directory do Azure fornece acesso programático ao AD do Azure por meio de pontos de extremidade da API REST. Os aplicativos podem usar a Graph API para realizar operações de criação, leitura, atualização e exclusão (CRUD) nos objetos e dados do diretório. Por exemplo, a Graph API dá suporte às seguintes operações comuns para um objeto de usuário:

- Criar um novo usuário em um diretório

- Obter propriedades detalhadas do usuário, como seus grupos

- Atualizar as propriedades do usuário, como sua localização e o número de telefone, ou alterar sua senha

- Verificar a associação de grupo do usuário para acesso baseado em função

- Desabilitar uma conta de usuário ou excluí-la totalmente

Além de objetos de usuário, você pode executar operações semelhantes em outros objetos, como grupos e aplicativos. Para chamar a Graph API em um diretório, o aplicativo deve ser registrado com o AD do Azure e estar configurado para permitir acesso ao diretório. Normalmente, isso é obtido por meio de um fluxo de consentimento do usuário ou administrador.

Para começar a usar a API do Azure Active Directory Graph, veja o [Guia de início rápido da Graph API](active-directory-graph-api-quickstart.md) ou a [documentação de referência da Graph API interativa](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).


## Recursos

A Graph API fornece os seguintes recursos:

- **Pontos de extremidade da API REST**: a Graph API é um serviço RESTful composto por pontos de extremidade acessados por meio de solicitações HTTP padrão. A Graph API dá suporte a tipos de conteúdo XML ou JavaScript Object Notation (JSON) para solicitações e respostas. Para saber mais, consulte [Referência da Graph API REST do AD do Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- **Autenticação com o AD do Azure**: cada solicitação à Graph API deve ser autenticada com o anexo de um JSON Web Token (JWT) no cabeçalho de Autorização da solicitação. Esse token é adquirido ao fazer uma solicitação ao ponto de extremidade do AD do Azure e fornecer credenciais válidas. Você pode usar o fluxo de credenciais de cliente OAuth 2.0 ou o fluxo de concessão do código de autorização para adquirir um token para chamar o Graph. Para obter mais informações, veja [OAuth 2.0 no AD do Azure](https://msdn.microsoft.com/library/azure/dn645545.aspx).

- **Autorização baseada em função (RBAC)**: grupos de segurança são usados para executar a RBAC na Graph API. Por exemplo, se você quiser determinar se um usuário tem acesso a um recurso específico, o aplicativo pode chamar a operação [Verificar associação de grupo (transitiva)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive) que retorna verdadeiro ou falso.

- **Consulta diferencial**: se quiser verificar alterações em um diretório em dois períodos de tempo sem precisar fazer consultas frequentes à Graph API, você pode fazer uma solicitação de consulta diferencial. Este tipo de solicitação retornará apenas as alterações feitas entre a solicitação de consulta diferencial anterior e a solicitação atual. Para saber mais, leia [Consulta diferencial da Graph API do AD do Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).

- **Extensões de diretório**: se estiver desenvolvendo um aplicativo que precisa ler ou gravar propriedades exclusivas para objetos de diretório, você pode registrar e usar valores de extensão usando a Graph API. Por exemplo, se seu aplicativo requer uma propriedade Skype ID para cada usuário, você pode registrar a nova propriedade no diretório e ela estará disponível para todos os objetos de usuário. Para obter mais informações, veja [Extensões de esquema do diretório da Graph API do AD do Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).

## Cenários

A Graph API habilita muitos cenários de aplicativos. A seguir, os cenários mais comuns:

- **Aplicativo de linha de negócios (único locatário)**: neste cenário, um desenvolvedor corporativo trabalha para uma organização que tem uma assinatura do Office 365. O desenvolvedor está criando um aplicativo Web que interage com o Azure AD para executar tarefas como a atribuição de uma licença para um usuário. Esta tarefa exige acesso à Graph API para que o desenvolvedor registre o aplicativo de único locatário no Azure AD e configure as permissões de leitura e gravação para a Graph API. Em seguida, o aplicativo é configurado para usar suas próprias credenciais ou aquelas do usuário conectado no momento para adquirir um token para chamar a Graph API.

- **Aplicativo de software como serviço (multilocatário)**: neste cenário, um fornecedor de software independente (ISV) está desenvolvendo um aplicativo Web multilocatário hospedado que fornece recursos de gerenciamento de usuário para outras organizações que usam o AD do Azure. Esses recursos exigem acesso a objetos de diretório, por isso o aplicativo precisa chamar a Graph API. O desenvolvedor registra o aplicativo no Azure AD, configura para exigir as permissões de leitura e gravação para a Graph API e habilita o acesso externo para que outras organizações possam concordar com o uso do aplicativo em seu diretório. Quando um usuário de outra organização autentica o aplicativo pela primeira vez, é mostrada uma caixa de diálogo de consentimento com as permissões que o aplicativo está solicitando. Conceder consentimento dará o aplicativo às permissões solicitadas para a Graph API no diretório do usuário. Para saber mais sobre a estrutura de consentimento, consulte [Visão geral da estrutura de consentimento](active-directory-integrating-applications.md).

## Consulte também

[Guia de início rápido para a Graph API do AD do Azure](active-directory-graph-api-quickstart.md)

[Documentação do REST para a Graph AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md)

<!---HONumber=AcomDC_0302_2016-->