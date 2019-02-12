---
title: Mover as VMs IaaS do Azure para outra região do Azure usando o serviço Azure Site Recovery | Microsoft Docs
description: Use o Azure Site Recovery para mover as VMs IaaS do Azure de uma região do Azure para outra.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: bbede01844f20c0240b154fd319b818a43463131
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824423"
---
# <a name="move-azure-vms-to-another-region"></a>Mover VMs do Azure para outra região

Há vários cenários em que você desejará mover as máquinas virtuais IaaS existentes do Azure de uma região para outra – para melhorar a confiabilidade e disponibilidade das VMs existentes, para melhorar a capacidade de gerenciamento ou devido a motivos de governança etc., conforme detalhado. 

Este tutorial mostra como mover as VMs do Azure para outra região usando o Azure Site Recovery. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * [Verificar pré-requisitos](#verify-prerequisites)
> * [Preparar as VMs de origem](#prepare-the-source-vms)
> * [Preparar a região de destino](#prepare-the-target-region)
> * [Copiar os dados para a região de destino](#copy-data-to-the-target-region)
> * [Testar a configuração ](#test-the-configuration)
> * [Realizar a movimentação ](#perform-the-move-to-the-target-region-and-confirm)
> * [Descartar o recurso na região de origem ](#discard-the-resource-in-the-source-region)

> [!IMPORTANT]
> Este documento explica como mover as VMs do Azure de uma região para outra no estado em que se encontra. Se o requisito é melhorar a disponibilidade de sua infraestrutura movendo as VMs para zonas de disponibilidade, consulte o tutorial aqui.

## <a name="verify-prerequisites"></a>Verificar pré-requisitos

- Verifique se você tem as VMs do Azure na região de origem do Azure da qual deseja realizar a movimentação.
- Verifique se [há suporte para sua escolha de combinação de região de origem – região de destino](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) e tome uma decisão informada sobre a região de destino.
- Verifique se você entende os [componentes e a arquitetura do cenário](azure-to-azure-architecture.md).
- Examine os [requisitos e limitações com suporte](azure-to-azure-support-matrix.md).
- Verificar permissões da conta: Se você acabou de criar sua conta gratuita do Azure, você é o administrador da assinatura. Se você não for o administrador da assinatura, peça para o administrador atribuir as permissões necessárias. Para habilitar a replicação em uma VM e, mais tarde, copiar os dados para o destino usando o Azure Site Recovery, é necessário ter:

    1. Permissões para criar uma VM em recursos do Azure. A função interna 'Colaborador da Máquina Virtual' tem essas permissões, que incluem:
        - Permissão para criar uma VM no grupo de recursos selecionado
        - Permissão para criar uma VM na rede virtual selecionada
        - Permissão para gravar na conta de armazenamento selecionada

    2. Você também precisa de permissão para gerenciar operações de recuperação do Azure Site Recovery. A função 'Colaborador do Site Recovery' tem todas as permissões necessárias para gerenciar operações do Site Recovery em um cofre de Serviços de Recuperação.

## <a name="prepare-the-source-vms"></a>Preparar as VMs de origem

1. Verifique se todos os certificados raiz mais recentes estão presentes nas VMs do Azure que deseja mover. Se os certificados raiz mais recentes não estiverem presentes, a cópia de dados para a região de destino não poderá ser habilitada devido a restrições de segurança.

    - Para VMs Windows, instale todas as atualizações do Windows mais recentes na VM para que todos os certificados raiz confiáveis estejam no computador. Em um ambiente desconectado, siga os processos padrão do Windows Update e de atualização de certificado para sua organização.
    - Para VMs Linux, siga as diretrizes fornecidas pelo seu distribuidor Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogados na VM.
2. Verifique se você não está usando um proxy de autenticação para controlar a conectividade de rede das VMs que deseja mover.
3. Se a VM que você está tentando mover não tiver acesso à Internet e estiver usando um proxy de firewall para controlar o acesso de saída, verifique os requisitos [aqui](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
4. Identifique e liste o layout da rede de origem e todos os recursos que estão sendo usados no momento – incluindo, entre outros, balanceadores de carga, NSGs, IP público etc. para a verificação.

## <a name="prepare-the-target-region"></a>Preparar a região de destino

1. Verifique se a sua assinatura do Azure permite criar VMs na região de destino usada para recuperação de desastre. Contate o suporte para habilitar a cota necessária se for preciso.

2. Verifique se a sua assinatura tem recursos suficientes para dar suporte a VMs com tamanhos que correspondem às VMs de origem. Se você estiver usando o Site Recovery para copiar os dados para o destino, ele escolherá o mesmo tamanho ou o tamanho mais próximo possível para a VM de destino.

3. Crie um recurso de destino para cada componente identificado no layout da rede de origem. Isso é importante para garantir que, após a substituição para a região de destino, as VMs tenham todos os recursos e as funcionalidades que tinham na origem.

    > [!NOTE]
    > O Azure Site Recovery descobre automaticamente e cria uma rede virtual e uma conta de armazenamento quando você habilita a replicação para a VM de origem. Se preferir, você também poderá pré-criar esses recursos e atribuí-los à VM como parte da etapa de habilitação da replicação. Porém, para outros recursos, conforme mencionado abaixo, você precisará criá-los manualmente na região de destino.

     Consulte os documentos a seguir para criar os recursos de rede mais comumente usados que são relevantes para você, com base na configuração da VM de origem.

    - [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Balanceadores de carga](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [IP público ](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    Para outros componentes de rede, consulte a [documentação](https://docs.microsoft.com/azure/#pivot=products&panel=network) da rede. 

4. [Crie uma rede de não produção](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) manualmente na região de destino caso deseje testar a configuração antes de realizar a substituição final para a região de destino. Isso criará interferência mínima com o ambiente de produção e é recomendado.
    
## <a name="copy-data-to-the-target-region"></a>Copiar os dados para a região de destino
As etapas abaixo mostrarão como usar o Azure Site Recovery para copiar dados para a região de destino.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Crie o cofre em qualquer região, exceto a região de origem.

1. Entre no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Clique em **Criar um recurso** > **Ferramentas de Gerenciamento** > **Backup e Site Recovery**.
3. Em **Nome**, especifique o nome amigável **ContosoVMVault**. Caso você tenha mais de uma a. assinatura, selecione a assinatura apropriada.
4. Crie um grupo de recursos **ContosoRG**.
5. Especifique uma região do Azure. Para verificar as regiões com suporte, confira a disponibilidade geográfica nos [Detalhes dos Preços de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Nos cofres dos Serviços de Recuperação, clique em **Visão Geral** > **ConsotoVMVault** > **+Replicar**.
7. Em **Fonte**, selecione **Azure**.
8. Em **Local de origem**, selecione a fonte de região do Azure em que suas VMs estão sendo executados.
9. Selecione o modelo de implantação do Gerenciador de Recursos. Em seguida, selecione a **Assinatura de origem** e o **Grupo de recursos de origem**.
10. Clique em **OK** para salvar as configurações.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Habilite a replicação para as VMs do Azure e comece a copiar os dados.

O Site Recovery recupera uma lista das VMs associadas à assinatura e ao grupo de recursos.

1. Na próxima etapa, Selecione a VM que deseja mover. Em seguida, clique em **OK**.
3. Em **Configurações**, clique em **Recuperação de desastre**.
4. Em **Configurar a recuperação de desastre** > **Região de destino**, selecione a região de destino para a qual você replicará.
5. Você pode optar por usar os recursos de destino padrão ou aqueles pré-criados.
6. Clique em **Habilitar a replicação**. Isso inicia um trabalho para habilitar a replicação para a VM.

    ![habilitar a replicação](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Testar a configuração


1. Navegue para o cofre, em **Configurações** > **Itens replicados**, clique na Máquina Virtual que pretende mover para a região de destino e clique no ícone **+Failover de Teste**.
2. Em **Failover de Teste**, selecione um ponto de recuperação para usar no failover:

   - **Mais recente processado**: Faz failover da VM para o ponto de recuperação único que foi processado pelo serviço do Site Recovery. A carimbo de data/hora é mostrado. Com essa opção, nenhum tempo é gasto em processamento de dados, portanto, ela fornece um RTO (Objetivo do Tempo de Recuperação) baixo
   - **Consistente com o aplicativo mais recente**: Essa opção falha em todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente. A carimbo de data/hora é mostrado.
   - **Personalizado**: Selecione qualquer ponto de recuperação.

3. Selecione a rede virtual de destino do Azure para a qual deseja mover as VMs do Azure para testar a configuração. 

> [!IMPORTANT]
> Recomendamos que você use uma rede separada de VMs do Azure para a falha de teste, não a rede de produção na região de destino para a qual deseja mover as VMs mais tarde.

4. Para começar a testar a movimentação, clique em **OK**. Para acompanhar o progresso, clique na VM para abrir suas propriedades. Ou você pode clicar no trabalho **Failover de Teste** no nome do cofre > **Configurações** > **Trabalhos** > **Trabalhos do Site Recovery**.
5. Após a conclusão do failover, a réplica da VM do Azure aparece no portal do Azure > **Máquinas Virtuais**. Verifique se a VM está em execução, tem o tamanho apropriado e está conectada à rede apropriada.
6. Caso deseje excluir a VM criada como parte do teste da movimentação, clique em **Limpar failover de teste** no item replicado. Em **Observações**, registre e salve as observações associadas ao teste.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Realize a movimentação para a região de destino e confirme-a.

1.  Navegue para o cofre, em **Configurações** > **Itens replicados**, clique na máquina virtual e, em seguida, clique em **Failover**.
2. Em **Failover**, selecione **Mais recente**. 
3. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tenta desligar a VM de origem antes de acionar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** . 
4. Depois que o trabalho for concluído, verifique se a VM é exibida na região de destino do Azure conforme esperado.
5. Em **Itens replicados**, clique com o botão direito do mouse em VM > **Confirmar**. Isso concluirá o processo de movimentação para a região de destino. Aguarde até o trabalho de confirmação ser concluído.

## <a name="discard-the-resource-in-the-source-region"></a>Descartar o recurso na região de origem 

1. Navegue para a VM.  Clique em **Desabilitar Replicação**.  Isso interromperá o processo de cópia dos dados para a VM.  

> [!IMPORTANT]
> É importante executar a etapa acima para evitar a cobrança pela replicação do Site Recovery após a movimentação.

Caso não tenha planos de reutilizar os recursos de origem, continue com o próximo conjunto de etapas.

1. Continue e exclua todos os recursos de rede relevantes da região de origem listados como parte da Etapa 4 em [Preparar as VMs de origem](#prepare-the-source-vms) 
2. Exclua a conta de armazenamento correspondente da região de origem.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu uma VM do Azure para uma região diferente dele. Agora é possível configurar a recuperação de desastres para VMs do Azure movida.

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastres após a migração](azure-to-azure-quickstart.md)

