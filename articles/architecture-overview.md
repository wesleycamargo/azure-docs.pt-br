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
	ms.date="08/11/2015" 
	ms.author="robb"/>

#Arquitetura de aplicativos no Microsoft Azure
Recursos para criar aplicativos que usam o Microsoft Azure. Entre eles estão ferramentas para ajudá-lo a desenhar diagramas para descrever visualmente os sistemas de software.



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

![Padrões de design de arquitetura do Azure](./media/architecture-overview/AzureArchPatterns.jpg)


##Cartaz de padrões de design
Os desenvolvedores de práticas e padrões da Microsoft publicaram o livro [Padrões de Design de Nuvem](http://msdn.microsoft.com/library/dn568099.aspx) disponível no MSDN e para download em PDF. Está disponível também um pôster de grande formato que lista todos os padrões.

![Pôster de práticas e padrões de nuvem](./media/architecture-overview/PnPPatternPosterThumb.jpg)



##Curso de certificação de arquitetura da Microsoft

A Microsoft acabou de lançar um novo curso de arquitetura para dar suporte ao exame de certificação 70-534 da Microsoft. Ele está [disponível gratuitamente no EDX.ORG](https://www.edx.org/course/architecting-microsoft-azure-solutions-microsoft-dev205x). Ele usa o novo [Modelo de planta 3D do Visio](#3d-blueprint-visio-template).

![Curso de certificação de Arquitetura da Microsoft](./media/architecture-overview/EDXCourse.png)


##Plantas de Arquitetura da Microsoft

A Microsoft publica um conjunto de [plantas de arquitetura](http://aka.ms/azblueprints) de alto nível, mostrando como criar tipos específicos de sistemas que usam produtos Microsoft.

Cada planta inclui um

- Arquivo simples baseado em **Visio 2003 2D** que você pode baixar e modificar 
- Arquivo **PDF de perspectiva 3D** colorido para apresentar a planta para um público menos técnico
- **Vídeo** que descreve a versão 3D. 

As Plantas usam o [Conjunto de símbolos de nuvem e empresarial](#symbol-and-icon-sets).

![Diagrama 3D de plantas de Arquitetura da Microsoft](./media/architecture-overview/BluePrintThumb.jpg)



##Modelo de planta 3D do Visio

As versões 3D das [Plantas de arquitetura da Microsoft](http://aka.ms/azblueprints) foram criadas inicialmente em uma ferramenta não Microsoft. Um novo modelo de 2013 (e posterior) do Visio foi fornecido em 5 de agosto de 2015 como parte de um [Curso de certificação de arquitetura da Microsoft distribuído em EDX.ORG](#microsoft-architecture-certification-course).

O modelo também está disponível fora do curso.

- [Veja o vídeo de treinamento](http://aka.ms/3dBlueprintTemplateVideo) primeiro para saber o que ele pode fazer   
- Baixe o [Modelo de planta 3D do Visio da Microsoft](http://aka.ms/3DBlueprintTemplate)
- Baixe os [Símbolos de Nuvem e Enterprise](#symbol-and-icon-sets) para usar com o modelo 3D

Envie um email para [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) com perguntas específicas não respondidas pelos materiais de treinamento ou para fazer algum comentário. A usabilidade é uma das principais metas do modelo, por isso, gostaríamos de saber o que está bom e o que pode ser melhorado.

![Modelo de planta 3D do Visio da Microsoft](./media/architecture-overview/3DBlueprintVisioTemplate.jpg)



##Conjuntos de símbolos de desenho e ícones 

[Veja o vídeo de treinamento dos símbolos e do Visio](http://aka.ms/CnESymbolsVideo) e [baixe o conjunto de Símbolos de Nuvem e Enterprise](http://aka.ms/CnESymbols) para ajudá-lo a criar materiais técnicos que descrevem o Azure, o Windows Server, o SQL Server e muito mais. Você pode usar os símbolos em diagramas de arquitetura, materiais de treinamento, apresentações, folhas de dados, infográficos, white papers e até mesmo em livros de terceiros se o livro treina as pessoas para usarem os produtos da Microsoft. No entanto, eles não se destinam ao uso em interfaces do usuário.

Os símbolos de Nuvem e Enterprise são nos formatos Visio e PNG. Instruções adicionais sobre como usar os PNGs no PowerPoint estão incluídas no conjunto.

O conjunto de símbolos é fornecido trimestralmente e é atualizado na medida em que novos serviços são lançados.

Símbolos adicionais para Microsoft Office e tecnologias relacionadas estão disponíveis no [estêncil do Microsoft Office Visio](http://www.microsoft.com/pt-BR/download/details.aspx?id=35772), embora eles não sejam otimizados para diagramas de arquitetura, como é o caso do conjunto CnE.

**Comentários:** se você usou os símbolos CnE, preencha a curta [pesquisa](http://aka.ms/azuresymbolssurveyv2) de 5 perguntas ou envie um email para [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) para perguntas e problemas específicos. Queremos saber a sua opinião, incluindo comentários positivos para que saibamos que podemos continuar a investir tempo neles.

![Conjunto de ícones/símbolos Enterprise e Nuvem](./media/architecture-overview/CnESymbols.png)


##Infográfico de arquitetura

A Microsoft publica diversas arquiteturas relacionadas a cartazes/infográficos. Elas incluem [Criando aplicativos de nuvem do mundo real](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) e [Dimensionamento com serviços de nuvem](http://azure.microsoft.com/documentation/infographics/cloud-services/).

![Infográfico de arquitetura do Azure](./media/architecture-overview/AzureArchInfographicThumb.jpg)

<!---HONumber=Oct15_HO3-->