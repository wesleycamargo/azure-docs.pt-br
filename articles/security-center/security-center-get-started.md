---
title: "Guia de início rápido da Central de Segurança do Azure | Microsoft Docs"
description: "Este artigo ajuda você a se familiarizar rapidamente com a Central de Segurança do Azure, pois o orienta em relação aos componentes de monitoramento da segurança e gerenciamento de políticas, além de conduzi-lo às próximas etapas."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 0ca7382ed64fd548f8a086893ea2e3187dd26929
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017


---
# <a name="azure-security-center-quick-start-guide"></a>Guia de início rápido da Central de Segurança do Azure
Este artigo ajuda você a se familiarizar rapidamente com a Central de Segurança do Azure, pois o orienta em relação aos componentes de monitoramento da segurança e gerenciamento de políticas da Central de Segurança.

> [!NOTE]
> Este artigo apresenta o serviço usando uma implantação de exemplo. Ele não é um guia passo a passo.
>
>

## <a name="prerequisites"></a>Pré-requisitos
Para começar a usar a Central de Segurança, você deve ter uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, pode se inscrever em uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

A camada gratuita da Central de Segurança é habilitada automaticamente com sua assinatura e fornece visibilidade sobre o estado de segurança de seus recursos do Azure. Ela fornece gerenciamento de política de segurança básica, recomendações de segurança e integração com produtos e serviços de segurança de parceiros do Azure.

