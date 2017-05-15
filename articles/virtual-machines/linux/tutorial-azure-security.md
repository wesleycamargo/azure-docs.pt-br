---
title: "Segurança da VM Linux com a Central de Segurança do Azure | Microsoft Docs"
description: "Tutorial – Segurança da VM com a Central de Segurança do Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/01/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 4c680ee63e1c2d8e858c725adc42bbcbc49e4045
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017

---
# <a name="monitor-vm-security-with-the-azure-security-center"></a>Monitorar a segurança da VM com a Central de Segurança do Azure

A Central de Segurança do Azure ajuda você a obter visibilidade da configuração de recursos do Azure relacionada às práticas de segurança. Ela também fornece um monitoramento de segurança integrado, que pode detectar ameaças que, de outro modo, podem passar despercebidas. Este tutorial fornece uma breve visão geral da Central de Segurança do Azure e descreve como usá-la com máquinas virtuais do Azure.   

## <a name="security-center-overview"></a>Visão geral da Central de Segurança

A Central de Segurança do Azure ajuda a identificar possíveis problemas de configuração da VM e ameaças de segurança direcionadas. Alguns exemplos incluem a identificação de VMs com grupos de segurança de rede ausentes, discos não criptografados e ataques de RDP de força bruta. Essas informações são apresentadas no painel da Central de Segurança do Azure em gráficos fáceis de ler.

O painel da Central de Segurança do Azure pode ser acessado clicando em **Central de Segurança** no painel de navegação esquerdo do portal do Azure. O painel fornece uma visão de alto nível da integridade de recursos, dos alertas de segurança e das recomendações de configuração. Aqui, é possível exibir a integridade da segurança de seu ambiente do Azure, encontrar uma lista das recomendações atuais e exibir o estado atual dos alertas de ameaça. Cada um desses gráficos de alto nível pode ser expandido, que fornece mais detalhes sobre a área de foco.

![Painel do ASC](./media/tutorial-azure-security/asc-dash.png)

A Central de Segurança do Azure se estende para além da descoberta de dados, fornecendo recomendações para os problemas detectados. Por exemplo, se uma VM tiver sido implantada sem um grupo de segurança de rede anexado, será criada uma recomendação que inclui as etapas de correção. Essas recomendações também fornecem a automação de correção sem sair do contexto da Central de Segurança do Azure.  

![Recomendações](./media/tutorial-azure-security/recommendations.png)

## <a name="configure-data-collection"></a>Configurar a coleta de dados

Antes que você possa obter visibilidade das configurações de segurança da VM, a coleta de dados da Central de Segurança do Azure precisa ser configurada. Isso envolve a habilitação da coleta de dados e a criação de uma conta de armazenamento do Azure para manter os dados coletados. 

1. No painel da Central de Segurança do Azure, clique em **Política de Segurança** e selecione sua assinatura. 
2. Em **Coleta de dados**, selecione *Ativada*.
3. Clique em **Escolher uma conta de armazenamento** e crie uma nova conta de armazenamento. Selecione **OK** quando tiver concluído.
4. Clique em **Salvar** na folha **Política de Segurança**. 

Quando essa ação for concluída, o agente de coleta de dados da Central de Segurança do Azure será instalado em todas as máquinas virtuais e a coleta de dados será iniciada. 

## <a name="configure-security-policy"></a>Configurar a política de segurança

Uma política de segurança define os itens da política de segurança para os quais os dados são coletados e as recomendações são feitas. Por padrão, os recursos do Azure são avaliados em relação a todos os itens da política. Itens individuais da política podem ser desabilitados globalmente em todos os recursos do Azure ou desabilitados por grupo de recursos. Essa configuração oferece a capacidade de aplicar diferentes políticas de segurança a diferentes conjuntos de recursos do Azure. Para obter informações detalhadas sobre as políticas de segurança da Central de Segurança do Azure, consulte [Definir políticas de segurança na Central de Segurança do Azure](../../security-center/security-center-policies.md). 

Para configurar uma política de segurança para todos os recursos do Azure:

1. No painel da Central de Segurança do Azure, clique em **Política de Segurança** e selecione sua assinatura. 
2. Clique em **Política de prevenção**.
3. Habilite ou desabilite os itens de políticas que precisam ser aplicados a todos os recursos do Azure.
4. Toque em **OK** quando terminar.
5. Clique em **Salvar** na folha **Política de Segurança**. 

Para configurar uma política para um grupo de recursos específico, siga as mesmas etapas, mas em vez de selecionar a assinatura na folha da política de segurança, selecione um grupo de recursos. Ao configurar a política, selecione *Exclusiva* em **Herança**. Se você desejar desabilitar a coleta de dados em um grupo de recursos específico, essa configuração também pode ser feita aqui.

