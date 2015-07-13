<properties
	title="Elastic database Split-Merge tool tutorial"
	pageTitle="Tutorial de ferramenta da Divisão de Mesclagem do banco de dados elástico | Microsoft Azure"
	description="Divisão e mesclagem com ferramenta de banco de dados elástico"
	metaKeywords="elastic database tools, split and merge, Azure SQL Database sharding, elastic scale, splitting and merging elastic databases"
	services="sql-database" documentationCenter=""  
	manager="jeffreyg"
	authors="sidneyh"/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/08/2015"
	ms.author="sidneyh" />

# Tutorial de ferramenta da Divisão de Mesclagem do banco de dados elástico

## Baixe os pacotes de Divisão-Mesclagem
1. Baixe a versão mais recente do NuGet de [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).
2. Abra um prompt de comando e navegue até o diretório onde baixou o nuget.exe.
3. Baixe o pacote de mesclagem divisão mais recente no diretório atual com o comando abaixo:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge`  

As etapas acima baixam os arquivos de Divisão-Mesclagem para a pasta atual. Os arquivos são colocados em um diretório chamado **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** onde *x.x.xxx.x* reflete o número de versão. Localize os arquivos do serviço de Divisão-Mesclagem no subdiretório **content\splitmerge\service** e os scripts de Divisão-Mesclagem do PowerShell (e as .DLLs do cliente necessárias) no subdiretório **content\splitmerge\powershell**.

## Pré-requisitos

1. Crie um banco de dados do Banco de Dados SQL do Azure que será usado como o banco de dados de status de Divisão-Mesclagem. Vá para o [Portal de Visualização do Azure](https://ms.portal.azure.com). Crie um novo **banco de dados SQL**. Preencha o nome do banco de dados e crie novos usuário e senha. Certifique-se de registrar o nome e a senha para uso posterior.

2. Certifique-se de que o servidor de Banco de Dados SQL do Azure permite que os Serviços do Azure se conectem a ele. No [portal de visualização](https://ms.portal.azure.com), em **configurações de Firewall**, certifique-se de que a configuração **Permitir acesso aos serviços do Azure** esteja definida como **Ativada**. Clique no botão “Salvar”.

    ![Serviços permitidos][1]

3. Crie uma conta de Armazenamento do Azure que será usada para a saída de diagnóstico. Vá para o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com). Na parte inferior esquerda, clique em **Novo**, **Serviços de dados**, **Armazenamento** e em **Criação rápida**.

4. Crie um Serviço de nuvem do Azure que conterá o seu serviço de Divisão-Mesclagem. Vá para o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com). No canto inferior esquerdo, clique em **Novo**, em **Computação**, **Serviço de nuvem** e em **Criação rápida**.


## Configurando seu serviço de Divisão-Mesclagem

### Configuração do serviço de Divisão-Mesclagem

1. Na pasta onde você baixou os bits de Divisão/Mesclagem, crie uma cópia do arquivo **ServiceConfiguration.Template.cscfg** fornecido junto com **SplitMergeService.cspkg** e mude seu nome para **ServiceConfiguration.cscfg**.

2. Abra o ServiceConfiguration.cscfg no seu editor de texto favorito. É recomendável usar o Visual Studio pois ele validará as entradas, como o formato de impressões digitais de certificados.

3. Crie um novo banco de dados ou escolha um já existente para servir como o banco de dados de status para as operações de Divisão/Mesclagem e recupere a cadeia de conexão do banco de dados. Com o Banco de Dados SQL do Azure, a cadeia de caracteres de conexão normalmente tem o seguinte formato:

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .
4.    Insira essa cadeia de conexão no arquivo cscfg nas seções de função **SplitMergeWeb** e **SplitMergeWorker** na configuração de ElasticScaleMetadata.

5.    Para a função **SplitMergeWorker**, digite uma cadeia de caracteres de conexão válida para o armazenamento do Azure para a configuração **WorkerRoleSynchronizationStorageAccountConnectionString**.
        
### Configurando a segurança
Para obter instruções detalhadas configurar a segurança do serviço, consulte as [Configuração de segurança da divisão e mesclagem](../sql-database-elastic-scale-configure-security.md).

Para fins de implementação de teste simples, é adequado para concluir este tutorial, um conjunto mínimo de etapas de configuração será executada para colocar o serviço em operação. Essas etapas permite que somente o computador/conta que as executa se comunique com o serviço.

### Criando um certificado autoassinado

Crie um novo diretório e, nesse diretório, execute o seguinte comando usando uma janela de [Prompt de comando do desenvolvedor para o Visual Studio](http://msdn.microsoft.com/pt-br/library/ms229859.aspx):

    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer

Você precisará fornecer uma senha para proteger a chave privada. Digite uma senha forte e confirme-a. Em seguida, será solicitado que você digite a senha mais uma vez. Clique em **Sim** no final para importá-la no armazenamento Raiz de autoridades de certificação confiável.

### Criar um arquivo PFX

Execute o seguinte comando na mesma janela onde o makecert foi executado; use a mesma senha que você usou para criar o certificado:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### Importe o certificado do cliente no repositório pessoal
1. No Windows Explorer, clique duas vezes em **Mycert.pfx**.
2. No **Assistente de importação de certificado**, selecione o **Usuário** atual e clique em **Avançar**.
3. Confirme o caminho do arquivo e clique em **Avançar**.
4. Digite a senha, deixe **Incluir todas as propriedades estendidas** verificadas e clique em **Avançar**.
5. Deixe **Selecionar automaticamente o repositório de certificados[...]** marcada e clique em **Avançar**.
6. Clique em **Concluir** e em **OK**.

### Carregue o arquivo PFX para o serviço de nuvem

Vá para o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).

1. Selecione os **Serviços de nuvem**.
2. Selecione o serviço de nuvem criado anteriormente para o serviço de Divisão/Mesclagem.
3. Clique em **Certificados** no menu superior.
4. Clique em **Carregar** na barra de ferramentas inferior.
5. Selecione o arquivo PFX e digite a mesma senha como acima.
6. Depois de concluído, copie a impressão digital do certificado da nova entrada na lista.

### Atualize o arquivo de configuração de serviço

Cole a impressão digital do certificado copiado acima no atributo de impressão digital/valor dessas configurações. Para a função da web:

    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

Para a função de trabalho:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />


Observe que para implantações de produção devem ser usados certificados separados para a autoridade de certificação, para criptografia, o certificado do servidor e os certificados de cliente. Para obter instruções detalhadas sobre isso, consulte a [Configuração de segurança](../sql-database-elastic-scale-configure-security.md).

### Implantando seu serviço de Divisão-Mesclagem
1. Vá para o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique na guia **Serviços de nuvem** à esquerda e selecione o serviço de nuvem que você criou anteriormente.
3. Clique em **Painel**.
4. Escolha o ambiente de preparo e clique em **Carregar uma nova implantação de preparo**.

    ![Staging][3]

5. Na caixa de diálogo, digite um rótulo de implantação. Para 'Pacote' e 'Configuração', clique em 'Do local' e escolha o arquivo **SplitMergeService.cspkg** e seu arquivo .cscfg que você configurou anteriormente.
6. Certifique-se de que a caixa de seleção rotulada **Implantar mesmo se uma ou mais funções contiverem uma única instância** esteja marcada.
7. Clique no botão de escala no canto inferior direito para iniciar a implantação. Isso poderá levar alguns minutos para ser concluído.

![Carregar][4]


## Solucionando problemas de implantação
Se sua função web não ficar online, provavelmente é um problema com a configuração de segurança. Verifique se o SSL está configurado como descrito acima.

Se sua função de trabalho não fica online, mas sua função web tiver êxito, provavelmente é um problema na conexão com o banco de dados de status que você criou anteriormente.

* Certifique-se de que a cadeia de caracteres de conexão no seu .cscfg seja precisa.
* Verifique se o servidor e o banco de dados existem e se a ID de usuário e a senha estão corretas.
* Para o Banco de Dados SQL do Azure, a cadeia de conexão deve estar no formato:

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .

* Certifique-se de que o nome do servidor não começa com **https://**.
* Certifique-se de que o servidor de Banco de Dados SQL do Azure permite que os Serviços do Azure se conectem a ele. Para fazer isso, abra https://manage.windowsazure.com, clique em “Bancos de dados SQL” à esquerda, clique em “Servidores” na parte superior e selecione seu servidor. Clique em **Configurar** na parte superior e verifique se a configuração dos **Serviços do Azure** estão definidas como “Sim”. (Consulte a seção Pré-requisitos na parte superior deste artigo).

## Testando sua implantação do serviço de Divisão-Mesclagem
### Conectar-se com um navegador da web

Determine o ponto de extremidade da web do serviço de Divisão-Mesclagem. Você pode descobrir isso no Portal de gerenciamento do Azure indo para o **Painel** do seu serviço de nuvem e procurando na **URL do Site**, no lado direito. Substitua **http://** por **https://** desde que as configurações de segurança padrão desabilitem o ponto de extremidade HTTP. Carregue a página para este URL no seu navegador.

### Testando com scripts do PowerShell

A implantação e sue ambiente podem ser testados, executando os scripts de exemplo do PowerShell incluídos.

Os arquivos de script incluídos são:

1. **SetupSampleSplitMergeEnvironment.ps1** - configura uma camada de dados de teste para Divisão/Mesclagem (consulte a tabela abaixo para uma descrição detalhada)
2. **ExecuteSampleSplitMerge.ps1** - executa operações de teste no teste de camada de dados (consulte a tabela abaixo para uma descrição detalhada)
3. **GetMappings.ps1 – **o script de exemplo de nível superior que imprime o estado atual dos mapeamentos de fragmento.
4. **ShardManagement.psm1** – o script auxiliar que encapsula a API ShardManagement
5. **SqlDatabaseHelpers.psm1** – o script auxiliar para criar e gerenciar bancos de dados SQL

<table style="width:100%">
  <tr>
    <th>Arquivo do PowerShell</th>
    <th>Etapas</th>
  </tr>
  <tr>
    <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
    <td>1. Cria um banco de dados do gerenciador de mapa do fragmento</td>
  </tr>
  <tr>
    <td>2. Cria 2 bancos de dados do fragmento.
  </tr>
  <tr>
    <td>3. Cria um mapa do fragmento para esses bancos de dados (exclui quaisquer mapas do fragmento existentes nesses bancos de dados). </td>
  </tr>
  <tr>
    <td>4. Cria uma pequena tabela de exemplo em ambos os fragmentos e preenche a tabela em um dos fragmentos.</td>
  </tr>
  <tr>
    <td>5. Declara o SchemaInfo para a tabela fragmentada.</td>
  </tr>

</table>

<table style="width:100%">
  <tr>
    <th>Arquivo do PowerShell</th>
    <th>Etapas</th>
  </tr>
<tr>
    <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
    <td>1. Envia uma solicitação divisão para o front-end da Web do Serviço de Divisão-Mesclagem, que divide metade dos dados do primeiro fragmento para o segundo.</td>
  </tr>
  <tr>
    <td>2. Elege o front-end da web para o status da solicitação de divisão e aguarda até que a solicitação seja concluída.</td>
  </tr>
  <tr>
    <td>3. Envia uma solicitação de mesclagem ao front-end da Web do Serviço de Divisão-Mesclagem, que move os dados do segundo fragmento para o primeiro.</td>
  </tr>
  <tr>
    <td>4. Elege o front-end da web para o status da solicitação de mesclagem e aguarda até que a solicitação seja concluída.</td>
  </tr>
</table>

##Usando o PowerShell para verificar sua implantação

1.    Abra uma nova janela do PowerShell, navegue até o diretório onde baixou o pacote de Divisão-Mesclagem e, em seguida, navegue para o diretório do “powershell”.
2.    Crie um servidor de banco de dados SQL do Azure (ou escolha um servidor existente) onde o gerenciador do mapa do fragmento e os fragmentos serão criados.

    Observação: o script SetupSampleSplitMergeEnvironment.ps1 cria todos esses bancos de dados no mesmo servidor por padrão para manter o script simples. Isso não é uma restrição do Serviço de Divisão-Mesclagem em si.

    Um logon de autenticação do SQL com acesso de leitura/gravação para os bancos de dados será necessário para que o serviço de Divisão-Mesclagem mova os dados e atualize o mapa do fragmento. Desde que o Serviço de Divisão-Mesclagem seja executado na nuvem, ele atualmente não dá suporte à Autenticação integrada.

    Verifique se o SQL Server do Azure está configurado para permitir acesso do endereço IP do computador que executa esses scripts. Você pode encontrar essa configuração em SQL Server do Azure / configuração / endereços de IP permitidos.

3.    Execute o script SetupSampleSplitMergeEnvironment.ps1 para criar o ambiente de exemplo.

    A execução desse script apagará quaisquer estruturas de dados de gerenciamento de mapa do fragmento existentes no banco de dados do gerenciador do mapa do fragmento e nos fragmentos. Ele pode ser útil para executar novamente o script, se desejar reinicializar o mapa do fragmento ou os fragmentos.

    Linha de comando de exemplo:

        .\SetupSampleSplitMergeEnvironment.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

4.    Execute o script Getmappings.ps1 para exibir os mapeamentos que existem atualmente no ambiente de exemplo.

        .\GetMappings.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

5.    Execute o script ExecuteSampleSplitMerge.ps1 para executar uma operação de divisão (mover metade dos dados no primeiro fragmento para o segundo) e, em seguida, uma operação de mesclagem (mover os dados de volta para o primeiro fragmento). Se você configurou o SSL e deixou o ponto de extremidade http desabilitado, verifique se, ao invés disso, usou o ponto de extremidade https://.

    Linha de comando de exemplo:

        .\ExecuteSampleSplitMerge.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net' `
            -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' `
            -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'

    Se você receber o erro abaixo, provavelmente é um problema com o certificado do ponto de extremidade da Web. Tente se conectar ao ponto de extremidade da Web com seu navegador da Web favorito e verifique se há um erro de certificado.

        Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.

    Se for bem-sucedido, a saída deve se parecer com isso:

        > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' –CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
        Sending split request
        Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
        Polling split-merge request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
        Sending merge request
        Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
        Polling request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.

