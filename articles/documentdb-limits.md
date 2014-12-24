<properties title="DocumentDB limits for the preview release" pageTitle="Limites da versão de teste do Banco de Dados de Documentos | Azure" description="Learn about the limits and quota enforcements of DocumentDB for the preview release." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />


#Limites da versão de teste do Banco de Dados de Documentos
A tabela a seguir descreve os limites e imposições de cota do Banco de Dados de Documentos durante a versão de teste. Na maioria dos casos, os limites são impostos com a intenção de obter seus comentários ou com base nas restrições de capacidade atual. Se você tem a necessidade comercial de reduzir os limites, entre em contato conosco e faremos o possível para acomodá-la dentro das restrições da oferta pública.    

|Entidade |Cota (oferta Standard da versão de teste)|
|-------|--------|
|Contas do banco de dados     |5
|Número de bancos de dados por conta de banco de dados     |100
|Número de usuários por conta de banco de dados - em todos os bancos de dados |500.000
|Número de permissões por conta de banco de dados - em todos os bancos de dados   |2.000.000
|Armazenamento de anexos por conta de banco de dados      |2 GB
|Número máximo de unidades de capacidade por conta de banco de dados       |5
|Número de coleções por unidade de capacidade      |3
|Armazenamento mínimo alocado por coleção com, no mínimo, 1 documento    |3,3 GB
|Rendimento mínimo alocado por coleção com, no mínimo, 1 documento |667 RUs
|Elasticidade de uma coleção    |0-10 GB
|Máximo de unidades de solicitação/s por coleção   |2000
|Número de procedimentos armazenados, gatilhos e UDFs por coleção       |25 cada
|Tempo de execução máximo para procedimento armazenado e gatilho     |5 segundos
|Armazenamento/unidade de capacidade de documento provisionado |10 GB
|Unidades de solicitação/s provisionadas por unidade de capacidade     |2000
|Armazenamento máximo de documentos por banco de dados (5 unidades de capacidade)    |50 GB
|Tamanho máximo da propriedade Id    |255 caracteres
|Número padrão de itens por página     |100
|Máximo de itens por página        |1000
|Tamanho máximo da solicitação de documento e anexo       |256 KB
|Tamanho máximo da solicitação de procedimento armazenado, gatilho e UDF        |256 KB
|Tamanho de resposta máximo |1 MB
|Número máximo de caminhos exclusivos por coleção       |100
|Cadeia de caracteres |Todas as cadeias de caracteres devem ser codificadas em UTF-8. Como UTF-8 é uma codificação com largura variável, os tamanhos das cadeias de caracteres são determinados usando os bytes UTF-8.
|Tamanho máximo da propriedade ou valor  |Nenhum limite prático
|Número máximo de UDFs por consulta     |1
|Número máximo de JOINs por consulta    |2
|Número máximo de cláusulas AND por consulta      |5
|Número máximo de cláusulas OR por consulta       |5
