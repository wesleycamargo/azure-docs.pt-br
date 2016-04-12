## Configurar uma cadeia de conexão de armazenamento

A Biblioteca de Cliente de Armazenamento do Azure para .NET oferece suporte ao uso de uma cadeia de conexão de armazenamento para configurar pontos de extremidade e credenciais para acesso a serviços de armazenamento. A melhor maneira de manter a cadeia de conexão de armazenamento é em um arquivo de configuração.

Para obter mais detalhes sobre as sequências de conexão, veja [Configurar uma cadeia de conexão para o Armazenamento do Azure](../articles/storage/storage-configure-connection-string.md).

> [AZURE.NOTE] Sua chave de conta de armazenamento é semelhante para a senha raiz da sua conta de armazenamento. Sempre tenha cuidado para proteger a chave de sua conta de armazenamento. Evite distribuí-la a outros usuários, embuti-la no código ou salvá-lo em um arquivo de texto sem formatação que esteja acessível a outras pessoas. Regenere a chave usando o Portal do Azure se você achar que ela pode ter sido comprometida.

### Determinar o ambiente de destino

Você tem duas opções de ambiente para executar os exemplos neste guia:

- Você pode executar o código em um emulador de armazenamento do Azure. O emulador de armazenamento é um ambiente local que emula uma conta de Armazenamento do Azure na nuvem. O emulador é uma opção gratuita para testar e depurar seu código enquanto o aplicativo está em desenvolvimento. O emulador usa uma conta e chave bem conhecidas. Para mais detalhes, confira [Usar o Emulador de Armazenamento do Azure para desenvolvimento e teste](../articles/storage/storage-use-emulator.md).
- Você pode executar o código em uma conta de Armazenamento do Azure na nuvem. 

Se você estiver selecionando uma conta de armazenamento na nuvem, copie a chave de acesso primário para sua conta de armazenamento a partir do Portal do Azure. Para saber mais, confira [Exibir e copiar chaves de acesso de armazenamento](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys).

> [AZURE.NOTE] Você pode utilizar o emulador de armazenamento como destino para evitar quaisquer custos associados ao Armazenamento do Azure. Todavia, se você escolher utilizar como destino uma conta de armazenamento do Azure na nuvem, os custos para realização das operações neste tutorial serão insignificantes.
	
### Configurar sua cadeia de conexão usando configuração do .NET

Se seu aplicativo é executado em um dispositivo móvel ou na área de trabalho, em uma máquina virtual do Azure ou em um aplicativo Web do Azure, salve a cadeia de conexão usando configuração .NET (*por exemplo,*, o arquivo `web.config` ou `app.config` do aplicativo). Armazene a cadeia de conexão usando o elemento `<appSettings>` conforme descrito a seguir. Substitua `account-name` pelo nome da sua conta de armazenamento e `account-key` pela chave de acesso da conta:

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
  		</appSettings>
	</configuration>

Por exemplo, a configuração será semelhante a:

	<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=account-key" />

Para direcionar o emulador de armazenamento, você pode usar um atalho que mapeia para o nome de conta e chave bem conhecidos. Nesse caso, a configuração da cadeia de conexão será:

	<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />

### Configurar sua cadeia de conexão para um serviço de nuvem do Azure

Ao usar os Serviços de Nuvem do Azure, é recomendável armazenar a cadeia de conexão usando o arquivo de configuração de serviço do Azure (arquivos `*.csdef` e `*.cscfg`). Confira [Como Criar e Implantar um Serviço de Nuvem](../articles/cloud-services/cloud-services-how-to-create-deploy.md) para obter detalhes sobre a configuração do serviço de nuvem do Azure.

<!---HONumber=AcomDC_0406_2016-->