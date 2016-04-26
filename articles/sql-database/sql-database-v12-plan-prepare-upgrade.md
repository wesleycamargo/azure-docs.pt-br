<properties
	pageTitle="Planejar sua atualização para o Banco de dados SQL V12 | Microsoft Azure"
	description="Descreve as preparações e limitações envolvidas na atualização para a versão V12 do Banco de Dados SQL do Azure."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="genemi"/>


# Planejar e preparar a atualização para o Banco de Dados SQL V12


Este tópico descreve o planejamento e a preparação necessária para atualizar seus bancos de dados SQL do Azure da versão V11 para a V12.


Um novo [portal do Azure](https://portal.azure.com/) está disponível para oferecer suporte à atualização para V12.


A tabela a seguir lista outros tópicos de Ajuda da V12.


| Título e link | Descrição do conteúdo |
| :--- | :--- |
| [Novidades no Banco de Dados SQL V12](sql-database-v12-whats-new.md) | Descreve os detalhes de como o V12 aproxima o Banco de Dados SQL do Azure à paridade total com o Microsoft SQL Server. |
| [Criar um banco de dados no Banco de Dados SQL V12](sql-database-get-started.md) | Descreve como você pode criar um novo banco de dados SQL do Azure na versão V12. Ela descreve várias opções além de apenas um banco de dados vazio. |


## Planejar com antecedência


As subseções a seguir descrevem o aprendizado e a tomada de decisão que devem ser solucionados antes que você execute uma ação para atualizar seu banco de dados SQL do Azure para V12.

### Esclarecimento da versão


Este documento aborda a atualização do Banco de dados SQL do Microsoft Azure da versão V11 para V12. Mais formalmente, os números de versão estão próximos aos dois valores a seguir, conforme relatado pela instrução Transact-SQL **SELECT @@version;** :


- 12\.0.2000.8 *(ou um pouco maior, V12)*
- 11\.0.9228.18 *(V11)*
 - O V11 também era conhecido como SAWA v2.

### Planejamento da camada de serviço


Começando com V12, o Banco de Dados SQL do Azure oferecerá suporte apenas as camadas de serviço denominadas Basic, Standard e Premium. Não há suporte para a camada de serviço Web e Business no V12. Portanto, se você planeja atualizar seu banco de dados SQL do Azure que está atualmente na camada de serviço Web ou Business, você deve decidir qual deve ser sua nova camada de serviço.


Para obter informações detalhadas sobre as camadas de serviço Basic, Standard e Premium, consulte:

- [Camadas de serviço do Banco de Dados SQL](sql-database-service-tiers.md)
- [Atualizar os bancos de dados Web/Business do Banco de Dados SQL para novas camadas de serviço](sql-database-upgrade-server-portal.md)



### Revisar a configuração da replicação geográfica


Se o seu banco de dados SQL do Azure está configurado para replicação geográfica, você deve documentar sua configuração atual e parar a replicação geográfica, antes de iniciar as ações de preparação para atualização. Concluída a atualização, você deve reconfigurar seu banco de dados para replicação geográfica.


A estratégia é deixar a fonte intacta e testar em uma cópia do banco de dados.


## Ações de preparação


Depois de concluir o planejamento, você pode executar as etapas descritas nas subseções a seguir para se preparar para a fase final de atualização.


Descrições detalhadas sobre a fase final de atualização estão disponíveis nos tópicos da Ajuda que estão vinculados na parte superior deste tópico de Ajuda.


### Ações da camada de serviço


A faixa de preços do serviço Web e Business não é suportada no V12.


Se o seu banco de dados SQL do Azure V11 for um banco de dados Web ou Business, o processo de atualização oferece alternar o banco de dados para uma camada compatível. A atualização recomenda uma camada que se ajuste ao histórico de carga de trabalho do banco de dados. No entanto, você pode escolher qualquer camada compatível desejada.


Você pode reduzir as etapas necessárias durante a atualização, alternando o banco de dados V11 da camada Web e Business antes de iniciar a atualização. Você pode fazer isso usando o novo [Portal do Azure](https://portal.azure.com/).


Se não tiver certeza de para qual camada de serviço alterar, o nível S2 da camada Standard pode ser uma opção inicial adequada. Qualquer camada inferior terá menos recursos do que tinha a camada Web e Business.


### Suspender a replicação geográfica durante a atualização


A atualização para a V12 não pode ser executada se a replicação geográfica estiver ativa no seu banco de dados. Primeiro, você deve reconfigurar o banco de dados para parar de usar a replicação geográfica.


Após a conclusão da atualização, você pode configurar seu banco de dados para usar a replicação geográfica novamente.


### Cliente em uma VM do Azure


Se o seu programa cliente se conecta ao Banco de Dados SQL V12 enquanto seu cliente é executado em uma máquina virtual do Azure (VM), abra os seguintes intervalos de porta na VM:

- 11000-11999
- 14000-14999


Clique [aqui](sql-database-develop-direct-route-ports-adonet-v12.md) para obter detalhes sobre as portas para o Banco de Dados SQL V12. As portas são exigidas por aprimoramentos de desempenho no Banco de Dados SQL V12.


##<a id="limitations"></a>Limitações durante e após a atualização para a V12


### Portais da V12


Há três portais do Azure, e cada um deles tem diferentes capacidades relacionadas ao Banco de Dados SQL V12.


- [http://portal.azure.com/](https://portal.azure.com/)<br/>Esse portal do Azure é novo e ainda está no status de visualização. Esse portal ainda não está totalmente liberado para disponibilidade geral. Esse portal:
 - Pode gerenciar seu servidor e banco de dados da V12.
 - Pode atualizar seu banco de dados da V11 para V12.


- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>Esse portal Clássico do Azure, eventualmente, pode ser desativado. Esse portal:
 - Pode gerenciar seu servidor e banco de dados da V12.
 - *Não* pode atualizar o banco de dados da V11 para V12.


- (http://*yourservername*.database.windows.net)<br/> Portal clássico do banco de dados SQL do Azure:
 - *Não* pode gerenciar servidores V12.


Incentivamos você a se conectar aos bancos de dados SQL do Azure com o Visual Studio 2013 (VS2013). O VS2013 pode ser usado para tarefas como as seguintes:


- Para executar uma instrução Transact-SQL.
- Para criar um esquema.
- Para desenvolver um banco de dados online ou offline.


Você pode se conectar ao [Visual Studio Community 2013](https://www.visualstudio.com/pt-BR/news/vs2013-community-vs.aspx/), que é uma versão gratuita e completa em termos de recursos do VS2013.


No antigo portal clássico do Azure, na página de banco de dados, você pode clicar em **Abrir no Visual Studio** para iniciar o VS2013 no seu computador para conexão com o Banco de Dados SQL do Azure.


Como outra alternativa, você pode usar o SQL Server Management Studio (SSMS) 2014 com [CU6](http://support.microsoft.com/kb/3031047/) para se conectar ao Banco de Dados SQL do Azure. Há mais detalhes nesta postagem de blog:<br/>[Atualizações das ferramentas do cliente para Banco de Dados SQL do Azure](https://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


### Limitação *durante* a atualização para V12


O banco de dados anterior à V11 continua disponível para acesso aos dados durante a atualização para a V12. Ainda assim, há certas limitações a serem consideradas.


| Limitações | Descrição |
| :--- | :--- |
| Duração da atualização | A duração da atualização depende do tamanho, da edição e do número de bancos de dados no servidor. O processo de atualização de servidores pode levar horas ou dias, especialmente para servidores que tenham bancos de dados:<br/><br/>* Com mais de 50 GB ou <br/>* Em uma camada de serviço que não seja Premium<br/><br/>A criação de novos bancos de dados no servidor durante a atualização também pode aumentar o tempo da atualização. |
| Sem replicação geográfica | Não há suporte para replicação geográfica em um servidor V12 atualmente envolvido em uma atualização a partir da V11. |
| O banco de dados está temporariamente indisponível na fase final da atualização para o V12 | Os bancos de dados que pertencem ao servidor V11 permanecem disponíveis durante o processo de atualização. No entanto, a conexão com o servidor e os bancos de dados está temporariamente indisponível na fase final, durante a transição do V11 para o V12 pronto.<br/><br/>O período de transição pode variar de 40 segundos a 5 minutos. Para a maioria dos servidores, a transição deve ser concluída em até 90 segundos. O tempo de transição aumenta para servidores que têm um grande número de bancos de dados, ou quando os bancos de dados têm cargas de trabalho pesadas de gravação. |


### Limitação *após* a atualização para V12


| Limitações | Descrição |
| :--- | :--- |
| Não é possível reverter para V11 | Após um atualização no local, o resultado não pode ser revertido ou desfeito. |
| Camada da Web ou Business | Depois que a atualização começa, o servidor do banco de dados novo V12 não pode mais reconhecer ou aceitar a camada de serviço Web ou Business. |



### Exportar e importar *após* a atualização para V12


Você pode exportar ou importar um banco de dados V12 usando o [portal do Azure](https://portal.azure.com/). Ou você pode exportar ou importar usando qualquer uma das seguintes ferramentas:


- SQL Server Management Studio (SSMS)
- Visual Studio 2013
- Estrutura de Aplicativo da Camada de Dados (DacFX)


No entanto, para usar as ferramentas, primeiro instale as atualizações mais recentes para garantir que elas oferecem suporte aos novos recursos V12:


- [Atualização cumulativa 6 do SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Atualização de fevereiro de 2015 para ferramentas do banco de dados SQL Server no Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [DacFx (Microsoft SQL Server Data-Tier Application Framework) de fevereiro de 2015 para Banco de Dados SQL V12 do Azure](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] Os links a ferramenta anteriores foram atualizados em ou após 2 de março de 2015. É recomendável que você use essas atualizações mais recentes dessas ferramentas.


#### Exportação Automatizada


A Exportação Automatizada continua disponível como visualização. Não há necessidade de atualizar a ferramenta de cliente ao usar a Exportação Automatizada. Se você optar por executar o arquivo resultante e importar para um servidor local, as atualizações de ferramentas listadas acima serão necessárias para que a importação seja bem-sucedida.


### Restaurar V12 de um banco de dados V11 excluído

O cenário a seguir explica que um banco de dados SQL do Azure V11 excluído pode ser restaurado em um servidor de banco de dados SQL do V12.

1. Suponha que você tenha um servidor de banco de dados SQL do Azure V11. <br/> No servidor que você tem um banco de dados na camada de serviço Web e Business obsoleto.
2. Exclua o banco de dados.
3. Atualize o servidor para V12.
4. Em seguida, você restaura o banco de dados para o servidor. <br/> O banco de dados é atualizado para V12, no nível S0 da camada de serviço Standard.
5. Você pode alternar o banco de dados para qualquer camada de serviço compatível, se S0 não for da sua preferência.


### Cmdlets do PowerShell


Os cmdlets do PowerShell estão disponíveis para iniciar, parar ou monitorar uma atualização para o Banco de Dados SQL do Azure da V11 para a V12 ou de qualquer outra versão anterior à V12.

- [Atualização para o Banco de Dados SQL V12 com o PowerShell](sql-database-upgrade-server-powershell.md)

Para ver a documentação de referência sobre esses cmdlets do PowerShell, confira:


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


O cmdlet Stop- significa cancelar, e não pausar. Não há como retomar uma atualização, a não ser começá-la novamente. O cmdlet Stop- limpa e libera todos os recursos apropriados.


## Resolução de falhas


Se a atualização falhar por algum motivo estranho, seu banco de dados V11 permanece ativo e disponível como normal.


## Links relacionados


- [Recursos de visualização](https://azure.microsoft.com/services/preview/) do Microsoft Azure.


<!--Anchors-->
[Subheading 1]: #subheading-1

<!---HONumber=AcomDC_0413_2016-->