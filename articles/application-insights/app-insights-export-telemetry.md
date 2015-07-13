<properties 
	pageTitle="Exportação contínua de telemetria por meio do Application Insights" 
	description="Exportar dados de uso e diagnóstico para armazenamento no Microsoft Azure e baixá-los de lá." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/17/2015" 
	ms.author="awills"/>
 
# Exportar telemetria do Application Insights

Deseja fazer algumas análises personalizadas em sua telemetria? Ou talvez você gostaria de receber um alerta por email sobre eventos com propriedades específicas? Exportação contínua é ideal para isso. Os eventos que você vê no portal do Application Insights podem ser exportados para armazenamento no Microsoft Azure no formato JSON. Ali, você pode baixar os dados e gravar qualquer código de que você precisa para processá-los.

A Exportação Contínua está disponível no período de avaliação gratuita nos [planos de preços Standard e Premium](http://azure.microsoft.com/pricing/details/application-insights/).

(Se você quiser apenas fazer uma [exportação única](app-insights-metrics-explorer.md#export-to-excel) do que você vê em uma folha de métrica ou pesquisa, clique em Exportar na parte superior da folha.)

## <a name="setup"></a> Configurar a exportação contínua

Na folha Visão geral do aplicativo no portal do Application Insights, abra Exportação contínua:

![Role para baixo e clique em Exportação contínua](./media/app-insights-export-telemetry/01-export.png)

Adicione uma exportação e escolha uma [conta de armazenamento do Azure](../storage-introduction.md) no qual você deseja colocar os dados:

![Clique em Adicionar, Destino de exportação, Conta de armazenamento e, em seguida, crie um novo repositório ou escolha um repositório existente](./media/app-insights-export-telemetry/02-add.png)

Escolha os tipos de eventos que você deseja exportar:

![Clique em Escolher tipos de evento](./media/app-insights-export-telemetry/03-types.png)


Depois de criar sua exportação, ela começa a ser realizada. (Você só obtém os dados que chegam após a criação da exportação.)


Se você quiser alterar os tipos de evento mais tarde, basta editar a exportação:

![Clique em Escolher tipos de evento](./media/app-insights-export-telemetry/05-edit.png)

Para interromper o fluxo, clique em Desabilitar. Quando você clicar em Habilitar novamente, o fluxo será reiniciado com novos dados. Você não obterá os dados recebidos no portal enquanto a exportação estava desabilitada.

Para interromper o fluxo permanentemente, exclua a exportação. Isso não exclui seus dados do armazenamento.

#### Não consegue adicionar nem alterar uma exportação?

* Para adicionar ou alterar exportações, você precisa de direitos de acesso de Proprietário, Colaborador ou Colaborador do Application Insights. [Saiba mais sobre as funções][roles].

## <a name="analyze"></a> Quais eventos você recebe?

Os dados exportados são a telemetria bruta que recebermos do seu aplicativo, exceto:

* Resultados de teste da Web não estão incluídos no momento. 
* Podemos adicionar dados de localização que calculamos por meio do endereço IP do cliente.  

Métricas calculadas não são incluídas. Por exemplo, nós não exportamos a utilização média de CPU, mas exportamos a telemetria bruta por meio da qual a média é computada.

## <a name="get"></a> Inspecionar os dados

Ao abrir o repositório de blob com uma ferramenta como o [Gerenciador de Servidores](http://msdn.microsoft.com/library/azure/ff683677.aspx), você verá um contêiner com um conjunto de arquivos de blob. O URI de cada arquivo é id-do-aplicativo/tipo-de-telemetria/data/hora.

![Inspecione o repositório de blob com uma ferramenta adequada](./media/app-insights-export-telemetry/04-data.png)

A data e hora são em formato UTC, e referentes a quando a telemetria foi depositada no repositório - não à hora em que essa telemetria foi gerada. Então, se você escrever código para baixar os dados, ele pode percorrer os dados linearmente.



## <a name="format"></a> Formato dos dados

* Cada blob é um arquivo de texto que contém várias linhas separadas por “ \n”.
* Cada linha é um documento JSON não formatado. Se você quiser ficar sentado e olhando para ele, tente usar um visualizador como o Bloco de notas++ com o plug-in JSON:

![Veja a telemetria com uma ferramenta adequada](./media/app-insights-export-telemetry/06-json.png)

As durações de tempo são em tiques, em que 10.000 tiques = 1 ms. Por exemplo, esses valores mostram um tempo de 10 ms para enviar uma solicitação do navegador, 30 ms recebê-la e 1,8 s para processar a página no navegador:

	"sendRequest": {"value": 10000.0},
	"receiveRequest": {"value": 30000.0},
	"clientProcess": {"value": 17970000.0}



## Processamento dos dados

Em pequena escala, você pode escrever um código para extrair e separar seus dados, lê-los em uma planilha e assim por diante. Por exemplo:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

Para obter um exemplo de código maior, consulte [usando uma função de trabalho][exportasa].

#### Exportar para SQL

Outra opção é mover os dados para um banco de dados SQL, no qual você pode executar análises mais potentes.

Temos exemplos mostrando dois métodos alternativos de mover os dados do armazenamento de blob para um banco de dados:

* [Exportar para SQL usando uma função de trabalho][exportcode]
* [Exportar para o SQL usando o Stream Analytics][exportasa]


Em escalas maiores, considere usar o [HDInsight](http://azure.microsoft.com/services/hdinsight/) - clusters de Hadoop na nuvem. O HDInsight fornece uma variedade de tecnologias para gerenciar e analisar grandes volumes de dados.

## <a name="delete"></a>Excluir dados antigos
Observe que você é responsável por gerenciar a capacidade de armazenamento e excluir dados antigos, se necessário.

## Se você regenerar sua chave de armazenamento...

Se você alterar a chave para seu armazenamento, a exportação contínua deixará de funcionar. Você verá uma notificação em sua conta do Azure.

Abrir a folha Exportação Contínua e edite sua exportação. Edite o destino de exportação, mas mantenha o mesmo armazenamento selecionado. Clique em OK para confirmar.

![Edite a exportação contínua, abra e feche o destino de exportação.](./media/app-insights-export-telemetry/07-resetstore.png)

A exportação contínua será reiniciada.


## Perguntas e respostas

* *Mas tudo o que eu quero é um download único de um gráfico.*  
 
    Sim, você pode fazer isso. Na parte superior da folha, clique [Exportar dados](app-insights-metrics-explorer.md#export-to-excel).

* *Eu configuro uma exportação, mas não há nenhum dado no meu repositório.*

    O Application Insights recebeu qualquer telemetria do seu aplicativo desde que você configurou a exportação? Você receberá apenas novos dados.

* *Eu tentei configurar uma exportação, mas o acesso foi negado*

    Se a conta pertence à sua organização, você precisa ser membro do grupo de proprietários ou do grupo de colaboradores.

    <!-- Your account has to be either a paid-for account, or in the free trial period. -->

* *Eu posso exportar diretamente para meu próprio repositório local?*

    Não, infelizmente. Nosso mecanismo de exportação funciona apenas com o armazenamento do Azure no momento.

* *Há qualquer limite para a quantidade de dados que você coloca em meu repositório?*

    Não. Continuaremos a enviar dados por push até que você exclua a exportação. Interromperemos o envio se atingirmos os limites externos para o armazenamento de blob, mas são limites enormes. Cabe a você controlar a quantidade de armazenamento que usa.

* *Eu regenerei a chave para o meu armazenamento ou alterei o nome do contêiner, e agora a exportação não funciona.*

    Edite a exportação e abra a folha Destino de exportação. Deixe o mesmo armazenamento de antes selecionado e clique em OK para confirmar. A exportação será reiniciada. Se a alteração foi realizada nos últimos dias, você não perderá dados.

* *Posso pausar a exportação?*

    Sim. Clique em Desabilitar.


<!--Link references-->

[exportcode]: app-insights-code-sample-export-telemetry-sql-database.md
[exportasa]: app-insights-code-sample-export-sql-stream-analytics.md
[roles]: app-insights-resources-roles-access-control.md

 

<!---HONumber=62-->