<properties 
	pageTitle="Criar uma solução IOT usando o Stream Analytics | Microsoft Azure" 
	description="introdução ao tutorial para a solução de iot do Stream Analytics de um cenário de pedágio"
	keywords="solução de iot, funções da janela"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="jhubbard" 
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="08/11/2016" 
	ms.author="jeffstok"
/>

# Compilar uma solução IOT usando o Stream Analytics

## Introdução

Neste tutorial, você aprenderá como obter informações de dados em tempo real usando o Stream Analytics do Azure. O serviço de processamento de transmissão do Azure permite que os desenvolvedores lidem facilmente com o espaço de dados em movimento ao combinar os fluxos de dados, como fluxos de clique, logs e eventos gerados por dispositivo com registros históricos ou dados de referência para obter ideias de negócios de forma rápida e fácil. Sendo um serviço de computação de transmissão em tempo real, totalmente gerenciado e hospedado no Microsoft Azure, o Stream Analytics do Azure fornece resiliência interna, baixa latência e escalabilidade para você colocar tudo em funcionamento em minutos.

Depois de concluir este tutorial, você poderá:

-   Familiarize-se com o Portal do Stream Analytics do Azure.
-   Configurar e implantar um trabalho de transmissão.
-   Articular os problemas do mundo real e resolvê-los usando a linguagem de consulta do Stream Analytics.
-   Desenvolver soluções de transmissão para os clientes usando o Stream Analytics do Azure com confiança.
-   Use o monitoramento e experiência de log para solucionar problemas.

## Pré-requisitos

Você terá os seguintes pré-requisitos para concluir este tutorial com êxito.

