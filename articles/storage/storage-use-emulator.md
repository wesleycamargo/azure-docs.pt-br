---
title: Usar o Emulador de Armazenamento do Azure para desenvolvimento e teste | Microsoft Docs
description: "O emulador de armazenamento do Azure fornece um ambiente de desenvolvimento local gratuito para desenvolvimento e teste no armazenamento do Azure. Saiba mais sobre o emulador de armazenamento, como as solicitações são autenticadas, como se conectar ao emulador por meio do seu aplicativo e como usar a ferramenta de linha de comando."
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: f480b059-df8a-4a63-b05a-7f2f5d1f5c2a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: e207c221a7294d1288e38c377d64327d889b29de
ms.openlocfilehash: 7a635fa0f63e851f63f56dc7eb3bca405603dec0


---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Usar o Emulador de Armazenamento do Azure para desenvolvimento e teste
## <a name="overview"></a>Visão geral
O emulador de armazenamento do Microsoft Azure fornece um ambiente local que emula os serviços de blob, fila e tabela do Azure para fins de desenvolvimento. Usando o emulador de armazenamento, você pode testar seu aplicativo contra os serviços de armazenamento locais, sem criar uma assinatura Azure ou incorrer em custos. Quando estiver satisfeito com o funcionamento de seu aplicativo no emulador, você pode alternar para usar uma conta de armazenamento do Azure na nuvem.

