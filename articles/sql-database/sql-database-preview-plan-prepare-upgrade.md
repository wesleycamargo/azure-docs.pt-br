<properties 
	pageTitle="Planejar e preparar a atualização para o Banco de Dados SQL V12" 
	description="Descreve as preparações e as limitações envolvidas na atualização para o Banco de Dados SQL do Azure V12." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="genemi"/>


# Planejar e preparar a atualização para V12 de banco de dados do SQL Azure


<!--
GeneMi , 05-março-2015, quinta-feira 20:06pm
Remover 'Azure' do título.
-->


Este tópico descreve o planejamento e a preparação necessária para atualizar seus bancos de dados SQL do Azure da versão V11 para a V12.


Um novo [portal do Azure](http://portal.azure.com/) está disponível para oferecer suporte à atualização para V12.


> [AZURE.NOTE]
> Novos bancos de dados, cópias de banco de dados ou bancos de dados de teste são bons candidatos para a atualização para a visualização. Os bancos de dados de produção dos quais sua empresa depende devem esperar até após o período de visualização.<br/><br/>
> Para a família da versão V12, você pode determinar se a região geográfica está em status de visualização ou em status de DG, examinando a tabela de regiões em [O que há de novo no Banco de Dados SQL V12](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/#V12AzureSqlDbPreviewGaTable).


A tabela a seguir lista e descreve os outros tópicos de Ajuda para V12.


| Título e link | Descrição do conteúdo |
| :--- | :--- |
| [O que há de novo no Banco de Dados SQL V12](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/) | Descreve os detalhes de como o V12 aproxima o Banco de Dados SQL do Azure à paridade total com o Microsoft SQL Server 2014. |
| [Passo a passo: inscrever-se para a mais recente atualização do Banco de Dados SQL V12 (visualização)](http://azure.microsoft.com/documentation/articles/sql-database-preview-sign-up/) | Descreve as etapas que você deve executar se você quiser atualizar seus bancos de dados SQL do Azure para a mais recente visualização. |
| [Criar um banco de dados na atualização do banco de dados SQL V12 (visualização) mais recente](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) | Descreve como você pode criar um novo banco de dados SQL do Azure que inclui os novos recursos da visualização mais recente. Ela descreve várias opções além de apenas um banco de dados vazio. |
| [Atualização para a atualização mais recente do banco de dados SQL V12 (visualização)](http://azure.microsoft.com/documentation/articles/sql-database-preview-upgrade/) | Descreve como você pode atualizar os bancos de dados SQL do Azure para a visualização mais recente. Ele menciona que você deveria fazer um backup do banco de dados em primeiro lugar, porque a atualização é permanente e não pode ser desfeita. |


## A. Planeje com antecedência


As subseções a seguir descrevem o aprendizado e a tomada de decisão que devem ser solucionados antes que você execute uma ação para atualizar seu banco de dados SQL do Azure para V12.

### A.1 Esclarecimento da versão


Este documento aborda a atualização do Banco de dados SQL do Microsoft Azure da versão V11 para V12. Mais formalmente os números de versão estão próximos aos dois valores a seguir, conforme relatado pela instrução Transact-SQL **SELECT @@version;** :


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(ou um pouco maior, V12)*


### A.2 Planejamento da camada de serviço


Começando com V12, o Banco de Dados SQL do Azure oferecerá suporte apenas as camadas de serviço denominadas Basic, Standard e Premium. Não há suporte para a camada de serviço Web e Business no V12. Portanto, se você planeja atualizar seu banco de dados SQL do Azure que está atualmente na camada de serviço Web ou Business, você deve decidir qual deve ser sua nova camada de serviço.


Para obter informações detalhadas sobre as camadas de serviço Basic, Standard e Premium, consulte:


- [Atualizar bancos de dados SQL Web/Business para novas camadas de serviço](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/)
- [Preços do Banco de dados SQL do Azure](http://azure.microsoft.com/pricing/details/sql-database/)


### A.3 Revisão da configuração de replicação geográfica


Se o seu banco de dados SQL do Azure está configurado para replicação geográfica, você deve documentar sua configuração atual e parar a replicação geográfica, antes de iniciar as ações de preparação para atualização. Concluída a atualização, você deve reconfigurar seu banco de dados para replicação geográfica.


A estratégia é deixar a fonte intacta e testar em uma cópia do banco de dados.


### A.4 Atualização no local x Cópia do banco de dados para o novo servidor


Você tem duas técnicas para escolher para atualizar seu banco de dados SQL do Azure do V11 para V12:


- Atualização no local: Quando esse processo for concluído, o banco de dados V11 não existirá mais. O servidor de banco de dados SQL estará na V12.
- Copiar o banco de dados para o servidor V12: Esse processo deixa o banco de dados V11 inalterado e cria uma cópia atualizada do banco de dados V11 em um servidor de banco de dados SQL do Azure V12. <br/> Observe que as informações de DNS que são usadas pelo seu código de cliente para acessar o banco de dados não terão mais acesso à versão V11 e edições para as informações de DNS serão necessárias para acessar o banco de dados V11.


## B. Ações de preparação


Depois de concluir o planejamento, você pode executar as etapas descritas nas subseções a seguir para se preparar para a fase final de atualização.


Descrições detalhadas sobre a fase final de atualização estão disponíveis nos tópicos da Ajuda que estão vinculados na parte superior deste tópico de Ajuda.


### B.1 Ações de Camada de serviço


A faixa de preços do serviço Web e Business não é suportada no V12.


Se o seu banco de dados SQL do Azure V11 for um banco de dados Web ou Business, o processo de atualização oferece alternar o banco de dados para uma camada compatível. A atualização recomenda uma camada que se ajuste ao histórico de carga de trabalho do banco de dados. No entanto, você pode escolher qualquer camada compatível desejada.


Você pode reduzir as etapas necessárias durante a atualização, alternando o banco de dados V11 da camada Web e Business antes de iniciar a atualização. Você pode fazer isso usando o Portal do Azure.


Se você não souber para qual camada de serviço alternar, o nível S0 da camada Standard pode ser uma opção inicial adequada.


### B.2 Suspender a replicação geográfica durante a atualização


A atualização para a V12 não pode ser executada se a replicação geográfica estiver ativa no seu banco de dados. Primeiro, você deve reconfigurar o banco de dados para parar de usar a replicação geográfica.


Após a conclusão da atualização, você pode configurar seu banco de dados para usar a replicação geográfica novamente.


##<a id="limitations"></a>C. limitações durante e após a atualização para V12


Esta seção descreve as limitações que estão associadas com a atualização V12 de banco de dados do SQL Azure.


### C.1 Portal de visualização para V12


Somente o primeiro dos dois portais de gerenciamento do Azure a seguir oferece suporte ao bancos de dados V12:


- [http://portal.azure.com/](http://portal.azure.com/)
 - Esse portal mais recente é o status de visualização e ainda não está em disponibilidade geral (DG).<br/><br/>
- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)
 - Esse portal mais antigo não será atualizado para oferecer suporte a V12.


Encorajamos os clientes a conectarem a seus bancos de dados SQL do Azure com o Visual Studio 2013 (VS2013). O VS2013 pode ser usado para tarefas como as seguintes:


- Para executar uma instrução T-SQL.
- Para criar um esquema.
- Para desenvolver um banco de dados online ou offline.


Em vez disso, você pode se conectar ao [Visual Studio Community 2013](https://www.visualstudio.com/pt-br/news/vs2013-community-vs.aspx/), que é uma versão gratuita e completa do VS2013.


No portal de gerenciamento do Azure mais antigo, na página de banco de dados, você pode clicar em **Aberto no Visual Studio** para iniciar o VS2013 no seu computador para conexão com o Banco de Dados SQL do Azure.


Para outra alternativa, você pode usar o SQL Server Management Studio (SSMS) 2014 com [CU6](http://support.microsoft.com/kb/3031047/) para se conectar ao Banco de Dados SQL do Azure. Mais detalhes estão nesta postagem do blog:<br/>[Cliente de atualizações de ferramentas para o Banco de Dados SQL do Azure](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


### C.2 Limitação *during* atualização para V12

A tabela a seguir descreve as limitações que estão em vigor durante o processo de atualização.


| Limitações | Descrição |
| :--- | :--- |
| Duração da atualização | Para um banco de dados muito grande de talvez mais de 50 GB de tamanho, o processo de atualização pode levar até 24 horas. |
| Atraso da atualização de entrada DNS | Após a conclusão da atualização, são necessários vários minutos para o sistema atualizar a entrada DNS para seu banco de dados V12, para conectividade de seu aplicativo cliente. |
| Sem a criação de um banco de dados | Enquanto a atualização estiver em andamento as seguintes ações para a criação de um banco de dados não estarão disponíveis no servidor do Banco de Dados SQL do Azure V12 de destino: <p></p> *Criando um novo banco de dados <br/> * Copiando um banco de dados para o servidor <br/> *Restaurando um banco de dados excluído <br/> * Restaurando um banco de dados para um momento determinado <br/><br/> No entanto, o suporte para restauração para um momento determinado durante a atualização é um recurso que pode ser aceito antes do término do período de visualização V12. |
| Sem replicação geográfica | Não há suporte para replicação geográfica em um servidor V12 atualmente envolvido em uma atualização a partir da V11. |
| Regras de alerta | Regras de alerta não podem ser adicionadas ao banco de dados V12. |


### C.3 Limitação *after* atualização para V12

A tabela a seguir descreve as limitações que estão em vigor após o processo de atualização.


| Limitações | Descrição |
| :--- | :--- |
| DNS para o banco de dados V11 | Após a conclusão de uma atualização através da técnica de copiar seu banco de dados V11 para um novo servidor V12, as informações de DNS que o código do cliente usa para conectar-se, referem-se ao banco de dados V12. <p> </p> O código do cliente precisaria usar informações de DNS editadas para consultar o banco de dados V11. |
| Não é possível reverter para V11 | Após um atualização no local, o resultado não pode ser revertido ou desfeito. |
| Camada da Web ou Business | Depois que a atualização começa, o servidor do banco de dados novo V12 não pode mais reconhecer ou aceitar a camada de serviço Web ou Business. |
| Um desconto de 50% não será refletido nos cartões de camada de preços no portal do Azure | Durante o período de visualização, há uma visualização de 50% de desconto * em bancos de dados inscritos na atualização mais recente de visualização de banco de dados SQL do Azure (V12). Mesmo que o desconto não seja exibido no portal de visualização na lâmina de preços das camadas de serviço, o desconto está em vigor.<br/><br/> O desconto de 50% permanece em vigor em todas as regiões geográficas até **31 de março de 2015**, quando ele expira para todas as regiões. O desconto está em vigor mesmo em regiões que foram apresentadas no status de disponibilidade geral (DG).<br/><br/> (* O uso de recursos da atualização V12 do banco de dados SQL do Azure mais recente está sujeito aos termos de visualização no contrato de licença (por exemplo, o contrato corporativo, contrato do Microsoft Azure ou contrato de assinatura on-line da Microsoft), conforme aplicável bem como quaisquer [termos complementares do uso para visualizações do Microsoft Azure](http://azure.microsoft.com/support/legal/preview-supplemental-terms/).  Durante a visualização, a Microsoft irá cobrá-lo (ou seu revendedor, conforme aplicável) por todos os bancos de dados registrados nesta visualização na metade da taxa de disponibilidade geral (DG) para obter um desconto de 50% na visualização. A Microsoft fornecerá um aviso por email 30 dias antes da expiração do período de visualização e da taxa de visualização com desconto.) |


### C.4 Importação e exportação  *after* atualização para V12


Você pode exportar ou importar um banco de dados V12 usando o [portal da web do Azure](http://portal.azure.com/). Ou você pode exportar ou importar usando qualquer uma das seguintes ferramentas:


- SQL Server Management Studio (SSMS)
- Visual Studio 2013
- Estrutura de Aplicativo da Camada de Dados (DacFX)


No entanto, para usar as ferramentas, primeiro instale as atualizações mais recentes para garantir que elas oferecem suporte aos novos recursos V12:


- [Atualização cumulativa 6 do SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Atualização de fevereiro de 2015 da ferramenta de banco de dados do SQL Server no Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Estrutura de Aplicativo da Camada de Dados (DacFx) de fevereiro de 2015 para o Banco de Dados SQL do Azure V12](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] Os links a ferramenta anteriores foram atualizados em ou após 2 de março de 2015. É recomendável que você use essas atualizações mais recentes dessas ferramentas.


#### C.4.1 A importação tem um problema temporário com arquivos bacpac


 (A partir de quarta-feira, 18 de fevereiro de 2015)


Há um problema conhecido com a exportação de arquivos bacpac a partir de um servidor de banco de dados SQL do Azure que tenha sido atualizado para V12. Os arquivos bacpac exportados conterão um objeto incorreto chamado script_deployment_databases. Os arquivos Bacpac que contém esse objeto incorreto não podem ser importados usando as ferramentas do SQL Server Management Studio (SSMS), SqlPackage.exe ou a API da Estrutura de aplicativo da camada de dados (DacFx).


No entanto, o portal da web do Azure pode ser usado para importar um arquivo bacpac afetado para o banco de dados do SQL Azure. Esperamos liberar uma correção permanente para esse problema em torno de sexta-feira, 27 de fevereiro de 2015, incluindo uma atualização para as ferramentas afetadas. Enquanto isso, entre em contato o Suporte da Microsoft se precisar de mais ajuda para recuperar um arquivo já exportado que tenha .bacpac como sua extensão de nome de arquivo.


Além do problema de exportação, um número limitado de servidores e clientes que recentemente atualizaram para a versão V12 podem apresentar um erro diferente ao tentar importar um arquivo bacpac. Esse erro relacionado a permissões é transitório e normalmente é resolvido em um servidor afetado dentro de um dia. Esperamos que esse problema também seja corrigido permanentemente durante a semana de segunda-feira, 23 de fevereiro de 2015. Nesse ínterim, repetir a operação de importação, talvez seja bem-sucedida. Entre em contato com o Suporte da Microsoft se precisar de assistência adicional na importação de um arquivo bacpac para o banco de dados do SQL Azure.


Se necessário, você pode seguir estas etapas para entrar em contato com o Suporte da Microsoft:


1. Navegue até o portal do Azure.
2. Clique com o botão direito no nome da conta, localizado no canto superior direito.
3. No menu de contexto exibido, clique no item de suporte.
 - O item provavelmente está denominado como**Entre em contato com o Suporte da Microsoft** ou **Ajuda + suporte**.


> [AZURE.NOTE] (Segunda-feira, 2 de março de 2015)
> 
> O problema descrito nesta seção C.4.1 foi resolvido. Nenhuma ação ou conhecimento especial é necessário para os clientes prosseguirem como desejarem.
> 
> Importação/exportação automatizada normalmente funciona independentemente de quanto tempo tem o seu arquivo bacpac.
> 
> Os clientes que processam seus arquivos bacpac usando DacFx devem baixar as ferramentas atualizadas. Os links de download estão disponíveis na seção 4.


### C.5 Restaurar V12 de um banco de dados V11 excluído

O cenário a seguir explica que um banco de dados SQL do Azure V11 excluído pode ser restaurado em um servidor de banco de dados SQL do V12.

1. Suponha que você tenha um servidor de banco de dados SQL do Azure V11. <br/> No servidor, você tem um banco de dados obsoleto na camada de serviço Web e Business.
2. Exclua o banco de dados.
3. Atualize o servidor para V12.
4. Em seguida, você restaura o banco de dados para o servidor. <br/> O banco de dados, portanto, é atualizado para a V12, no nível S0 da camada de serviço Standard.
5. Você pode alternar o banco de dados para qualquer camada de serviço compatível, se S0 não for da sua preferência.



## D. Resolução de falhas

Se a atualização falhar por algum motivo estranho, seu banco de dados V11 permanece ativo e disponível como normal.


## Links relacionados

- Recursos de visualização do [Microsoft Azure](http://azure.microsoft.com/services/preview/)


<!--Anchors-->
[Subtítulo 1]: #subheading-1
<!--HONumber=47-->
 