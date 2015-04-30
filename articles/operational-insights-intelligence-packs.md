<properties 
	pageTitle="Pacotes de Inteligência do Insights Operacionais" 
	description="Insights operacionais é um serviço de análise que permite aos administradores de TI obter uma visão mais profunda entre ambientes locais e na nuvem. Ele permite interagir com os dados históricos e em tempo real de computador para rapidamente desenvolver soluções personalizadas e fornecer padrões desenvolvidos pela comunidade e pela Microsoft para análise de dados." 
	services="operational-insights" 
	documentationCenter="n/a" 
	authors="bandersmsft" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="operational-insights" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="NA" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="alfran"/>

# Pacotes de inteligência

Os Pacotes de inteligência são uma coleção de **regras de lógica**, **visualização** e **aquisição de dados** que abordam os principais desafios dos clientes hoje. Pacotes de inteligência são ativados pela Pesquisa do Insights Operacionais para fornecer métricas que girem em torno de uma área de problema específica. Eles permitem insights mais profundos para ajudar a investigar e resolver problemas operacionais mais rápido, coletar e correlacionar os vários tipos de dados do computador e ajudá-lo a ser proativo com atividades como planejamento de capacidade, relatórios de status de patch e a auditoria de segurança. 

Este guia o orienta através dos vários pacotes inteligência e para o que eles são usados.

>[AZURE.NOTE] Para obter mais informações sobre como  *adicionar* pacotes de inteligência, consulte [Adicionar pacotes de inteligência](operational-insights-add-intelligence-pack.md)

## Avaliação do SQL

O Pacote de inteligência de avaliação do SQL avalia o risco e a integridade de seus ambientes do SQL Server em um intervalo regular. Por padrão, ele examinará os sistemas SQL semanalmente e as informações serão apresentadas como um acúmulo mensal. Fornece uma lista priorizada de recomendações personalizadas para suas implantações. Essas recomendações são categorizadas em seis áreas de foco que permitem que você e sua equipe:

- **rapidamente** entendem o **risco e a integridade** de seus ambientes
- facilmente realizem **ações** para diminuir o risco e melhorar a integridade
- **priorizem** seu trabalho e se tornem mais **produtivos**

