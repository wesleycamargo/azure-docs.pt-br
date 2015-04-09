<properties 
	pageTitle="Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure" 
	description="Saiba como escalar verticalmente ou horizontalmente um aplicativo Web no Serviço de Aplicativo do Azure, incluindo o dimensionamento automático." 
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
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure #

Para aumentar o desempenho e a produtividade de seus aplicativos Web no Microsoft Azure, você pode usar o [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715) para dimensionar seu plano de [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) do modo **Gratuito** para **Compartilhado**, **Básico**, **Padrão** ou **Premium**. 

A expansão de Sites do Azure envolve duas ações relacionadas: mudar seu modo de plano do Serviço de Aplicativo para um nível mais alto de serviço e configurar determinados parâmetros depois de ter mudado para o nível mais alto de serviço. Os dois tópicos são abordados neste artigo. Níveis de serviço mais altos, como os modos **Padrão** e **Premium**, oferecem maior eficiência e flexibilidade para determinar como os recursos do Azure são usados.

A alteração e a configuração dos modos são realizadas facilmente na guia Escala do portal de gerenciamento. Você pode expandir ou reduzir conforme necessário. Essas alterações terão apenas alguns segundos para serem aplicadas e afetam todos os aplicativos Web em seu plano de Serviço de Aplicativo. Não é necessário alterar seu código nem reimplantar seus aplicativos.

Para obter informações sobre planos de serviço de aplicativo, consulte [O que é um plano de Serviço de Aplicativo?](web-sites-web-hosting-plan-overview.md) e [Visão geral aprofundada de planos de Serviço de Aplicativo do Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md). Para informações sobre os preços e os recursos dos planos de Serviço de Aplicativo individuais, consulte [Detalhes de preços de Serviço de Aplicativo](/pricing/details/web-sites/).  

> [AZURE.NOTE] Antes de alternar de um aplicativo Web do modo **Gratuito** para o modo **Compartilhado**, **Básico** ou **Padrão**, você deve primeiro remover os limites de gastos em vigor para sua assinatura de Serviço de Aplicativo do Azure. Para exibir ou alterar as opções de sua assinatura do Serviço de Aplicativo do Microsoft Azure, consulte [Assinaturas do Microsoft Azure][azuresubscriptions].

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## Dimensionando para o modo Compartilhado ou Básico
<!-- ===================================== -->

1. No seu navegador, abra o [Portal do Azure][portal].
	
2. Na lâmina de seu aplicativo Web, clique em **Todas as configurações**; em seguida, clique em **Escala** e em **Atualizar de um plano Gratuito para adicionar instâncias e obter um melhor desempenho**.
	
	![Choose Plan][ChooseWHP]
	
4. Na lâmina **Escolha sua faixa de preço**, escolha o modo **Compartilhado** ou então **Básico**, depois clique em **Selecionar**.
	
	Na guia **Notificações**, a mensagem **SUCESSO** piscará em verde quando a operação for concluída. 
	
5. Deslize a barra **Instância** da esquerda para a direita para aumentar o número de instâncias, depois clique em **Salvar** na barra de comandos. A opção de tamanho de instância não está disponível no modo **Compartilhado**. Para obter mais informações sobre esses tamanhos de instâncias, consulte [Tamanhos de máquina virtual e de serviço de nuvem do Microsoft Azure][vmsizes].
	
	![Instance size for Basic mode][ChooseBasicInstances]
	
	Na guia **Notificações**, a mensagem **SUCESSO** piscará em verde quando a operação for concluída. 
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## Dimensionando para o modo Standard ou Premium
<!-- ================================= -->

> [AZURE.NOTE] Antes de mudar um plano de Serviço de Aplicativo para o modo **Padrão** ou **Premium**, você deve remover os limites de gasto existentes para sua assinatura do Serviço de Aplicativo do Microsoft Azure. Caso contrário, seu aplicativo Web correrá o risco de se tornar indisponível se você atingir os limites antes do término do período de cobrança. Para exibir ou alterar as opções de sua assinatura do Serviço de Aplicativo do Microsoft Azure, consulte [Assinaturas do Microsoft Azure][azuresubscriptions].

