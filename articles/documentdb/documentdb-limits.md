<properties 
	pageTitle="Limites e cotas do Banco de Dados de Documentos | Azure" 
	description="Saiba mais sobre os limites e imposições de cota do Banco de Dados de Documentos." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="mimig"/>


#Limites e cotas do Banco de Dados de Documentos

A tabela a seguir descreve os limites e imposições de cota do Banco de Dados de Documentos. Cotas listadas com um asterisco (*) [pode ser ajustado entrando em contato com o suporte do Azure](documentdb-increase-limits.md).

|Entidade |Cota (oferta padrão)|
|-------|--------|
|Contas de banco de dados* |5
|Número de bancos de dados por conta de banco de dados |100
|Número de usuários por conta de banco de dados — em todos os bancos de dados |500.000
|Número de permissões por conta de banco de dados — em todos os bancos de dados |2.000.000
|Armazenamento de anexos por conta de banco de dados (recurso de visualização) |2 GB
|Máximo de unidades de solicitação / segundos por coleção |2500
|Número de procedimentos armazenados, gatilhos e UDFs por coleção* |25 cada
|Tempo de execução máximo para procedimento armazenado e gatilho |5 s
|Provisionamento de armazenamento de documentos / coleção |10 GB
|Coleções máxima por conta do banco de dados* |100
|Armazenamento máximo de documentos por banco de dados (100 coleções)* |1 TB
|Comprimento máximo da propriedade Id |255 caracteres
|Máximo de itens por página |1000
|Tamanho máximo da solicitação de documento e anexo |512KB
|Tamanho máximo da solicitação de procedimento armazenado, gatilho e UDF |512KB
|Tamanho de resposta máximo |1 MB
|Cadeia de caracteres |Todas as cadeias de caracteres devem ser codificadas em UTF-8. Como UTF-8 é uma codificação com largura variável, os tamanhos das cadeias de caracteres são determinados usando os bytes UTF-8.
|Comprimento máximo da propriedade ou valor |Sem limite prático
|Número máximo de UDFs por consulta* |1
|Número máximo de funções internas por consulta |Sem limite prático
|Número máximo de JOINs por consulta* |2
|Número máximo de cláusulas AND por consulta* |5
|Número máximo de cláusulas OR por consulta* |5
|Número máximo de valores por expressão IN* |100
|Número máximo de coleções criadas por minuto* |5
|Número máximo de operações de escala por minuto* |5
 

<!---HONumber=July15_HO1-->