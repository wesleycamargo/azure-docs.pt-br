---
title: Solução de problemas do Microsoft Azure Stack | Microsoft Docs
description: Solução de pilha do Azure.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: b6ec3283121a3403afb80ccad81f313decf16c88
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957633"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Solução de problemas do Microsoft Azure Stack

Este documento fornece informações de solução de problemas comuns para o Azure Stack. 

> [!NOTE]
> Porque o Azure Stack Technical desenvolvimento ASDK (Kit) é oferecido como um ambiente de avaliação, não há nenhum suporte oficial da Microsoft Customer Support Services. Se você estiver tendo um problema, certifique-se de verificar a [Fórum do MSDN do Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) para obter assistência adicional e informações.  

As recomendações para solução de problemas que são descritos nesta seção são derivadas de várias fontes e podem ou não podem resolver seu problema específico. Exemplos de código são fornecidos como estão e resultados esperados não podem ser garantidos. Esta seção está sujeito aos edições frequentes e atualizações, como melhorias ao produto são implementadas.

## <a name="deployment"></a>Implantação
### <a name="deployment-failure"></a>Falha de implantação
Se você enfrentar uma falha durante a instalação, você poderá reiniciar a implantação da etapa com falha usando a - opção de nova execução do script de implantação.  

### <a name="at-the-end-of-asdk-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>No final da implantação ASDK, a sessão do PowerShell ainda está aberta e não mostra nenhuma saída.
Esse comportamento provavelmente é apenas o resultado do comportamento padrão de uma janela de comando do PowerShell, quando ela tiver sido selecionada. A implantação do kit de desenvolvimento foi bem-sucedida, mas o script foi pausado ao selecionar a janela. Você pode verificar a instalação foi concluída ao procurar a palavra "selecionar" na barra de título da janela de comando.  Pressione a tecla ESC para desmarcá-la, e a mensagem de conclusão deve ser mostrada depois dela.

## <a name="virtual-machines"></a>Máquinas virtuais
### <a name="default-image-and-gallery-item"></a>Item de galeria e de imagem padrão
Um item de imagem e a Galeria do Windows Server deve ser adicionado antes de implantar VMs no Azure Stack.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Depois de reiniciar o meu host Azure Stack, algumas VMs podem não iniciar automaticamente.
Após a reinicialização de seu host, você pode perceber a serviços do Azure Stack não estão imediatamente disponíveis.  Isso ocorre porque o Azure Stack [infra-estrutura VMs](../azure-stack/asdk/asdk-architecture.md#virtual-machine-roles) e provedores de recursos levar algum tempo para verificar a consistência, mas eventualmente serão iniciado automaticamente.

Você também pode observar esse locatário a que VMs não iniciam automaticamente após uma reinicialização do host do kit de desenvolvimento do Azure Stack. Este é um problema conhecido e requer apenas algumas etapas manuais para colocá-los online:

1.  No host do kit de desenvolvimento do Azure Stack, inicie **Gerenciador de Cluster de Failover** no Menu Iniciar.
2.  Selecione o cluster **Cluster.azurestack.local S**.
3.  Selecione **funções**.
4.  VMs de locatário são exibidos em uma *salvo* estado. Depois que todas as VMs de infraestrutura estão em execução, as VMs do locatário com o botão direito e selecione **iniciar** para retomar a máquina virtual.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Ter excluído algumas máquinas virtuais, mas ainda ver os arquivos VHD no disco. Esse comportamento é esperado?
Sim, esse comportamento é esperado. Ele foi projetado dessa forma porque:

* Quando você exclui uma VM, os VHDs não serão excluídos. Os discos são recursos separados no grupo de recursos.
* Quando uma conta de armazenamento é excluída, a exclusão é visível imediatamente por meio do Azure Resource Manager, mas os discos que pode conter ainda são mantidos no armazenamento até que a coleta de lixo é executado.

Se você vir "órfãos" VHDs, é importante saber se eles fazem parte da pasta para uma conta de armazenamento que foi excluída. Se a conta de armazenamento não foi excluída, é normal que elas ainda estão lá.

Você pode ler mais sobre como configurar a recuperação sob demanda e limite de retenção no [gerenciar contas de armazenamento](azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Armazenamento
### <a name="storage-reclamation"></a>Recuperação de armazenamento
Pode levar até 14 horas para capacidade recuperada apareça no portal. Recuperação de espaço depende de vários fatores, inclusive a porcentagem de utilização de arquivos do contêiner interno no repositório de blob de bloco. Portanto, dependendo da quantidade de dados é excluído, não há nenhuma garantia sobre a quantidade de espaço que poderia ser recuperado quando o coletor de lixo é executado.

