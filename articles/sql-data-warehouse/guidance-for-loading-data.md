---
title: "Orientação sobre carregamento de dados - SQL Data Warehouse do Azure | Microsoft Docs"
description: "Recomendações para carregar dados e executar ELT com o SQL Data Warehouse do Azure."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 7b698cad-b152-4d33-97f5-5155dfa60f79
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 12/13/2017
ms.author: barbkess
ms.openlocfilehash: 8903be1361d1574a5d81b69223f608ccb7a698ea
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="guidance-for-loading-data-into-azure-sql-data-warehouse"></a>Guia para carregar dados no SQL Data Warehouse do Azure
Recomendações e otimizações de desempenho para carregar dados no SQL Data Warehouse do Azure. 

- Para saber mais sobre o PolyBase e a criação de um processo de Extrair, carregar e transformar (ELT), consulte [Projetar ELT para o SQL Data Warehouse](design-elt-data-loading.md).
- Para ver um tutorial de carregamento, [Usar o PolyBase para carregar dados do armazenamento de blobs do Azure para o SQL Data Warehouse do Azure](load-data-from-azure-blob-storage-using-polybase.md).


## <a name="extract-source-data"></a>Extrair dados de origem

Ao exportar dados para um Formato de Arquivo ORC do SQL Server ou do SQL Data Warehouse do Azure, as colunas pesadas de texto podem ser limitadas a apenas 50 colunas, devido a erros de memória insuficiente do Java. Para resolver isso, exporte apenas um subconjunto das colunas.


## <a name="land-data-to-azure"></a>Enviar dados para o Azure
O PolyBase não consegue carregar linhas que tenham mais de um milhão de bytes de dados. Ao colocar dados nos arquivos de texto no armazenamento de blob do Azure ou do Azure Data Lake Store, eles devem ter menos de um milhão de bytes de dados. Isso é verdadeiro, independentemente do esquema de tabela definido.

Colocalizar sua camada de armazenamento e o data warehouse para minimizar a latência.

## <a name="data-preparation"></a>Preparação dos dados

Todos os formatos de arquivo têm características diferentes de desempenho. Para o carregamento mais rápido, use arquivos de texto delimitados compactados. A diferença entre o desempenho de UTF-16 e UTF-8 é mínima.

Dividir arquivos compactados grandes em arquivos compactados menores.

## <a name="create-designated-loading-users"></a>Criar usuários de carregamento designados
Para executar cargas com recursos de computação apropriados, crie usuários de carregamento designados para executar cargas. Atribua cada usuário de carregamento para uma classe de recurso específica. Para executar uma carga, faça logon como um dos usuários de carregamento e execute a carga. A carga é executada com a classe de recurso do usuário.  Esse método é mais simples do que tentar alterar a classe de recurso do usuário para se ajustar à necessidade de classe de recurso atual.

Esse código cria um usuário de carregamento para a classe de recurso staticrc20. Ele fornece permissão de uso de controle de usuário em um banco de dados e adiciona o usuário como um membro da função de banco de dados staticrc20. Para executar uma carga com recursos para as classes de recursos staticRC20, simplesmente faça logon como LoaderRC20 e execute a carga. 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

