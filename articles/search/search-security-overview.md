---
title: "Proteger dados e operações no Azure Search | Microsoft Docs"
description: "A segurança do Azure Search baseia-se em conformidade com SOC 2, criptografia, autenticação e identidade de acesso por meio de identificadores de usuário e de grupo de segurança nos filtros do Azure Search."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 12/14/2017
ms.author: heidist
ms.openlocfilehash: 23616c70a5fd336b743f5acfad2601a6c3e23fc4
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="data-security-and-controlled-access-to-azure-search-operations"></a>Segurança de dados e acesso controlado a operações do Azure Search

O Azure Search é [compatível com SOC 2](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports), com uma arquitetura de segurança abrangente que abarca segurança física, transmissões criptografadas, armazenamento criptografado e garantias de software em toda a plataforma. Operacionalmente, o Azure Search só aceita solicitações autenticadas. Opcionalmente, você pode adicionar controles de acesso por usuário no conteúdo. Este artigo aborda a segurança em cada camada, mas se concentra principalmente na proteção das operações e dos dados no Azure Search.

![Diagrama de bloco das camadas de segurança](media/search-security-overview/azsearch-security-diagram.png)

Enquanto o Azure Search herda as proteções e garantias da plataforma Azure, o principal mecanismo usado pelo próprio serviço é a autenticação baseada em chave, em que o tipo de chave determina o nível de acesso. Uma chave é uma chave de administrador ou uma chave de consulta para acesso somente leitura.

O acesso ao seu serviço baseia-se em uma interseção de permissões concedidas pela chave (completa ou somente leitura), além de um contexto que defina o escopo de operações. Cada solicitação é composta de uma chave obrigatória, uma operação e um objeto. Quando unidas, os dois níveis de permissão e o contexto são suficientes para fornecer segurança completa nas operações de serviço. 

## <a name="physical-security"></a>Segurança física

