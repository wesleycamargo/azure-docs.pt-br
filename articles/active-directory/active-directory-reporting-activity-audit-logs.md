---
title: Auditoria de relatórios de atividade no portal do Azure Active Directory | Microsoft Docs
description: Introdução à auditoria de relatórios de atividade no portal do Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/31/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 97ea32a1e0f8815accff6201251771ab8c088859
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Relatórios de atividades de auditoria no portal do Azure Active Directory 

Com os relatórios no Azure Active Directory (Azure AD) você obtém todas as informações de que precisa para determinar como seu ambiente está se comportando.

A arquitetura de relatório no Azure AD consiste nos seguintes componentes:

- **Atividade** 
    - **Atividades de entrada** – informações sobre o uso de aplicativos gerenciados e de atividades de entrada do usuário
    - **Logs de auditoria** – informações de atividade do sistema sobre o gerenciamento de usuários e de grupos, sobre os aplicativos gerenciados e sobre as atividades de diretório.
- **Segurança** 
    - **Entradas arriscadas** - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário. Para obter mais detalhes, veja Entradas de risco.
    - **Usuários sinalizados para riscos** - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida. Para obter mais detalhes, consulte Usuários sinalizados para risco.

Este tópico fornece uma visão geral das atividades de auditoria.
 
## <a name="who-can-access-the-data"></a>Quem pode acessar os dados?
* Usuários na função de Administrador de segurança ou Leitor de segurança
* Administradores globais
* Os usuários individuais (não administradores) podem ver suas próprias atividades


## <a name="audit-logs"></a>Logs de auditoria

Os logs de auditoria no Azure Active Directory fornecem registros de atividades do sistema para fins de conformidade.  
O primeiro ponto de entrada para todos os dados de auditoria é **Logs de auditoria**, na seção **Atividade** do **Azure Active Directory**.

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/61.png "Logs de auditoria")

Um log de auditoria tem um modo de exibição de lista padrão que mostra:

- a data e a hora da ocorrência
- o iniciador/ator (*quem*) de uma atividade 
- a atividade (*o quê*) 
- o destino

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/18.png "Logs de auditoria")

Você pode personalizar o modo de exibição de lista clicando em **Colunas** na barra de ferramentas.

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/19.png "Logs de auditoria")

Isso permite a você exibir campos adicionais ou remover campos que já estão exibidos.

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/21.png "Logs de auditoria")


Ao clicar em um item na exibição de lista, você obterá mais detalhes sobre ele.

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/22.png "Logs de auditoria")


## <a name="filtering-audit-logs"></a>Filtragem de logs de auditoria

Para restringir os dados relatados a um nível que funciona para você, filtre os dados de auditoria usando os seguintes campos:

- Intervalo de datas
- Iniciado por (ator)
- Categoria
- Tipo de recurso de atividade
- Atividade

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/23.png "Logs de auditoria")


O filtro **intervalo de datas** permite definir um período de tempo para os dados retornados.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 24 horas
- Personalizado

Quando você seleciona um período de tempo personalizado, pode configurar uma hora de início e uma hora de término.

O filtro **iniciado por** permite que você defina o nome do ator ou seu nome principal universal (UPN).

O filtro **categoria** permite que você selecione um dos seguintes filtros:

- Todos
- Categoria principal
- Diretório principal
- Gerenciamento de senhas de autoatendimento
- Gerenciamento de grupo de autoatendimento
- Provisionamento de conta - Substituição de senha automática
- Usuários convidados
- Serviço MIM
- Identity Protection
- B2C

O filtro **tipo de recurso de atividade** permite que você selecione um dos seguintes filtros:

- Todos 
- Agrupar
- Diretório
- Usuário
- Aplicativo
- Política
- Dispositivo
- Outros

Ao selecionar **Grupo** como **tipo de recurso de atividade**, você obtém uma categoria de filtro adicional que permite que você forneça também uma **Origem**:

- AD do Azure
- O365


O filtro **atividade** se baseia na categoria e na seleção do tipo de recurso de atividade que você faz. Você pode selecionar uma atividade específica que deseja exibir ou escolher todas. 

Você pode obter a lista de todas as Atividades de Auditoria usando a API do Graph https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, onde $tenantdomain = seu nome de domínio ou consulte o artigo [eventos do relatório de auditoria](active-directory-reporting-audit-events.md).


## <a name="audit-logs-shortcuts"></a>Atalhos de logs de auditoria

Além do **Azure Active Directory**, o portal do Azure fornece dois pontos de entrada adicionais para dados de auditoria:

- Usuários e grupos
- Aplicativos empresariais

### <a name="users-and-groups-audit-logs"></a>Logs de auditoria de usuários e grupos

Com relatórios de auditoria baseados em grupos e usuários, você pode obter respostas a perguntas como:

- Que tipos de atualizações foram aplicadas os usuários?

- Quantos usuários foram alterados?

- Quantas senhas foram alteradas?

- O que um administrador fez em um diretório?

- Quais são os grupos que foram adicionados?

- Existem grupos com alterações de associação?

- Os proprietários do grupo foram alterados?

- Quais licenças foram atribuídas a um grupo ou a um usuário?

Se quiser examinar os dados de auditoria relacionados aos usuários e aos grupos, você poderá encontrar uma exibição filtrada em **Logs de auditoria** na seção **Atividade** de **Usuários e Grupos**. Esse ponto de entrada tem **Usuários e grupos** como **Tipo de Recurso de Atividade** pré-selecionado.

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/93.png "Logs de auditoria")

### <a name="enterprise-applications-audit-logs"></a>Logs de auditoria de aplicativos empresariais

Com relatórios de auditoria baseados em aplicativos, você pode obter respostas a perguntas como:

* Quais aplicativos foram adicionados ou atualizados?
* Quais aplicativos foram removidos?
* Um princípio de serviço para um aplicativo foi alterado?
* Os nomes de aplicativos foram alterados?
* Quem deu permissão a um aplicativo?