6.    Experimente outros tipos de dados! Todos esses scripts usam um parâmetro de - ShardKeyType opcional que permite que você especifique o tipo de chave. O padrão é Int32, mas você também pode especificar Int64, Guid ou binário.

## Criando suas próprias solicitações

O serviço pode ser usado por meio da IU da web ou importando e usando o módulo SplitMerge.psm1 do PowerShell que irá enviar suas solicitações por meio da função web.

O serviço de Divisão-Mesclagem pode mover dados em tabelas fragmentadas e tabelas de referência. Uma tabela fragmentada possui uma coluna de chave de fragmentação e tem diferentes dados de linha em cada fragmento. Uma tabela de referência não é fragmentada para que ele contenha os mesmos dados de linha em cada fragmento. As tabelas de referência são úteis para dados que não mudam com frequência e são usadas para associação com tabelas fragmentadas em consultas.

Para executar uma operação de Divisão-Mesclagem, você deve declarar as tabelas fragmentadas e as tabelas de referência que deseja mover. Isso é feito com a API **SchemaInfo**. Esta API está no namespace **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema**.

1.    Para cada tabela fragmentada, crie um objeto **ShardedTableInfo** que descreve o nome do esquema da tabela pai (opcional, o padrão é “dbo”), o nome da tabela e o nome da coluna na tabela que contém a chave de fragmentação.
2.    Para cada tabela de referência, crie um objeto de **ReferenceTableInfo** que descreve o nome do esquema da tabela pai (opcional, o padrão é “dbo”) e o nome da tabela.
3.    Adicione os objetos TableInfo acima para um novo objeto **SchemaInfo**.
4.    Obtenha uma referência para um objeto **ShardMapManager** e chame **GetSchemaInfoCollection**.
5.    Adicione o **SchemaInfo** ao **SchemaInfoCollection**, fornecendo o nome do mapa de fragmento.

Um exemplo disso pode ser visto no script SetupSampleSplitMergeEnvironment.ps1.

Observe que o serviço de Divisão-Mesclagem não cria o banco de dados de destino (ou o esquema para todas as tabelas no banco de dados) para você. Eles devem ser criados previamente antes de enviar uma solicitação ao serviço.


## Solucionar problemas
Você pode ver a mensagem abaixo ao executar os scripts do powershell de exemplo:

    Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.

Esse erro significa que o certificado SSL não está corretamente configurado. Siga as instruções na seção 'Conectando-se com um navegador da Web'.

Se não for possível enviar solicitações, você verá isso:

 [Exceção] System.Data.SqlClient.SqlException (0x80131904): não foi possível encontrar o procedimento armazenado “dbo.InsertRequest”.

Nesse caso, verifique seu arquivo de configuração, em particular a configuração para **WorkerRoleSynchronizationStorageAccountConnectionString**. Esse erro normalmente indica que a função de trabalho não pôde inicializar com êxito o banco de dados de metadados no primeiro uso.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png
 

<!---HONumber=62-->