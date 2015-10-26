<properties
	pageTitle="Diretrizes de desempenho do Banco de Dados SQL do Azure para bancos de dados únicos"
	description="Este documento oferece diretrizes para ajudar você a determinar a camada de serviço adequada para seu aplicativo e oferece recomendações para ajustar seu aplicativo para obter o máximo proveito de seu Banco de Dados SQL do Azure."
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="10/13/2015"
	ms.author="jroth" />

# Diretrizes de desempenho do Banco de Dados SQL do Azure para bancos de dados únicos

## Visão geral

O Banco de Dados SQL do Microsoft Azure tem três [camadas de serviço](sql-database-service-tiers.md), Basic, Standard e Premium. Controlando estritamente a quantidade de recursos do seu Banco de Dados SQL do Azure e suas réplicas secundárias, a camada de serviço Premium oferece desempenho mais previsível para aplicativos de nuvem. O Banco de Dados SQL do Azure estende esse conceito para a camada de serviço Standard para oferecer alta previsibilidade de desempenho para bancos de dados com requisitos de desempenho inferiores que a camada de serviço Premium oferece. A camada de serviço Basic é projetada para preencher os requisitos de desempenho para bancos de dados de baixo custo.

>[AZURE.NOTE]As camadas de serviço Web e de negócios serão desativadas em setembro de 2015. Para saber mais, confira [Perguntas frequentes sobre a descontinuação das edições Web e Business](https://msdn.microsoft.com/library/azure/dn741330.aspx). Para obter informações detalhadas sobre como atualizar bancos de dados da Web e Business existentes para novas camadas de serviço, consulte [Atualizar banco de dados SQL Web/Business para novas camadas de serviço](sql-database-upgrade-new-service-tiers.md).

Este documento fornece orientação para ajudar você a determinar a camada de serviço adequada para seu aplicativo e fornece recomendações para ajustar seu aplicativo para obter o máximo proveito de seu Banco de Dados SQL do Azure.

>[AZURE.NOTE]Este artigo se concentra na orientação de desempenho para bancos de dados únicos no Banco de Dados SQL. Para obter as diretrizes de desempenho relacionadas aos pools de bancos de dados elásticos, consulte [Considerações de preço e desempenho para um pool de banco de dados elástico](sql-database-elastic-pool-guidance.md). No entanto, observe que muitas recomendações de ajuste neste artigo para um banco de dados único podem ser aplicadas aos bancos de dados em um pool elástico com benefícios de desempenho semelhante.

**Autores:** Conor Cunningham, Kun Cheng, Jan Engelsberg

**Revisores técnicos:** Morgan Oslake, Joanne Marone, Keith Elmore, José Batista-Neto, Rohit Nayak

## Histórico do Banco de Dados SQL do Azure

Para entender como as camadas de serviço Basic, Standard e Premium melhoram o serviço do Banco de Dados SQL do Azure, convém ter um bom entendimento geral do Banco de Dados SQL do Azure. Você pode escolher o Banco de Dados SQL do Azure por vários motivos. Um motivo é evitar o ciclo demorado de comprar e instalar o hardware. O Banco de Dados SQL do Azure permite criar e remover bancos de dados dinamicamente sem esperar a aprovação de uma ordem de compra, a chegada de máquinas, a atualização da energia e do resfriamento ou que a instalação seja feita. A Microsoft trata desses desafios e reduz significativamente o tempo necessário da ideia à solução pelo provisionamento prévio de hardware com base na demanda agregada em cada um dos nossos data centers. Isso pode poupar semanas ou meses para a sua empresa na compra e na implantação manual de hardware.

O Microsoft também inclui muitos recursos de gerenciamento automático no Banco de Dados SQL do Azure, como HA automática, balanceamento de carga e gerenciamento interno.

### A alta disponibilidade (HA) automática 
 O Banco de Dados SQL do Azure mantém pelo menos três réplicas para cada banco de dados de usuário e tem lógica para confirmar automaticamente cada alteração para um quorum de réplicas de forma síncrona. Isso garante que qualquer falha única de máquina não cause perda de dados. Além disso, cada réplica é colocada em racks de hardwares diferentes para que a perda de energia ou de switches de rede não afete o banco de dados. Por fim, há lógica para recriar automaticamente as réplicas caso um computador seja perdido, de forma que o sistema preserve as propriedades desejadas de integridade mesmo que um computador se torne não íntegro. Esses mecanismos impedem o processo demorado exigido hoje para a instalação e a configuração de soluções de alta disponibilidade. Ter uma solução de HA pré-configurada para seus dados elimina outro problema da criação de uma solução de banco de dados de missão crítica usando técnicas tradicionais.

### Balanceamento de carga 
 Ao contrário das máquinas virtuais tradicionais, o Banco de Dados SQL do Azure também contém um mecanismo para fazer o balanceamento em várias máquinas automaticamente. O Balanceador de Carga inspeciona automaticamente a utilização de recursos para um cluster e move as réplicas de banco de dados para computadores em cluster para compartilhar a carga entre muitos usuários dinamicamente. Isso amplia a capacidade sob demanda do banco de dados e permite que um usuário considere os requisitos de capacidade para cada banco de dados independentemente, desde que o balanceador de carga possa migrar bancos de dados ocupados para longe uns dos outros. Ao criar soluções que se estendam por vários bancos de dados, essa lógica fornecerá uma camada de abstração que permite que você se concentre nas necessidades de capacidade de cada banco de dados, em vez das limitações de tamanho específico de uma máquina virtual.

### Gerenciamento interno 
 O Banco de Dados SQL do Azure é executado como um serviço. Isso significa que há destinos de atividade definidos para cada banco de dados, evitando períodos de inatividade de manutenção demoradas. A Microsoft fornece uma solução de fornecedor único para o serviço, o que significa que há apenas uma empresa a ser chamada caso surja algum problema. A Microsoft também está atualizando continuamente o serviço, adicionando recursos, capacidade e encontrando maneiras de melhorar a experiência em cada atualização que fazemos. As atualizações ocorrem de modo transparente e sem períodos de inatividade, o que significa que elas são integradas ao nosso mecanismo normal de failover de HA. Isso permite tirar proveito dos novos recursos imediatamente sempre que anunciamos sua disponibilidade em vez de esperar um servidor a ser atualizado durante um período de inatividade futuro.

Todos esses recursos são fornecidos em todos os níveis de serviço, começando em uma faixa de preços de entrada baixo de alguns dólares por mês. Isso é muito menos do que custaria comprar e executar seu próprio servidor, o que significa que mesmo o menor dos projetos pode tirar proveito do Azure sem o gasto de muito dinheiro.

## O que é diferente nas camadas de serviço?

A Microsoft trabalhou em conjunto com alguns clientes durante a migração inicial para o Banco de Dados SQL do Azure para saber como eles usaram o serviço e levou lições à nossa equipe de engenharia para planejamento futuro de recursos. Durante as conversas, descobrimos que alguns tipos de clientes acharam que o conjunto de recursos também era adequado às necessidades deles. Por exemplo, as startups desenvolvendo novos serviços de nuvem com frequência acharam que a combinação da capacidade sob demanda e a sobrecarga administrativa reduzida simplificaram a vida delas e permitiram que elas se concentrassem no negócio principal. Outros clientes tinham desafios em algumas áreas relacionadas a requisitos rigorosos de desempenho, talvez para atender uma API central em uma solução de banco de dados grande e de várias camadas, que não eram atendidos pelo serviço do Banco de Dados SQL do Azure. Os comentários indicaram que alguns clientes estavam muito dispostos a aceitar a variação de desempenho superior para atingir uma faixa de preços muito baixos, outros clientes estavam mais interessados em garantias de desempenho específicas para que eles pudessem criar com mais facilidade um valor superior nesses bancos de dados.

Para atender a todas as suas necessidades, a Microsoft tem três camadas de serviço: Basic, Standard e Premium. Cada camada de serviço tem um ou mais níveis de desempenho que oferecem a capacidade de executar seus bancos de dados de maneira previsível. A capacidade é descrita no [Unidades de transação do banco de dados (DTUs)](sql-database-technical-overview.md#understand-dtus).

A camada de serviço Basic foi projetada para fornecer previsibilidade de bom desempenho para cada banco de dados hora a hora. A DTU de um banco de dados básico foi projetada para oferecer recursos suficientes para bancos de dados pequenos sem várias solicitações simultâneas para o desempenho ideal.

A camada de serviço padrão oferece melhor previsibilidade de desempenho e eleva o nível para bancos de dados com várias solicitações simultâneas, como aplicativos da Web e de grupo de trabalho. Usar um banco de dados de camada de serviço Standard permite que você dimensione seu aplicativo de banco de dados com base em desempenho previsível minuto a minuto.

O recurso de letreiro relacionado ao desempenho da camada de serviço Premium é o desempenho previsível segundo por segundo para cada banco de dados Premium. A utilização da camada de serviço Premium permite que você dimensione seu aplicativo de banco de dados com base na carga de pico desse banco de dados e remova os casos em que a variação de desempenho possa causar um atraso em consultas pequenas em operações sensíveis à latência. Esse modelo pode simplificar muito os ciclos de desenvolvimento e de validação de produtos necessários para aplicativos que precisam criar instruções fortes sobre as necessidades de recursos de pico, a variação de desempenho ou a latência da consulta. Ele pode também possibilita a migração de alguns aplicativos locais sem alterações significativas, pois essa é uma experiência mais próxima da experiência tradicional isolada presumida nesses aplicativos quando foram criados originalmente.

Como padrão, a camada de serviço Premium oferece a escolha de diferentes níveis de desempenho com base no isolamento desejado para um cliente.

As configurações de nível de desempenho no serviço Standard e Premium permitem que você pague apenas pela capacidade de que precisa e ajuste essa capacidade para cima ou para baixo à medida que uma carga de trabalho seja alterada. Por exemplo, se sua carga de trabalho do banco de dados for intensa durante a temporada de compras de volta às aulas, você poderá aumentar o nível de desempenho do banco de dados durante esse período e reduzi-lo após o término da janela de tempo de pico. Isso permite que você minimize o que pagará ao otimizar seu ambiente de nuvem para a periodicidade dos seus negócios. Esse modelo também funciona bem para ciclos de lançamento de produto de software. Uma equipe de teste pode alocar capacidade ao executar testes e liberar essa capacidade quando os testes forem concluídos. Essas solicitações de capacidade se adaptam bem ao modelo em que você pagaria pela capacidade conforme precisasse e evitaria gastos em recursos dedicados que raramente são usados. Isso proporciona uma experiência muito mais próxima ao modelo de hardware dedicado tradicional que muitos clientes da Microsoft têm usado com o SQL Server. Isso deve permitir que um conjunto maior de aplicativos seja executado com mais facilidade no Banco de Dados SQL do Azure.

Para saber mais sobre camadas de serviço, níveis de desempenho e DTUs, consulte [Camadas de serviço e níveis de desempenho do Banco de Dados SQL do Azure](sql-database-service-tiers.md).

## Motivos para usar as camadas de serviço

Embora cada carga de trabalho possa ser diferente, a finalidade das camadas de serviço é fornecer previsibilidade de alto desempenho em uma variedade de níveis de desempenho. Elas permitem que os clientes com uma grande escala de requisitos de recursos para seus bancos de dados trabalhem em um ambiente de computação mais dedicado.

### Casos de uso da camada de serviço Basic:

- **Introdução ao Banco de Dados SQL do Azure**: aplicativos em desenvolvimento geralmente não precisam de altos níveis de desempenho. Os bancos de dados Basic oferecem um ambiente ideal de desenvolvimento de banco de dados por uma faixa de preço baixos.
- **Banco de dados com um único usuário**: aplicativos que associam um único usuário a um banco de dados normalmente não têm requisitos de alta simultaneidade e desempenho. Os aplicativos com esses requisitos são candidatos à camada de serviço Basic.

### Casos de uso da camada de serviço Standard:

- **Banco de dados com várias solicitações simultâneas**: aplicativos que atendem a mais de um usuário ao mesmo tempo, como sites com tráfego moderado ou aplicativos departamentais que exigem uma quantidade maior de recursos são bons candidatos para a camada de serviço Standard.

### Casos de uso da camada de serviço Premium:

- **Alta carga de pico**: um aplicativo que requer muita CPU, memória ou E/S para concluir suas operações. Por exemplo, se uma operação de banco de dados sabidamente consumir vários núcleos de CPU por um longo período de tempo, isso é um candidato para bancos de dados Premium.Por exemplo, se uma operação de banco de dados sabidamente consumir vários núcleos de CPU por um longo período de tempo, isso é um candidato para bancos de dados Premium.
- **Muitas solicitações simultâneas**: alguns aplicativos de banco de dados atendem a muitas solicitações simultâneas, por exemplo, ao servir um site com alto volume de tráfego. As camadas de serviço Basic e Standard têm limites para a quantidade de solicitações simultâneas. Os aplicativos que exigem mais conexões precisariam escolher um tamanho de reserva apropriado para lidar com o número máximo de solicitações necessárias.
- **Baixa latência**: alguns aplicativos precisam garantir uma resposta do banco de dados em um tempo mínimo. Se um determinado procedimento armazenado for chamado como parte de uma operação de cliente mais ampla, poderá haver um requisito para retornar daquela chamada em não mais do que 20 milissegundos, 99% do tempo. Esse tipo de aplicativo se beneficiará de bancos de dados Premium para certificar-se de que a capacidade de computação está disponível.

O nível exato, que você precisará depende dos requisitos de carga de pico para cada dimensão de recurso. Alguns aplicativos podem usar quantidades comuns de um recurso, mas ter requisitos significativos em outro.

Para saber mais sobre as camadas de serviço, consulte [Camadas de serviço e níveis de desempenho do Banco de Dados SQL do Azure](sql-database-service-tiers.md).

## Noções básicas sobre o uso de recursos

O gráfico a seguir mostra os recursos da CPU para o banco de dados Premium com nível de desempenho P2 para cada hora em uma semana. Esse gráfico em particular começa na segunda-feira, mostrando 5 dias úteis e um fim de semana em que ocorrem menos atividades no aplicativo.

![Utilização de recursos do Banco de Dados SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Dos dados, atualmente esse banco de dados tem um pico de carga de CPU de pouco mais de 50% de utilização da CPU em relação ao nível de desempenho P2 (meio-dia de terça-feira). Se a CPU era o fator dominante no perfil de recursos do aplicativo, você poderá decidir que P2 é o nível de desempenho adequado para garantir que a carga de trabalho sempre se ajuste. Se for esperado que um aplicativo cresça com o tempo, fará sentido deixar algum buffer adicional do recurso para que o aplicativo nunca atinja o limite máximo. Isso ajudará a evitar erros visíveis ao cliente causados pelo banco de dados não tem capacidade suficiente para processar solicitações de modo eficaz, especialmente em ambientes sensíveis à latência (como um banco de dados oferece suporte a um aplicativo que pinta páginas da web com base nos resultados de chamadas de banco de dados).

Vale a pena observar que outros tipos de aplicativos podem interpretar o mesmo gráfico diferente.Vale a pena observar que outros tipos de aplicativos podem interpretar o mesmo gráfico diferente. Por exemplo, se um aplicativo tentar processar dados de folha de pagamento por dia e incluir o mesmo gráfico, esse tipo de modelo de "trabalho em lotes" pode fazer muito bem em um nível de desempenho P1.Por exemplo, se um aplicativo tentar processar dados de folha de pagamento por dia e incluir o mesmo gráfico, esse tipo de modelo de "trabalho em lotes" pode fazer muito bem em um nível de desempenho P1. O nível de desempenho P1 tem 100 DTUs em relação a 200 DTUs do nível de desempenho P2.O nível de desempenho P1 tem 100 DTUs em relação a 200 DTUs do nível de desempenho P2. Isso significa que o nível de desempenho P1 fornece metade do desempenho do nível de desempenho P2.Isso significa que o nível de desempenho P1 fornece metade do desempenho do nível de desempenho P2. Até 50% da utilização da CPU em P2 equivale a 100% de utilização da CPU em P1.Até 50% da utilização da CPU em P2 equivale a 100% de utilização da CPU em P1. Desde que o aplicativo não têm tempo limite, pode não importar se um trabalho grande demorar 2 horas ou 2,5 horas para ser concluído, desde que seja feito hoje.Desde que o aplicativo não têm tempo limite, pode não importar se um trabalho grande demorar 2 horas ou 2,5 horas para ser concluído, desde que seja feito hoje. Um aplicativo nessa categoria provavelmente só pode usar um nível de desempenho P1.Um aplicativo nessa categoria provavelmente só pode usar um nível de desempenho P1. Você pode tirar proveito do fato de que há períodos de tempo durante o dia em que o uso de recursos é menor, o que significa que um "pico maior" poderá extrapolar para um dos ciclos mais tarde. Talvez o nível de desempenho P1 seja ótimo para esse aplicativo (e economize dinheiro), já que os trabalhos podem ser concluídos no horário todos os dias.

O Banco de Dados SQL do Azure expõe informações de recurso consumido para cada banco de dados ativo na exibição **sys.resource\_stats** do banco de dados master em cada servidor. Os dados na tabela são agregados em intervalos de 5 minutos. Com as camadas de serviço Basic, Standard e Premium, os dados podem demorar mais de 5 minutos para aparecer na tabela, o que significa que esses dados são melhores para análise histórica do que para a análise em tempo real. A consulta à exibição **sys.resource\_stats** mostra o histórico recente de um banco de dados a ser validado caso o pico de reserva tenha oferecido o desempenho desejado quando necessário. O exemplo a seguir demonstra como os dados são expostos nessa exibição:

	SELECT TOP 10 * 
	FROM sys.resource_stats 
	WHERE database_name = 'resource1' 
	ORDER BY start_time DESC

![estatísticas de recursos do sistema](./media/sql-database-performance-guidance/sys_resource_stats.png)

>[AZURE.NOTE]Algumas colunas da tabela foram truncadas para obtenção de espaço. Consulte o tópico [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) para obter uma descrição completa da saída.

## Como monitorar o uso de recursos

Esta seção descreve maneiras para monitorar o uso de recursos do Banco de Dados SQL do Azure e comparar a utilização de recursos atual com diferentes níveis de desempenho.

1. A exibição do catálogo **sys.resource\_stats** é aprimorada com informações mais históricas de uso de recursos no nível do banco de dados. Por exemplo, para pesquisar o uso de recursos da semana passada para o banco de dados, "userdb1", você poderá executar a seguinte consulta.
	
		SELECT * 
		FROM sys.resource_stats 
		WHERE database_name = 'userdb1' AND 
		      start_time > DATEADD(day, -7, GETDATE())
		ORDER BY start_time DESC;
	
2. Para avaliar como sua carga de trabalho se ajusta ao nível de desempenho, terá de detalhar cada aspecto diferente da métrica de recursos: CPU, leituras, gravação, número de trabalhadores e número de sessões. Veja uma consulta revisada usando sys.resource\_stats para relatar os valores médio e máximo dessa métrica de recurso.
	
		SELECT 
		    avg(avg_cpu_percent) AS 'Average CPU Utilization In Percent',
		    max(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent',
		    avg(avg_physical_data_read_percent) AS 'Average Physical Data Read Utilization In Percent',
		    max(avg_physical_data_read_percent) AS 'Maximum Physical Data Read Utilization In Percent',
		    avg(avg_log_write_percent) AS 'Average Log Write Utilization In Percent',
		    max(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent',
		    avg(active_session_count) AS 'Average # of Sessions',
		    max(active_session_count) AS 'Maximum # of Sessions',
		    avg(active_worker_count) AS 'Average # of Workers',
		    max(active_worker_count) AS 'Maximum # of Workers'
		FROM sys.resource_stats 
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
	
3. Com as informações acima de valores médio e máximo de cada métrica de recurso, você poderá avaliar como sua carga de trabalho se ajusta ao nível de desempenho escolhido. Na maioria dos casos, os valores médios de sys. resource\_stats oferecem uma boa referência a ser usada com o tamanho de destino. Deve ser seu cartão de medida principal. Por exemplo, se você estiver usando a camada de serviço Standard com nível de desempenho S2, as porcentagens de utilização média de CPU, as leituras e as gravações estejam abaixo de 40%, o número médio de trabalhadores for inferior a 50 e o número médio de sessões for inferior a 200, sua carga de trabalho talvez se ajuste ao nível de desempenho S1. É fácil ver se o banco de dados se ajusta aos limites de sessão e de trabalho. Para ver se um banco de dados se enquadra em um nível de desempenho inferior em termos de CPU, leituras e gravações, você poderá dividir o número de DTUS do nível de desempenho inferior pelo número de DTUS do nível de desempenho atual e multiplicar o resultado por 100:
	
	**S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
	
	O resultado é a diferença de desempenho relativo entre os dois níveis de desempenho em porcentagem. Se sua utilização não exceder essa porcentagem, sua carga de trabalho talvez se ajuste ao nível de desempenho inferior. No entanto, você precisará examinar todos os intervalos de valores de uso de recursos e determinar, em relação à porcentagem, com que frequência a carga de trabalho do banco de dados se ajustaria ao nível de desempenho inferior. A consulta a seguir produz a porcentagem de ajuste por dimensão de recurso com base no limite de 40% calculado acima.
	
		SELECT 
		    (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
		    ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
		    ,(COUNT(database_name) - SUM(CASE WHEN avg_physical_data_read_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data Read Fit Percent'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
	
	Com base no seu objetivo de nível de serviço (SLO) do banco de dados, você pode decidir se sua carga de trabalho se enquadra no nível de desempenho inferior. Se sua carga de trabalho do banco de dados SLO for de 99,9% e a consulta acima retornar valores superiores a 99,9 para todas as três dimensões, é muito provável que sua carga de trabalho se ajuste ao nível de desempenho inferior.
	
	O exame da porcentagem de ajuste também oferece informações que indicam se você precisará ir para o próximo nível de desempenho superior para atender ao seu SLO. Por exemplo, "userdb1" mostra a seguinte utilização para a semana passada.
	
	| Porcentagem média da CPU | Porcentagem máxima de CPU |
	|---|---|
	| 24,5 | 100,00 |
	
	A Média de CPU é de aproximadamente um quarto do limite do nível de desempenho, o que se ajusta bem ao nível de desempenho do banco de dados. No entanto, o valor máximo mostra que o banco de dados atinge o limite do nível de desempenho. Você precisa ir para o próximo nível mais alto de desempenho? Novamente, você precisará examinar quantas vezes sua carga de trabalho atinge 100% e a compara ao SLO da carga de trabalho do seu banco de dados.
	
		SELECT 
		(COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
		,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent’
		,(COUNT(database_name) - SUM(CASE WHEN avg_physical_data_read_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data Read Fit Percent'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
	
	Se a consulta acima retornar um valor inferior a 99,9 em qualquer uma das três dimensões, você deverá considerar a ida para o próximo nível mais alto de desempenho ou usar técnicas de ajuste de aplicativo para reduzir a carga no Banco de Dados SQL do Azure.
	
4. O exercício acima também deve levar em consideração o aumento da carga de trabalho projetada no futuro.

## Ajustando seu aplicativo

No SQL Server local tradicional, o processo de planejamento da capacidade inicial geralmente é separado do processo de execução de um aplicativo em produção. Em outras palavras, a compra de hardware e das licenças associadas para executar o SQL Server é feita antecipadamente, enquanto o ajuste de desempenho é feito depois. Ao usar o Banco de Dados SQL do Azure, geralmente é recomendável (e, como você será cobrado mensalmente, provavelmente é desejável) intercalar o processo de execução e ajuste de um aplicativo. O modelo de pagamento pela capacidade sob demanda permite que você ajuste seu aplicativo para usar os recursos mínimos necessários no momento, em vez de provisionar excessivamente o hardware com base em palpites sobre os planos de crescimento futuro para um aplicativo (que geralmente estão incorretos, já que precisam prever o futuro). Observe que alguns clientes podem optar por não ajustar um aplicativo, mas provisionar excessivamente os recursos de hardware. Essa abordagem poderá fazer sentido quando você não quiser alterar o aplicativo principal durante um período ocupado do aplicativo. O ajuste de um aplicativo pode minimizar os requisitos de recurso e diminuir as contas mensais ao usar as camadas de serviço no Banco de Dados SQL do Azure.

### Características do aplicativo

Embora as camadas de serviço sejam projetadas para melhorar a estabilidade e a previsibilidade do desempenho para um aplicativo, existem algumas práticas recomendadas para ajustar seu aplicativo para aproveitar melhor o recurso. Embora vários aplicativos vejam ganhos de desempenho significativos simplesmente alterando para um nível mais alto de desempenho e/ou camada de serviço, nem todos os aplicativos poderão ser tão beneficiados sem o ajuste adicional. Os aplicativos com as características a seguir também devem considerar o ajuste para melhorar ainda mais o desempenho ao usar o Banco de Dados SQL do Azure.

- **Aplicativos com um desempenho lento devido ao comportamento "tagarela"**: isso inclui aplicativos que geram operações de acesso de dados sensíveis à latência de rede. Tais aplicativos podem exigir modificação para reduzir o número de operações de acesso de dados no Banco de Dados SQL do Azure. Por exemplo, o aplicativo pode ser melhorado usando técnicas como o envio em lote de consultas ad hoc juntas ou a movimentação das consultas em procedimentos armazenados. Para saber mais, consulte a seção 'Consultas em lote' a seguir.
- **Bancos de dados com uma carga de trabalho intensiva que não têm suporte de um único computador inteiro**: bancos de dados que excedem os recursos do nível mais alto de desempenho Premium não são bons candidatos. Esses bancos de dados podem se beneficiar da expansão da carga de trabalho. Para saber mais, consulte as seções 'Fragmentação entre banco de dados' e 'Particionamento funcional' a seguir.
- **Aplicativos que contêm consultas não ideais**: os aplicativos, especialmente na camada de acesso a dados, que tenham consultas mal ajustadas talvez não se beneficiem da seleção de um nível mais alto de desempenho como o esperado. Isso inclui consultas sem uma cláusula WHERE, com índices ausentes ou com estatísticas desatualizadas. Esses aplicativos serão beneficiados com técnicas de ajuste de desempenho de consulta padrão. Para saber mais, consulte as seções 'Índices ausentes' e ' Ajuste/dicas de consulta ' a seguir.
- **Aplicativos com design de acesso a dados não ideal**: os aplicativos com problemas de simultaneidade de acesso de dados intrínsecos, por exemplo, deadlock, talvez não se beneficiem da seleção de um nível mais alto de desempenho. Os desenvolvedores de aplicativos devem considerar a redução de viagens de ida e volta ao Banco de Dados SQL do Azure pelo cache de dados no lado do cliente usando o serviço de Cache do Azure ou outras tecnologias de cache. Consulte a seção Cache da camada de aplicativo a seguir.

## Técnicas de ajuste

Esta seção explica algumas técnicas que você pode usar para ajustar o Banco de Dados SQL do Azure para obter o melhor desempenho do seu aplicativo e ser capaz de executar no menor nível de desempenho possível. Diversas técnicas correspondem às tradicionais práticas recomendadas de ajuste do SQL Server, mas algumas técnicas são específicas do Banco de Dados SQL do Azure. Em alguns casos, as técnicas tradicionais do SQL Server podem ser estendidas para funcionarem também no Banco de Dados SQL do Azure, examinando os recursos consumidos para um banco de dados localizar áreas para ajustar ainda mais.

### Índices ausentes
Um problema comum no desempenho do banco de dados OLTP está relacionado ao design do banco de dados físico. Com frequência, os esquemas de banco de dados são projetados e enviados sem testes em escala (seja na carga ou no volume de dados). Infelizmente, o desempenho de um plano de consulta pode ser aceitável em pequena escala, mas pode ser substancialmente comprometido ao se deparar com volumes de dados de nível de produção. A fonte mais comum desse problema deve-se à falta de índices apropriados para satisfazer filtros ou outras restrições em uma consulta. Geralmente, isso se manifesta como uma verificação de tabela quando uma busca de índice poderia bastar.

O exemplo a seguir cria um caso em que o plano de consulta selecionado contém uma verificação quando uma pesquisa seria suficiente.

	DROP TABLE dbo.missingindex;
	CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
	DECLARE @a int = 0;
	SET NOCOUNT ON;
	BEGIN TRANSACTION
	WHILE @a < 20000
	BEGIN
	    INSERT INTO dbo.missingindex(col2) VALUES (@a);
	    SET @a += 1;
	END
	COMMIT TRANSACTION;
	GO
	SELECT m1.col1 
	FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1) 
	WHERE m1.col2 = 4;

![plano de consulta com índices ausentes](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

O Banco de Dados SQL do Azure contém funcionalidade para ajudar os administradores de banco de dados sobre como localizar e corrigir condições comuns de índice ausente. As exibições de gerenciamento dinâmico (DMVs) incorporadas ao Banco de Dados SQL do Azure examinam a compilação da consulta em que um índice reduziria significativamente o custo estimado para executar uma consulta. Durante a execução da consulta, ela controla com que frequência cada plano de consulta é executado, bem como o intervalo estimado entre o plano de consulta em execução e o imaginado onde esse índice existia. Isso permite que um administrador de banco de dados adivinhe rapidamente quais alterações de design de banco de dados físico podem melhorar a carga de trabalho geral para um determinado banco de dados e sua carga de trabalho real.

A consulta a seguir pode ser usada para avaliar potenciais índices ausentes.

	SELECT CONVERT (varchar, getdate(), 126) AS runtime, 
	    mig.index_group_handle, mid.index_handle, 
	    CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact * 
	            (migs.user_seeks + migs.user_scans)) AS improvement_measure, 
	    'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' + 
	              CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + ' 
	              (' + ISNULL (mid.equality_columns,'') 
	              + CASE WHEN mid.equality_columns IS NOT NULL 
	                          AND mid.inequality_columns IS NOT NULL 
	                     THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
	              + ')' 
	              + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement, 
	    migs.*, 
	    mid.database_id, 
	    mid.[object_id]
	FROM sys.dm_db_missing_index_groups AS mig
	INNER JOIN sys.dm_db_missing_index_group_stats AS migs 
	    ON migs.group_handle = mig.index_group_handle
	INNER JOIN sys.dm_db_missing_index_details AS mid 
	    ON mig.index_handle = mid.index_handle
	ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

Neste exemplo, sugere-se o índice a seguir.

	CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Depois de criada, a mesma instrução SELECT agora escolhe um plano diferente que usa uma busca em vez de um exame, com execução mais eficiente como mostrado no plano de consulta a seguir.

![plano de consulta com índices corrigidos](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

A ideia principal é que a capacidade de E/S de um sistema de mercadorias compartilhado é geralmente mais limitada do que uma máquina de servidor dedicado. Assim, há um bônus na minimização de E/S desnecessária para tirar o máximo proveito do sistema na DTU de cada nível de desempenho das camadas de serviço no Banco de Dados SQL do Azure. As opções de design de banco de dados físico apropriadas podem melhorar significativamente a latência para consultas individuais, a taxa de transferência de solicitações simultâneas que você pode tratar por unidade de escala e minimizar os custos necessários para satisfazer a consulta. Para saber mais sobre os DMVs de índice ausente, consulte [sys.dm\_db\_missing\_index\_details](https://msdn.microsoft.com/library/ms345434.aspx).

### Ajustes/dicas de consulta
O Otimizador de Consulta do Banco de Dados SQL do Azure é muito semelhante ao Otimizador de Consulta do SQL Server tradicional. Muitas das práticas recomendadas para ajuste de consultas e a compreensão das limitações do modelo de raciocínio para o Otimizador de Consulta também se aplicam ao Banco de Dados SQL do Azure. O ajuste de consultas no Banco de Dados SQL do Azure pode ter o benefício adicional de reduzir as demandas de recursos agregados e permitir que um aplicativo seja executado com um custo menor do que um equivalente sem ajuste porque pode ser executado em um nível de desempenho inferior.

Um exemplo comum observado no SQL Server que também se aplica ao Banco de Dados SQL do Azure está relacionado ao modo como os parâmetros são "detectados" durante a compilação para tentar criar um plano mais ideal. A detecção de parâmetros é um processo com que o otimizador de consulta considera o valor atual de um parâmetro ao criar uma consulta com o objetivo de gerar um plano de consulta mais ideal. Embora muitas vezes essa estratégia possa levar a um plano de consulta que seja significativamente mais rápido do que um plano compilado sem o conhecimento dos valores de parâmetro, o comportamento atual do SQL Server/Banco de Dados SQL do Azure é imperfeito – há casos em que o parâmetro não é detectado e há casos em que o parâmetro é detectado, mas o plano gerado está abaixo do ideal para o conjunto completo de valores de parâmetro em uma carga de trabalho. A Microsoft inclui dicas de consulta (diretivas) para permitir que você especifique a intenção mais deliberadamente e substitua o comportamento padrão para detecção de parâmetro. Muitas vezes, o uso de dicas pode corrigir casos em que o comportamento padrão do banco de dados do SQL Server/Banco de Dados SQL do Azure é imperfeito para uma carga de trabalho específica do cliente.

O exemplo a seguir demonstra como o processador de consultas pode gerar um plano ideal para os requisitos de desempenho e de recurso e como o uso de uma dica de consulta pode reduzir os requisitos de recursos e de tempo de execução de consulta no Banco de Dados SQL do Azure.

A seguir, uma configuração de exemplo.

	DROP TABLE psptest1;
	CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
	
	DECLARE @a int = 0;
	SET NOCOUNT ON;
	BEGIN TRANSACTION
	WHILE @a < 20000
	BEGIN
	    INSERT INTO psptest1(col2) values (1);
	    INSERT INTO psptest1(col2) values (@a);
	    SET @a += 1;
	END
	COMMIT TRANSACTION
	CREATE INDEX i1 on psptest1(col2);
	GO
	
	CREATE PROCEDURE psp1 (@param1 int)
	AS
	BEGIN
	    INSERT INTO t1 SELECT * FROM psptest1 
	    WHERE col2 = @param1
	    ORDER BY col2;
	END
	GO
	
	CREATE PROCEDURE psp2 (@param2 int)
	AS
	BEGIN
	    INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
	    ORDER BY col2
	    OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
	END
	GO
	
	CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
	GO

O código de configuração cria uma tabela que contém a distribuição de dados distorcida. O plano de consulta ideal varia dependendo do parâmetro selecionado. Infelizmente, o comportamento do cache de plano nem sempre recompila a consulta com base no valor do parâmetro mais comuns, que significa que é possível para um plano abaixo do ideal seja armazenado em cache e usado para vários valores, mesmo quando um plano diferente for uma opção de plano médio melhor. Em seguida, ele cria dois procedimentos armazenados idênticos, exceto que um contém uma dica de consulta especial (raciocínio explicado abaixo).

**Exemplo (parte 1):**

	-- Prime Procedure Cache with scan plan
	EXEC psp1 @param1=1;
	TRUNCATE TABLE t1;
	
	-- Iterate multiple times to show the performance difference
	DECLARE @i int = 0;
	WHILE @i < 1000
	BEGIN
	    EXEC psp1 @param1=2;
	    TRUNCATE TABLE t1;
	    SET @i += 1;
	END

**Exemplo (parte 2 – aguarde 10 minutos antes de experimentar esta parte para que ela seja obviamente diferente nos dados de telemetria resultantes):**

	EXEC psp2 @param2=1;
	TRUNCATE TABLE t1;
	
	DECLARE @i int = 0;
	WHILE @i < 1000
	BEGIN
	    EXEC psp2 @param2=2;
	    TRUNCATE TABLE t1;
	    SET @i += 1;
	END

Cada parte deste exemplo tenta executar uma instrução insert com parâmetros 1000 vezes (para gerar a carga suficiente para ser interessante em um conjunto de dados de teste). Ao executar procedimentos armazenados, o processador de consultas examina o valor do parâmetro passado ao procedimento durante a primeira compilação (conhecido como "detecção" de parâmetros). O plano resultante é armazenado em cache e usado para invocações posteriores, mesmo que o valor do parâmetro seja diferente. Como resultado, é possível que o plano ideal não seja usado em todos os casos. Às vezes, os clientes precisam orientar o otimizador a escolher um plano que seja melhor para o caso médio em vez de um caso específico quando a consulta tiver sido compilada pela primeira vez. Neste exemplo, o plano inicial gerará um plano de "verificação" que lê todas as linhas para localizar cada valor que corresponda ao parâmetro.

![Ajuste de Consulta](./media/sql-database-performance-guidance/query_tuning_1.png)

Como executamos o procedimento com o valor 1, o plano resultante era ideal para 1, mas abaixo do ideal para todos os outros valores da tabela. O comportamento resultante provavelmente não será o comportamento desejado se você escolher cada plano aleatoriamente, já que o plano terá um desempenho mais lento e consumirá mais recursos em sua execução.

A execução do teste com "SET STATISTICS IO ON" mostra o trabalho de verificação de lógica feito neste exemplo nos bastidores – você pode ver que há 1148 leituras feitas pelo plano (que será ineficiente se o caso comum retornar apenas uma linha).

![Ajuste de Consulta](./media/sql-database-performance-guidance/query_tuning_2.png)

A segunda parte do exemplo usa uma dica de consulta para dizer ao otimizador para usar um valor específico durante o processo de compilação. Nesse caso, ele força o processador de consultas a ignorar o valor passado como o parâmetro e, em vez disso pressupor um "UNKNOWN", o que significa um valor com a frequência média na tabela (ignorando a distorção). O plano resultante é um plano baseado na busca que será mais rápida e usa menos recursos, em média, do que o plano da parte 1 do exemplo.

![Ajuste de Consulta](./media/sql-database-performance-guidance/query_tuning_3.png)

Você poderá ver o impacto disso ao examinar a tabela **sys.resource\_stats** (observação: haverá um atraso entre o tempo de execução de teste até a hora em que os dados são preenchidos na tabela). Para este exemplo, a parte 1 foi executada durante a janela de tempo 22:25:00 e a parte 2 foi executada em 22:35:00. Observe que a janela de tempo anterior usou mais recursos nessa janela de tempo do que a mais recente (devido a melhorias na eficiência do plano).

	SELECT TOP 1000 * 
	FROM sys.resource_stats 
	WHERE database_name = 'resource1' 
	ORDER BY start_time DESC

![Ajuste de Consulta](./media/sql-database-performance-guidance/query_tuning_4.png)

>[AZURE.NOTE]Embora o exemplo usado aqui seja propositalmente pequeno, o impacto de parâmetros abaixo do ideal pode ser significativo, especialmente em bancos de dados maiores. A diferença, em casos extremos, pode ficar entre segundos e horas para os casos rápidos e lentos.

Você pode examinar **sys.resource\_stats** para determinar se o recurso para um determinado teste usa mais ou menos recursos do que outro teste. Ao comparar dados, separe os testes por um tempo suficiente para que eles não sejam agrupados na mesma janela de tempo de 5 minutos na exibição **sys.resource\_stats**. Além disso, observe que o objetivo do exercício é minimizar o total de recursos usados, não minimizar os recursos de pico. Em geral, a otimização de uma parte do código de latência também reduz o consumo de recursos. Certifique-se de que as alterações consideradas em qualquer aplicativo sejam realmente necessárias e não afetem negativamente a experiência do cliente para qualquer pessoa que use um aplicativo ao usar dicas de consulta.

Se uma carga de trabalho contiver um conjunto de consultas repetidas, geralmente fará sentido capturar e validar a natureza ideal dessas opções de plano, pois provavelmente ela orientará a unidade de tamanho mínima de recursos necessária para hospedar o banco de dados. Depois da validação, reexamine ocasionalmente os planos para ter certeza de que não tiveram degradação. Para saber mais sobre as dicas de consulta, consulte [Dicas de consulta (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### Fragmentação entre bancos de dados
Como o Banco de Dados SQL do Azure é executado no hardware de mercadoria, geralmente há limites inferiores de capacidade para um único banco de dados em relação a uma instalação do SQL Server local tradicional. Portanto, há um número de clientes que usa técnicas de fragmentação para distribuir as operações de banco de dados por vários bancos de dados quando elas não se ajustarem aos limites de um único Banco de Dados SQL do Azure. A maioria dos clientes que usam as técnicas de fragmentação atualmente no Banco de Dados SQL do Azure divide seus dados em uma única dimensão entre vários bancos de dados. A abordagem envolve compreender que os aplicativos OLTP executam com frequência as transações que se aplicam somente a uma linha ou a um pequeno grupo de linhas no esquema. Por exemplo, se um banco de dados contiver o cliente, o pedido e os detalhes do pedido (como mostra o exemplo tradicional da Northwind fornecido no SQL Server), então esses dados poderão ser divididos em vários bancos de dados agrupando um cliente com o pedido relacionado e as informações de detalhes do pedido e garantindo que ele permaneça em um único banco de dados. O aplicativo dividiria clientes diferentes entre os bancos de dados, distribuindo efetivamente a carga entre vários bancos de dados. Isso permite que os clientes não apenas evitem o limite de tamanho máximo do banco de dados, como também permite que o Banco de Dados SQL do Azure processe cargas de trabalho significativamente maiores do que os limites de diferentes níveis de desempenho, desde que cada banco de dados individual se ajuste à sua DTU.

Embora a fragmentação do banco de dados não reduza a capacidade agregada de recursos para uma solução, essa técnica é altamente eficaz para dar suporte a soluções muito grandes distribuídas entre vários bancos de dados e permite que cada banco de dados seja executado em um nível de desempenho diferentes para oferecer suporte a bancos de dados "efetivos" muito grandes com altos requisitos de recursos.

Observe que o Banco de Dados SQL do Azure agora fornece uma biblioteca para ajudar você com a fragmentação. Para saber mais, consulte [Visão geral sobre a biblioteca de clientes do Banco de Dados Elástico](sql-database-elastic-database-client-library.md).

### Particionamento funcional
Com frequência, os usuários do SQL Server combinam muitas funções dentro de um único banco de dados. Por exemplo, se um aplicativo contiver a lógica para gerenciar o estoque de uma loja, esse banco de dados poderá conter lógica associada ao controle de inventário, ordens de compra, procedimentos armazenados e exibições indexadas/materializadas que gerenciavam os relatórios de final de mês e outras funções. Essa técnica oferece o benefício de poder administrar facilmente o banco de dados para operações como BACKUP, mas também requer que você dimensione o hardware para lidar com a carga de pico em todas as funções de um aplicativo.

Dentro de uma arquitetura de expansão usada dentro do Banco de Dados SQL do Azure, geralmente é útil dividir funções diferentes de um aplicativo por bancos de dados diferentes. Isso permite que cada uma delas seja dimensionada de forma independente. À medida que um aplicativo se torna mais ocupado (e recebe mais carga no banco de dados), isso permite que o administrador escolha níveis de desempenho de forma independente para cada função em um aplicativo. No limite, essa arquitetura permite que um aplicativo fique maior do que um único computador de mercadoria possa lidar ao distribuir a carga por várias máquinas.

### Consultas em lote
Para os aplicativos que acessam dados na forma de consultas ad-hoc frequentes, uma grande parte do tempo de resposta é gasto na comunicação de rede entre a camada de aplicativo e a camada do Banco de Dados SQL. Mesmo quando o aplicativo e o Banco de Dados SQL do Azure residir nos mesmos dados centers, a latência da rede entre os dois poderá ser aumentada por um grande número de dados de operações de acesso. Para reduzir as viagens de ida e volta de rede para acesso a essas operações de dados, o desenvolvedor do aplicativo deverá considerar as opções de processamento em lote de consultas ad hoc ou a compilação delas em procedimentos armazenados. O processamento em lote de consultas ad hoc pode enviar várias consultas como um lote grande em uma única viagem ao Banco de Dados SQL do Azure. A compilação de consultas ad hoc em um procedimento armazenado pode alcançar o mesmo resultado do lote. O uso de um procedimento armazenado também oferece o benefício de aumentar as chances de armazenamento em cache dos planos de consulta no Banco de Dados SQL do Azure para execuções subsequentes do mesmo procedimento armazenado .

Alguns aplicativos apresentam gravação intensa. Às vezes, é possível reduzir a carga de E/S total em um banco de dados ao considerar como gravar em lote. Isso costuma ser tão simples quanto usar transações explícitas em vez de transações de confirmação automática em procedimentos armazenados e lotes ad hoc. Veja uma avaliação das técnicas diferentes que podem ser usadas em [Técnicas de envio em lote para aplicativos do Banco de Dados SQL no Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Experimente sua própria carga de trabalho para encontrar o modelo certo para o envio em lote, tomando cuidado para entender que um determinado modelo pode ter garantias de consistência transacional ligeiramente diferentes. A localização da carga de trabalho certa que minimize o uso de recursos exige a localização da combinação correta de compensações de consistência e de desempenho.

### Cache de camada de aplicativo
Alguns aplicativos de banco de dados contêm cargas de trabalho de leitura pesada. É possível utilizar camadas de cache para reduzir a carga no banco de dados e reduzir o nível de desempenho necessário para dar suporte a um banco de dados usando o Banco de Dados SQL do Azure. O [Cache Redis do Azure](https://azure.microsoft.com/services/cache) permite que um cliente com uma carga de trabalho de leitura pesada leia os dados de uma vez (ou talvez uma vez por computador de camada de aplicativo, dependendo de como estiver configurado) e armazene esses dados fora do Banco de Dados SQL do Azure. Isso oferece a capacidade de reduzir a carga do banco de dados (CPU e E/S de leitura), mas há um impacto na consistência transacional, já que os dados lidos do cache podem ser atualizados com os dados no banco de dados. Embora haja muitos aplicativos onde uma quantidade de inconsistência seja aceitável, isso não é verdadeiro para todas as cargas de trabalho. Compreenda completamente quaisquer requisitos de aplicativo antes de empregar uma estratégia de armazenamento da camada de aplicativo em cache.

## Conclusão

As camadas de serviço no Banco de Dados SQL do Azure capacita você a elevar o nível nos tipos de aplicativos criados na nuvem. Quando combinadas ao ajuste cuidadoso do aplicativo, você poderá obter o desempenho ideal e previsível para seu aplicativo. Este documento descreve as técnicas recomendadas para otimizar o consumo de recursos do banco de dados para o ajuste adequado a um dos níveis de desempenho. O ajuste é um exercício contínuo no modelo de nuvem e as camadas de serviço e seus níveis de desempenho permitem aos administradores maximizar o desempenho e minimizar os custos na plataforma Microsoft Azure.

<!---HONumber=Oct15_HO3-->