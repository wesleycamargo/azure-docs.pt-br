---
title: Mover as VMs IaaS do Azure para outra região do Azure como VMs fixas em zonas usando o serviço Azure Site Recovery | Microsoft Docs
description: Use o Azure Site Recovery para mover as VMs IaaS do Azure para outra região do Azure como VMs fixas em zonas.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 6a731750da4edfb4a71c00156c5ff527dee30941
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824421"
---
# <a name="move-azure-vms-into-availability-zones"></a>Mover as VMs do Azure para Zonas de Disponibilidade
As Zonas de Disponibilidade do Azure protegem seus aplicativos e seus dados contra falhas do datacenter. Cada zona de disponibilidade é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física das Zonas de Disponibilidade dentro de uma região protege os aplicativos e dados contra falhas do datacenter. Com Zonas de Disponibilidade, o Azure oferece o melhor SLA de tempo de atividade da VM de 99,99% do setor. Há suporte para a zona de disponibilidade em regiões selecionadas, conforme mencionado [aqui](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones). 

Em um cenário em que você implantou as Máquinas Virtuais como 'instância única' em uma região específica e deseja melhorar a disponibilidade movendo-as para uma zona de disponibilidade, faça isso usando o Azure Site Recovery. Isso pode ser categorizado mais detalhadamente em:

- Mover as VMs de instância única para Zonas de Disponibilidade em uma região de destino
- Mover as VMs em um conjunto de disponibilidade para Zonas de Disponibilidade em uma região de destino

> [!IMPORTANT]
> Atualmente, o Azure Site Recovery dá suporte à movimentação de VMs de uma região para outra, mas não dá suporte à movimentação dentro de uma região. 

## <a name="verify-prerequisites"></a>Verificar pré-requisitos

- Verificar se há [suporte para a zona de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones) na região de destino – verifique se [há suporte para sua escolha de combinação de região de origem – região de destino](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) e tome uma decisão informada sobre a região de destino.
- Verifique se você entende os [componentes e a arquitetura do cenário](azure-to-azure-architecture.md).
- Examine os [requisitos e limitações com suporte](azure-to-azure-support-matrix.md).
- Verificar permissões da conta: Se você acabou de criar sua conta gratuita do Azure, você é o administrador da assinatura. Se você não for o administrador da assinatura, peça para o administrador atribuir as permissões necessárias. Para habilitar a replicação em uma VM e, mais tarde, copiar os dados para o destino usando o Azure Site Recovery, é necessário ter:

    1. Permissões para criar uma VM em recursos do Azure. A função interna 'Colaborador da Máquina Virtual' tem essas permissões, que incluem:
        - Permissão para criar uma VM no grupo de recursos selecionado
        - Permissão para criar uma VM na rede virtual selecionada
        - Permissão para gravar na conta de armazenamento selecionada

    2. Você também precisa de permissão para gerenciar operações de recuperação do Azure Site Recovery. A função 'Colaborador do Site Recovery' tem todas as permissões necessárias para gerenciar operações do Site Recovery em um cofre de Serviços de Recuperação.

## <a name="prepare-the-source-vms"></a>Preparar as VMs de origem

1. As VMs deverão estar usando discos gerenciados caso você deseje movê-las para a zona de disponibilidade usando o Site Recovery. Converta as VMs (Máquinas Virtuais) existentes do Windows que usam discos não gerenciados para usarem discos gerenciados seguindo as etapas mencionadas [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). Para isso, verifique se o conjunto de disponibilidade está configurado como 'Gerenciado'. 
2. Verifique se todos os certificados raiz mais recentes estão presentes nas VMs do Azure que deseja mover. Se os certificados raiz mais recentes não estiverem presentes, a cópia de dados para a região de destino não poderá ser habilitada devido a restrições de segurança.

3. Para VMs Windows, instale todas as atualizações do Windows mais recentes na VM para que todos os certificados raiz confiáveis estejam no computador. Em um ambiente desconectado, siga os processos padrão do Windows Update e de atualização de certificado para sua organização.

4. Para VMs Linux, siga as diretrizes fornecidas pelo seu distribuidor Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogados na VM.
5. Verifique se você não está usando um proxy de autenticação para controlar a conectividade de rede das VMs que deseja mover.