-   [Azure PowerShell](../powershell-install-configure.md) mais recente
-   O Visual Studio 2015 ou o [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) gratuito
-   [Assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Privilégios administrativos no computador
-   Baixar [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) do Centro de Download da Microsoft
-   Opcional: código-fonte do gerador de evento TollApp no [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## Introdução ao cenário - “Olá, pedágio!”


A estação de pedágio é um fenômeno comum– ela é encontrada em várias estradas, pontes e túneis em todo o mundo. Cada estação de pedágio tem várias cabines de pedágios, que podem ser manuais – o que significa que você para com o intuito de pagar o pedágio para um atendente, ou automatizadas – nas quais um sensor colocado na parte superior da cabine examina um cartão RFID afixado no para-brisa do seu veículo conforme você passa pela cabine de pedágio. É fácil visualizar a passagem dos veículos por essas estações de pedágio como uma transmissão de eventos sobre quais operações interessantes podem ser executadas.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## Dados de entrada

Trabalharemos com dois fluxos de dados que são produzidos por sensores instalados na entrada e na saída das estações de pedágio e um conjunto de dados de pesquisa estático com os dados de registro do veículo.

### Transmissão de dados de entrada

A transmissão de dados de entrada contém informações sobre carros que estão entrando nas estações de pedágio.


| ID de pedágio | EntryTime | PlacaDeCarro | Estado | Faça | Modelo | Tipo de veículo | Peso do veículo | Pedágio | Marca |
|---------|-------------------------|--------------|-------|--------|---------|--------------|----------------|------|-----------|
| 1 | 2014-09-10 12:01:00.000 | JNB 7001 | NOVA IORQUE | Honda | CRV | 1 | 0 | 7 | |
| 1 | 2014-09-10 12:02:00.000 | 1001 YXZ | NOVA IORQUE | Toyota | Camry | 1 | 0 | 4 | 123456789 |
| 3 | 2014-09-10 12:02:00.000 | ABC 1004 | CT | Ford | Taurus | 1 | 0 | 5 | 456789123 |
| 2 | 2014-09-10 12:03:00.000 | 1003 XYZ | CT | Toyota | Corolla | 1 | 0 | 4 | |
| 1 | 2014-09-10 12:03:00.000 | 1007 BNJ | NOVA IORQUE | Honda | CRV | 1 | 0 | 5 | 789123456 |
| 2 | 2014-09-10 12:05:00.000 | CDE 1007 | NJ | Toyota | 4x4 | 1 | 0 | 6 | 321987654 |


Aqui está uma breve descrição das colunas:


| TollID | A ID da cabine de pedágio identifica exclusivamente uma cabine de pedágio |
|--------------|----------------------------------------------------------------|
| EntryTime | A data e hora da entrada do veículo na cabine de pedágio em UTC |
| PlacaDeCarro | Número da placa de licença do veículo |
| Estado | É um estado nos Estados Unidos |
| Faça | O fabricante do automóvel |
| Modelo | Número do modelo do automóvel |
| VehicleType | 1 para passageiro e 2 para veículos comerciais |
| WeightType | Peso do veículo em toneladas; 0 para veículos de passageiros |
| Pedágio | Valor do pedágio em dólares americanos |
| Marca | e-Tag no automóvel que automatiza o pagamento deixado em branco onde o pagamento foi feito manualmente |


### Transmissão de dados de saída

A transmissão de dados de saída contém informações sobre os carros que estão saindo da estação de pedágio.


| **TollId** | **ExitTime** | **PlacaDeCarro** |
|------------|------------------------------|------------------|
| 1 | 2014-09-10T12:03:00.0000000Z | JNB 7001 |
| 1 | 2014-09-10T12:03:00.0000000Z | 1001 YXZ |
| 3 | 2014-09-10T12:04:00.0000000Z | ABC 1004 |
| 2 | 2014-09-10T12:07:00.0000000Z | 1003 XYZ |
| 1 | 2014-09-10T12:08:00.0000000Z | 1007 BNJ |
| 2 | 2014-09-10T12:07:00.0000000Z | CDE 1007 |

Aqui está uma breve descrição das colunas:


| Coluna | Descrição |
|--------------|-----------------------------------------------------------------|
| TollID | A ID da cabine de pedágio identifica exclusivamente uma cabine de pedágio |
| ExitTime | A data e hora de saída do veículo no pedágio em UTC |
| PlacaDeCarro | Número da placa de licença do veículo |

### Dados de registro de veículo comercial

Usaremos um instantâneo estático do banco de dados de registro de veículo comercial.


| PlacaDeCarro | RegistrationId | Expirado |
|--------------|----------------|---------|
| SVT 6023 | 285429838 | 1 |
| XLZ 3463 | 362715656 | 0 |
| BAC 1005 | 876133137 | 1 |
| RIV 8632 | 992711956 | 0 |
| SNY 7188 | 592133890 | 0 |
| ELH 9896 | 678427724 | 1 |

Aqui está uma breve descrição das colunas:


| Coluna | Descrição |
|--------------|-----------------------------------------------------------------|
| PlacaDeCarro | Número da placa de licença do veículo |
| RegistrationId | RegistrationId |
| Expirado | 0 se o registro de veículo estiver ativo, 1 se o registro estiver vencido |


## Configurando o ambiente para o Stream Analytics do Azure

Para executar este tutorial, você precisará de uma assinatura do Microsoft Azure. A Microsoft oferece uma avaliação gratuita de serviços do Microsoft Azure conforme descrito abaixo.

Se você não tem uma conta do Azure, solicite uma versão de avaliação gratuita acessando <http://azure.microsoft.com/pricing/free-trial/>.

Observação: para se inscrever para uma avaliação gratuita, será necessário um dispositivo móvel que pode receber mensagens de texto e um cartão de crédito válido.

Certifique-se de seguir as etapas da seção “Limpar sua conta do Azure” no final deste exercício para que você possa fazer o melhor uso de seu crédito Azure gratuito de \\$200.

## Provisionamento de recursos do Azure necessário para o Tutorial

Este tutorial requer dois Hubs de Eventos do Azure para receber fluxos de dados “Entry” e “Exit”. Nós usaremos o banco de dados SQL para saída dos resultados dos trabalhos do Stream Analytics. Também usaremos o armazenamento do Azure para armazenar dados de referência sobre o registro do veículo.

O script Setup.ps1 na pasta TollApp no GitHub pode ser usado para criar todos os recursos necessários. Devido ao tempo, recomendamos que você execute-o. Se quiser saber mais sobre como configurar esses recursos no portal do Azure, veja o apêndice “Configurando recursos de tutorial no Portal do Azure”

Baixe e salve os arquivos e pastas [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) de suporte.

Abra uma janela do “Microsoft Azure PowerShell” **COMO ADMINISTRADOR**. Se você ainda não tem o Azure PowerShell, siga as instruções para instalá-lo: [Instalar e configurar o Azure PowerShell](../powershell-install-configure.md)

O Windows bloqueia automaticamente os arquivos ps1, dll e exe baixados da Internet. É necessário definir a Política de Execução antes de executar o script. Verifique se a janela do Azure PowerShell está executando como um administrador. Execute “Set-ExecutionPolicy unrestricted”. Quando solicitado, digite "Y".

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Execute Get-ExecutionPolicy para se certificar de que o comando funcionou.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Acesse o diretório com os scripts e o aplicativo gerador.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Digite “.\\Setup.ps1” para configurar sua conta do azure, criar e configurar todos os recursos necessários e iniciar a geração de eventos. O script selecionará aleatoriamente uma região para criar seus recursos. Se você quiser especificar a região de forma explícita, você pode passar o parâmetro -location como o exemplo a seguir:

**.\\Setup.ps1 -location “Central US”**

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

O script abrirá a página “Entrar” no Microsoft Azure. Insira suas credenciais de conta.

Observe que, se sua conta tiver acesso a várias assinaturas, será solicitado que você insira o nome da assinatura que deseja usar para o tutorial.

O script pode levar vários minutos para ser executado. Depois de concluída, a saída deve parecer com a captura de tela abaixo.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Você também verá outra janela semelhante à captura de tela abaixo. Este aplicativo está enviando eventos ao seu Hub de Eventos e é necessário para executar os exercícios do tutorial. Portanto, você não deve interromper o aplicativo ou fechar esta janela até concluir o tutorial.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Você deve ser capaz de ver todos os recursos criados no Portal de Gerenciamento agora. Acesse <https://manage.windowsazure.com> e faça logon com suas credenciais de conta.

### Hubs de Eventos

Clique no item de menu “Barramento de Serviço” no lado esquerdo do Portal de Gerenciamento do Azure para ver os Hubs de Eventos criados pelo script na seção anterior.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Você verá todos os namespaces disponíveis na sua assinatura. Clique no que começa com "TollData". (TollData4637388511 em nosso exemplo). Clique na guia “Hubs de Eventos”.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

Você verá dois Hubs de Eventos chamados *entry* e *exit* criados nesse namespace.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### Contêiner de armazenamento do Azure

Clique no item de menu “Armazenamento” no lado esquerdo do Portal de Gerenciamento do Azure para ver o contêiner de armazenamento usado no laboratório.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

Clique em um começando com “tolldata”. (tolldata4637388511 em nosso exemplo). Abra a guia “Contêineres” para ver o contêiner criado.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

Clique no contêiner “tolldata” para ver o arquivo JSON carregado com os dados de registro do veículo.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### Banco de Dados SQL Azure

Clique no item de menu “bancos de dados SQL” no lado esquerdo do Portal de Gerenciamento do Azure para ver o banco de dados SQL que será usado no laboratório.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

Clique em “TollDataDB”

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

Copie o nome do servidor sem o número da porta (*servername*.database.windows.net, por exemplo)

## Conecte-se ao banco de dados do Visual Studio

Usaremos o Visual Studio para acessar os resultados da consulta no banco de dados de saída.

Conecte-se ao banco de dados (o destino) do Azure do Visual Studio:

1) Abra o Visual Studio e clique em “Ferramentas” e, em seguida, no item de menu “Conectar-se ao banco de dados...”.

2) Se for solicitado, selecione “Microsoft SQL Server” como uma fonte de dados

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3) No campo Nome do Servidor, cole o nome do SQL Server copiado na seção anterior do Portal do Azure (ou seja, *servername*.database.windows.net)

