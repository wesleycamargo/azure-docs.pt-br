<properties
	pageTitle="Mover os recursos do aplicativo Web para outro grupo de recursos"
	description="Descreve os cenários em que você pode mover aplicativos Web e Serviços de Aplicativos de um grupo de recursos para outro."
	services="app-service\web"
	documentationCenter=""
	authors="ZainRizvi"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="zarizvi"/>
	
# Configurações de movimentação com suporte

Você pode mover os recursos de aplicativo Web do Azure usando a [API de recursos de movimentação ARM](../resource-group-move-resources.md).

Atualmente, os aplicativos Web do Azure dão suporte para os seguintes cenários de movimentação:

* Mover todo o conteúdo de um grupo de recursos (aplicativos Web, planos de serviços de aplicativos e certificados) para outro grupo de recursos 
	* Observação: o grupo de recursos de destino não pode conter nenhum recurso Microsoft.Web nesse cenário
* Mover aplicativos Web individuais para um grupo de recursos diferente, enquanto ainda os hospeda em seu plano de serviço de aplicativo atual (o plano de serviço de aplicativo permanece no grupo de recursos antigo)

<!---HONumber=August15_HO8-->