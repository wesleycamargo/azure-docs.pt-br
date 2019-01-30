---
title: Solução de problemas do RBAC no Azure | Microsoft Docs
description: Solucionar problemas com RBAC (controle de acesso baseado em função) do Azure.
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
ms.date: 01/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: e204beea5bdf72c2ec5ebcf661d3c983a2e0e6b4
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2019
ms.locfileid: "54411230"
---
# <a name="troubleshoot-rbac-in-azure"></a>Solucionar problemas de RBAC no Azure

Este artigo responde a perguntas comuns sobre o RBAC (controle de acesso baseado em função), para que você saiba o que esperar ao usar as funções no portal do Azure e possa solucionar problemas de acesso.

## <a name="problems-with-rbac-role-assignments"></a>Problemas com as atribuições de função RBAC

- Se você não conseguir adicionar uma atribuição de função porque a opção **Adicionar atribuição de função** está desabilitada ou porque recebeu um erro de permissões, verifique se você está usando uma função que tenha a permissão `Microsoft.Authorization/roleAssignments/*` no escopo para o qual está tentando atribuir a função. Se você não tiver essa permissão, verifique com seu administrador de assinatura.
- Se você receber um erro de permissões ao tentar criar um recurso, verifique se você está usando uma função que tenha permissão para criar recursos no escopo selecionado. Por exemplo, talvez você precise ser um Colaborador. Se você não tiver a permissão, verifique com o administrador de assinatura.
- Se você receber um erro de permissões ao tentar criar ou atualizar um tíquete de suporte, verifique se você está usando uma função que tenha a permissão `Microsoft.Support/*`, como [Colaborador de Solicitação de Suporte](built-in-roles.md#support-request-contributor).
- Se você receber um erro informando que o número de atribuições de função foi excedido ao tentar atribuir uma função, tente reduzir o número de atribuições de função, atribuindo funções a grupos. O Azure dá suporte a até **2.000** atribuições de função por assinatura.

## <a name="problems-with-custom-roles"></a>Problemas com funções personalizadas

- Se você não conseguir atualizar uma função personalizada existente, verifique se você tem a permissão `Microsoft.Authorization/roleDefinition/write`.
- Se você não conseguir atualizar uma função personalizada existente, verifique se um ou mais escopos atribuíveis foram excluídos do locatário. A propriedade `AssignableScopes` de uma função personalizada controla [quem pode criar, excluir, atualizar ou exibir a função personalizada](custom-roles.md#who-can-create-delete-update-or-view-a-custom-role).
- Se você receber um erro informando que o limite da definição de função é excedido quando tentar criar uma função, exclua as funções personalizadas que não são usadas. Você também pode tentar consolidar ou reutilizar as funções personalizadas existentes. O Azure dá suporte a até **2.000** funções personalizadas em um locatário.
- Se você não conseguir excluir uma função personalizada, verifique se há uma ou mais atribuições de função ainda usando a função personalizada.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Recuperar RBAC quando as assinaturas são movidas entre locatários

- Se você precisar saber as etapas sobre como transferir uma assinatura para um locatário diferente, confira [Transferir a propriedade de uma assinatura do Azure para outra conta](../billing/billing-subscription-transfer.md).
- Quando você transfere uma assinatura para outro locatário, todas as atribuições de função são excluídas permanentemente do locatário de origem e não são migradas para o locatário de destino. Você precisa recriar as atribuições de função no locatário de destino.
- Se você é um Administrador Global e perdeu o acesso a uma assinatura, use a alternância **Gerenciamento de acesso para recursos do Azure** para [elevar seu acesso](elevate-access-global-admin.md) temporariamente e obter o acesso à assinatura mais uma vez.

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
* [Gerenciar acesso usando o RBAC e o Portal do Azure](role-assignments-portal.md)
* [Exibir logs de atividades para alterações de RBAC](change-history-report.md)

