---
title: "Configurar uma política de replicação para a replicação do Hyper-V para um site secundário com o Azure Site Recovery | Microsoft Docs"
description: "Descreve como configurar uma política para replicação de VM do Hyper-V para um site secundário do VMM com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5d9b79cf-89f2-4af9-ac8e-3a32ad8c6c4d
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: a3ecc555e049914d7bffdddce7b0522f09362d24
ms.contentlocale: pt-br
ms.lasthandoff: 08/01/2017

---
# <a name="step-8-set-up-a-replication-policy"></a>Etapa 8: Configurar uma política de replicação

Depois de configurar o [mapeamento de rede](vmm-to-vmm-walkthrough-network-mapping.md), use este artigo para configurar uma política de replicação para replicação de máquina virtual (VM) do Hyper-V para um site secundário usando o [Azure Site Recovery](site-recovery-overview.md).

Depois de ler este artigo, poste comentários na parte inferior ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Antes de começar

- Quando você cria uma política de replicação, todos os hosts usando a política devem ter o mesmo sistema operacional. Nuvem do VMM pode conter hosts Hyper-V executando versões diferentes do Windows Server, mas nesse caso, você precisa de várias políticas de replicação.
- Você pode executar a replicação inicial offline.

## <a name="configure-replication-settings"></a>Definir configurações de replicação

1. Para criar uma nova política de replicação, clique em **Preparar a Infraestrutura** > **Configurações de Replicação** > **+Criar e associar**.

    ![Rede](./media/vmm-to-vmm-walkthrough-replication/gs-replication.png)
2. Em **Criar e associar política**, especifique um nome de política. Os tipos de origem e de destino devem ser **Hyper-V**.
3. Em **Versão do host do Hyper-V**, selecione qual sistema operacional está em execução no host.
4. Em **Tipo de autenticação** e **Porta de autenticação**, especifique como o tráfego é autenticado entre os servidores host Hyper-V primário e de recuperação. Selecione **Certificado** , a menos que você tenha um ambiente Kerberos em funcionamento. A Recuperação de Site do Azure configurará automaticamente os certificados para autenticação HTTPS. Você não precisa fazer nada manualmente. Por padrão, as portas 8083 e 8084 (para certificados) serão abertas no Firewall do Windows nos servidores host Hyper-V. Se você selecionar **Kerberos**, um tíquete Kerberos será usado para autenticação mútua dos servidores host. Observe que esta configuração só é relevante para servidores de host Hyper-V no Windows Server 2012 R2.
5. Em **Frequência de cópia**, especifique com que frequência você deseja replicar os dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).
6. Em **Retenção do ponto de recuperação**, especifique, em horas, qual será a duração da janela de retenção para cada ponto de recuperação. Os computadores protegidos podem ser recuperados para qualquer ponto nessa janela.
7. Em **Frequência do instantâneo consistente com aplicativo**, especifique com que frequência (1 a 12 horas) são criados os pontos de recuperação que contêm instantâneos consistentes com o aplicativo. O Hyper-V usa dois tipos de instantâneos: um instantâneo padrão, que fornece um instantâneo incremental de toda a máquina virtual, e um instantâneo consistente com aplicativos, que cria um instantâneo pontual dos dados do aplicativo na máquina virtual. Os instantâneos consistentes com aplicativos usam o Serviço de VSS (Cópias de Sombra de Volume) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo for obtido. Se você habilitar instantâneos consistentes com o aplicativo, isso afetará o desempenho de aplicativos executados em máquinas virtuais de origem. Verifique se o valor definido é menor do que o número de pontos de recuperação adicionais que você configurar.
8. Em **Compactação da transferência de dados**, especifique se os dados replicados transferidos devem ser compactados.
9. Selecione **Excluir VM de réplica** para especificar que a máquina virtual de réplica deverá ser excluída se você desabilitar a proteção para a VM de origem. Se você habilitar essa configuração, quando você desabilitar a proteção para a VM de origem, ela será removida do console de Recuperação de Site, as configurações de Recuperação de Site para o VMM serão removidas do console do VMM e a réplica será excluída.
10. Em **Método de replicação inicial**, se você estiver replicando na rede, especifique se deseja iniciar a replicação inicial ou agendá-la. Para economizar largura de banda de rede, talvez você deseje agendá-la fora de seus horários mais ocupados. Em seguida, clique em **OK**.

     ![Política de replicação](./media/vmm-to-vmm-walkthrough-replication/gs-replication2.png)
11. Quando você cria uma nova política, ela é automaticamente associada à nuvem do VMM. Em **Política de replicação**, clique em **OK**. É possível associar Nuvens do VMM adicionais (e as VMs nelas) a essa política de replicação em **Replicação** > nome da política > **Associar Nuvem do VMM**.

     ![Política de replicação](./media/vmm-to-vmm-walkthrough-replication/policy-associate.png)



## <a name="prepare-for-offline-initial-replication"></a>Preparar a replicação inicial offline

Você pode fazer a replicação offline para a cópia inicial de dados. Você pode preparar isso da seguinte maneira:

* No servidor de origem, você especifica um local do caminho do qual será feita a exportação de dados. Atribua Controle Total de permissões de NTFS e de Compartilhamento ao serviço VMM no caminho de exportação. No servidor de destino, você especifica um local do caminho do qual será feita a importação de dados. Atribua as mesmas permissões nesse caminho de importação.
* Se o caminho de importação ou de exportação for compartilhado, atribua a associação de grupo de Administrador, Usuário Avançado, Operador de Impressão ou Operador de Servidor à conta de serviço VMM no computador remoto no qual o caminho compartilhado está localizado.
* Se você estiver usando contas Executar Como para adicionar hosts, nos caminhos de importação e exportação, atribua permissões de leitura e gravação às contas Executar Como no VMM.
* Os compartilhamentos de importação e de exportação não devem estar localizados em um computador usado como um servidor host Hyper-V porque o Hyper-V não dá suporte à configuração de loopback.
* No Active Directory, em cada servidor host Hyper-V que contém máquinas virtuais que deseja proteger, habilite e configure a delegação restrita para confiar nos computadores remotos nos quais os caminhos de importação e de exportação estão localizados, da seguinte maneira:
  1. No controlador de domínio, abra **Usuários e Computadores do Active Directory**.
  2. Na árvore de console, clique em **DomainName** > **Computadores**.
  3. Clique com o botão direito do mouse no nome do servidor host Hyper-V > **Propriedades**.
  4. Na guia **Delegação**, clique em **Confiar no computador para delegação apenas a serviços especificados**.
  5. Clique em **Usar qualquer protocolo de autenticação**.
  6. Clique em **Adicionar** > **Usuários e Computadores**.
  7. Digite o nome do computador que hospeda o caminho de exportação > **OK**. Na lista de serviços disponíveis, mantenha pressionada a tecla CTRL e clique em **cifs** > **OK**. Repita o procedimento para o nome do computador que hospeda o caminho de importação. Repita conforme necessário para servidores host Hyper-V adicionais.



## <a name="next-steps"></a>Próximas etapas

Acesse a [Etapa 9: Habilitar a replicação](vmm-to-vmm-walkthrough-enable-replication.md).

