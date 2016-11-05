---
title: Segurança de Automação do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da segurança da automação e dos diferentes métodos de autenticação disponíveis para Contas de Automação na Automação do Azure.
services: automation
documentationcenter: ''
author: MGoedtel
manager: jwhit
editor: tysonn
keywords: segurança de automação, automação segura

ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2016
ms.author: magoedte

---
# Segurança da Automação do Azure
A Automação do Azure permite automatizar tarefas em relação a recursos no Azure, locais e com outros provedores de nuvem, como AWS (Amazon Web Services). Para que um runbook execute as ações necessárias, ele deve ter permissões para acessar os recursos com segurança e apenas com os direitos mínimos necessários na assinatura. Este artigo abordará os vários cenários de autenticação com suporte na Automação do Azure e mostrará como iniciar baseado nos ambientes que você precisa gerenciar.

## Visão geral da Conta de Automação
Ao iniciar a Automação do Azure pela primeira vez, você deve criar pelo menos uma conta de Automação. As contas de Automação permitem isolar seus recursos de Automação (runbooks, ativos, configurações) dos recursos contidos em outras contas de Automação. Você pode usar contas de Automação para separar os recursos em ambientes lógicos separados. Por exemplo, você pode usar uma conta para desenvolvimento, outra para produção e outra para seu ambiente local. Uma conta de Automação do Azure é diferente de sua conta da Microsoft ou de contas criadas em sua assinatura do Azure.

Os recursos de Automação para cada conta de Automação estão associados a uma única região do Azure, mas as contas de Automação podem gerenciar recursos em qualquer região. O principal motivo para criar contas de Automação em regiões diferentes seria se você tiver políticas que exijam que dados e recursos sejam isolados em uma região específica.

> [!NOTE]
> As contas de Automação e os recursos que eles contêm, que são criados no portal do Azure, não podem ser acessados no portal clássico do Azure. Se quiser gerenciar essas contas ou os recursos com o Windows PowerShell, você deve usar os módulos do Azure Resource Manager.
> 
> 

Todas as tarefas que podem ser executadas em relação a recursos usando o Azure Resource Manager e os cmdlets do Azure na Automação do Azure devem ser autenticadas no Azure usando a autenticação baseada em credenciais de identidade organizacional do Azure Active Directory. A autenticação baseada em certificado era o método de autenticação original do modo de Gerenciamento de Serviços do Azure, mas sua instalação era complicada. A autenticação no Azure com o usuário do Azure AD foi introduzida em 2014 não apenas para simplificar o processo de configuração de uma conta de Autenticação, mas também para dar suporte à capacidade de executar a autenticação não interativa no Azure com uma única conta de usuário que funcionava com o Azure Resource Manager e recursos clássicos.

Atualmente, quando você cria uma nova conta de Automação no portal do Azure, ela cria automaticamente:

* A conta Executar como, que cria uma nova entidade de serviço no Azure Active Directory, um certificado, e atribui o controle de acesso baseado em função de Colaborador (RBAC), que será usado para gerenciar os recursos do Resource Manager usando runbooks.
* A conta Executar como Clássica, ao carregar um certificado de gerenciamento, que será usado para gerenciar o Gerenciamento de Serviços do Azure ou recursos clássicos usando runbooks.

O controle de acesso baseado em função está disponível com o Azure Resource Manager para conceder ações permitidas a uma conta de usuário do Azure AD e uma conta Executar como e autenticar essa entidade de serviço. Leia [o artigo Controle de acesso baseado em função da Automação do Azure](automation-role-based-access-control.md) para obter mais informações que o ajudarão a desenvolver seu modelo para gerenciar permissões de Automação.

Runbooks em execução em um Hybrid Runbook Worker em seu datacenter ou em relação a serviços de computação no AWS não podem usar o mesmo método que é normalmente usado para autenticar runbooks em recursos do Azure. Isso ocorre porque esses recursos estão em execução fora do Azure e, assim, exigirão suas próprias credenciais de segurança definidas na Automação para autenticar em relação aos recursos que acessarão localmente.

## Métodos de autenticação
A tabela a seguir resume os diferentes métodos de autenticação para cada ambiente com suporte na Automação do Azure e o artigo que descreve como configurar a autenticação para seus runbooks.

| Método | Ambiente | Artigo |
| --- | --- | --- |
| Conta de Usuário do Azure AD |Azure Resource Manager e o Gerenciamento de Serviços do Azure |[Autenticar Runbooks com uma conta de Usuário do Azure AD](automation-sec-configure-aduser-account.md) |
| Conta Executar como do Azure |Gerenciador de Recursos do Azure |[Autenticar runbooks com uma conta Executar como do Azure](automation-sec-configure-azure-runas-account.md) |
| Conta Executar como do Azure Clássico |Gerenciamento do Serviço do Azure |[Autenticar runbooks com uma conta Executar como do Azure](automation-sec-configure-azure-runas-account.md) |
| Autenticação do Windows |Datacenter local |[Autenticar runbooks para Hybrid Runbook Workers](automation-hybrid-runbook-worker.md) |
| Credenciais do AWS |Amazon Web Services |[Autenticar runbooks com o AWS (Amazon Web Services)](automation-sec-configure-aws-account.md) |

<!---HONumber=AcomDC_0803_2016-->