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


#  Segurança e gerenciamento do Azure Governamental

##  Cofre da Chave

As informações a seguir identificam o limite do Azure Governamental para o Cofre de Chaves do Azure:

| Dados regulamentados/controlados permitidos | Dados regulamentados/controlados não permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos os dados criptografados com uma chave do Cofre de Chaves do Azure podem conter dados regulamentados/controlados. | Metadados do Cofre de Chaves do Azure não são permitidos para conter dados de exportação controlados. Esses metadados incluem todos os dados de configuração inseridos durante a criação e manutenção de seu Cofre de Chaves. Não insira dados Regulamentados/controlados nos seguintes campos: Nomes de grupo de recursos, Nomes de Cofre de Chaves, Nome de assinatura |

O Cofre de Chaves está disponível no Azure Governamental. Assim como no público, não há qualquer extensão, portanto o Cofre de Chaves só está disponível por meio do PowerShell e da CLI.

Para saber mais, confira a [documentação pública do Cofre de Chaves do Azure](/key-vault-get-started).

Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>

<!---HONumber=AcomDC_0831_2016-->