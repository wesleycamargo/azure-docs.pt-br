<properties title="Split and Merge Service Tutorial" pageTitle="Tutorial de serviço de divisão e mesclagem SQL do Azure" description="Splitting and Merging with Elastic Scale" metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Tutorial de serviço de divisão e mesclagem do Elastic Scale

## Baixe os pacotes de divisão e mesclagem 
1. Baixe a versão mais recente do NuGet de [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget). 
2. Abra um prompt de comando e navegue até o diretório para o qual você baixou nuget.exe. 
3. Baixe o pacote de mesclagem e divisão mais recente para o diretório atual com o comando a seguir: 
`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge`  

As etapas acima baixam os arquivos de mesclagem e divisão para o diretório atual. Os arquivos são colocados em um diretório chamado **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x**, em que onde *x.x.xxx.x* reflete o número de versão. Localize os arquivos de Serviço de Divisão e Mesclagem no subdiretório **content\splitmerge\service** e os scripts do PowerShell de divisão e mesclagem (e as .dlls cliente requeridas) no subdiretório **content\splitmerge\powershell**.

##Pré-requisitos 

1. Crie um banco de dados SQL DB do Azure que será usado como o banco de dados de status de divisão e mesclagem. Vá para o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com). Na parte inferior esquerda, clique em **Novo**, então clique em **Serviços de Dados** -> **Banco de dados SQL** -> **Criar personalizado**. Preencha o nome do banco de dados e crie um novo usuário e senha. Certifique-se de registrar o nome e a senha para uso posterior.

2. Certifique-se de que o servidor de banco de dados SQL do Azure permita que os serviços do Windows Azure conectem-se a ele. Vá para o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), na painel esquerdo, cliente em **Bancos de dados SQL**. Então clique em **Servidores** na faixa de opções na parte superior da tela e selecione seu servidor. Então clique em **Configurar** na parte superior e garanta que a configuração dos **Serviços do Windows Azure** esteja definida para **Sim**.

    ![Allowed services][1]

3. Crie uma conta do Armazenamento do Azure que será usada para a saída de diagnóstico. Vá para o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com). Na parte inferior esquerda, clique em **Novo**, clique em **Serviços de Dados**, **Armazenamento** e então **Criação Rápida**. 

4. Crie um Serviço de Nuvem do Azure que conterá o seu serviço de divisão e mesclagem.  Vá para o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com). Na parte inferior esquerda, clique em **Novo**, então em **Calcular**, **Serviço de Nuvem** e **Criação Rápida**. 


## Configurando o serviço de divisão e mesclagem 

### Configuração do serviço de divisão e mesclagem 

1. Na pasta para a qual você baixou os bits de Divisão/Mesclagem, crie uma cópia do arquivo **ServiceConfiguration.Template.cscfg** enviado junto com **SplitMergeService.cspkg** e chame-o de **ServiceConfiguration.cscfg**.

2. Abra ServiceConfiguration.cscfg em seu editor de texto favorito. É recomendável usar o Visual Studio, uma vez que ele validará entradas, como o formato de impressões digitais de certificados. 

3. Crie um novo banco de dados ou escolha um banco de dados existente para servir como o banco de dados de status para operações de Divisão/Mesclagem e recuperar a cadeia de conexão desse banco de dados. Com o Banco de Dados SQL do Azure, a cadeia de conexão normalmente está na seguinte forma:

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .
4.    Insira essa cadeia de conexão no arquivo cscfg nas seções de função **SplitMergeWeb** e **SplitMergeWorker** na configuração ElasticScaleMetadata.

5.    A configuração do destino para os logs de diagnóstico requer uma conta de armazenamento no Azure. A configuração de Divisão/Mesclagem exige a cadeia de conexão para sua conta de armazenamento do Azure. A cadeia de conexão deve estar no formato:

        "DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccessKey" 
    
Para determinar a chave de acesso, vá para [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), clique na guia **Armazenamento** à esquerda, selecione o nome correspondente à sua conta de armazenamento e clique em **Gerenciar Chaves de Acesso** na parte inferior. Clique no botão **copiar** para a **Chave de Acesso Principal**.

![manage access keys][2]

6.    Insira o nome da conta de armazenamento e uma das chaves de acesso fornecidas nos espaços reservados na cadeia de conexão de armazenamento. Essa cadeia de conexão é usada nas seções de função **SplitMergeWeb** e **SplitMergeWorker** na configuração de **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. Potencialmente, você pode usar contas de armazenamento diferentes para funções diferentes. 

