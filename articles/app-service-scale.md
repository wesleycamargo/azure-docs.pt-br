<properties 
	pageTitle="Como dimensionar um aplicativo Web no Serviço de Aplicativo do Azure" 
	description="Saiba como escalar verticalmente e horizontalmente um aplicativo Web no Serviço de Aplicativo do Azure, incluindo o dimensionamento automático." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="cephalin"/>

# Como dimensionar um aplicativo Web no Serviço de Aplicativo do Azure #

Para aumentar o desempenho e a produtividade de seus aplicativos Web no Microsoft Azure e habilitar mais funcionalidades para atender às suas necessidades comerciais, você pode usar o Portal de Gerenciamento do Azure para dimensionar seu plano de Serviço de Aplicativo do modo Gratuito para Compartilhado, Básico, Padrão e Premium (visualização). 

Aumentar a escala em aplicativos Web do Azure envolve duas ações relacionadas: alterar o modo de plano do Serviço de Aplicativo para um nível mais alto de serviço e configurar determinados parâmetros depois de alternar para o nível mais alto de serviço.  Os dois tópicos são abordados neste artigo.  Níveis de serviço mais altos como o modo Padrão oferecem maior eficiência e flexibilidade para determinar como os recursos do Azure são usados.

A alteração e a configuração dos modos são realizadas facilmente na guia Escala do portal de gerenciamento.  Você pode expandir ou reduzir conforme necessário.  Essas alterações terão apenas alguns segundos para serem aplicadas e afetam todos os aplicativos Web em seu plano de serviço.  Não é necessário alterar seu código nem reimplantar seus aplicativos.

