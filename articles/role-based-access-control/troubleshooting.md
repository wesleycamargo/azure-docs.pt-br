---
title: Solução de problemas de RBAC no Azure | Microsoft Docs
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
ms.topic: article
ms.date: 03/19/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 557d3330ef155181c050a18b14d31b65ba1f2dcf
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295380"
---
# <a name="troubleshooting-rbac-in-azure"></a>Solução de problemas de RBAC no Azure

Este artigo responde a perguntas comuns sobre o RBAC (controle de acesso baseado em função), para que você saiba o que esperar ao usar as funções no portal do Azure e possa solucionar problemas de acesso. Estas três funções abrangem todos os tipos de recurso:

* Proprietário  
* Colaborador  
* Leitor  

Os Proprietários e Colaboradores têm acesso completo a experiência de gerenciamento, mas um Colaborador não pode conceder acesso a outros usuários ou grupos. As coisas ficam um pouco mais interessantes com a função de Leitor e é para ela que vamos dedicar algum tempo. Para obter informações sobre como conceder acesso, consulte [Gerenciar o acesso usando o RBAC e o portal do Azure](role-assignments-portal.md).

## <a name="app-service"></a>Serviço de Aplicativo
### <a name="write-access-capabilities"></a>Recursos do acesso de gravação
Se você conceder a um usuário o acesso somente leitura a um aplicativo Web, para sua surpresa, alguns recursos estarão desabilitados. As funcionalidades de gerenciamento a seguir exigem o acesso de **gravação** para um aplicativo Web (Colaborador ou Proprietário) e não estarão disponíveis em um cenário somente leitura.

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

### <a name="dealing-with-related-resources"></a>Lidando com recursos relacionados
Os aplicativos Web são complicados pela presença de alguns recursos diferentes que interagem. Aqui encontra-se um grupo de recursos típico com alguns sites:

![Grupo de recursos do aplicativo Web](./media/troubleshooting/website-resource-model.png)

Como resultado, se você conceder a alguém o acesso somente ao aplicativo Web, muitas das funcionalidades na folha do site no portal do Azure estarão desabilitadas.

Estes itens exigem acesso para **gravação** no **Plano do Serviço de Aplicativo** que corresponde ao seu site:  

* Exibindo o tipo de preço do aplicativo Web (Grátis ou Standard)  
* Configuração de escala (número instâncias, tamanho da máquina virtual, configurações de escalonamento automático)  
* Cotas (armazenamento, largura de banda, CPU)  

Estes itens exigem acesso para **gravação** no **Grupo de recursos** inteiro que contém o seu site:  

* Associações e Certificados SSL (os certificados SSL podem ser compartilhados entre sites no mesmo grupo de recursos e localização geográfica)  
* Regras de alerta  
* Configurações de autoescala  
* Componentes do Application insights  
* Testes da Web  

## <a name="azure-functions"></a>Funções do Azure
Alguns recursos do [Azure Functions](../azure-functions/functions-overview.md) exigem acesso de gravação. Por exemplo, se um usuário receber a função Leitor, ele não poderá exibir as funções em um aplicativo de funções. O portal exibirá **(Sem acesso)**.

![Aplicativos de funções sem acesso](./media/troubleshooting/functionapps-noaccess.png)

Um leitor pode clicar na guia **Recursos da plataforma** e, em seguida, clicar em **Todas as configurações** para exibir algumas configurações relacionadas a um aplicativo de funções (semelhante a um aplicativo Web), mas não pode modificar essas configurações.

## <a name="virtual-machine"></a>Máquina virtual
Como muitos aplicativos Web, alguns recursos na folha da máquina virtual requerem o acesso de gravação para a máquina virtual ou a outros recursos no grupo de recursos.

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

## <a name="next-steps"></a>Próximas etapas
* [Gerenciar acesso usando o RBAC e o Portal do Azure](role-assignments-portal.md)
* [Exibir logs de atividades para alterações de RBAC](change-history-report.md)

