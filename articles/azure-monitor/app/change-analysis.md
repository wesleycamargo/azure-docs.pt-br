---
title: Aplicativo do Azure Monitor alterar análise - descobrir alterações que possam afetar a problemas de site ativo/interrupções com o aplicativo do Azure Monitor alteram análise | Microsoft Docs
description: Solucionar problemas de site ao vivo do aplicativo em serviços de aplicativo do Azure com análise de alterações de aplicativo do Azure Monitor
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: b04bd57c66b52e9a2c14d43c9e89d7c54fc48ae2
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415825"
---
# <a name="azure-monitor-application-change-analysis-public-preview"></a>Análise de alterações de aplicativo de Monitor do Azure (visualização pública)

Quando ocorre uma problema/interrupção do site ativo, determinar a causa raiz rapidamente é fundamental. Padrão de soluções de monitoramento pode ajudar você a identificar rapidamente o que há um problema e muitas vezes até mesmo componente que está falhando. Mas isso não será sempre levar a uma explicação imediata por que a falha está ocorrendo. Seu site trabalhou cinco minutos atrás, agora ele está quebrado. O que mudou nos últimos cinco minutos? Esta é a pergunta que o novo recurso do Azure Monitor, análise de alterações de aplicativo é projetado para responder. Criando o poder da [grafo de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview) análise de alterações de aplicativo fornece informações sobre as alterações de aplicativo do serviço de aplicativo do Azure para aumentar a observação e reduzir o MTTR (tempo médio para reparo).

> [!IMPORTANT]
> Análise de alteração de aplicativo do Azure Monitor está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-do-i-use-application-change-analysis"></a>Como posso usar a análise de alterações de aplicativo?

Análise de alteração de aplicativo do Azure Monitor atualmente é integrado ao autoatendimento **diagnosticar e solucionar problemas** experiência, que podem ser acessado da **visão geral** seção do serviço de aplicativo do Azure aplicativo:

![Visão geral da captura de tela do serviço de aplicativo do Azure com as caixas vermelhas em torno do botão visão geral de página e diagnosticar e resolver o botão de problemas](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis"></a>Habilitar análise de alterações

1. Selecione **disponibilidade e desempenho**

    ![captura de tela de opções de solução de problemas de desempenho e disponibilidade](./media/change-analysis/availability-and-performance.png)

2. Clique o **aplicativo travar** lado a lado.

   ![Captura de tela com o bloco de falhas do aplicativo](./media/change-analysis/application-crashes-tile.png)

3. Para habilitar **análise de alterações** selecionar **habilitar agora**.

   ![captura de tela de opções de solução de problemas de desempenho e disponibilidade](./media/change-analysis/application-crashes.png)

4. Para tirar proveito de todo o alterar conjunto de funcionalidades de análise **alterar Analysis**, **examinar alterações de código de**, e **Always on** para **em** e selecione **salvar**.

    ![Captura de tela de habilitar o serviço de aplicativo do Azure alterar a interface do usuário de análise](./media/change-analysis/change-analysis-on.png)

    Se **análise de alterações** é habilitado, você será capaz de detectar alterações no nível do recurso. Se **examinar alterações de código de** é habilitado, você verá também os arquivos de implantação e as alterações de configuração do site. Habilitando **Always on** otimizará a desempenho da verificação de alteração, mas pode incorrer em custos adicionais de uma perspectiva de cobrança.

5.  Depois que tudo estiver habilitada, selecionando **diagnosticar e solucionar problemas** > **desempenho e disponibilidade** > **aplicativo travar** permitirá que você acesse a experiência de análise de alteração. O gráfico será summerize o tipo de alterações que ocorreram ao longo do tempo, juntamente com detalhes sobre essas alterações:

     ![Captura de tela da exibição de comparação de alteração](./media/change-analysis/change-view.png)

## <a name="troubleshooting"></a>solução de problemas

### <a name="unable-to-fetch-change-analysis-information"></a>Não é possível buscar informações de análise de alterações.

Esse é um problema temporário com a experiência de entrada de visualização atual. A solução alternativa consiste em definir uma marca oculta em seu aplicativo web e, em seguida, atualizar a página:

   ![Captura de tela da marca de alteração ocultada](./media/change-analysis/hidden-tag.png)

Para definir a marca ocultada usando o PowerShell:

1. Abra o Azure Cloud Shell:

    ![Captura de tela de alteração do Azure Cloud Shell](./media/change-analysis/cloud-shell.png)

2. Altere o tipo de shell para PowerShell:

   ![Captura de tela de alteração do Azure Cloud Shell](./media/change-analysis/choose-powershell.png)

3. Execute o comando a seguir:

```powershell
$webapp=Get-AzWebApp -Name <name_of_your_webapp>
$tags = $webapp.Tags
$tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
```

> [!NOTE]
> Depois que a marca ocultada é adicionada, você ainda precisa inicialmente aguardar até quatro horas para ser capaz de exibir primeiro a alterações. Isso ocorre porque o freqeuncy de 4 horas que o serviço de análise de alteração usa para verificar seu aplicativo web enquanto os limita o impacto no desempenho da verificação.

## <a name="next-steps"></a>Próximas etapas

- Melhorar o monitoramento dos serviços de aplicativo do Azure [, permitindo que os recursos do Application Insights](azure-web-apps.md) do Azure Monitor.
- Aprimorar seu entendimento do [grafo de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview) que ajuda a aplicativo do Azure Monitor power alterar análise. 
