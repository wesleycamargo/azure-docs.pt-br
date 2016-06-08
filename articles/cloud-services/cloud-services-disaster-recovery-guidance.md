<properties
	pageTitle="O que fazer caso uma interrupção de serviço do Azure afete os Serviços de Nuvem do Azure | Microsoft Azure"
	description="Saiba o que fazer caso uma interrupção de serviço do Azure afete os Serviços de Nuvem do Azure."
	services="cloud-services"
	documentationCenter=""
	authors="kmouss"
	manager="drewm"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="cloud-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="kmouss;aglick"/>

#O que fazer caso uma interrupção de serviço do Azure afete os Serviços de Nuvem do Azure

Na Microsoft, trabalhamos muito para garantir que nossos serviços estejam sempre disponíveis quando você precisar deles. Às vezes, forças além do nosso controle nos afetam de formas que causam interrupções de serviço não planejadas.

A Microsoft fornece SLAs (Contratos de Nível de Serviço) para seus serviços como um compromisso com o tempo de atividade e a conectividade. O SLA para serviços individuais do Azure pode ser encontrado em [Contratos de Nível de Serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

O Azure já tem muitos recursos de plataforma internos que dão suporte a aplicativos altamente disponíveis. Para saber mais sobre esses serviços, confira [Recuperação de Desastre e Alta Disponibilidade para Aplicativos do Azure](https://aka.ms/drtechguide).

Este documento aborda a Recuperação de Desastre verdadeira, quando uma região inteira sofre uma interrupção devido a um grande desastre natural ou a uma interrupção do serviço generalizada. Essas ocorrências são raras, mas você deve se preparar para a possibilidade de que haja uma interrupção em toda a região. Se toda a região sofrer uma interrupção de serviço, as cópias localmente redundantes dos dados poderão estar temporariamente indisponíveis. Se você tiver habilitado a Replicação geográfica, haverá três cópias adicionais dos blobs de Armazenamento do Azure e tabelas armazenadas em uma região diferente. Em caso de interrupção regional completa ou de um desastre no qual a região principal não seja recuperável, o Azure remapeia todas as entradas de DNS para a região geográfica replicada.
 
>[AZURE.NOTE]Lembre-se de que você não tem nenhum controle sobre esse processo, e ele ocorrerá somente para falhas em todo o datacenter. Por isso, você também deve contar com outras estratégias de backup específicas ao aplicativo para chegar ao nível mais alto de disponibilidade. Para obter mais informações, confira a seção sobre [Estratégias de Dados para recuperação de desastre](https://aka.ms/drtechguide#DSDR). Se você quisesse poder afetar seu próprio failover, convém considerar o uso de [RA-GRS (Armazenamento com Redundância Geográfica com Acesso de Leitura)](../storage/storage-redundancy.md#read-access-geo-redundant-storage), que cria uma cópia somente leitura de seus dados em outra região.

Para ajudá-lo a lidar com essas ocorrências raras, fornecemos as seguintes diretrizes para a Máquina Virtual do Azure no caso de uma interrupção de serviço de toda a região em que seu aplicativo da Máquina Virtual do Azure é implantado.

##Opção 1: aguardar a recuperação 
Nesse caso, nenhuma ação sua é necessária. Saiba que as equipes do Azure estão trabalhando cuidadosamente para restaurar a disponibilidade do serviço. Você pode ver o status atual do serviço no nosso [Painel de integridade do serviço Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Essa será a melhor opção se o cliente não tiver o programa de instalação do ASR (Azure Site Recovery) ou tiver uma implantação secundária em uma região diferente.

Para clientes que desejam acesso imediato a seus Serviços de Nuvem implantados, as opções a seguir estão disponíveis.

>[AZURE.NOTE]Lembre-se de que essas opções têm a possibilidade de alguma perda de dados.

##Opção 2: reimplante a configuração do Serviço de Nuvem para uma nova Região 

Se tiver o código original, você poderá simplesmente reimplantar o aplicativo e a configuração e os recursos associados para um novo Serviço de Nuvem em uma nova região.

Para obter mais detalhes sobre como criar e implantar um aplicativo de Serviço de Nuvem, confira [Como criar e implantar um serviço de nuvem](./cloud-services-how-to-create-deploy-portal.md).

Dependendo das fontes de dados do aplicativo, você precisa verificar os procedimentos de recuperação para a fonte de dados do aplicativo.
  * Para fontes de dados do Armazenamento do Azure, confira [Replicação de Armazenamento do Azure](../storage/storage-redundancy.md#read-access-geo-redundant-storage) para conhecer as opções disponíveis com base no modelo de replicação escolhido para o aplicativo.
  * Para fontes de Banco de Dados SQL, confira [Visão geral: continuidade de negócios em nuvem e recuperação de desastre de banco de dados com o Banco de Dados SQL](../sql-database/sql-database-business-continuity.md) para conhecer as opções disponíveis com base no modelo de replicação escolhido para o aplicativo. 

##Opção 3: use uma implantação de backup por meio de um Gerenciador de Tráfego 
Essa opção pressupõe que você já tenha criado sua solução de aplicativo com a recuperação de desastre regional em mente. Essa opção poderá ser usada se você já tiver uma implantação de aplicativo de Serviços de Nuvem secundária em execução em uma região diferente e conectada por meio de um canal do gerenciador de tráfego. Nesse caso, verifique a integridade da implantação secundária e, se estiver totalmente íntegro, você poderá redirecionar o tráfego para ela por meio do Gerenciador de Tráfego do Azure aproveitando o método de roteamento de tráfego e as configurações de ordem de failover no ATM. Confira [Como definir as configurações do Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md#how-to-configure-traffic-manager-settings).

![Balanceamento dos Serviços de Nuvem do Azure em regiões com o Gerenciador de Tráfego do Azure](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

##Referências 

[Recuperação de Desastre e Alta Disponibilidade para Aplicativos do Azure](https://aka.ms/drtechguide)

[Orientação técnica sobre a continuação de negócios do Azure](https://aka.ms/bctechguide)
 
Se as instruções não estiverem claras ou se você desejar que a Microsoft faça as operações em seu nome, entre em contato com o [Atendimento ao Cliente](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

<!---HONumber=AcomDC_0525_2016-->