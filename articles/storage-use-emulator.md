<properties 
	pageTitle="Usar o Emulador de Armazenamento do Azure para desenvolvimento e teste" 
	description="Aprenda a usar o Emulador de Armazenamento do Azure para desenvolvimento e teste." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>
<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Usar o Emulador de Armazenamento do Azure para desenvolvimento e teste

## Visão geral
O emulador de armazenamento do Microsoft Azure fornece um ambiente local que emula os serviços de blob, fila e tabela do Azure para fins de desenvolvimento. Usando o emulador de armazenamento, você pode testar seu aplicativo nos serviços de armazenamento localmente, sem gerar custos.

> [AZURE.NOTE] O emulador de armazenamento está disponível como parte do SDK do Microsoft Azure. Você também pode instalar o emulador de armazenamento como um pacote autônomo.
Para configurar o emulador de armazenamento, você deve ter privilégios administrativos no computador. 
> Observe que os dados criados em uma versão do emulador de armazenamento não são garantia de estar acessível ao usar uma versão diferente. Se você precisar persistir seus dados a longo prazo, é recomendável que você armazene esses dados em uma conta de armazenamento do Azure e não no emulador de armazenamento.
 
Existem algumas diferenças entre o emulador de armazenamento e os serviços de armazenamento do Azure. Para obter mais informações sobre essas diferenças, consulte [Diferenças entre o emulador de armazenamento e serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/gg433135.aspx).

O emulador de armazenamento usa uma instância do Microsoft (r) SQL Server(tm) e o sistema de arquivos local para emular os serviços de armazenamento do Azure. Por padrão, o emulador de armazenamento é configurado para um banco de dados no Microsoft (r) SQL Server(tm) 2012 Express LocalDB. Você pode instalar o SQL Server Management Studio Express para gerenciar a instalação do LocalDB. O emulador de armazenamento se conecta ao SQL Server ou LocalDB usando a autenticação do Windows. Você pode optar por configurar o emulador de armazenamento para acessar uma instância local do SQL Server em vez de LocalDB usando a Referência da Ferramenta de Linha de Comando do Emulador de Armazenamento.

## Autenticando Solicitações

O emulador de armazenamento oferece suporte a apenas uma única conta fixa e uma chave de autenticação conhecida. Essa conta e chave são as únicas credenciais permitidas para uso com o emulador de armazenamento. Eles são:

    Account name: devstoreaccount1
    Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtrKBHBeksoGMGw==
    
> [AZURE.NOTE] A chave de autenticação suportada pelo emulador de armazenamento destina-se somente para testar a funcionalidade do seu código de autenticação de cliente. Ela não serve para fins de segurança. Você não pode usar sua conta de armazenamento de produção e a chave com o emulador de armazenamento. Observe também que você não deve usar a conta de desenvolvimento com dados de produção.
 

## Iniciar e inicializar o emulador de armazenamento
Para iniciar o emulador de armazenamento do Azure, selecione o botão Iniciar ou pressione a tecla Windows. Comece a digitar Emulador de Armazenamento do Azure e selecione o Emulador de Aarmazenamento do Azure na lista de aplicativos. 

