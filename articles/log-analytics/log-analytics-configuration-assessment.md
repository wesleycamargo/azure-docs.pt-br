<properties
	pageTitle="Solução de Avaliação de Configuração no Log Analytics | Microsoft Azure"
	description="A solução de Avaliação de Configuração no Log Analytics fornece informações detalhadas sobre o estado atual da sua infraestrutura de servidor do System Center Operations Manager ao usar agentes ou um grupo de gerenciamento do Operations Manager."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="banders"/>

# Solução de Avaliação de Configuração no Log Analytics

A solução de Avaliação de Configuração no Log Analytics ajuda você a localizar possíveis problemas de configuração de servidor por meio de alertas e recomendações de conhecimento.

Essa solução requer o System Center Operations Manager. A Avaliação de Configuração não estará disponível se você usar apenas agentes conectados diretamente.

Exibir algumas informações na solução de Avaliação de Configuração requer o plug-in do Silverlight para o navegador.

>[AZURE.NOTE] A solução de Avaliação de Configuração só está disponível para os espaços de trabalho na região Leste dos EUA e não será adicionada às demais regiões. Os recursos da solução de Avaliação de Configuração estão sendo substituídos por avaliações específicas de carga de trabalho, incluindo Avaliação do Active Directory e do SQL Server.

![Bloco da Avaliação de Configuração](./media/log-analytics-configuration-assessment/oms-config-assess-tile.png)

Dados de configuração são coletados de servidores monitorados e, em seguida, enviados para o serviço do OMS na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados. Dados processados para os servidores são mostrados para as seguintes áreas:

- **Alertas:** mostra os alertas proativos relacionados à configuração gerados para os servidores monitorados. Eles são produzidos por regras criadas pela organização Microsoft Customer and Support (CSS) com as práticas recomendadas do campo.
- **Recomendações de Conhecimento:** mostra os artigos da Base de Dados de Conhecimento Microsoft recomendados para as cargas de trabalho encontradas em sua infraestrutura. Eles são sugeridos automaticamente com base na sua configuração por meio do uso de aprendizado de máquina.
- **Servidores e Cargas de Trabalho Analisados:** mostra os servidores e as cargas de trabalho monitorados pelo OMS.

![Painel da Avaliação de Configuração](./media/log-analytics-configuration-assessment/oms-config-assess-dash01.png)

### Tecnologias que podem ser analisadas com a Avaliação de Configuração

A Avaliação de Configuração do OMS analisa as cargas de trabalho a seguir:

- Windows Server 2012 e Microsoft Hyper-V Server 2012
- Windows Server 2008 e Windows Server 2008 R2, inclusive:
    - Active Directory
	- Host Hyper-V
	- Sistema operacional geral
- SQL Server 2008 e posterior
    - Mecanismo do Banco de Dados do SQL Server
- Microsoft SharePoint 2010
- Microsoft Exchange Server 2010 e Microsoft Exchange Server 2013
- Microsoft Lync Server 2013 e Lync Server 2010
- System Center 2012 SP1 – Virtual Machine Manager

Para o SQL Server, as seguintes edições de 32 e 64 bits têm suporte para a análise:

- SQL Server 2016 - todas as edições
- SQL Server 2014 - todas as edições
- SQL Server 2008 e 2008 R2 - todas as edições

O Mecanismo de Banco de Dados do SQL Server é analisado em todas as edições com suporte. Além disso, a edição de 32 bits do SQL Server tem suporte ao ser executada na implementação do WOW64.

## Instalando e configurando a solução
Use as informações a seguir para instalar e configurar a solução.

- O Operations Manager é necessário para a solução de Avaliação de Configuração.
- Você deve ter um agente do Operations Manager em cada computador em que deseja avaliar a configuração.
- Adicione a solução de Avaliação de Configuração ao seu espaço de trabalho do OMS usando o processo descrito em [Adicionar soluções do Log Analytics da Galeria de Soluções](log-analytics-add-solutions.md). Não é necessária nenhuma configuração.

## Detalhes da coleta de dados da Avaliação de Configuração

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para a Avaliação de Configuração.

| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
|---|---|---|---|---|---|---|
|Windows|![Não](./media/log-analytics-configuration-assessment/oms-bullet-red.png)|![Sim](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![Não](./media/log-analytics-configuration-assessment/oms-bullet-red.png)| ![Sim](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![Sim](./media/log-analytics-configuration-assessment/oms-bullet-green.png)| duas vezes por dia|


## Alertas da Avaliação de Configuração
Você pode exibir e gerenciar alertas da Avaliação de Configuração com a página de Alertas. Alertas informam o problema que foi detectado, sua causa e como resolvê-lo. Eles também fornecem informações sobre definições de configuração em seu ambiente que podem causar problemas de desempenho.

![exibição de alertas](./media/log-analytics-configuration-assessment/oms-config-assess-alerts01.png)

>[AZURE.NOTE] Os alertas da Avaliação de Configuração são diferentes dos outros alertas no Log Analytics. Exibir alertas requer um plug-in do Silverlight para o navegador.

Ao selecionar um item ou uma categoria de itens na página Alertas, você verá uma lista de servidores ou cargas de trabalho com alertas aplicáveis a cada item.

![lista de alertas](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-view-config.png)

Cada alerta inclui um link para um artigo da Base de Dados de Conhecimento Microsoft. Esses artigos fornecem mais informações sobre o alerta.

>[AZURE.TIP] Por padrão, o número máximo de alertas exibidos é 2.000. Para exibir mais alertas, clique na barra de notificação acima da lista de alertas.

Você pode clicar em qualquer item na lista para exibir o artigo da base de dados que pode ajudar a resolver a causa do problema que gerou o alerta.

![artigos da base de dados](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-details-kb.png)

Você pode gerenciar as regras de alerta para ignorar regras específicas ou uma classe de regras.

![gerenciar regras de alerta](./media/log-analytics-configuration-assessment/oms-config-assess-alert-rules.png)

## Recomendações de Conhecimento
Ao exibir as recomendações de conhecimento, você receberá resultados da pesquisa de log listando artigos da base de dados da Microsoft recomendados para cargas de trabalho e computadores que fornecem informações adicionais sobre o alerta.

![resultados da pesquisa de recomendações de conhecimento](./media/log-analytics-configuration-assessment/oms-config-assess-knowledge-recommendations.png)

## Servidores e cargas de trabalho analisados
Ao exibir recomendações de conhecimento, você receberá resultados da pesquisa de log listando todos os servidores e cargas de trabalho do Operations Manager que são conhecidos pelo OMS.

![Servidores e cargas de trabalho](./media/log-analytics-configuration-assessment/oms-config-assess-servers-workloads.png)

## Próximas etapas

- Use [Pesquisas de log no Log Analytics](log-analytics-log-searches.md) para exibir dados detalhados da avaliação de configuração.

<!---HONumber=AcomDC_0518_2016-->