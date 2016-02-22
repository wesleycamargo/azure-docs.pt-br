<properties 
	pageTitle="Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure" 
	description="Saiba como escalar verticalmente e horizontalmente um aplicativo Web no Serviço de Aplicativo do Azure, incluindo o dimensionamento automático." 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/10/2015" 
	ms.author="cephalin"/>

# Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure #

Para aumentar o desempenho e a produtividade de seus aplicativos Web no Microsoft Azure, você pode usar o [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715) para dimensionar seu plano de [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) do modo **Gratuito** para **Compartilhado**, **Básico**, **Padrão** ou **Premium**.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

A expansão de Sites do Azure envolve duas ações relacionadas: mudar seu modo de plano do Serviço de Aplicativo para um nível mais alto de serviço e configurar determinados parâmetros depois de ter mudado para o nível mais alto de serviço. Os dois tópicos são abordados neste artigo. Níveis de serviço mais altos, como os modos **Padrão** e **Premium**, oferecem maior eficiência e flexibilidade para determinar como os recursos do Azure são usados.

As configurações de escala terão apenas alguns segundos para serem aplicadas e afetam todos os aplicativos Web em seu plano do Serviço de Aplicativo. Não é necessário alterar seu código nem reimplantar seus aplicativos.

Para obter informações sobre planos do serviço de aplicativo, consulte [O que é um plano do Serviço de Aplicativo?](../app-service/web-sites-web-hosting-plan-overview.md) e [Visão geral aprofundada de planos do Serviço de Aplicativo do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Para obter informações sobre os preços e recursos de planos individuais do Serviço de Aplicativo, consulte [Detalhes de Preços dos Serviços de Aplicativo](/pricing/details/web-sites/).

> [AZURE.NOTE] Antes de alternar de um aplicativo Web do modo **Gratuito** para o modo **Básico**, **Padrão** ou **Premium**, primeiro você deve remover os limites de gastos em vigor para sua assinatura do Serviço de Aplicativo do Azure. Para exibir ou alterar as opções para a sua assinatura do Serviço de Aplicativo do Microsoft Azure, consulte [Assinaturas do Microsoft Azure][azuresubscriptions].

<a name="scalingsharedorbasic"></a> 
<!-- ===================================== -->
## Dimensionando para o modo Compartilhado ou Básico
<!-- ===================================== -->

1. No seu navegador, abra o [Portal do Azure][portal].
	
2. Na folha de seu aplicativo Web, clique em **Todas as configurações** e, em seguida, clique em **Escala**, clique em **Atualizar de um plano Gratuito para adicionar instâncias e obter um melhor desempenho**.
	
	![Escolher um plano][ChooseWHP]
	
4. Na folha **Escolha sua faixa de preços**, escolha **Compartilhado** ou um modo **Básico** e, então, clique em **Selecionar**.
	
	A guia **Notificações** vai piscar **SUCESSO** em verde quando a operação for concluída.
	
5. Deslize a barra de **Instância** da esquerda para a direita para aumentar o número de instâncias, então clique em **Salvar** na barra de comandos. A opção de tamanho de instância não está disponível no modo **Compartilhado**. Para obter mais informações sobre esses tamanhos de instâncias, consulte [Tamanhos de máquina virtual e de serviço de nuvem do Microsoft Azure][vmsizes].
	
	![Tamanho da instância para o modo Básico][ChooseBasicInstances]
	
	A guia **Notificações** vai piscar **SUCESSO** em verde quando a operação for concluída.
	
<a name="scalingstandard"></a> 
<!-- ================================= -->
## Dimensionando para o modo Standard ou Premium
<!-- ================================= -->

> [AZURE.NOTE] Antes de mudar um plano do Serviço de Aplicativo para o modo **Padrão** ou **Premium**, você deve remover os limites de gasto existentes para sua assinatura do Serviço de Aplicativo do Microsoft Azure. Caso contrário, seu aplicativo Web correrá o risco de se tornar indisponível se você atingir os limites antes do término do período de cobrança. Para exibir ou alterar as opções para a sua assinatura do Serviço de Aplicativo do Microsoft Azure, consulte [Assinaturas do Microsoft Azure][azuresubscriptions].

1. Para dimensionar para o modo **Padrão** ou **Premium**, execute as mesmas etapas iniciais executadas para dimensionar para **Compartilhado** ou **Básica** e, em seguida, escolha um modo **Padrão** ou **Premium** em **Escolha sua faixa de preço** e, depois clique em **Selecionar**. 
	
	A guia **Notificações** vai piscar **SUCESSO** em verde quando a operação for concluída, e o Modo de **Dimensionamento Automático** será habilitado.
	
	![Escala em modo Padrão ou Premium][ScaleStandard]
	
	Você ainda pode deslizar a barra **Instância** para dimensionar manualmente mais instâncias, assim como no modo **Básico** mostrado acima. No entanto, aqui você aprenderá como usar o **Modo de Dimensionamento Automático**.
	
2. No **Modo de Dimensionamento Automático**, selecione **Desempenho** para dimensionamento automático com base nas métricas de desempenho.
	
	![Modo de dimensionamento automático definido como Desempenho][Autoscale]
	
3. Em **Intervalo da Instância**, mova os dois controles deslizantes para definir o número mínimo e máximo de instâncias para dimensionar automaticamente para o plano do Serviço de Aplicativo. Para este tutorial, mova o controle deslizante máximo para **seis** instâncias.
	
4. Clique em **Salvar** na barra de comandos.
	
4. Em **Métricas Alvo**, clique em **>** para configurar as regras de dimensionamento automático para a métrica padrão.
	
	![Definir métricas de destino][SetTargetMetrics]
	
	Você pode configurar as regras de dimensionamento automático para métricas de desempenho diferentes, incluindo CPU, memória, fila de disco, fila HTTP e fluxo de dados. Aqui, você vai configurar o dimensionamento automático para o percentual de CPU que faz o seguinte:
	
	- Escale verticalmente para mais em 1 instância se a CPU estiver acima de 70% nos últimos 10 minutos
	- Escale verticalmente para mais em 3 instâncias se CPU estiver acima de 90% nos últimos 5 minutos
	- Reduza verticalmente para menos em 1 instância se a CPU está abaixo de 50% nos últimos 30 minutos 
	
	
4. Deixe a lista suspensa de **Métrica** como **Percentual de CPU**.
	
5. Em **Regras de escala vertical**, configure a primeira regra definindo **Condição** para **Maior**, **Limite** para **70** (%), **Nos últimos** para **10** (minutos), **Expandir** para **1** (instância) e **Esfriar** para **10** (minutos).
	
	![Definir primeira regra de dimensionamento automático][SetFirstRule]
	
	>[AZURE.NOTE] A configuração de **Esfriar** especifica quanto tempo que essa regra deve esperar após a ação de escala anterior para dimensionar novamente.
	
6. Clique em **Adicionar Regra de Escala Vertical** e, em seguida, configure a segunda regra definindo a **Condição** para **Maior**, **Limite** para **90** (%) **Nos últimos** para **1** (minutos), **Escalar verticalmente por** para **3** (instância) e **Esfriar** para **1** (minutos).
	
	![Definir segunda regra de dimensionamento automático][SetSecondRule]
	
5. Em **Regras de redução vertical**, configure a terceira regra definindo **Condição** para **Menos**, **Limite** para **50** (%), **Nos últimos** para **30** (minutos), **Reduzir por** para **1** (instância) e **Esfriar** para **60** (minutos).
	
	![Definir terceira regra de dimensionamento automático][SetThirdRule]
	
7. Clique em **Salvar** na barra de comandos. A regra de escala automática agora deve ser refletida na folha **Escala**.
	
	![Definir resultado da regra de dimensionamento automático][SetRulesFinal]

<a name="ScalingSQLServer"></a>
##Dimensionando um Banco de Dados do SQL Server conectado ao seu aplicativo Web
Se você tiver um ou mais Bancos de Dados SQL Server vinculados ao seu aplicativo Web (independentemente do modo de plano do Serviço de Aplicativo), você pode dimensionar com base em suas necessidades rapidamente.

1. Para dimensionar um dos bancos de dados vinculados, abra a folha de seu aplicativo Web no [Portal do Azure][portal]. Na lista suspensa recolhível **Essentials**, clique no link **Grupo de Recursos**. Em seguida, na parte de **Resumo** da folha de grupo de recursos, clique em um dos bancos de dados vinculados.

	![Banco de dados vinculado][ResourceGroup]
	
2. Na sua folha de Banco de Dados SQL vinculada, clique na parte de **Camada de preços**, selecione uma das camadas com base em suas necessidades de desempenho e clique em **Selecionar**.
	
	![Dimensionar seu banco de dados SQL][ScaleDatabase]
	
3. Você também pode configurar a replicação geográfica para aumentar os recursos de recuperação de desastres e alta disponibilidade do Banco de Dados SQL. Para fazer isso, clique na parte **Replicação Geográfica**.
	
	![Configurar a replicação geográfica para o banco de dados SQL][GeoReplication]

<a name="devfeatures"></a>
## Recursos de desenvolvedor
Dependendo do modo usado pelo aplicativo Web, os seguintes recursos para desenvolvedores estarão disponíveis:

### Número de bits ###

- Os modos **Básico**, **Padrão** e **Premium** dão suporte a aplicativos de 32 e 64 bits.
- O modos dos planos **Gratuito** e **Compartilhado** dão suporte apenas a aplicativos de 32 bits.

### Suporte ao depurador ###

- Suporte ao depurador está disponível para os modos **Gratuito**, **Compartilhado** e **Básico** em uma conexão simultânea por plano do Serviço de Aplicativo.
- O suporte ao depurador está disponível para os modos **Padrão** e **Premium** em cinco conexões simultâneas por plano de Serviço de Aplicativo.

<a name="OtherFeatures"></a>
## Outros recursos

### Monitoramento do ponto de extremidade da web ###

- O monitoramento de ponto de extremidade da Web está disponível nos modos **Básico**, **Padrão** e **Premium**. Para obter mais informações sobre o monitoramento de ponto de extremidade da web, consulte [Como monitorar aplicativos Web](web-sites-monitor.md).

- Para obter informações detalhadas sobre todos os recursos restantes nos planos do Serviço de Aplicativo, incluindo preços e recursos de interesse para todos os usuários (incluindo desenvolvedores), consulte [Detalhes de Preços do Serviço de Aplicativo](/pricing/details/web-sites/).

>[AZURE.NOTE] Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

<a name="Next Steps"></a>
## Próximas etapas

- Para começar a usar o Azure, consulte [Avaliação Gratuita do Microsoft Azure](/pricing/free-trial/).
- Para obter informações sobre preços, suporte e SLA, visite os links a seguir.
	
	[Detalhes de preços de transferências de dados](/pricing/details/data-transfers/)
	
	[Planos de suporte do Microsoft Azure](/support/plans/)
	
	[Contratos de Nível de Serviço](/support/legal/sla/)
	
	[Detalhes de preços do banco de dados SQL](/pricing/details/sql-database/)
	
	[Tamanhos de máquina virtual e de serviço de nuvem do Microsoft Azure][vmsizes]
	
	[Detalhes de Preços do Serviço de Aplicativo](/pricing/details/web-sites/)
	
	[Detalhes de Preços do Serviço de Aplicativo - Conexões SSL](/pricing/details/web-sites/#ssl-connections)

- Para obter informações sobre práticas recomendadas do Serviço de Aplicativo do Azure, incluindo a criação de uma arquitetura escalonável e flexível, consulte [Práticas recomendadas: Aplicativos Web do Serviço de Aplicativo do Azure](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Vídeos sobre como dimensionar aplicativos Web:
	
	- [Quando dimensionar Sites do Azure - com Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Dimensionamento automático de Sites do Azure, CPU ou programado - com Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Como dimensionar sites do Azure - com Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- LINKS -->
[vmsizes]: http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]: http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]: http://go.microsoft.com/fwlink/?LinkID=235288
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
 

<!---HONumber=AcomDC_0211_2016-->