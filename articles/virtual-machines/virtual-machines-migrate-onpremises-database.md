<properties
	pageTitle="Migrar o banco de dados local para o SQL Server na máquina virtual do Azure"
	description="Saiba mais sobre como migrar um banco de dados de usuário local para o SQL Server em uma máquina virtual do Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="carlrab"/>


# Migrando um banco de dados para SQL Server em uma VM do Azure

Existem vários métodos para migrar um banco de dados de usuário do SQL Server local para o SQL Server em uma VM do Azure. Este artigo discute brevemente vários métodos, recomenda o melhor para diversos cenários e inclui um tutorial para orientar você a usar o assistente para Implantar um Banco de Dados do SQL Server em uma VM do Microsoft Azure.

## Quais são os principais métodos de migração?

Os principais métodos de migração são:

- Usar o Assistente para implantar um Banco de Dados do SQL Server em uma VM do Microsoft Azure
- Executar backup local usando a compactação e copiar manualmente o arquivo de backup para a máquina virtual do Azure
- Executar um backup para URL e restaurar na máquina virtual do Azure desde a URL
- Desanexar e copiar os arquivos de log e de dados para o armazenamento de blobs do Azure e, em seguida, anexar ao SQL Server em uma VM do Azure desde a URL
- Converter a máquina física local em VHD do Hyper-V, carregar no armazenamento de Blob do Azure e, em seguida, implantar como nova máquina virtual usando o VHD carregado
- Remeter o disco rígido usando o Serviço de Importação/Exportação do Windows

