<properties title="Plan and Prepare to Upgrade to the Latest SQL Database Update V12 (preview)" pageTitle="Planejar e preparar a atualização para a atualização do Banco de dados SQL V12 mais recente (visualização)" description="Descreve o planejamento, as preparações e limitações que envolvem uma atualização para a atualização mais recente do banco de dados do SQL do Azure (visualização)." metaKeywords="Azure, SQL DB, Update, Preview, Plan" services="sql-database" documentationCenter="" authors="GeneMi" manager="jhubbard, jeffreyg" videoId="" scriptId=""/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="genemi"/>

<!-- Unmentioned in JH emailed example of properties, so removing.
solutions="upgrade, migrate, move, monitor, t-sql, powershell"
-->

<!--
Latest Edit Datetime:  GM,  2014-12-10  Wednesday  14:28pm.
sql-database-preview-plan-prepare-upgrade.md
sql-database- , -preview- :  Set of topics using -preview- as their group identifier node.
-->

# Planejar e preparar a atualização para a atualização do Banco de dados SQL V12 mais recente (visualização)

Este tópico descreve o planejamento e a preparação necessária para atualizar seus bancos de dados SQL do Azure da versão V11 para a visualização V12.

Um novo [portal Azure](http://portal.azure.com/) está disponível para oferecer suporte para a atualização para a visualização mais recente.

A tabela a seguir lista e descreve os outros tópicos da Ajuda para essa visualização mais recente.

| Título e link | Descrição do conteúdo |
| :--- | :--- |
| [o que há de novo na visualização de banco de dados SQL do Azure V12](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/) | Descreve os detalhes de como a visualização V12 traz o SQL banco de dados SQL do Azure mais próximo à paridade completa com o Microsoft SQL Server 2014. |
| [Passo a passo: Inscreva-se para a visualização do banco de dados SQL do Azure V12](http://azure.microsoft.com/documentation/articles/sql-database-preview-sign-up/) | Descreve as etapas que você deve executar se você quiser atualizar seus bancos de dados SQL do Azure para a mais recente visualização. |
| [Criar um banco de dados na visualização de banco de dados SQL do Azure V12](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) | Descreve como você pode criar um novo banco de dados SQL do Azure que inclui os novos recursos da visualização mais recente. Ela descreve várias opções além de apenas um banco de dados vazio. |
| [Atualizar a visualização de banco de dados SQL do Azure V12](http://azure.microsoft.com/documentation/articles/sql-database-preview-upgrade/) | Descreve como você pode atualizar os bancos de dados do SQL Azure para a visualização mais recente. Ela menciona que você deveria fazer um backup do banco de dados em primeiro lugar, porque a atualização é permanente e não pode ser desfeita. |




## A. Planeje com antecedência

As subseções a seguir descrevem o aprendizado e a tomada de decisão que devem ser solucionados antes que você execute uma ação para atualizar seu banco de dados do SQL Azure para a mais recente visualização.

### A.1 Esclarecimento da versão

Este documento aborda a atualização do Banco de dados SQL do Microsoft Azure da versão V11 para V12. Mais formalmente os números de versão estão próximos aos dois valores a seguir, conforme relatado pela instrução T-SQL **SELECT @@version;** :

- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(ou um pouco maior, V12) *


### A.2 Planejamento da camada de serviço

Começando com essa visualização mais recente, o banco de dados SQL do Azure oferecerá suporte somente as camadas de serviço denominadas Basic, Standard e Premium. A camada de serviços Web e Business não é suportada nesta versão de visualização. Portanto, se você planeja atualizar seu banco de dados SQL do Azure que está atualmente na camada de serviço Web ou Business, você deve decidir qual deve ser sua nova camada de serviço.

Para obter informações detalhadas sobre as camadas de serviço Basic, Standard e Premium, consulte:

- [Atualizar bancos de dados SQL Web/Business para novas camadas de serviço](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/)
- [Preços do Banco de dados SQL do Azure](http://azure.microsoft.com/pricing/details/sql-database/)


### A.3 Revisão da configuração de replicação geográfica

Se o seu banco de dados SQL do Azure está configurado para replicação geográfica, você deve documentar sua configuração atual e parar a replicação geográfica, antes de iniciar as ações de preparação para atualização. Concluída a atualização, você deve reconfigurar seu banco de dados para replicação geográfica.

A estratégia é deixar a fonte intacta e testar em uma cópia do banco de dados.

### A.4 Atualização no local x Cópia do banco de dados para o novo servidor

Antes do período de visualização V12 ser concluído, você terá duas técnicas para escolher como atualizar seu banco de dados do SQL Azure do V11 para V12:

- Atualização no local: Quando esse processo for concluído, o banco de dados V11 não existirá mais. O servidor de banco de dados SQL estará na V12.
- Copiar o banco de dados para o servidor V12: Esse processo deixa o banco de dados V11 inalterado e cria uma cópia atualizada do banco de dados V11 em um servidor de banco de dados SQL do Azure V12. <br/> Observe que as informações de DNS que são usadas pelo seu código de cliente para acessar o banco de dados não terão mais acesso à versão V11 e edições para as informações de DNS serão necessárias para acessar o banco de dados V11.



## B. Ações de preparação

Depois de concluir o planejamento, você pode executar as etapas descritas nas subseções a seguir para se preparar para a fase final de atualização.

Descrições detalhadas sobre a fase final de atualização estão disponíveis nos tópicos da Ajuda que estão vinculados na parte superior deste tópico de Ajuda.

### B.1 Ações de Camada de serviço


A camada de preços dos serviços Web e Business não é suportada nesta versão de visualização.

Se o seu banco de dados SQL do Azure V11 for um banco de dados Web ou Business, o processo de atualização oferece alternar o banco de dados para uma camada compatível. A atualização recomenda uma camada que se ajuste ao histórico de carga de trabalho do banco de dados. No entanto, você pode escolher qualquer camada compatível desejada.

Você pode reduzir as etapas necessárias durante a atualização, alternando o banco de dados V11 da camada Web e Business antes de iniciar a atualização. Você pode fazer isso usando o Portal do Azure.

Se você não souber para qual camada de serviço alternar, o nível S0 da camada Standard pode ser uma opção inicial adequada.


### B.2 Suspender a replicação geográfica durante a atualização

A atualização para a visualização mais recente não pode ser executada se a replicação geográfica estiver ativa no seu banco de dados. Primeiro, você deve reconfigurar o banco de dados para parar de usar a replicação geográfica.

Após a conclusão da atualização, você pode configurar seu banco de dados para usar a replicação geográfica novamente.


## C. Limitações durante e após a atualização

Esteja ciente das seguintes limitações da visualização mais recente:


| Limitação | Descrição |
| :--- | :--- |
| Duração da atualização | Para um banco de dados muito grande, eventualmente maior que 50 GB de tamanho, o processo de atualização pode levar até 24 horas. |
| Atraso da atualização de entrada DNS | Após a conclusão da atualização, são necessários vários minutos para o sistema atualizar a entrada DNS para seu banco de dados V12, para conectividade de seu aplicativo cliente. |
| DNS para o banco de dados V11 | Após a conclusão de uma atualização através da técnica de copiar seu banco de dados V11 para um novo servidor V12, as informações de DNS que o código do cliente usa para conectar-se, referem-se ao banco de dados V12. <p> </p> O código do cliente precisaria usar informações de DNS editadas para consultar o banco de dados V11. |
| Não é possível reverter para V11 | Após uma atualização no local, o resultado não poderá ser revertido ou desfeito. |
| Camada da Web ou Business | Depois que a atualização iniciar, o servidor para o novo banco de dados V12 pode não mais reconhecer ou aceitar a camada de serviço Web ou Business. |
| Sem a criação de um banco de dados | Enquanto a atualização estiver em andamento as seguintes ações para a criação de um banco de dados não estarão disponíveis no servidor de banco de dados SQL do Azure V12 de destino: <p></p> * Criação de um novo banco de dados <br/> * Cópia de um banco de dados para o servidor <br/> * Restauração de um banco de dados excluído <br/> * Restauração de um banco de dados para um momento determinado <br/><br/> No entanto, o suporte para restauração para um momento determinado durante a atualização é um recurso que pode ser aceito antes do término do período de visualização V12. |
| Não há replicação geográfica | Não há suporte para replicação geográfica em um servidor V12 atualmente envolvido em uma atualização a partir do V11. |
| 50% de desconto semioculto | Durante o período de visualização, há um desconto de 50% nos bancos de dados com a atualização mais recente de visualização de banco de dados SQL do Azure (V12). Mesmo que o desconto não seja exibido no portal de visualização na lâmina de preços das camadas de serviço, o desconto está em vigor. |


### Restaurar V12 de um banco de dados V11 excluído

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

<!--HONumber=35.2-->
