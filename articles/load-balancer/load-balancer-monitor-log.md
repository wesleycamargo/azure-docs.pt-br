<properties 
   pageTitle="Monitorar operações, eventos e contadores para o Balanceador de Carga | Microsoft Azure"
   description="Saiba como habilitar o registro em log de eventos de alerta e do status da integridade de investigação para o Balanceador de Carga do Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="joaoma" />

# Análise de log para o Balanceador de Carga do Azure (Preview)
Você pode usar diferentes tipos de log no Azure para gerenciar e solucionar problemas de balanceadores de carga. Alguns desses logs podem ser acessados por meio do portal, e todos os logs podem ser extraídos de um armazenamento de blob do Azure e exibidos em diferentes ferramentas, como o Excel e o PowerBI. Você pode saber mais sobre os diferentes tipos de logs na lista abaixo.


- **Logs de auditoria:** você pode usar os [Logs de Auditoria do Azure](insights-debugging-with-events.md) (anteriormente conhecidos como Logs Operacionais) para exibir todas as operações que estão sendo enviadas à(s) sua(s) assinatura(s) do Azure, bem como seu status. Os logs de auditoria são habilitados por padrão e podem ser exibidos no portal do Azure.
- **Logs de eventos de alerta:** você pode usar esse log para ver quais alertas foram gerados para o balanceador de carga. O status do balanceador de carga é coletado a cada cinco minutos. Esse log será gravado somente se um evento de alerta do balanceador de carga for gerado.  
- **Logs de investigação de integridade:** você pode usar esse log para verificar o status da verificação da integridade de investigação, quantas instâncias estão online no back-end do balanceador de carga e a porcentagem de máquinas virtuais que está recebendo tráfego da rede do balanceador de carga. Esse log é gravado na alteração do evento de status da investigação.

>[AZURE.WARNING] Os logs estão disponíveis apenas para os recursos implantados no modelo de implantação do Gerenciador de Recursos. Você não pode usar logs para recursos do modelo de implantação clássico. Para obter uma melhor compreensão dos dois modelos, consulte o artigo [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](resource-manager-deployment-model.md). <BR> Atualmente, a análise de log funciona somente para balanceadores de carga voltados para a Internet. Essa limitação é temporária e pode mudar a qualquer momento. Certifique-se de visitar novamente esta página para verificar as alterações futuras.

## Habilitar o registro em log
O log de auditoria é sempre habilitado automaticamente para todos os recursos do Gerenciador de Recursos. Você precisa habilitar o registro em log da investigação de integridade e de eventos para começar a coletar os dados disponíveis por meio desses logs. Para habilitar os logs, siga as etapas abaixo.