### Configurando a segurança 
Para obter instruções detalhadas para configurar a segurança do serviço, consulte [Configurações de Segurança de Elastic Scale](./sql-database-elastic-scale-configure-security.md).

Para fins de uma implementação de teste simples adequada para concluir este tutorial, um conjunto mínimo de etapas de configuração será executado para que o serviço fique ativo e em execução. Essas etapas habilitam somente um computador/conta a executá-las para se comunicar com o serviço.

### Criando um certificado autoassinado 

Crie um novo diretório e, nesse diretório, execute o seguinte comando usando uma janela do [Prompt de Comando do Desenvolvedor para Visual Studio](http://msdn.microsoft.com/pt-br/library/ms229859.aspx):

    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer

Você será solicitado a fornecer uma senha para proteger a chave privada. Digite uma senha forte e confirme-a. Em seguida, você será solicitado a informar a senha a ser usada mais uma vez depois disso. Clique em **Sim** ao final para importar para o repositório de Raiz de Autoridades de Certificação Confiáveis.

###    Criar um arquivo PFX 

Execute o seguinte comando a na mesma janela em que makecert foi executado; Use a mesma senha usada para criar o certificado:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### Importar o certificado do cliente para o repositório pessoal
1. No Windows Explorer, clique duas vezes em **MyCert.pfx**.
2. No **Assistente de Importação de Certificado**, selecione **Usuário Atual** e clique em **Avançar**.
3. Confirme o caminho do arquivo e clique em **Avançar**.
4. Digite a senha, deixe **Incluir todas as propriedades estendidas** marcado e clique em **Avançar**.
5. Deixe **Selecionar automaticamente o repositório de certificados[...]** marcado e clique em **Avançar**.
6. Clique em **Concluir** e em **OK**.

### Carregar o arquivo PFX para o serviço de nuvem

Vá para o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).

1. Selecione **Serviços de Nuvem**.
2. Selecione o serviço de nuvem criado anteriormente para o serviço de Divisão/Mesclagem.
3. Clique em **Certificados** no menu superior.
4. Clique em **Carregar** na barra inferior.
5. Selecione o arquivo PFX e digite a mesma senha de acima.
6. Depois de concluído, copie a impressão digital do certificado da nova entrada na lista.

### Atualize o arquivo de configuração de serviço

Cole a impressão digital do certificado copiada anteriormente para o atributo de impressão digital/valor destas configurações:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Observe que, para implantações de produção, certificados separados devem ser usados para a autoridade de certificação, o certificado do servidor e os certificados de cliente. Para obter instruções detalhadas sobre isso, consulte [Configuração de Segurança](./sql-database-elastic-scale-configure-security.md).

### Implantando o serviço de divisão e mesclagem
1. Vá para o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique na guia **Serviços de Nuvem** à esquerda e selecione o serviço de nuvem criado anteriormente. 
3. Clique em **Painel**. 
4. Escolha o ambiente de preparo, em seguida, clique em **Carregar uma nova implantação de preparo**.

    ![Staging][3]

5. Na caixa de diálogo, digite um rótulo de implantação. Para 'Pacote' e 'Configuração', clique em 'Do Local' e escolha o arquivo **SplitMergeService.cspkg** e seu arquivo. cscfg configurado anteriormente.
6. Certifique-se de que a caixa de seleção rotulada como **Implantar mesmo que uma ou mais funções contenham uma única instância** esteja selecionada.
7. Clique no botão de marca de escala no canto inferior direito para iniciar a implantação. Espere que isso possa levar alguns minutos para ser concluído.

![Upload][4]


## Solucionando problemas de implantação
Se sua função web não ficar online, provavelmente é um problema com a configuração de segurança. Verifique se a SSL está configurada como descrito acima.

Se sua função de trabalho não ficar online, mas sua função web obtiver sucesso, provavelmente é um problema na conexão com o banco de dados de status criado anteriormente. 

* Certifique-se de que a cadeia de conexão no .cscfg seja precisa. 
* Verifique se o servidor e o banco de dados existem e se a ID de usuário e a senha estão corretas.
* Para o banco de dados SQL do Azure, a cadeia de conexão deve estar no formato: 

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .

* Certifique-se de que o nome do servidor não comece com **https://**.
* Certifique-se de que o servidor de banco de dados SQL do Azure permita que os serviços do Windows Azure conectem-se a ele. Para fazer isso, abra https://manage.windowsazure.com, clique em "Bancos de dados SQL" à esquerda, clique em "Servidores" na parte superior e selecione seu servidor. Clique em **Configurar** na parte superior e garanta que a configuração dos **Serviços do Windows Azure** esteja definida para "Sim". (Consulte a seção Pré-requisitos na parte superior deste artigo).

