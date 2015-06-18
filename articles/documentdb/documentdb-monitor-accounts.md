<properties 
	pageTitle="Monitorar uma conta do Banco de Dados de Documentos | Azure" 
	description="Saiba como monitorar sua conta do Banco de Dados de Documentos para métricas de desempenho (como solicitações e erros de servidor) e as métricas de uso (por exemplo, o consumo de armazenamento)." 
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
	ms.date="01/13/2015" 
	ms.author="mimig"/>

#Monitorar uma conta de Banco de Dados de Documentos  

Você pode monitorar suas contas do Banco de Dados de Documentos no [Portal de Visualização do Azure](https://portal.azure.com/). Para cada conta do Banco de Dados de Documentos, tanto as métricas de desempenho (como as solicitações e erros de servidor) quanto as métricas de utilização (como consumo de armazenamento) estão disponíveis.

## <a id="metrics"></a>  Como: Visualizar métricas de desempenho para uma conta do Banco de Dados de Documentos
1.	No [Portal de Visualização do Azure](https://portal.azure.com/), clique em **Procurar**, **Contas do Banco de Dados de Documentos** e clique no nome da conta do Banco de Dados de Documentos cujas métricas de desempenho você quer ver.
2.	Na lente de **Monitoramento** você pode, por padrão, ver:
	*	Solicitações totais para o dia de hoje.
	*	Média de solicitações por minuto no dia atual. 
	
	![](./media/documentdb-monitor-accounts/madocdb1.png)


3.	Clicar em **Total de Solicitações** ou **Média de Solicitações por Segundo** abre uma folha detalhada de **Métrica**.
4.	A folha **Métrica** mostra detalhes das métricas que você selecionou.  Na parte superior da folha há um gráfico e, abaixo dele, uma tabela que mostra valores de agregação das métricas selecionadas como a média, o mínimo e o máximo.  A folha Métrica também exibe a lista de alertas que foram definidos, filtrados para as métricas que aparecem na folha de métricas atual (desse modo, se você possui um determinado número de alertas, você verá apenas aqueles relevantes apresentados aqui).   

	![](./media/documentdb-monitor-accounts/madocdb2.png)


## <a id="custom"></a>Personalizar exibições de métricas de desempenho para uma conta do Banco de Dados de Documentos

1.	Para personalizar as métricas exibidas em uma parte específica, clique com o botão direito no gráfico de métricas e selecione **Editar gráfico**.  
	![](./media/documentdb-monitor-accounts/madocdb3.png)

2.	Na folha **Editar Gráfico**, há opções para modificar as métricas que são exibidas nessa parte, além de seu intervalo de tempo.  
	![](./media/documentdb-monitor-accounts/madocdb4.png)

3.	Para alterar as métricas que são exibidas na parte, basta marcar ou desmarcar as métricas de desempenho disponíveis e clicar em **Salvar** na parte inferior da folha.  
4.	Para alterar o intervalo de tempo, escolha um intervalo diferente (por exemplo, **Última Hora**) e clique em **Salvar** na parte inferior da folha.  

	![](./media/documentdb-monitor-accounts/madocdb5.png) 


## <a id="create"></a>Criar gráficos de métricas de desempenho lado a lado
O portal de Visualização do Azure permite que você crie gráficos de métricas lado a lado.  

1.	Primeiro, clique com o botão direito no gráfico que você deseja clonar e modificar e selecione **Personalizar**. 

	![](./media/documentdb-monitor-accounts/madocdb6.png)

2.	Clique em **Clonar** no menu para copiar a parte e clique em **Personalização concluída**. 

	![](./media/documentdb-monitor-accounts/madocdb7.png)  


Agora, você pode tratar essa parte como qualquer outra parte de métrica, personalizando as métricas e o intervalo de tempo exibidos na parte.  Fazendo isso, você pode ver um gráfico com duas métricas diferentes lado a lado simultaneamente.  
	![](./media/documentdb-monitor-accounts/madocdb8.png)  

## <a id="view"></a>Ver métricas de uso de uma conta do Banco de Dados de Documentos
1.	No [Portal de Visualização do Azure](https://portal.azure.com/), clique em **Procurar**, **Contas do Banco de Dados de Documentos** e clique no nome da conta do Banco de Dados de Documentos cujas métricas de uso você quer ver.
2.	Na lente **Uso**, por padrão você vê os seguintes itens:
	*	Custo estimado.
	*	Armazenamento consumido na conta.
	*	Armazenamento máximo disponível da conta.
	*	Uso de permissões e usuários.
	*	Alocação de unidades de capacidade.
	*	Uso de anexos.

	![](./media/documentdb-monitor-accounts/madocdb9.png)
 
## <a id="setup"></a>Definir alertas de métricas de desempenho para uma conta do Banco de Dados de Documentos
1.	No [Portal de Visualização do Azure](https://portal.azure.com/), clique em **Procurar**, **Contas do Banco de Dados de Documentos** e clique no nome da conta do Banco de Dados de Documentos para a qual você quer definir alertas de métricas de desempenho.
2.	Na lente **Operações**, clique na parte **Regras de alerta**.  
	![](./media/documentdb-monitor-accounts/madocdb10.png)

3.	Na folha das Regras de alerta, clique em **Adicionar Alerta**.  
	![](./media/documentdb-monitor-accounts/madocdb11.png)

4.	Na folha **Adicionar uma regra de alerta**, especifique:
	*	O nome da regra de alerta que você está configurando.
	*	Uma descrição da nova regra de alerta.
	*	A métrica para a regra de alerta.
	*	A condição, o limite e o período que determinam quando o alerta é ativado. Por exemplo, uma contagem de erros de servidor superior a cinco nos últimos 15 minutos.
	*	Se o administrador de serviços e seus coadministradores recebem ou não emails quando o alerta é enviado.
	*	Endereços de email adicionais para notificações de alerta.  
	![](./media/documentdb-monitor-accounts/madocdb12.png)

 
## <a id="next"></a>Próximas etapas
Para saber mais sobre a capacidade e o desempenho do Banco de Dados de Documentos, consulte [Gerenciar a capacidade e o desempenho do Banco de Dados de Documentos](documentdb-manage.md). 

<!--Anchors-->
[Como exibir métricas de desempenho para uma conta do Banco de Dados de Documentos]: #How-to-view-performance-metrics-for-a-DocumentDB-account
[Personalizar exibições de métricas de desempenho para uma conta do Banco de Dados de Documentos]: #Customize-performance-metric-views-for-a-DocumentDB-account
[Como criar gráficos de métricas de desempenho lado a lado]: #How-to-create-side-by-side-performance-metric-charts
[Como exibir métricas de uso para uma conta do Banco de Dados de Documentos]: #How-to-view-usage-metrics-for-a-DocumentDB-account
[Como definir alertas de métricas de desempenho para uma conta do Banco de Dados de Documentos]: #How-to-setup-performance-metric-alerts-for-a-DocumentDB-account
[Próximas etapas]: #Next-steps

<!--HONumber=49--> 