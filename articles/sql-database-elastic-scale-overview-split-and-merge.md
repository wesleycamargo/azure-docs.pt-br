<properties 
title="Splitting and Merging with Elastic Scale" 
pageTitle="Divisão e mesclagem com dimensionamento Elástico" 
description="Explica como manipular fragmentos e mover os dados por meio de um serviço auto-hospedado usando APIs de escala elástica." 
metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" 
services="sql-database" documentationCenter="" 
manager="jhubbard" 
authors="torsteng"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="torsteng" />

# Divisão e mesclagem com Escala Elástica

Os aplicativos criados no Banco de Dados SQL do Azure enfrentam desafios quando suas necessidades de dados ou de processamento não se ajustam mais a uma única unidade de escala no Banco de Dados SQL do Azure. Os exemplos incluem aplicativos que viralizam ou onde um conjunto específico de locatários cresce além dos limites de um único banco de dados do Banco de Dados SQL do Azure. O **Serviço de divisão/mesclagem** de escala elástica ameniza consideravelmente esse problema. 

Esta discussão sobre o Serviço de divisão/mesclagem gerencia a redução e o escalonamento horizontais alterando o número de bancos de dados do Banco de Dados do Azure e equilibrando a distribuição de **shardlets** entre eles. (Para definições de termos, consulte o [Glossário de escala elástica](sql-database-elastic-scale-glossary.md)). 

Com as atuais opções de edições do Banco de Dados SQL do Azure, a capacidade pode ser gerenciada escalando ou reduzindo horizontalmente a capacidade de um único banco de dados do Banco de Dados SQL do Azure. A dimensão de escalonamento/redução horizontal do gerenciamento da capacidade elástica não é coberta pela divisão/mesclagem (em vez disso, consulte Elasticidade de fragmento [Elasticidade de fragmento de escala elástica]).(sql-database-elastic-scale-elasticity.md)). 
 
## O que há de novo em Divisão/Mesclagem

A versão mais recente de Divisão/Mesclagem oferece os seguintes aprimoramentos:
* Agora há suporte para mapas de fragmento da lista.
* Limites de intervalo em solicitações podem corresponder mais facilmente aos intervalos armazenados no mapa do fragmento.
* Agora há suporte para várias instâncias de função de trabalho para aumentar a disponibilidade.
* As credenciais armazenadas como parte de sua operação de Divisão/Mesclagem agora estão criptografadas em rest.

## Como atualizar

Para atualizar para a versão mais recente de Divisão/Mesclagem, siga estas etapas:

1. Baixe a versão mais recente do pacote NuGet, conforme descrito na seção de Introdução à Divisão/Mesclagem "Baixar os pacotes de Divisão/Mesclagem".
2. Altere o arquivo de configuração do serviço de nuvem para sua implantação de Divisão/Mesclagem refletir os novos parâmetros de configuração. Um novo parâmetro obrigatório são as informações sobre o certificado usado para criptografia. Uma maneira fácil de fazer isso é comparar o novo arquivo de modelo de configuração do download em relação a sua configuração existente. Certifique-se de que adicionar as configurações para "DataEncryptionPrimaryCertificateThumbprint" e "DataEncryptionPrimary" para a função de trabalho e da web.
3. Antes de implantar a atualização para o Azure, certifique-se de que todas as operações de Divisão/Mesclagem atualmente em execução terminaram. Você pode fazer isso facilmente consultando as tabelas RequestStatus e PendingWorkflows no banco de dados de metadados Divisão/Mesclagem para as solicitações em andamento.
4. Atualize sua implantação de serviço de nuvem existente para Divisão/Mesclagem na sua assinatura do Azure com o novo pacote e o seu arquivo de configuração de serviço atualizado.

Você não precisa provisionar um novo banco de dados de metadados para a Divisão/Mesclagem atualizar. A nova versão irá atualizar automaticamente seu banco de dados de metadados existentes para a nova versão. 

## Cenários de divisão/mesclagem 
Aplicativos precisam de flexibilidade para além dos limites de um único banco de dados do Banco de Dados SQL do Azure, como ilustrado nos cenários a seguir: 

