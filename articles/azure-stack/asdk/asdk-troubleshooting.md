---
title: Solução de problemas de pilha do Microsoft Azure | Microsoft Docs
description: Azure pilha Development Kit (ASDK) informações de solução de problemas.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 6c715f07f75c9196b7cf2cc8659c6e541e1260da
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
ms.locfileid: "30167887"
---
# <a name="microsoft-azure-stack-development-kit-asdk-troubleshooting"></a>Solução de problemas do Microsoft Azure pilha Development Kit (ASDK)
Este documento fornece informações de solução de problemas comuns para o ASDK. Se você estiver enfrentando um problema que não está documentado, certifique-se de verificar o [Fórum do MSDN do Azure pilha](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) para obter mais assistência e informações.  

> [!IMPORTANT]
> Como o ASDK é um ambiente de avaliação, não há nenhum oficial de suporte oferecido por meio de serviços de suporte de cliente do Microsoft (CSS).

As recomendações de solução de problemas que são descritos nesta seção são provenientes de várias fontes e podem ou não podem resolver o problema específico. Exemplos de código são fornecidos "como está" e não podem ser garantidos resultados esperados. Esta seção está sujeito edições frequentes e atualizações conforme são implementadas melhorias ao produto.

## <a name="deployment"></a>Implantação
### <a name="deployment-failure"></a>Falha na implantação
Se houver uma falha durante a instalação, você pode reiniciar a implantação da etapa com falha usando-opção de executar novamente o script de implantação como no exemplo a seguir:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>No final da implantação, a sessão do PowerShell ainda está aberta e não exibe nenhuma saída
Esse comportamento provavelmente é apenas o resultado do comportamento padrão de uma janela de comando do PowerShell, quando ele foi selecionado. A implantação do kit de desenvolvimento foi bem-sucedida, mas o script foi pausado ao selecionar a janela. Você pode verificar a instalação foi concluída ao procurar a palavra "select" na barra de título da janela de comando. Pressione a tecla ESC para desmarcá-la e a mensagem de conclusão deve ser mostrada após ele.

## <a name="virtual-machines"></a>Máquinas virtuais
### <a name="default-image-and-gallery-item"></a>Item de galeria e de imagem padrão
Um item de imagem e a Galeria do Windows Server deve ser adicionado antes de implantar VMs na pilha do Azure.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Depois de reiniciar meu host de pilha do Azure, algumas VMs podem não iniciar automaticamente.
Depois de reinicializar o host, você pode notar serviços do Azure pilha não estão imediatamente disponíveis. Isso ocorre porque a pilha do Azure [infraestrutura VMs](asdk-architecture.md#virtual-machine-roles) e RPs tomar algumas tempo para verificar a consistência, mas eventualmente serão iniciado automaticamente.

Você também pode observar esse locatário que VMs não iniciam automaticamente após a reinicialização do host do kit de desenvolvimento pilha do Azure. Este é um problema conhecido e requer apenas algumas etapas manuais para colocá-los online:

1.  No host do kit de desenvolvimento pilha do Azure, inicie **Gerenciador de Cluster de Failover** no Menu Iniciar.
2.  Selecione o cluster **Cluster.azurestack.local S**.
3.  Selecione **funções**.
4.  Máquinas virtuais de locatário aparecem em uma *salvo* estado. Quando todas as VMs de infraestrutura está em execução, as máquinas virtuais do locatário e selecione **iniciar** para retomar a máquina virtual.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Ter excluído algumas máquinas virtuais, mas ainda, consulte os arquivos VHD no disco. Esse comportamento é esperado?
Sim, esse comportamento é esperado. Ele foi criado dessa maneira porque:

* Quando você exclui uma máquina virtual, os VHDs não são excluídos. Os discos são recursos separados no grupo de recursos.
* Quando uma conta de armazenamento é excluída, a exclusão é visível imediatamente por meio do Azure Resource Manager, mas os discos que podem conter ainda são mantidos no armazenamento até que a coleta de lixo é executado.

Se você vir VHDs "órfãos", é importante saber se eles fazem parte da pasta para uma conta de armazenamento que foi excluída. Se a conta de armazenamento não foi excluída, é normal que eles ainda estão lá.

Você pode ler mais sobre como configurar a recuperação de limite e sob demanda de retenção em [gerenciar contas de armazenamento](.\.\azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Armazenamento
### <a name="storage-reclamation"></a>Recuperação de armazenamento
Pode levar até 14 horas para capacidade recuperada que aparecerão no portal. Recuperação de espaço depende de vários fatores, incluindo a porcentagem de utilização de arquivos de contêiner interno no repositório de blob de bloco. Portanto, dependendo da quantidade de dados é excluído, não há nenhuma garantia sobre a quantidade de espaço que pode ser recuperada quando o coletor de lixo é executado.

## <a name="next-steps"></a>Próximas etapas
[Visite o fórum de suporte de pilha do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)