A avaliação do SQL requer o .NET 4 em execução em cada agente. Ele dá suporte para as edições Standard, Developer e Enterprise do SQL Server, sendo todas elas versões com suporte no momento.
[Leia mais no MSDN](https://msdn.microsoft.com/library/azure/dn873958.aspx)


## Avaliação de Configuração

A avaliação de configuração fornece informações detalhadas sobre o estado atual de sua infraestrutura de servidor. 

Ao contrário de pacotes de inteligência, a avaliação de configuração está disponível quando você começa a usar o Insights Operacionais com o System Center Operations Manager. Ela não estará disponível se você usar apenas agentes conectados diretamente.

Dados de configuração são coletados de servidores monitorados e, em seguida, enviados para o serviço de Insights Operacionais na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados. Dados processados para os servidores são mostrados para as seguintes áreas:

- **Alertas:** mostra os alertas relacionados à configuração e alertas proativos gerados para os servidores monitorados. São produzidos por regras criadas pela organização de Suporte e Atendimento ao Cliente (CSS) da Microsoft com as práticas recomendadas do campo
- **Recomendações de conhecimento:** mostra os artigos da Base de Dados de Conhecimento Microsoft recomendados para as cargas de trabalho encontradas em sua infraestrutura. Eles são sugeridos automaticamente com base na sua configuração por meio do uso de aprendizado de máquina 
- **Servidores e cargas de trabalho analisados:** mostra os servidores e as cargas de trabalho monitorados pelo Insights Operacionais
- **Instantâneo atual:** mostra as informações mais recentes sobre servidores que relataram dados para o serviço de Insights Operacionais
- **Histórico de alterações:** mostra uma lista de alterações de configuração feitas aos servidores monitorados

> [AZURE.IMPORTANT] A avaliação de configuração só pode ser habilitada para computadores gerenciados pelo **Operations Manager**.  Para obter informações adicionais, consulte [Conectar o Operations Manager](operational-insights-connect-scom.md)


## Avaliação de malware
Se houver proteção insuficiente, os servidores com ameaças ativas e servidores com proteção insuficiente são mostrados na página **Malware**. Usando as informações na página **Malware**, você pode desenvolver um plano para aplicar proteção aos servidores que precisam dela.

> [AZURE.IMPORTANT] A avaliação de malware dá suporte no momento apenas para os clientes em tempo real do Windows Defender e do System Center Endpoint Protection (SCEP). Se nenhum desses clientes for encontrado, ele usa dados da ferramenta de remoção de software mal-intencionado e marca o servidor como não tendo proteção em tempo real. Você pode ler mais [aqui](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519202-support-other-antivirus-products-in-malware-assess). Também estamos acompanhando [um problema com computadores Windows 7 e Windows Server 2008 R2](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519211-windows-server-2008-r2-sp1-servers-are-shown-as-n)





## Gerenciamento de alertas

Com o pacote de inteligência de gerenciamento de alerta, você pode exibir os alertas do **Operations Manager** em todos os servidores. Essa ferramenta pode ajudá-lo a fazer facilmente uma triagem de alertas e identificar as causas raiz no seu ambiente de modo muito rápido e fluido.

Você pode obter informações sobre os principais cenários, incluindo:

- Quantos alertas foram gerados em um período especificado?
- Principais fontes com o maior número de alertas ativos gerados em um período especificado.
- Principais alertas críticos e de aviso gerados em um período especificado.
- Capacidade de pesquisar em todos os alertas e exibir cada alerta em detalhes

> [AZURE.IMPORTANT] o Gerenciador de Alerta só pode ser habilitado quando o Insights Operacionais for usado em conjunto com o **System Center Operations Manager**. Isso não envia nenhum dados dos agentes, mas simplesmente sincroniza alertas do Operations Manager com a nuvem para permitir que você faça triagem no Insights Operacionais e use a Pesquisa. Para obter informações adicionais, consulte [Conectar o Operations Manager](operational-insights-connect-scom.md)


## Planejamento da capacidade

Você pode usar o pacote de inteligência de Gerenciamento de Capacidade no Insights Operacionais do Microsoft Azure para ajudá-lo a compreender a capacidade da sua infraestrutura de servidor. O pacote de inteligência lê os contadores de desempenho no servidor monitorado e envia dados de uso para o serviço de informações operacionais na nuvem para processamento. Lógica é aplicada aos dados de uso e o serviço de nuvem registra os dados. Ao longo do tempo, padrões de uso são identificados e a capacidade é projetada com base no consumo atual.

Por exemplo, uma projeção pode identificar quando a núcleos do de processador adicionais ou memória adicional serão necessários para um servidor individual. Neste exemplo, a projeção pode indicar que o servidor precisará de mais memória em 30 dias. Isso pode ajudá-lo a planejar uma atualização de memória durante a próxima janela de manutenção do servidor, que pode ocorrer uma vez a cada duas semanas.

>[AZURE.IMPORTANT] O Gerenciamento de Capacidade só pode ser habilitado quando o Insights Operacionais for usado em conjunto com o **System Center Operations Manager**, e você também deverá habilitar o conector do Operations Manager com o Virtual Machine Manager (VMM). Para obter informações adicionais sobre como conectar os sistemas, consulte [Como conectar o VMM com o Operations Manager](https://technet.microsoft.com/library/hh882396.aspx).

Para obter mais informações sobre como usar o pacote de inteligência de gerenciamento de capacidade, consulte:

- [Como usar a página Computação](https://msdn.microsoft.com/library/azure/dn873965.aspx)
- [Como usar a página Armazenamento Anexado Direto](https://msdn.microsoft.com/library/azure/dn873954.aspx) 


## Controle de Alterações

Você pode usar o pacote de inteligência de Controle de Alterações de Configuração para ajudá-lo a identificar facilmente as alterações no software e nos Serviços Windows que ocorrem em seu ambiente. Identificar essas alterações de configuração pode ajudá-lo a detectar problemas operacionais. Usando as informações na página Controle de Alterações, você pode ver facilmente as alterações feitas à sua infraestrutura de servidor. Você pode exibir as alterações à sua infraestrutura e então analisar em detalhes para as seguintes categorias:

- Alterações por tipo de configuração para serviços de software e do Windows
- Alterações de software para aplicativos e atualizações para servidores individuais
- Número total de alterações de software para cada aplicativo
- Alterações no serviço Windows para servidores individuais


## Avaliação de atualização do sistema

Você pode usar o pacote de inteligência de atualizações do sistema no Insights Operacionais do Microsoft Azure para ajudá-lo a aplicar as atualizações ausentes a servidores na sua infraestrutura. Se forem encontradas atualizações ausentes, elas serão mostradas na página **Atualizações**. Você pode usar a página **Atualizações** para trabalhar com as atualizações ausentes e desenvolver um plano para aplicá-las aos servidores que precisam delas.

A página Atualizações detalha as seguintes categorias:

- Servidores com atualizações de segurança ausentes
- Servidores que não foram atualizados recentemente
- Atualizações que devem ser aplicadas a servidores específicos
- Tipo de atualizações que estão faltando

Você pode clicar em qualquer bloco ou item para exibir seus detalhes na página de **Pesquisa** para obter mais informações sobre a atualização ausente.  Os resultados da pesquisa incluem:

- Servidor
- Título da atualização
- ID da Base de Dados de Conhecimento
- A atualização de produto é para
- Gravidade da atualização
- Data da publicação

Os resultados da pesquisa de servidor incluem:

- Nome do servidor
- Nome da versão do sistema operacional
- Método para habilitar a atualização automática
- Dias desde a última atualização
- Versão do agente do Windows Update







<!--HONumber=52-->