* **Aumentar a capacidade - dividindo intervalos**: A habilidade de aumentar a capacidade agregada na camada de dados visa as crescentes necessidades de capacidade. Nesse cenário, o aplicativo fornece a capacidade adicional fragmentando os dados e distribuindo-a incrementalmente em mais bancos de dados até que as necessidades de capacidade sejam atendidas. O recurso 'dividir' do Serviço de divisão/mesclagem de escala elástica visa este cenário. 

* **Reduzir a capacidade - mesclando intervalos**: A capacidade flutua devido à natureza sazonal de um negócio. Este cenário destaca a necessidade de facilmente escalar de volta para menos unidades quando o negócio desacelera. O recurso 'mesclar' no Serviço de divisão/mesclagem de escala elástica aborda esse requisito. 

* **Gerenciar pontos de acesso - movendo shardlets**: Com vários locatários por banco de dados, a alocação de shardlets para fragmentos pode levar a afunilamentos de capacidade em alguns fragmentos. Isso exige a realocação de shardlets ou a movimentação de shardlets ocupados para fragmentos novos ou menos utilizados. 

A seguir, nos referiremos a qualquer processamento no serviço, ao longo desses recursos, como solicitações de **divisão/mesclagem/movimentação**. 


Figura 1: Visão geral conceitual de divisão/mesclagem


![Overview][1] 


**Observação**:  Nem todos os cenários de **Capacidade de crescimento** precisam do serviço de Divisão/Mesclagem. Por exemplo, se você cria periodicamente novos fragmentos no seu ambiente para armazenar novos dados com valores crescentes de chave de fragmentação, você pode usar as APIs de cliente do Gerenciamento de mapa do fragmento para direcionar novos intervalos de dados para fragmentos recém criados. O serviço de Divisão/Mesclagem é necessário somente quando os dados existentes precisam ser movidos também.

## Principais recursos e conceitos

**Serviços hospedados no cliente**: A Divisão/﻿Mesclagem é entregue como um serviço hospedado no cliente. Você deve implantar e hospedar o serviço na sua assinatura do Microsoft Azure. O pacote que você baixou do NuGet contém um modelo de configuração para concluir as informações da sua implantação específica. Consulte o [Tutorial de divisão-mesclagem](sql-database-elastic-scale-configure-deploy-split-and-merge.md) para obter detalhes. Uma vez que o serviço é executado na sua assinatura do Azure, você pode controlar e configurar a maioria dos aspectos de segurança do serviço. O modelo padrão inclui as opções para configurar o SSL, autenticação de cliente baseada no certificado, criptografia para credenciais armazenadas, proteção DoS e restrições de IP. Você pode encontrar mais informações sobre os aspectos de segurança no seguinte documento [Considerações de segurança de escala elástica](sql-database-elastic-scale-configure-security.md).

O serviço padrão implantado é executado com um operador e uma função web. Cada um usa o tamanho da VM A1 nos Serviços de Nuvem do Azure. Embora não seja possível modificar essas configurações durante a implantação do pacote, eles podem ser alterados após uma implantação bem-sucedida no serviço de nuvem em execução (por meio do portal do Azure). Observe que a função de trabalho não deve ser configurada para mais de uma única instância por motivos técnicos. 

**Integração de mapa de fragmentos**: O serviço de Divisão/Mesclagem interage com o mapa do fragmento do aplicativo. Ao usar o serviço de Divisão/Mesclagem para dividir ou mesclar intervalos ou para mover shardlets entre dois fragmentos, o serviço mantém automaticamente o mapa do fragmento atualizado. Para fazer isso, o serviço se conecta ao banco de dados do gerenciador de mapa do fragmento do aplicativo e mantém intervalos e mapeamentos como progresso de solicitações de divisão/mesclagem/movimentação. Isso garante que o mapa do fragmento sempre apresente uma exibição atualizada quando operações de Divisão/Mesclagem estão sendo realizadas. Operações de divisão, mesclagem e de movimentação de shardlet são implementadas movendo um lote de shardlets do fragmento de origem para o fragmento de destino. Durante a operação de movimentação do shardlet, os shardlets sujeitos ao lote atual são marcados como offline no mapa do fragmento e ficam indisponíveis para conexões de roteamento dependentes de dados usando a API **OpenConnectionForKey**. 

