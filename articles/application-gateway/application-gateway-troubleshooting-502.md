<properties
   pageTitle="Solucionar problemas de erros de gateway inválido (502) do Application Gateway | Microsoft Azure"
   description="Saiba como solucionar problemas de erros 502 do Application Gateway"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/15/2016"
   ms.author="amitsriva" />

#Solução de problemas de erros de gateway incorreto no Application Gateway

##Visão geral
Após a configuração de um Application Gateway do Azure, um dos erros que os usuários podem encontrar é "Erro de Servidor: 502 - o servidor Web recebeu uma resposta inválida ao atuar como gateway ou proxy de servidor". Isso pode ocorrer pelos seguintes motivos principais -

- O pool de back-end do Application Gateway do Azure não está configurado ou está vazio.
- Nenhuma das VMs ou instâncias no Conjunto de Escala de VM está íntegra.
- VMs de back-end ou instâncias do Conjunto de Escala de VM não estão respondendo à investigação de integridade padrão.
- Configuração inválida ou incorreta de investigações de integridade personalizadas.
- Tempo limite de solicitação ou problemas de conectividade com solicitações de usuário.


##BackendAddressPool vazio
###Causa
Se o Application Gateway não tiver VMs ou o Conjunto de Escala de VM configurados no pool de endereços de back-end, não poderá encaminhar solicitações de clientes e gerará um erro de gateway incorreto.

###Solução
Verifique se o pool de endereços de back-end não está vazio. Isso pode ser feito por meio do PowerShell, da CLI ou do portal.

	
	Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"

A saída do cmdlet acima deve conter o pool de endereços de back-end não vazio. A seguir há um exemplo em que são retornados dois pools que estão configurados com endereços FQDN ou IP para VMs de back-end. O estado de provisionamento de BackendAddressPool deve ser 'Bem-sucedido'.
	
		BackendAddressPoolsText: 
				[{
					"BackendAddresses": [{
						"ipAddress": "10.0.0.10",
						"ipAddress": "10.0.0.11"
					}],
					"BackendIpConfigurations": [],
					"ProvisioningState": "Succeeded",
					"Name": "Pool01",
					"Etag": "W/"00000000-0000-0000-0000-000000000000"",
					"Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
				}, {
					"BackendAddresses": [{
						"Fqdn": "xyx.cloudapp.net",
						"Fqdn": "abc.cloudapp.net"
					}],
					"BackendIpConfigurations": [],
					"ProvisioningState": "Succeeded",
					"Name": "Pool02",
					"Etag": "W/"00000000-0000-0000-0000-000000000000"",
					"Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
				}]
	
	
## Instâncias não íntegras em BackendAddressPool

###Causa
Se todas as instâncias do BackendAddressPool estiverem não íntegras, o Application Gateway não terá um back-end para o qual rotear a solicitação do usuário. Esse também pode ser o caso quando instâncias de back-end estão íntegras, mas não têm o aplicativo necessário implantado.

###Solução
Verifique se as instâncias estão íntegras e se o aplicativo está configurado corretamente. Verifique se as instâncias de back-end podem responder a um ping de outra VM na mesma VNet. Se a configuração tiver sido feita com um ponto de extremidade público, verifique se uma solicitação do navegador ao aplicativo Web é operacional.


##Problemas com a investigação de integridade padrão
###Causa
Erros 502 também podem ser indicadores frequentes de que a investigação de integridade padrão não consegue acessar VMs de back-end. Quando uma instância do Application Gateway é provisionada, configura automaticamente um teste de integridade padrão para cada BackendAddressPool usando as propriedades de BackendHttpSetting. Nenhuma entrada do usuário é necessária para definir essa investigação. Especificamente, quando uma regra de balanceamento de carga é configurada, é feita uma associação entre BackendHttpSetting e BackendAddressPool. Uma investigação padrão é configurada para cada um dessas associações, e o Application Gateway inicia uma conexão de verificação de integridade periódica para cada instância em BackendAddressPool na porta especificada no elemento BackendHttpSetting. A tabela a seguir lista os valores associados à investigação de integridade padrão.


|Propriedades da investigação | Valor | Descrição|
|---|---|---|
| URL de investigação| http://127.0.0.1/ | Caminho da URL |
| Intervalo | 30 | Intervalo da investigação em segundos |
| Tempo limite | 30 | Tempo limite da investigação em segundos |
| Limite não íntegro | 3 | Contagem de repetições da investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite de não íntegro. |