Se quiser examinar os dados de auditoria relacionados aos aplicativos, você poderá encontrar uma exibição filtrada em **Logs de auditoria** na seção **Atividade** da folha **Aplicativos empresariais**. Esse ponto de entrada tem **aplicativos empresariais** como **tipo de recurso de atividade** pré-selecionado.

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/134.png "Logs de auditoria")

Você pode filtrar essa exibição para apenas **grupos** ou apenas **usuários**.

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/25.png "Logs de auditoria")



## <a name="azure-ad-audit-activity-list"></a>Lista de atividades de auditoria do Azure AD

Esta seção fornece uma lista de todas as atividades que podem ser registradas em log. 


|Nome do Serviço|Auditar categoria|Tipo de recurso de atividade|Atividade|
|---|---|---|---|
|Provisionamento de conta de usuário|Gerenciamento de Aplicativos|Aplicativo|Administração|
|Provisionamento de conta de usuário|Gerenciamento de Aplicativos|Aplicativo|Operação do diretório|
|Provisionamento de conta de usuário|Gerenciamento de Aplicativos|Aplicativo|Exportação|
|Provisionamento de conta de usuário|Gerenciamento de Aplicativos|Aplicativo|Importar|
|Provisionamento de conta de usuário|Gerenciamento de Aplicativos|Aplicativo|Outros|
|Provisionamento de conta de usuário|Gerenciamento de Aplicativos|Aplicativo|Caução de processo|
|Provisionamento de conta de usuário|Gerenciamento de Aplicativos|Aplicativo|Ação de regra de sincronização|
|Proxy de Aplicativo|Gerenciamento de Aplicativos|Aplicativo|Adicionar aplicativo|
|Proxy de Aplicativo|Recurso|Recurso|Adicionar certificado SSL do aplicativo|
|Proxy de Aplicativo|Recurso|Recurso|Excluir associação SSL|
|Proxy de Aplicativo|Gerenciamento de Aplicativos|Aplicativo|Excluir aplicativo|
|Proxy de Aplicativo|Gerenciamento de diretórios|Diretório|Desabilitar SSO da Área de Trabalho|
|Proxy de Aplicativo|Gerenciamento de diretórios|Diretório|Desabilitar SSO da Área de Trabalho para um domínio específico|
|Proxy de Aplicativo|Gerenciamento de diretórios|Diretório|Desabilitar proxy de aplicativo|
|Proxy de Aplicativo|Gerenciamento de diretórios|Diretório|Desabilitar autenticação de passagem|
|Proxy de Aplicativo|Gerenciamento de diretórios|Diretório|Habilitar SSO da Área de Trabalho|
|Proxy de Aplicativo|Gerenciamento de diretórios|Diretório|Habilitar SSO da Área de Trabalho para um domínio específico|
|Proxy de Aplicativo|Gerenciamento de diretórios|Diretório|Habilitar proxy de aplicativo|
|Proxy de Aplicativo|Gerenciamento de diretórios|Diretório|Habilitar autenticação de passagem|
|Proxy de Aplicativo|Recurso|Recurso|Registrar conector|
|Proxy de Aplicativo|Gerenciamento de Aplicativos|Aplicativo|Atualizar aplicativo|
|Proxy de Aplicativo|Gerenciamento de Aplicativos|Aplicativo|Atualizar Modo de Logon Único do aplicativo|
|Substituição de senha automática|Gerenciamento de Aplicativos|Aplicativo|Substituição de senha automática|
|B2C|Gerenciamento de Aplicativos|Aplicativo|Adicionar permissões da aplicativo V2|
|B2C|Autorização|Autorização|Adicionar permissões da aplicativo V2|
|B2C|Chave|Chave|Adicionar uma chave baseada em segredo ASCII a um contêiner de chave CPIM|
|B2C|Autorização|Autorização|Adicionar uma chave baseada em segredo ASCII a um contêiner de chave CPIM|
|B2C|Chave|Chave|Adicionar uma chave a um contêiner de chave CPIM|
|B2C|Autorização|Autorização|Adicionar uma chave a um contêiner de chave CPIM|
|B2C|Recurso|Recurso|AdminPolicyDatas-RemoveResources|
|B2C|Autorização|Autorização|AdminPolicyDatas-SetResources|
|B2C|Recurso|Recurso|AdminPolicyDatas-SetResources|
|B2C|Recurso|Recurso|AdminUserJourneys-GetResources|
|B2C|Autorização|Autorização|AdminUserJourneys-GetResources|
|B2C|Autorização|Autorização|AdminUserJourneys-RemoveResources|
|B2C|Recurso|Recurso|AdminUserJourneys-RemoveResources|
|B2C|Recurso|Recurso|AdminUserJourneys-SetResources|
|B2C|Autorização|Autorização|AdminUserJourneys-SetResources|
|B2C|Autorização|Autorização|Criar IdentityProvider|
|B2C|Recurso|Recurso|Criar IdentityProvider|
|B2C|Autorização|Autorização|Criar aplicativo V1|
|B2C|Gerenciamento de Aplicativos|Aplicativo|Criar aplicativo V1|
|B2C|Gerenciamento de Aplicativos|Aplicativo|Criar aplicativo V2|
|B2C|Autorização|Autorização|Criar aplicativo V2|
|B2C|Gerenciamento de diretórios|Diretório|Criar um domínio personalizado no locatário|
|B2C|Autorização|Autorização|Criar um domínio personalizado no locatário|
|B2C|Autorização|Autorização|Criar um novo AdminUserJourney|
|B2C|Recurso|Recurso|Criar um novo AdminUserJourney|
|B2C|Recurso|Recurso|Criar json de recurso localizado|
|B2C|Autorização|Autorização|Criar json de recurso localizado|
|B2C|Autorização|Autorização|Criar novo IDP personalizado|
|B2C|Recurso|Recurso|Criar novo IDP personalizado|
|B2C|Recurso|Recurso|Criar novo IDP|
|B2C|Autorização|Autorização|Criar novo IDP|
|B2C|Autorização|Autorização|Criar ou atualizar um recurso de diretório do B2C|
|B2C|Recurso|Recurso|Criar ou atualizar um recurso de diretório do B2C|
|B2C|Recurso|Recurso|Criar política|
|B2C|Autorização|Autorização|Criar política|
|B2C|Autorização|Autorização|Criar política trustFramework|
|B2C|Recurso|Recurso|Criar política trustFramework|
|B2C|Autorização|Autorização|Criar política trustFramework com prefixo configurável|
|B2C|Recurso|Recurso|Criar política trustFramework com prefixo configurável|
|B2C|Recurso|Recurso|Criar atributo de usuário|
|B2C|Autorização|Autorização|Criar atributo de usuário|
|B2C|Autorização|Autorização|CreateTrustFrameworkPolicy|
|B2C|Recurso|Recurso|CreateTrustFrameworkPolicy|
|B2C|Autorização|Autorização|Cria ou atualiza um novo AdminUserJourney|
|B2C|Recurso|Recurso|Excluir IDP|
|B2C|Autorização|Autorização|Excluir IDP|
|B2C|Recurso|Recurso|Excluir IdentityProvider|
|B2C|Autorização|Autorização|Excluir IdentityProvider|
|B2C|Gerenciamento de Aplicativos|Aplicativo|Excluir aplicativo V1|
|B2C|Autorização|Autorização|Excluir aplicativo V1|
|B2C|Gerenciamento de Aplicativos|Aplicativo|Excluir aplicativo V2|
|B2C|Autorização|Autorização|Excluir aplicativo V2|
|B2C|Autorização|Autorização|Excluir concessão de permissão de aplicativo V2|
|B2C|Gerenciamento de Aplicativos|Aplicativo|Excluir concessão de permissão de aplicativo V2|
|B2C|Recurso|Recurso|Excluir um recurso do diretório do B2C|
|B2C|Autorização|Autorização|Excluir um recurso do diretório do B2C|
|B2C|Chave|Chave|Excluir um contêiner de chave CPIM|
|B2C|Autorização|Autorização|Excluir um contêiner de chave CPIM|
|B2C|Chave|Chave|Excluir contêiner de chave|
|B2C|Recurso|Recurso|Excluir política trustFramework|
|B2C|Autorização|Autorização|Excluir política trustFramework|
|B2C|Recurso|Recurso|Excluir atributo de usuário|
|B2C|Autorização|Autorização|Excluir atributo de usuário|
|B2C|Autorização|Autorização|Habilitar recurso do B2C|
|B2C|Gerenciamento de diretórios|Diretório|Habilitar recurso do B2C|
|B2C|Recurso|Recurso|Obter recursos de diretório do B2C em um grupo de recursos|
|B2C|Recurso|Recurso|Obter os recursos de diretório do B2C em uma assinatura|
|B2C|Autorização|Autorização|Obter os recursos de diretório do B2C em uma assinatura|
|B2C|Autorização|Autorização|Obter IDP personalizado|
|B2C|Recurso|Recurso|Obter IDP personalizado|
|B2C|Recurso|Recurso|Obter IDP|
|B2C|Autorização|Autorização|Obter IDP|
|B2C|Autorização|Autorização|Obter aplicativos V1 e V2|
|B2C|Gerenciamento de Aplicativos|Aplicativo|Obter aplicativos V1 e V2|
|B2C|Autorização|Autorização|Obter aplicativo V1|
|B2C|Gerenciamento de Aplicativos|Aplicativo|Obter aplicativo V1|
|B2C|Autorização|Autorização|Obter aplicativos V1|
|B2C|Gerenciamento de Aplicativos|Aplicativo|Obter aplicativos V1|
|B2C|Gerenciamento de Aplicativos|Aplicativo|Obter aplicativo V2|
|B2C|Autorização|Autorização|Obter aplicativo V2|
|B2C|Gerenciamento de Aplicativos|Aplicativo|Obter aplicativos V2|
|B2C|Autorização|Autorização|Obter aplicativos V2|
|B2C|Recurso|Recurso|Obter um recurso de diretório do B2C|
|B2C|Autorização|Autorização|Obter um recurso de diretório do B2C|
|B2C|Autorização|Autorização|Obter uma lista de domínios personalizados no locatário|
|B2C|Gerenciamento de diretórios|Diretório|Obter uma lista de domínios personalizados no locatário|
|B2C|Autorização|Autorização|Obter um Percurso do Usuário|
|B2C|Recurso|Recurso|Obter um Percurso do Usuário|
|B2C|Recurso|Recurso|Obter declarações do aplicativo permitidas para o Percurso do Usuário|
|B2C|Autorização|Autorização|Obter declarações do aplicativo permitidas para o Percurso do Usuário|
|B2C|Recurso|Recurso|Obter declarações autodeclaradas permitidas para o Percurso do Usuário|
|B2C|Autorização|Autorização|Obter declarações autodeclaradas permitidas para o Percurso do Usuário|
|B2C|Recurso|Recurso|Obter declarações autodeclaradas permitidas de política|
|B2C|Autorização|Autorização|Obter declarações autodeclaradas permitidas de política|
|B2C|Recurso|Recurso|Obter lista de declarações de saída disponíveis|
|B2C|Autorização|Autorização|Obter lista de declarações de saída disponíveis|
|B2C|Recurso|Recurso|Obter definições de conteúdo para o Percurso do Usuário|
|B2C|Autorização|Autorização|Obter definições de conteúdo para o Percurso do Usuário|
|B2C|Autorização|Autorização|Obter IDPs para um fluxo específico do administrador|
|B2C|Recurso|Recurso|Obter IDPs para um fluxo específico do administrador|
|B2C|Chave|Chave|Obter metadados de chave ativa do contêiner de chave em JWK|
|B2C|Autorização|Autorização|Obter metadados de chave ativa do contêiner de chave em JWK|
|B2C|Chave|Chave|Obter metadados do contêiner de chave|
|B2C|Recurso|Recurso|Obter lista de todos os fluxos de administrador|
|B2C|Autorização|Autorização|Obter lista de todos os fluxos de administrador|
|B2C|Autorização|Autorização|Obter lista de marcas de todos os fluxos de administrador para todos os usuários|
|B2C|Recurso|Recurso|Obter lista de marcas de todos os fluxos de administrador para todos os usuários|
|B2C|Recurso|Recurso|Obter lista de locatários de um usuário|
|B2C|Autorização|Autorização|Obter lista de locatários de um usuário|
|B2C|Recurso|Recurso|Obter declarações autodeclaradas das contas locais|
|B2C|Autorização|Autorização|Obter declarações autodeclaradas das contas locais|
|B2C|Recurso|Recurso|Obter json de recurso localizado|
|B2C|Autorização|Autorização|Obter json de recurso localizado|
|B2C|Autorização|Autorização|Obter operações do provedor de recursos Microsoft.AzureActiveDirectory|
|B2C|Recurso|Recurso|Obter operações do provedor de recursos Microsoft.AzureActiveDirectory|
|B2C|Recurso|Recurso|Obter políticas|
|B2C|Autorização|Autorização|Obter políticas|
|B2C|Recurso|Recurso|Obter política|
|B2C|Autorização|Autorização|Obter política|
|B2C|Gerenciamento de diretórios|Diretório|Obter propriedades de recurso de um locatário|
|B2C|Autorização|Autorização|Obter propriedades de recurso de um locatário|
|B2C|Recurso|Recurso|Obter lista de IDP com suporte|
|B2C|Autorização|Autorização|Obter lista de IDP com suporte|
|B2C|Recurso|Recurso|Obter lista de IDP com suporte do Percurso do Usuário|
|B2C|Autorização|Autorização|Obter lista de IDP com suporte do Percurso do Usuário|
|B2C|Gerenciamento de diretórios|Diretório|Obter informações do locatário|
|B2C|Autorização|Autorização|Obter informações do locatário|
|B2C|Gerenciamento de diretórios|Diretório|Obter recursos permitidos para o locatário|
|B2C|Autorização|Autorização|Obter recursos permitidos para o locatário|
|B2C|Autorização|Autorização|Obter lista de IDPs personalizadas definida pelo locatário|
|B2C|Recurso|Recurso|Obter lista de IDPs personalizadas definida pelo locatário|
|B2C|Recurso|Recurso|Obter lista de IDPs definida pelo locatário|
|B2C|Autorização|Autorização|Obter lista de IDPs definida pelo locatário|
|B2C|Recurso|Recurso|Obter lista de IDPs locais definida pelo locatário|
|B2C|Autorização|Autorização|Obter lista de IDPs locais definida pelo locatário|
|B2C|Recurso|Recurso|Obter detalhes do locatário de um usuário para a criação de recursos|
|B2C|Autorização|Autorização|Obter detalhes do locatário de um usuário para a criação de recursos|
|B2C|Autorização|Autorização|Obter lista de locatários|
|B2C|Autorização|Autorização|Obter tenantDomains|
|B2C|Gerenciamento de diretórios|Diretório|Obter tenantDomains|
|B2C|Recurso|Recurso|Obter a cultura com suporte padrão para CPIM|
|B2C|Autorização|Autorização|Obter a cultura com suporte padrão para CPIM|
|B2C|Recurso|Recurso|Obter os detalhes de um fluxo de administrador|
|B2C|Autorização|Autorização|Obter os detalhes de um fluxo de administrador|
|B2C|Autorização|Autorização|Obter a lista de UserJourneys para o locatário|
|B2C|Recurso|Recurso|Obter a lista de UserJourneys para o locatário|
|B2C|Autorização|Autorização|Obter o conjunto de culturas com suporte disponíveis para CPIM|
|B2C|Recurso|Recurso|Obter o conjunto de culturas com suporte disponíveis para CPIM|
|B2C|Autorização|Autorização|Obter política trustFramework|
|B2C|Recurso|Recurso|Obter política trustFramework|
|B2C|Autorização|Autorização|Obter política trustFramework como xml|
|B2C|Recurso|Recurso|Obter política trustFramework como xml|
|B2C|Recurso|Recurso|Editar atributo de usuário|
|B2C|Autorização|Autorização|Editar atributo de usuário|
|B2C|Autorização|Autorização|Obter atributos de usuário|
|B2C|Recurso|Recurso|Obter atributos de usuário|
|B2C|Autorização|Autorização|Obter lista de Percurso do Usuário|
|B2C|Recurso|Recurso|Obter lista de Percurso do Usuário|
|B2C|Autorização|Autorização|GetIEFPolicies|
|B2C|Recurso|Recurso|GetIEFPolicies|
|B2C|Autorização|Autorização|GetIdentityProviders|
|B2C|Recurso|Recurso|GetIdentityProviders|
|B2C|Recurso|Recurso|GetTrustFrameworkPolicy|
|B2C|Autorização|Autorização|GetTrustFrameworkPolicy|
|B2C|Chave|Chave|Obtém um contêiner de chave CPIM em formato jwk|
|B2C|Autorização|Autorização|Obtém um contêiner de chave CPIM em formato jwk|
|B2C|Chave|Chave|Obtém uma lista de contêineres de chave no locatário|
|B2C|Autorização|Autorização|Obtém uma lista de contêineres de chave no locatário|
|B2C|Autorização|Autorização|Obtém o tipo de locatário|
|B2C|Gerenciamento de diretórios|Diretório|Obtém o tipo de locatário|
|B2C|Autenticação|Autenticação|Emitir um token de acesso para o aplicativo|
|B2C|Autenticação|Autenticação|Emitir um código de autorização para o aplicativo|
|B2C|Outros|Outros|Emitir um código de autorização para o aplicativo|
|B2C|Autenticação|Autenticação|Emitir um id_token para o aplicativo|
|B2C|Outros|Outros|Emitir um id_token para o aplicativo|
|B2C|Autorização|Autorização|MigrateTenantMetadata|
|B2C|Recurso|Recurso|MigrateTenantMetadata|
|B2C|Recurso|Recurso|Mover recursos|
|B2C|Autorização|Autorização|Fazer patch de IdentityProvider|
|B2C|Recurso|Recurso|Fazer patch de IdentityProvider|
|B2C|Recurso|Recurso|PutTrustFrameworkPolicy|
|B2C|Autorização|Autorização|PutTrustFrameworkPolicy|
|B2C|Autorização|Autorização|PutTrustFrameworkpolicy|
|B2C|Recurso|Recurso|PutTrustFrameworkpolicy|
|B2C|Recurso|Recurso|Remover um Percurso do Usuário|
|B2C|Autorização|Autorização|Remover um Percurso do Usuário|
|B2C|Autorização|Autorização|Restaurar um backup de contêiner de chave CPIM|
|B2C|Chave|Chave|Restaurar um backup de contêiner de chave CPIM|
|B2C|Gerenciamento de Aplicativos|Aplicativo|Recuperar concessões de permissões de aplicativo V2|
|B2C|Autorização|Autorização|Recuperar concessões de permissões de aplicativo V2|
|B2C|Gerenciamento de Aplicativos|Aplicativo|Recuperar entidades de serviço de aplicativo V2 no locatário atual|
|B2C|Autorização|Autorização|Recuperar entidades de serviço de aplicativo V2 no locatário atual|
|B2C|Chave|Chave|Salvar contêiner de chave|
|B2C|Autorização|Autorização|Atualizar IDP personalizado|
|B2C|Recurso|Recurso|Atualizar IDP personalizado|
|B2C|Recurso|Recurso|Atualizar IDP|
|B2C|Autorização|Autorização|Atualizar IDP|
|B2C|Recurso|Recurso|Atualizar IDP local|
|B2C|Autorização|Autorização|Atualizar IDP local|
|B2C|Gerenciamento de Aplicativos|Aplicativo|Atualizar aplicativo V1|
|B2C|Autorização|Autorização|Atualizar aplicativo V1|
|B2C|Gerenciamento de Aplicativos|Aplicativo|Atualizar aplicativo V2|
|B2C|Autorização|Autorização|Atualizar aplicativo V2|
|B2C|Gerenciamento de Aplicativos|Aplicativo|Atualizar concessão de permissão de aplicativo V2|
|B2C|Autorização|Autorização|Atualizar concessão de permissão de aplicativo V2|
|B2C|Recurso|Recurso|Exibir um recurso de diretório do B2C|
|B2C|Recurso|Recurso|Atualizar política|
|B2C|Autorização|Autorização|Atualizar política|
|B2C|Recurso|Recurso|Atualizar status da assinatura|
|B2C|Recurso|Recurso|Atualizar atributo de usuário|
|B2C|Autorização|Autorização|Atualizar atributo de usuário|
|B2C|Chave|Chave|Carregar uma chave CPIM criptografada|
|B2C|Autorização|Autorização|Carregar uma chave CPIM criptografada|
|B2C|Autorização|Autorização|Autorização do usuário: a API está desabilitada para o conjunto de recursos do locatário|
|B2C|Autorização|Autorização|Autorização do usuário: acesso concedido ao usuário como ‘Administrador do locatário’|
|B2C|Autorização|Autorização|Autorização do usuário: direitos de acesso concedidos ao usuário como 'Usuários autenticados'|
|B2C|Autenticação|Autenticação|Validar credenciais de conta local|
|B2C|Recurso|Recurso|Validar movimentação de recursos|
|B2C|Autenticação|Autenticação|Validar autenticação de usuário|
|B2C|Gerenciamento de diretórios|Diretório|Verificar se o recurso B2C está habilitado|
|B2C|Autorização|Autorização|Verificar se o recurso B2C está habilitado|
|B2C|Autorização|Autorização|Verificar se o recurso está habilitado|
|B2C|Gerenciamento de diretórios|Diretório|Verificar se o recurso está habilitado|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Adicionar Oauth2Permissiongrant|
|Diretório principal|Gerenciamento de unidades administrativas|AdministrativeUnit|Adicionar unidade administrativa|
|Diretório principal|Gerenciamento de Usuários|Usuário|Adicionar concessão de atribuição de função de aplicativo ao usuário|
|Diretório principal|Gerenciamento de grupos|Agrupar|Adicionar atribuição de função de aplicativo ao grupo|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Adicionar atribuição de função de aplicativo à entidade de serviço|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Adicionar aplicativo|
|Diretório principal|Recurso|Recurso|Adicionar dispositivo|
|Diretório principal|Recurso|Recurso|Adicionar configuração do dispositivo|
|Diretório principal|Gerenciamento de funções|Função|Adicionar membro qualificado à função|
|Diretório principal|Gerenciamento de grupos|Agrupar|Adicionar grupo|
|Diretório principal|Gerenciamento de unidades administrativas|AdministrativeUnit|Adicionar membro à unidade administrativa|
|Diretório principal|Gerenciamento de grupos|Agrupar|Adicionar membro ao grupo|
|Diretório principal|Gerenciamento de funções|Função|Adicionar membro à função|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Adicionar proprietário ao aplicativo|
|Diretório principal|Gerenciamento de grupos|Agrupar|Adicionar proprietário ao grupo|
|Diretório principal|Gerenciamento de política|Política|Adicionar proprietário à política|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Adicionar proprietário à entidade de serviço|
|Diretório principal|Gerenciamento de diretórios|Diretório|Adicionar um parceiro à empresa|
|Diretório principal|Gerenciamento de política|Política|Adicionar política|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Adicionar política à entidade de serviço|
|Diretório principal|Recurso|Recurso|Adicionar proprietário registrado ao dispositivo|
|Diretório principal|Recurso|Recurso|Adicionar usuários registrados ao dispositivo|
|Diretório principal|Gerenciamento de funções|Função|Adicionar atribuição de função à definição de função|
|Diretório principal|Gerenciamento de funções|Função|Adicionar função originada no modelo|
|Diretório principal|Gerenciamento de funções|Função|Adicionar membro no escopo à função|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Adicionar entidade de serviço|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Adicionar credenciais de entidade de serviço|
|Diretório principal|Gerenciamento de diretórios|Diretório|Adicionar domínio não verificado|
|Diretório principal|Gerenciamento de Usuários|Usuário|Adicionar usuário|
|Diretório principal|Gerenciamento de Usuários|Usuário|Adicionar detalhes de aplicativo de telefone de autenticação forte do usuário|
|Diretório principal|Gerenciamento de diretórios|Diretório|Adicionar domínio verificado|
|Diretório principal|Gerenciamento de Usuários|Usuário|Alterar licença de usuário|
|Diretório principal|Gerenciamento de Usuários|Usuário|Alterar senha de usuário|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Autorizar aplicativo|
|Diretório principal|Gerenciamento de Usuários|Usuário|Converter usuário federado em gerenciado|
|Diretório principal|Gerenciamento de Usuários|Usuário|Criar senha de aplicativo para o usuário|
|Diretório principal|Gerenciamento de diretórios|Diretório|Criar empresa|
|Diretório principal|Gerenciamento de diretórios|Diretório|Criar configurações da empresa|
|Diretório principal|Gerenciamento de grupos|Agrupar|Criar configurações de grupo|
|Diretório principal|Gerenciamento de unidades administrativas|AdministrativeUnit|Excluir unidade administrativa|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Excluir aplicativo|
|Diretório principal|Gerenciamento de Usuários|Usuário|Excluir senha de aplicativo do usuário|
|Diretório principal|Gerenciamento de diretórios|Diretório|Excluir configurações da empresa|
|Diretório principal|Recurso|Recurso|Excluir um dispositivo|
|Diretório principal|Recurso|Recurso|Excluir configuração de dispositivo|
|Diretório principal|Gerenciamento de grupos|Agrupar|Excluir grupo|
|Diretório principal|Gerenciamento de grupos|Agrupar|Excluir configurações de grupo|
|Diretório principal|Gerenciamento de política|Política|Excluir política|
|Diretório principal|Gerenciamento de Usuários|Usuário|Excluir usuário|
|Diretório principal|Gerenciamento de diretórios|Diretório|Rebaixar parceiro|
|Diretório principal|Recurso|Recurso|O dispositivo não é mais compatível|
|Diretório principal|Recurso|Recurso|O dispositivo não é mais gerenciado|
|Diretório principal|Gerenciamento de diretórios|Diretório|Diretório excluído|
|Diretório principal|Gerenciamento de diretórios|Diretório|Diretório excluído permanentemente|
|Diretório principal|Gerenciamento de diretórios|Diretório|Diretório agendado para exclusão|
|Diretório principal|Gerenciamento de Usuários|Usuário|Desabilitar conta|
|Diretório principal|Gerenciamento de Usuários|Usuário|Habilitar autenticação forte|
|Diretório principal|Gerenciamento de grupos|Agrupar|Concluir a aplicação de licença com base em grupo aos usuários|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Excluir aplicativo irreversivelmente|
|Diretório principal|Gerenciamento de grupos|Agrupar|Excluir grupo irreversivelmente|
|Diretório principal|Gerenciamento de Usuários|Usuário|Excluir usuário irreversivelmente|
|Diretório principal|Gerenciamento de diretórios|Diretório|Promover empresa à condição de parceira|
|Diretório principal|Gerenciamento de diretórios|Diretório|Limpar propriedades de gerenciamento de direitos|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Remover OAuth2PermissionGrant|
|Diretório principal|Gerenciamento de grupos|Agrupar|Remover atribuição de função de aplicativo do grupo|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Remover a atribuição de função de aplicativo da entidade de serviço|
|Diretório principal|Gerenciamento de Usuários|Usuário|Remover atribuição de função de aplicativo do usuário|
|Diretório principal|Gerenciamento de funções|Função|Remover membro qualificado da função|
|Diretório principal|Gerenciamento de unidades administrativas|AdministrativeUnit|Remover membro de unidade administrativa|
|Diretório principal|Gerenciamento de grupos|Agrupar|Remover membro do grupo|
|Diretório principal|Gerenciamento de funções|Função|Remover membro da função|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Remover proprietário do aplicativo|
|Diretório principal|Gerenciamento de grupos|Agrupar|Remover proprietário do grupo|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Remover proprietário da entidade de serviço|
|Diretório principal|Gerenciamento de diretórios|Diretório|Remover parceiro da empresa|
|Diretório principal|Gerenciamento de política|Política|Remover credenciais de política|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Remover política da entidade de serviço|
|Diretório principal|Recurso|Recurso|Remover proprietário registrado do dispositivo|
|Diretório principal|Recurso|Recurso|Remover usuários registrados do dispositivo|
|Diretório principal|Gerenciamento de funções|Função|Remover atribuição de função da definição de função|
|Diretório principal|Gerenciamento de funções|Função|Remover membro no escopo da função|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Remover entidade de serviço|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Remover credenciais de entidade de serviço|
|Diretório principal|Gerenciamento de diretórios|Diretório|Remover domínio não verificado|
|Diretório principal|Gerenciamento de Usuários|Usuário|Remover detalhes de aplicativo de telefone de autenticação forte do usuário|
|Diretório principal|Gerenciamento de diretórios|Diretório|Remover domínio verificado|
|Diretório principal|Gerenciamento de Usuários|Usuário|Redefinir senha de usuário|
|Diretório principal|Gerenciamento de grupos|Agrupar|Restaurar grupo|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Restaurar aplicativo|
|Diretório principal|Gerenciamento de Usuários|Usuário|Restaurar usuário|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Revogar autorização|
|Diretório principal|Gerenciamento de diretórios|Diretório|Definir informações da empresa|
|Diretório principal|Gerenciamento de diretórios|Diretório|Definir recurso DirSync|
|Diretório principal|Gerenciamento de diretórios|Diretório|Definir sinalizador DirSyncEnabled|
|Diretório principal|Gerenciamento de diretórios|Diretório|Definir parceria|
|Diretório principal|Gerenciamento de diretórios|Diretório|Definir limite de exclusão acidental|
|Diretório principal|Gerenciamento de diretórios|Diretório|Definir local de dados permitido da empresa|
|Diretório principal|Gerenciamento de diretórios|Diretório|Definir recurso multinacional de empresa como habilitado|
|Diretório principal|Gerenciamento de diretórios|Diretório|Definir recurso de diretório no locatário|
|Diretório principal|Gerenciamento de diretórios|Diretório|Definir a autenticação de domínio|
|Diretório principal|Gerenciamento de diretórios|Diretório|Definir configurações de federação no domínio|
|Diretório principal|Gerenciamento de Usuários|Usuário|Definir alteração forçada de senha de usuário|
|Diretório principal|Gerenciamento de grupos|Agrupar|Definir licença de grupo|
|Diretório principal|Gerenciamento de grupos|Agrupar|Definir grupo a ser gerenciado pelo usuário|
|Diretório principal|Gerenciamento de diretórios|Diretório|Definir política de senha|
|Diretório principal|Gerenciamento de diretórios|Diretório|Definir propriedades de gerenciamento de direitos|
|Diretório principal|Gerenciamento de Usuários|Usuário|Definir gerenciador de usuários|
|Diretório principal|Gerenciamento de Usuários|Usuário|Definir metadados de token OAuth de usuários como habilitados|
|Diretório principal|Gerenciamento de grupos|Agrupar|Iniciar a aplicação de licença com base em grupo aos usuários|
|Diretório principal|Gerenciamento de grupos|Agrupar|Disparar recálculo de licença do grupo|
|Diretório principal|Gerenciamento de Usuários|Usuário|Atualizar carimbo de data/hora StsRefreshTokenValidFrom|
|Diretório principal|Gerenciamento de unidades administrativas|AdministrativeUnit|Atualizar unidade administrativa|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Atualizar aplicativo|
|Diretório principal|Gerenciamento de diretórios|Diretório|Atualizar empresa|
|Diretório principal|Gerenciamento de diretórios|Diretório|Atualizar configurações da empresa|
|Diretório principal|Recurso|Recurso|Atualizar dispositivo|
|Diretório principal|Recurso|Recurso|Atualizar configuração do dispositivo|
|Diretório principal|Gerenciamento de diretórios|Diretório|Domínio de atualização|
|Diretório principal|Gerenciamento de Usuários|Usuário|Atualizar segredos externos|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Atualizar segredos externos|
|Diretório principal|Gerenciamento de grupos|Agrupar|Atualizar grupo|
|Diretório principal|Gerenciamento de grupos|Agrupar|Atualizar configurações de grupo|
|Diretório principal|Gerenciamento de política|Política|Atualizar política|
|Diretório principal|Gerenciamento de funções|Função|Atualizar função|
|Diretório principal|Gerenciamento de Aplicativos|Aplicativo|Atualizar entidade de serviço|
|Diretório principal|Gerenciamento de Usuários|Usuário|Atualizar usuário|
|Diretório principal|Gerenciamento de diretórios|Diretório|Verificar domínio|
|Diretório principal|Gerenciamento de diretórios|Diretório|Verificar domínio por email|
|Identity Protection|Gerenciamento de Usuários|Usuário|O administrador gera uma senha temporária|
|Identity Protection|Gerenciamento de Usuários|Usuário|Os administradores exigem que o usuário redefina a senha|
|Identity Protection|Outros|Outros|Baixar um único tipo de evento de risco|
|Identity Protection|Outros|Outros|Baixar administradores e status de aceitação do resumo semanal|
|Identity Protection|Outros|Outros|Baixar todos os tipos de evento de risco|
|Identity Protection|Outros|Outros|Baixar eventos de risco de usuário gratuitos|
|Identity Protection|Outros|Outros|Baixar usuários sinalizados por risco|
|Identity Protection|Gerenciamento de diretórios|Diretório|Integração|
|Identity Protection|Gerenciamento de política|Política|Definir política de registro MFA|
|Identity Protection|Gerenciamento de política|Política|Definir política de risco de entrada|
|Identity Protection|Gerenciamento de política|Política|Definir política de risco do usuário|
|Identity Protection|Gerenciamento de diretórios|Diretório|Atualizar configurações de alerta|
|Identity Protection|Gerenciamento de diretórios|Diretório|Atualizar configurações de resumo semanal|
|Usuários Convidados|Gerenciamento de Usuários|Usuário|Atribuir usuário externo ao aplicativo|
|Usuários Convidados|Outros|Outros|Convites do Lote processados|
|Usuários Convidados|Outros|Outros|Convites do Lote carregados|
|Usuários Convidados|Gerenciamento de Usuários|Usuário|Email não enviado; o usuário cancelou a assinatura|
|Usuários Convidados|Gerenciamento de Usuários|Usuário|Convidar usuário externo|
|Usuários Convidados|Gerenciamento de Usuários|Usuário|Resgatar convite para usuário externo|
|Usuários Convidados|Gerenciamento de Usuários|Usuário|Criação de locatário viral|
|Usuários Convidados|Gerenciamento de Usuários|Usuário|Criação de usuário viral|
|MIM (Microsoft Identity Manager)|Gerenciamento de grupos|Agrupar|Adicionar membro|
|MIM (Microsoft Identity Manager)|Gerenciamento de grupos|Agrupar|Criar Grupo|
|MIM (Microsoft Identity Manager)|Gerenciamento de grupos|Agrupar|Excluir grupo|
|MIM (Microsoft Identity Manager)|Gerenciamento de grupos|Agrupar|Remover membro|
|MIM (Microsoft Identity Manager)|Gerenciamento de grupos|Agrupar|Atualizar grupo|
|MIM (Microsoft Identity Manager)|Gerenciamento de Usuários|Usuário|Registro de senha do usuário|
|MIM (Microsoft Identity Manager)|Gerenciamento de Usuários|Usuário|Redefinição de senha do usuário|
|Privileged Identity Management|Gerenciamento de funções|Função|AccessReview_Review|
|Privileged Identity Management|Gerenciamento de funções|Função|AccessReview_Update|
|Privileged Identity Management|Gerenciamento de funções|Função|ActivationAborted|
|Privileged Identity Management|Gerenciamento de funções|Função|ActivationApproved|
|Privileged Identity Management|Gerenciamento de funções|Função|ActivationCanceled|
|Privileged Identity Management|Gerenciamento de funções|Função|ActivationRequested|
|Privileged Identity Management|Gerenciamento de funções|Função|Added|
|Privileged Identity Management|Gerenciamento de funções|Função|Assign|
|Privileged Identity Management|Gerenciamento de funções|Função|Elevate|
|Privileged Identity Management|Gerenciamento de funções|Função|Removed|
|Privileged Identity Management|Gerenciamento de funções|Função|Alterações na configuração de função|
|Privileged Identity Management|Gerenciamento de funções|Função|ScanAlertsNow|
|Privileged Identity Management|Gerenciamento de funções|Função|Inscrever-se|
|Privileged Identity Management|Gerenciamento de funções|Função|Unelevate|
|Privileged Identity Management|Gerenciamento de funções|Função|UpdateAlertSettings|
|Privileged Identity Management|Gerenciamento de funções|Função|UpdateCurrentState|
|Gerenciamento de grupos de autoatendimento|Gerenciamento de grupos|Agrupar|Aprovar uma solicitação pendente para ingressar em um grupo|
|Gerenciamento de grupos de autoatendimento|Gerenciamento de grupos|Agrupar|Cancelar uma solicitação pendente para ingressar em um grupo|
|Gerenciamento de grupos de autoatendimento|Gerenciamento de grupos|Agrupar|Criar política de gerenciamento de ciclo de vida|
|Gerenciamento de grupos de autoatendimento|Gerenciamento de grupos|Agrupar|Excluir uma solicitação pendente para ingressar em um grupo|
|Gerenciamento de grupos de autoatendimento|Gerenciamento de grupos|Agrupar|Rejeitar uma solicitação pendente para ingressar em um grupo|
|Gerenciamento de grupos de autoatendimento|Gerenciamento de grupos|Agrupar|Renovar grupo|
|Gerenciamento de grupos de autoatendimento|Gerenciamento de grupos|Agrupar|Solicitar ingresso em um grupo|
|Gerenciamento de grupos de autoatendimento|Gerenciamento de grupos|Agrupar|Definir propriedades de grupo dinâmico|
|Gerenciamento de grupos de autoatendimento|Gerenciamento de grupos|Agrupar|Atualizar política de gerenciamento de ciclo de vida|
|Gerenciamento de senhas de auto-atendimento|Gerenciamento de Usuários|Usuário|Impedido de executar a redefinição de senha de autoatendimento|
|Gerenciamento de senhas de auto-atendimento|Gerenciamento de Usuários|Usuário|Alterar senha (autoatendimento)|
|Gerenciamento de senhas de auto-atendimento|Gerenciamento de diretórios|Diretório|Desabilitar write-back de senha para o diretório|
|Gerenciamento de senhas de auto-atendimento|Gerenciamento de diretórios|Diretório|Habilitar write-back de senha para o diretório|
|Gerenciamento de senhas de auto-atendimento|Gerenciamento de Usuários|Usuário|Redefinir senha (pelo administrador)|
|Gerenciamento de senhas de auto-atendimento|Gerenciamento de Usuários|Usuário|Redefinir senha (autoatendimento)|
|Gerenciamento de senhas de auto-atendimento|Gerenciamento de Usuários|Usuário|Progresso da atividade de fluxo de redefinição de senha de autoatendimento|
|Gerenciamento de senhas de auto-atendimento|Gerenciamento de Usuários|Usuário|Progresso da atividade de fluxo de redefinição de senha de autoatendimento|
|Gerenciamento de senhas de auto-atendimento|Gerenciamento de Usuários|Usuário|Desbloquear conta de usuário (autoatendimento)|
|Gerenciamento de senhas de auto-atendimento|Gerenciamento de Usuários|Usuário|Usuário registrado para redefinição de senha de autoatendimento|
|Termos de Uso|Gerenciamento de política|Política|Aceitar os Termos de Uso|
|Termos de Uso|Gerenciamento de política|Política|Criar Termos de Uso|
|Termos de Uso|Gerenciamento de política|Política|Recusar Termos de Uso|
|Termos de Uso|Gerenciamento de política|Política|Excluir Termos de Uso|
|Termos de Uso|Gerenciamento de política|Política|Editar Termos de Uso|
|Termos de Uso|Gerenciamento de política|Política|Publicar Termos de Uso|
|Termos de Uso|Gerenciamento de política|Política|Cancelar a publicação dos Termos de Uso|




## <a name="next-steps"></a>Próximas etapas

Para ter uma visão geral dos relatórios, consulte [Relatórios do Azure Active Directory](active-directory-reporting-azure-portal.md).