> [AZURE.NOTE]Se você tiver uma implantação local do AlwaysOn, também poderá usar o [Assistente para Adicionar Réplica do Azure](https://msdn.microsoft.com/library/dn463980.aspx) para criar uma réplica no Azure e, em seguida, realizar o failover como um método de migração.

## Escolhendo o método de migração

Para obter um desempenho de transferência de dados ideal, geralmente o melhor método é migrar os arquivos de banco de dados na VM do Azure usando um arquivo de backup compactado. Esse é o método que o [Assistente para Implantar um Banco de Dados do SQL Server em uma VM do Microsoft Azure](#azure-vm-deployment-wizard-tutorial) utiliza. Esse assistente é o método recomendado para migrar um banco de dados de usuário local em execução no SQL Server 2005 ou posterior para o SQL Server 2014 ou superior quando o arquivo de backup do banco de dados compactado tiver menos de 1 TB.

Se não for possível usar o assistente porque o arquivo de backup do banco de dados é muito grande ou a instância do SQL Server de destino não é o SQL Server 2014 ou mais recente, o processo de migração será um processo manual que geralmente começa com um backup de banco de dados seguido por uma cópia do backup do banco de dados para o Azure e então é concluído com uma restauração de banco de dados. Você também pode copiar os próprios arquivos do banco de dados para o Azure. Existem vários métodos pelos quais você pode realizar esse processo manual de migrar um banco de dados para uma VM do Azure.

> [AZURE.NOTE]Quando você atualizar para o SQL Server 2014 ou para o SQL Server 2016 de versões anteriores do SQL Server, deverá considerar se as alterações são necessárias. É recomendável resolver todas as dependências nos recursos sem suporte da nova versão do SQL Server como parte do seu projeto de migração. Para saber mais sobre os cenários e as edições com suporte, consulte [Atualizar para o SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

A tabela a seguir lista os principais métodos de migração e discute quando o uso de cada método é mais apropriado.

| Método | Versão do banco de dados de origem | Versão do banco de dados de destino | Restrição de tamanho do backup do banco de dados de origem | Observações |
|---|---|---|---|---|
| [Use o assistente para Implantar um Banco de Dados do SQL Server em uma VM do Microsoft Azure](#azure-vm-deployment-wizard-tutorial) | SQL Server 2005 ou posterior | SQL Server 2014 ou posterior | Mais de 1 TB | Método mais simples e mais rápido, use sempre que possível para migrar para uma instância do SQL Server nova ou existente em uma máquina virtual do Azure |
| [Execute o backup local usando a compactação e copie manualmente o arquivo de backup para a máquina virtual do Azure](#backup-to-file-and-copy-to-vm-and-restore) | SQL Server 2005 ou posterior | SQL Server 2005 ou posterior | [Limite de armazenamento da VM do Azure](https://azure.microsoft.com/pt-br/documentation/articles/azure-subscription-service-limits/) | Use somente quando você não pode usar o assistente, como quando a versão de banco de dados de destino for anterior ao SQL Server 2012 SP1 CU2 ou quando o tamanho do backup do banco de dados for maior do que 1 TB (12,8 TB com o SQL Server 2016) |
| [Execute um backup para URL e restaure na máquina virtual do Azure desde a URL](#backup-to-url-and-restore) | SQL Server 2012 SP1 CU2 ou posterior | SQL Server 2012 SP1 CU2 ou posterior | Mais de 1 TB (para o SQL Server 2016, menos de 12,8 TB) | Geralmente, usar o [backup para URL](https://msdn.microsoft.com/library/dn435916.aspx) tem o mesmo desempenho de usar o assistente e não é tão fácil |
| [Desanexe e copie os arquivos de log e dados para o armazenamento de blob do Azure e anexe à máquina virtual do SQL Server no Azure desde a URL](#detach-and-copy-to-url-and-attach-from-url) | SQL Server 2005 ou posterior | SQL Server 2014 ou posterior | [Limite de armazenamento da VM do Azure](https://azure.microsoft.com/pt-br/documentation/articles/azure-subscription-service-limits/) | Use ao anexar arquivos de banco de dados no SQL Server a uma VM do Azure[armazenando esses arquivos usando o serviço de armazenamento de Blob do Azure](https://msdn.microsoft.com/library/dn385720.aspx), especialmente com bancos de dados muito grandes |
| [Converta a máquina local em VHDs do Hyper-V, carregue no armazenamento de Blob do Azure e, em seguida, implante uma nova máquina virtual usando o VHD carregado](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) | SQL Server 2005 ou posterior | SQL Server 2005 ou posterior | [Limite de armazenamento da VM do Azure](https://azure.microsoft.com/pt-br/documentation/articles/azure-subscription-service-limits/) | Use quando estiver [trazendo sua própria licença do SQL Server](../data-management-azure-sql-database-and-sql-server-iaas/) ao migrar um banco de dados que você irá executar em uma versão anterior do SQL Server ou ao migrar bancos de dados do sistema e do usuário como parte da migração de banco de dados dependente de outros bancos de dados de usuário e/ou bancos de dados do sistema. |
| [Remeter o disco rígido usando o Serviço de Importação/Exportação do Windows](#ship-hard-drive) | SQL Server 2005 ou posterior | SQL Server 2005 ou posterior | [Limite de armazenamento da VM do Azure](https://azure.microsoft.com/pt-br/documentation/articles/azure-subscription-service-limits/) | Use o [Serviço de Importação/Exportação do Windows](../storage-import-export-service/) quando o método de cópia manual for muito lento, como com bancos de dados muito grandes |

## Tutorial do assistente para implantação de VM do Azure

Use o assistente para Implantar um Banco de Dados do SQL Server em uma VM do Microsoft Azure no Microsoft SQL Server Management Studio para migrar um banco de dados de usuário local do SQL Server 2005, SQL Server 2008, SQL Server 2008 R2, SQL Server 2012, SQL Server 2014 ou SQL Server 2016 (até 1 TB) para o SQL Server 2014 ou SQL Server 2016 em uma máquina virtual do Azure. Use esse assistente para migrar um banco de dados de usuário para uma máquina virtual do Azure existente ou para uma VM do Azure com o SQL Server criado pelo assistente durante o processo de migração. Ao migrar um banco de dados para uma versão mais recente do SQL Server, o banco de dados será atualizado automaticamente durante o processo.

### Obter a versão mais recente do assistente para Implantar um Banco de Dados do SQL Server em uma VM do Microsoft Azure

Use a versão mais recente do Microsoft SQL Server Management Studio para SQL Server para garantir que você tenha a versão mais recente do assistente para Implantar um Banco de Dados do SQL Server em uma VM do Microsoft Azure A versão mais recente desse assistente incorpora as atualizações mais recentes ao portal do Azure e oferece suporte às imagens mais recentes de VM do Azure na galeria (as versões mais antigas do assistente podem não funcionar). Para obter a versão mais recente do Microsoft SQL Server Management Studio para SQL Server, [baixe-a](http://go.microsoft.com/fwlink/?LinkId=616025)e instale-a em um computador cliente com conectividade com o banco de dados que você planeja migrar e com a Internet.

### Configurar a máquina virtual do Azure existente e a instância do SQL Server (se aplicável)

Se você estiver migrando para uma VM do Azure existente, as etapas de configuração a seguir serão necessárias:

- Configure a VM do Azure e a instância do SQL Server para habilitar a conectividade de outro computador seguindo as etapas na seção Conectar-se à instância de VM do SQL Server do SSMS em outro computador em [Como provisionar uma máquina virtual do SQL Server no Azure](../virtual-machines-provision-sql-server/#SSMS). Somente as imagens do SQL Server 2014 e SQL Server 2016 na galeria terão suporte se você estiver migrando usando o assistente.
- Configure um ponto de extremidade aberto para o serviço do adaptador de nuvem do SQL Server no gateway do Microsoft Azure com a porta privada 11435. Essa porta é criada como parte do provisionamento do SQL Server 2014 ou SQL Server 2016 em uma VM do Microsoft Azure. O adaptador de nuvem também cria uma regra do Firewall do Windows para permitir suas conexões TCP de entrada na porta padrão 11435. Esse ponto de extremidade permite que o assistente utilize o serviço do adaptador de nuvem para copiar os arquivos de backup da instância local para a VM do Azure. Para saber mais, consulte [Adaptador de nuvem para o SQL Server](https://msdn.microsoft.com/library/dn169301.aspx).

	![Criar Ponto de Extremidade de Adaptador de Nuvem](./media/virtual-machines-migrate-onpremises-database/cloud-adapter-endpoint.png)

### Execute o assistente para Implantar um Banco de Dados do SQL Server em uma VM do Microsoft Azure

1. Abra o Microsoft SQL Server Management Studio para Microsoft SQL Server 2016 e conecte-se à instância do SQL Server que contém o banco de dados de usuário que será migrado para uma VM do Azure.
2. Clique com o botão direito no banco de dados que você está migrando, aponte para Tarefas e, em seguida, clique em Implantar em uma VM do Microsoft Azure.

	![Iniciar o Assistente](./media/virtual-machines-migrate-onpremises-database/start-wizard.png)

3. Na página Introdução, clique em Avançar.
4. Na página Configurações da Origem, conecte-se à instância do SQL Server que contém o banco de dados que você pretende migrar para uma VM do Azure.
5. Especifique um local temporário para os arquivos de backup. Se você estiver se conectando a um servidor remoto, deverá especificar uma unidade de rede.

	![Configurações da Origem](./media/virtual-machines-migrate-onpremises-database/source-settings.png)

6. Clique em Avançar.
7. Na página de entrada do Microsoft Azure, clique em Entrar para entrar em sua conta do Azure.
8. Selecione a assinatura que você deseja usar e clique em Avançar.

	![Entrada do Azure](./media/virtual-machines-migrate-onpremises-database/azure-signin.png)

9. Na página Configurações de Implantação, você poderá especificar um nome de serviço de nuvem novo ou existente e o nome da máquina virtual:

 - Especifique um novo nome de serviço de nuvem e um nome de máquina virtual para criar um novo serviço de nuvem com uma nova máquina virtual do Azure usando uma imagem da galeria do SQL Server 2014 ou SQL Server 2016.

     - Se você especificar um novo nome de serviço de nuvem, especifique a conta de armazenamento que você usará.

     - Se você especificar um nome de serviço de nuvem existente, a conta de armazenamento será recuperada e inserida para você.

 - Especifique um nome de serviço de nuvem existente e o novo nome de máquina virtual para criar uma nova máquina virtual do Azure em um serviço de nuvem existente. Especifique apenas uma imagem de galeria do SQL Server 2014 ou do SQL Server 2016.
 - Especifique um nome de serviço de nuvem existente e o nome da máquina virtual para usar uma máquina virtual existente do Azure. Deve ser uma imagem criada usando uma imagem da galeria do SQL Server 2014 ou SQL Server 2016.

		![Deploymnent Settings](./media/virtual-machines-migrate-onpremises-database/deployment-settings.png)

10. Clique em Configurações
  - Se você tiver especificado um nome de serviço de nuvem existente e o nome da máquina virtual, deverá fornecer o nome de usuário e senha.

		![Azure machine settings](./media/virtual-machines-migrate-onpremises-database/azure-machine-settings.png)

	- Se você tiver especificado um novo nome de máquina virtual, deverá selecionar uma imagem na lista de imagens da galeria e fornecer as seguintes informações:
	  - Imagem – selecione somente o SQL Server 2014 ou o SQL Server 2016
		- Nome de Usuário
		- Nova senha
		- Confirmar senha
		- Local
		- Tamanho.
 	- Além disso, clique para aceitar o certificado gerado automaticamente para essa nova máquina virtual do Microsoft Azure e, em seguida, clique em OK.

	![Novas configurações de máquina do Azure](./media/virtual-machines-migrate-onpremises-database/azure-new-machine-settings.png)

11. Especifique o nome do banco de dados de destino se for diferente do nome do banco de dados de origem. Se o banco de dados de destino já existir, o sistema automaticamente incrementará o nome do banco de dados em vez de substituir o banco de dados existente.
12. Em seguida, clique em Avançar e em Concluir.

	![Resultados](./media/virtual-machines-migrate-onpremises-database/results.png)

13. Quando o assistente for concluído, conecte-se à sua máquina virtual e verifique se o banco de dados foi migrado.
14. Se você criou uma nova máquina virtual, configure a máquina virtual do Azure e a instância do SQL Server seguindo as etapas na seção Conectar-se à instância de máquina virtual do SQL Server do SSMS em outro computador em [Como provisionar uma máquina Virtual do SQL Server no Azure](../virtual-machines-provision-sql-server/#SSMS).

## Fazer backup em arquivo e copiar para a VM e restaurar

Use esse método quando não puder usar o assistente para Implantar um Banco de Dados do SQL Server em uma VM do Microsoft Azure porque está migrando para uma versão do SQL Server anterior ao SQL Server 2014 ou o arquivo de backup tiver mais de 1 TB. Se o arquivo de backup tiver mais de 1 TB, você deverá dividi-lo, já que o tamanho máximo de um disco de VM é de 1 TB. Use as seguintes etapas gerais para migrar um banco de dados do usuário usando este método manual:

1.	Execute um backup de banco de dados completo para um caminho local.
2.	Crie ou carregue uma máquina virtual com a versão do SQL Server desejada.
3.	Provisione a máquina virtual usando as etapas em [Como provisionar uma máquina Virtual do SQL Server no Azure](../virtual-machines-provision-sql-server/#SSMS).
4.	Copie os arquivos de backup para sua VM usando a área de trabalho remota, Windows Explorer ou comando de cópia em um prompt de comando.

## Fazer backup para URL e restaurar

Use o método de [fazer backup para URL](https://msdn.microsoft.com/library/dn435916.aspx) quando não for possível usar o assistente para Implantar um Banco de Dados do SQL Server em uma VM do Microsoft Azure porque o arquivo de backup tem mais de 1 TB e você estiver migrando de e para o SQL Server 2016. Para bancos de dados com menos de 1 TB ou executando uma versão do SQL Server anterior ao SQL Server 2016, recomenda-se o uso do assistente. Com o SQL Server 2016, conjuntos de backup divididos têm suporte, são recomendados para um melhor desempenho e são necessários para exceder os limites de tamanho por blob. Para bancos de dados muito grandes, é recomendado o uso do [Serviço de Importação/Exportação do Windows](../storage-import-export-service/).

## Desanexar e copiar a URL e anexar da URL

Use esse método quando você planejar [armazenar esses arquivos usando o serviço de armazenamento de blobs do Azure](https://msdn.microsoft.com/library/dn385720.aspx) e anexá-los ao SQL Server em execução em uma VM do Azure, particularmente com bancos de dados muito grandes. Use as seguintes etapas gerais para migrar um banco de dados do usuário usando este método manual:

1.	Desanexe os arquivos de banco de dados da instância de banco de dados local.
2.	Copie os arquivos do banco de dados desanexado para armazenamento de blobs do Azure usando o [Utilitário de linha de comando AZCopy](../storage-use-azcopy/).
3.	Anexe os arquivos de banco de dados da URL do Azure para a instância do SQL Server na VM do Azure.

## Converter a VM e carregar a URL e implantar como uma nova VM

Use esse método para migrar todos os sistemas e bancos de dados de usuário em uma instância local do SQL Server para a máquina virtual do Azure. Use as seguintes etapas gerais para migrar de uma instância inteira do SQL Server usando este método manual:

1.	Converter máquinas físicas ou virtuais para VHDs do Hyper-V usando o [Conversor de Máquina Virtual da Microsoft](http://technet.microsoft.com/library/dn873998.aspx).
2.	Carregue arquivos de VHD no armazenamento do Azure usando o [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3.	Implante uma nova máquina virtual usando o VHD carregado.

> [AZURE.NOTE]Para migrar um aplicativo inteiro, use o [Azure Site Recovery](../services/site-recovery/).

## Remeter a unidade de disco rígido

Use o [método do Serviço de Importação/Exportação do Azure](../storage-import-export-service/) para transferir grandes quantidades de dados de arquivo para o armazenamento de Blob do Azure em situações onde o carregamento pela rede é extremamente dispendioso ou inviável. Com esse serviço, você envia um ou mais discos rígidos contendo esses dados para um data center do Azure, onde os dados serão carregados para sua conta de armazenamento.

<!---HONumber=July15_HO5-->