Entre no [portal do Azure](http://portal.azure.com). Se você ainda não tiver um balanceador de carga [crie um](load-balancer-internet-arm-ps.md) antes de continuar.

No portal, clique em **Procurar** >> **Balanceadores de Carga**.

![portal - balanceador de carga](./media/load-balancer-monitor-log/load-balancer-browse.png)

Selecione um balanceador de carga existente >> **Todas as Configurações**.

![portal - configurações do balanceador de carga](./media/load-balancer-monitor-log/load-balancer-settings.png) <BR>

Na folha **Configurações**, clique em **Diagnóstico** e, no painel **Diagnóstico**, ao lado de **Status**, clique em **Ativar**. Na folha **Configurações**, clique em **Conta de Armazenamento** e escolha uma conta de armazenamento existente ou crie uma.

Na lista suspensa, logo abaixo de **Conta de Armazenamento**, escolha se deseja registrar os eventos de alerta, o status da integridade da investigação, ou ambos, e clique em **Salvar**.

![Portal de visualização - logs de diagnóstico](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

>[AZURE.INFORMATION] Os logs de auditoria não exigem uma conta de armazenamento separada. O uso do armazenamento para registro em log de eventos e da investigação de integridade incorrerá em cobranças de serviço.

## Log de auditoria
Por padrão, esse log (anteriormente conhecido como "log operacional") é gerado pelo Azure. Os logs são preservados por 90 dias no repositório de Logs de Eventos do Azure. Saiba mais sobre esses logs lendo o artigo [Exibir logs de eventos e de auditoria](insights-debugging-with-events.md).

## Log de eventos de alerta
Esse log só será gerado se você o tiver habilitado para cada balanceador de carga, conforme detalhado acima. Os dados são armazenados na conta de armazenamento especificada quando você habilitou o registro em log. As informações são registradas no formato JSON, conforme pode ser visto abaixo.

	
	{
    "time": "2016-01-26T10:37:46.6024215Z",
	"systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
	

A saída JSON mostra a propriedade *eventname*, que descreverá o motivo para o balanceador de carga ter criado um alerta. Nesse caso, o alerta gerado foi devido à exaustão da porta TCP causada pelos limites de NAT do IP de origem (SNAT).

## Log de investigação de integridade
Esse log só será gerado se você o tiver habilitado para cada balanceador de carga, conforme detalhado acima. Os dados são armazenados na conta de armazenamento especificada quando você habilitou o registro em log. Um contêiner denominado 'insights-logs-loadbalancerprobehealthstatus' é criado e os seguintes dados são registrados:

		{
	    "records":

	    {
	   		"time": "2016-01-26T10:37:46.6024215Z",
	        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
	        "category": "LoadBalancerProbeHealthStatus",
	        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
	        "operationName": "LoadBalancerProbeHealthStatus",
	        "properties": {
	            "publicIpAddress": "40.83.190.158",
	            "port": "81",
	            "totalDipCount": 2,
	            "dipDownCount": 1,
	            "healthPercentage": 50.000000
	        }
	    },
	    {
	        "time": "2016-01-26T10:37:46.6024215Z",
			"systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
	        "category": "LoadBalancerProbeHealthStatus",
	        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
	        "operationName": "LoadBalancerProbeHealthStatus",
	        "properties": {
	            "publicIpAddress": "40.83.190.158",
	            "port": "81",
	            "totalDipCount": 2,
	            "dipDownCount": 0,
	            "healthPercentage": 100.000000
	        }
	    }

	]
	}

A saída JSON mostra no campo de propriedades as informações básicas do status da integridade da investigação. A propriedade *dipDownCount* mostra o número total de instâncias no back-end que não estão recebendo tráfego de rede devido à falha nas respostas de investigação.

## Exibir e analisar o log de auditoria
Você pode exibir e analisar dados do log de auditoria usando qualquer um dos seguintes métodos:

- **Ferramentas do azure:** recupere informações dos logs de auditoria por meio do Azure PowerShell, a CLI (Interface de Linha de Comando) do Azure, a API REST do Azure ou o portal de visualização do Azure. Instruções passo a passo para cada método são detalhadas no artigo [Operações de auditoria com o Gerenciador de Recursos](resource-group-audit.md).
- **Power BI:** se ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing), você poderá testá-lo gratuitamente. Usando o [Pacote de conteúdo dos Logs de Auditoria do Azure para Power BI](https://support.powerbi.com/knowledgebase/articles/742695), você pode analisar seus dados com painéis pré-configurados que você pode usar como estão ou personalizar.

## Exibir e analisar o log de eventos de investigação de integridade 
Você precisa se conectar à sua conta de armazenamento e recuperar as entradas de log JSON para logs de eventos e investigação de integridade. Depois de baixar os arquivos JSON, você pode convertê-los em CSV e exibi-lo no Excel, no PowerBI ou em qualquer outra ferramenta de visualização de dados.

>[AZURE.TIP] Se estiver familiarizado com o Visual Studio e os conceitos básicos de alteração de valores de constantes e variáveis em C# , você poderá usar as [ferramentas de conversor de log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no Github.

## Recursos adicionais

- Postagem de blog [Visualizar os logs de auditoria do Azure com o Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx).
- Postagem de blog [Exibir e analisar logs de auditoria do Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).

<!---HONumber=AcomDC_0204_2016-->