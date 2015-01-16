<properties title="Sharding Overview" pageTitle="Visão geral de fragmentação" description="Motivos para a fragmentação: dimensionar os recursos de banco de dados para aumentar a disponibilidade ou o desempenho." metaKeywords="sharding, scaling, elastic scale, Azure SQL Database" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />
#Visão geral de fragmentação 

##Princípios de fragmentação 

A **Fragmentação** é uma técnica para distribuir grandes quantidades de dados estruturado de forma idêntica em vários bancos de dados independentes. É especialmente popular com desenvolvedores de nuvem que estiverem criando Software como as ofertas de serviço (SAAS) para clientes finais ou empresas. Esses clientes finais são referidos como "Locatários". Fragmentação pode ser necessária por vários motivos: 

* A quantidade total de dados é muito grande para caber dentro das restrições de um único banco de dados 
* A taxa de transferência de transação da carga de trabalho geral excede as capacidades de um único banco de dados 
* Inquilinos podem exigir isolamento físico entre si, para que bancos de dados separados são necessários para cada locatário 
* Seções diferentes de um banco de dados talvez precise residir em regiões diferentes para questões geopolíticas, de desempenho ou de conformidade. 
 
A fragmentação funciona melhor quando todas as transações em um aplicativo podem ser restrita a um único valor de uma chave de fragmentação. Isso garante que todas as transações serão locais para um banco de dados específico. 

Alguns aplicativos usam a abordagem mais simples de criar um banco de dados separado para cada locatário. Este é o **padrão de fragmentação de locatário único** que fornece isolamento, capacidade de backup/restauração e escala de recurso na granularidade do inquilino. Com a fragmentação de locatário único, cada banco de dados está associado a um valor de ID específico do locatário (ou o valor de chave de cliente), mas essa chave não deve sempre estar presente nos próprios dados. É responsabilidade do aplicativo para rotear cada solicitação para o banco de dados apropriado. 

![][1]

Outros cenários de vários locatários pack juntos em bancos de dados, em vez de isolá-los em bancos de dados separados. Esse é um **padrão típico multilocatário fragmentação** - e pode ser orientada por considerações de custo, eficiência ou o fato de que um aplicativo gerencia grandes números de locatários muito pequenos. Na fragmentação multilocatária, as linhas nas tabelas de banco de dados foram projetadas para executar uma chave que identifica a ID do locatário ou a chave de fragmentação. Novamente, a camada de aplicativo é responsável pelo roteamento de solicitação de um locatário no banco de dados apropriado. 

Em outros cenários, como inclusão de dados de dispositivos distribuídos, a fragmentação pode ser usada para preencher um conjunto de bancos de dados distribuídos em um período de tempo. Por exemplo, um banco de dados pode ser dedicado a cada dia ou semana. Nesse caso, a chave de fragmentação pode ser um número inteiro que representa a data (presente em todas as linhas das tabelas fragmentadas) e as consultas que recuperam informações para um intervalo de datas devem ser roteadas pelo aplicativo para o subconjunto de bancos de dados que abrangem o intervalo em questão.

Independentemente do modelo de fragmentação que está sendo usado, uma estrutura de dados especial, conhecida como um **mapa do fragmento**, serve como uma tabela de pesquisa que associa valores de chave de fragmentação de bancos de dados; isso permite que o aplicativo execute o roteamento de solicitações de banco de dados. Isso é conhecido como **roteamento dependente de dados** e é uma das principais funcionalidades necessárias para que um aplicativo use uma camada de dados fragmentados. As [APIs de cliente de Escala Elástica](http://go.microsoft.com/?linkid=9862592) fornecem um conjunto rico de funcionalidades necessárias para [gerenciar mapas de fragmento](http://go.microsoft.com/?linkid=9862595) e habilitando [Data-depenendent routing capabilities](http://go.microsoft.com/?linkid=9862596) eficientes e fáceis de usar em um aplicativo. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-sharding-overview/tenancy.png
