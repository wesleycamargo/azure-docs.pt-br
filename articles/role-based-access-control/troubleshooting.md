---
title: Solução de problemas com o RBAC para recursos do Azure | Microsoft Docs
description: Solucione problemas com RBAC (controle de acesso baseado em função) para recursos do Azure.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: d85c49cc8533b88382de81f8f12fde7116afb69a
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407582"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Solução de problemas com o RBAC para recursos do Azure

Este artigo responde a perguntas comuns sobre o RBAC (controle de acesso baseado em função) para recursos do Azure, para que você saiba o que esperar ao usar as funções no portal do Azure e possa solucionar problemas de acesso.

## <a name="problems-with-rbac-role-assignments"></a>Problemas com as atribuições de função RBAC

- Se você não conseguir adicionar uma atribuição de função no portal do Azure no **controle de acesso (IAM)** porque o **Add** > **Adicionar atribuição de função** opção está desabilitada ou porque você receber o erro de permissões "o cliente com a id de objeto não tem autorização para executar a ação", verifique se você está atualmente registrado com um usuário que é atribuído a uma função que tenha o `Microsoft.Authorization/roleAssignments/write` permissão como [proprietário](built-in-roles.md#owner) ou [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) no escopo do qual você está tentando atribuir a função.
- Se você receber a mensagem de erro "nenhum mais atribuições de função podem ser criadas (código: RoleAssignmentLimitExceeded) "ao tentar atribuir uma função, tente reduzir o número de atribuições de função, atribuindo funções aos grupos em vez disso. O Azure dá suporte a até **2.000** atribuições de função por assinatura.

## <a name="problems-with-custom-roles"></a>Problemas com funções personalizadas

- Se você precisar que as etapas para criar uma função personalizada, consulte os tutoriais de função personalizada usando [Azure PowerShell](tutorial-custom-role-powershell.md) ou [CLI do Azure](tutorial-custom-role-cli.md).
- Se você não é possível atualizar uma função personalizada existente, verifique se você está atualmente registrado com um usuário que é atribuído a uma função que tenha o `Microsoft.Authorization/roleDefinition/write` permissão como [proprietário](built-in-roles.md#owner) ou [acesso de usuário administrador](built-in-roles.md#user-access-administrator).
- Se não for possível excluir uma função personalizada e obter a mensagem de erro "há fazendo referência à função de atribuições de função existentes (código: RoleDefinitionHasAssignments) ", em seguida, há as atribuições de função ainda usando a função personalizada. Remover essas atribuições de função e tente excluir a função personalizada novamente.
- Se você receber a mensagem de erro "o limite de definição do função excedido. Sem as definições de função podem ser criadas (código: RoleDefinitionLimitExceeded) "ao tentar criar uma nova função personalizada, exclua as funções personalizadas que não estão sendo usadas. O Azure dá suporte a até **2.000** funções personalizadas em um locatário.
- Se você receber um erro semelhante a "o cliente tem permissão para executar a ação 'Microsoft.Authorization/roleDefinitions/write' no escopo '/ assinaturas / {subscriptionid}', no entanto, a assinatura vinculada não foi encontrada" ao tentar atualizar uma função personalizada, verifique Se um ou mais [escopos atribuíveis](role-definitions.md#assignablescopes) ter sido excluída no locatário. Se o escopo foi excluído, em seguida, crie um tíquete de suporte pois não há nenhuma solução de autoatendimento disponível no momento.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Recuperar RBAC quando as assinaturas são movidas entre locatários

- Se você precisar que as etapas sobre como transferir uma assinatura para um anúncio do Azure diferentes locatário, consulte [transferir a propriedade de uma assinatura do Azure para outra conta](../billing/billing-subscription-transfer.md).
- Se você transferir uma assinatura para um AD do Azure diferente locatário, todas as atribuições de função são excluídas permanentemente do locatário do AD do Azure de origem e não são migradas para o locatário do AD do Azure de destino. Você precisa recriar as atribuições de função no locatário de destino.
- Se você for um Azure AD Administrador Global e você não tem acesso a uma assinatura depois que ele foi movido entre locatários, use o **Access management para recursos do Azure** ativar/desativar temporariamente [elevar seu acesso](elevate-access-global-admin.md) para obter acesso à assinatura.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemas com os administradores de serviço ou coadministradores

- Se você estiver tendo problemas com o administrador de serviços ou coadministradores, consulte [adicionar ou alterar os administradores de assinatura do Azure](../billing/billing-add-change-azure-subscription-administrator.md) e [funções de administrador de assinatura clássico, funções de RBAC do Azure e Azure AD funções de administrador](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Acesso negado ou erros de permissão

- Se você receber o erro de permissões "o cliente com a id de objeto não tem autorização para executar a ação no escopo (código: AuthorizationFailed) "ao tentar criar um recurso, verifique se você está atualmente registrado com um usuário que é atribuído a uma função que tenha permissão de gravação para o recurso no escopo selecionado. Por exemplo, para gerenciar máquinas virtuais em um grupo de recursos, você deve ter o [colaborador da máquina Virtual](built-in-roles.md#virtual-machine-contributor) função no grupo de recursos (ou escopo pai). Para obter uma lista das permissões para cada função interna, consulte [funções internas para recursos do Azure](built-in-roles.md).
- Se você receber o erro de permissões "Você não tem permissão para criar uma solicitação de suporte" ao tentar criar ou atualizar um tíquete de suporte, verifique se você está atualmente registrado com um usuário que é atribuído a uma função que tenha o `Microsoft.Support/supportTickets/write` permissão, como [Colaborador de solicitação de suporte](built-in-roles.md#support-request-contributor).

## <a name="rbac-changes-are-not-being-detected"></a>Não estão sendo detectadas alterações de RBAC

Às vezes, o Azure Resource Manager armazena em cache configurações e dados para melhorar o desempenho. Ao criar ou excluir atribuições de função, poderá demorar até 30 minutos para que as alterações tenham efeito. Se estiver usando o portal do Azure, o Azure PowerShell ou a CLI do Azure, será possível forçar uma atualização das alterações de atribuição de função, saindo e entrando novamente. Se estiver fazendo alterações de atribuição de função com chamadas à API REST, poderá forçar uma atualização atualizando o token de acesso.

## <a name="web-app-features-that-require-write-access"></a>Recursos de aplicativo Web que exigem acesso para gravação

Se você conceder a um usuário o acesso somente leitura a um aplicativo Web, para sua surpresa, alguns recursos estarão desabilitados. As funcionalidades de gerenciamento a seguir exigem o acesso de **gravação** em um aplicativo Web (Colaborador ou Proprietário) e não estão disponíveis em um cenário somente leitura.

* Comandos (como iniciar, parar, etc.)
* Alterar configurações como configuração geral, configurações de escala, configurações de backup e configurações de monitoramento.
* Acessar credenciais de publicação e outros segredos como configurações de aplicativos e cadeias de conexão.
* Logs de streaming
* Configuração dos logs de diagnóstico
* Console (prompt de comando)
* Ativo e implantações recentes (para a implantação contínua do git local)
* Gasto estimado
* Testes da Web
* Rede virtual (somente visível para um leitor se uma rede virtual foi anteriormente configurada por um usuário com acesso para gravação).

Se você não conseguir acessar nenhum desses blocos, precisará solicitar ao administrador o acesso de Colaborador ao aplicativo Web.

## <a name="web-app-resources-that-require-write-access"></a>Recursos de aplicativos Web que exigem acesso para gravação

Os aplicativos Web são complicados pela presença de alguns recursos diferentes que interagem. Aqui está um grupo de recursos típico com alguns sites:

![Grupo de recursos do aplicativo Web](./media/troubleshooting/website-resource-model.png)

Como resultado, se você conceder a alguém o acesso somente ao aplicativo Web, muitas das funcionalidades na folha do site no portal do Azure estarão desabilitadas.

Estes itens exigem acesso para **gravação** no **Plano do Serviço de Aplicativo** que corresponde ao seu site:  

* Exibindo o tipo de preço do aplicativo Web (Gratuito ou Standard)  
* Configuração de escala (número instâncias, tamanho da máquina virtual, configurações de escalonamento automático)  
* Cotas (armazenamento, largura de banda, CPU)  

Estes itens exigem acesso para **gravação** no **Grupo de recursos** inteiro que contém o seu site:  

* Associações e Certificados SSL (os certificados SSL podem ser compartilhados entre sites no mesmo grupo de recursos e localização geográfica)  
* Regras de alerta  
* Configurações de autoescala  
* Componentes do Application insights  
* Testes da Web  

## <a name="virtual-machine-features-that-require-write-access"></a>Recursos da máquina virtual que exigem acesso para gravação

Semelhante aos aplicativos Web, alguns recursos na folha da máquina virtual exigem acesso para gravação à máquina virtual ou a outros recursos no grupo de recursos.

As máquinas virtuais são relacionadas a nomes de domínio, redes virtuais, contas de armazenamento e regras de alerta.

Estes itens exigem acesso para **gravação** na **Máquina virtual**:

* Pontos de extremidade  
* Endereços IP  
* Discos  
* Extensões  

Estes exigem acesso para **gravação** tanto na **Máquina virtual** quanto no **Grupo de recursos** (juntamente com o Nome de domínio) encontrados em:  

* Conjunto de disponibilidade  
* Conjunto de balanceamento de carga  
* Regras de alerta  

Se você não conseguir acessar nenhum desses blocos, solicite ao administrador o acesso de Colaborador ao Grupo de recursos.

## <a name="azure-functions-and-write-access"></a>Azure Functions e acesso para gravação

Alguns recursos do [Azure Functions](../azure-functions/functions-overview.md) exigem acesso de gravação. Por exemplo, se um usuário receber a função Leitor, ele não poderá exibir as funções em um aplicativo de funções. O portal exibirá **(Sem acesso)**.

![Aplicativos de funções sem acesso](./media/troubleshooting/functionapps-noaccess.png)

Um leitor pode clicar na guia **Recursos da plataforma** e, em seguida, clicar em **Todas as configurações** para exibir algumas configurações relacionadas a um aplicativo de funções (semelhante a um aplicativo Web), mas não pode modificar essas configurações.

## <a name="next-steps"></a>Próximas etapas
* [Gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure](role-assignments-portal.md)
* [Exibir logs de atividades para alterações de RBAC para recursos do Azure](change-history-report.md)