6. Se a VM que você está tentando mover não tiver acesso à Internet e estiver usando um proxy de firewall para controlar o acesso de saída, verifique os requisitos [aqui](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

7. Identifique o layout da rede de origem e todos os recursos que estão sendo usados no momento – incluindo balanceadores de carga, NSGs, IP público etc. para a verificação.

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

> [!IMPORTANT]
> Use um balanceador de carga com redundância de zona no destino. Leia mais [aqui](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. [Crie uma rede de não produção](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) manualmente na região de destino caso deseje testar a configuração antes de realizar a substituição final para a região de destino. Isso criará interferência mínima com o ambiente de produção e é recomendado.


> [!NOTE]
> As etapas abaixo destinam-se a uma única VM; você poderá estender o mesmo para várias VMs navegando para o cofre dos Serviços de Recuperação, clicando em + Replicar e selecionando as VMs relevantes juntas.

## <a name="enable-replication"></a>Habilitar a replicação
As etapas abaixo explicarão como usar o Azure Site Recovery para habilitar a replicação de dados para a região de destino antes que, mais tarde, eles sejam movidos para as Zonas de Disponibilidade.

1. No portal do Azure, clique em **Máquinas virtuais** e selecione a VM que deseja mover para as Zonas de Disponibilidade.
2. Em **Operações**, clique em **Recuperação de desastre**.
3. Em **Configurar a recuperação de desastre** > **Região de destino**, selecione a região de destino para a qual você replicará. Garanta que essa região [dê suporte](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones) às Zonas de Disponibilidade.
![enable-rep-1.PNG](media/azure-vms-to-zones/enable-rep-1.PNG)

1. Selecione **Avançar: Configurações avançadas**
2. Escolha os valores apropriados para a assinatura de destino, bem como para o grupo de recursos e a rede virtual da VM de destino.
3. Na seção **Disponibilidade**, escolha a zona de disponibilidade para a qual deseja mover a VM. 
> [!NOTE]
> Se a opção de conjunto de disponibilidade ou zona de disponibilidade não for exibida, garanta que os [pré-requisitos](#prepare-the-source-vms) sejam atendidos e que a [preparação](#prepare-the-source-vms) das VMs de origem seja concluída.

   ![enable-rep-2.PNG](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Clique em Habilitar replicação. Isso inicia um trabalho para habilitar a replicação para a VM.

## <a name="verify-settings"></a>Verificar as configurações

Após o trabalho de replicação, você poderá verificar o status de replicação, modificar as configurações de replicação e testar a implantação.

1. No menu da VM, clique em **Recuperação de desastre**.
2. Você pode verificar a integridade da replicação, os pontos de recuperação que foram criados e as regiões de origem e destino no mapa.

   ![Status de replicação](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Testar a configuração

1. No menu da máquina virtual, clique em **Recuperação de Desastre**.
2. Clique no ícone **Failover de Teste**.
3. Em **Failover de Teste**, selecione um ponto de recuperação para usar no failover:

   - **Mais recente processado**: Faz failover da VM para o ponto de recuperação único que foi processado pelo serviço do Site Recovery. A carimbo de data/hora é mostrado. Com essa opção, nenhum tempo é gasto em processamento de dados, portanto, ela fornece um RTO (Objetivo do Tempo de Recuperação) baixo
   - **Consistente com o aplicativo mais recente**: Essa opção falha em todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente. A carimbo de data/hora é mostrado.
   - **Personalizado**: Selecione qualquer ponto de recuperação.

3. Selecione a rede virtual de destino de teste do Azure para a qual deseja mover as VMs do Azure para testar a configuração. 

> [!IMPORTANT]
> Recomendamos que você use uma rede separada de VMs do Azure para a falha de teste, não a rede de produção na região de destino para a qual deseja mover as VMs mais tarde.

4. Para começar a testar a movimentação, clique em **OK**. Para acompanhar o progresso, clique na VM para abrir suas propriedades. Ou você pode clicar no trabalho **Failover de Teste** no nome do cofre > **Configurações** > **Trabalhos** > **Trabalhos do Site Recovery**.
5. Após a conclusão do failover, a réplica da VM do Azure aparece no portal do Azure > **Máquinas Virtuais**. Verifique se a VM está em execução, tem o tamanho apropriado e está conectada à rede apropriada.
6. Caso deseje excluir a VM criada como parte do teste da movimentação, clique em **Limpar failover de teste** no item replicado. Em **Observações**, registre e salve as observações associadas ao teste.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Realize a movimentação para a região de destino e confirme-a.

1.  No menu da máquina virtual, clique em **Recuperação de Desastre**.
2. Clique no ícone **Failover**.
3. Em **Failover**, selecione **Mais recente**. 
4. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tenta desligar a VM de origem antes de acionar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** . 
5. Depois que o trabalho for concluído, verifique se a VM é exibida na região de destino do Azure conforme esperado.
6. Em **Itens replicados**, clique com o botão direito do mouse em VM > **Confirmar**. Isso concluirá o processo de movimentação para a região de destino. Aguarde até o trabalho de confirmação ser concluído.

## <a name="discard-the-resource-in-the-source-region"></a>Descartar o recurso na região de origem 

1. Navegue para a VM.  Clique em **Desabilitar Replicação**.  Isso interromperá o processo de cópia dos dados para a VM.  

> [!IMPORTANT]
> É importante executar a etapa acima para evitar a cobrança pela replicação do Site Recovery após a movimentação. As configurações de replicação de origem são limpas automaticamente. Observe que a extensão de Site Recovery instalada como parte da replicação não é removida e precisa ser removida manualmente. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aumentou a disponibilidade de uma VM do Azure movendo-a para um conjunto de disponibilidade ou uma zona de disponibilidade. Agora é possível configurar a recuperação de desastres para VMs do Azure movida.

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastres após a migração](azure-to-azure-quickstart.md)


