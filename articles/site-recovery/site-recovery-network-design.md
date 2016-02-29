<properties
	pageTitle="Considerações sobre a implantação do VMM e Recuperação de Site | Microsoft Azure"
	description="Este artigo aborda considerações de design prático para a integração do VMM com o Azure Site Recovery"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="02/15/2016"
	ms.author="raynew"/>

#  Considerações sobre a implantação do VMM e Site Recovery

O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. As máquinas podem ser replicadas no Azure ou em um datacenter local secundário. Para uma breve visão geral, leia [O que é o Azure Site Recovery?](site-recovery-overview.md).


## Visão geral

Este artigo ajuda você a projetar e implementar uma solução BCDR e a infraestrutura que inclui o System Center VMM e o Azure Site Recovery.

A finalidade de sua estratégia de BCDR é manter seus aplicativos de negócios em execução e restaurar as cargas de trabalho com falha e os serviços para que a organização possa retomar rapidamente a operação normal. Desenvolver estratégias de recuperação de desastres é um desafio, devido à dificuldade inerente de prever eventos imprevisíveis e o alto custo da implementação de uma proteção adequada contra falhas de longo alcance. A Recuperação de Site ajuda a implementar proteção e failover de seu datacenter principal em um datacenter secundário (ou no Azure), copiando inicialmente (replicando) os dados principais e, em seguida, atualizando periodicamente as réplicas.

Como uma parte essencial do planejamento BCDR, você precisa definir o objetivo do tempo de recuperação (RTO) e o objetivo do ponto de recuperação (RPO) para que possa colocar novamente os dados da organização online o mais rápido possível (com um baixo RTO) e com perda mínima de dados (RPO baixo). O design de rede de sua organização representa um gargalo potencial para os objetivos de RTO e RPO, e um planejamento de design sólido pode ajudar a evitar esse desafio.


Se você estiver implantando a Recuperação de Site para replicar máquinas virtuais Hyper-V em nuvens do VMM, precisará considerar como o VMM integra-se à sua estratégia de replicação e failover de recuperação de Site.

## Integrando um servidor VMM autônomo

Nessa topologia, você implantará um servidor VMM em uma máquina virtual no site principal e, em seguida, replicará essa máquina virtual para um site secundário com Réplica do Hyper-V e Recuperação de Site. Convém considerar que a instalação do servidor VMM e do respectivo SQL Server de suporte na mesma máquina virtual pode reduzir o tempo de inatividade, porque apenas uma VM deve ser instanciada. Quando o serviço VMM está usando um SQL Server remoto, você precisa recuperar a instância do SQL Server antes de recuperar o servidor VMM.

Para implantar um único VMM em uma máquina virtual com o Hyper-V Replica:

1. Configure o VMM em uma VM com o SQL Server instalado.
2. Adicione hosts a serem gerenciados a nuvens no servidor VMM.
3. Faça logon no portal do Azure e, em seguida, configure nuvens para proteção.
4. Habilite a replicação para todas as VMs que precisam ser protegidas pelo servidor VMM.
5. Vá para o console do Gerenciador do Hyper-V, escolha o Hyper-V Replica e, em seguida, habilite a replicação na VM do VMM.
6. Certifique-se de que a VM do VMM não é adicionada para as nuvens que estão protegidas pelo serviço ASR para que as configurações de replicação do Hyper-V não sejam substituídas pelo ASR.

No caso de um desastre, as cargas de trabalho podem ser recuperadas da seguinte maneira:

1. Faça o failover da VM do VMM para o site de recuperação, usando o Gerenciador do Hyper-V.
2. Depois que a VM do VMM for recuperada, o usuário poderá fazer logon no Hyper-V Recovery Manager do site secundário.
3. Após a conclusão do failover não planejado os usuários poderão acessar todos os recursos no site principal.
4. Observe que a VM do VMM precisa sofrer failover manualmente para o site secundário antes de as cargas de trabalho falharem.


### Integração de um cluster do VMM


[Implantar o VMM em um cluster](https://technet.microsoft.com/library/gg610675.aspx) fornece alta disponibilidade e proteção contra failover de hardware. Se você estiver implantando o cluster do VMM com a Recuperação de Site, observerá que:

- O servidor do VMM deve ser implantado em um cluster ampliado em sites geograficamente separados.
- O banco de dados SQL Server usados pelo VMM deve ser protegido com os grupos de disponibilidade AlwaysOn do SQL Server com uma réplica no site secundário.
- Se o desastre ocorrer o servidor VMM e o SQL Server correspondente irão automaticamente fazer failover para o site de recuperação. Em seguida, é possível realizar failover de cargas de trabalho com a Recuperação de Site.

	![Cluster do VMM ampliado](./media/site-recovery-network-design/network-design1.png)


## Próximas etapas

[Saiba](site-recovery-network-mapping.md) como a Recuperação de Site mapeia redes de origem e destino.

<!---HONumber=AcomDC_0218_2016-->