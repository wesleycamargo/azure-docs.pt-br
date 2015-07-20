<properties 
	pageTitle="Escala Elástica de script com scripts" 
	description="Tarefas de escala elástica de script com runbooks do PowerShell e serviço de automação do Azure." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="Joseidz" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="Joseidz@microsoft.com"/>

# Gerenciando Escala Elástica com scripts


## Serviço de automação do Azure 

A [Automação do Azure](http://azure.microsoft.com/documentation/services/automation/) oferece um poderoso serviço de execução de fluxo de trabalho do PowerShell, muito útil para a plataforma do Azure. Agora você pode automatizar difíceis tarefas de manutenção de dentro das experiências comuns do portal do Azure. Basta criar um fluxo de trabalho do PowerShell (chamado de **runbook** na Automação do Azure), carregá-lo para a nuvem e agendar quando deseja que o runbook seja executado. Este documento fornece uma configuração de ponta-a-ponta de Automação do Azure para alguns exemplos de elasticidade de fragmento. Para obter mais informações, consulte o [comunicado de visualização](http://blogs.technet.com/b/in_the_cloud/archive/2014/04/15/announcing-the-microsoft-azure-automation-preview.aspx). Ou inscreva-se para uma [assinatura](https://account.windowsazure.com/PreviewFeatures?fid=automation) do Azure.

Neste exemplo, a Automação do Azure é usada como execução da carga de trabalho e do agendamento. Pense na Automação do Azure como seu [Agente SQL na nuvem](http://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/).

Além deste documento, aqui estão outros recursos:

* [Introdução à Automação do Azure](../automation-create-runbook-from-samples.md)
* [Passo a passo: introdução ao recurso de visualização da NOVA Automação do Microsoft Azure](http://blogs.technet.com/b/keithmayer/archive/2014/04/04/step-by-step-getting-started-with-windows-azure-automation.aspx) 
* [Automação do Microsoft Azure](http://blogs.technet.com/b/cbernier/archive/2014/04/08/microsoft-azure-automation.aspx) 
* Faça perguntas específicas sobre Automação do Azure no [Fórum de automação](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).  


## Pré-requisitos

[Inscreva-se](http://azure.microsoft.com/services/preview/) e [familiarize-se](../automation-create-runbook-from-samples.md) com o serviço de visualização da Automação do Microsoft Azure.


## Arquivos elasticidade de fragmento do PowerShell

O seguinte conjunto de arquivos do PowerShell contém os comandos básicos para realizar cenários de dimensionamento horizontal e vertical usando a Automação do Azure.

Estes exemplos ilustram como usar os módulos de exemplo do PowerShell para executar tarefas básicas de elasticidade de fragmentação. Em combinação com o serviço de Automação do Microsoft Azure e runbooks de Automação do Azure correspondentes, você pode criar trabalhos automatizados e agendados para provisionar um novo fragmento e/ou alterar o nível de desempenho de fragmentos específicos com base no conjunto de regras.

**SetupShardedEnvironment.ps1**: esse runbook do PowerShell executa uma configuração única de um ambiente fragmentado, completa com um gerenciador de mapa do fragmento e um mapa do fragmento de intervalo.

**ProvisionByDate.ps1**: provisiona um novo banco de dados antes da próxima carga de trabalho do dia. O banco de dados é criado e nomeado com base no formato de data (AAAAMMDD) e é registrado com o gerenciador de mapa do fragmento como um intervalo de [AAAAMMDD, AAAAMMDD + 1D).

**ProvisionBySize.ps1**: provisiona um novo banco de dados quando o banco de dados atual está ficando sem capacidade.

**ReduceServiceTier.ps1**: percorre os fragmentos em um mapa de fragmentos fornecido e determina se cada fragmento individual é um candidato para a redução do desempenho da camada. Dois critérios de determinam se o fragmento é um candidato: 1) a camada de serviço atual do fragmento e 2) a idade do banco de dados.

**ShardManagement.psm1**: fornece um conjunto de métodos para interagir com o gerenciador de mapa de fragmentos.

**SqlDatabaseHelpers.psm1**: fornece um conjunto de métodos para interagir com bancos de dados SQL do Azure.

**ShardElasticity.psm1**: fornece um conjunto de métodos para executar o dimensionamento horizontal, bem como as operações de dimensionamento verticais.

**ShardElasticityModule.psd1**: fornece um conjunto de métodos para interagir com a escala elástica e o Banco de Dados SQL do Azure.

## Custos

Observe que a execução dos scripts de exemplo do PowerShell resultarão na criação de bancos de dados que incorrerão em custos reais para o proprietário da assinatura. Os Bancos de Dados SQL do Azure subjacentes serão [cobrados a uma taxa](http://azure.microsoft.com/pricing/details/sql-database/) igual a qualquer outro banco de dados do Banco de Dados SQL do Azure. Os custos, começando em 1º de novembro, são:

* O runbook SetupShardedEnvironment cria o gerenciador de mapa de fragmento em um banco de dados Básico (US $0,0069/hora) e configura o primeiro fragmento em um banco de dados também Básico (US $0,0069/hora). 

* Ambos os runbooks ProvisionBySize e ProvisionByDate provisionarão um banco de dados S0 Padrão (US $0,0208/hora). Para contra-atacar esses custos, se executado em conjunto com o runbook ReduceServiceTier, a camada de serviço do banco de dados recentemente provisionado será reduzida de um S0 Padrão (US $0,0208/hora) para Básico (US $0,0069/hora) após um dia.

Por fim, dentro do escopo dos exemplos fornecidos, o uso da [Automação do Azure](http://azure.microsoft.com/pricing/details/automation/) atualmente não incorrerá em encargos para o proprietário da assinatura. Consulte a [página de preços da Automação do Azure](http://azure.microsoft.com/pricing/details/automation/) para obter mais detalhes.

## Para carregar os runbooks 

1. Baixe o arquivo **ShardElasticity.zip** e extraia o conteúdo.
2. [Adicione referências para os binários de Escala Elástica usando o NuGet](sql-database-elastic-scale-add-references-visual-studio.md)
3. Encontre o binário do cliente de Escala Elástica (**Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll**).
4. Coloque a DLL na pasta ShardElasticityModule e compacte a pasta. 
3. Na sua conta da Automação do Azure, carregue o arquivo ShardElasticityModule.zip como um **Ativo**. 
4. Na Automação do Azure, crie uma **Credencial de Ativo** chamada *ElasticScaleCredential* contendo o nome de usuário e a senha para seu servidor de Banco de Dados SQL do Azure. 
5. Crie uma **Variável de Ativo** chamada *SqlServerName* para o nome totalmente qualificado do servidor de Banco de Dados SQL do Azure. 
5. Carregue o **SetupShardedEnvironment.ps1**, **ProvisionBySize.ps1**, **ProvisionByDate.ps1** e **ProvisionByDate.ps1** como runbooks. 
6. Como uma operação única, teste o runbook **SetupShardedEnvironment.ps1** para configurar o ambiente fragmentado. 
7. Publique um ou mais dos runbooks restantes e vincule o(s) runbook(s) a uma agenda. 
8. Observe a saída do runbook na guia **TRABALHOS**. 

Se as Instruções de exemplo rápido não forem bem-sucedidas, consulte as Instruções detalhadas de exemplo, abaixo.

## Usar runbooks

1. Criar e empacotar um módulo do PowerShell 
2. Criar uma Conta da Automação do Microsoft Azure 
3. Carregar o módulo do PowerShell para a Automação do Azure como um ativo 
4. Criar a credencial de Automação do Azure e ativos de variável 
5. Carregar runbooks do PowerShell para a Automação do Azure 
6. Configurar um ambiente fragmentado 
7. Testar os runbooks de Automação 
8. Publicar os runbooks 
9. Agendar os runbooks 


## Criar e empacotar um módulo do PowerShell 

A primeira etapa é criar um módulo do PowerShell que faz referência aos assemblies de Escala Elástica e empacota este módulo para que ele esteja pronto para ser carregado para o Serviço de Automação do Azure como um ativo.

1. Baixe o arquivo "ShardElasticity.zip".
2. Extrai todo o conteúdo.

    ![Extrair todos][1]
3. Obtém a DLL cliente da Escala Elástica (ou seja, Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll) e copia os seguintes arquivos para a pasta "ShardElasticityModule" local, baixados na etapa 1. Isso pode ser feito de duas maneiras: 1) baixe a DLL por no link do pacote NuGet da Escala Elástica ou 2) no projeto do Kit de início da Escala Elástica [link] (deve ser criado), vá para \\bin\\Debug\\ para obter a DLL.

    ![Obter Dll][2]

4. Compacte a pasta ShardElasticityModule.

    A Automação do Azure exige várias convenções de nome: dado o nome do módulo ShardElasticityModule.psm1, o nome do arquivo zip deve corresponder exatamente a ele (ShardElasticityModule.zip). O arquivo zip contém a pasta ShardElasticityModule (correspondência de nome com o módulo), que por sua vez, contém o arquivo psm1. Se essa estrutura não for seguida, a Automação do Azure não poderá descompactar o módulo.

5.    Após ter verificado que o conteúdo e a estrutura da pasta compactada correspondem aos requisitos, vá para a próxima etapa. Ele deve ter esta aparência:

    ![dll][3]


## Assine para a visualização da automação do Azure

1. Vá para os [Recursos de Visualização do Azure](http://azure.microsoft.com/services/preview/).
    
2. Clique em **Experimentar**.

    ![Clique em Experimentar][8]

2. Vá para o [Portal do Microsoft Azure](https://manage.windowsazure.com/microsoft.onmicrosoft.com).
3. Clique em **Automação**.

    ![Automação][4]
4. Na parte inferior da tela, clique em **Criar**. 
5. No prompt mostrado abaixo, insira um nome de conta válida e clique na marca no canto inferior direito da caixa.

    ![Prompt][5] 
5. Vá para a próxima etapa. O êxito se parece com o gráfico a seguir.

    ![sucesso][6]

## Carregar o módulo do PowerShell para a Automação do Azure como um ativo 

Carregar o módulo do PowerShell acima para a sua Conta de Automação do Azure. Por exemplo, o módulo contém um conjunto de funções de Elasticidade de Fragmentos e de DLLs de Escala Elástica que podem ser referenciados por meio dos runbooks.

1. Clique em **ATIVOS** na faixa de opções na parte superior da tela.
2. Clique em **IMPORTAR MÓDULO** na parte inferior da página. 
3. Clique em **PROCURAR ARQUIVO...** e localize o arquivo **ShardElasticityModule**.zip acima. 
4. Depois que o arquivo correto tiver sido escolhido, clique na marca no canto inferior direito da caixa para importá-lo. O serviço de Automação do Azure importa o módulo. 
5. Vá para a próxima etapa. O sucesso é semelhante a este gráfico. Se o módulo não foi importado com êxito, verifique se o arquivo zip corresponde às convenções descritas acima.

    ![Ativos][10]
      
## Criar a credencial de Automação do Azure e ativos de variável 

Em vez de credenciais de hard coding e variáveis comumente usadas em runbooks, a Automação do Azure pode criar ativos de credencial e de variável, respectivamente, que podem ser referenciados nos vários runbooks. Por exemplo, alterar uma senha, em seguida, acontece em um só local.

1. Selecione a nova conta de Automação do Azure que você acabou de criar.
2. Na conta **ShardElasticityExamples**, clique em **ATIVOS** na faixa de opções.
3. Clique em **ADICIONAR CONFIGURAÇÃO** na parte inferior da tela.  
4. Clique em **ADICIONAR CREDENCIAL**. 

    ![Adicionar credenciais][9]
4. Selecione **Credencial do Windows PowerShell** como o **TIPO DE CREDENCIAL** e **ElasticScaleCredential** como o Nome. Uma descrição é opcional.  
5. Clique na seta no canto inferior direito da caixa. 

    Observação: para usar os runbooks sem modificação, utilize os nomes de variáveis literalmente, conforme fornecido nas instruções. Os nomes das variáveis são referenciados por runbooks. 
5. Insira o nome de usuário e a senha (duas vezes) para o servidor do Banco de Dados SQL do Azure no qual você deseja executar os exemplos de Elasticidade de fragmento. 
 
6. Para criar o ativo variável, clique em **ADICIONAR CONFIGURAÇÃO** e selecione **ADICIONAR VARIÁVEL**.

    ![Adicionar variável][7]
7. Para este tutorial, crie uma variável para o nome do servidor de Banco de Dados SQL do Azure, totalmente qualificado, sob a qual o gerenciador do mapa do fragmento e os bancos de dados fragmentados residirão. Selecione a **Cadeia de caracteres** como o **TIPO DE VARIÁVEL** e digite **SqlServerName**. Clique na seta para continuar. 
8. Insira o nome totalmente qualificado do servidor de Bando de Dados SQL do Azure como VALOR e clique no cheque. 
9. Você criou tanto uma credencial e quanto um ativo variável que serão usados nos runbooks de Elasticidade de fragmento. Vá para a próxima etapa. Sucesso é semelhante ao seguinte: 
    

## Carregar runbooks do PowerShell para a Automação do Azure 

Carregue os quatro runbooks de exemplo do PowerShell fornecidos.

1. Para carregar um novo Runbook de Automação do Azure, clique em **RUNBOOKS** na faixa de opções. 
2. Na parte inferior da tela, clique em **IMPORTAR**. 
3. Navegue até a pasta que contém o arquivo, selecione **SetupShardedEnvironment.ps1** e clique na marca de seleção. 
4. Repita as etapas 2 e 3 para os três runbooks do PowerShell restante (**ProvisionByDate.ps1**, **ProvisionBySize.ps1** e **ReduceServiceTier.ps1**). 
5. Vá para a próxima etapa. 

## Configurar um ambiente fragmentado 

A próxima etapa é executar o runbook **SetupShardedEnvironment** que provisiona um ambiente fragmentado, completo com um banco de dados de gerenciador de mapa do fragmento, um mapa do fragmento de intervalo e um fragmento do dia atual.

1. Selecione o runbook **SetupShardedEnvironment** clicando em seu nome. 
2. Clique em **CRIAR** na faixa de opções. 
3. Nessa tela, você verá o código (e poderá editar, se preferir) que compõe o runbook. Para configurar o ambiente fragmentado, clique no botão **TESTAR** na parte inferior da tela. Confirme que você deseja salvar e testar o runbook. 
4. Você pode observar o status do trabalho no painel de saída. Quando terminar, o servidor de Banco de Dados SQL do Azure especificado será populado com um banco de dados do gerenciador de mapa do fragmento, bem como um banco de dados do fragmento. O runbook **SetupShardedEnvironment** destina-se somente ao provisionamento do ambiente fragmentado e não é destinado para execução em uma agenda recorrente. Vá para a próxima etapa.  

## Testar o runbook de Automação 

Teste a execução bem-sucedida de cada um dos runbooks antes da publicação e o agendamento do runbook.

1. Clique em **RUNBOOK** na faixa de opções na parte superior da página. 
2. Clique no runbook **ProvisionByDate**.
3. Clique em **CRIAR** na faixa de opções na parte superior da página. 
4. Clique em **SALVAR** e, em seguida, em **TESTAR**. 5. Repita para o **ReduceServiceTier**. 

    Observe como **ProvisionBySize** e **ProvisionByDate**, ambos provisionam novos fragmentos (usando algoritmos diferentes), não é necessário executar **ProvisionByDate** neste momento.

## Publicar o runbook 
A próxima etapa é publicar o runbook para que ele possa ser agendado para executar periodicamente.

1. Clique em **PUBLICAR** na parte inferior da página. 
2. Clique em **Publicado**. 
3. Vá para a próxima etapa.
 
## Agendar o runbook 

A etapa final é criar e vincular uma agenda ao runbook publicado anteriormente.

1. Clique em **AGENDAR** na parte superior da página. 
2. Clique em **LINK PARA UMA NOVA AGENDA**.
3. Nomeie a agenda de forma apropriada e clique no botão de seta para a direita.
4. Configure o agendamento.
5. Quando terminar, clique em verificar na parte inferior da caixa.
6. Depois que o trabalho tiver sido executado com base no agendamento estabelecido anteriormente, clique na opção de **TRABALHOS** na faixa de opções da parte superior da página e selecione o trabalho agendado. 
## Conclusão 

Você criou e empacotou com êxito um módulo do PowerShell, carregou o módulo do PowerShell para Automação do Azure como um ativo e criou, testou, publicou e agendou um runbook. Para monitorar a integridade e o status do runbook, use as guias do painel e dos trabalhos.

Os exemplos fornecidos são apenas superficiais quanto ao que é possível combinando a Escala Elástica, o Banco de Dados SQL do Azure e a Automação do Azure. Experimente e desenvolva esses exemplos para habilitar seu aplicativo de Escala Elástica do Banco de Dados SQL do Azure para escalonar horizontal ou verticalmente, ou ambos.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-manage-scripting/zip.png
[2]: ./media/sql-database-elastic-scale-manage-scripting/dll.png
[3]: ./media/sql-database-elastic-scale-manage-scripting/lookslikethis.png
[4]: ./media/sql-database-elastic-scale-manage-scripting/automation.png
[5]: ./media/sql-database-elastic-scale-manage-scripting/prompt.png
[6]: ./media/sql-database-elastic-scale-manage-scripting/success.png
[7]: ./media/sql-database-elastic-scale-manage-scripting/add-variable.png
[8]: ./media/sql-database-elastic-scale-manage-scripting/sign-up.png
[9]: ./media/sql-database-elastic-scale-manage-scripting/add-credential.png
[10]: ./media/sql-database-elastic-scale-manage-scripting/assets.png
 

<!---HONumber=July15_HO2-->