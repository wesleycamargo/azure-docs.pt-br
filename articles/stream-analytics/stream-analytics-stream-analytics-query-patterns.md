<properties
	pageTitle="Exemplos de consulta para padrões de uso comuns do Stream Analytics | Microsoft Azure"
	description="Padrões de consulta comuns do Stream Analytics"
	keywords="exemplos de consulta"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="06/13/2016"
	ms.author="jeffstok"/>


# Exemplos de consulta para padrões de uso do Stream Analytics

## Introdução

As consultas no Stream Analytics do Azure são expressadas em uma linguagem de consulta do tipo SQL, que está documentada na [Referência de Linguagem de Consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx). Este artigo descreve soluções para vários padrões comuns de consulta com base em cenários do mundo real. É um trabalho em andamento e continuará sendo atualizado com novos padrões de forma contínua.

## Exemplo de consulta: conversões de tipo de dados
**Descrição**: defina os tipos das propriedades no fluxo de entrada. Por exemplo, o peso do carro está vindo no fluxo de entrada como cadeias de caracteres e precisa ser convertido em INT para executar a SOMA.

**Entrada**:

| Faça | Hora | Peso |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | "1000" |
| Honda | 2015-01-01T00:00:02.0000000Z | "2000" |

**Saída**:

| Faça | Peso |
| --- | --- |
| Honda | 3000 |

**Solução**:

	SELECT
    	Make,
    	SUM(CAST(Weight AS BIGINT)) AS Weight
	FROM
    	Input TIMESTAMP BY Time
	GROUP BY
		Make,
    	TumblingWindow(second, 10)