Os data centers da Microsoft fornecem segurança física líder no setor e são compatíveis com um portfólio abrangente de padrões e regulamentos. Para saber mais, acesse a página [Data centers globais](https://www.microsoft.com/cloud-platform/global-datacenters) ou assista a um breve vídeo sobre segurança de data center.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]

## <a name="encrypted-transmission-and-storage"></a>Armazenamento e transmissão criptografados

O Azure Search escuta na porta HTTPS 443. Na plataforma, as conexões com os serviços do Azure são criptografadas. 

No armazenamento de back-end usado para índices e outros constructos, o Azure Search aproveita os recursos de criptografia dessas plataformas. A conformidade total com [AICPA SOC 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) está disponível para todos os serviços (novos e existentes) de pesquisa em todos os data centers que oferecem o Azure Search. Para examinar o relatório completo, acesse [Azure - e relatório do Azure Governamental SOC 2 Tipo II](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports).

A criptografia é transparente, com chaves de criptografia gerenciadas internamente e universalmente aplicadas. Não é possível desligá-la para serviços de pesquisa ou índices específicos, nem gerenciar chaves diretamente, nem fornecer sua própria chave. 

## <a name="azure-wide-logical-security"></a>Segurança lógica por todo o Azure

Vários mecanismos de segurança estão disponíveis no Azure Stack e, portanto, estão automaticamente disponíveis para os recursos do Azure Search que você criar.

+ [Bloqueios no nível de recurso ou da assinatura para impedir a exclusão](../azure-resource-manager/resource-group-lock-resources.md)
+ [RBAC (controle de acesso baseado em função) para controlar o acesso a informações e operações administrativas](../active-directory/role-based-access-control-what-is.md)

Todos os serviços do Azure oferecem suporte a RBAC para definir níveis de acesso consistentes em todos os serviços. Por exemplo, a exibição de dados confidenciais, como a chave do administrador, é restrita às funções de Proprietário e Colaborador, enquanto a exibição do status do serviço fica disponível para os membros de qualquer função. O RBAC fornece funções de Leitor, Colaborador e Proprietário. Por padrão, todos os administradores de serviço são membros da função Proprietário.

## <a name="service-authentication"></a>Autenticação do serviço

O Azure Search fornece sua própria metodologia de autenticação. A autenticação ocorre em cada solicitação e é baseada em uma chave de acesso que determina o escopo das operações. Uma chave de acesso válida é considerada prova de que a solicitação se origina de uma entidade confiável. 

A autenticação por serviço existe em dois níveis: direitos totais e somente consulta. O tipo de chave determina qual nível de acesso está em vigor.

|Chave|DESCRIÇÃO|limites|  
|---------|-----------------|------------|  
|Administrador|Concede direitos totais para todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir **índices**, **indexadores** e **fontes de dados**.<br /><br /> Duas **chaves de API** de administrador, chamadas de chaves *primária* e *secundária* no portal, são geradas quando o serviço é criado e podem ser regeneradas individualmente sob demanda. Ter duas chaves permite que você substitua uma chave enquanto usa a segunda chave de acesso contínuo para o serviço.<br /><br /> As chaves de administrador são especificadas somente nos cabeçalhos de solicitação HTTP. Você não pode colocar uma **chave de API** de administrador em uma URL.|Máximo de dois por serviço|  
|Consultar|Concede acesso somente leitura a índices e documentos e normalmente são distribuídas para aplicativos cliente que emitem solicitações de pesquisa.<br /><br /> As chaves de consulta são criadas sob demanda. Você pode criá-las manualmente no portal ou programaticamente por meio da [API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> As chaves de consulta podem ser especificadas em um cabeçalho de solicitação HTTP para pesquisa, sugestões ou operação de pesquisa. Como alternativa, você pode passar uma chave de consulta como um parâmetro em uma URL. Dependendo de como seu aplicativo cliente formula a solicitação, pode ser mais fácil passar a chave como um parâmetro de consulta:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01&api-key=A8DA81E03F809FE166ADDB183E9ED84D`|50 por serviço|  

 Visualmente, não há nenhuma distinção entre as chaves de administrador ou de consulta. As duas chaves são cadeias de caracteres compostas de 32 caracteres alfanuméricos gerados aleatoriamente. Se você perder o controle de qual tipo de chave está especificado em seu aplicativo, poderá [verificar os valores de chave no portal](https://portal.azure.com) ou usar a [API REST](https://docs.microsoft.com/rest/api/searchmanagement/) para retornar o valor e o tipo de chave.  

> [!NOTE]  
>  Passar dados confidenciais, como um `api-key`, no URI de solicitação, é considerado uma prática de segurança inadequada. Por esse motivo, o Azure Search só aceita uma chave de consulta como uma `api-key` na cadeia de consulta, e você deve evitar fazer isso, a menos que o conteúdo do índice precise estar publicamente disponível. Em vez disso, recomendamos passar a `api-key` como um cabeçalho de solicitação.  

### <a name="how-to-find-the-access-keys-for-your-service"></a>Como localizar as chaves de acesso para o serviço

Você pode obter chaves de acesso no portal ou por meio da [API REST de Gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/). Para saber mais, confira [Gerenciar chaves](search-manage.md#manage-api-keys).

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Liste os [serviços de pesquisa](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) para sua assinatura.
3. Selecione o serviço e, na página de serviço, localize **Configurações** >**Chaves** para exibir as chaves de administrador e de consulta.

![Página do portal, Configurações, seção Chaves](media/search-security-overview/settings-keys.png)

## <a name="index-access"></a>Acesso de índice

No Azure Search, um índice individual não é um objeto que possa ser protegido. Em vez disso, o acesso a um índice é determinado na camada de serviço (acesso de leitura ou gravação), junto com o contexto de uma operação.

No caso do acesso do usuário final, você pode estruturar solicitações de consulta em seu aplicativo para se conectar usando uma chave de consulta, o que torna todas as solicitações somente leitura, e incluir o índice específico usado pelo seu aplicativo. Em uma solicitação de consulta, não há o conceito de adicionar índices ou acessar vários índices simultaneamente. Portanto, todas as solicitações têm um único índice de destino por definição. Como tal, a estrutura da solicitação de consulta em si (uma chave e um índice de destino único) define o limite de segurança.

O acesso de administrador e de desenvolvedor aos índices não é diferenciado: ambos precisam de acesso de gravação para criar, excluir e atualizar os objetos gerenciados pelo serviço. Qualquer pessoa com uma chave de administrador para seu serviço pode ler, modificar ou excluir um índice no mesmo serviço. Para proteção contra exclusão de índices acidental ou mal-intencionada, o controle do código-fonte interno para ativos de código é a solução para reverter uma modificação ou exclusão de índice indesejada. O Azure Search tem failover dentro do cluster para garantir a disponibilidade, mas ele não armazena nem executa seu código proprietário usado para criar ou carregar índices.

Para soluções de multilocação que exigem limites de segurança no nível do índice, essas soluções normalmente incluem uma camada intermediária que os clientes usam para lidar com isolamento de índice. Para saber mais sobre o caso de uso de vários locatários, confira [Criar padrões para aplicativos SaaS multilocatários e para o Azure Search](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access-from-client-apps"></a>Acesso de administrador de aplicativos cliente

A API REST de Gerenciamento do Azure Search é uma extensão do Azure Resource Manager e compartilha suas dependências. Assim, o Active Directory é um pré-requisito para a administração do serviço de Azure Search. Todas as solicitações administrativas do código do cliente devem ser autenticadas usando o Azure Active Directory antes que a solicitação atinja o Gerenciador de Recursos.

As solicitações de dados em relação ao ponto de extremidade de serviço Azure Search, como Criar Índice (API REST do serviço Azure Search) ou Pesquisar Documentos (API REST do serviço Azure Search), usam uma chave de API no cabeçalho da solicitação.

Se o código do aplicativo manipula as operações de administração de serviço e as operações de dados em índices de pesquisa ou documentos, implemente dois métodos de autenticação no seu código: a chave de acesso nativa para o Azure Search e a metodologia de autenticação do Active Directory exigida pelo Gerenciador de Recursos. 

Para saber mais sobre como estruturar uma solicitação no Azure Search, confira [REST do serviço Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Para saber mais sobre requisitos de autenticação para o Gerenciador de Recursos, confira [Usar a API de autenticação do Gerenciador de Recursos para acessar assinaturas](../azure-resource-manager/resource-manager-api-authentication.md).

## <a name="user-access-to-index-content"></a>Acesso de usuário ao conteúdo do índice

O acesso por usuário ao conteúdo de um índice é implementado por meio de filtros de segurança em suas consultas, retornando os documentos associados com uma identidade de segurança. Em vez de funções predefinidas e atribuições de função, o controle de acesso baseado em identidade é implementado como um filtro que corta os resultados da pesquisa de documentos e conteúdo com base nas identidades. A tabela a seguir descreve duas abordagens para cortar resultados da pesquisa com conteúdo não autorizado.

| Abordagem | DESCRIÇÃO |
|----------|-------------|
|[Filtragem de segurança com base nos filtros de identidade](search-security-trimming-for-azure-search.md)  | Documenta o fluxo de trabalho básico para implementar o controle de acesso de identidade do usuário. Ele aborda a adição de identificadores de segurança a um índice e explica a filtragem em relação a esse campo para cortar resultados de conteúdo proibido. |
|[Filtragem de segurança com base em Identidades do Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Este artigo aprofunda o artigo anterior, fornecendo etapas para recuperar identidades do Azure Active Directory (AAD), um dos [serviços gratuitos](https://azure.microsoft.com/free/) na plataforma de nuvem do Azure. |

## <a name="table-permissioned-operations"></a>Tabela: operações permitidas

A tabela a seguir resume as operações permitidas no Azure Search e qual chave desbloqueia o acesso a uma determinada operação.

| Operação | Permissões |
|-----------|-------------------------|
| Criar um serviço | Titular da assinatura do Azure|
| Dimensionar um serviço | Chave de administrador, Proprietário ou Colaborador RBAC no recurso  |
| Excluir um serviço | Chave de administrador, Proprietário ou Colaborador RBAC no recurso |
| Criar, modificar e excluir objetos no serviço: <br>Índices e partes do componente (incluindo definições do analisador, perfis de pontuação, opções de CORS), indexadores, fontes de dados, sinônimos, sugestores. | Chave de administrador, Proprietário ou Colaborador RBAC no recurso  |
| Consultar um índice | Chave de consulta ou de administrador (RBAC não aplicável) |
| Informações do sistema de consulta como: retornar estatísticas, contagens e listas de objetos. | Chave de administração, RBAC no recurso (Proprietário, Colaborador, Leitor) |
| Gerenciar chaves de administrador | Chave de administração, Proprietário ou Colaborador RBAC no recurso. |
| Gerenciar chaves de consulta |  Chave de Administrador, Proprietário ou Colaborador de RBAC no recurso. O Leitor de RBAC pode exibir as chaves de consulta. |


## <a name="see-also"></a>Consulte também

+ [Introdução ao .NET (demonstra o uso de uma chave de administrador para criar um índice)](search-create-index-dotnet.md)
+ [Introdução ao REST (demonstra o uso de uma chave de administrador para criar um índice)](search-create-index-rest-api.md)
+ [Controle de acesso baseado na identidade usando filtros do Azure Search](search-security-trimming-for-azure-search.md)
+ [Controle de acesso baseado na identidade do Active Directory usando filtros do Azure Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtros no Azure Search](search-filters.md)