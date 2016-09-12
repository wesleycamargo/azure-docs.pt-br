<properties
	pageTitle="Documentação do Azure Governamental | Microsoft Azure"
	description="Este guia fornece uma comparação dos recursos e orientações sobre como desenvolver aplicativos para o Azure Government"
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="08/25/2016"
	ms.author="ryansoc"/>


#  Computação do Azure Governamental

##  Máquinas Virtuais


As informações a seguir identificam o limite do Azure Governamental para o máquinas virtuais do Azure:

| Dados regulamentados/controlados permitidos | Dados regulamentados/controlados não permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Dados inseridos, armazenados e processados em uma VM podem conter dados de exportação controlados. Binários em execução nas máquinas virtuais do Azure. Autenticadores estáticos, como senhas e PINs de cartão inteligente para acesso aos componentes da plataforma Azure. Chaves privadas de certificados usados para gerenciar os componentes da plataforma Azure. Cadeias de conexão SQL. Outros informações/segredos de segurança, como certificados, chaves de criptografia, chaves mestras e chaves de armazenamento armazenadas nos serviços do Azure. | Metadados não podem conter dados de exportação controlados. Esses metadados incluem todos os dados de configuração inseridos durante a criação e manutenção de sua máquina virtual do Azure. Não insira dados Regulamentados/controlados nos seguintes campos: Nomes de funções de locatários, Grupos de recursos, Nomes de implantação, Nomes de recursos, Marcas de recurso  

Para saber mais, consulte a <a href=https://azure.microsoft.com/pt-BR/documentation/services/virtual-machines/windows/> Documentação pública de máquinas virtuais do Azure</a>.

Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>

<!---HONumber=AcomDC_0831_2016-->