<properties 
	pageTitle="Arquitetura de aplicativos no Microsoft Azure" 
	description="Visão geral da arquitetura que aborda os padrões comuns de design" 
	services="" 
	documentationCenter="" 
	authors="Rboucher" 
	manager="jwhit" 
	editor="mattshel"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="robb"/>

#Arquitetura de aplicativos no Microsoft Azure
Recursos para criar aplicativos que usam o Microsoft Azure.

##Padrões de design arquitetural do Azure
A Microsoft publica uma série de padrões de design arquitetural para ajudá-lo a criar seus próprios designs personalizados. Os padrões têm a finalidade de ser guias arquiteturais concisos que podem ser compostos em conjunto a fim de fornecer orientação sobre como melhor aproveitar a plataforma Microsoft Azure para atender às necessidades de negócios da sua organização.


[Visão geral](../azure-architectures-cpif-overview/) - [Rede híbrida](../azure-architectures-cpif-infrastructure-hybrid-networking/) - [Processamento em lotes externo](../azure-architectures-cpif-foundation-offsite-batch-processing-tier/) - [Camada de dados de vários locais](../azure-architectures-cpif-foundation-multi-site-data-tier/) - [Camada Web com balanceamento de carga global](../azure-architectures-cpif-foundation-global-load-balanced-web-tier/) - [Nível de pesquisa do Azure](../azure-architectures-cpif-foundation-azure-search-tier/)
 
Cada padrão contém
 
- Uma descrição de serviço
- Uma lista de serviços do Azure necessários para aproveitar o padrão
- Diagramas arquiteturais
- Dependências arquiteturais
- Limitações ou considerações de design que podem afetar o padrão
- Interfaces e pontos de extremidade
- Antipadrões
- Importantes considerações gerais de arquitetura, incluindo disponibilidade e resiliência, SLAs compostos para serviços usados, escala e desempenho, considerações operacionais e de custo.

##Plantas de Arquitetura da Microsoft

A Microsoft publica um conjunto de plantas de arquitetura de alto nível, mostrando como criar tipos específicos de sistemas que incluem os produtos Microsoft e serviços do Microsoft Azure. Cada planta inclui um arquivo do Visio com base em 2D que você pode baixar e modificar, um arquivo PDF 3D mais colorido para apresentar a planta e um vídeo de orientação para a versão 3D. Consulte [Plantas de Arquitetura da Microsoft](http://msdn.microsoft.com/dn630664).

Os diagramas de plantas 2D usam o conjunto de símbolos Enterprise e Nuvem mencionados a seguir.

Os PDFs de plantas 3D são criados em uma ferramenta que não é da Microsoft, mas há um modelo do Visio em desenvolvimento. Consulte um [Vídeo de treinamento BETA do modelo aqui](http://aka.ms/3dBlueprintTemplate). Para obter a versão beta de modelo de planta 3D do Visio, envie um email para [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com).

![Diagrama 3D de plantas de Arquitetura da Microsoft](./media/architecture-overview/BluePrintThumb.jpg)

##Conjunto de ícones/símbolos Enterprise e Nuvem

[Baixar o conjunto de ícones/símbolos Enterprise e Nuvem](http://aka.ms/CnESymbols) para criar materiais técnicos que descrevem o Azure, Windows Server, SQL Server e outros produtos da Microsoft. Você pode usá-los em diagramas de arquitetura, materiais de treinamento, apresentações, folhas de dados, infográficos, white papers e até mesmo livros de terceiros se o livro treina as pessoas para usarem os produtos da Microsoft. Os símbolos são nos formatos Visio e PNG. Estão incluídas instruções sobre como usar os PNGs no PowerPoint.

O conjunto de símbolos é fornecido trimestralmente e é atualizado na medida em que novos serviços são lançados. Se você quiser uma visualização prévia da versão mais recente, que pode incluir serviços adicionais do Azure, envie um email para [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com).

Gostaríamos de saber o que você pensa, portanto, há instruções para fornecer comentários no download. Se você já usou os símbolos, lembre-se de preencher a nossa [pesquisa](http://aka.ms/azuresymbolssurveyv2) curta de 5 perguntas ou informar-nos pelo email acima se eles são úteis e como você os usa.

Símbolos adicionais estão disponíveis no [estêncil do Microsoft Office Visio](http://www.microsoft.com/pt-br/download/details.aspx?id=35772), embora eles não sejam otimizados para diagramas de arquitetura, como é o caso do conjunto CnE.

![Conjunto de ícones/símbolos Enterprise e Nuvem](./media/architecture-overview/CnESymbols.png)

##Padrões de design
Os desenvolvedores de práticas e padrões da Microsoft publicaram o livro [Padrões de Design de Nuvem](http://msdn.microsoft.com/library/dn568099.aspx) disponível no MSDN e para download em PDF. Está disponível também um pôster de grande formato que lista todos os padrões.

![Pôster de práticas e padrões de nuvem](./media/architecture-overview/PnPPatternPosterThumb.jpg)

##Infográfico de arquitetura
A Microsoft publica diversas arquiteturas relacionadas a cartazes/infográficos. Elas incluem [Criando aplicativos de nuvem do mundo real](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) e [Dimensionamento com serviços de nuvem](http://azure.microsoft.com/documentation/infographics/cloud-services/).

![Infográfico de arquitetura do Azure](./media/architecture-overview/AzureArchInfographicThumb.jpg)

<!---HONumber=July15_HO5-->