* Revise os logs de diagnóstico para a instância do serviço de divisão/mesclagem. Abra uma instância do Visual Studio e, na barra de menus, clique em **Visualizar** e **Gerenciador de Servidores**. Clique no ícone do **Windows Azure** para conectar-se à sua assinatura do Azure. Em seguida, navegue para o Windows Azure -> <sua conta de armazenamento> -> Tabelas -> WADLogsTable. Para obter mais informações, consulte [Procurando recursos de armazenamento com o Gerenciador de Servidores](http://msdn.microsoft.com/pt-br/library/azure/ff683677.aspx) 

    ![][5]

    ![][6]

## Testando a implantação do serviço de divisão e mesclagem
### Conectando-se com um navegador da Web

Determine o ponto de extremidade da web do serviço de divisão e mesclagem. Você pode descobrir isso no Portal de gerenciamento do Azure indo para o **Painel** do seu serviço de nuvem e procurando no **URL do site** do site no lado direito. Substitua **http://** por **https://**, uma vez que as configurações de segurança padrão desabilitam o ponto de extremidade HTTP. Carregue a página para este URL no seu navegador.

### Testando com Scripts do PowerShell

A implantação e o ambiente podem ser testados executando os scripts do PowerShell de exemplo incluídos.

Os arquivos de script incluídos são:

1. **SetupSampleSplitMergeEnvironment.ps1** - configura uma camada de dados de teste para divisão e mesclagem (consulte a tabela a seguir para uma descrição detalhada)
2. **ExecuteSampleSplitMerge.ps1** - executa operações de teste na camada de dados de teste (consulte a tabela a seguir para uma descrição detalhada)
3. **GetMappings.ps1** - o script de exemplo de nível superior que imprime o estado atual dos mapeamentos de fragmento.
4. **ShardManagement.psm1** - o script auxiliar que encapsula a API ShardManagement
5. **SqlDatabaseHelpers.psm1** - o script auxiliar para criar e gerenciar bancos de dados SQL

<table style="width:100%">
  <tr>
    <th>Arquivo do PowerShell</th>
    <th>Etapas</th>
  </tr>
  <tr>
    <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
    <td>1.    Cria um banco de dados do gerenciador de mapa do fragmento</td>
  </tr>
  <tr>
    <td>2.    Cria dois bancos de dados do fragmento. 
  </tr>
  <tr>
    <td>3.    Cria um mapa do fragmento para esses bancos de dados (exclui qualquer mapa de fragmento existente nesses bancos de dados). </td>
  </tr>
  <tr>
    <td>4.    Cria uma tabela de exemplo pequena em ambos os fragmentos e preenche a tabela em um dos fragmentos.</td>
  </tr>
  <tr>
    <td>5.    Declara SchemaInfo para a tabela fragmentada.</td>
  </tr>

</table>

<table style="width:100%">
  <tr>
    <th>Arquivo do PowerShell</th>
    <th>Etapas</th>
  </tr>
<tr>
    <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
    <td>1.    Envia uma solicitação de divisão para o front-end da web do serviço de divisão e mesclagem, que divide metade dos dados do primeiro fragmento para o segundo fragmento.</td>
  </tr>
  <tr>
    <td>2.    Consulta o front-end da web para o status da solicitação de divisão e aguarda até a solicitação ser concluída.</td>
  </tr>
  <tr>
    <td>3.    Envia uma solicitação de mesclagem para o front-end da web do serviço de divisão e mesclagem, que move os dados do segundo fragmento de volta para o primeiro fragmento.</td>
  </tr>
  <tr>
    <td>4.    Consulta o front-end da web para o status da solicitação de mesclagem e aguarda até a solicitação ser concluída.</td>
  </tr>
</table>

##Usando o PowerShell para verificar a implantação

1.    Abra uma nova janela do PowerShell, navegue até o diretório para o qual você baixou o pacote de divisão e mesclagem e, em seguida, navegue para o diretório "powershell".
2.    Crie um servidor de banco de dados SQL do Azure (ou escolha um servidor existente) em que o gerenciador do mapa de fragmento e os fragmentos serão criados. 

Observação: O script SetupSampleSplitMergeEnvironment.ps1 cria todos esses bancos de dados no mesmo servidor por padrão para manter o script simples. Isso não é uma restrição do serviço de divisão e mesclagem em si.

		Um logon de autenticação com acesso de leitura/gravação para os bancos de dados será necessário para o serviço de divisão e mesclagem mover os dados e atualizar o mapa de fragmento. Uma vez que o serviço de divisão e mesclagem é executado na nuvem, no momento ele não dá suporte para autenticação integrada.

		Verifique se o SQL Server do Azure está configurado para permitir acesso do endereço IP do computador que executa esses scripts. Você pode encontrar essa configuração em SQL Server do Azure / configuração / endereços IP permitidos.

3.    Execute o script SetupSampleSplitMergeEnvironment.ps1 para criar o ambiente de exemplo. 

		Executar esse script apagará quaisquer estruturas de dados de gerenciamento de mapa fragmento existentes no banco de dados do gerenciador de mapa de fragmento e os fragmentos. Isso pode ser útil para executar novamente o script se você desejar reinicializar o mapa de fragmento ou fragmentos.

    Exemplo de linha de comando:

        .\SetupSampleSplitMergeEnvironment.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    
4.    Execute o script Getmappings.ps1 para exibir os mapeamentos que existem atualmente no exemplo de ambiente.

        .\GetMappings.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

5.    Execute o script ExecuteSampleSplitMerge.ps1 para executar uma operação de divisão (mover metade os dados no primeiro fragmento para o segundo fragmento) e, em seguida, uma operação de mesclagem (mover os dados novamente para o primeiro fragmento). Se você tiver configurado SSL e deixado o ponto de extremidade http desabilitado, use o ponto de extremidade https://.

    Exemplo de linha de comando:

        .\ExecuteSampleSplitMerge.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net' `
            -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' `
            -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'

		Se você receber o erro a seguir, provavelmente é um problema com o certificado do ponto de extremidade da web. Tente se conectar ao ponto de extremidade da Web com seu navegador da Web favorito e verifique se há um erro de certificado.

        Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.

    Se for bem-sucedido, o resultado deve ser semelhante ao seguinte:

        > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
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

6.    Experimente outros tipos de dados! Todos esses scripts usam um parâmetro - ShardKeyType opcional que permite especificar o tipo de chave. O padrão é Int32, mas você também pode especificar Int64, Guid ou binário. 

## Criando suas próprias solicitações 

O serviço pode ser usado por meio da IU da web ou importando e usando o módulo SplitMerge.psm1 PowerShell.

O serviço de divisão e mesclagem pode mover dados em tabelas fragmentadas e tabelas de referência. Uma tabela fragmentada tem uma coluna de chave de fragmentação e diferentes dados de linha em cada fragmento. Uma tabela de referência não é fragmentada para que contenha os mesmos dados de linha em cada fragmento. Tabelas de referência são úteis para dados que não são alterados com frequência e são usadas para associação a tabelas fragmentadas em consultas.

Para executar uma operação de divisão e mesclagem, você deve declarar as tabelas fragmentadas e as tabelas de referência que deseja mover. Isso é feito com a API **SchemaInfo**. Esta API está no namespace **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema**.

1.    Para cada tabela fragmentada, crie um objeto **ShardedTableInfo** que descreva o nome do esquema pai da tabela (opcional, o padrão é "dbo"), o nome da tabela e o nome da coluna nessa tabela que contém a chave de fragmentação.
2.    Para cada tabela de referência, crie um objeto de **ReferenceTableInfo** que descreva o nome do esquema pai da tabela (opcional, o padrão é "dbo") e o nome da tabela.
3.    Adicione os objetos TableInfo acima a um novo objeto **SchemaInfo**.
4.    Obtenha uma referência para um objeto **ShardMapManager** e chame **GetSchemaInfoCollection**.
5.    Adicione **SchemaInfo** a **SchemaInfoCollection**, fornecendo o nome do mapa de fragmentos.

Um exemplo disso pode ser visto no script SetupSampleSplitMergeEnvironment.ps1.

Observe que o serviço de divisão e mesclagem não cria o banco de dados de destino (ou o esquema para quaisquer tabelas no banco de dados) para você. Eles devem ser pré-criados antes de enviar uma solicitação ao serviço.


## Solucionar problemas
Você pode ver a mensagem a seguir ao executar os scripts powershell de exemplo:

    Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.

Esse erro significa que o certificado SSL não está configurado corretamente. Siga as instruções na seção 'Conectando-se com um navegador da Web'.

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
 
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/allowed-services.png
[2]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/manage.png
[3]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/staging.png
[4]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/upload.png
[5]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/storage.png
[6]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/logs.png
