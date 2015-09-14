<properties 
	pageTitle="Criando conjuntos de dados"
	description="Entenda os conjuntos de dados do Azure Data Factory e aprenda como criá-los."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags 
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="spelluru"/>

# Conjunto de dados

## Descrição
Um conjunto de dados é uma descrição lógica dos dados. Os dados que estão sendo descritos podem variar de simples bytes, dados semiestruturados como arquivos CSV até mesmo modelos ou tabelas relacionais. O mecanismo (endereço, protocolo, esquema de autenticação) para acessar os dados é definido no serviço vinculado e referenciado na definição de conjunto de dados.

## Sintaxe

	{
	    "name": "<name of dataset>",
	    "properties":
	    {
	       "structure": [ ],
	       "type": "<type of dataset>",
			"external": <boolean flag to indicate external data>,
	        "typeProperties":
	        {
	        },
	        "availability":
	        {
	
	        },
	       "policy": 
	        {      
	
	        }
	    }
	}

**Detalhes da sintaxe**

| Propriedade | Descrição | Obrigatório | Padrão |
| -------- | ----------- | -------- | ------- |
| Nome | Nome do conjunto de dados | Sim | ND |
| Estrutura | <p>Esquema do conjunto de dados</p><p>Consulte a seção [Estrutura do conjunto de dados](#Structure) para obter mais detalhes</p> | Nº | ND |
| Tipo | Tipo de conjunto de dados | Sim | ND |
| typeProperties | <p>Propriedades que correspondem ao tipo selecionado</p><p>Consulte a seção [Tipo de conjunto de dados](#Type) para obter detalhes sobre os tipos com suporte e suas propriedades.</p> | Sim | ND |
| externo | Sinalizador booliano para especificar se um conjunto de dados é explicitamente produzido por um pipeline de fábrica de dados ou não | Não | false | 
| Disponibilidade | <p>Define a janela de processamento ou o modelo de divisão para a produção de conjunto de dados. </p><p>Consulte o tópico [Disponibilidade do conjunto de dados](#Availability) para obter mais detalhes</p><p>Consulte o artigo [Agendamento e execução](data-factory-scheduling-and-execution.md) para obter mais detalhes sobre o modelo de divisão do conjunto de dados</p> | Sim | ND
| policy | Define os critérios ou a condição que as fatias de conjunto de dados devem atender. <p>Consulte o tópico [Política de conjunto de dados](#Policy) para obter mais detalhes</p> | Não | ND |

### Exemplo

Abaixo está um exemplo de um conjunto de dados que representa uma tabela chamada **MyTable**no **Banco de dados SQL do Azure**. As cadeias de conexão de banco de dados SQL do Azure são definidas no **AzureSqlLinkedService** referenciado neste conjunto de dados. Esse conjunto de dados é dividido diariamente.

	{
	    "name": "DatasetSample",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": 
	        {
	            "tableName": "MyTable"
	        },
	        "availability": 
	        {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

## <a name="Structure"></a>Estrutura do conjunto de dados

A seção **Estrutura** declara o esquema do conjunto de dados. Ela contém a coleção de colunas e o tipo dessas colunas. Cada coluna contém as seguintes propriedades:

Propriedade | Descrição | Obrigatório | Padrão  
-------- | ----------- | -------- | -------
name | Nome da coluna | Não | ND 
type | Tipo de dados da coluna | Não | ND 

### Exemplo

No exemplo a seguir, o conjunto de dados tem três colunas: slicetimestamp, projectname e pageviews.

	structure:  
	[ 
	    { "name": "slicetimestamp", "type": "String"},
	    { "name": "projectname", "type": "String"},
	    { "name": "pageviews", "type": "Decimal"}
	]

## <a name="Type"></a> Tipo de conjunto de dados

As fontes de dados com suporte e os tipos de conjunto de dados são alinhados. Consulte os tópicos de conector referenciados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter informações sobre os tipos e a configuração de conjuntos de dados.

## <a name="Availability"></a> Disponibilidade do conjunto de dados

A seção Disponibilidade em um conjunto de dados define a janela de processamento ou o modelo de divisão para a produção de conjunto de dados. Consulte o tópico Divisão do conjunto de dados para obter mais detalhes sobre o modelo de divisão e dependência de conjunto de dados.

| Propriedade | Descrição | Obrigatório | Padrão |
| -------- | ----------- | -------- | ------- |
| frequência | Especifica a unidade de tempo para a produção da divisão de conjunto de dados.<p>**Frequência com suporte**: minuto, hora, dia, semana, mês</p> | Sim | ND |
| intervalo | Especifica um multiplicador para frequência<p>"Intervalo x da frequência" determina a frequência com que a divisão é produzida.</p><p>Se você precisar que o conjunto de dados seja dividido por hora, defina **Frequência** como **Hora** e o **Intervalo** como **1**.</p><p>** Observação:** se você especificar a frequência como minuto, recomendamos que você defina o intervalo como menor do que 15</p> | Sim | ND |
| estilo | Especifica se a divisão deve ser produzida no início/término do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><p>Se a frequência é definida como Mês e o estilo é definido como EndOfInterval, a divisão é produzida no último dia do mês. Se o estilo é definido como StartOfInterval, a divisão é produzida no primeiro dia do mês. <.p><p>Se Frequência é definida como Dia e estilo é definido como EndOfInterval, a divisão é gerada na última hora do dia.</p>Se a Frequência é definida como Hora e estilo é definido como EndOfInterval, a divisão é produzida ao final da hora. Por exemplo, para uma divisão de período 13h – 14h, a divisão é produzida às 14h.</p> | Não | EndOfInterval |
| anchorDateTime | Define a posição absoluta no tempo usado pelo agendador para computar limites de divisão do conjunto de dados.<p>** Observação:** se o AnchorDateTime tem partes de data que são mais granulares do que a frequência, as partes mais granulares serão ignoradas. Por exemplo, se o **intervalo** é **por hora** (frequência: hora e intervalo: 1) e o **AnchorDateTime** contém **minutos e segundos**, as partes **minutos e segundos** do AnchorDateTime serão ignoradas.</p>| Não | 01/01/0001 |
| Deslocamento | O intervalo de tempo pelo qual o início e término de todas as divisões de conjunto de dados são deslocados.<p>**Observação:** se anchorDateTime e o deslocamento forem especificados, o resultado é a mudança combinada.</p> | Não | ND |

### Exemplos de anchorDateTime

**Exemplo:** divisões de conjuntos de dados de 23 horas que começam em 2007-04-19T08:00:00

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": "23",	
		"anchorDataTime":"2007-04-19T08:00:00"	
	}


### exemplo de deslocamento

Divisões diárias que iniciam às 6h, em vez da meia-noite do padrão.

	"availability":
	{
		"frequency": "Daily",
		"interval": "1",
		"offset": "06:00:00"
	}

Nesse caso, SliceStart é deslocado por seis horas e será 6h.

Para uma agenda de 12 meses (frequência = mês; intervalo = 12), o deslocamento: 60.00:00:00 significa cada ano em 2 ou 3 de março (60 dias desde o início do ano se estilo = StartOfInterval), dependendo do ano ser ano bissexto ou não.



## <a name="Policy"></a>Política de conjunto de dados

A seção Política no conjunto de dados define os critérios ou a condição que as divisões de conjunto de dados devem atender.

### Políticas de validação

| Nome da política | Descrição | Aplicado a | Obrigatório | Padrão |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Valida se os dados em um blob do Azure atendem aos requisitos de tamanho mínimo (em megabytes). | Blob do Azure | Não | ND |
|minimumRows | Valida se os dados em um banco de dados SQL do Azure ou uma tabela do Azure contêm o número mínimo de linhas. | <ul><li>Banco de dados SQL Azure</li><li>Tabela do Azure</li></ul> | Não | ND

#### Exemplos

**minimumSizeMB:**

	"policy":
	
	{
	    "validation":
	    {
	        "minimumSizeMB": 10.0
	    }
	}

**minimumRows**

	"policy":
	{
		"validation":
		{
			"minimumRows": 100
		}
	}

### Políticas ExternalData

Conjuntos de dados externos são aqueles que não são produzidos por um pipeline em execução na fábrica dados. Se o conjunto de dados estiver marcado como Externo, a política ExternalData pode ser definida para influenciar o comportamento da disponibilidade da divisão do conjunto de dados.

| Nome | Descrição | Obrigatório | Valor Padrão |
| ---- | ----------- | -------- | -------------- |
| dataDelay | <p>Tempo para esperar a verificação na disponibilidade dos dados externos de uma determinada divisão. Por exemplo, se os dados tiverem que estar disponíveis por hora, para verificar se os dados externos estão realmente disponíveis e se a divisão correspondente preparada pode ser atrasada por dataDelay.</p><p>Só se aplica a hora atual. Por exemplo, se for 13h agora e esse valor for 10 minutos, a validação será iniciada em 13h10.</p><p>Essa configuração não afeta divisões no passado (divisões com a hora de término da fatia + dataDelay < agora serão processadas sem atraso).</p> <p>Um período de tempo maior que 23:59 horas precisa ser especificado usando o formato dia.horas:minutos:segundos. Por exemplo, para especificar 24 horas, não use 24:00:00; em vez disso, use 1.00:00:00. Se você usar 24:00:00, esse valor será tratado como 24 dias (24.00:00:00). Para 1 dia e 4 horas, especifique 1:04:00:00. </p>| Não | 0 |
| retryInterval | O tempo de espera entre uma falha e a próxima tentativa de repetição. Aplica-se a hora atual. Se o anterior falhou, podemos esperar muito tempo após a última tentativa. <p>Se forem 13h agora, começaremos a primeira tentativa. Se a duração para concluir a primeira verificação de validação for 1 minuto e a operação falhou, a próxima repetição será 1:00 + 1 min (duração) + 1min (intervalo de repetição) = 13h02. </p><p>Para divisões no passado, não haverá nenhum atraso. A repetição acontecerá imediatamente.</p> | Não | 00:01:00 (1 minuto) | 
| retryTimeout | O tempo limite para cada tentativa de repetição.<p>Se for definido como 10 minutos, a validação deve ser concluída em 10 minutos. Se demorar mais de 10 minutos para executar a validação, a repetição atingirá o tempo limite.</p><p>Se todas as tentativas para a validação excederem o tempo limite, a divisão será marcada como TimedOut.</p> | Não | 00:10:00 (10 minutos) |
| maximumRetry | Número de vezes para verificar a disponibilidade dos dados externos. O valor máximo permitido é 10. | Não | 3 | 

#### Mais exemplos

Se precisar executar um pipeline mensalmente em uma data e hora específicas (digamos que seja no dia 3 de cada mês às 8h), você poderá usar a marca **offset** para definir a data e a hora de execução.

	{
	  "name": "MyDataset",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "availability": {
	      "frequency": "Month",
	      "interval": 1,
	      "offset": "3.08:10:00",
	      "style": "StartOfInterval"
	    }
	  }
	}

## Enviar comentários
Apreciamos muito seus comentários sobre este artigo. Reserve alguns minutos para enviar seus comentários por meio de [email](mailto:adfdocfeedback@microsoft.com?subject=data-factory-create-datasets.md).





  

<!---HONumber=September15_HO1-->