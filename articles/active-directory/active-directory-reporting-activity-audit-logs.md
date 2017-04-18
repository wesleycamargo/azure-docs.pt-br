---
title: "Auditoria de relatórios de atividade no portal do Azure Active Directory – versão prévia | Microsoft Docs"
description: "Introdução à auditoria de relatórios de atividade no portal do Azure Active Directory – versão prévia"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: aef5bce6f440f4a0a57763f915d307297f50281b
ms.lasthandoff: 04/10/2017


---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal---preview"></a>Auditoria de relatórios de atividade no portal do Azure Active Directory – versão prévia

Com o relatório na [versão prévia](active-directory-preview-explainer.md) do Azure Active Directory, você obtém todas as informações de que precisa para determinar como seu ambiente está se comportando.

A arquitetura de relatório no Azure Active Directory consiste nos seguintes componentes:

- **Atividade** 
    - **Atividades de entrada** – informações sobre o uso de aplicativos gerenciados e de atividades de entrada do usuário
    - **Logs de auditoria** – informações de atividade do sistema sobre o gerenciamento de usuários e de grupos, sobre os aplicativos gerenciados e sobre as atividades de diretório.
- **Segurança** 
    - **Entradas arriscadas** - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário. Para obter mais detalhes, veja Entradas de risco.
    - **Usuários sinalizados para riscos** - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida. Para obter mais detalhes, consulte Usuários sinalizados para risco.

Este tópico fornece uma visão geral das atividades de auditoria.
 


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
- Iniciado por
- Categoria
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

O filtro **iniciado por** permite que você defina o nome de um ator ou seu nome UPN.

O filtro **categoria** permite que você selecione um dos seguintes filtros:

- Todos
- Categoria principal
- Diretório principal
- Gerenciamento de senhas de autoatendimento
- Gerenciamento de grupo de autoatendimento
- Provisionamento de conta
- Substituição de senha automática
- usuários convidados
- Serviço MIM

O filtro **tipo de recurso de atividade** permite que você selecione um dos seguintes filtros:

- Todos 
- Agrupar
- Diretório
- Usuário
- Aplicativo
- Política
- Dispositivo
- outro

Ao selecionar **Grupo** como **tipo de recurso de atividade**, você obtém uma categoria de filtro adicional que permite que você forneça também uma **Origem**:

- AD do Azure
- O365




O filtro **atividade** se baseia na categoria e na seleção do tipo de recurso de atividade que você faz. Você pode selecionar uma atividade específica que deseja exibir ou escolher todas. 