No exemplo a seguir, uma política exclusiva foi criada para o grupo de recursos chamado *myResoureGroup*. Nessa política, tanto a criptografia de disco quanto as recomendações de firewall do aplicativo Web foram desabilitadas.

![Política exclusiva](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Exibir a integridade da configuração da VM

Depois que a coleta de dados for habilitada e uma política de segurança configurada, a Central de Segurança do Azure começará a fornecer alertas e recomendações. Conforme as VMs são implantadas, o agente de coleta de dados é instalado e a Central de Segurança do Azure é populada com dados dessas novas VMs. Para obter informações detalhadas sobre a integridade da configuração da VM, consulte [Protegendo suas máquinas virtuais na Central de Segurança do Azure](../../security-center/security-center-virtual-machine-recommendations.md). 

Conforme os dados são coletados, a integridade de recursos de cada VM e os recursos do Azure relacionados são agregados e apresentados em um gráfico fácil de ler. Para exibir a integridade de recursos, retorne ao painel da Central de Segurança do Azure. Em **Integridade de recursos de segurança**, clique em **Computação**. Por fim, na folha **Computação**, clique em **Máquinas virtuais**. Essa exibição fornece um resumo do status de configuração de todas as VMs.

![Integridade de computação](./media/tutorial-azure-security/compute-health.png)

A seleção de cada VM exibe todas as recomendações referentes a ela. As recomendações são detalhadas na próxima seção deste tutorial.

## <a name="remediate-configuration-issues"></a>Corrigir problemas de configuração

Depois que a Central de Segurança do Azure começar a ser populada com dados de configuração, as recomendações serão feitas de acordo com a política de segurança configurada. Por exemplo, se uma VM tiver sido configurada sem um grupo de segurança de rede associado, uma recomendação será feita para a criação de um. Para ver uma lista de todas as recomendações: 

1. No painel da Central de Segurança do Azure, clique em **Recomendações**.
3. Selecione uma recomendação específica e uma folha será aberta com uma lista de todos os recursos para os quais a recomendação se aplica.
4. Selecione um recurso específico que você deseja abordar.
5. Siga as instruções na tela para obter as etapas de correção. 

Em muitos casos, a Central de Segurança do Azure fornece etapas práticas para trabalhar com a recomendação sem sair do contexto da Central de Segurança do Azure. Por exemplo, no exemplo a seguir, foi detectado um NSG com uma regra de entrada irrestrita. Nessa recomendação, o botão **Editar regras de entrada** pode ser selecionado, que fornece a interface do usuário apropriada necessária para modificar a regra. 

![Recomendações](./media/tutorial-azure-security/remediation.png)

À medida que as recomendações são corrigidas, elas são marcadas como resolvidas. 

## <a name="view-detected-threats"></a>Exibir as ameaças detectadas

Além das recomendações de configuração de recursos, a Central de Segurança do Azure também fornece alertas de detecção de ameaças. O recurso de alertas de segurança agrega os dados coletados de cada VM, os logs de rede do Azure e as soluções de parceiros conectadas para detectar ameaças de segurança aos recursos do Azure. Para obter informações detalhadas sobre as funcionalidades de detecção de ameaças da Central de Segurança do Azure, consulte [Funcionalidades de detecção da Central de Segurança do Azure](../../security-center/security-center-detection-capabilities.md).

O recurso de alertas de segurança exige que o tipo de preço da Central de Segurança do Azure seja aumentado de *Gratuito* para *Standard*. Ao fazer isso, uma **avaliação gratuita** de 30 dias estará disponível. Para alterar o tipo de preço:  

1. No painel da Central de Segurança do Azure, clique em **Política de Segurança** e selecione sua assinatura.
2. Clique em **Tipo de preço**.
3. Selecione o novo tipo e clique em **Selecionar**.
5. Clique em **Salvar** na folha **Política de Segurança**. 

Quando for habilitado, o gráfico de alertas de segurança começará a ser populado conforme forem detectadas ameaças de segurança.

![Alertas de segurança](./media/tutorial-azure-security/security-alerts.png)

Selecione um alerta para exibir informações como uma descrição da ameaça, a hora de detecção, tentativas de ameaça e a correção recomendada. Neste exemplo, um ataque de RDP de força bruta foi detectado com 294 tentativas de RDP com falha e uma solução recomendada é fornecida.

![Ataque de RDP](./media/tutorial-azure-security/rdp-attack.png)
