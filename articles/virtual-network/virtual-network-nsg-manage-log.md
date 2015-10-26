<properties 
   pageTitle="Monitorar operações, eventos e contadores para NSGs | Microsoft Azure"
   description="Saiba como habilitar logs operacionais , de eventos e de contadores para NSGs"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/02/2015"
   ms.author="telmos" />

#Análise de logs para NSGs (grupos de segurança de rede)

Você pode usar diferentes tipos de logs no Azure para gerenciar e solucionar problemas de NSGs. Alguns desses logs podem ser acessados por meio do portal, e todos os logs podem ser extraídos de um armazenamento de blob do Azure e exibidos em diferentes ferramentas, como o Excel e o PowerBI. Você pode saber mais sobre os diferentes tipos de logs na lista abaixo.

- **Logs de auditoria:** você pode usar os [Logs de Auditoria do Azure](insights-debugging-with-events.md) (anteriormente conhecidos como Logs Operacionais) para exibir todas as operações que estão sendo enviadas à(s) sua(s) assinatura(s) do Azure, bem como seu status. Os logs de auditoria são habilitados por padrão e podem ser exibidos no portal de visualização do Azure.
- **Logs de eventos:** você pode usar esse log para exibir quais regras de NSGs são aplicadas às máquinas virtuais e às funções de instância com base no endereço MAC. O status para essas regras é coletado a cada 60 segundos. 
- **Logs do contador:** você pode usar esse log para ver quantas vezes cada regra NSG foi aplicada para negar ou permitir tráfego.

>[AZURE.WARNING]Os logs estão disponíveis apenas para os recursos implantados no modelo de implantação do Gerenciador de Recursos. Você não pode usar logs para recursos do modelo de implantação clássico. Para obter uma melhor compreensão dos dois modelos, consulte o artigo [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](resource-manager-deployment-model.md).

##Habilitar o registro em log
O log de auditoria é sempre habilitado automaticamente para todos os recursos do Gerenciador de Recursos. Você precisa habilitar os logs de eventos e de contador para começar a coletar os dados disponíveis por meio desses logs. Para habilitar os logs, siga as etapas abaixo.

1.  Entre no [portal de visualização do Azure](http://portal.azure.com). Se você ainda não tiver um grupo de segurança de rede existente, [crie um NSG](virtual-networks-create-nsg-arm-ps.md) antes de continuar. 

2.  No portal de visualização, clique em **Navegar** >> **Grupos de segurança de rede**.

	![Portal de visualização - grupos de segurança de rede](./media/virtual-network-nsg-manage-log/portal-enable1.png)

3. Selecione um grupo de segurança de rede existente.

	![Portal de visualização - configurações de grupos de segurança de rede](./media/virtual-network-nsg-manage-log/portal-enable2.png)

4. Na folha **Configurações**, clique em **Diagnóstico** e, em seguida, no painel **Diagnóstico**, ao lado de **Status**, clique em **Ativado**
5. Na folha **Configurações**, clique em **Conta de Armazenamento** e selecione uma conta de armazenamento existente ou crie uma nova.  

>[AZURE.INFORMATION]Os logs de auditoria não exigem uma conta de armazenamento separada. O uso do armazenamento para logs de eventos e regras gerarão encargos de serviço.

6. Na lista suspensa em **Conta de Armazenamento**, selecione se deseja registrar em log eventos, contadores ou ambos e clique em **Salvar**.

	![Portal de visualização - logs de diagnóstico](./media/virtual-network-nsg-manage-log/portal-enable3.png)

## Log de auditoria
Por padrão, esse log (anteriormente conhecido como "log operacional") é gerado pelo Azure. Os logs são preservados por 90 dias no repositório de Logs de Eventos do Azure. Saiba mais sobre esses logs lendo o artigo [Exibir logs de eventos e de auditoria](insights-debugging-with-events.md).

## Log de contador
Esse log será gerado apenas se você o tiver habilitado para cada NSG, conforme descrito acima. Os dados são armazenados na conta de armazenamento especificada quando você habilitou o registro em log. Cada regra aplicada aos recursos é registrada em log no formato JSON, conforme mostrado abaixo.

	{
		"time": "2015-09-11T23:14:22.6940000Z",
		"systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
		"category": "NetworkSecurityGroupRuleCounter",
		"resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
		"operationName": "NetworkSecurityGroupCounters",
		"properties": {
			"vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
			"subnetPrefix":"10.0.0.0/24",
			"macAddress":"001517D9C43C",
			"ruleName":"DenyAllOutBound",
			"direction":"Out",
			"type":"block",
			"matchedConnections":0
			}
	}

## Log de eventos
Esse log será gerado apenas se você o tiver habilitado para cada NSG, conforme descrito acima. Os dados são armazenados na conta de armazenamento especificada quando você habilitou o registro em log. Os seguintes dados são registrados em log:

	{
		"time": "2015-09-11T23:05:22.6860000Z",
		"systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
		"category": "NetworkSecurityGroupEvent",
		"resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
		"operationName": "NetworkSecurityGroupEvents",
		"properties": {
			"vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
			"subnetPrefix":"10.0.0.0/24",
			"macAddress":"001517D9C43C",
			"ruleName":"AllowVnetOutBound",
			"direction":"Out",
			"priority":65000,
			"type":"allow",
			"conditions":{
				"destinationPortRange":"0-65535",
				"sourcePortRange":"0-65535",
				"destinationIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32",
				"sourceIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32"
			}
		}
	}

##Exibir e analisar o log de auditoria
Você pode exibir e analisar dados do log de auditoria usando qualquer um dos seguintes métodos:

- **Ferramentas do azure:** recupere informações dos logs de auditoria por meio do Azure PowerShell, a CLI (Interface de Linha de Comando) do Azure, a API REST do Azure ou o portal de visualização do Azure. Instruções passo a passo para cada método são detalhadas no artigo [Operações de auditoria com o Gerenciador de Recursos](resource-group-audit.md).
- **Power BI:** se ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing), você poderá testá-lo gratuitamente. Usando o [Pacote de conteúdo dos Logs de Auditoria do Azure para Power BI](https://support.powerbi.com/knowledgebase/articles/742695), você pode analisar seus dados com painéis pré-configurados que você pode usar como estão ou personalizar.

##Exibir e analisar o log de eventos e de contador 
Você precisa se conectar à sua conta de armazenamento e recuperar as entradas de log JSON para logs de eventos e contador. Depois de baixar os arquivos JSON, você pode convertê-los em CSV e exibi-lo no Excel, no PowerBI ou em qualquer outra ferramenta de visualização de dados.

>[AZURE.TIP]Se estiver familiarizado com o Visual Studio e os conceitos básicos de alteração de valores de constantes e variáveis em C# , você poderá usar as [ferramentas de conversor de log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no Github.

##Recursos adicionais

- Postagem de blog [Visualizar os logs de auditoria do Azure com o Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx).
- Postagem de blog [Exibir e analisar logs de auditoria do Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).

<!---HONumber=Oct15_HO3-->