> [!NOTE]
> O emulador de armazenamento está disponível como parte do [SDK do Microsoft Azure](https://azure.microsoft.com/downloads/). Você também pode instalar o emulador de armazenamento usando o [instalador autônomo](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409). Para configurar o emulador de armazenamento, você deve ter privilégios administrativos no computador.
>
> O emulador de armazenamento atualmente é executado apenas no Windows.
>
> Os dados criados em uma versão do emulador de armazenamento não têm a garantia de estarem acessíveis ao usar outra versão. Se você precisar persistir seus dados a longo prazo, é recomendável que você armazene esses dados em uma conta de armazenamento do Azure e não no emulador de armazenamento.
>
> O emulador de armazenamento depende das bibliotecas do OData versão 5.6. Não há suporte para a substituição das DLLs do OData usadas pelo emulador de armazenamento em versões posteriores; além disso, a substituição causa um comportamento inesperado. No entanto, qualquer versão do OData com suporte no serviço de armazenamento pode ser usada para enviar solicitações para o emulador.
>
>

## <a name="how-the-storage-emulator-works"></a>Como o emulador de armazenamento funciona
O emulador de armazenamento usa uma instância do Microsoft SQL Server local e o sistema de arquivos local para emular os serviços de armazenamento do Azure. Por padrão, o emulador de armazenamento usa um banco de dados no Microsoft SQL Server 2012 Express LocalDB.  Você pode optar por configurar o emulador de armazenamento para acessar uma instância local do SQL Server em vez da instância do LocalDB. Para obter mais informações, consulte [Iniciar e inicializar o emulador de armazenamento](#start-and-initialize-the-storage-emulator) abaixo.

Você pode instalar o SQL Server Management Studio Express para gerenciar a instalação do LocalDB. O emulador de armazenamento se conecta ao SQL Server ou LocalDB usando a autenticação do Windows.

Existem algumas diferenças de funcionalidade entre o emulador de armazenamento e os serviços de armazenamento do Azure. Para obter mais informações sobre essas diferenças, consulte [Diferenças entre o emulador de armazenamento e o armazenamento do Azure](#differences-between-the-storage-emulator-and-azure-storage).

## <a name="authenticating-requests-against-the-storage-emulator"></a>Autenticando solicitações no emulador de armazenamento
Assim como ocorre com o Armazenamento do Azure na nuvem, cada solicitação feita no emulador de armazenamento deve ser autenticada, a menos que seja uma solicitação anônima. Você pode autenticar solicitações no emulador de armazenamento usando a autenticação de chave compartilhada ou uma SAS (Assinatura de Acesso Compartilhado).

### <a name="authentication-with-shared-key-credentials"></a>Autenticação com credenciais de chave compartilhada
[!INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Para obter mais informações sobre cadeias de conexão, consulte [Configurar cadeias de conexão do Armazenamento do Azure](storage-configure-connection-string.md).

### <a name="authentication-with-a-shared-access-signature"></a>Autenticação de assinatura de acesso compartilhado
Algumas bibliotecas de cliente de armazenamento do Azure, como a biblioteca do Xamarin, só oferecem suporte à autenticação com um token SAS(Assinatura de Acesso Compartilhado). Crie esse token SAS usando uma ferramenta ou um aplicativo que dá suporte à autenticação de Chave Compartilhada. Uma maneira fácil de gerar o token SAS é por meio do PowerShell do Azure:

1. Instale o PowerShell do Azure, se ainda não tiver feito isso. É recomendável usar a versão mais recente dos cmdlets do PowerShell do Azure. Para obter instruções, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azureps-cmdlets-docs) .
2. Abra o Azure PowerShell e execute os seguintes comandos, substituindo *ACCOUNT_NAME* e *ACCOUNT_KEY==* por suas próprias credenciais e *CONTAINER_NAME* por um nome de sua escolha:

```powershell
$context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="

New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

O URI da assinatura de acesso compartilhado resultante do novo contêiner deve ser semelhante ao seguinte:

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

A assinatura de acesso compartilhado criada com este exemplo é válida por um dia. A assinatura concede acesso completo (por exemplo, leitura, gravação, exclusão e lista) para os blobs do contêiner.

Para saber mais sobre assinaturas de acesso compartilhado, confira [Uso de SAS (Assinaturas de Acesso Compartilhado)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="start-and-initialize-the-storage-emulator"></a>Iniciar e inicializar o emulador de armazenamento
Para iniciar o emulador de armazenamento do Azure, selecione o botão Iniciar ou pressione a tecla Windows. Comece a digitar **Emulador de Armazenamento do Azure**, e selecione o emulador na lista de aplicativos.

Quando o emulador estiver em execução, você verá um ícone na área de notificação da barra de tarefas do Windows.

Quando o emulador de armazenamento é iniciado, aparecerá uma janela de linha de comando. Use essa janela da linha de comando para iniciar e parar o emulador de armazenamento, bem como para limpar dados, obter o status e inicializar o emulador. Para obter mais informações, consulte [Referência da ferramenta de linha de comando do emulador de armazenamento](#storage-emulator-command-line-tool-reference).

Quando a janela da linha de comando for fechada, o emulador de armazenamento continuará sendo executado. Para exibir novamente a linha de comando, siga as etapas anteriores, como se estivesse iniciando o emulador de armazenamento.

Na primeira vez que você executar o emulador de armazenamento, o ambiente de armazenamento local é inicializado para você. O processo de inicialização cria um banco de dados no LocalDB e reserva portas HTTP para cada serviço de armazenamento local.

O emulador de armazenamento é instalado por padrão no diretório C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator.

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inicializar o emulador de armazenamento para usar outro banco de dados SQL
Você pode usar a ferramenta de linha de comando do emulador de armazenamento para inicializar o emulador de armazenamento para apontar para uma instância do banco de dados SQL diferente da instância LocalDB padrão. É necessário executar a ferramenta de linha de comando com privilégios administrativos para inicializar o banco de dados de back-end do emulador de armazenamento:

1. Clique no botão **Iniciar** ou pressione a tecla **Windows**. Comece a digitar `Azure Storage Emulator` e selecione-o quando ele aparecer para abrir a ferramenta de linha de comando do emulador de armazenamento.
2. Na janela do prompt de comando, digite o seguinte comando em que `<SQLServerInstance>` é o nome da instância do SQL Server. Para usar o LocalDb, especifique `(localdb)\v11.0` como a instância do SQL Server.

        AzureStorageEmulator init /server <SQLServerInstance>

    Você também pode usar o seguinte comando, que direciona o emulador para usar a instância padrão do SQL Server:

        AzureStorageEmulator init /server .\\

    Se preferir, use o seguinte comando, que reinicializa o banco de dados na instância LocalDB padrão:

        AzureStorageEmulator init /forceCreate

Para obter mais informações sobre esses comandos, consulte [Referência da ferramenta de linha de comando do emulador de armazenamento](#storage-emulator-command-line-tool-reference).

## <a name="addressing-resources-in-the-storage-emulator"></a>Endereçamento de recursos no emulador de armazenamento
Os pontos de extremidade de serviço para o emulador de armazenamento são diferentes dos de uma conta de armazenamento do Azure. A diferença ocorre porque o computador local não executa a resolução de nomes de domínio, exigindo que os pontos de extremidade do emulador de armazenamento sejam endereços locais.

Ao lidar com um recurso em uma conta de armazenamento do Azure, use o esquema a seguir. O nome da conta faz parte do nome do host do URI e o recurso em questão faz parte do caminho do URI:

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>

Por exemplo, o seguinte URI é um endereço válido para um blob em uma conta de armazenamento do Azure:

    https://myaccount.blob.core.windows.net/mycontainer/myblob.txt

No emulador de armazenamento, como o computador local não executa resolução de nomes de domínio, o nome da conta faz parte do caminho do URI e não do nome do host. Use o seguinte esquema para um recurso em execução no emulador de armazenamento:

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

Por exemplo, o endereço a seguir pode ser usado para acessar um blob no emulador de armazenamento:

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

Os pontos de extremidade de serviço para o emulador de armazenamento são:

    Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
    Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
    Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Endereçamento da conta secundária com RA-GRS
A partir da versão 3.1, a conta do emulador de armazenamento oferece suporte a replicação de georedundância com acesso de leitura (RA-GRS). Para recursos de armazenamento na nuvem e no emulador do local, você pode acessar o local secundário acrescentando -secundário no nome da conta. Por exemplo, o endereço a seguir pode ser usado para acessar um blob usando o secundário somente leitura no emulador de armazenamento:

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt

> [!NOTE]
> Para obter acesso programático ao secundário com o emulador de armazenamento, use a Biblioteca Cliente de Armazenamento para .NET versão 3.2 ou posterior. Consulte a [Biblioteca de Clientes do Armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) para ver mais detalhes.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Referência da ferramenta de linha de comando do emulador de armazenamento
A partir da versão 3.0, ao iniciar o Emulador de Armazenamento, será exibida a janela da linha de comando. Use a janela de linha de comando para iniciar e parar o emulador, bem como para consultar o status e executar outras operações.

> [!NOTE]
> Se você tiver o emulador de computação do Microsoft Azure instalado, um ícone de bandeja do sistema será exibido ao iniciar o Emulador de Armazenamento. Clique com o botão direito do mouse no ícone para revelar um menu, que fornece uma maneira gráfica de iniciar e parar o Emulador de Armazenamento.
>
>

### <a name="command-line-syntax"></a>Sintaxe da linha de comando
    AzureStorageEmulator [start] [stop] [status] [clear] [init] [help]

### <a name="options"></a>Opções
Para exibir a lista de opções, digite `/help` no prompt de comando.

| Opção | Descrição | Command | Argumentos |
| --- | --- | --- | --- |
| **Iniciar** |Inicia o emulador de armazenamento. |`AzureStorageEmulator start [-inprocess]` |*-inprocess*: inicia o emulador no processo atual em vez de criar um novo processo. |
| **Parar** |Para o emulador de armazenamento. |`AzureStorageEmulator stop` | |
| **Status** |Imprime o status do emulador de armazenamento. |`AzureStorageEmulator status` | |
| **Limpar** |Limpa os dados em todos os serviços especificados na linha de comando. |`AzureStorageEmulator clear [blob] [table] [queue] [all]                                                    ` |*blob*: limpa os dados do blob. <br/>*fila*: limpa os dados da fila. <br/>*tabela*: limpa os dados de tabela. <br/>*todos*: limpa todos os dados em todos os serviços. |
| **Init** |Executa a inicialização única para configurar o emulador. |`AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate] [-inprocess]` |*-server nomedoServidor\nomedaInstância*: especifica o servidor que hospeda a instância do SQL. <br/>*-sqlinstance instanceName*: especifica o nome da instância SQL a ser usada na instância do servidor padrão. <br/>*-forcecreate*: força a criação do Banco de Dados SQL, mesmo se ele já existir. <br/>*-inprocess*: executa a inicialização no processo atual em vez de gerar um novo processo. O processo atual deve ser iniciado com permissões elevadas para executar a inicialização. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Diferenças entre o emulador de armazenamento e o armazenamento do Azure
Como o emulador de armazenamento é um ambiente emulado executado em uma instância SQL local, há diferenças na funcionalidade entre o emulador e uma conta de armazenamento do Azure na nuvem:

* O emulador de armazenamento oferece suporte a apenas uma única conta fixa e uma chave de autenticação conhecida.
* O emulador de armazenamento não é um serviço de armazenamento escalonável e não dá suporte a um grande número de clientes simultâneos.
* Conforme descrito em [Endereçamento de recursos no emulador de armazenamento](#addressing-resources-in-the-storage-emulator), os recursos são endereçados de forma diferente no emulador de armazenamento em comparação com uma conta de armazenamento do Azure. Essa diferença ocorre porque a resolução de nomes de domínio está disponível na nuvem, mas não no computador local.
* A partir da versão 3.1, a conta do emulador de armazenamento oferece suporte a replicação de georedundância com acesso de leitura (RA-GRS). No emulador, todas as contas têm RA-GRS habilitado e nunca há qualquer tempo de retardo entre as réplicas primárias e secundárias. As operações Obter Estatísticas do Serviço Blob, Obter Estatísticas do Serviço Fila e Obter Estatísticas do Serviço Tabela são compatíveis com a conta secundária e sempre retornarão o valor do elemento da resposta `LastSyncTime` , como a hora atual, de acordo com o Banco de Dados SQL subjacente.

    Para obter acesso programático ao secundário com o emulador de armazenamento, use a Biblioteca Cliente de Armazenamento para .NET versão 3.2 ou posterior. Consulte a [Biblioteca de Clientes do Armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) para ver mais detalhes.
* O serviço de arquivo e os pontos de extremidade de serviço de protocolo SMB não têm suporte no momento no emulador de armazenamento.
* Se você usar uma versão dos serviços de armazenamento que ainda não tem suporte no emulador, o emulador de armazenamento retornará um erro VersionNotSupportedByEmulator (código de status HTTP 400 – Solicitação Incorreta).

### <a name="differences-for-blob-storage"></a>Diferenças do armazenamento de blob
As diferenças a seguir aplicam-se ao armazenamento de blob no emulador:

* O emulador de armazenamento só dá suporte a tamanhos de blob de até 2 GB.
* A cópia incremental permite que os instantâneos de blobs substituídos sejam copiados, o que retorna uma falha no serviço.
* O recurso Obter Diferença de Intervalos de Páginas não funciona entre os instantâneos copiados com o uso de um Blob de Cópia Incremental.
* Uma operação Put Blob poderá ser bem-sucedida em um blob existente no emulador de armazenamento com uma concessão ativa, mesmo que a ID de concessão não tenha sido especificada na solicitação.
* As operações de blob de anexo não são compatíveis com o emulador. Tentar uma operação em um blob de anexo retornará um erro FeatureNotSupportedByEmulator (código de status HTTP 400 - Solicitação incorreta).

### <a name="differences-for-table-storage"></a>Diferenças do armazenamento de tabela
As diferenças a seguir aplicam-se ao armazenamento de tabela no emulador:

* As propriedades de data no serviço Tabela no emulador de armazenamento oferecem suporte apenas à faixa com suporte pelo SQL Server 2005 (*ou seja*, é necessário que elas sejam posteriores a 1º de janeiro de 1753). Todas as datas anteriores a 1º de janeiro de 1753 são alteradas para esse valor. A precisão das datas é limitada à precisão do SQL Server 2005, o que significa que as datas são precisas a 1/300 de um segundo.
* O emulador de armazenamento dá suporte a valores de propriedade de chave de linha e chave de partição de menos de 512 bytes cada. Além disso, o tamanho total do nome de conta, nome de tabela e nomes de propriedade de chave juntos não pode exceder 900 bytes.
* O tamanho total de uma linha em uma tabela no emulador de armazenamento é limitado a menos de 1 MB.
* No emulador de armazenamento, as propriedades de tipo de dados `Edm.Guid` ou `Edm.Binary` só oferecem suporte aos operadores de comparação `Equal (eq)` e `NotEqual (ne)` nas cadeias de filtro de consulta.

### <a name="differences-for-queue-storage"></a>Diferenças do armazenamento de fila
Não existem diferenças específicas para o armazenamento de fila no emulador.

## <a name="storage-emulator-release-notes"></a>Notas de versão do emulador de armazenamento
### <a name="version-46"></a>Versão 4.6
* O emulador de armazenamento agora dá suporte à versão 2016-05-31 dos serviços de armazenamento nos pontos de extremidade de serviço Blob, Fila e Tabela.

### <a name="version-45"></a>Versão 4.5
* Foi corrigido um erro que causava a falha da inicialização e instalação do emulador de armazenamento quando o banco de dados de backup era renomeado.

### <a name="version-44"></a>Versão 4.4
* O emulador de armazenamento agora dá suporte à versão 2015-12-11 dos serviços de armazenamento dos pontos de extremidade dos serviços de Blob, Fila e Tabela.
* A coleta de lixo do emulador de armazenamento de dados de blob agora é mais eficiente ao lidar com um grande número de blobs.
* Foi corrigido um bug que fazia o contêiner ACL XML ser validado de forma ligeiramente diferente de como o serviço de armazenamento o faz.
* Foi corrigido um bug que às vezes fazia com que valores máx. e mín. de DateTime fossem relatados no fuso horário incorreto.

### <a name="version-43"></a>Versão 4.3
* O emulador de armazenamento agora dá suporte à versão 2015-07-08 dos serviços de armazenamento dos pontos de extremidade dos serviços de Blob, Fila e Tabela.

### <a name="version-42"></a>Versão 4.2
* O emulador de armazenamento agora dá suporte à versão 2015-04-05 dos serviços de armazenamento dos pontos de extremidade dos serviços de Blob, Fila e Tabela.

### <a name="version-41"></a>Versão 4.1
* O emulador de armazenamento agora dá suporte à versão 2015-02-21 dos serviços de armazenamento em pontos de extremidade de serviço Blob, Fila e Tabela, com exceção dos novos recursos de Blob de Acréscimo.
* Se você usar uma versão dos serviços de armazenamento que ainda não tem suporte no emulador, o emulador retornará uma mensagem de erro significativa. É recomendável usar a versão mais recente do emulador. Se você encontrar um erro VersionNotSupportedByEmulator (código de status HTTP 400 - Solicitação incorreta), baixe a versão mais recente do emulador de armazenamento.
* Correção do bug em que uma condição de corrida fazia com que dados de entidade de tabela ficassem incorretos durante operações de mesclagem simultâneas.

### <a name="version-40"></a>Versão 4.0
* O emulador de armazenamento executável foi renomeado para *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Versão 3.2
* O emulador de armazenamento agora dá suporte à versão 2014-02-14 dos serviços de armazenamento dos pontos de extremidade dos serviços de Blob, Fila e Tabela. No momento, não há suporte para pontos de extremidade de serviço de Arquivo no emulador de armazenamento. Consulte [Controle de versão dos serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd894041.aspx) para obter detalhes sobre a versão 2014-02-14.

### <a name="version-31"></a>Versão 3.1
* Armazenamento com redundância geográfica com acesso de leitura (RA-GRS) agora tem suporte no emulador de armazenamento. As operações Obter Estatísticas do Serviço Blob, Obter Estatísticas do Serviço Fila e Obter Estatísticas do Serviço Tabela são compatíveis com a conta secundária e sempre retornarão o valor do elemento da resposta LastSyncTime, como a hora atual, de acordo com o banco de dados SQL subjacente. Para obter acesso programático ao secundário com o emulador de armazenamento, use a Biblioteca Cliente de Armazenamento para .NET versão 3.2 ou posterior. Consulte a Referência da Biblioteca de Clientes do Armazenamento do Microsoft Azure para .NET para ver mais detalhes.

### <a name="version-30"></a>Versão 3.0
* O emulador de armazenamento do Azure não é mais fornecido no mesmo pacote que o emulador de computação.
* A interface gráfica do usuário do emulador de armazenamento é preterida por uma interface de linha de comando programável por script. Para obter detalhes sobre a interface de linha de comando consulte Referência da ferramenta de linha de comando do emulador de armazenamento. A interface gráfica continuará presente na versão 3.0, mas só poderá ser acessada quando o emulador de computação for instalado, clicando com o botão direito no ícone de bandeja do sistema e selecionando Mostrar IU do Emulador de Armazenamento.
* A versão 2013-08-15 dos serviços de armazenamento do Azure agora tem total suporte. (Anteriormente nesta versão só tinha suporte do emulador de armazenamento versão 2.2.1 Preview.)



<!--HONumber=Dec16_HO3-->