**Explicação**: use uma instrução de CONVERSÃO no campo Peso para especificar seu tipo (consulte a lista de Tipos de dados com suporte [aqui](https://msdn.microsoft.com/library/azure/dn835065.aspx)).


## Exemplo de consulta: usando Like/Not like para fazer correspondência de padrões
**Descrição**: verifique se um valor de campo no evento corresponde a um determinado padrão. Por exemplo, retornar placas de carro que comecem com A e terminem com 9

**Entrada**:

| Faça | PlacaDeCarro | Hora |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC-369 | 2015-01-01T00:00:03.0000000Z |

**Saída**:

| Faça | PlacaDeCarro | Hora |
| --- | --- | --- |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC-369 | 2015-01-01T00:00:03.0000000Z |

**Solução**:

	SELECT
    	*
	FROM
    	Input TIMESTAMP BY Time
	WHERE
    	LicensePlate LIKE 'A%9'

**Explicação**: use instrução LIKE para verificar se o valor do campo PlacaDeCarro começa com A e depois tem uma cadeia de zero ou mais caracteres e termina com 9.

## Exemplo de consulta: especifique a lógica para casos/valores diferentes (instruções CASE)
**Descrição**: forneça uma computação diferente para um campo com base em alguns critérios. Por exemplo, forneça uma descrição de cadeia de caracteres para quantos carros da mesma marca passaram com um caso especial para 1.

**Entrada**:

| Faça | Hora |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Saída**:

| CarrosPassaram | Hora |
| --- | --- | --- |
| 1 Honda | 2015-01-01T00:00:10.0000000Z |
| 2 Toyotas | 2015-01-01T00:00:10.0000000Z |

**Solução**:

    SELECT
    	CASE
			WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
			ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
		END AS CarsPassed,
		System.TimeStamp AS Time
	FROM
		Input TIMESTAMP BY Time
	GROUP BY
		Make,
		TumblingWindow(second, 10)

**Explicação**: a cláusula CASE nos permite fornecer uma computação diferente com base em alguns critérios (no nosso caso, a contagem de carros na janela de agregação).

## Exemplo de consulta: enviar dados para várias saídas
**Descrição**: envie dados para vários destinos de saída de um único trabalho. Por exemplo, analise os dados para um alerta de limite e arquive todos os eventos no armazenamento de blob.

**Entrada**:

| Faça | Hora |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Saída1**:

| Faça | Hora |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Saída2**:

| Faça | Hora | Contagem |
| --- | --- | --- |
| Toyota | 2015-01-01T00:00:10.0000000Z | 3 |

**Solução**:

	SELECT
		*
	INTO
		ArchiveOutput
	FROM
		Input TIMESTAMP BY Time

	SELECT
		Make,
		System.TimeStamp AS Time,
		COUNT(*) AS [Count]
	INTO
		AlertOutput
	FROM
		Input TIMESTAMP BY Time
	GROUP BY
		Make,
		TumblingWindow(second, 10)
	HAVING
		[Count] >= 3

**Explicação**: a cláusula INTO informa o Stream Analytics quais das saídas gravar os dados dessa instrução. A primeira consulta é uma passagem dos dados que recebemos para uma saída que chamamos de SaídaDeArquivos. A segunda consulta faz uma agregação e filtragem simples e envia os resultados para um sistema de alerta de downstream. *Observação*: você também pode reutilizar os resultados de CTEs (ou seja, instruções COM). Isso tem o benefício adicional de abrir menos leitores para a fonte de entrada, por exemplo.

	WITH AllRedCars AS (
		SELECT
			*
		FROM
			Input TIMESTAMP BY Time
		WHERE
			Color = 'red'
	)
	SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
	SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## Exemplo de consulta: contagem de valores exclusivos
**Descrição**: conte o número de valores de campo exclusivos que aparecem no fluxo em uma janela de tempo. Por exemplo, quantas marcas exclusivas de carros passaram pelo pedágio em uma segunda janela 2?

**Entrada**:

| Faça | Hora |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Saída:**

| Contagem | Hora |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**Solução:**

	WITH Makes AS (
	    SELECT
	        Make,
	        COUNT(*) AS CountMake
	    FROM
	        Input TIMESTAMP BY Time
	    GROUP BY
	          Make,
	          TumblingWindow(second, 2)
	)
	SELECT
	    COUNT(*) AS Count,
	    System.TimeStamp AS Time
	FROM
	    Makes
	GROUP BY
	    TumblingWindow(second, 1)


**Explicação:** fazemos uma agregação inicial para obter marcas exclusivas com sua contagem pela janela. Em seguida, fazemos uma agregação de quantas marcas temos, considerando todos valores exclusivos em uma janela obtêm o mesmo carimbo de hora e a segunda janela de agregação deve ser mínima para não agregar duas janelas da primeira etapa.

## Exemplo de consulta: determinar se um valor foi alterado#
**Descrição**: examine um valor anterior para determinar se ele é diferente do valor atual. Por exemplo, o carro anterior na estrada com pedágio é da mesma marca que o carro atual?

**Entrada**:

| Faça | Hora |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Saída**:

| Faça | Hora |
| --- | --- |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Solução**:

	SELECT
		Make,
		Time
	FROM
		Input TIMESTAMP BY Time
	WHERE
		LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Explicação**: use LAG para inspecionar um fluxo de entrada do evento anterior e obter o valor Marca. Em seguida, compare-o à Marca no evento atual e retire o evento se eles forem diferentes.

## Exemplo de consulta: localizar o primeiro evento em uma janela
**Descrição**: localizar o primeiro carro em cada intervalo de 10 minutos?

**Entrada**:

| PlacaDeCarro | Faça | Hora |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Saída**:

| PlacaDeCarro | Faça | Hora |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |

**Solução**:

	SELECT 
		LicensePlate,
		Make,
		Time
	FROM 
		Input TIMESTAMP BY Time
	WHERE 
		IsFirst(minute, 10) = 1

Agora vamos alterar o problema e localizar o primeiro carro de determinada Marca em cada intervalo de 10 minutos.

| PlacaDeCarro | Faça | Hora |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Solução**:

	SELECT 
		LicensePlate,
		Make,
		Time
	FROM 
		Input TIMESTAMP BY Time
	WHERE 
		IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## Exemplo de consulta: localizar o último evento em uma janela
**Descrição**: localize o último carro a cada intervalo de 10 minutos.

**Entrada**:

| PlacaDeCarro | Faça | Hora |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Saída**:

| PlacaDeCarro | Faça | Hora |
| --- | --- | --- |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Solução**:

	WITH LastInWindow AS
	(
		SELECT 
			MAX(Time) AS LastEventTime
		FROM 
			Input TIMESTAMP BY Time
		GROUP BY 
			TumblingWindow(minute, 10)
	)
	SELECT 
		Input.LicensePlate,
		Input.Make,
		Input.Time
	FROM
		Input TIMESTAMP BY Time 
		INNER JOIN LastInWindow
		ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
		AND Input.Time = LastInWindow.LastEventTime

**Explicação**: há duas etapas na consulta. A primeira localiza o carimbo de data e hora mais recente em janelas de 10 minutos. A segunda etapa une os resultados da primeira consulta com fluxo original para localizar eventos que correspondem aos carimbos de data e hora mais recentes em cada janela.

## Exemplo de consulta: detectar a ausência de eventos
**Descrição**: verifique se um fluxo não tem um valor que corresponde a um determinado critério. Por exemplo, 2 carros consecutivos da mesma marca passaram pelo pedágio em 90 segundos?

**Entrada**:

| Faça | PlacaDeCarro | Hora |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Honda | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Toyota | DEF-987 | 2015-01-01T00:00:03.0000000Z |
| Honda | GHI-345 | 2015-01-01T00:00:04.0000000Z |

**Saída**:

| Faça | Hora | PlacaDoCarroAtual | PlacaDoPrimeiroCarro | HoraDoPrimeiroCarro |
| --- | --- | --- | --- | --- |
| Honda | 2015-01-01T00:00:02.0000000Z | AAA-999 | ABC-123 | 2015-01-01T00:00:01.0000000Z |

**Solução**:

	SELECT
	    Make,
	    Time,
	    LicensePlate AS CurrentCarLicensePlate,
	    LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
	    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
	FROM
	    Input TIMESTAMP BY Time
	WHERE
	    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**Explicação**: use LAG para inspecionar um fluxo de entrada do evento anterior e obter o valor Marca. Em seguida, compare-o à Marca no evento atual e retire o evento se eles forem iguais e use LAG para obter dados sobre o carro anterior.

## Exemplo de consulta: detectar a duração entre os eventos
**Descrição**: localizar a duração de um evento específico. Por exemplo, após receber os dados de sequência de cliques da web, determinar o tempo gasto em um recurso.

**Entrada**:
  
| Usuário | Recurso | Evento | Hora |
| --- | --- | --- | --- |
| user@location.com | RightMenu | Iniciar | 2015-01-01T00:00:01.0000000Z |
| user@location.com | RightMenu | End | 2015-01-01T00:00:08.0000000Z |
  
**Saída**:
  
| Usuário | Recurso | Duração |
| --- | --- | --- |
| user@location.com | RightMenu | 7 |
  

**Solução**

````
    SELECT
    	[user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
    	Event = 'end'
````

**Explicação**: usar a função LAST para recuperar o último valor temporal, quando o tipo de evento era ‘Start’. Observe que a função LAST usa PARTITION BY [usuário] para indicar que o resultado deverá ser calculado por usuário exclusivo. A consulta tem um limite máximo de uma hora de diferença de tempo entre os eventos ‘Start’ e ‘Stop’, mas é configurável como necessária (LIMIT DURATION(hour, 1).

## Exemplo de consulta: detectar a duração de uma condição
**Descrição**: descubra há quanto tempo uma condição ocorreu. Por exemplo, suponha que um bug que resultou no peso incorreto de todos os carros (acima de 9.000 quilos). Queremos calcular a duração do bug.

**Entrada**:

| Faça | Hora | Peso |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | 2000 |
| Toyota | 2015-01-01T00:00:02.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:03.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:04.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:05.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:06.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:07.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:08.0000000Z | 2000 |

**Saída**:

| FalhaInicial | FalhaFinal |
| --- | --- |
| 2015-01-01T00:00:02.000Z | 2015-01-01T00:00:07.000Z |

**Solução**:

````
	WITH SelectPreviousEvent AS
	(
	SELECT
	*,
		LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
		LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
	FROM input TIMESTAMP BY [time]
	)

	SELECT 
    	LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
    	previousTime [EndFault]
	FROM SelectPreviousEvent
	WHERE
    	[weight] < 20000
	    AND previousWeight > 20000
````

**Explicação**: usar LAG para exibir o fluxo de entrada de 24 horas e procurar por instâncias, nas quais StartFault e StopFault são incluídas por peso < 20000.

## Exemplo de consulta: preencher valores ausentes
**Descrição**: para o fluxo de eventos que têm valores ausentes, produzir um fluxo de eventos com intervalos regulares. Por exemplo, gere eventos a cada 5 segundos que relatam o ponto de dados visto mais recentemente.

**Entrada**:

| t | value |
|--------------------------|-------|
| "2014-01-01T06:01:00" | 1 |
| "2014-01-01T06:01:05" | 2 |
| "2014-01-01T06:01:10" | 3 |
| "2014-01-01T06:01:15" | 4 |
| "2014-01-01T06:01:30" | 5 |
| "2014-01-01T06:01:35" | 6 |

**(10 primeiras linhas) de saída**:

| windowend | lastevent.t | lastevent.value |
|--------------------------|--------------------------|--------|
| 2014-01-01T14:01:00.000Z | 2014-01-01T14:01:00.000Z | 1 |
| 2014-01-01T14:01:05.000Z | 2014-01-01T14:01:05.000Z | 2 |
| 2014-01-01T14:01:10.000Z | 2014-01-01T14:01:10.000Z | 3 |
| 2014-01-01T14:01:15.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:20.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:25.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:30.000Z | 2014-01-01T14:01:30.000Z | 5 |
| 2014-01-01T14:01:35.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:40.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:45.000Z | 2014-01-01T14:01:35.000Z | 6 |

    
**Solução**:

    SELECT
    	System.Timestamp AS windowEnd,
    	TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
    	input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Explicação**: esta consulta gerará ventos a cada 5 segundos e produzirá o último evento recebido antes. A duração da [Janela de salto](https://msdn.microsoft.com/library/dn835041.aspx "Janela de salto - Stream Analytics do Azure") determina o quanto no passado a consulta buscará para localizar o evento mais recente (300 segundos neste exemplo).


## Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=AzureStreamAnalytics)

## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=AcomDC_0615_2016-->