<properties 
	pageTitle="Funções do Data Factory e Variáveis do Sistema | Microsoft Azure" 
	description="Fornece uma lista de funções do Azure Data Factory e variáveis do sistema" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"
	services="data-factory"
/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2016" 
	ms.author="spelluru"/>

# Azure Data Factory - Funções e Variáveis do Sistema
Este artigo fornece informações sobre funções e variáveis com suporte no Azure Data Factory.
  
## Variáveis do sistema do Data Factory

Nome de variável | Descrição | Escopo do objeto | Escopo JSON e casos de uso
------------- | ----------- | ------------ | ------------------------
WindowStart | Início do intervalo de tempo para a janela da execução de atividade atual | atividade | <ol><li>Especifica consultas de seleção de dados. Veja os artigos sobre o conector referenciados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md).</li><li>Transmita parâmetros para o script do Hive (exemplo mostrado acima).</li>
WindowEnd | Fim do intervalo de tempo para a janela da execução de atividade atual | atividade | o mesmo que o descrito acima
SliceStart | Início do intervalo de tempo para a fatia de dados sendo gerada | atividade<br/>conjunto de dados | <ol><li>Especifique caminhos de pasta dinâmicos e nomes de arquivos trabalhando simultaneamente com o [Blob do Azure](data-factory-azure-blob-connector.md) e os [Conjuntos de dados do Sistema de Arquivos](data-factory-onprem-file-system-connector.md).</li><li>Especifique as dependências de entrada com funções da fábrica de dados na coleção de entradas de atividade.</li></ol>
SliceEnd | Fim do intervalo de tempo para a fatia de dados atual que está sendo gerada | atividade<br/>conjunto de dados | O mesmo que o descrito acima. 

> [AZURE.NOTE] Atualmente, o data factory exige que a agenda especificada na atividade corresponda exatamente à agenda especificada na disponibilidade do conjunto de dados de saída. Isso significa que WindowStart, WindowEnd e SliceStart e SliceEnd sempre são mapeados para o mesmo período de tempo e uma única fatia de saída.
 
## Funções do Data Factory 

Você pode usar funções no Data Factory junto com as variáveis do sistema mencionadas acima para as seguintes finalidades:

1.	Especificando consultas de seleção de dados (veja os artigos sobre o conector referenciados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md).

	A sintaxe para invocar uma função do Data Factory é: **$$<function>** para consultas de seleção de dados e outras propriedades na atividade e nos conjuntos de dados.  
2. Especificar dependências de entrada com funções de data factory na coleção de entradas da atividade (consulte o exemplo acima).

	$$ não é necessário para especificar expressões de dependência de entrada.

No exemplo a seguir, a propriedade **sqlReaderQuery** em um arquivo JSON é atribuída a um valor retornado pela função **Text.Format**. Este exemplo também usa uma variável de sistema chamada **WindowStart**, que representa a hora de início da janela de execução de atividade.
	
	{
	    "Type": "SqlSource",
	    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
	}

### Funções

As tabelas a seguir listam todas as funções no Azure Data Factory:

Categoria | Função | Parâmetros | Descrição
-------- | -------- | ---------- | ----------- 
Hora | AddHours(X,Y) | X: DateTime <p>Y: int</p> | Adiciona Y horas até o momento X determinado. <p>Exemplo: 5/9/2013 12h + 2 horas = 5/9/2013 14h</p>
Hora | AddMinutes(X,Y) | X: DateTime <p>Y: int</p> | Adiciona Y minutos a X.<p>Exemplo: 15/9/2013 12h + 15 minutos = 15/9/2013 12h15</p>
Hora | StartOfHour(X) | X: Datetime | Obtém a hora de início para a hora representada pelo componente de hora do X. <p>Exemplo: a StartOfHour de 15/9/2013 17h10min23 é 15/9/2013 17h</p>
Data | AddDays(X,Y) | X: DateTime<p>Y: int</p> | Adiciona Y dias a X.<p>Exemplo: 15/9/2013 12h + 2 dias = 17/9/2013 12h</p>
Data | AddMonths(X,Y) | X: DateTime<p>Y: int</p> | Adiciona Y meses a X.<p>Exemplo: 15/9/2013 12h + 1 mês = 15/10/2013 12h</p> 
Data | AddQuarters(X,Y) | X: DateTime <p>Y: int</p> | Adiciona Y * 3 meses a X.<p>Exemplo: 15/9/2013 12h + 1 trimestre = 15/12/2013 12h</p>
Data | AddWeeks(X,Y) | X: DateTime<p>Y: int</p> | Adiciona Y * 7 dias a X<p>Exemplo: 15/9/2013 12h + 1 semana = 22/9/2013 12h</p>
Data | AddYears(X,Y) | X: DateTime<p>Y: int</p> | Adiciona Y anos a X<p>Exemplo: 15/9/2013 12h + 1 ano = 15/9/2014 12h</p>
Data | Day(X) | X: DateTime | Obtém o componente de dia de X.<p>Exemplo: o componente Day de 15/9/2013 12h é 15.</p>
Data | DayOfWeek(X) | X: DateTime | Obtém o componente de dia da semana de X.<p>Exemplo: o DayOfWeek de 15/9/2013 12h é Domingo.</p>
Data | DayOfYear(X) | X: DateTime | Obtém o dia do ano que representa o componente de ano de X.<p>Exemplos:<br/>1/12/2015: dia 335 de 2015<br/>31/12/2015: dia 365 de 2015<br/>31/12/2016: dia 366 de 2016 (ano bissexto)</p>
Data | DaysInMonth(X) | X: DateTime | Obtém os dias do mês representados pelo componente de mês do parâmetro X.<p>Exemplo: DaysInMonth de 15/9/2013 são 30, já que há 30 dias do mês de setembro.</p>
Data | EndOfDay(X) | X: DateTime | Obtém a data e hora que representam o fim do dia (componente do dia) do X.<p>Exemplo: EndOfDay de 15/9/2013 17h10min23 é 15/9/2013 23h59min59.</p>
Data | EndOfMonth(X) | X: DateTime | Obtém o final do mês representado pelo componente de mês de parâmetro X.<p>Exemplo: EndOfMonth de 15/9/2013 17h09min23 é 30/9/2013 23h59min59 (data e hora que representam o final do mês de setembro)</p>
Data | StartOfDay(X) | X: DateTime | Obtém o início do dia representado pelo componente dia do parâmetro X.<p>Exemplo: StartOfDay de 15/9/2013 17:10:23 é 15/9/2013 12h.</p>
DateTime | From(X) | X: Cadeia de caracteres | Analise a cadeia de caracteres X para um valor de data e hora.
DateTime | Ticks(X) | X: DateTime | Obtém os tiques de propriedade do parâmetro X. Um tique é igual a 100 nanossegundos. O valor dessa propriedade representa o número de tiques que se passaram desde 0h, meia-noite de 1º de janeiro de 0001. 
Texto | Format(X) | X: variável de cadeia de caracteres | Formata o texto.

#### Exemplo de Text.Format

	"defines": { 
	    "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
	    "Month" : "$$Text.Format('{0:MM}',WindowStart)",
	    "Day" : "$$Text.Format('{0:dd}',WindowStart)",
	    "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
	}

Consulte o tópico [Cadeias de caracteres de formato de data e hora personalizados](https://msdn.microsoft.com/library/8kb3ddd4.aspx) que descreve as diferentes opções de formatação que você pode usar (por exemplo: aa versus aaaa).

> [AZURE.NOTE] Ao usar uma função dentro de outra função, você não precisa usar o prefixo **$$** para a função interna. Por exemplo: $$Text.Format('PartitionKey eq \\'my\_pkey\_filter\_value\\' e RowKey ge \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). Neste exemplo, observe que o prefixo **$$** não é usado para a função **Time.AddHours**.
  

<!---HONumber=AcomDC_0224_2016-->