1. Para dimensionar para o modo **Padrão** ou **Premium**, execute as mesmas etapas iniciais executadas para dimensionar para **Compartilhado** ou **Básica** e, em seguida, escolha um modo **Padrão** ou **Premium** em **Escolha sua faixa de preço**, depois clique em **Selecionar**. 
	
	Na guia **Notificações**, a mensagem **SUCESSO** piscará em verde quando a operação for concluída, e o **Modo de dimensionamento automático** será habilitado.
	
	![Scale in Standard or Premium Mode][ScaleStandard]
	
	Você ainda pode deslizar a barra **Instância** para dimensionar manualmente para mais instâncias, assim como no modo **Básico**, conforme mostrado acima. No entanto, aqui você aprenderá como usar o **Modo de dimensionamento automático**. 
	
2. Em **Modo de dimensionamento automático**, selecione **Desempenho** para dimensionamento automático baseado nas métricas de desempenho.
	
	![Autoscale Mode set to Performance][Autoscale]
	
3. Em **Intervalo de instâncias**, mova os dois controles deslizantes para definir os números mínimo e máximo de instâncias a dimensionar automaticamente para o plano de Serviço de Aplicativo. Para este tutorial, mova o controle deslizante máximo para **6** instâncias.
	
4. Clique em **Salvar**, na barra de comando.
	
4. Em **Métricas desejadas**, clique em **>** para configurar regras de dimensionamento automático para a métrica padrão.  
	
	![Set Target Metrics][SetTargetMetrics]
	
	Você pode configurar regras de dimensionamento automático para métricas de desempenho diferentes, incluindo CPU, memória, fila de disco, fila HTTP e fluxo de dados. Aqui, você vai configurar o dimensionamento automático para percentual de CPU, que faz o seguinte:
	
	- Escalar verticalmente em 1 instância se a CPU estiver acima de 70% nos últimos 10 minutos
	- Escalar verticalmente em 3 instâncias se a CPU estiver acima de 90% nos últimos 5 minutos
	- Reduza verticalmente em 1 instância se a CPU estiver acima de 50% nos últimos 30 minutos 
	
	
4. Deixe a lista suspensa **Métrica** como **Percentual de CPU**.
	
5. Em **Regras de escala vertical**, configure a primeira regra definindo **Condição** como **Maior**, **Limite** como **70** (%), **Termina após** como **10** (minutos), **Escalar verticalmente em** como **1** (instância) e **Espera** como **10** (minutos). 
	
	![Set First Autoscale Rule][SetFirstRule]
	
	>[AZURE.NOTE] A configuração **Espera** define quanto tempo esta regra deve esperar para uma nova ação de dimensionamento, após a ação de dimensionamento anterior.
	
6. Em **Adicionar regra de escala vertical**, configure a primeira regra definindo **Condição** como **Maior**, **Limite** como **90** (%), **Termina após** como **1** (minuto), **Escalar verticalmente em** como **3** (instâncias) e **Espera** como **1** (minuto).
	
	![Set Second Autoscale Rule][SetSecondRule]
	
5. Em **Regras de redução vertical**, configure a terceira regra definindo **Condição** como **Menor**, **Limite** como **50** (%), **Termina após** como **30** (minutos), **Reduzir em** como **1** (instância) e **Espera** como **60** (minutos). 
	
	![Set Third Autoscale Rule][SetThirdRule]
	
7. Clique em **Salvar**, na barra de comando. A regra de dimensionamento automático agora deve ser refletida na lâmina **Escala**. 
	
	![Set Autoscale Rule Result][SetRulesFinal]

<a name="ScalingSQLServer"></a>
##Dimensionando um Banco de Dados de SQL Server conectado ao seu aplicativo Web
Se você tiver um ou mais bancos de dados SQL Server vinculados ao seu aplicativo Web (independentemente do modo de plano de Serviço de Aplicativo), você pode dimensioná-los rapidamente com base em suas necessidades.