###Solução
- Verifique se um site padrão está configurado e está escutando em 127.0.0.1.
- Se BackendHttpSetting especificar uma porta diferente de 80, o site padrão deverá ser configurado para escutar nessa porta.
- A chamada para http://127.0.0.1:port deve retornar um código de resultado HTTP 200. Ele deve ser retornado dentro do período de tempo limite de 30 segundos.
- Verifique se a porta configurada está aberta e se não há regras de firewall ou Grupos de Segurança de Rede do Azure que bloqueiam o tráfego de entrada ou de saída na porta configurada.
- Se VMs clássicas do Azure ou o Serviço de Nuvem forem usados com FQDN ou IP Público, verifique se o [ponto de extremidade](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md) correspondente está aberto.
- Se a VM for configurada por meio do Azure Resource Manager e estiver fora da rede virtual em que o Application Gateway está implantado, o [Grupo de Segurança de Rede](../virtual-network/virtual-networks-nsg.md) deverá ser configurado para permitir o acesso na porta desejada.


##Problemas com a investigação de integridade personalizada
###Causa
Investigações de integridade personalizadas oferecem flexibilidade adicional para o comportamento de investigação padrão. Ao usar investigações personalizadas, os usuários podem configurar o intervalo de investigação, a URL e o caminho a testar e quantas respostas com falha devem ser aceitas antes de marcar a instância do pool de back-end como não íntegra. As propriedades adicionais a seguir são adicionadas.


|Propriedades da investigação| Descrição|
|---|---|
| Nome | O nome da investigação. Este é o nome usado para se referir à investigação nas configurações de HTTP de back-end. |
| Protocolo | O protocolo usado para enviar a investigação. HTTP é o único protocolo válido. |
| Host | O nome do host para enviar a investigação. Aplicável somente quando vários sites são configurados no Application Gateway. Isso é diferente do nome de host de VM. |
| Caminho | O caminho relativo da investigação. Um caminho válido começa com '/'. A investigação é enviada a <protocolo>://<host>:<porta><caminho> |
| Intervalo | Intervalo de investigação em segundos. Este é o intervalo de tempo entre duas investigações consecutivas.|
| Tempo limite | Tempo limite da investigação em segundos. A investigação é marcada como com falha se não for recebida uma resposta válida dentro desse período de tempo limite. |
| Limite não íntegro | Contagem de repetições da investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite de não íntegro. |


###Solução
Valide se a Investigação de Integridade Personalizada está configurada corretamente conforme a tabela acima. Além das etapas de solução de problemas acima, também verifique os itens a seguir.

- Verifique se o Protocolo está definido apenas como HTTP. Atualmente, não há suporte a HTTPS.
- Verifique se a investigação foi especificada corretamente, conforme o [guia](application-gateway-create-probe-ps.md).
- Se o Application Gateway estiver configurado para um único site, por padrão, o nome do Host deverá ser especificado como '127.0.0.1', a menos que seja configurado de outra forma na investigação personalizada.
- Verifique se uma chamada a http://\<host>:<porta><caminho> retorna um código de resultado HTTP 200.
- Verifique se Interval, Time-out e UnhealtyThreshold estão dentro dos intervalos aceitáveis.


##Tempo limite de solicitação
###Causa
Quando é recebida uma solicitação de usuário, o Application Gateway aplica as regras configuradas para a solicitação e a roteia para uma instância de pool de back-end. Ele aguarda por um intervalo de tempo configurável para receber uma resposta da instância de back-end. Por padrão, o intervalo é de **30 segundos**. Se o Application Gateway não receber uma resposta do aplicativo de back-end nesse intervalo, a solicitação de usuário obterá um erro 502.

###Solução
O Application Gateway permite aos usuários definir essa configuração por meio de BackendHttpSetting, que pode então ser aplicado a diferentes pools. Pools de back-end diferentes podem ter BackendHttpSetting diferentes e, assim, um tempo limite de solicitação diferente configurado.

	New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60

##Próximas etapas

Se as etapas acima não resolverem o problema, abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).

<!---HONumber=AcomDC_0720_2016-->