| Categoria da atividade| Tipo de recurso de atividade| Atividade |
| :-- | :-: | :-- |
| Diretório principal| Agrupar| Excluir configurações de grupo|
| Diretório principal| Diretório| Domínio de atualização|
| Diretório principal| Diretório| Remover o parceiro da empresa|
| Diretório principal| Usuário| Atualizar função|
| Diretório principal| Usuário| Função adicionada de modelo|
| Diretório principal| Agrupar| Adicionar atribuição de função de aplicativo ao grupo|
| Diretório principal| Agrupar| Iniciar a aplicação de licença com base em grupo para usuários|
| Diretório principal| Aplicativo| Adicionar entidade de serviço|
| Diretório principal| Política| Atualizar política|
| Diretório principal| Política| Adicionar política à entidade de serviço|
| Diretório principal| Dispositivo| Adicionar proprietário registrado ao dispositivo|
| Diretório principal| Dispositivo| Adicionar usuários registrados ao dispositivo|
| Diretório principal| Dispositivo| Atualizar configuração do dispositivo|
| Gerenciamento de senhas de auto-atendimento| Usuário| Redefinir a senha (autoatendimento)|
| Gerenciamento de senhas de auto-atendimento| Usuário| Desbloquear a conta de usuário (autoatendimento)|
| Gerenciamento de senhas de auto-atendimento| Usuário| Redefinir a senha (pelo administrador)|
| Gerenciamento de grupos de autoatendimento| Agrupar| Excluir uma solicitação pendente para ingressar em um grupo|
| Provisionamento de conta de usuário| Aplicativo| Caução de processo|
| Substituição de senha automática| Aplicativo| Substituição de senha automática|
| Usuários Convidados| outro| Convites do Lote processados|
| Diretório principal| Diretório| Remover domínio verificado|
| Diretório principal| Diretório| Adicionar domínio não verificado|
| Diretório principal| Diretório| Adicionar domínio verificado|
| Diretório principal| Diretório| Definir recurso de diretório no locatário|
| Diretório principal| Diretório| Definir sinalizador DirSyncEnabled|
| Diretório principal| Diretório| Criar configurações da empresa|
| Diretório principal| Diretório| Atualizar configurações da empresa|
| Diretório principal| Diretório| Excluir configurações da empresa|
| Diretório principal| Diretório| Definir local de dados permitidos da empresa|
| Diretório principal| Diretório| Definir recurso multinacional de empresa habilitado|
| Diretório principal| Usuário| Atualizar usuário|
| Diretório principal| Usuário| Excluir usuário|
| Diretório principal| Agrupar| Remover membro do grupo|
| Diretório principal| Agrupar| Definir licença de grupo|
| Diretório principal| Agrupar| Criar configurações de grupo|
| Diretório principal| Aplicativo| Atualizar entidade de serviço|
| Diretório principal| Aplicativo| Excluir Aplicativo|
| Diretório principal| Aplicativo| Atualizar aplicativo|
| Diretório principal| Aplicativo| Remover entidade de serviço|
| Diretório principal| Aplicativo| Adicionar credenciais de entidade de serviço|
| Diretório principal| Aplicativo| Remover a atribuição de função de aplicativo da entidade de serviço|
| Diretório principal| Aplicativo| Remover proprietário do aplicativo|
| Diretório principal| Dispositivo| Remover proprietário registrado do dispositivo|
| Gerenciamento de senhas de auto-atendimento| Usuário| Progresso da atividade de fluxo de redefinição de senha de autoatendimento|
| Provisionamento de conta de usuário| Aplicativo| Administração|
| Provisionamento de conta de usuário| Aplicativo| Operação de diretório|
| Serviço MIM| Agrupar| Remover membro|
| Diretório principal| Política| Excluir política|
| Usuários Convidados| Usuário| Criação de locatário viral|
| Diretório principal| Diretório| Atualizar segredos externos|
| Diretório principal| Diretório| Definir propriedades do Rights Management|
| Diretório principal| Diretório| Atualizar empresa|
| Diretório principal| Usuário| Adicionar usuário|
| Diretório principal| Usuário| Converter usuário federado em gerenciado|
| Diretório principal| Usuário| Criar senha de aplicativo do usuário|
| Diretório principal| Agrupar| Adicionar membro ao grupo|
| Diretório principal| Agrupar| Adicionar grupo|
| Diretório principal| Aplicativo| Consentimento para o aplicativo|
| Diretório principal| Aplicativo| Adicionar aplicativo|
| Diretório principal| Aplicativo| Adicionar proprietário à entidade de serviço|
| Diretório principal| Aplicativo| Remover Oauth2Permissiongrant|
| Diretório principal| Política| Remover credenciais de política|
| Diretório principal| Dispositivo| Excluir configuração de dispositivo|
| Gerenciamento de grupos de autoatendimento| Agrupar| Definir propriedades de grupo dinâmico|
| Gerenciamento de grupos de autoatendimento| Agrupar| Atualizar política de gerenciamento de ciclo de vida|
| Provisionamento de conta de usuário| Aplicativo| Ação de regra de sincronização|
| Usuários Convidados| outro| Convites do Lote carregados|
| Serviço MIM| Agrupar| Adicionar membro|
| Diretório principal| Usuário| Definir propriedades de licença|
| Diretório principal| Usuário| Restaurar usuário|
| Diretório principal| Usuário| Remover membro da função|
| Diretório principal| Usuário| Remover atribuição de função de aplicativo do usuário|
| Diretório principal| Usuário| Remover membro de escopo de função|
| Diretório principal| Agrupar| Atualizar grupo|
| Diretório principal| Agrupar| Adicionar proprietário ao grupo|
| Diretório principal| Agrupar| Terminar de aplicar licença com base em grupo para usuários|
| Diretório principal| Agrupar| Remover atribuição de função de aplicativo do grupo|
| Diretório principal| Agrupar| Definir grupo a ser gerenciado pelo usuário|
| Diretório principal| Aplicativo| Add Oauth2Permissiongrant|
| Diretório principal| Aplicativo| Adicionar atribuição de função de aplicativo à entidade de serviço|
| Diretório principal| Aplicativo| Remover credenciais de entidade de serviço|
| Diretório principal| Política| Remover política da entidade de serviço|
| Diretório principal| Dispositivo| Atualizar dispositivo|
| Diretório principal| Dispositivo| Adicionar dispositivo|
| Diretório principal| Dispositivo| Adicionar configuração do dispositivo|
| Gerenciamento de senhas de auto-atendimento| Usuário| Alterar senha (autoatendimento)|
| Gerenciamento de senhas de auto-atendimento| Usuário| Usuário registrado para redefinição de senha de autoatendimento|
| Gerenciamento de grupos de autoatendimento| Agrupar| Aprovar uma solicitação pendente para ingressar em um grupo|
| Diretório principal| Diretório| Remover domínio não verificado|
| Diretório principal| Diretório| Verificar domínio|
| Diretório principal| Diretório| Definir a autenticação de domínio|
| Diretório principal| Diretório| Definir política de senha|
| Diretório principal| Diretório| Adicionar parceiro à empresa|
| Diretório principal| Diretório| Promover empresa parceira|
| Diretório principal| Diretório| Definir parceria|
| Diretório principal| Diretório| Definir limite de exclusão acidental|
| Diretório principal| Diretório| Rebaixar parceiro|
| Usuários Convidados| Usuário| Convidar usuário externo|
| Provisionamento de conta de usuário| Aplicativo| Importar|
| Diretório principal| Aplicativo| Remover proprietário da entidade de serviço|
| Diretório principal| Dispositivo| Remover usuários registrados do dispositivo|
| Diretório principal| Diretório| Definir informações da empresa|
| Diretório principal| Diretório| Definir configurações de federação no domínio|
| Diretório principal| Diretório| Criar empresa|
| Diretório principal| Diretório| Limpar propriedades do Rights Management|
| Diretório principal| Diretório| Definir recurso DirSync|
| Diretório principal| Diretório| Verificar domínio verificado por email|
| Diretório principal| Usuário| Alterar licença de usuário|
| Diretório principal| Usuário| Alterar senha de usuário|
| Diretório principal| Usuário| Redefinir senha de usuário|
| Diretório principal| Usuário| Adicionar concessão de atribuição de função de aplicativo ao usuário|
| Diretório principal| Usuário| Adicionar membro à função|
| Diretório principal| Usuário| Excluir senha de aplicativo do usuário|
| Diretório principal| Usuário| Atualizar credenciais de usuário|
| Diretório principal| Usuário| Definir Gerenciador de Usuários|
| Diretório principal| Usuário| Adicioanr membro de escopo à função|
| Diretório principal| Agrupar| Excluir grupo|
| Diretório principal| Agrupar| Remover proprietário do grupo|
| Diretório principal| Agrupar| Atualizar configurações de grupo|
| Diretório principal| Aplicativo| Adicionar proprietário ao aplicativo|
| Diretório principal| Aplicativo| Revogar autorização|
| Diretório principal| Política| Adicionar política|
| Diretório principal| Dispositivo| Excluir dispositivo|
| Gerenciamento de senhas de auto-atendimento| Usuário| Impedido de executar a redefinição de senha de autoatendimento|
| Gerenciamento de grupos de autoatendimento| Agrupar| Solicitar ingresso em um grupo|
| Gerenciamento de grupos de autoatendimento| Agrupar| Criar política de gerenciamento de ciclo de vida|
| Gerenciamento de grupos de autoatendimento| Agrupar| Rejeitar uma solicitação pendente para ingressar em um grupo|
| Gerenciamento de grupos de autoatendimento| Agrupar| Cancelar uma solicitação pendente para ingressar em um grupo|
| Gerenciamento de grupos de autoatendimento| Agrupar| Renovar grupo|
| Provisionamento de conta de usuário| Aplicativo| Exportação|
| Provisionamento de conta de usuário| Aplicativo| outro|
| Usuários Convidados| Usuário| Resgatar convite para usuário externo|
| Usuários Convidados| Usuário| Criação de usuário viral|
| Usuários Convidados| Usuário| Atribuir usuário externo ao aplicativo|




## <a name="audit-logs-shortcuts"></a>Atalhos de logs de auditoria

Além do **Azure Active Directory**, o portal do Azure fornece dois pontos de entrada adicionais para dados de auditoria:

- Usuários e grupos
- Aplicativos empresariais

Para obter uma lista completa de atividades de relatório de auditoria, consulte o [lista de eventos de relatório de auditoria](active-directory-reporting-audit-events.md#list-of-audit-report-events).


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

Se quiser examinar os dados de auditoria relacionados aos aplicativos, você poderá encontrar uma exibição filtrada em **Logs de auditoria** na seção **Atividade** da folha **Aplicativos empresariais**. Esse ponto de entrada tem **Aplicativo** como **Tipo de Recurso de Atividade** pré-selecionado.

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/134.png "Logs de auditoria")

Você pode filtrar essa exibição para apenas **grupos** ou apenas **usuários**.

![Logs de auditoria](./media/active-directory-reporting-activity-audit-logs/25.png "Logs de auditoria")


## <a name="next-steps"></a>Próximas etapas
Veja o [Guia de relatórios do Azure Active Directory](active-directory-reporting-guide.md).


