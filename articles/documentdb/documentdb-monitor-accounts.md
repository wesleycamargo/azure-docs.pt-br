<properties
	pageTitle="Monitorar uma conta do Banco de Dados de Documentos usando o Portal do Azure | Microsoft Azure"
	description="Saiba como monitorar sua conta do Banco de Dados de Documentos para métricas de desempenho, como solicitações e erros de servidor, e as métricas de uso, como o consumo de armazenamento."
	services="documentdb"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2016"
	ms.author="mimig"/>

# Monitorar um uso de conta do Banco de Dados de Documentos no Portal do Azure

Você pode monitorar suas contas do Banco de Dados de Documentos no [Portal do Microsoft Azure](https://portal.azure.com/). Para cada conta do Banco de Dados de Documentos, tanto as métricas de desempenho (como as solicitações e erros de servidor) quanto as métricas de utilização (como consumo de armazenamento) estão disponíveis.

## Visualizar métricas de desempenho para uma conta do Banco de Dados de Documentos
1.	No [Portal do Azure](https://portal.azure.com/), clique em **Procurar**, **Contas do Banco de Dados de Documentos** e clique no nome da conta do Banco de Dados de Documentos cujas métricas de desempenho você deseja exibir.
2.	Dentro da lente **Monitoramento**, você pode, por padrão, consultar:
	*	Solicitações totais para o dia de hoje.
	*	Utilização de armazenamento da conta.

	Se sua tabela exibe **Não há dados disponíveis** consulte a seção [Solução de Problemas](#troubleshooting).

	![Captura de tela da Lente de monitoramento que mostra o total de solicitações de hoje e o uso de armazenamento](./media/documentdb-monitor-accounts/documentdb-total-requests-and-usage.png)


3.	Clicar no bloco **Total de Solicitações** ou **Armazenamento** abre uma folha de **Métrica** detalhada.
4.	A folha **Métrica** mostra detalhes das métricas que você selecionou. Na parte superior da folha há um gráfico e abaixo dele há uma tabela que mostra os valores de agregação das métricas selecionadas como a média, o mínimo, o máximo e o total. A folha Métrica também exibe a lista de alertas que foram definidos, filtrados para as métricas que aparecem na folha de métricas atual (desse modo, se você possui um determinado número de alertas, você verá apenas aqueles relevantes apresentados aqui).   

	![Captura de tela da lâmina Métrica](./media/documentdb-monitor-accounts/documentdb-metric-blade.png)


## Personalizar visualizações de métricas de desempenho para uma conta do Banco de Dados de Documentos

1.	Para personalizar as métricas exibidas em uma parte específica, clique com o botão direito no gráfico de métricas e selecione **Editar gráfico**. ![Captura de tela do gráfico Total de Solicitações com o botão Editar gráfico realçado](./media/documentdb-monitor-accounts/madocdb3.png)

2.	Na folha **Editar Gráfico**, há opções para modificar as métricas que são exibidas nessa parte, além de seu intervalo de tempo. ![Captura de tela da folha Editar gráfico](./media/documentdb-monitor-accounts/madocdb4.png)

3.	Para alterar as métricas que são exibidas na parte, basta marcar ou desmarcar as métricas de desempenho disponíveis e clicar em **Salvar** na parte inferior da folha.
4.	Para alterar o intervalo de tempo, escolha um intervalo diferente (por exemplo, **Personalizado**) e clique em **Salvar** na parte inferior da folha.  

	![Captura de tela da parte do Intervalo de Tempo da folha Editar gráfico mostrando como inserir um intervalo de tempo personalizado](./media/documentdb-monitor-accounts/madocdb5.png)


## Criar gráficos de métricas de desempenho lado a lado
O Portal do Azure permite que você crie gráficos de métricas lado a lado.

1.	Primeiro, clique com o botão direito no gráfico que você deseja clonar e modificar e selecione **Personalizar**.

	![Captura de tela do gráfico Total de Solicitações com a opção Personalizar realçada](./media/documentdb-monitor-accounts/madocdb6.png)

2.	Clique em **Clonar** no menu para copiar a parte e clique em **Personalização concluída**.

	![Captura de tela do gráfico Total de Solicitações com as opções de personalização Clonar e Concluído realçadas](./media/documentdb-monitor-accounts/madocdb7.png)


Agora, você pode tratar essa parte como qualquer outra parte de métrica, personalizando as métricas e o intervalo de tempo exibidos na parte. Fazendo isso, você pode ver um gráfico com duas métricas diferentes lado a lado simultaneamente. ![Captura de tela do gráfico Total de Solicitações e o novo gráfico Total de solicitações na última hora](./media/documentdb-monitor-accounts/madocdb8.png)

## Defina alertas de métrica de desempenho para uma conta do Banco de Dados de Documentos
1.	No [Portal do Azure](https://portal.azure.com/), clique em **Procurar Tudo**, **Contas do Banco de Dados de Documentos** e clique no nome da conta do Banco de Dados de Documentos para a qual deseja definir alertas de métricas de desempenho.

2.	Se a folha **Todas as Configurações** não estiver aberta, clique no comando **Configurações** na parte superior esquerda para abrir a folha. ![Captura de tela do comando Configurações na folha Conta de Banco de Dados](./media/documentdb-monitor-accounts/madocdb10.png)

3.	Na folha **Todas as Configurações**, clique em **Regras de Alerta** para abrir a folha de Regras de alerta. ![Captura de tela da parte de Regras de alerta selecionada](./media/documentdb-monitor-accounts/madocdb10.5.png)

4.	Na folha das Regras de alerta, clique em **Adicionar Alerta**. ![Captura de tela da folha Regras de Alerta, com o botão Adicionar alerta realçado](./media/documentdb-monitor-accounts/madocdb11.png)

5.	Na lâmina **Adicionar uma regra de alerta**, especifique:
	*	O nome da regra de alerta que você está configurando.
	*	Uma descrição da nova regra de alerta.
	*	A métrica para a regra de alerta.
	*	A condição, o limite e o período que determinam quando o alerta é ativado. Por exemplo, uma contagem de erros de servidor superior a cinco nos últimos 15 minutos.
	*	Se o administrador de serviços e seus coadministradores recebem ou não emails quando o alerta é enviado.
	*	Endereços de email adicionais para notificações de alerta. ![Captura de tela da folha da regra Adicionar um alerta](./media/documentdb-monitor-accounts/madocdb12.png)

## Outras maneiras de monitorar o Banco de Dados de Documentos
As métricas no nível da conta disponíveis no portal, como o uso de armazenamento da conta e o total de solicitações, não estão disponíveis por meio das APIs do Banco de Dados de Documentos. No entanto, você pode recuperar os dados de uso no nível da coleção. Para recuperar os dados do nível de coleção, faça o seguinte:

- Para usar a API REST, [execute um GET na coleção](https://msdn.microsoft.com/library/mt489073.aspx). As informações de cota e de uso da coleção são retornadas nos cabeçalhos x-ms-resource-quota e x-ms-resource-usage na resposta.
- Para usar o SDK do .NET, use o método [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx), que retorna uma [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) que contém diversas propriedades de uso como **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** e muito mais.

## Solucionar problemas
Se seus blocos de monitoramento exibirem a mensagem **Não há dados disponíveis** e você enviou solicitações ou adicionou dados ao banco de dados recentemente, é possível editar o bloco para refletir o uso recente.

### Editar um bloco para atualizar os dados atuais
1.	Para personalizar as métricas exibidas em uma parte específica, clique com o botão direito no gráfico de métricas e selecione **Editar gráfico**. ![Captura de tela do bloco de solicitações Total que indica que não há dados disponíveis](./media/documentdb-monitor-accounts/documentdb-no-available-data.png)

2.	Na folha **Editar Gráfico**, na seção **Intervalo de Tempo**, clique em **última hora** e em **Salvar**. ![Captura de tela da folha Editar Gráfico com a última hora selecionada](./media/documentdb-monitor-accounts/documentdb-no-available-data-past-hour.png)


3.	Seu bloco agora deve ser atualizado para mostrar seus dados atuais e o uso. ![Captura de tela de atualizada do bloco Total de solicitações na última hora atualizado](./media/documentdb-monitor-accounts/documentdb-no-available-data-fixed.png)

## Próximas etapas
Para saber mais sobre a capacidade do Banco de Dados de Documentos, consulte [Gerenciar a capacidade do Banco de Dados de Documentos](documentdb-manage.md).

<!---HONumber=AcomDC_0316_2016-->