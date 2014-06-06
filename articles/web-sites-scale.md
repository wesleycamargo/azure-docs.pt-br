<properties linkid="manage-scenarios-how-to-scale-websites" urlDisplayName="Como dimensionar" pageTitle="Como dimensionar sites - Gerenciamento de serviços do Microsoft Azure" metaKeywords="dimensionamento de sites no Azure" description="Saiba como dimensionar sites no Azure para usar planos de hospedagem na web Gratuito, Compartilhado, Básico e Padrão." metaCanonical="" services="web-sites" documentationCenter="" title="Como dimensionar sites" authors="timamm"  solutions="" writer="timamm" manager="paulettm" editor="mollybos"  />

# Como dimensionar sites #

Para aumentar o desempenho e a produtividade de seus sites no Microsoft Azure, você pode usar o Portal de Gerenciamento do Azure para dimensionar o modo de seu Plano de Hospedagem na Web de Gratuito para Compartilhado, Básico ou Padrão. 

A expansão de Sites do Azure envolve duas coisas relacionadas: uma é mudar de seu modo de Plano de Hospedagem na Web para um nível mais alto de serviço e a outra é configurar determinados parâmetros depois que você muda para o nível mais alto de serviço. Os dois tópicos são abordados neste artigo. Níveis de serviço mais altos como o modo Padrão oferecem maior eficiência e flexibilidade para determinar como os recursos do Azure são usados.

A alteração e a configuração dos modos são realizadas facilmente na guia Escala do portal de gerenciamento. Você pode expandir ou reduzir conforme necessário. Essas alterações levarão apenas alguns segundos para serem aplicadas e afetam todos os sites em seu Plano de Hospedagem na Web. Não é necessário alterar seu código nem reimplantar seus aplicativos.

