---
title: "Proteger uma implantação de aplicativos SAP NetWeaver de várias camada usando o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como proteger as implantações de aplicativos SAP NetWeaver usando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: b6ab734186f23d51d60e51bd0946329d5209097b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-by-using-site-recovery"></a>Proteger uma implantação de aplicativo SAP NetWeaver de várias camadas usando o Site Recovery

A maioria das implantações do SAP de grande e médio porte usam alguma forma de solução de recuperação de desastre. A importância de se ter soluções de recuperação de desastre sólidas foi aumentando à medida que os processos de negócios mais vitais foram movidos para aplicativos como o SAP. O Azure Site Recovery foi testado e integrado a aplicativos SAP. O Site Recovery excede a capacidade da maioria das soluções de recuperação de desastre locais e em um menor TCO (custo total de propriedade) que as soluções da concorrência.

Com o Site Recovery, você pode:
* **Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver de produção em execução no local** por meio da replicação de componentes no Azure.
* **Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver de produção em execução no Azure** por meio da replicação de componentes em outro datacenter do Azure.
* **Simplifique a migração na nuvem** usando o Site Recovery para migrar sua implantação do SAP para o Azure.
* **Simplifique as atualizações, teste e criação de protótipos de projeto SAP** criando um clone de produção sob demanda para testar aplicativos SAP.

Este artigo descreve como proteger as implantações de aplicativos SAP NetWeaver usando o [Azure Site Recovery](site-recovery-overview.md). O artigo aborda as melhores práticas para proteger uma implantação do SAP NetWeaver de três camadas no Azure por meio da replicação para outro datacenter do Azure usando o Site Recovery. Ele descreve os cenário e as configurações com suporte, e como executar failovers de teste (simulações de recuperação de desastre) e failovers reais.

## <a name="prerequisites"></a>pré-requisitos
Antes de começar, verifique se você sabe como fazer as seguintes tarefas:

* [Replicar uma máquina virtual no Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Criar uma rede de recuperação](site-recovery-azure-to-azure-networking-guidance.md)
* [Executar um failover de teste para o Azure](azure-to-azure-walkthrough-test-failover.md)
* [Executar um failover para o Azure](site-recovery-failover.md)
* [Replicar um controlador de domínio](site-recovery-active-directory.md)
* [Replicar o SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Cenários com suporte
Você pode usar o Azure Site Recovery para implementar uma solução de recuperação de desastre nos seguintes cenários:
* Sistemas SAP em execução em um datacenter do Azure que replica para outro datacenter do Azure (recuperação de desastre do Azure para o Azure). Para saber mais, confira [Arquitetura de replicação do Azure para o Azure](https://aka.ms/asr-a2a-architecture).
* Sistemas SAP em execução em servidores VMware (ou físicos) locais que replicam para um local de recuperação de desastre em um datacenter do Azure (recuperação de desastre do VMware para o Azure). Este cenário requer alguns componentes adicionais. Para saber mais, confira [Arquitetura de replicação do VMware para o Azure](https://aka.ms/asr-v2a-architecture).
* Sistemas SAP em execução no Hyper-V localmente que são replicados em um local de recuperação de desastre em um datacenter do Azure (recuperação de desastre do Hyper-V para Azure). Este cenário requer alguns componentes adicionais. Para saber mais, confira [Arquitetura de replicação do Hyper-V para o Azure](https://aka.ms/asr-h2a-architecture).

Neste artigo, usamos um cenário de recuperação de desastre do Azure para o Azure a fim de demonstrar os recursos de recuperação de desastre de SAP do Site Recovery. Como a replicação do Site Recovery não é específica do aplicativo, o processo descrito deve também se aplicar a outros cenários.

### <a name="required-foundation-services"></a>Serviços básicos necessários
No cenário discutido neste artigo, os seguintes serviços de base são implantados:
* Gateway de VPN do Azure ou Azure ExpressRoute
* Pelo menos um controlador de domínio do Active Directory e um servidor DNS em execução no Azure

Recomendamos que você estabeleça essa infraestrutura antes de implantar o Site Recovery.

## <a name="typical-sap-application-deployment"></a>Implantação típica do aplicativo SAP
Os clientes grandes do SAP geralmente implantam entre 6 e 20 aplicativos SAP individuais. A maioria desses aplicativos é baseada em mecanismos Java ou ABAP do SAP NetWeaver. Vários mecanismos autônomos e aplicativos específicos que não são SAP NetWeaver e, tipicamente, alguns aplicativos que não são SAP, dão suporte a esses aplicativos principais NetWeaver.  

É fundamental inventariar todos os aplicativos SAP que estão em execução no seu ambiente. Em seguida, determine o modo de implantação (de duas ou três camadas), as versões, os patches, os tamanhos, as taxas de variação e os requisitos de persistência do disco.

![Diagrama de um padrão de implantação típico do SAP](./media/site-recovery-sap/sap-typical-deployment.png)

Proteja a camada de persistência do Banco de Dados SAP por meio de ferramentas nativas do DBMS, como o AlwaysOn do SQL Server, o Oracle Data Guard ou o sistema de replicação SAP HANA. Assim como a camada de banco de dados do SAP, a camada de cliente não está protegida pelo Site Recovery. É importante considerar os fatores que afetam essa camada. Os fatores incluem atraso de propagação DNS, segurança e acesso remoto ao datacenter de recuperação de desastre.

O Site Recovery é a solução recomendada para a camada de aplicativo, incluindo os SCS e ASCS SAP. Outros aplicativos, como aplicativos que não são SAP NetWeaver e SAP, fazem parte do ambiente geral de implantação do SAP. Você deve protegê-los com o Site Recovery.

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais
Para iniciar a replicação de todas as máquinas virtuais do aplicativo SAP para o datacenter de recuperação de desastre do Azure, siga as orientações em [Replicar uma máquina virtual no Azure](azure-to-azure-walkthrough-enable-replication.md).

Se você usar um endereço IP estático, poderá especificar o endereço IP que você deseja que a máquina virtual use. Para configurar o endereço IP, vá para **Configurações de computação e rede** > **Placa de adaptador de rede**.

![Captura de tela que mostra como definir um endereço IP privado no painel de adaptador de rede do Site Recovery](./media/site-recovery-sap/sap-static-ip.png)

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação
Um plano de recuperação dá suporte à sequência de várias camadas em um aplicativo de várias camadas durante um failover. O sequenciamento ajuda a manter a consistência do aplicativo. Quando você criar um plano de recuperação para um aplicativo Web de várias camadas, conclua as etapas descritas em [Criar um plano de recuperação usando o Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação
Talvez seja necessário fazer algumas operações nas máquinas virtuais do Azure após o failover ou durante o teste de failover para que seus aplicativos funcionem corretamente. Você pode automatizar algumas operações após o failover. Por exemplo, você pode atualizar a entrada DNS e alterar associações e conexões adicionando scripts correspondentes ao plano de recuperação.

### <a name="dns-update"></a>Atualização de DNS
Se o DNS estiver configurado para atualização dinâmica de DNS, as máquinas virtuais normalmente atualizarão o DNS com o novo endereço IP quando forem iniciadas. Se você quiser adicionar uma etapa explícita para atualizar o DNS com os novos IPs das máquinas virtuais, adicione um [script para atualizar o endereço IP no DNS](https://aka.ms/asr-dns-update) como uma ação pós-failover nos grupos de plano de recuperação.  

## <a name="example-azure-to-azure-deployment"></a>Exemplo de implantação do Azure para o Azure
O diagrama abaixo mostra o cenário de recuperação de desastre do Azure para o Azure pelo Site Recovery:

![Diagrama de um cenário de replicação do Azure para o Azure](./media/site-recovery-sap/sap-replication-scenario.png)

* O datacenter primário está localizado em Cingapura (Sudeste Asiático). O datacenter de recuperação de desastre fica em Hong Kong (Azure Ásia Oriental). Nesse cenário, a alta disponibilidade local é fornecida por duas VMs que executam o SQL Server AlwaysOn no modo síncrono em Cingapura.
* O ASCS SAP do compartilhamento de arquivos fornece alta disponibilidade para os pontos únicos de falha do SAP. O ASCS do compartilhamento de arquivos não requer um disco de cluster compartilhado. Aplicativos como SIOS não são necessários.
* A proteção de recuperação de desastre para a camada de DBMS é obtida usando replicação assíncrona.
* Este cenário mostra "recuperação de desastre simétrica". Esse termo descreve uma solução de recuperação de desastre que é uma réplica exata da produção. A solução SQL Server de recuperação de desastre tem alta disponibilidade local. A recuperação de desastre simétrica não é obrigatória para a camada de banco de dados. Muitos clientes aproveitam a flexibilidade de implantações de nuvem para criar rapidamente um nó de alta disponibilidade local após um evento de recuperação de desastre.
* O diagrama ilustra a camada de servidor de aplicativo e do ASCS SAP NetWeaver replicada pelo Site Recovery.

## <a name="run-a-test-failover"></a>Execute um teste de failover

1.  No portal do Azure, selecione seu cofre dos Serviços de Recuperação.
2.  Selecione o plano de recuperação que você criou para aplicativos SAP.
3.  Selecione **Failover de Teste**.
4.  Selecione o ponto e recuperação e a rede virtual do Azure para iniciar o processo de failover de teste.
5.  Quando o ambiente secundário está ativo, faça validações.
6.  Quando as validações forem concluídas, para limpar o ambiente de failover, selecione **Limpar failover de teste**.

Para saber mais, confira [Failover de teste para o Azure no Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar um failover

1.  No portal do Azure, selecione seu cofre dos Serviços de Recuperação.
2.  Selecione o plano de recuperação que você criou para aplicativos SAP.
3.  Selecione **Failover**.
4.  Para iniciar o processo de failover, selecione o ponto de recuperação.

Para saber mais, confira [Failover no Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como criar uma solução de recuperação de desastre para implantações do SAP NetWeaver usando o Site Recovery, confira o white paper para download [SAP NetWeaver: criando uma solução de recuperação de desastre com o Azure Site Recovery](http://aka.ms/asr-sap). O white paper aborda as recomendações para várias arquiteturas do SAP, lista de aplicativos com suporte e tipos de VM para SAP no Azure e descreve opções de planos de teste para a solução de recuperação de desastre.
* Saiba mais sobre [como replicar outras cargas de trabalho](site-recovery-workload.md) usando o Site Recovery.
