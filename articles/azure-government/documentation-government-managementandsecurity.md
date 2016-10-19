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
	ms.date="09/23/2016"
	ms.author="ryansoc"/>


#  Segurança e gerenciamento do Azure Governamental

##  Cofre da Chave

As informações a seguir identificam o limite do Azure Governamental para o Cofre de Chaves do Azure:

| Dados regulamentados/controlados permitidos | Dados regulamentados/controlados não permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos os dados criptografados com uma chave do Cofre de Chaves do Azure podem conter dados regulamentados/controlados. | Metadados do Cofre de Chaves do Azure não são permitidos para conter dados de exportação controlados. Esses metadados incluem todos os dados de configuração inseridos durante a criação e manutenção de seu Cofre de Chaves. Não insira dados Regulamentados/controlados nos seguintes campos: Nomes de grupo de recursos, Nomes de Cofre de Chaves, Nome de assinatura |

O Cofre de Chaves está disponível no Azure Governamental. Assim como no público, não há qualquer extensão, portanto o Cofre de Chaves só está disponível por meio do PowerShell e da CLI.

Para saber mais, confira a [documentação pública do Cofre de Chaves do Azure](/key-vault-get-started).

## Log Analytics

O Log Analytics está em visualização no Azure Governamental.

### Diferenças do Azure público

Os recursos e as soluções do Log Analytics a seguir não estão disponíveis atualmente no Azure Governamental. Essa lista é atualizada quando o status dos recursos/das soluções muda.

+ Métricas quase em tempo real
  - Ao exibir gráficos de métricas com um intervalo de menos de seis horas, o gráfico não será atualizado automaticamente com os novos valores de métrica
+ Exportação de dados para o Power BI
+ Solução de monitoramento de rede
+ Solução do Office 365
+ Solução de Análise de Atualização do Windows 10
+ Monitoramento da Dependência de Aplicativos
+ Avaliação de Atualização
+ Integração ao portal do Azure
  - A seleção de contas de armazenamento do Azure para serem monitoradas deve ser feita pelo PowerShell ou por modelos do Resource Manager
  - A seleção de máquinas virtuais para habilitar o agente do Log Analytics deve ser feita pelo PowerShell ou por modelos do Resource Manager
+ Monitoramento do Linux
+ Aplicativos móveis do OMS
+ Extensão de VM do Microsoft Monitoring Agent
+ Extensão de VM do agente Linux OMS
+ Dados de uso
+ Emails de alerta e correção usando a Automação do Azure

Os seguintes recursos do Log Analytics têm um comportamento diferente no Azure Governamental:

+ O agente do Windows deve ser baixado no portal do Log Analytics para o Azure Governamental.
+ A solução de Segurança e Auditoria não tem suporte para detecção de IP mal-intencionado.
+ O carregamento de dados usando a API do Coletor de Dados requer o uso da URL do Azure Governamental.

### Perguntas frequentes

+ Posso migrar dados do Log Analytics no Azure público para o Azure Governamental?
  - Não. Não é possível mover os dados ou seu espaço de trabalho do Azure público para o Azure Governamental.
+ Posso alternar entre espaços de trabalho do Azure público e do Azure Governamental no portal do Log Analytics do OMS?
  - Não. Os portais do Azure público e do Azure Governamental são separados e não compartilham informações.

Para saber mais, confira [Documentação pública do Log Analytics](../log-analytics/log-analytics-overview.md).

## Próximas etapas

Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>

<!---HONumber=AcomDC_0928_2016-->