**Conexões consistentes de shardlet**: Quando a movimentação de dados inicia para um novo lote de shardlets, quaisquer conexões de roteamento dependentes de dados fornecidas pelo mapa do fragmento, ao fragmento armazenando o shardlet são interrompidas e as conexões subsequentes das APIs de mapa do fragmento a esses shardlets são bloqueadas enquanto o movimento de dados está em andamento para evitar inconsistências. Conexões com outros shardlets no mesmo fragmento também serão interrompidas, mas funcionarão de novo, imediatamente em uma nova tentativa. Depois que o lote é movido, os shardlets são marcados novamente como online para o fragmento de destino e os dados de origem são removidos do fragmento de origem. O serviço passa por essas etapas, a cada lote, até que todos os shardlets sejam movidos. Isso levará a várias operações de interrupção de conexão ao longo da operação de divisão/mesclagem/movimentação.   

**Gerenciando a disponibilidade de shardlets**: A limitação da interrupção da conexão do lote atual de shardlets, conforme discutido acima, restringe o escopo de indisponibilidade a um lote de shardlets por vez. Isso é preferível a uma abordagem em que o fragmento completo permaneceria offline para todos os seus shardlets ao longo da operação de divisão/mesclagem. O tamanho de um lote, definido como o número de shardlets distintos para mover por vez, é um parâmetro de configuração. Ele pode ser definido para cada operação de divisão/mesclagem, dependendo das necessidades de desempenho e de disponibilidade do aplicativo. Observe que o intervalo que está sendo bloqueado no mapa do fragmento pode ser maior que o tamanho de lote especificado. Isso ocorre porque o serviço escolhe o tamanho do intervalo, de modo que o número real de valores de chave de fragmentação nos dados corresponda aproximadamente ao tamanho do lote. É importante lembrar isso, especialmente para chaves de fragmentação escassamente preenchidas. 

**Armazenamento de metadados**: O serviço de Divisão/Mesclagem usa um banco de dados para manter seu status e manter registros durante o processamento da solicitação. O usuário cria este banco de dados na sua assinatura e fornece a cadeia de conexão para ele no arquivo de configuração para a implantação do serviço. Os administradores da organização do usuário também podem se conectar a este banco de dados para examinar o andamento da solicitação e para investigar informações detalhadas sobre falhas potenciais.

**Reconhecimento de fragmentação**: O serviço de Divisão/Mesclagem faz distinção entre (1) tabelas fragmentadas, (2) tabelas de referência e (3) tabelas normais. A semântica de uma operação de divisão/mesclagem/movimentação depende do tipo da tabela usada e é definida da seguinte maneira: 

* **Tabelas fragmentadas**: Operações de divisão/mesclagem/movimentação movem shardlets do fragmento de origem ao de destino. Após a conclusão bem-sucedida da solicitação geral, esses shardlets não estarão mais presentes na origem. Observe que as tabelas de destino precisam existir no fragmento de destino e não podem conter dados no intervalo de destino antes do processamento da operação. 

* **Tabelas de referência**: Para tabelas de referência, as operações de divisão, mesclagem e movimentação copiam os dados do fragmento de origem ao de destino. No entanto, observe que nenhuma alteração ocorre no fragmento de destino, para uma determinada tabela, se já houver alguma linha nesta tabela, no destino. A tabela deve estar vazia para que qualquer operação de cópia de tabela de referência seja processada.

* **Outras tabelas**: Outras tabelas podem estar presentes tanto na origem quanto no destino de uma operação de divisão/mesclagem. A divisão/mesclagem ignora essas tabelas em qualquer operação de cópia ou movimentação de dados. No entanto, observe que elas podem interferir com essas operações no caso de restrições.

As informações sobre referência versus tabelas fragmentadas são fornecidas pelas APIs **SchemaInfo** no mapa de fragmento. O exemplo a seguir ilustra o uso dessas APIs em um objeto de gerenciador de mapa de fragmento smm: 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