Como alternativa, se o emulador de computação do Azure já estiver em execução, você poderá iniciar o emulador de armazenamento clicando com o botão direito do mouse no ícone de bandeja do sistema e selecionando Iniciar Emulador de Armazenamento para obter mais informações sobre como executar o emulador de computação, consulte [Executar um aplicativo do Azure no emulador de computação](https://msdn.microsoft.com/library/azure/hh403990.aspx).

Quando o emulador de armazenamento é iniciado, aparecerá uma linha de comando. Usamos esta linha de comando para iniciar e parar o emulador de armazenamento bem como limpar dados, obter o status atual e inicializar o emulador. Para obter mais informações, consulte [Referência da ferramenta de linha de comando do emulador de armazenamento](https://msdn.microsoft.com/library/azure/gg433005.aspx).

Quando a janela de linha de comando é fechada, o emulador de armazenamento continua a executar. Para exibir novamente a linha de comando, siga as etapas acima, como se estivesse iniciando o emulador de armazenamento.


Na primeira vez que você executar o emulador de armazenamento, o ambiente de armazenamento local é inicializado para você. Você pode usar a ferramenta da linha de comando do emulador de armazenamento para apontar para uma instância do banco de dados diferente ou reinicializar o banco de dados existente. O processo de inicialização cria um banco de dados no LocalDB e reserva portas HTTP para cada serviço de armazenamento local. Esta etapa requer privilégios administrativos. Para obter detalhes, consulte [Referência da ferramenta de linha de comando do emulador de armazenamento](https://msdn.microsoft.com/library/azure/gg433005.aspx).

## Sobre URIs do serviço de armazenamento

Como você endereça um recurso nos serviços de armazenamento do Azure difere dependendo se o recurso reside no Azure ou em serviços do emulador de armazenamento. Um esquema de URI é usado para endereçar um recurso de armazenamento no Azure e outro esquema URI é usado para endereçar um recurso de armazenamento no emulador de armazenamento. A diferença é devido ao fato de que o computador local não executa resolução de nomes de domínio. Ambos os esquemas de URI sempre incluem o nome da conta e o endereço do recurso que está sendo solicitado.

## Endereçando recursos de armazenamento do Azure na nuvem

No esquema de URI para endereçar recursos de armazenamento no Azure, o nome da conta faz parte do nome de host de URI e o recurso sendo endereçado faz parte do caminho do URI. O seguinte esquema de endereçamento básico é usado para acessar recursos de armazenamento:

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>


O `<account-name>` é o nome da sua conta de armazenamento. O `<service-name>` é o nome do serviço que está sendo acessado e o `<resource-path>` é o caminho para o recurso que está sendo solicitado. A lista a seguir mostra o esquema de URI para cada um dos serviços de armazenamento:

	Blob Service: <http|https>://<account-name>.blob.core.windows.net/<resource-path>
	Queue Service: <http|https>://<account-name>.queue.core.windows.net/<resource-path>
	Table Service: <http|https>://<account-name>.table.core.windows.net/<resource-path>

Por exemplo, o endereço a seguir pode ser usado para acessar um blob na nuvem:
    
    http://myaccount.blob.core.windows.net/mycontainer/myblob.txt

> [AZURE.NOTE] Você também pode associar um nome de domínio personalizado com um ponto de extremidade de armazenamento de Blob na nuvem e usar esse nome de domínio personalizado para contêineres e blobs de endereço. Consulte 
 
## Endereçando recursos de armazenamento do Azure local no emulador de armazenamento

No emulador de armazenamento, como o computador local não executa resolução de nomes de domínio, o nome da conta faz parte do caminho do URI. O esquema de URI para um recurso em execução no emulador de armazenamento segue este formato:

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

O seguinte formato é usado para endereçar recursos em execução no emulador de armazenamento:

	Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
	Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
	Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

Por exemplo, o endereço a seguir pode ser usado para acessar um blob no emulador de armazenamento:

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

> [AZURE.NOTE] HTTPS não é um protocolo permitido para endereçar recursos de armazenamento local.
 
## Endereçamento da conta secundária com RA-GRS
A partir da versão 3.1, a conta do emulador de armazenamento oferece suporte a replicação de georedundância com acesso de leitura (RA-GRS). Para recursos de armazenamento na nuvem e no emulador do local, você pode acessar o local secundário acrescentando -secundário no nome da conta. Por exemplo, o endereço a seguir pode ser usado para acessar um blob usando o secundário somente leitura no emulador de armazenamento:

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] Para obter acesso programático ao secundário com o emulador de armazenamento, use a Biblioteca Cliente de Armazenamento para .NET versão 3.2 ou posterior. Consulte a Referência de Biblioteca Cliente de Armazenamento para obter detalhes.
 
## Inicialize o emulador de armazenamento usando a ferramenta de linha de comando
Você pode usar a ferramenta de linha de comando do emulador de armazenamento para inicializar o emulador de armazenamento para apontar para uma instância do banco de dados diferente do padrão. Se você quiser usar a instância do banco de dados LocalDB padrão, não é necessário executar uma etapa de inicialização separada.

Essa ferramenta é instalada por padrão no diretório C:\Program Files(x86)\Microsoft SDKs\Azure\Storage Emulator\. A inicialização é executada automaticamente na primeira vez que você iniciar o emulador.

> [AZURE.NOTE] Você deve ter privilégios administrativos para executar o comando `init` descrito abaixo.

1. Clique no botão **Iniciar** ou pressione a tecla **Windows**. Comece a digitar `Emulador de Armazenamento do Azure` e selecione-o quando for exibido. Uma janela de prompt de comando será exibida.


2. Na janela do prompt de comando, digite o seguinte comando onde `<SQLServerInstance>` é o nome da instância do SQL Server. Para usar o LocalDb, especifique `(localdb)\v11.0` como a instância do SQL Server.

    	WAStorageEmulator init /sqlInstance <SQLServerInstance>

Você também pode usar o seguinte comando, que direciona o emulador para usar a instância padrão do SQL Server:

    WAStorageEmulator init /server .\\ 

Ou, você pode usar o comando a seguir, que inicializa novamente o banco de dados:

    WAStorageEmulator init /forceCreate 

## Referência da ferramenta de linha de comando do emulador de armazenamento

Começando na versão 3.0, ao iniciar o Emulador de Armazenamento, você verá um prompt de comando pop-up. Use o prompt de comando para iniciar e parar o emulador, bem como para consultar o status e executar outras operações.

> [AZURE.NOTE] Se você tiver o Emulador de Computação instalado, um ícone de bandeja do sistema será exibido quando você iniciar o Emulador de Armazenamento. Clique com o botão direito do mouse no ícone para revelar um menu, que fornece uma maneira gráfica de iniciar e parar o Emulador de Armazenamento.

### Sintaxe da linha de comando

	WAStorageEmulator [/start] [/stop] [/status] [/clear] [/init] [/help]

### Opções
Para exibir a lista de opções, digite `/help` no prompt de comando.

## Próximas etapas
- [Referência da ferramenta de linha de comando do emulador de armazenamento](https://msdn.microsoft.com/library/azure/gg433005.aspx)
- [Diferenças entre o Emulador de Armazenamento e os Serviços de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/gg433135.aspx)
- [Notas de versão do Emulador de Armazenamento](https://msdn.microsoft.com/library/azure/dn683879.aspx)

<!--HONumber=47-->
