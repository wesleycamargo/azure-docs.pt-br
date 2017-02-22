---
title: "Perguntas frequentes sobre a Central de Segurança do Azure | Microsoft Docs"
description: "Encontre respostas para perguntas frequentes sobre a Central de Segurança do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 40f8f1b52c39a170a57168db9227a7c2fa069570
ms.openlocfilehash: 466d4a566ebb426f48e8c271e1305b844842d638


---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Perguntas frequentes sobre a Central de Segurança do Azure
Estas perguntas frequentes estão relacionadas à Central de Segurança do Azure, um serviço que ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Microsoft Azure.

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-security-center"></a>O que é a Central de Segurança do Azure?
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

### <a name="how-do-i-get-azure-security-center"></a>Como posso obter a Central de Segurança do Azure?
A Central de Segurança do Azure é habilitada com sua assinatura do Microsoft Azure e pode ser acessada no [portal do Azure](https://azure.microsoft.com/features/azure-portal/). ([Entre no portal](https://portal.azure.com), selecione **Procurar** e role até a **Central de Segurança**).  

## <a name="billing"></a>Cobrança
### <a name="how-does-billing-work-for-azure-security-center"></a>Como funciona o faturamento para a Central de Segurança do Azure?
A Central de Segurança é oferecida em duas camadas: Gratuita e Standard.

A camada Gratuita permite definir políticas de segurança e receber alertas, incidentes e recomendações de segurança que orientam você durante o processo de configuração dos controles necessários. Com a Camada gratuita, você também pode monitorar o estado de segurança de seus recursos do Azure e de soluções de parceiros integradas à sua assinatura do Azure.

A camada Standard oferece os recursos da camada Gratuita mais as detecções avançadas: inteligência de ameaças, análise comportamental, análise de falha e detecção de anomalias. Uma avaliação gratuita de 90 dias da camada Standard está disponível. Para atualizar, selecione Tipo de Preço na [política de segurança](security-center-policies.md#set-security-policies-for-subscriptions). Para saber mais, consulte [Azure Security Center pricing](security-center-pricing.md) (Preços da Central de Segurança do Azure).

## <a name="permissions"></a>Permissões
A Central de Segurança do Azure usa o [RBAC (Controle de Acesso Baseado em Função)](../active-directory/role-based-access-control-configure.md), que fornece [funções internas](../active-directory/role-based-access-built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure.

A Central de Segurança avalia a configuração de seus recursos para identificar problemas de segurança e vulnerabilidades. Na Central de Segurança, você vê apenas as informações relacionadas a um recurso quando for atribuído à função de Proprietário, Colaborador ou Leitor da assinatura ou do grupo de recursos ao qual o recurso pertence.

Confira [Permissões na Central de Segurança do Azure](security-center-permissions.md) para saber mais sobre as funções e as ações permitidas na Central de Segurança.

## <a name="data-collection"></a>Coleta de dados
A Central de Segurança coleta dados das máquinas virtuais para avaliar seu estado de segurança, fornecer recomendações de segurança e alertar sobre ameaças. Quando você acessa pela primeira vez a Central de Segurança, a coleta de dados é habilitada em todas as máquinas virtuais em sua assinatura. A coleta de dados é recomendada, mas você pode recusá-la [desabilitando a coleta de dados](#how-do-i-disable-data-collection) na política da Central de Segurança.

### <a name="how-do-i-disable-data-collection"></a>Como desabilitar a coleta de dados?
Você pode desabilitar a **Coleta de dados** para uma assinatura na política de Segurança a qualquer momento. ([Entre no portal do Azure](https://portal.azure.com), selecione **Procurar**, **Central de Segurança** e **Política**.)  Ao selecionar uma assinatura, uma nova folha será aberta e fornecerá a opção para desligar a **Coleta de dados**. Os Agentes de Monitoramento do Azure são automaticamente removidos das máquinas virtuais existentes da assinatura quando você desativar a coleta de dados.

> [!NOTE]
> As políticas de segurança podem ser definidas no nível da assinatura e do grupo de recursos do Azure, mas é necessário selecionar uma assinatura para desligar a coleta de dados.
>
>

### <a name="how-do-i-enable-data-collection"></a>Como habilitar a coleta de dados?
É possível habilitar a coleta de dados para suas assinaturas do Azure na Política de segurança. Para habilitar a coleta de dados, [entre no portal do Azure](https://portal.azure.com), selecione **Procurar**, **Central de Segurança** e **Política**. Defina a **Coleta de dados** como **Ativada** e configure as contas de armazenamento nas quais você deseja que os dados sejam coletados (veja a pergunta “[Onde meus dados são armazenados?](#where-is-my-data-stored)”). Quando estiver habilitada, a **Coleta de dados** coletará automaticamente as informações de configuração e os eventos de segurança de todas as máquinas virtuais com suporte na assinatura.

> [!NOTE]
> As políticas de segurança podem ser definidas no nível da assinatura do Azure e no nível do grupo de recursos, mas a configuração da coleta de dados ocorre apenas no nível da assinatura.
>
>

### <a name="what-happens-when-data-collection-is-enabled"></a>O que acontece quando a coleta de dados é habilitada?
A coleta de dados é habilitada por meio do agente de monitoramento do Azure e da extensão de monitoramento de segurança do Azure. A extensão de Monitoramento de Segurança do Azure examina várias configurações de segurança relevantes e as envia para os rastreamentos do [ETW (Rastreamento de Eventos para Windows)](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx). Além disso, o sistema operacional cria entradas de log de eventos.  O agente de monitoramento do Azure lê as entradas do registro de eventos e os vestígios de ETW e os copia para sua conta de armazenamento para análise.  Essa é a conta de armazenamento configurada na política de segurança. Para mais informações sobre a conta de armazenamento, consulte a pergunta “[Onde meus dados são armazenados?](#where-is-my-data-stored)”

### <a name="does-the-monitoring-agent-or-security-monitoring-extension-impact-the-performance-of-my-servers"></a>A extensão do Agente de monitoramento ou o Monitoramento de segurança afetam o desempenho do meu servidor?
O agente e a extensão consomem uma quantidade nominal de recursos do sistema e devem causar pouco impacto sobre o desempenho. Para obter mais informações sobre o impacto no desempenho, bem como o agente e a extensão, consulte o [guia de planejamento e operações](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Onde meus dados são armazenados?
Para cada região em que você tiver máquinas virtuais em execução, você deverá escolher a conta de armazenamento na qual os dados coletados dessas máquinas virtuais serão armazenados. Isso facilita para manter os dados na mesma área geográfica para fins de privacidade e soberania de dados. Você pode escolher a conta de armazenamento para uma assinatura na política de segurança. ([Entre no portal do Azure](https://portal.azure.com), selecione **Procurar**, **Central de Segurança** e **Política**.) Ao selecionar uma assinatura, uma nova folha será aberta. Para selecionar uma região, selecione **Escolher contas de armazenamento**. Se você não escolher uma conta de armazenamento para cada região, uma conta de armazenamento será criada e colocada no grupo de recursos securitydata.

> [!NOTE]
> As políticas de segurança podem ser definidas no nível da assinatura do Azure e no nível do grupo de recursos, mas a seleção da região de sua conta de armazenamento ocorre apenas no nível da assinatura.
>
>

Para saber mais sobre as contas de armazenamento e o armazenamento do Azure, consulte a [Documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) e a seção [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md).

## <a name="using-azure-security-center"></a>Como usar a Central de Segurança do Azure
### <a name="what-is-a-security-policy"></a>O que é uma política de segurança?
Uma política de segurança define o conjunto de controles recomendados para os recursos na assinatura ou grupo de recursos especificado. Na Central de Segurança do Azure, você pode definir as políticas para as assinaturas do Azure e grupos de recurso de acordo com os requisitos de segurança de sua empresa e os tipos de aplicativos ou a confidencialidade dos dados de cada assinatura.

Por exemplo, os recursos usados para desenvolvimento ou teste podem ter requisitos de segurança diferentes daqueles usados para aplicativos de produção. Da mesma forma, os aplicativos com dados regulamentados, como PII (informações de identificação pessoal), podem exigir um nível mais alto de segurança. As políticas de segurança habilitadas na Central de Segurança do Azure determinam as recomendações de segurança e o monitoramento. Para saber mais sobre as políticas de segurança, consulte a seção [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md).

> [!NOTE]
> Se houver um conflito entre a política de nível de assinatura e a política de nível de grupo de recursos, a política de nível de grupo de recursos terá precedência.
>
>

### <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?
As políticas de segurança são configuradas para cada grupo de recursos ou assinatura. Para modificar uma política de segurança no nível de assinatura ou no nível de grupo de recursos, você deve ser um Proprietário ou Colaborador dessa assinatura.

Para saber como configurar uma política de segurança, consulte a seção [Como configurar de políticas de segurança na Central de segurança do Azure](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>O que é são recomendações de segurança?
A Central de Segurança do Azure analisa o estado de segurança de seus recursos do Azure. Quando possíveis vulnerabilidades de segurança são identificadas, são criadas recomendações. As recomendações guiam você pelo processo de configuração do controle necessário. Os exemplos abrangem:

* Provisionamento de antimalware para ajudar a identificar e remover software mal-intencionado
* Como configurar [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) e as regras para controlar o tráfego para máquinas virtuais
* Provisionamento de um Firewall do Aplicativo Web para ajudar a proteger contra ataques direcionados aos seus aplicativos Web
* Como implantar atualizações de sistema ausentes
* Endereçamento de configurações do sistema operacional que não coincidem com as linhas de base recomendadas

Somente as recomendações que são habilitadas nas Políticas de segurança são mostradas aqui.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Como é possível ver o estado atual da segurança dos meus recursos do Azure?
Um bloco **Integridade de recursos** da folha da **Central de segurança** mostra a postura de segurança geral do seu ambiente dividido por máquinas virtuais, aplicativos Web e outros recursos. Cada recurso tem um indicador mostrando se possíveis vulnerabilidades de segurança foram identificadas. Clicar no bloco de integridade de recursos exibe os recursos e identifica onde a atenção é requerida ou talvez haja problemas.

### <a name="what-triggers-a-security-alert"></a>O que dispara um alerta de segurança?
A Central de Segurança do Azure automaticamente coleta, analisa e funde os dados de log de seus recursos do Azure, da rede e das soluções de parceiros como antimalware e firewalls. Quando forem detectadas ameaças, é criado um alerta de segurança. Os exemplos abrangem a detecção de:

* As máquinas virtuais comprometidas se comunicam com os endereços IP mal-intencionados conhecidos
* Malware avançado detectado com o relatório de erros do Windows
* Ataques por força bruta contra máquinas virtuais
* Alertas de segurança das soluções de segurança de parceiro integradas, como antimalware ou Firewalls de aplicativo Web

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Qual é a diferença entre ameaças detectadas e alertadas pelo Microsoft Security Response Center versus pela Central de Segurança do Azure?
O MSRC (Microsoft Security Response Center) executa determinado monitoramento de segurança da rede e da infraestrutura do Azure e recebe reclamações de inteligência e abuso de ameaça de terceiros. Quando o MSRC fica ciente de que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal ou que o uso do cliente do Azure não está de acordo com os termos de Uso Aceitável, um gerente de incidentes de segurança notifica o cliente. Normalmente, a notificação ocorre com o envio de um email para os contatos de segurança especificados na Central de Segurança do Azure ou para o proprietário da assinatura do Azure, caso um contato de segurança não seja especificado.

A Central de Segurança é um serviço do Azure que monitora o ambiente do cliente do Azure continuamente e aplica a análise para detectar automaticamente uma ampla gama de atividades potencialmente mal-intencionadas. Essas detecções são exibidas como alertas de segurança no painel da Central de Segurança.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Quais recursos do Azure são monitorados pela Central de Segurança do Azure?
A Central de Segurança do Azure monitora os seguintes recursos do Azure:

* VMs (máquinas virtuais) (incluindo os [Serviços de Nuvem](../cloud-services/cloud-services-choose-me.md))
* Redes Virtuais do Azure
* Serviço do SQL Azure
* Soluções de parceiros integradas com sua assinatura do Azure, como um firewall de aplicativo Web em VMs e no [Ambiente do Serviço de Aplicativo](../app-service/app-service-app-service-environments-readme.md)

## <a name="virtual-machines"></a>Máquinas Virtuais
### <a name="what-types-of-virtual-machines-are-supported"></a>Quais tipos de máquinas virtuais têm suporte?
O monitoramento e as recomendações de integridade de segurança estão disponíveis para VMs (máquinas virtuais) criadas usando os [modelos de implantação clássico e do Gerenciador de Recursos](../azure-classic-rm.md).

VMs do Windows com suporte:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

VMs do Linux com suporte:

* Ubuntu versões 12.04, 14.04, 16.04, 16.10
* Debian versões 7, 8
* CentOS versões 6.\*, 7.*
* Red Hat Enterprise Linux (RHEL) versões 6.\*, 7.*
* SUSE Linux Enterprise Server (SLES) versões 11.SP4+, 12.*
* Oracle Linux versões 6.\*, 7. *

Também há suporte para VMs em execução em um serviço de nuvem. Apenas serviços de nuvem da Web e funções de trabalho em execução em slots de produção são monitorados. Para saber mais sobre o serviço de nuvem, confira [Visão geral dos Serviços de Nuvem](../cloud-services/cloud-services-choose-me.md).

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Por que a Central de Segurança do Azure não reconhece a solução antimalware em execução em minha VM do Azure?
A Central de Segurança do Azure só tem visibilidade de antimalware instalado por meio de extensões do Azure. Por exemplo, a Central de Segurança não será capaz de detectar antimalware que foi pré-instalado em uma imagem fornecida por você ou se você tiver instalado antimalware em suas máquinas virtuais usando seus próprios processos (como sistemas de gerenciamento de configuração).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Por que recebo a mensagem "Dados de verificação ausentes" para minha VM?
Pode levar algum tempo (menos de uma hora) para que os dados de exame sejam populados depois que a Coleta de Dados é habilitada na Central de Segurança do Azure. Os exames não são populados para VMs em um estado parado.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Por que recebo a mensagem "O Agente de VM está ausente?"
O Agente de VM deve ser instalado nas VMs para habilitar a Coleta de Dados. O agente de VM está instalado por padrão nas VMs implantadas do Azure Marketplace. Para obter informações sobre como instalar o Agente da VM em outras VMs, consulte a postagem de blog sobre o [Agente de VM e Extensões](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).



<!--HONumber=Feb17_HO2-->