Você acessar a Central de Segurança pelo [portal do Azure](https://azure.microsoft.com/features/azure-portal/). Para saber mais sobre o portal do Azure, confira a [documentação do portal](https://azure.microsoft.com/documentation/services/azure-portal/).

## <a name="permissions"></a>Permissões
Na Central de Segurança, você vê apenas informações relacionadas a um recurso do Azure quando está atribuído a uma função de Proprietário, Colaborador ou Leitor para a assinatura ou grupo de recursos a que o recurso pertence. Confira [Permissões na Central de Segurança do Azure](security-center-permissions.md) para saber mais sobre as funções e as ações permitidas na Central de Segurança.

## <a name="data-collection"></a>Coleta de dados
A Central de Segurança coleta dados de suas máquinas virtuais (VMs) para avaliar o estado de sua segurança, fornecer recomendações de segurança e alertar sobre ameaças. Quando você acessa pela primeira vez a Central de Segurança, a coleta de dados é habilitada em todas as VMs em sua assinatura. A coleta de dados é recomendada, mas você pode recusar desativando-a na política da Central de Segurança.

As etapas a seguir descrevem como acessar e usar os componentes da Central de Segurança. Nestas etapas, mostramos como desativar a coleta de dados se você optar pelo cancelamento.

## <a name="access-security-center"></a>Acessar a Central de Segurança
No Portal, execute estas etapas para acessar a Central de Segurança:

1. No menu **Microsoft Azure**, selecione **Central de Segurança**.

   ![Menu do Azure][1]
2. Se você estiver acessando a Central de Segurança pela primeira vez, a folha **Bem-vindo** será aberta. Selecione **Iniciar a Central de Segurança** para abrir a folha **Central de Segurança** e habilitar a coleta de dados.
   ![Tela de boas-vindas][10]
3. Depois que você iniciar a Central de Segurança na folha Bem-vindo ou no menu do Microsoft Azure, a folha **Central de Segurança** será aberta. Para facilitar o acesso à folha **Central de segurança** no futuro, selecione opção **Fixar folha no painel** (canto superior direito).
   ![Fixar a folha à opção de painel][2]

## <a name="use-security-center"></a>Usar a Central de Segurança
Você pode configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure. Vamos configurar uma política de segurança para sua assinatura:

1. Na folha **Central de Segurança**, selecione o bloco **Política**.
   ![Política de segurança][3]
2. Na folha **Política de segurança - definir política por assinatura ou grupo de recursos** , selecione uma assinatura.
3. Na folha **Política de segurança**, a **Coleta de dados** é habilitada para coletar os registros automaticamente. A extensão de monitoramento é provisionada em todas as VMs atuais e novas na assinatura. (Você pode recusar a coleta de dados definindo a **Coleta de dados** para **Desativada**, mas isso impedirá que a Central de Segurança forneça recomendações e alertas de segurança.)
4. Na folha **Política de segurança**, selecione **Escolher uma conta de armazenamento por região**. Para cada região em que você tiver VMs em execução, escolha a conta de armazenamento na qual os dados coletados dessas VMs serão armazenados. Se você não escolher uma conta de armazenamento para cada região, uma conta de armazenamento será criada e colocada no grupo de recursos securitydata. Os dados coletados são isolados logicamente dos dados de outros clientes por motivos de segurança.

   > [!NOTE]
   > É recomendável que você habilite a coleta de dados e escolha uma conta de armazenamento no nível da assinatura primeiro. As políticas de segurança podem ser definidas no nível da assinatura do Azure e no nível do grupo de recursos, mas a configuração da conta de armazenamento e da coleta de dados ocorre apenas no nível da assinatura.
   >
   >
5. Na folha **Política de segurança**, selecione **Política de prevenção**. Isso abre a folha **Política de prevenção**.
   ![Política de prevenção][4]
6. Na folha **Política de prevenção**, ative as recomendações que você deseja ver como parte de sua política de segurança. Exemplos:

   * Definir as **Atualizações do sistema** como **Ativado** verificará todas as máquinas virtuais com suporte quanto às atualizações do SO que faltam.
   * Definir as **Vulnerabilidade do SO** para **Ativado** verificará as máquinas virtuais com suporte para identificar as configurações do SO que poderiam tornar a máquina virtual mais vulnerável a ataques.

### <a name="view-recommendations"></a>Exibir recomendações
1. Volte para a folha **Central de Segurança** e selecione o bloco **Recomendações**. A Central de Segurança analisa periodicamente o estado de segurança de seus recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, cria recomendações na folha **Recomendações** .
   ![Recomendações na Central de Segurança do Azure][5]
2. Selecione uma recomendação na folha **Recomendação** para exibir mais informações e/ou tomar medidas para resolver o problema.

### <a name="view-the-security-state-of-your-resources"></a>Exiba o estado de segurança de seus recursos
1. Volte para a folha **Central de segurança** . A seção **Prevenção** do painel contém indicadores do estado da segurança para máquinas virtuais, rede, dados e aplicativos.
2. Selecione **Computação** para exibir mais informações. A folha **Computação** será aberta mostrando três guias:

  - **Visão geral** - Contém recomendações de monitoramento e da máquina virtual.
  - **Máquinas virtuais** - Lista todas as máquinas virtuais e seu estado de segurança atual.
  - **Serviços de nuvem** - Lista as funções da Web e de trabalho monitoradas pela Central de Segurança.

    ![O bloco de integridade de recursos na Central de Segurança do Azure][6]

3. Na guia **Visão geral**, selecione uma recomendação em **RECOMENDAÇÕES DA MÁQUINA VIRTUAL** para exibir mais informações e/ou tomar medidas para configurar os controles necessários.
4. Na guia **Máquinas virtuais**, selecione uma VM para exibir detalhes adicionais.

### <a name="view-security-alerts"></a>Exibir alertas de segurança
1. Volte para a folha **Central de Segurança** e selecione o bloco **Alertas de segurança**. A folha **Alertas de segurança** exibe uma lista de alertas. A análise da Central de Segurança dos seus logs de segurança e da atividade da rede gera esses alertas. Os alertas das soluções de parceiro integradas estão incluídos.
   ![Alertas na Central de Segurança do Azure][7]

   > [!NOTE]
   > Os alertas de segurança só estarão disponíveis se a camada Standard da Central de Segurança estiver habilitada. Uma avaliação gratuita de 60 dias da camada Standard está disponível. Confira as [Próximas etapas](#next-steps) para obter informações sobre como obter a camada Standard.
   >
   >
2. Selecione um alerta para exibir mais informações. Neste exemplo, vamos selecionar **Binário do sistema modificado descoberto**. Isso abre folhas que fornecem detalhes adicionais sobre o alerta.
   ![Detalhes de alerta de segurança na Central de Segurança do Azure][8]

### <a name="view-the-health-of-your-partner-solutions"></a>Exiba a integridade de suas soluções de parceiros:
1. Volte para a folha **Central de segurança** . O bloco **Soluções de parceiros** permite monitorar rapidamente o status da integridade de suas soluções de parceiros integradas em sua assinatura do Azure.
2. Selecione o bloco **Soluções de parceiros** . Uma folha será aberta e exibirá uma lista de suas soluções de parceiros conectadas à Central de Segurança.
   ![Soluções de parceiros][9]
3. Selecione uma solução de parceiro. Neste exemplo, vamos selecionar a solução **QualysVa1** .  Uma folha será aberta e mostrará o status da solução de parceiro e dos recursos associados a ela. Selecione **Console da solução** para abrir a experiência de gerenciamento do parceiro para essa solução.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você foi apresentado aos componentes de monitoramento de segurança e de gerenciamento de políticas da Central de Segurança. Agora que você está familiarizado com a Central de Segurança, tente as seguintes etapas:

* Configure uma política de segurança para sua assinatura do Azure. Para saber mais, confira [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md).
* Use as recomendações na Central de Segurança para ajudar a proteger os recursos do Azure. Para saber mais, confira [Gerenciando recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md).
* Confira e gerencie os alertas de segurança atuais. Para saber mais, confira [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md).
* Saiba mais sobre os [recursos avançados de detecção de ameaça](security-center-detection-capabilities.md) que vêm com a [camada Standard](security-center-pricing.md) da Central de Segurança. A camada Standard é oferecida gratuitamente nos 60 primeiros dias.
* Se você tiver dúvidas sobre como usar a Central de Segurança, confira as [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md).

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/prevention-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png