4) No Campo de Autenticação, selecione Autenticação do SQL Server

5) Digite o NOME DE LOGON como “tolladmin” e a SENHA DE LOGON como “123toll!”

6) Escolha TollDataDB como o banco de dados

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)

7) Clique em OK.

8) Abra o Gerenciador de Servidores

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)

9) Confira quatro tabelas criadas no banco de dados TollDataDB.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## Gerador de evento - projeto de exemplo TollApp

O script do PowerShell inicia automaticamente o envio de eventos usando o programa TollApp do aplicativo de exemplo. Você não precisa executar nenhuma etapa adicional.

No entanto, se estiver interessado nos detalhes da implementação, você encontrará o código-fonte do aplicativo TollApp no GitHub [samples/TollApp](https://aka.ms/azure-stream-analytics-toll-source)

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

##Criar um trabalho do Stream Analytics

No Portal do Azure, abra o Stream Analytics e clique em “Novo” no canto inferior esquerdo da página para criar um novo trabalho de análise.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

Clique em “Criação rápida”. Selecione a mesma região onde os outros recursos são criados pelo script.

Para a configuração de “Conta de Armazenamento de Monitoramento Regional”, selecione “Criar Nova Conta de Armazenamento” e dê um nome exclusivo a ela. O Stream Analytics do Azure usará essa conta para armazenar informações de monitoramento para todos os trabalhos futuros.

Clique em "Criar trabalho do Stream Analytics" na parte inferior da página.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## Definir fontes de entrada

Clique no trabalho de análise criado no portal.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

Abra a guia “Entradas” para definir os dados de origem.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

Clique em "Adicionar uma entrada"

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

Selecione “Transmissão de dados” na primeira página

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

Selecione “Hub de evento” na segunda página do assistente.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

Digite “EntryStream” como Alias de entrada.

Clique no menu suspenso “Hub de Eventos” e selecione um que comece com “TollData” (por exemplo, TollData9518658221).

Selecione “entry” como nome do Hub de evento e “all” como nome da política do Hub de evento.

As configurações ficarão semelhantes:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

Mover para a próxima página. Selecione os valores como JSON, codificação UTF8.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

Clique em OK na parte inferior da caixa de diálogo para concluir o assistente.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

Você precisará seguir a mesma sequência de etapas para criar a segunda entrada do Hub de Eventos para a transmissão com eventos de Saída. Certifique-se de inserir valores na terceira página conforme são mostrados a captura de tela abaixo.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

Agora você tem dois fluxos de entrada definidos:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

Em seguida, adicionaremos a entrada de dados “Referência” para o arquivo de blob com os dados de registro do carro.

Clique em "Adicionar entrada". Selecione "Dados de referência"

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

Na página seguinte, selecione a conta de armazenamento que comece com “tolldata”. O nome do contêiner deve ser “tolldata” e o nome do blob no caminho padrão deve ser “registration.json”. Este nome de arquivo diferencia maiúsculas de minúsculas e deve estar em minúsculas.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

Selecione os valores conforme mostrado abaixo na próxima página e clique em OK para concluir o assistente.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

Agora, todas as entradas são definidas.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## Definir saída

Acesse a guia “Saída” e clique em “Adicionar uma saída”.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

Escolha "banco de dados SQL".

Selecione o nome do servidor que foi usado em “Conectar-se ao banco de dados do Visual Studio”. O nome do banco de dados deve ser TollDataDB.

Digite “tolladmin” como o nome de usuário e “123toll!” como a senha. O nome da tabela deve ser definido como “TollDataRefJoin”.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## Consulta do Stream Analytics do Azure

A guia Consulta contém uma consulta de SQL que executa a transformação dos dados de entrada.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

Através deste tutorial, tentaremos responder várias perguntas de negócios relacionadas aos dados de Pedágio e criar consultas do Stream Analytics que possam ser usadas no Stream Analytics do Azure para fornecer uma resposta relevante.

Antes de começarmos o nosso primeiro trabalho do Stream Analytics do Azure, vamos explorar alguns cenários e sintaxe de consulta.

## Introdução à linguagem de consulta do Stream Analtyics do Azure
-----------------------------------------------------

Digamos que precisamos contar o número de veículos que entram em uma cabine pedágio. Como essa é uma transmissão contínua de eventos, é essencial, definirmos um “período de tempo”. Portanto, precisamos modificar nosso pergunta para “Número de veículos que entram em uma cabine de pedágio a cada três minutos”. Isso é conhecido como a contagem em cascata.

Vamos analisar a consulta do Stream Analytics do Azure ao responder essa pergunta:

SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count FROM EntryStream TIMESTAMP BY EntryTime GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Como você pode ver, o Stream Analytics do Azure está usando uma linguagem de consulta do tipo SQL com algumas extensões adicionais para habilitar, especificando os aspectos relacionados ao tempo da consulta.

Para obter mais detalhes, você pode ler sobre [Gerenciamento de tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e construções de [Janelas](https://msdn.microsoft.com/library/azure/dn835019.aspx) usadas na consulta do MSDN.

## Testando consultas do Stream Analytics do Azure

Agora que poderíamos ter escrito nossa primeira consulta do Stream Analytics do Azure, é hora de testá-la usando os arquivos de dados de exemplo localizados na pasta TollApp no caminho abaixo:

**.. \\TollApp\\TollApp\\Data**

Esta pasta contém os seguintes arquivos:

1.  Entry.json

2.  Exit.JSON

3.  Registration.json

## Pergunta 1 - Número de veículos que estão entrando em uma cabine de pedágio

Abra o Portal de Gerenciamento do Azure e navegue até o trabalho criado do Stream Analytics do Azure. Abra a guia de consulta e copie e cole a consulta da seção anterior.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

Para validar essa consulta em relação aos dados de exemplo, clique no botão Testar. Na caixa de diálogo que é aberta, navegue até Entry.json, um arquivo com dados de amostra da transmissão de eventos EntryTime.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

Valide que a saída da consulta é conforme o esperado:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## Pergunta 2 - Relatar o tempo total para cada carro passar pela cabine de pedágio

Queremos localizar o tempo médio necessário para o carro passar pelo pedágio para avaliar a eficiência e a experiência do cliente.

Para isso, precisamos associar a transmissão contendo EntryTime com a transmissão contendo ExitTime. Uniremos os fluxos nas colunas TollId e LicencePlate. O operador JOIN exige a especificação de um espaço de manobra temporal descrevendo a diferença de tempo aceitável entre os eventos associados. Usaremos a função DATEDIFF para especificar que os eventos não devem ter um intervalo maior de 15 minutos entre si. Também aplicaremos a função DATEDIFF nas horas de Entrada e Saída para computar o tempo real que um carro gasta em um pedágio. Observe a diferença do uso da DATEDIFF quando usada em uma instrução SELECT em comparação com uma condição JOIN.

SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes FROM EntryStream TIMESTAMP BY EntryTime JOIN ExitStream TIMESTAMP BY ExitTime ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate) AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

Para testar essa consulta, atualize a consulta na guia de consulta do trabalho:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

Clique em teste e especifique os arquivos de entrada de exemplo para EntryTime e ExitTime.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

Clique na caixa de seleção para testar a consulta e exibir a saída:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## Pergunta 3 – Relatar todos os veículos comerciais com o registro expirado

O Stream Analytics do Azure pode usar instantâneos estáticos de dados para se associar com os fluxos de dados temporais. Para demonstrar essa funcionalidade, usaremos a pergunta de exemplo a seguir.

Se um veículo comercial é registrado com a Empresa de Pedágio, ele pode passar pelo pedágio sem ser parado para inspeção. Usaremos a tabela de pesquisa de Registro de Veículo Comercial para identificar todos os veículos comerciais com o registro expirado.

SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId FROM EntryStream TIMESTAMP BY EntryTime JOIN Registration ON EntryStream.LicensePlate = Registration.LicensePlate WHERE Registration.Expired = '1'

Observe que testar uma consulta com Dados de Referência requer que uma fonte de entrada para os Dados de Referência seja definida, o que fizemos na etapa 5.

Para testar essa consulta, cole a consulta na guia Consulta, clique em Testar e especifique as fontes da entrada 2:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

Exibir a saída da consulta:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## Iniciar o trabalho do Stream Analytics


Agora, já que gravamos nossa primeira consulta do Stream Analytics do Azure, é hora de finalizar a configuração e iniciar o trabalho. Salve a consulta da Pergunta 3, que produzirá a saída correspondente ao esquema da nossa tabela de saída **TollDataRefJoin**.

Navegue até o Painel de Trabalho e clique em Iniciar.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

Na caixa de diálogo que aparece, altere a hora de Iniciar Saída para Personalizar Saída. Edite a hora e configure-a como uma hora para trás. Isso garantirá que podemos processar todos os eventos do Hub de Eventos desde o momento em que começamos a gerar eventos no início do tutorial. Agora, clique na marca de seleção para iniciar o trabalho.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

O início do trabalho pode levar alguns minutos. Você poderá ver o status na página de nível superior para o Stream Analytics.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## Verificar resultados no Visual Studio

Abra o Gerenciador de Servidores do Visual Studio e clique com o botão direito do mouse na tabela TollDataRefJoin. Selecione “Mostrar dados da tabela” para ver a saída do seu trabalho.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## Colocação em escala dos trabalhos do Stream Analytics do Azure

O Stream Analytics do Azure foi projetado para dimensionar elasticidade e ser capaz de lidar com a alta carga de dados. A consulta do Stream Analytics do Azure pode usar uma cláusula **PARTITION BY** para informar ao sistema que esta etapa será escalável horizontalmente. PartitionId é uma coluna especial adicionada pelo sistema que corresponde à ID da partição de entrada (Hub de Eventos)

SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

Interromper o trabalho atual, atualizar a consulta no guia de consulta e abrir a guia Escala.

as unidades de streaming definem a quantidade de capacidade de computação que pode receber o trabalho.

Mova o controle deslizante para 6.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

Acesse a guia de saída e altere o nome da tabela SQL para "TollDataTumblingCountPartitioned"

Agora, se você iniciar o trabalho, o Stream Analytics do Azure será capaz de distribuir o trabalho entre mais recursos de computação e obter uma taxa de transferência melhor. Observe que o aplicativo TollApp também está enviando eventos particionados pelo TollId.

## Monitoramento

A guia de monitoramento contém estatísticas sobre o trabalho em execução.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

Você pode acessar os Logs de operação na guia Painel.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

Para obter informações adicionais sobre um evento específico, selecione o evento e clique no botão “Detalhes”.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## Conclusão

Neste tutorial, nós fornecemos uma introdução ao serviço Stream Analytics do Azure. Demonstramos como configurar entradas e saídas para o trabalho do Stream Analytics. Ao usarmos o cenário Dados de Pedágio, explicamos os tipos comuns de problemas que podem surgir no espaço de dados em movimento e como eles podem ser resolvidos com consultas simples do tipo SQL no Stream Analytics do Azure. Descrevemos construções de extensão do SQL para trabalhar com dados temporais. Mostramos como combinar transmissões de dados, como aprimorar a transmissão de dados com dados de referência estáticos. Explicamos como escalar horizontalmente uma consulta para obter uma taxa de transferência maior.

Embora este tutorial forneça uma boa cobertura introdutória, ele não é, de forma alguma, completo. Você pode encontrar mais Padrões de Consulta usando a linguagem SAQL [aqui](stream-analytics-stream-analytics-query-patterns.md). Consulte a [documentação online](https://azure.microsoft.com/documentation/services/stream-analytics/) para saber mais sobre o Azure Stream Analytics.

## Limpe sua conta do Azure

Interrompa o trabalho do Stream Analytics do Portal do Azure.

O script Setup.ps1 cria dois Hubs de Eventos do Azure e o servidor de banco de dados SQL do Azure. As instruções a seguir ajudarão você a limpar recursos no final do tutorial.

Na janela PowerShell, digite “.\\Cleanup.ps1” Isso iniciará o script que exclui os recursos usados no tutorial.

Observe que os recursos são identificados pelo nome. Certifique-se de examinar cuidadosamente cada item antes de confirmar a remoção.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)

<!---HONumber=AcomDC_0914_2016-->