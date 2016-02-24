<properties 
	pageTitle="Serviço de Aplicativo do Azure: dimensionamento de aplicativos do Serviço de Aplicativo" 
	description="Saiba as vantagens e desvantagens do dimensionamento do aplicativo no Serviço de Aplicativo." 
	keywords="serviço de aplicativo, serviço de aplicativo do azure, escala, escalonável, plano de serviço de aplicativo, custo de serviço de aplicativo"
	services="app-service" 
	documentationCenter="" 
	authors="btardif" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2016" 
	ms.author="byvinyal"/>
	
# Serviço de Aplicativo do Azure: dimensionamento de aplicativos do Serviço de Aplicativo

Os aplicativos hospedados no Serviço de Aplicativo do Azure podem [atingir grandes escalas](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/); entretanto, o dimensionamento de um aplicativo é um problema complexo que não tem uma solução de "tamanho único". Para dimensionar corretamente o aplicativo, há três áreas principais que contribuirão para o sucesso do seu aplicativo:

1. Entender a arquitetura do aplicativo e seus pontos fracos.
	* Seu aplicativo é com estado? Sem estado?
	* O que são todos os componentes do seu aplicativo?
		* Onde estão os gargalos no aplicativo? 
	* Quando a carga é aplicada ao seu aplicativo, o que será interrompido primeiro?
2. Entender os requisitos de desempenho e carga esperados.
	* O aplicativo precisa atender mil usuários? Ou um milhão?
	* O tráfego virá de um único local geográfico ou é global?
	* Há variações sazonais? Picos de tráfego? 
	* Com que rapidez o aplicativo deve responder? 1 segundo? 1 milissegundo?
3. Entender e aproveitar corretamente a plataforma de hospedagem de seu aplicativo.
	* Quais recursos devo aproveitar para alcançar meus objetivos de escala?
	
Esta seção o ajudará a entender todos os fatores e ajudá-lo a conceber uma estratégia que aproveita os recursos do Serviço de Aplicativo necessários para atingir suas metas de escalabilidade do dispositivo.

[AZURE.INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]

<!---HONumber=AcomDC_0211_2016-->