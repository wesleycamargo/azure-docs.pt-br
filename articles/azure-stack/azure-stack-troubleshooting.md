---
title: "Solução de problemas de pilha do Microsoft Azure | Microsoft Docs"
description: "Solução de pilha do Azure."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2017
ms.author: helaw
ms.openlocfilehash: 0a8e871a3a44cb14503832d2f3a096712f8112a7
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2017
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Solução de problemas de pilha do Microsoft Azure

*Aplica-se a: Kit de desenvolvimento de pilha do Azure*

Este documento fornece informações de solução de problemas comuns de pilha do Azure. 

Porque o Kit de desenvolvimento do Azure pilha técnica é oferecido como um ambiente de avaliação, não há nenhum suporte oficial de suporte técnico da Microsoft.  Se você estiver enfrentando um problema não documentado, certifique-se de verificar o [Fórum do MSDN do Azure pilha](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) para obter mais assistência e informações.  

As recomendações de solução de problemas que são descritos nesta seção são provenientes de várias fontes e podem ou não podem resolver o problema específico. Exemplos de código são fornecidos como estão, e não podem ser garantidos resultados esperados. Esta seção está sujeito edições frequentes e atualizações conforme são implementadas melhorias ao produto.

## <a name="deployment"></a>Implantação
### <a name="deployment-failure"></a>Falha na implantação
Se houver uma falha durante a instalação, você pode usar usar a opção de executar novamente o script de implantação para reiniciar a implantação da etapa com falha.  


### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>No final da implantação, a sessão do PowerShell ainda está aberta e não exibe nenhuma saída
Esse comportamento provavelmente é apenas o resultado do comportamento padrão de uma janela de comando do PowerShell, quando ele foi selecionado. A implantação do kit de desenvolvimento, na verdade, teve êxito, mas o script foi pausado ao selecionar a janela. Você pode verificar que esse for o caso, procurando a palavra "select" na barra de título da janela de comando.  Pressione a tecla ESC para desmarcá-la e a mensagem de conclusão deve ser mostrada após ele.

## <a name="virtual-machines"></a>Máquinas virtuais
### <a name="default-image-and-gallery-item"></a>Item de galeria e de imagem padrão
Primeiro, você deve adicionar um item de imagem e a Galeria do Windows Server antes de implantar VMs na pilha do Azure.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Depois de reiniciar meu host de pilha do Azure, algumas VMs podem não iniciar automaticamente.
Depois de reinicializar o host, você pode notar serviços do Azure pilha não estão imediatamente disponíveis.  Isso ocorre porque a pilha do Azure [infraestrutura VMs](azure-stack-architecture.md#virtual-machine-roles) e RPs demorar um pouco para verificar a consistência, mas eventualmente será iniciado automaticamente.

Você também pode observar esse locatário que VMs não iniciam automaticamente após a reinicialização do host do kit de desenvolvimento pilha do Azure.  Este é um problema conhecido e requer apenas algumas etapas manuais para colocá-los online:

1.  No host do kit de desenvolvimento pilha do Azure, inicie **Gerenciador de Cluster de Failover** no Menu Iniciar.
2.  Selecione o cluster **Cluster.azurestack.local S**.
3.  Selecione **funções**.
4.  VMs do locatário serão exibidas em uma *salvo* estado.  Quando todas as VMs de infraestrutura está em execução, as máquinas virtuais do locatário e selecione **iniciar** para retomar a máquina virtual.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Ter excluído algumas máquinas virtuais, mas ainda, consulte os arquivos VHD no disco. Esse comportamento é esperado?
Sim, esse comportamento é esperado. Ele foi criado dessa maneira porque:

* Quando você exclui uma máquina virtual, os VHDs não são excluídos. Os discos são recursos separados no grupo de recursos.
* Quando uma conta de armazenamento é excluída, a exclusão é visível imediatamente por meio do Azure Resource Manager (portal, PowerShell), mas os discos que podem conter ainda são mantidos no armazenamento até que a coleta de lixo é executado.

Se você vir VHDs "órfãos", é importante saber se eles fazem parte da pasta para uma conta de armazenamento que foi excluída. Se a conta de armazenamento não foi excluída, é normal que eles ainda estão lá.

Você pode ler mais sobre como configurar a recuperação de limite e sob demanda de retenção em [gerenciar contas de armazenamento](azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Armazenamento
### <a name="storage-reclamation"></a>Recuperação de armazenamento
Pode levar até 14 horas para capacidade recuperada que aparecerão no portal. Recuperação de espaço depende de vários fatores, incluindo a porcentagem de utilização de arquivos de contêiner interno no repositório de blob de bloco. Portanto, dependendo da quantidade de dados é excluído, não há nenhuma garantia sobre a quantidade de espaço que pode ser recuperada quando o coletor de lixo é executado.

## <a name="windows-azure-pack-connector"></a>Conector do Windows Azure Pack
* Se você alterar a senha da conta de azurestackadmin após a implantação do kit de desenvolvimento de pilha do Azure, você não pode configurar modo de várias nuvens. Portanto, não será possível conectar-se ao ambiente do Windows Azure Pack de destino.
* Depois de configurar o modo de várias nuvens:
    * Um usuário pode ver o painel apenas depois que eles redefinir as configurações do portal. (No portal do usuário, clique no ícone de configurações do portal (ícone de engrenagem no canto superior direito). Em **restaurar as configurações padrão**, clique em **aplicar**.)
    * Os títulos do painel não podem aparecer. Se esse problema ocorrer, você deve adicionar manualmente-los novamente.
    * Alguns blocos não sejam exibidas corretamente quando você adicioná-los primeiro ao painel. Para corrigir esse problema, atualize o navegador.



