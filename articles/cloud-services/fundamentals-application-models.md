<properties 
	pageTitle="Opções de hospedagem de computação fornecidas pelo Azure" 
	description="Saiba mais sobre as opções de hospedagem de computação do Azure e como elas funcionam: Serviços de Aplicativo, Serviços de Nuvem, Máquinas Virtuais entre outros" 
	headerExpose="" 
	footerExpose="" 
	services="cloud-services,virtual-machines"
	authors="Thraka" 
	documentationCenter=""
	manager="timlt"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="adegeo;cephalin;kathydav"/>




# Opções de hospedagem de computação fornecidas pelo Azure

A Azure fornece diferentes modelos de hospedagem para executar aplicativos. Cada um deles fornece um conjunto de serviços diferente e, portanto, qual deles você escolhe depende exatamente do que você está tentando fazer. Este artigo aborda as opções, descrevendo cada tecnologia e fornecendo exemplos de quando usá-la.

| Opções de computação | Público-alvo |
| ------------------ | --------   |
| [Serviço de Aplicativo] | Aplicativos Web escalonáveis, Aplicativos Móveis, Aplicativos de API e Aplicativos Lógicos para qualquer dispositivo |
| [Serviços de Nuvem] | Aplicativos de nuvem de n camadas altamente disponíveis e escalonáveis com mais controle do sistema operacional |
| [Máquinas virtuais] | VMs Windows e Linux personalizadas com controle completo do sistema operacional |

[AZURE.INCLUDE [conteúdo](../../includes/app-service-choose-me-content.md)]

[AZURE.INCLUDE [conteúdo](../../includes/cloud-services-choose-me-content.md)]

[AZURE.INCLUDE [conteúdo](../../includes/virtual-machines-choose-me-content.md)]

## Outras opções

A Azure também oferece outros modelos de hospedagem de computação para fins mais especializados, como os seguintes:

* [Serviços Móveis](/services/mobile-services/)  
  Otimizado para fornecer um back-end de nuvem para aplicativos que são executados em dispositivos móveis.
* [Lote](/services/batch/)  
  Otimizado para processar grandes volumes de tarefas semelhantes, idealmente a cargas de trabalho que prestam-se para executar como tarefas paralelas em vários computadores.
* [HDInsight (Hadoop)](/services/hdinsight/)  
  Otimizado para execução de trabalhos [MapReduce](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options/#hadoop) em clusters de Hadoop. 

## Qual devo usar? Fazendo uma escolha

Todas os três modelos de hospedagem de computação da Azure para propósito geral permitem que você crie aplicativos escalonáveis e confiáveis na nuvem. Dada essa importante semelhança, qual deles devo usar?

O Serviço de Aplicativo é a melhor opção para a maioria dos aplicativos Web. A implantação e o gerenciamento estão integrados na plataforma, os sites podem ser dimensionados rapidamente para dar suporte a altas cargas de tráfego e o gerenciador de balanceamento de carga e tráfego integrado oferece alta disponibilidade. Você pode mover sites existentes para o Serviço de Aplicativo facilmente com uma [ferramenta de migração online](https://www.migratetoazure.net/), usar um aplicativo de software livre da Galeria de Aplicativos Web ou criar um novo site usando a estrutura e as ferramentas de sua escolha. O recurso [Trabalhos Web](http://go.microsoft.com/fwlink/?linkid=390226) facilita o trabalho em segundo plano para o seu aplicativo de processamento de adicionar, ou até mesmo executar uma carga de trabalho de computação que não seja um aplicativo da web em qualquer caso.

Se precisar de mais controle sobre o ambiente do servidor Web, como a capacidade de acessar remotamente seu servidor ou configurar as tarefas de inicialização do servidor, os Serviços de Nuvem do Azure geralmente são a melhor opção.

Se você tiver um aplicativo existente que necessitaria de modificações substanciais para ser executado em Websites do Azure ou nos Serviços de Nuvem do Azure, você pode escolher as Máquinas Virtuais do Azure para simplificar a migração para a nuvem. No entanto, configurar, proteger e manter as máquinas virtuais corretamente requer muito tempo e conhecimento de TI em comparação aos Websites do Azure e aos Serviços de Nuvem. Se estiver considerando as Máquinas Virtuais do Azure, assegure-se de levar em conta o esforço contínuo de manutenção necessário para corrigir, atualizar e gerenciar seu ambiente de máquinas virtuais. Se você tiver um aplicativo existente que necessitaria de modificações substanciais para ser executado no Serviço de Aplicativo ou nos Serviços de nuvem do Azure, você pode escolher as Máquinas Virtuais do Azure para simplificar a migração para a nuvem. No entanto, configurar, proteger e manter as máquinas virtuais corretamente requer muito tempo e conhecimento de TI em comparação ao Serviço de Aplicativo e aos Serviços de Nuvem. Se estiver considerando as Máquinas Virtuais do Azure, assegure-se de levar em conta o esforço contínuo de manutenção necessário para corrigir, atualizar e gerenciar seu ambiente de máquinas virtuais.

Às vezes, nenhuma opção única é certa. Em situações como essa, é perfeitamente legal combinar as opções. Por exemplo, suponha que você esteja criando um aplicativo onde deseja os benefícios de gerenciamento das funções Web dos Serviços de Nuvem, mas também precisa usar o SQL Server padrão hospedado em uma Máquina Virtual por motivos de compatibilidade ou desempenho.

<!-- In this case, the best option is to combine compute hosting options, as the figure below shows.--

<a name="fig4"></a>
![07_CombineTechnologies][07_CombineTechnologies] 
 
**Figure: A single application can use multiple hosting options.**

As the figure illustrates, the Cloud Services VMs run in a separate cloud service from the Virtual Machines VMs. Still, the two can communicate quite efficiently, so building an app this way is sometimes the best choice.
[07_CombineTechnologies]: ./media/fundamentals-application-models/ExecModels_07_CombineTechnologies.png
!-->

[Serviço de Aplicativo]: #tellmeas
[Máquinas virtuais]: #tellmevm
[Serviços de Nuvem]: #tellmecs

## Próximas etapas

* [Comparação](../choose-web-site-cloud-service-vm/) de Serviço de Aplicativo, serviços de nuvem e máquinas virtuais
* Saiba mais sobre [Serviço de Aplicativo](../app-service-web-overview.md)
* Saiba mais sobre [Serviço de Nuvem](services/cloud-services/)
* Saiba mais sobre [Máquinas virtuais](https://msdn.microsoft.com/library/azure/jj156143.aspx) 

<!---HONumber=62-->