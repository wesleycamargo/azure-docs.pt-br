---
title: Solucionar problemas de dimensionamento automático com conjuntos de escala de máquina virtual | Microsoft Docs
description: Solução de problemas de dimensionamento automático com conjuntos de escala de máquina virtual. Compreenda os problemas típicos encontrados e como resolvê-los.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gbowerman
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: windows
ms.devlang: na
ms.topic: article
ms.date: 03/28/2016
ms.author: guybo

---
# Solução de problemas do dimensionamento automático com conjuntos de escala de máquina virtual
**Problema**: você criou uma infraestrutura de dimensionamento automático no Azure Resource Manager usando conjuntos de escala de VM, por exemplo implantando um modelo como este: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale – suas regras de escala estão definidas e ele funciona muito bem, exceto que, independentemente da carga colocada nas VMs, ele não usa dimensionamento automático.

## Etapas para solucionar problemas
Alguns aspectos a serem considerados incluem:

* Quantos núcleos cada VM tem e você está carregando cada núcleo? O modelo de Início Rápido do Azure de exemplo acima tem um script do\_work.php, que carrega um único núcleo. Se você estiver usando uma VM maior que Standard\_A1, será necessário executar essa carga várias vezes. Verifique quantos núcleos suas VMs têm conferindo [Tamanhos para máquinas virtuais do Windows no Azure](../virtual-machines/virtual-machines-windows-sizes.md)
* Quantas VMs há no conjunto de VM escala, e você está balhando em cada uma delas?
  
    Um evento de escala horizontal só ocorrerá quando a CPU média em **todas** as VMs em um conjunto de escala exceder o valor de limite além do tempo interno definido nas regras de dimensionamento automático.
* Você perdeu algum evento de escala?
  
    Verifique os logs de auditoria no Portal do Azure sobre os eventos de escala. Pode ter ocorrido uma escala ou redução vertical que foi perdida. Você pode filtrar por “Escala”.
  
    ![Logs de Auditoria][audit]
* Os limites de escala e redução são diferentes o suficiente?
  
    Suponha que você definiu uma regra para escalar horizontalmente quando a CPU média for maior que 50% durante 5 minutos e reduzir horizontalmente quando a CPU média for menor que 50%. Isso causaria um problema de "oscilação" quando uso da CPU estiver perto desse limite, com ações de escala constantemente aumentando e diminuindo o tamanho do conjunto. Por isso, o serviço de dimensionamento automático tenta impedir “oscilações”, que pode se manifestar como o impedimento do dimensionamento. Portanto, verifique se seus limites de escala e redução são diferentes o suficiente para que exista algum espaço entre os dimensionamentos.
* Você criou seu próprio modelo JSON?
  
    É fácil cometer erros, portanto, comece com um modelo como o mostrado acima, com funcionamento comprovado, e faça pequenas alterações incrementais. O modelo precisa correlacionar uma conta de armazenamento de extensão de Diagnóstico, o conjunto de escala e o recurso Microsoft.Insights, bem como referenciar corretamente o nome de métrica de dados de desempenho, que é diferente entre o Windows e Linux.
* Você consegue aumentar ou reduzir horizontalmente?
  
    Tente reimplantar o recurso do conjunto de escala de VM com uma configuração de "capacidade" diferente para alterar o número de VMs manualmente. Um modelo de exemplo para fazer isso está aqui: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – pode ser necessário editar o modelo para garantir que ele possua o mesmo tamanho de máquina que o conjunto de escala usa. Se for possível alterar o número de VMs manualmente, você saberá que o problema é isolado ao dimensionamento automático.
* Verifique seus recursos Microsoft.Compute/virtualMachineScaleSet e Microsoft.Insights no [Gerenciador de Recursos do Azure](https://resources.azure.com/)
  
    Essa é uma ferramenta indispensável de solução de problemas que mostra o estado dos seus recursos do Azure Resource Manager. Clique na sua assinatura e examine o Grupo de Recursos em que você está solucionando problemas. No Provedor de recursos de computação, examine o conjunto de escala de VM criado e verifique a Exibição de Instância, que mostra o estado de uma implantação. Verifique também a exibição de instância de VMs no conjunto de escala de VM. Vá para o provedor de recursos Microsoft.Insights e verifique se as regras de dimensionamento automático parecem corretas.
* A extensão de Diagnóstico está funcionando e emitindo os dados de desempenho?
  
    O dimensionamento automático no Azure Resource Manager funciona por meio de uma extensão de VM chamada Extensão de Diagnóstico (dividida em extensões de Diagnóstico do Linux e do Windows). Emite dados de desempenho para uma conta de armazenamento definida no modelo. Esses dados são então agregados pelo serviço do Azure Insights.
  
    Se o serviço do Insights não puderem ler os dados de VMs, ele deverá enviar um email, se as VMs ficarem inativas, por exemplo, por isso verifique seu email (aquele especificado ao criar a conta do Azure).
  
    Também é possível examinar os dados você mesmo. Examine a conta de armazenamento do Azure usando um gerenciador de nuvem. Usando o [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), por exemplo, faça logon e escolha a assinatura do Azure que você está usando e o nome da conta de armazenamento de Diagnóstico referenciada na definição da extensão de Diagnóstico no seu modelo de implantação.
  
    ![Gerenciador de Nuvem][explorer]
  
    Aqui você verá um monte de tabelas em que os dados de cada VM estão sendo armazenados. Usando o Linux e a métrica de CPU como exemplo, procure linhas mais recentes. O Visual Studio Cloud Explorer dá suporte a uma linguagem de consulta para que você possa executar uma consulta como “Timestamp gt datetime’2016-02-02T21:20:00Z’” para obter com certeza os eventos mais recentes (supondo que o fuso horário é UTC). Os dados exibidos correspondem às regras de escala que você configurou? No exemplo abaixo, a CPU do computador 20 começou a aumentar para 100% nos últimos 5 minutos.
  
    ![Tabelas de Armazenamento][tables]
  
    Se os dados não estiverem lá, isso significará que o problema está na extensão de diagnóstico em execução nas VMs. Se os dados estiverem lá, isso significará que há um problema com as suas regras de escala ou com o serviço do Insights. Verifique o [Status do Azure](https://azure.microsoft.com/status/).
  
    Depois que você percorrer essas etapas, experimente visitar os fóruns no [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp), [Stack Overflow](http://stackoverflow.com/questions/tagged/azure) ou abrir uma chamada de suporte. Prepare-se para compartilhar o modelo e uma exibição dos dados de desempenho.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png

<!---HONumber=AcomDC_0427_2016-->