<properties 
	pageTitle="Monitorar uma conta do Banco de Dados de Documentos usando o Portal de Visualização do Azure | Microsoft Azure" 
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
	ms.date="11/18/2015" 
	ms.author="mimig"/>

# Monitorar uma conta de Banco de Dados de Documentos 

Você pode monitorar suas contas do Banco de Dados de Documentos no [Portal de Visualização do Microsoft Azure](https://portal.azure.com/). Para cada conta do Banco de Dados de Documentos, tanto as métricas de desempenho (como as solicitações e erros de servidor) quanto as métricas de utilização (como consumo de armazenamento) estão disponíveis.

## Como: exibir métricas de desempenho para uma conta do Banco de Dados de Documentos
1.	No [Portal de visualização do Azure](https://portal.azure.com/), clique em **Procurar**, **Contas do Banco de Dados de Documentos** e clique no nome da conta do Banco de Dados de Documentos cujas métricas de desempenho você deseja ver.
2.	Dentro da lente **Monitoramento**, você pode, por padrão, consultar:
	*	Solicitações totais para o dia de hoje.
	*	Média de solicitações por minuto no dia atual. 
	
	![Captura de tela da lente Monitoramento](./media/documentdb-monitor-accounts/madocdb1.png)


3.	Clicar em **Total de Solicitações** ou **Média de Solicitações por Segundo** abre uma folha detalhada de **Métrica**.
4.	A folha **Métrica** mostra detalhes das métricas que você selecionou. Na parte superior da folha, há um gráfico e, abaixo dele, uma tabela que mostra valores de agregação das métricas selecionadas, como a média, o mínimo e o máximo. A folha Métrica também exibe a lista de alertas que foram definidos, filtrados para as métricas que aparecem na folha de métricas atual (desse modo, se você possui um determinado número de alertas, você verá apenas aqueles relevantes apresentados aqui).   

	![Captura de tela da lâmina Métrica](./media/documentdb-monitor-accounts/madocdb2.png)


## Personalizar visualizações de métricas de desempenho para uma conta do Banco de Dados de Documentos

1.	Para personalizar as métricas exibidas em uma parte específica, clique com o botão direito no gráfico de métricas e selecione **Editar gráfico**. ![Captura de tela do gráfico Total de Solicitações com o botão Editar gráfico realçado](./media/documentdb-monitor-accounts/madocdb3.png)

2.	Na folha **Editar Gráfico**, há opções para modificar as métricas que são exibidas nessa parte, além de seu intervalo de tempo. ![Captura de tela da folha Editar gráfico](./media/documentdb-monitor-accounts/madocdb4.png)

3.	Para alterar as métricas que são exibidas na parte, basta marcar ou desmarcar as métricas de desempenho disponíveis e clicar em **Salvar** na parte inferior da folha.
4.	Para alterar o intervalo de tempo, escolha um intervalo diferente (por exemplo, **Personalizado**) e clique em **Salvar** na parte inferior da folha.  

	![Captura de tela da parte do Intervalo de Tempo da folha Editar gráfico mostrando como inserir um intervalo de tempo personalizado](./media/documentdb-monitor-accounts/madocdb5.png)


## Criar gráficos de métricas de desempenho lado a lado
O Portal de Visualização do Azure permite que você crie gráficos de métricas lado a lado.

1.	Primeiro, clique com o botão direito no gráfico que você deseja clonar e modificar e selecione **Personalizar**. 

	![Captura de tela do gráfico Total de Solicitações com a opção Personalizar realçada](./media/documentdb-monitor-accounts/madocdb6.png)

2.	Clique em **Clonar** no menu para copiar a parte e clique em **Personalização concluída**.

	![Captura de tela do gráfico Total de Solicitações com as opções de personalização Clonar e Concluído realçadas](./media/documentdb-monitor-accounts/madocdb7.png)


Agora, você pode tratar essa parte como qualquer outra parte de métrica, personalizando as métricas e o intervalo de tempo exibidos na parte. Fazendo isso, você pode ver um gráfico com duas métricas diferentes lado a lado simultaneamente. ![Captura de tela do gráfico Total de Solicitações e o novo gráfico Total de solicitações na última hora](./media/documentdb-monitor-accounts/madocdb8.png)

## Visualizar métricas de utilização para uma conta do Banco de Dados de Documentos
1.	No [Portal de Visualização do Azure](https://portal.azure.com/), clique em **Procurar**, **Contas do Banco de Dados de Documentos** e clique no nome da conta do Banco de Dados de Documentos cujas métricas de uso você quer ver.
2.	Na lente **Uso**, por padrão, você vê os seguintes itens:
	*	Custo estimado até a data do Banco de Dados de Documentos para o período de cobrança atual.
	*	Armazenamento consumido na conta.
	*	Armazenamento máximo disponível da conta (limite).
	*	Uso de permissões e usuários.
	*	Uso de anexos.

	![Captura de tela da lente de Uso](./media/documentdb-monitor-accounts/madocdb9.png)
 
## Defina alertas de métrica de desempenho para uma conta do Banco de Dados de Documentos
1.	No [Portal de Visualização do Azure](https://portal.azure.com/), clique em **Procurar Tudo**, **Contas do Banco de Dados de Documentos** e clique no nome da conta do Banco de Dados de Documentos para a qual você quer definir alertas de métricas de desempenho.
2.	Na lente **Operações**, clique na parte **Regras de alerta**. ![Captura de tela da lente de Operações, com a parte de regras de Alerta selecionada](./media/documentdb-monitor-accounts/madocdb10.png)

3.	Na folha das Regras de alerta, clique em **Adicionar Alerta**. ![Captura de tela da folha Regras de Alerta, com o botão Adicionar alerta realçado](./media/documentdb-monitor-accounts/madocdb11.png)

4.	Na lâmina **Adicionar uma regra de alerta**, especifique:
	*	O nome da regra de alerta que você está configurando.
	*	Uma descrição da nova regra de alerta.
	*	A métrica para a regra de alerta.
	*	A condição, o limite e o período que determinam quando o alerta é ativado. Por exemplo, uma contagem de erros de servidor superior a cinco nos últimos 15 minutos.
	*	Se o administrador de serviços e seus coadministradores recebem ou não emails quando o alerta é enviado.
	*	Endereços de email adicionais para notificações de alerta. ![Captura de tela da folha da regra Adicionar um alerta](./media/documentdb-monitor-accounts/madocdb12.png)

## Próximas etapas
Para saber mais sobre a capacidade do Banco de Dados de Documentos, consulte [Gerenciar a capacidade do Banco de Dados de Documentos](documentdb-manage.md).
 

<!---HONumber=Nov15_HO4-->