Execute cargas sob classes de recursos estáticas em vez de dinâmicas. Usar as classes de recursos estáticas garante os mesmos recursos, independentemente de seu [nível de serviço](performance-tiers.md#service-levels). Se você usar uma classe de recursos dinâmicos, os recursos variam de acordo com seu nível de serviço. Para classes dinâmicas, um nível inferior do serviço significa que você provavelmente precisa usar uma classe de recursos maior para seu usuário de carregamento.

### <a name="example-for-isolating-loading-users"></a>Exemplo para isolar usuários de carregamento

Geralmente, há a necessidade de ter vários usuários que podem carregar dados em um SQL DW. Como [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] requer permissões CONTROL do banco de dados, você acabará tendo vários usuários com acesso de controle sobre todos os esquemas. Para limitar isso, você pode usar a instrução DENY CONTROL.

Por exemplo: considere os esquemas de banco de dados, schema_A para o departamento A e schema_B para o departamento B. Os usuários de banco de dados user_A e user_B serão usuários de carregamento de PolyBase nos departamentos A e B respectivamente. Ambos receberam permissões de banco de dados CONTROL. Os criadores dos esquemas A e B agora bloquearam seus esquemas usando DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   

Agora os user_A e user_B são bloqueados do esquema do outro departamento.


## <a name="load-to-a-staging-table"></a>Carregar uma tabela de preparo

Para maior velocidade de carregamento, carregue-o em uma tabela de preparo de heap, round_robin. Essa será a maneira mais eficiente para mover os dados da camada de Armazenamento do Azure para o SQL Data Warehouse.

Aumente o data warehouse se esperar um trabalho de carregamento grande.

Executar apenas um trabalho de carregamento por vez para obter um desempenho ideal de carga

### <a name="optimize-columnstore-index-loads"></a>Otimizar os carregamentos do índice columnstore

Os Índices columnStore exigem muita memória para compactar os dados em rowgroups de alta qualidade. Para obter a melhor compactação e eficiência de índice, o índice columnstore precisa compactar 1.048.576 linhas em cada rowgroup. Esse é o número máximo de linhas por rowgroup. Quando há pressão de memória, o índice columnstore pode não ser capaz de alcançar as taxas máximas de compactação. Por sua vez, isso afeta o desempenho da consulta. Para uma análise profunda, consulte [Otimizações de memória columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Para garantir que o usuário de carregamento tenha memória suficiente para alcançar as taxas máximas de compactação, use usuários de carregamento que sejam membros de uma classe de recursos média ou grande. 
- Carregue linhas suficientes para preencher completamente novos rowgroups. Durante um carregamento em massa, cada 1.048.576 linhas vão diretamente para o columnstore. Carregamentos com menos de 102.400 linhas enviam as linhas para o deltastore, que mantém linhas em um índice clusterizado até que haja o suficiente para compactação. Se você carregar um número muito pequeno de linhas, elas podem ir todas para o deltastore e não serem compactadas imediatamente no formato de columnstore.


### <a name="handling-loading-failures"></a>Tratamento de falhas de carregamento

Um carregamento usando uma tabela externa pode falhar com o erro *“Consulta anulada – o limite de rejeição máximo foi atingido durante a leitura de uma fonte externa”*. Isso indica que os dados externos contêm registros *sujos* . Um registro de dados é considerado “sujo” se os tipos de dados/número de colunas reais não correspondem às definições de coluna da tabela externa ou se os dados não são compatíveis com o formato de arquivo externo especificado. 

Para corrigir esse problema, verifique se a tabela externa e as definições de formato de arquivo externo estão corretas e se os dados externos são compatíveis com essas definições. Caso um subconjunto de registros de dados externos esteja sujo, é possível rejeitar esses registros para suas consultas usando as opções de rejeição em CREATE EXTERNAL TABLE DDL.



## <a name="insert-data-into-production-table"></a>Inserir dados na tabela de produção
Essas são recomendações para inserir linhas nas tabelas de produção.


### <a name="batch-insert-statements"></a>Instruções de INSERT em lotes
Uma única carga para uma pequena tabela com uma [instrução INSERT](/sql/t-sql/statements/insert-transact-sql.md) ou mesmo uma recarga periódica de uma pesquisa pode ser útil para satisfazer suas necessidades com uma instrução como `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Inserções únicas de toneladas não são tão eficientes como executar um carregamento em massa. 

Se você tiver milhares ou mais de inserções únicas ao longo do dia, crie lotes para as inserções para que possa carregá-las em massa.  Desenvolva seus processos para acrescentar as inserções únicas para um arquivo e depois crie outro processo que periodicamente carrega o arquivo.

### <a name="create-statistics-after-the-load"></a>Criar estatísticas após o carregamento

Para melhorar o desempenho de suas consultas, é importante que as estatísticas sejam criadas em todas as colunas de todas as tabelas após o primeiro carregamento ou ocorrem alterações significativas nos dados.  Para obter uma explicação detalhada das estatísticas, confira [Estatísticas][Estatísticas]. O exemplo a seguir cria estatísticas em cinco colunas da tabela Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Girar as chaves de armazenamento
É uma boa prática de segurança alterar a chave de acesso para seu armazenamento de blob regularmente. Você tem duas chaves de armazenamento para sua conta de armazenamento de blob. Isso é para que você possa fazer a transição das chaves.

Para girar chaves de conta de armazenamento do Azure:

1. Crie uma segunda credencial de escopo do banco de dados com base na chave de acesso de armazenamento secundário.
2. Crie uma segunda fonte de dados externa com base nessa nova credencial.
3. Remova e crie as tabelas externas para que apontem para a nova fonte de dados externa. 

Depois de migrar suas tabelas externas para a nova fonte de dados, execute estas tarefas de limpeza:

1. Remover a primeira fonte de dados externa.
2. Remover a primeira credencial no escopo do banco de dados na chave de acesso de armazenamento primária.
3. Fazer logon no Azure e regenerar a chave de acesso primária para que ela esteja pronta para a próxima rotação.


## <a name="next-steps"></a>Próximas etapas
Para monitorar o processo de carregamento, consulte [Monitorar sua carga de trabalho usando DMVs](sql-data-warehouse-manage-monitor.md).



