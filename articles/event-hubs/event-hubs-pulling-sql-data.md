---
title: Efetuar pull de dados do SQL para os Hubs de Eventos do Azure | Microsoft Docs
description: Visão geral da importação dos Hubs de Eventos do exemplo da Web
services: event-hubs
documentationcenter: na
author: spyrossak
manager: timlt
editor: ''

ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: spyros;sethm

---
# Extrair dados do SQL para um Hub de Eventos do Azure
Uma arquitetura típica de um aplicativo para o processamento de dados em tempo real envolve primeiro enviá-la por push para um Hub de Eventos do Azure. Pode ser um cenário IoT, como o monitoramento de tráfego em trechos diferentes de uma autoestrada ou um cenário de jogos, o monitoramento das ações de uma horda de manifestantes enlouquecidos ou um cenário empresarial, como o monitoramento da ocupação de um edifício. Nesses casos, os produtores de dados geralmente são objetos externos que produzem dados de uma série temporal que você precisa coletar, analisar, armazenar e agir. Além disso, talvez você tenha investido muito esforço na criação da infraestrutura para esses processos. O que você fará se quiser trazer dados de algo como um banco de dados em vez de uma origem de dados de streaming, e usá-los em conjunto com os outros dados de streaming? Considere o caso em que você deseja usar o Stream Analytics do Azure, o Gerenciador de Dados Remotos (RDX) ou alguma outra ferramenta para analisar e agir em dados de alteração lenta do Microsoft Dynamics AX ou de um sistema de gerenciamento de instalações personalizadas. Para resolver esse problema, escrevemos e disponibilizamos como software livre uma pequena amostra em nuvem que você pode modificar e implantar e que extrairá os dados de uma tabela SQL e os enviará por push para um Hub de Eventos do Azure para usar como uma entrada em seus aplicativos analíticos downstream. Perceba que esse é um cenário raro, e o contrário do que você normalmente faz com um Hub de Eventos. No entanto, se você estiver na situação em que isso é o que você precisa fazer, localize o código na Galeria de Exemplos do Azure, [aqui](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-import-from-sql/).

Observe que o código deste exemplo é apenas isso, uma amostra. Ele **não** tem a intenção de ser um aplicativo de produção, e nenhuma tentativa foi feita para torná-lo adequado para uso em tal ambiente. Ele é estritamente um exemplo do tipo "faça você mesmo" voltado para desenvolvedores. Você deve analisar todos os tipos de segurança, de desempenho, de funcionalidade e os fatores de custo antes de definir uma arquitetura de ponta a ponta.

## Estrutura de aplicativo
O aplicativo é escrito em C# e o arquivo leiame.md no exemplo contém todas as informações necessárias para modificar, criar e publicar o aplicativo. As seções a seguir fornecem uma visão geral de alto nível do que o aplicativo faz.

Vamos começar com a suposição de que você tenha acesso a tabela SQL do Azure. Você também precisará configurar um Hub de Eventos do Azure e conhecer a cadeia de conexão necessária para acessá-lo.

Quando a solução SqlToEventHub é iniciada, ela lê um arquivo de configuração (App.config) para obter diversas coisas, como descrito no arquivo leiame.md. Eles são bastante auto-explicativos, como o nome da tabela de dados, etc., e não é preciso voltar a apresentá-los aqui.

Depois que o aplicativo tiver lido o arquivo de configuração, ele entrará em um loop, lendo a tabela SQL e enviando os registros por push para o hub de eventos e aguardando um intervalo de espera definido pelo usuário antes de fazer tudo isso novamente. Vale a pena observar alguns pontos:

1. O aplicativo se baseia na suposição de que a tabela SQL esteja sendo atualizada por um processo externo e que você deseja enviar todas as atualizações e apenas elas para um Hub de Eventos.
2. A tabela SQL precisa ter um campo com um número exclusivo e cada vez maior, por exemplo, um número de registro. Isso pode ser tão simples como um campo chamado "Id" ou qualquer outra coisa incrementada, como atualizações em que o banco de dados adiciona registros como "Creation\_time" ou "Sequence\_number". O aplicativo anota e armazena o valor do campo em cada iteração. Em cada passagem subsequente pelo loop, o aplicativo essencialmente consulta a tabela em busca de todos os registros onde o valor do campo excede o valor visto pela última vez no loop. Estamos chamando esse último valor de “deslocamento”.
3. Na inicialização, o aplicativo cria uma tabela "TableOffsets" para armazenar os deslocamentos. A tabela é criada com a consulta "CreateOffsetTableQuery" definida no arquivo de configuração.
4. Há várias consultas usadas para trabalhar com a tabela de deslocamento, definida no arquivo de configuração como "OffsetQuery", "UpdateOffsetQuery" e "InsertOffsetQuery". Você não deve alterá-las.
5. Por fim, a consulta "DataQuery" definida no arquivo de configuração é a consulta que será executada para obter os registros da tabela SQL. Atualmente, ela está limitada a 1.000 registros principais em cada passagem pelo loop para fins de otimização. Se, por exemplo, 25.000 registros tiverem sido adicionados ao banco de dados desde a última consulta, a execução da consulta poderá demorar. Ao limitar a consulta a 1.000 registros por vez, as consultas tornam-se muito mais rápidas. A seleção dos 1.000 registros principais simples envia por push sucessivos lotes de 1.000 registros para o hub de eventos.

## Próximas etapas
Para implantar a solução, clone ou baixe o aplicativo SqlToEventHub, edite o arquivo App.config, compile-o e, por fim, o publique. Após publicar o aplicativo, é possível vê-lo em execução no portal clássico do Azure, em Serviços de Nuvem, e monitorar os eventos que chegam ao hub de eventos. Observe que a frequência será determinada por duas coisas: a frequência das atualizações para a tabela do SQL e o intervalo de suspensão especificado no arquivo de configuração do aplicativo.

<!---HONumber=AcomDC_0831_2016-->