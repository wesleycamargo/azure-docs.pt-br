Você deve criar um ponto de extremidade de carga equilibrada para cada VM que hospeda uma réplica do Azure. Se você tiver réplicas em várias regiões, cada réplica para essa região deve estar no mesmo serviço de nuvem no mesmo VNet. Criação de réplicas do Grupo de Disponibilidade que abrangem várias regiões do Azure requer configurar diversas VNets. Para obter mais informações sobre como configurar através de conectividade VNet cruzada, consulte [Configurar conectividade VNet para VNet](../articles/vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. No portal do Azure, navegue até cada VM hospedando uma réplica e visualize os detalhes.

1. Clique na guia **Pontos de extremidade** para cada uma das VMs.

1. Verifique que o**Nome** e **Porta pública** do ponto de extremidade do ouvinte que você deseja usar não esteja em uso. No exemplo abaixo, o nome é "MyEndpoint" e a porta é "1433".

1. No seu cliente local, baixe e instale [o último módulo do PowerShell](https://azure.microsoft.com/downloads/).

1. Inicie o **PowerShell do Azure**. Uma nova sessão do PowerShell é aberta com os módulos administrativos do Azure carregados.

1. Executar **Get-AzurePublishSettingsFile**. Esse cmdlet direciona você a um navegador para baixar um arquivo de configurações de publicação para um diretório local. É possível que seja solicitado para inserir as suas credenciais para a assinatura do Aure.

1. Execute o comando **Import-AzurePublishSettingsFile** com o caminho do arquivo de configurações de publicação que você baixou:

		Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

	Após o arquivo de configurações de publicação tiver sido importado, você pode gerenciar sua assinatura do Azure na sessão do PowerShell.
<!----HONumber=AcomDC_0128_2016-->