1. Para dimensionar um dos bancos de dados vinculados, abra a lâmina de seu aplicativo Web no [Portal do Azure][portal]. No menu suspenso recolhível **Informações Gerais**, clique no link **Grupo de recursos**. Em seguida, na parte **Resumo** da lâmina de grupo de recursos, clique em um dos bancos de dados vinculados.

	![Linked database][ResourceGroup]
	
2. Na lâmina do seu banco de dados SQL vinculado, clique na parte **Faixa de preços**, selecione uma das faixas com base em suas necessidades de desempenho e clique em **Selecionar**. 
	
	![Scale your SQL Database][ScaleDatabase]
	
3. Você também pode configurar a replicação geográfica para aumentar os recursos de recuperação de desastres e de alta disponibilidade do banco de dados SQL. Para fazer isso, clique na parte **Replicação geográfica**.
	
	![Set up geo-replication for SQL Database][GeoReplication]

<a name="devfeatures"></a>
## Recursos de desenvolvedor
Dependendo do modo usado pelo aplicativo Web, os seguintes recursos para desenvolvedores estarão disponíveis:

### Número de bits ###

- Os modos **Básico**, **Padrão**, e **Premium** dão suporte a aplicativos de 32 e 64 bits.
- O modos dos planos **Gratuito** e **Compartilhado** dão suporte apenas a aplicativos de 32 bits.

### Suporte ao depurador ###

- Suporte ao depurador está disponível para os modos **Gratuito**, **Compartilhado** e **Básico** em uma conexão simultânea por plano de Serviço de Aplicativo.
- O suporte ao depurador está disponível para os modos **Padrão** e **Premium** em cinco conexões simultâneas por plano de Serviço de Aplicativo.

<a name="OtherFeatures"></a>
## Outros recursos

### Monitoramento do ponto de extremidade da Web ###

- O monitoramento de ponto de extremidade da Web está disponível nos modos **Básico**, **Padrão** e **Premium**. Para obter mais informações sobre o monitoramento de ponto de extremidade da Web, consulte [Como monitorar aplicativos Web (a página pode estar em inglês)](web-sites-monitor.md).

- Para obter informações detalhadas sobre todos os demais recursos dos planos de hospedagem na Web, incluindo preços e recursos de interesse para todos os usuários (incluindo desenvolvedores), consulte [Detalhes de preços de Serviço de Aplicativo](/pricing/details/web-sites/).

>[AZURE.NOTE] Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

<a name="Next Steps"></a>	
## Próximas etapas

- Para começar a usar o Azure, consulte [Avaliação Gratuita do Microsoft Azure](/pricing/free-trial/).
- Para obter informações sobre preços, suporte e SLA, visite os links a seguir.
	
	[Detalhes de preços de transferências de dados](/pricing/details/data-transfers/)
	
	[Planos de suporte do Microsoft Azure](/support/plans/)
	
	[Contratos de Nível de Serviço](/support/legal/sla/)
	
	[Detalhes de preços do banco de dados SQL](/pricing/details/sql-database/)
	
	[Tamanhos de máquina virtual e de serviço de nuvem para o Microsoft Azure][vmsizes]
	
	[Detalhes de preços de Serviço de Aplicativo](/pricing/details/web-sites/)
	
	[Detalhes de preços de Serviço de Aplicativo - conexões SSL](/pricing/details/web-sites/#ssl-connections)

- Para obter informações sobre as práticas recomendadas do Serviço de Aplicativo do Azure, incluindo a criação de uma arquitetura escalonável e resiliente, consulte [Práticas recomendadas: aplicativos Web do Serviço de Aplicativo do Azure](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Vídeos sobre como dimensionar aplicativos Web:
	
	- [Quando dimensionar Sites do Azure - com Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Dimensionamento automático de sites do Azure, CPU ou programado - com Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Como dimensionar Sites do Azure - com Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)

## O que mudou
* Para obter um guia para a mudança de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a mudança do portal antigo para o novo portal, consulte: [Referência para navegação no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

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