Para obter informações sobre planos de Serviço de Aplicativo (anteriormente, planos de hospedagem Web), consulte [O que é um Plano de Hospedagem do Aplicativo de Serviço?](web-sites-web-hosting-plan-overview.md) e [Visão Geral Detalhada de Planos de Hospedagem de Serviço de Aplicativo do Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md).  Para obter informações sobre os preços e recursos de planos individuais de Serviço de Aplicativo, consulte [Detalhes de Preços de Serviços de Aplicativo](http://http://azure.microsoft.com/pricing/details/web-sites/).  

> [AZURE.NOTE] Antes de trocar de um aplicativo Web no modo **Gratuito** para o modo **Básico** ou **Padrão**, remova os limites de gastos em vigor para sua assinatura do Serviço de Aplicativo do Azure.  Para exibir ou alterar as opções para a sua assinatura do Serviço de Aplicativo do Microsoft Azure, consulte [Assinaturas do Microsoft Azure][azuresubscriptions].

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## Dimensionando para o modo de plano Compartilhado ou Básico do Serviço de Aplicativo
<!-- ===================================== -->

1. No seu navegador, abra o [Portal do Azure][portal].
	
2. Na folha de seu aplicativo Web, clique em **Todas as configurações** e, em seguida, clique em **Escala**, clique em **Atualizar de um plano Gratuito para adicionar instâncias e obter um melhor desempenho**.
	
	![Choose Hosting Plan][ChooseWHP]
	
4. Na folha **Escolha sua faixa de preços**, escolha **Compartilhado** ou um modo **Básico**, então clique em **Selecionar**.

	A guia **Notificações** vai piscar **SUCESSO** em verde quando a operação for concluída. 
	
5. Deslize a barra de **Instância** da esquerda para a direita para aumentar o número de instâncias, então clique em **Salvar** na barra de comandos.  A opção de tamanho de instância não está disponível no modo **Compartilhado**.  Para obter mais informações sobre esses tamanhos de instâncias, consulte [Tamanhos de máquina virtual e de serviço de nuvem do Microsoft Azure][vmsizes].
	
	![Instance size for Basic mode][ChooseBasicInstances]
	
	A guia **Notificações** vai piscar **SUCESSO** em verde quando a operação for concluída. 
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## Dimensionando para o modo de plano de Serviço de Aplicativo padrão
<!-- ================================= -->

> [AZURE.NOTE] Antes de alternar de um plano de Serviço de Aplicativo do modo **Livre** para o **Padrão**, você deve remover caps de gasto no local para a assinatura do Serviço de Aplicativo do Microsoft Azure.  Caso contrário, seu site correrá o risco de se tornar indisponível se você atingir os limites antes do término do período de cobrança.  Para exibir ou alterar as opções para a sua assinatura do Serviço de Aplicativo do Microsoft Azure, consulte [Assinaturas do Microsoft Azure][azuresubscriptions].

1. Para dimensionar para o modo **Padrão** modo, siga as mesmas as etapas iniciais que para dimensionar para **Compartilhado** ou **Básico** e, em seguida, escolha um modo **Padrão** em **Escolha a sua faixa de preços**, clique em **Selecionar**. 
	
	A guia **Notificações** vai piscar **SUCESSO** em verde quando a operação for concluída, e o **Modo de Dimensionamento Automático** será habilitado.

	![Scale in Standard Mode][ScaleStandard]

	Você ainda pode deslizar a barra **Instância** para dimensionar manualmente mais instâncias, assim como no modo **Básico** mostrado acima.  No entanto, aqui você aprenderá como usar o **Modo de Dimensionamento Automático**. 

2. No **Modo de Dimensionamento Automático**, selecione **Desempenho** para dimensionamento automático com base nas métricas de desempenho.
	
	![Autoscale Mode set to Performance][Autoscale]
	
3. Em **Intervalo da Instância**, mova os dois controles deslizantes para definir o número mínimo e máximo de instâncias para dimensionar automaticamente para o plano de Serviço de Aplicativo.  Para este tutorial, mova o controle deslizante máximo para **6** instâncias.

4. Clique em **Salvar** na barra de comandos.

4. Em **Métricas Alvo**, clique em **>** para configurar as regras de dimensionamento automático para a métrica padrão.  
	
	![Set Target Metrics][SetTargetMetrics]
	
	Você pode configurar as regras de dimensionamento automático para métricas de desempenho diferentes, incluindo CPU, memória, fila de disco, fila HTTP e fluxo de dados.  Aqui, você vai configurar o dimensionamento automático para o percentual de CPU que faz o seguinte:

	- Escale verticalmente para mais em 1 instância se a CPU estiver acima de 70% nos últimos 10 minutos
	- Escale verticalmente para mais em 3 instâncias se CPU estiver acima de 90% nos últimos 5 minutos
	- Reduza verticalmente para menos em 1 instância se a CPU está abaixo de 50% nos últimos 30 minutos 


4. Deixe a lista suspensa de **Métrica** como **Percentual de CPU**.
	
5. Em **Regras de escala vertical**, configure a primeira regra definindo **Condição** para **Maior**, **Limite** para **70** (%) **Nos últimos** para **10** (minutos), **Expandir** para **1** (instância) e **Esfriar** para **10** (minutos). 
	
	![Set First Autoscale Rule][SetFirstRule]

	>[AZURE.NOTE] A configuração de **Esfriar** especifica quanto tempo que essa regra deve esperar após a ação de escala anterior para dimensionar novamente.
	
6. Clique em **Adicionar Regra de Escala Vertical** e, em seguida, configure a segunda regra definindo a **Condição** para **Maior**, **Limite** para **90** (%) **Nos últimos** para **1** (minutos), **Escalar verticamente por** para **3** (instância) e **Esfriar** para **1** (minutos).
	
	![Set Second Autoscale Rule][SetSecondRule]
	
5. Em **Regras de redução vertical**, configure a terceira regra definindo **Condição** para **Menos**, **Limite** para **50** (%) **Nos últimos** para **30** (minutos), **Reduzir por** para **1** (instância) e **Esfriar** para **60** (minutos). 
	
	![Set Third Autoscale Rule][SetThirdRule]
	
7. Clique em **Salvar** na barra de comandos.  A regra de escala automática agora deve ser refletida na folha **Escala**. 
	
	![Set Autoscale Rule Result][SetRulesFinal]
	
<a name="ScalingSQLServer"></a>
##Dimensionando um Banco de Dados do SQL Server conectado ao seu aplicativo Web
Se você tiver um ou mais Bancos de Dados SQL Server vinculados ao seu aplicativo Web (independentemente do modo de plano de Serviço de Aplicativo), você pode dimensionar com base em suas necessidades rapidamente.

1. Para dimensionar um banco de dados vinculado, abra a folha do aplicativo Web no [Portal do Azure][portal].  Na lista suspensa recolhível **Essentials**, clique no link **Grupo de Recursos**.  Em seguida, na parte de **Resumo** da folha de grupo de recursos, clique em um dos bancos de dados vinculados.
	
	![Linked database][ResourceGroup]
	
2. Na sua folha de Banco de Dados SQL vinculada, clique na parte de **Camada de preços**, selecione uma das camadas com base em suas necessidades de desempenho e clique em **Selecionar**. 
	
	![Scale your SQL Database][ScaleDatabase]
	
3. Você também pode configurar a replicação geográfica para aumentar os recursos de recuperação de desastres e alta disponibilidade do Banco de Dados SQL.  Para fazer isso, clique na parte **Replicação Geográfica**.

	![Set up geo-replication for SQL Database][GeoReplication]

<a name="devfeatures"></a>
## Recursos de desenvolvedor
Dependendo do modo do plano de serviço do aplicativo da web, os seguintes recursos para desenvolvedores estão disponíveis:

### Número de bits ###

- Os modos dos planos Básico e Padrão oferecem suporte a aplicativos de 32 e 64 bits.
- O modos dos planos Gratuito e Compartilhado oferecem suporte apenas a aplicativos de 32 bits.

### Suporte ao depurador ###

- Suporte do depurador está disponível para os modos de plano Gratuito, Compartilhado e Serviço de Aplicativo Básico em uma conexão simultânea por aplicativo.
- Suporte do depurador está disponível para o modo de plano de Serviço de Aplicativo Padrão em cinco conexões simultâneas por aplicativo.

<a name="OtherFeatures"></a>
## Outros recursos

### Monitoramento do ponto de extremidade da web ###

- Monitoramento de ponto de extremidade Web está disponível nos modos de plano do Serviço de Aplicativo básico e padrão.  Para obter mais informações sobre o monitoramento de ponto de extremidade da web, consulte [Como monitorar aplicativos Web](web-sites-monitor.md).

- Para obter informações detalhadas sobre todos os recursos restantes nos planos do Serviço de Aplicativo, incluindo preços e recursos de interesse para todos os usuários (incluindo desenvolvedores), consulte [Detalhes de Preços do Serviço de Aplicativo](http://azure.microsoft.com/pricing/details/web-sites/).

<a name="Next Steps"></a>	
## Próximas etapas

- Para começar a usar o Azure, consulte [Avaliação Gratuita do Microsoft Azure](http://azure.microsoft.com/pricing/free-trial/).

- Para obter informações sobre preços, suporte e SLA, visite os links a seguir.
	
	[Detalhes de preços de transferências de dados](http://azure.microsoft.com/pricing/details/data-transfers/)
	
	[Planos de suporte do Microsoft Azure](http://azure.microsoft.com/support/plans/)
	
	[Contratos de Nível de Serviço](http://azure.microsoft.com/support/legal/sla/)
	
	[Detalhes de preços do banco de dados SQL](http://azure.microsoft.com/pricing/details/sql-database/)
	
	[Tamanhos de máquina virtual e de serviço de nuvem do Microsoft Azure][vmsizes]
	
	[Detalhes de Preços do Serviço de Aplicativo](http://azure.microsoft.com/pricing/details/web-sites/)
	
	[Detalhes de Preços do Serviço de Aplicativo - Conexões SSL](http://azure.microsoft.com/pricing/details/web-sites/#ssl-connections)

- Para obter informações sobre práticas recomendadas do Serviço de Aplicativo do Azure, incluindo a criação de uma arquitetura escalonável e flexível, consulte [Práticas Recomendadas:  Sites do Microsoft Azure (WAWS)](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Vídeos sobre dimensionamento de sites do Azure:
	
	[Quando dimensionar Sites do Azure - com Stefan Schackow](http://azure.microsoft.com/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	
	[Dimensionamento automático de Sites do Azure, CPU ou programado - com Stefan Schackow](http://azure.microsoft.com/documentation/videos/auto-scaling-azure-web-sites/)

	[Como dimensionar Sites do Azure - com Stefan Schackow](http://azure.microsoft.com/documentation/videos/how-azure-web-sites-scale/)



<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png

<!--HONumber=49-->