Para obter informações sobre os Planos de Hospedagem na Web, consulte [Planos de Hospedagem na Web de Sites do Azure (a página pode estar em inglês) ](http://go.microsoft.com/fwlink/?LinkId=9845584). Para obter informações sobre os preços e os recursos de cada Plano de Hospedagem na Web, consulte [Detalhes de preços de Sites](http://www.windowsazure.com/pt-br/pricing/details/web-sites/).  

> [WACOM.NOTE] Antes de mudar de um modo de Plano de Hospedagem na Web **Gratuito** para **Básico** ou **Padrão**, primeiro você deve remover os limites de gastos estabelecidos para sua assinatura do Site. Para exibir ou alterar as opções de sua assinatura dos Sites do Microsoft Azure, consulte [Assinaturas do Microsoft Azure][azuresubscriptions].

Neste artigo:

- [Dimensionando para o modo de Plano Compartilhado ou Básico](#scalingsharedorbasic)
- [Dimensionando para o Modo do Plano Padrão](#scalingstandard)
- [Dimensionando um Banco de Dados SQL Server conectado ao seu site](#ScalingSQLServer)
- [Recursos de desenvolvedor](#devfeatures)
- [Outros recursos](#OtherFeatures)

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## Dimensionando para o modo de Plano Compartilhado ou Básico
<!-- ===================================== -->

1. No navegador, abra o [Portal de Gerenciamento][portal].
	
2. Na guia **Sites**, selecione o seu site.
	
	![Selecionando um site][SelectWebsite]
	
3. Clique na guia **Escala**.
	
	![Na guia Escala][SelectScaleTab]
	
4. Na seção **Modo de Plano de Hospedagem na Web**, escolha uma destas opções **COMPARTILHADO** ou **BÁSICO**. No exemplo da imagem a escolha é Básico.
	
	![Escolher o Plano de Hospedagem na Web][ChooseWHP]
	
	A seção **Sites do Plano de Hospedagem da Web** apresenta uma pequena lista de sites no plano atual. Todos os sites no plano atual serão alterados para o Modo de Hospedagem na Web que você selecionar.
	
5. Na seção **Capacidade**, escolha o **Tamanho da Instância**. As opções disponíveis são **Pequena**, **Média** ou **Grande**. A opção de tamanho de instância não está disponível no modo Compartilhado. Para obter mais informações sobre esses tamanhos de instâncias, consulte [Tamanhos de máquina virtual e de serviço de nuvem do Microsoft Azure][vmsizes].
	
	![Tamanho da instância para o modo Básico][ChooseBasicInstanceSize]
	
6. Use o controle deslizante para escolher a **Contagem de Instâncias** desejada.
	
	![Contagem de instâncias para o modo Básico][ChooseBasicInstanceCount]
	
7. Na barra de comandos, escolha **Salvar**. 
	
	![Botão Salvar][SaveButton]
 	
	> [WACOM.NOTE] Você pode configurar e salvar as definições de **Plano de Hospedagem na Web**, **Tamanho da Instância** e **Contagem de Instâncias** separadamente se desejar.
	
8. Uma mensagem de confirmação informa que os sites no mesmo Plano de Hospedagem na Web que o site atual também serão alterados para o novo modo. Escolha **Sim** para concluir a alteração. 
	
	No exemplo, o modo do plano foi alterado para **Básico**:
	
	![Alteração do plano concluída][BasicComplete]
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## Dimensionando para o Modo do Plano Padrão
<!-- ================================= -->

> [WACOM.NOTE] Antes de mudar um site do Plano de Hospedagem na Web para o modo Padrão, você deve remover os limites de gastos da sua assinatura de Sites do Microsoft Azure. Caso contrário, seu site correrá o risco de se tornar indisponível se você atingir os limites antes do término do período de cobrança. Para exibir ou alterar as opções de sua assinatura dos Sites do Microsoft Azure, consulte [Assinaturas do Microsoft Azure][azuresubscriptions].

1. Para dimensionar para Padrão, siga as mesmas etapas iniciais usadas para dimensionar para Compartilhado ou Básico e, em seguida, escolha **Padrão** para o **Modo do Plano de Hospedagem na Web** 
	
	![Escolher o Plano Padrão][ChooseStandard]
	
	Como anteriormente, a seção **Sites do Plano de Hospedagem na Web** apresenta uma pequena lista de sites no plano atual. Nesse caso, todos os sites no plano atual serão alterados para o modo Padrão.
	
2. A seleção de **Padrão** expande a seção **Capacidade** para revelar as opções **Tamanho da Instância** e **Contagem de Instâncias** que também estão disponíveis no modo Básico. As opções **Editar Configurações de Escala para Agenda** e **Escala por Métrica** estão disponíveis somente no modo Padrão.
	
	![Seção Capacidade no modo Padrão][CapacitySectionStandard]
	
3. Configure o **Tamanho da Instância**. As opções disponíveis são **Pequena**, **Média** ou **Grande**.
	
	![Escolher o tamanho da instância][ChooseInstanceSize]
	
	Para obter mais informações sobre esses tamanhos de instâncias, consulte [Tamanhos de máquina virtual e de serviço de nuvem do Microsoft Azure][vmsizes].
	
4. Se você desejar dimensionar automaticamente (autoescala) os recursos com base em dia ou noite, dia da semana versus fim de semana e/ou datas e horas específicas, escolha **Configurar Horas Agendadas** na opção **Editar Configurações de Escala para Agenda**.
	
	![Configurar horas agendadas][SetUpScheduleTimesButton]
	
5. A caixa de diálogo **Configurar horas agendadas** fornece várias opções de configuração úteis.
	
	![SetUpScheduleTimesDialog][SetUpScheduleTimesDialog]
	
6. Em **Agendamentos Recorrentes**, selecione **Diferentes escalas entre Dia e Noite** e/ou **Diferentes escalas entre Dia de semana e Fim de semana** para dimensionar os recursos com base em agendas separadas para o dia e a noite e/ou agendas separadas para dia da semana e finais de semana.
	
	> [WACOM.NOTE] Para o objetivo desse recurso, o fim de semana começa no final do dia de sexta-feira (20h00, por padrão) e termina no início do dia na segunda-feira (8h00, por padrão). O perfil de fim de semana usa o mesmo dia de início e de fim que você define na configuração de **Hora**.
	
7. Em **Hora**, escolha uma hora de início e de término para o dia em incrementos de meia hora e um fuso horário. Por padrão, o dia começa às 8:00 h e termina às 20:00 h. É respeitado o horário de verão para o fuso horário que você selecionar. 
	
8. Em **Datas Específicas**, você pode criar um ou mais períodos de tempo para os quais deseja dimensionar recursos. Por exemplo, você talvez queira fornecer recursos adicionais para um evento de vendas ou lançamento durante o qual haverá grandes picos no tráfego da web.
	
9. Depois de efetuar essas opções, clique em **OK** para fechar a caixa de diálogo **Horas Agendadas**.
	
10.   A caixa **Editar Configurações de Escala para Agenda** agora exibe agendas ou eventos configuráveis baseados nas alterações feitas. Selecione um dos agendamentos recorrentes ou datas específicas para configurá-lo. 
	
	![Editar configurações de escala para agenda][EditScaleSettingsForSchedule]
	
	Agora você pode usar os recursos **Escala por Métrica** e **Contagem de Instâncias** para ajustar o dimensionamento dos recursos para cada agenda escolhida. 
	
11.  Para ajustar dinamicamente o número de instâncias que seu site usará se sua carga for alterada, habilite a opção **Escala por Métrica** escolhendo **CPU**.
	
	![Escala por Métrica][ScaleByMetric]
	
	O gráfico mostra o número de instâncias que foram usadas na semana passada. Você pode usar o gráfico para monitorar a atividade de dimensionamento.
	
12. **Escala por Métrica** modifica o recurso **Contagem de Instâncias** para que você possa definir os números mínimo e máximo de máquinas virtuais a serem usados para o dimensionamento automático. O Azure nunca irá acima ou abaixo dos limites que você definir.
	
	![Contagem de instâncias][InstanceCount]
	
13. **Escala por métrica** também habilita a opção **CPU de Destino** para que você possa especificar um intervalo de destino para o uso da CPU. Este intervalo representa o uso médio da CPU pelo site. O Microsoft Azure adicionará ou removerá instâncias Standard para manter o site neste intervalo.
	
	![CPU de Destino][TargetCPU]
	
	**Observação**: quando a **Escala por Métrica** está habilitada, o Microsoft Azure verifica a CPU do seu site uma vez a cada cinco minutos e adiciona instâncias conforme necessário no momento. Se o uso da CPU estiver baixo, o Microsoft Azure removerá instâncias uma vez a cada duas horas para garantir que seu site permaneça com alto desempenho. Em geral, a contagem mínima de instância 1 é apropriada. No entanto, se houver aumentos repentinos no site, você deve certificar-se de que tem um número mínimo suficiente de instâncias para processar a carga. Por exemplo, se você tiver um aumento repentino de tráfego durante o intervalo de cinco minutos antes do Microsoft Azure verificar o uso da CPU, seu site poderá não responder durante esse período. Se você espera grandes quantidades de tráfego repentinas, defina a contagem mínima de instância adequadamente para prever essas explosões. 
	
14. Após concluir as alterações em todos os itens na lista **Editar Configurações de Escala para Agenda**, clique no ícone **Salvar** na barra de comandos na parte inferior da página para salvar todas as configurações de agendamento de uma vez (não é necessário salvar cada agenda individualmente).

<a name="ScalingSQLServer"></a>
##Dimensionando um Banco de Dados SQL Server conectado ao seu site	
Se você tiver um ou mais bancos de dados SQL Server vinculados ao seu site (independentemente do modo do plano de hospedagem na web), eles serão listados em uma seção **Recursos Vinculados** na parte inferior da página Colocação em Escala.

1. Para dimensionar um dos bancos de dados, na seção **Recursos Vinculados**, clique no link **Gerenciar escala para esse banco de dados** ao lado do nome do banco de dados.
	
	![Banco de dados vinculado][LinkedResources]
	
2. O link leva você para a guia SQL Server do Portal de Gerenciamento do Azure, onde você pode configurar a **Edição** e o **Tamanho Máximo** do banco de dados:
	
	![Dimensionar seu banco de dados SQL Server][ScaleDatabase]
	
	Em **Edição**, escolha **Web** ou **Business** dependendo da capacidade de armazenamento necessária. O **Web** edition oferece uma variedade de capacidades menores, enquanto o **Business** edition oferece uma variedade de capacidades maiores. 
	
	O valor que você escolher para **Tamanho Máximo** especificará um limite superior para o banco de dados. As cobranças de banco de dados são baseados na quantidade de dados que você realmente armazenar, portanto, a alteração da propriedade **Tamanho Máximo**, por si só, não afeta as cobranças do banco de dados. Para obter mais informações, consulte [Contas e faturamento no banco de dados SQL do Microsoft Azure][SQLaccountsbilling].

<a name="devfeatures"></a>
## Recursos de desenvolvedor
Dependendo do modo do plano de hospedagem na web, os seguintes recursos para desenvolvedores estarão disponíveis:

**Número de bits**

- Os modos dos planos Básico e Padrão oferecem suporte a aplicativos de 32 e 64 bits.
- O modos dos planos Gratuito e Compartilhado oferecem suporte apenas a aplicativos de 32 bits.

**Suporte ao depurador**

- Suporte ao depurador está disponível para os modos do plano de hospedagem na web Gratuito, Compartilhado e Básico em uma conexão simultânea por aplicativo.
- O suporte ao depurador está disponível para o modo do plano de hospedagem na web Padrão em cinco conexões simultâneas por aplicativo.

<a name="OtherFeatures"></a>
## Outros recursos

**Monitoramento do ponto de extremidade da web**

- O monitoramento de ponto de extremidade da web está disponível nos modos dos planos de hospedagem na web Básico e Padrão. Para obter mais informações sobre o monitoramento de ponto de extremidade da web, consulte [Como monitorar sites (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-monitor/).

- Para obter informações detalhadas sobre todos os demais recursos dos planos de hospedagem na web, incluindo preços e recursos de interesse para todos os usuários (incluindo desenvolvedores), consulte [Detalhes de preços de sites](http://www.windowsazure.com/pt-br/pricing/details/web-sites/).

<a name="Next Steps"></a>	
## Próximas etapas
- Para obter informações sobre as práticas recomendadas para Sites do Azure, incluindo a criação de uma arquitetura escalonável e resiliente, consulte [Práticas recomendadas: Sites do Windows Azure (WAWS) (a página pode estar em inglês)](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)

- Para obter informações sobre preços, suporte e SLA, visite os links a seguir.
	
	[Detalhes de preços de transferências de dados](http://www.windowsazure.com/pt-br/pricing/details/data-transfers/)
	
	[Planos de suporte do Microsoft Azure](http://www.windowsazure.com/pt-br/support/plans/)
	
	[Contratos de Nível de Serviço](http://www.windowsazure.com/pt-br/support/legal/sla/)
	
	[Detalhes de preços do banco de dados SQL](http://www.windowsazure.com/pt-br/pricing/details/sql-database/)
	
	[Tamanhos de máquina virtual e de serviço de nuvem do Microsoft Azure][vmsizes]
	
	[Detalhes de preços de sites](http://www.windowsazure.com/pt-br/pricing/details/web-sites/)
	
	[Detalhes de preços de Sites - Conexões SSL](http://www.windowsazure.com/pt-br/pricing/details/web-sites/#ssl-connections)

- Os links a seguir são vídeos sobre como dimensionar Sites do Azure:
	
	[Quando dimensionar Sites do Azure - com Stefan Schackow](http://www.windowsazure.com/pt-br/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	
	[Dimensionamento automático de Sites do Azure, CPU ou programado - com Stefan Schackow](http://www.windowsazure.com/pt-br/documentation/videos/auto-scaling-azure-web-sites/)

	[Como os Sites do Azure são dimensionados- com Stefan Schackow](http://www.windowsazure.com/pt-br/documentation/videos/how-azure-web-sites-scale/)



<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://manage.windowsazure.com/

<!-- IMAGES -->
[SelectWebsite]: ./media/web-sites-scale/01SelectWebSite.png
[SelectScaleTab]: ./media/web-sites-scale/02SelectScaleTab.png

[ChooseWHP]: ./media/web-sites-scale/03aChooseWHP.png
[ChooseBasicInstanceSize]: ./media/web-sites-scale/03bChooseBasicInstanceSize.png
[ChooseBasicInstanceCount]: ./media/web-sites-scale/04ChooseBasicInstanceCount.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ChooseStandard]: ./media/web-sites-scale/07ChooseStandard.png
[CapacitySectionStandard]: ./media/web-sites-scale/08CapacitySectionStandard.png
[ChooseInstanceSize]: ./media/web-sites-scale/09ChooseInstanceSize.png
[SetUpScheduleTimesButton]: ./media/web-sites-scale/10SetUpScheduleTimesButton.png
[SetUpScheduleTimesDialog]: ./media/web-sites-scale/11SetUpScheduleTimesDialog.png
[EditScaleSettingsForSchedule]: ./media/web-sites-scale/12EditScaleSettingsForSchedule.png
[ScaleByMetric]: ./media/web-sites-scale/13ScaleByMetric.png
[InstanceCount]: ./media/web-sites-scale/14InstanceCount.png
[TargetCPU]: ./media/web-sites-scale/15TargetCPU.png
[LinkedResources]: ./media/web-sites-scale/16LinkedResources.png
[ScaleDatabase]: ./media/web-sites-scale/17ScaleDatabase.png