As tabelas 'região' e 'país' são definidas como tabelas de referência e serão copiadas com operações de divisão/mesclagem/movimentação. 'cliente' e 'pedidos', por sua vez, são definidos como tabelas fragmentadas. C_CUSTKEY e O_CUSTKEY servem como chave de fragmentação. 

**Integridade referencial**: O serviço de Divisão/Mesclagem analisa as dependências entre as tabelas e usa relações FK-PK (chave estrangeira-chave primária) para preparar as operações para mover tabelas de referência e shardlets. Em geral, as tabelas de referência são copiadas primeiro em ordem de dependência, os shardlets são então copiados na ordem de suas dependências em cada lote. Isso é necessário para que as restrições FK-PK no fragmento de destino sejam respeitadas a medida que os novos dados chegam. 

**Consistência de mapa do fragmento e Eventual conclusão**: Na presença de falhas, o serviço de Divisão/Mesclagem retoma as operações após qualquer interrupção e visa conclui-las nas solicitações de andamento. No entanto, podem haver situações irrecuperáveis (ex.: quando o fragmento de destino é perdido ou comprometido e não pode ser reparado). Sob tais circunstâncias, alguns shardlets que deveriam ser movidos podem continuar a residir no fragmento de origem. O serviço garante que os mapeamentos de shardlet são atualizados somente depois que os dados necessários tiverem sido copiados, com êxito, no destino. Shardlets só são excluídos na origem depois que todos os seus dados tiverem sido copiados para o destino e os mapeamentos correspondentes atualizados com êxito. A operação de exclusão ocorre em segundo plano quando o intervalo já está online no fragmento de destino. O serviço de Divisão/Mesclagem sempre garante a exatidão dos mapeamentos armazenados no mapa do fragmento.

## Obtendo os binários de serviço

