---
title: O que fazer caso uma interrupção de serviço do Azure afete os Serviços de Nuvem do Azure | Microsoft Docs
description: Saiba o que fazer no caso uma interrupção de serviço do Azure que afete os Serviços de Nuvem do Azure.
services: cloud-services
documentationcenter: ''
author: kmouss
manager: drewm
editor: ''

ms.service: cloud-services
ms.workload: cloud-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: kmouss;aglick

---
# O que fazer no caso de uma interrupção de serviço do Azure que afete os Serviços de Nuvem do Azure
Na Microsoft, trabalhamos muito para garantir que nossos serviços estejam sempre disponíveis quando você precisar deles. Às vezes, forças além do nosso controle nos afetam de formas que causam interrupções de serviço não planejadas.

A Microsoft fornece um SLA (Contrato de Nível de Serviço) para seus serviços como um compromisso com o tempo de atividade e a conectividade. O SLA para serviços individuais do Azure pode ser encontrado em [Contratos de Nível de Serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

O Azure já tem muitos recursos internos de plataforma que oferecem suporte a aplicativos altamente disponíveis. Para saber mais sobre esses serviços, leia [Recuperação de desastres e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Este artigo aborda um cenário real de recuperação de desastre, quando uma região inteira sofre uma interrupção devido a um grande desastre natural ou a uma interrupção do serviço generalizada. Essas ocorrências são raras, mas você deve se preparar para a possibilidade de uma interrupção em toda uma região. Se toda a região sofrer uma interrupção de serviço, as cópias localmente redundantes dos dados poderão estar temporariamente indisponíveis. Se você tiver habilitado a replicação geográfica, haverá três cópias adicionais dos blobs de Armazenamento do Azure e tabelas armazenadas em uma região diferente. No caso de uma interrupção regional completa ou de um desastre no qual a região primária não seja recuperável, o Azure remapeia todas as entradas de DNS para a região geográfica replicada.

> [!NOTE]
> Lembre-se de que você não tem qualquer controle sobre esse processo e de que ele ocorrerá apenas em caso de interrupção do serviço em todo um datacenter. Por isso, você também deve contar com outras estratégias de backup específicas ao aplicativo para chegar ao nível mais alto de disponibilidade. Para saber mais, confira a seção sobre [Estratégias de dados para recuperação de desastre](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md#DSDR). Se você quiser ser capaz de afetar seu próprio failover, convém considerar o uso de [RA-GRS (armazenamento com Redundância Geográfica com Acesso de Leitura)](../storage/storage-redundancy.md#read-access-geo-redundant-storage), que cria uma cópia somente leitura de seus dados em outra região.
> 
> 

Para ajudar você a lidar com essas ocorrências raras, fornecemos a seguinte orientação para VMs (máquinas virtuais) do Azure no caso de uma interrupção do serviço de toda uma região na qual seu aplicativo de VM do Azure está implantado.

## Opção 1: aguardar a recuperação
Nesse caso, nenhuma ação sua é necessária. Saiba que as equipes do Azure estão trabalhando cuidadosamente para restaurar a disponibilidade do serviço. Você pode ver o status atual do serviço no nosso [Painel de integridade do serviço Azure](https://azure.microsoft.com/status/).

> [!NOTE]
> Essa é a melhor opção se um cliente não tiver configurado o Azure Site Recovery ou tiver uma implantação secundária em uma região diferente.
> 
> 

Para os clientes que desejam acesso imediato aos serviços de nuvem implantados, as opções a seguir estão disponíveis.

> [!NOTE]
> Lembre-se de que essas opções têm a possibilidade de alguma perda de dados.
> 
> 

## Opção 2: implantar novamente sua configuração de serviço de nuvem em uma nova região
Se você tiver seu código original, você poderá simplesmente reimplantar o aplicativo, a configuração associada e os recursos associados a um novo serviço de nuvem em uma nova região.

Para obter mais detalhes sobre como criar e implantar um aplicativo de serviço de nuvem, confira [Como criar e implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md).

Dependendo das fontes de dados do aplicativo, você precisa verificar os procedimentos de recuperação para a fonte de dados do aplicativo.

* Para obter as fontes de dados do Armazenamento do Azure, confira [Replicação de Armazenamento do Azure](../storage/storage-redundancy.md#read-access-geo-redundant-storage) para conhecer as opções disponíveis com base no modelo de replicação escolhido para o aplicativo.
* Para fontes de Banco de Dados SQL, confira [Visão geral: continuidade de negócios em nuvem e recuperação de desastre de banco de dados com o Banco de Dados SQL](../sql-database/sql-database-business-continuity.md) para conhecer as opções disponíveis com base no modelo de replicação escolhido para o aplicativo.

## Opção 3: usar uma implantação de backup por meio do Gerenciador de Tráfego do Azure
Essa opção pressupõe que você já criou sua solução de aplicativo com a recuperação de desastre regional em mente. Você poderá usar essa opção se já tiver uma implantação de aplicativo de serviços de nuvem secundária em execução em uma região diferente e conectada por meio de um canal do gerenciador de tráfego. Nesse caso, verifique a integridade da implantação secundária. Se estiver íntegra, você poderá redirecionar o tráfego até ela por meio do Gerenciador de Tráfego do Azure. Com essa estratégia, você pode aproveitar as vantagens do método de roteamento de tráfego e as configurações de ordem de failover no Gerenciador de Tráfego do Azure. Para obter mais informações, consulte [Como definir as configurações do Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md#how-to-configure-traffic-manager-settings).

![Balanceamento dos Serviços de Nuvem do Azure em regiões com o Gerenciador de Tráfego do Azure](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

## Próximas etapas
Para saber mais sobre como implementar uma estratégia de alta disponibilidade e recuperação de desastres, consulte [Recuperação de desastres e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Para desenvolver uma compreensão técnica detalhada dos recursos de uma plataforma de nuvem, consulte [Orientação técnica sobre a resiliência do Azure](../resiliency/resiliency-technical-guidance.md).

Se as instruções não estiverem claras ou se você desejar que a Microsoft faça as operações em seu nome, entre em contato com o [Atendimento ao Cliente](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

<!---HONumber=AcomDC_0629_2016-->