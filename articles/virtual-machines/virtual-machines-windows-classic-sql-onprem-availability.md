<properties
	pageTitle="Estender Grupos de Disponibilidade AlwaysOn locais para o Azure | Microsoft Azure"
	description="Este tutorial usa recurso criados com o modelo de implantação clássica e descreve como usar o assistente de Adição de Réplica no SSMS (SQL Server Management Studio) para adicionar uma réplica do Grupo de Disponibilidade AlwaysOn ao Azure."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="jroth" />

# Estender Grupos de Disponibilidade AlwaysOn locais para o Azure

Os Grupos de Disponibilidade AlwaysOn fornecem alta disponibilidade para grupos de bancos de dados adicionando réplicas secundárias. Essas réplicas permitem o failover dos bancos de dados em caso de falha. Além disso, elas podem ser usadas para descarregar cargas de trabalho de leitura ou tarefas de backup.

Você pode estender grupos de disponibilidade locais para o Microsoft Azure provisionando uma ou mais máquinas virtuais do Azure com o SQL Server e, depois, adicionando-as como réplicas aos grupos de disponibilidade locais.

Este tutorial pressupõe que você tenha o seguinte:

- Uma assinatura ativa do Azure. Você pode [se inscrever para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Um Grupo de Disponibilidade AlwaysOn local. Para obter mais informações sobre os Grupos de Disponibilidade, consulte [Grupos de Disponibilidade AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx).

- Conectividade entre a rede local e sua rede virtual do Azure. Para obter mais informações sobre como criar essa rede virtual, consulte [Configurar um VPN site a site no portal clássico do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.

## Assistente de adição de réplica do Azure

Esta seção mostra como usar o **Assistente de Adição de réplica do Azure** para estender sua solução do Grupo de Disponibilidade AlwaysOn para incluir réplicas do Azure.

1. De dentro do SQL Server Management Studio, expanda **Alta Disponibilidade AlwaysOn** > **Grupos de Disponibilidade** > **[Nome do seu Grupo de Disponibilidade]**.

1. Clique com o botão direito do mouse em **Réplicas de Disponibilidade** e em **Adicionar Réplica**.

1. Por padrão, o **Assistente de Adição de Réplica ao Grupo de Disponibilidade** é exibido. Clique em **Próximo**. Se você tiver selecionado a opção **Não mostrar esta página novamente** na parte inferior da página durante uma inicialização anterior desse assistente, esta tela não será exibida.

	![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)

1. Você precisará se conectar a todas as réplicas secundárias existentes. Você pode clicar em **Conectar...** ao lado de cada réplica ou pode clicar em **Conectar todos...** na parte inferior da tela. Após a autenticação, clique em **Avançar** para prosseguir para a próxima tela.

1. Na página **Especificar Réplicas**, várias guias são listadas na parte superior: **Réplicas**, **Pontos de Extremidade**, **Preferências de Backup** e **Ouvinte**. Na guia **Réplicas**, clique em **Adicionar Réplica do Azure...** para iniciar o Assistente de Adição de Réplica do Azure.

	![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)

1. Selecione um Certificado de gerenciamento do Azure existente no repositório de certificados local do Windows se você tiver instalado um antes. Selecione ou insira a ID de uma assinatura do Azure se tiver usado uma antes. Você pode clicar em Baixar para baixar e instalar um Certificado de gerenciamento do Azure e baixar a lista de assinaturas usando uma conta do Azure.

	![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)

1. Você preencherá cada campo da página com valores que serão usados para criar a VM (máquina virtual) do Azure que hospedará a réplica.

	|Configuração|Descrição|
|---|---|
|**Imagem**|Selecione a combinação desejada de SO e SQL Server|
|**Tamanho da MV**|Selecione o tamanho da VM que melhor atenda às suas necessidades de negócios|
|**Nome da VM**|Especifique um nome exclusivo para a nova VM. O nome deve conter entre 3 e 15 caracteres, pode conter apenas letras, números e hifens e precisa começar com uma letra e terminar com uma letra ou número.|
|**Nome de usuário da VM**|Especifique um nome de usuário que se tornará a conta de administrador na VM|
|**Senha do administrador da VM**|Especifique uma senha para a nova conta|
|**Confirmar Senha**|Confirme a senha para a nova conta|
|**Rede Virtual**|Especifique a rede virtual do Azure que a nova VM deve usar. Para obter mais informações sobre redes virtuais, consulte [Visão Geral da Rede Virtual](../virtual-network/virtual-networks-overview.md).|
|**Sub-rede da rede virtual**|Especifique a sub-rede da rede virtual que a nova VM deve usar|
|**Domínio**|Confirme se o valor preenchido previamente para o domínio está correto|
|**Nome de usuário do domínio**|Especifique uma conta que esteja no grupo de administradores locais em nós do cluster local|
|**Senha**|Especifique a senha para o nome de usuário do domínio|

1. Clique em **OK** para validar as configurações de implantação.

1. Os termos legais são exibidos em seguida. Leia e clique em **OK** se você concordar com os termos.

1. A página **Especificar Réplicas** é exibida novamente. Verifique as configurações para a nova réplica do Azure nas guias **Réplicas**, **Pontos de Extremidade** e **Preferências de Backup**. Modifique as configurações para atender às suas necessidades de negócios. Para obter mais informações sobre os parâmetros contidos nessas guias, consulte [Página Especificar Réplicas (Assistente de Novo Grupo de Disponibilidade/Assistente de Adição de Réplica)](https://msdn.microsoft.com/library/hh213088.aspx). Observe que ouvintes não podem ser criados usando a guia Ouvinte para Grupos de Disponibilidade que contêm réplicas do Azure. Além disso, se um ouvinte já tiver sido criado antes de iniciar o assistente, você receberá uma mensagem indicando que não há suporte no Azure. Veremos como criar ouvintes na seção **Criar um Ouvinte de Grupo de Disponibilidade**.

	![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)

1. Clique em **Próximo**.

1. Selecione o método de sincronização de dados que deseja usar na página **Selecionar Sincronização de Dados Inicial** e clique em **Avançar**. Na maioria dos cenários, selecione **Sincronização Completa de Dados**. Para obter mais informações sobre métodos de sincronização de dados, consulte [Página Selecionar Sincronização de Dados Inicial (Assistentes de Grupo de Disponibilidade AlwaysOn)](https://msdn.microsoft.com/library/hh231021.aspx).

1. Examine os resultados na página **Validação**. Corrija os problemas pendentes e execute novamente a validação, se necessário. Clique em **Próximo**.

	![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)

1. Analise as configurações na página **Resumo** e clique em **Concluir**.

1. O processo de provisionamento tem início. Quando o assistente for concluído com êxito, clique em **Fechar** para sair dele.

>[AZURE.NOTE] O Assistente para Adicionar Réplica do Azure cria um arquivo de log em Usuários\\Nome do Usuário\\AppData\\Local\\SQL Server\\AddReplicaWizard. Esse arquivo de log pode ser usado para solucionar problemas de implantações de réplica do Azure com falha. Se o assistente falhar durante a execução de alguma ação, todas as operações anteriores serão revertidas, incluindo a exclusão da VM provisionada.

## Criar um ouvinte de grupo de disponibilidade

Após o grupo de disponibilidade ter sido criado, você deve criar um ouvinte para os clientes se conectarem às réplicas. Os ouvintes direcionam conexões de entrada para a réplica primária ou uma réplica secundária somente leitura. Para obter mais informações sobre os ouvintes, consulte [Configurar um ouvinte de ILB para Grupos de Disponibilidade AlwaysOn no Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

## Próximas etapas

Além de usar o **Assistente de Adição de Réplica do Azure** para estender seu Grupo de Disponibilidade AlwaysOn ao Azure, você também pode mover algumas cargas de trabalho do SQL Server completamente para o Azure. Para começar, consulte [Provisionando uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-classic-portal-sql.md).

Para outros tópicos relacionados à execução do SQL Server em VMs do Azure, consulte [SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-classic-sql-overview.md).

<!---HONumber=AcomDC_0323_2016-->