Os binários do serviço de Divisão/Mesclagem são fornecidos por meio do [Nuget](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Consulte o passo a passo [Tutorial de divisão-mesclagem](sql-database-elastic-scale-configure-deploy-split-and-merge.md) Para obter mais informações sobre como baixar os binários.

## A Interface do usuário de Divisão/Mesclagem

Além da sua função de trabalho, o pacote de serviço de Divisão/Mesclagem também inclui uma função web que pode ser usada para enviar solicitações de Divisão/Mesclagem de forma interativa. Os componentes principais da interface do usuário são os seguintes:

-    Tipo de operação: O tipo de operação é um botão de opção que controla o tipo de operação executada pelo serviço para esta solicitação. Você pode escolher entre dividir, mesclar e mover os cenários abordados em Conceitos e Recursos Chave. Além disso, você também pode cancelar uma operação enviada anteriormente.  Você pode usar divisão, mesclagem e mover solicitações para mapas de fragmento de intervalo. Os mapas de fragmento da lista apenas oferecem suporte para as operações de movimentação.

-    Mapa de fragmentos: A próxima seção de parâmetros de solicitação abrange as informações sobre o mapa do fragmento e sobre o banco de dados que hospeda seu mapa do fragmento. Em particular, você precisa fornecer o nome do servidor de Banco de Dados SQL do Azure e do banco de dados que hospeda o mapa do fragmento, credenciais para se conectar ao banco de dados de mapa do fragmento e, finalmente, o nome do mapa do fragmento. Atualmente, a operação aceita apenas um único conjunto de credenciais. Essas credenciais precisam ter permissões suficientes para realizar alterações no mapa de fragmento, bem como para os dados do usuário nos fragmentos.

-    Intervalo de origem (﻿Divisão/Mesclagem): Uma operação de divisão e mesclagem processa um intervalo usando sua chave baixa e alta. Para especificar uma operação com um valor alto de chave não associado, marque a caixa de seleção "Chave alta é max" e deixe o campo de chave com valor alto vazio. Os valores de chave do intervalo que você especificar não precisam corresponder exatamente a um mapeamento e seu limite em seu mapa de fragmento. Se você não especificar nenhum limite de intervalo em todos os serviços irá inferir o intervalo mais próximo para você automaticamente. Você pode usar o script do PowerShell GetMappings.ps1 para recuperar os mapeamentos atuais em um determinado mapa de fragmento.

-    Chave de divisão e comportamento (divisão): Para operações de divisão, você também precisa definir em que ponto deseja dividir o intervalo de origem. Para tanto, forneça a chave de fragmentação onde deseja que a divisão ocorra. Em seguida, use o botão de opção para definir se deseja a parte inferior do intervalo (exceto a chave de divisão) ou a superior para mover (incluindo a chave de divisão).

-    Shardlet de origem (mover): Mover operações é diferente de dividir ou mesclar operações, pois elas não precisam de um intervalo para descrever a origem. Uma origem para mover é identificada simplesmente pelo valor de chave de fragmentação que pretende mover.

-    Fragmento de destino (divisão): Uma vez fornecidas as informações sobre a origem da sua operação de divisão, é necessário definir onde quer que os dados sejam copiados fornecendo os nomes do banco de dados e do servidor do Banco de Dados SQL do Azure e o nome do banco de dados do destino.

-    Intervalo de destino (mesclagem): Operações de mesclagem, por outro lado, movem shardlets para um fragmento existente. Você identifica o fragmento existente fornecendo os limites de intervalo do intervalo existente que ao qual deseja mesclar.

-    Tamanho do lote: O tamanho do lote controla o número de shardlets que ficarão offline por vez durante a movimentação de dados. Isso é um valor inteiro onde for possível usar valores menores quando for sensível a períodos longos de inatividade para shardlets. Valores maiores aumentarão o tempo que um determinado shardlet fica offline, mas podem melhorar o desempenho.

-    Id da operação (cancelar): Se tiver uma operação em andamento que não é mais necessária, você pode cancelá-la fornecendo sua ID da operação neste campo. Você pode recuperar a ID da operação da tabela de status de solicitação (consulte a seção 8.1) ou da saída do navegador da Web em que enviou a solicitação.


## Requisitos e limitações 

A implementação atual do serviço de Divisão/Mesclagem está sujeita aos requisitos e limitações a seguir: 

* Atualmente, os fragmentos precisam existir e ser registrados no mapa do fragmento para que uma operação de Divisão/Mesclagem nesses fragmentos possa ser executada. 

* O serviço de Divisão/Mesclagem atualmente não cria tabelas ou quaisquer outros objetos de banco de dados automaticamente como parte de suas operações. Isso significa que o esquema de todas as tabelas fragmentadas e tabelas de referência precisa existir no fragmento de destino antes de qualquer operação de divisão/mesclagem/movimentação. Tabelas fragmentadas, em particular, devem estar vazias no intervalo onde novos shardlets devem ser adicionados por uma operação de divisão/mesclagem/movimentação. Caso contrário, a operação falhará na verificação de consistência inicial no fragmento de destino. Observe também que os dados de referência só são copiados se a tabela de referência estiver vazia e se não houver nenhuma garantia de consistência em relação a outras operações de gravação simultâneas nas tabelas de referência. É recomendável que, no momento da execução das operações de divisão/mesclagem, nenhuma outra operação de gravação esteja fazendo alterações às tabelas de referência.

* O serviço depende atualmente da identidade de linha estabelecida por um índice exclusivo ou chave que inclui a chave de fragmentação para melhorar o desempenho e a confiabilidade de shardlets grandes. Isso permite que o serviço mova dados em uma granularidade ainda maior do que apenas fragmentando o valor chave. Isso ajuda a reduzir a quantidade máxima de espaço de log e de bloqueios necessários durante a operação. Considere a criação de um índice exclusivo ou uma chave primária, incluindo a chave de fragmentação em uma determinada tabela, se quiser usar essa tabela com solicitações de divisão/mesclagem/movimentação. Por motivos de desempenho, a chave de fragmentação deve ser a coluna à esquerda na chave ou índice.

* Durante o processamento de solicitações, alguns dados de shardlet podem estar presentes tanto no fragmento de origem quanto no de destino. Isso é necessário no momento para proteger contra falhas durante a movimentação de shardlet. Conforme explicado acima, a integração de Divisão/Mesclagem com o mapa do fragmento de Escala Elástica garante que as conexões por meio das APIs de roteamento dependentes de dados usando o método **OpenConnectionForKey** no mapa de fragmento não vejam nenhum estado intermediário inconsistente. No entanto, ao conectar-se à fonte ou aos fragmentos de destino sem usar o método **OpenConnectionForKey**, os estados intermediários inconsistentes podem ser visíveis quando solicitações de divisão/mesclagem/movimentação estiverem sendo executadas. Essas conexões podem mostrar resultados parciais ou duplicados, dependendo do tempo ou do fragmento que subjaz à conexão. Atualmente, essa limitação inclui as conexões feitas por Consultas-Multi-Fragmento de Escala Elástica.

* O banco de dados de metadados de Divisão/Mesclagem não deve ser compartilhado entre funções diferentes. Por exemplo, uma função do serviço de Divisão/Mesclagem executando em preparo precisa apontar para um banco de dados de metadados diferentes da função de produção.
 

## Cobrança 

Uma vez que o serviço de Divisão/Mesclagem é executado como um serviço de nuvem na sua assinatura do Microsoft Azure, encargos regulares para serviços de nuvem se aplicam para sua instância de Divisão/Mesclagem. A menos que você execute operações de divisão/mesclagem/movimentação com frequência, recomendamos que você exclua seu serviço de nuvem de Divisão/Mesclagem. Isso ajuda a economizar custos com execução ou instâncias de serviço de nuvem implantadas. Você pode implantar novamente e iniciar sua configuração prontamente executável sempre que desejar executar operações de Divisão/Mesclagem. 
  
## Monitoramento 
### Tabelas de status 

O serviço de Divisão/Mesclagem fornece a tabela **RequestStatus**nos banco de dados de armazenamento dos metadados para o monitoramento das solicitações em andamento e concluídas. A tabela relaciona uma linha para cada solicitação de Divisão/Mesclagem enviada para essa instância do serviço de Divisão/Mesclagem. Ele oferece as seguintes informações para cada solicitação:

* **Timestamp**: A hora e data em que a solicitação foi iniciada.

* **ID da operação**: Um GUID que identifica exclusivamente a solicitação. Essa solicitação também pode ser usada para cancelar a operação enquanto ela ainda está em andamento.

* **Status**: O estado atual da solicitação. Solicitações em andamento, também relaciona a fase em que a solicitação está no momento.

* **CancelRequest**: Um sinalizador que indica se a solicitação foi cancelada.

* **Progresso**: Uma estimativa do percentual de conclusão da operação. Um valor de 50 indica que a operação está aproximadamente 50% concluída.

* **Detalhes**: Um valor XML que fornece um relatório de progresso mais detalhado. O relatório de andamento é atualizado periodicamente a medida em que conjuntos de linhas são copiados da origem ao destino. No caso de falhas ou exceções, essa coluna também inclui informações mais detalhadas sobre a falha.


### Diagnóstico do Azure 

O modelo de serviço para Divisão/Mesclagem é pré-configurado para usar o armazenamento do WAD (Diagnóstico do Windows Azure) para armazenamento de diagnóstico e log detalhado adicional. Para controlar a configuração do WAD, como a conta de armazenamento e as credenciais, por meio do seu arquivo de configuração de serviço de Divisão/Mesclagem. A configuração do WAD para o serviço segue as diretrizes dos [Conceitos básicos do serviço de nuvem](http://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Inclui as definições para registrar os Contadores de desempenho, logs do IIS, Logs de eventos do Windows e logs de eventos do aplicativo de Divisão/Mesclagem. Você pode acessar facilmente esses logs no Gerenciador de Servidores do Visual Studio, na parte do Azure na árvore do Explorador:

![Azure Diagnostics][2]   

O WADLogsTable realçado na figura acima contém os eventos detalhados do log de aplicativo do serviço de Divisão/Mesclagem. No entanto, observe que a configuração padrão fornecida como parte do pacote baixado é destinada a uma implantação de produção. Como consequência, o intervalo em que contadores e logs são extraídos das instâncias de serviço é grande (5 minutos). Para teste e desenvolvimento, você pode diminuir o intervalo, ajustando as configurações de diagnóstico da web ou da função de trabalho, de acordo com as suas necessidades. Você pode fazer isso clicando com o botão direito na função no Gerenciador de Servidores do Visual Studio (veja acima) e, em seguida, ajustando o Período de transferência na caixa de diálogo de configuração do Diagnóstico:

![Configuration][3]


As diferentes guias na caixa de diálogo controlam os tipos de log diferentes, cada uma delas tem sua própria configuração do período de Transferência. 
Visibilidade sobre os logs e contadores no WAD será, normalmente, solicitada pelas equipes da Microsoft caso haja problemas com a implantação do serviço de Divisão/Mesclagem. Você pode usar ferramentas como o [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/) para exportar logs para arquivos CSV facilmente compartilháveis do WAD. 

## Desempenho

O desempenho das suas operações de divisão, mescla e movimentação dependem de vários fatores. Em geral, é esperado um melhor desempenho das camadas de serviço maiores e mais funcionais no Banco de dados SQL do Azure. Alocações de E/S, CPU e memória maiores, para camadas de serviço mais altas, beneficiarão a cópia em massa e excluirão operações que o serviço de Divisão/Mesclagem usa internamente. Por esse motivo, talvez seja interessante para aumentar a camada de serviço apenas para um determinado conjunto de bancos de dados por um período limitado e bem definido de tempo para concluir, rapidamente, as operações de serviço de Divisão/Mesclagem agendadas para intervalos hospedados nesses bancos de dados.

Observe que o serviço também executa consultas de validação como parte de suas operações normais. Essas consultas de validação, entre outras coisas, verificam a presença inesperada de dados no intervalo de destino e garantem que qualquer operação de divisão/mesclagem/movimentação comecem de um estado consistente. Todas essas consultas funcionam em intervalos de chave de fragmentação definidos pelo escopo da operação de divisão/mesclagem/movimentação e o tamanho do lote fornecido como parte da definição de solicitação. Essas consultas têm melhor desempenho quando um índice é apresentado com a chave de fragmentação como coluna à esquerda. 

Além disso, uma propriedade de exclusividade com a chave de fragmentação como a coluna principal permitirá que o serviço use uma abordagem otimizada, que limita o consumo de recursos em termos de memória e de espaço de log. Essa propriedade de exclusividade é necessária para mover os tamanhos de dados grandes (normalmente acima de 1GB). 

## Práticas recomendadas e solução de problemas 
-    Considere a definição de um locatário de teste e exercite suas mais importantes operações de divisão/mesclagem/movimentação com o locatário de teste ao longo de vários fragmentos. Isso ajudará a garantir que todos os metadados sejam definidos corretamente no seu mapa de fragmento e que as operações não violem restrições ou chaves estrangeiras.
-    Mantenha o tamanho dos dados de locatário de teste acima do tamanho máximo de dados do seu maior locatário para garantir que você não encontrará problemas relacionados ao tamanho dos dados. Isso também o ajudará a avaliar um limite superior no tempo que leva para mover um único locatário ao redor. 
-    O serviço de Divisão/Mesclagem requer a capacidade de remover dados de fragmento de origem depois que os dados tiverem sido copiados com êxito para o destino. Inspecione cuidadosamente o esquema para garantir que seu esquema permita as exclusões. Por exemplo, excluir gatilhos pode impedir o serviço de excluir dados na origem e pode causar falhas nas operações.
-    Certifique-se de que a chave de fragmentação é a coluna à esquerda em sua chave primária ou a definição de índice exclusivo. Isso vai garantir o melhor desempenho para as consultas de validação de divisão/mesclagem e para as operações de movimentação e exclusão de dados reais que sempre operam em intervalos de chave de fragmentação.
-    Por motivos de desempenho e de custo, a colocação do seu serviço de divisão/mesclagem no centro de dados e na região onde residem seus bancos de dados são, geralmente, a melhor opção. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

## Referências 

* [Tutorial de Mesclagem-Divisão](sql-database-elastic-scale-configure-deploy-split-and-merge.md)

* [Considerações de segurança de dimensionamento Elástico](sql-database-elastic-scale-configure-security.md)  


<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-split-and-merge/diagnostics-config.png

<!--HONumber=47-->
