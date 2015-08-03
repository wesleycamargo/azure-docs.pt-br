<properties
   pageTitle="Suporte do Gerenciador de Recursos para a Visualização do Gerenciador de Tráfego do Azure | Microsoft Azure"
   description="Usando o Powershell para o Gerenciador de Tráfego com o Gerenciador de Recursos do Azure (ARM) no modo de visualização"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma" />






# Suporte do Gerenciador de Recursos do Azure para a Visualização do Gerenciador de Tráfego do Azure
O Gerenciador de Recursos do Azure (ARM) é a nova estrutura de gerenciamento de serviços do Azure. Os perfis do Gerenciador de Tráfego do Azure agora podem ser gerenciados usando ferramentas e APIs baseadas no Gerenciador de Recursos do Azure. Para saber mais sobre o Gerenciador de Recursos do Azure, confira [Usando grupos de recursos para gerenciar recursos do Azure](../azure-preview-portal-using-resource-groups.md).

>[AZURE.NOTE]O suporte do ARM para o Gerenciador de Tráfego atualmente está disponível apenas no modo de Visualização, incluindo a API REST, o PowerShell do Azure, a CLI do Azure e o SDK do .NET.



## Modelo de recursos

O Gerenciador de Tráfego do Azure é configurado usando um conjunto de configurações chamado de perfil do Gerenciador de Tráfego. Esse perfil contém as configurações de DNS, as configurações de roteamento de tráfego, as configurações de monitoramento de ponto de extremidade e a lista de pontos de extremidade de serviço para os quais o tráfego será roteado.

No ARM, cada perfil do Gerenciador de Tráfego é representado por um recurso do ARM, do tipo “TrafficManagerProfiles”, gerenciado pelo provedor de recursos “Microsoft.Network”. No nível da API REST, o URI para cada perfil é:

	https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## Comparação com a API de Gerenciamento do Serviço do Gerenciador de Tráfego do Azure

Usar o ARM para configurar perfis do Gerenciador de Tráfego fornece acesso para o mesmo conjunto de recursos do Gerenciador de Tráfego, como a API de gerenciamento de serviço (não ARM), exceto as Limitações de Visualização abaixo.

No entanto, embora os recursos permanecem os mesmos, alguns termos foram alterados:

- O “método de balanceamento de carga”, que determina como o Gerenciador de Tráfego escolhe para qual ponto de extremidade rotear o tráfego ao responder a uma determinada solicitação de DNS, foi renomeado como “método de roteamento de tráfego”.

- O método de roteamento de tráfego “round robin” foi renomeado como “ponderado”.

- O método de roteamento de tráfego “failover” foi renomeado como “prioridade”.

## Limitações de visualização
Como o suporte do Gerenciador de Recursos do Azure para o Gerenciador de Tráfego é um serviço de visualização, há algumas limitações:

- Perfis do Gerenciador de Tráfego criados usando a API, as ferramentas e o Portal de gerenciamento de serviço (não ARM) não estão disponíveis por meio do ARM e vice-versa. No momento, não há suporte para a migração de perfis não ARM para APIs do ARM.

- 	A API REST não dá suporte à aplicação de patches de perfis do Gerenciador de Tráfego. Para atualizar uma propriedade de perfil, você deve obter o perfil e inserir o perfil modificado.
- 	Somente pontos de extremidade “externos” têm suporte. Eles ainda podem ser usados para usar o Gerenciador de Tráfego com serviços baseados no Azure e, ao fazer isso, esses pontos de extremidade serão cobrados pela taxa de pontos de extremidades internos. (O único impacto de usar pontos de extremidade externos é que eles não serão desabilitados ou excluídos automaticamente se o serviço do Azure subjacente tiver sido desabilitado ou excluído. Em vez disso, você precisará desabilitar ou excluir o ponto de extremidade manualmente).
-	O Gerenciador de Tráfego do Azure ainda não está disponível no portal do Azure, apenas no portal clássico.

## Configurando o PowerShell do Azure

Estas instruções usam o PowerShell do Microsoft Azure, que precisa ser configurado usando as etapas a seguir.

Para quem não for usuário do PowerShell, as mesmas operações poderão ser executadas em outras interfaces.

### Etapa 1
Instale a última versão do PowerShell do Azure, disponível na página de downloads do Azure.
### Etapa 2
Alterne modo do PowerShell para usar os cmdlets do ARM. Mais informações estão disponíveis em Usando o Windows Powershell com o Gerenciador de Recursos.

	PS C:> Switch-AzureMode -Name AzureResourceManager
### Etapa 3
Faça logon na sua conta do Azure.

	PS C:> Add-AzureAccount

Você deverá autenticar com suas credenciais.

### Etapa 4
Escolha quais das suas assinaturas do Azure deseja usar.

	PS C:> Select-AzureSubscription -SubscriptionName "MySubscription"

Para ver uma lista das assinaturas disponíveis, use o cmdlet “Get-AzureSubscription”.

### Etapa 5

 O serviço do Gerenciador de Tráfego é gerenciado pelo provedor de recursos Microsoft.Network. Sua assinatura do Azure precisa ser registrada para usar esse provedor de recursos antes de poder usar o Gerenciador de Tráfego pelo ARM. Essa operação deve ser executa apenas uma vez para cada assinatura.

	PS C:> Register-AzureProvider –ProviderNamespace Microsoft.Network

### Etapa 6
Crie um grupo de recursos (pule esta etapa se você estiver usando um grupo de recursos existente)

	PS C:> New-AzureResourceGroup -Name MyAzureResourceGroup -location "West US"

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. No entanto, como todos os recursos de perfil do Gerenciador de Tráfego são globais, não regionais, a escolha do local do grupo de recursos não afeta o Gerenciador de Tráfego do Azure.

## Criar um perfil do Gerenciador de Tráfego

Para criar um perfil do Gerenciador de Tráfego, use o cmdlet New-AzureTrafficManagerProfile:

	PS C:> $profile = New-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Os parâmetros são os seguintes:

- Name: o nome de recurso ARM para o recurso do perfil do Gerenciador de Tráfego. Perfis no mesmo grupo de recursos devem ter nomes exclusivos. Esse nome é diferente do nome DNS usado para consultas de DNS.

-	ResourceGroupName: o nome do grupo de recursos do ARM que contém o recurso de perfil.

-	TrafficRoutingMethod: especifica o método de roteamento de tráfego, usado para determinar qual ponto de extremidade é retornado na resposta a consultas de DNS de entrada. Os valores possíveis são “Desempenho”, “Ponderado” ou “Prioridade”.

-	RelativeDnsName: especifica o nome DNS relativo fornecido por esse perfil do Gerenciador de Tráfego. Esse valor é combinado ao nome de domínio DNS usado pelo Gerenciador de Tráfego do Azure para formar o nome de domínio totalmente qualificado (FQDN) do perfil. Por exemplo, o valor "contoso" fornecerá a um perfil do Gerenciador de Tráfego o nome totalmente qualificado “contoso.trafficmanager.net”.

-	TLL: especifica a vida útil (TLL) do DNS em segundos. Isso informa os resolvedores DNS locais e os clientes DNS o tempo necessário para armazenar em cache as respostas DNS fornecidas por esse perfil do Gerenciador de Tráfego.

-	MonitorProtocol: especifica o protocolo a ser usado para monitorar a integridade do ponto de extremidade. Os valores possíveis são “HTTP” e “HTTPS”.

-	MonitorPort: especifica a porta TCP usada para monitorar a integridade do ponto de extremidade.

-	MonitorPath: especifica o caminho relativo ao nome de domínio de ponto de extremidade usado para testar a integridade do ponto de extremidade.

O cmdlet cria um perfil do Gerenciador de Tráfego do Azure e retorna um objeto de perfil correspondente. Nesse momento, o perfil não contém pontos de extremidade. Confira [Atualizar um perfil do Gerenciador de Tráfego](#update-a-traffic-manager-profile) para obter detalhes sobre como adicionar pontos de extremidade a um perfil do Gerenciador de Tráfego.

## Obter um perfil do Gerenciador de Tráfego

Para recuperar um objeto existente de perfil do Gerenciador de Tráfego, use o cmdlet Get-AzureTrafficManagerProfle:

	PS C:> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup

Esse cmdlet retorna um objeto de perfil do Gerenciador de Tráfego.

## Atualizar um perfil do Gerenciador de Tráfego [](#update-traffic-manager-profile)

A modificação de perfis do Gerenciador de Tráfego, por exemplo, para adicionar ou remover pontos de extremidade ou modificar configurações de perfil, segue um processo de três etapas:

1.	Recupere o perfil usando Get-AzureTrafficManagerProfile (ou use o perfil retornado por New-AzureTrafficManagerProfile).

2.	Modifique o perfil adicionando pontos de extremidade, removendo pontos de extremidade, alterando os parâmetros de ponto de extremidade ou alterando os parâmetros de perfil. Essas alterações são offline, ou seja, apenas o objeto local que representa o perfil é alterado.

3.	Confirme as alterações usando o cmdlet Set-AzureTrafficManagerProfile. Isso substitui o perfil existente no Gerenciador de Tráfego do Azure pelo perfil fornecido.

Essa operação é melhor explicada nos exemplos abaixo:

### Adicionar pontos de extremidade a um perfil

Pontos de extremidade podem ser adicionados a um perfil do Gerenciador de Tráfego usando o cmdlet “Add-AzureTrafficManagerEndpointConfig”:

	PS C:> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:> Add-AzureTrafficManagerEndpointConfig –EndpointName site1 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target site1.contoso.com –EndpointStatus Enabled –Weight 10 –Priority 1 –EndpointLocation “West US”
	PS C:> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Os parâmetros para o Add-AzureTrafficManagerEndpointConfig são os seguintes:

- EndpointName: o nome do ponto de extremidade. Pontos de extremidade no mesmo perfil devem ter nomes distintos. Eles são usados para referenciar o ponto de extremidade durante as operações de gerenciamento de serviço. Não se tratam do nome DNS do ponto de extremidade.

-	TrafficManagerProfile: o objeto de perfil do Gerenciador de Tráfego ao qual o ponto de extremidade será adicionado.

-	Type: o tipo de ponto de extremidade do Gerenciador de Tráfego. Atualmente, apenas o tipo “ExternalEndpoint” tem suporte por meio da API do ARM (confira [Limitações de Visualização](#preview-limitations)).

-	Target: o nome DNS totalmente qualificado do ponto de extremidade. O Gerenciador de Tráfego retorna esse valor nas respostas DNS para direcionar o tráfego para esse ponto de extremidade.

-	EndpointStatus: especifica o status do ponto de extremidade. Se o ponto de extremidade estiver habilitado, esse parâmetro é analisado para determinar a integridade do ponto de extremidade e é incluído no método de roteamento de tráfego. Os valores possíveis são “Habilitado” ou “Desabilitado”.

-	Weight: especifica o peso atribuído ao ponto de extremidade. Esse parâmetro é usado apenas se o perfil do Gerenciador de Tráfego for configurado para usar o método de roteamento de tráfego “ponderado”. Os valores possíveis estão na faixa de 1 a 1000.

-	Priority: especifica a prioridade do ponto de extremidade ao usar o método de roteamento de tráfego “prioridade”. A prioridade deve estar na faixa de 1 a 1000. Valores mais baixos representam uma prioridade mais alta.

-	EndpointLocation: especifica o local do ponto de extremidade externo, para uso com o método de roteamento de tráfego “Desempenho”. Para obter uma lista de locais possíveis, confira Get-AzureLocation.

O status do ponto de extremidade, a prioridade e o peso são parâmetros opcionais. Caso sejam omitidos, eles não são aprovados pelo PowerShell e os padrões do servidor serão aplicados.

### Remover pontos de extremidade de um perfil

Para remover um ponto de extremidade de um perfil, use “Remove-AzureTrafficmanagerEndpointConfig”, especificando o nome do ponto de extremidade a ser removido:

	PS C:> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:> Remove-AzureTrafficManagerEndpointConfig –EndpointName site1 –TrafficManagerProfile $profile
	PS C:> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

A sequência de operações para adicionar ou remover pontos de extremidade também pode ser 'transferida', passando o objeto de perfil por meio do pipe em vez de como um parâmetro. Por exemplo:

	PS C:> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerEndpointConfig –EndpointName site1 | Set-AzureTrafficManagerProfile

### Alterar as configurações de perfil ou de ponto de extremidade

Os parâmetros de perfil e de ponto de extremidade podem ser alterados offline e as alterações podem ser confirmadas usando Set-AzureTrafficManagerProfile. A única exceção é que o perfil RelativeDnsName não pode ser alterado depois de ser criado (para alterar esse valor, exclua e recrie o perfil). Por exemplo, para alterar a TTL do perfil e o status do primeiro ponto de extremidade:

	PS C:> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:> $profile.Ttl = 300
	PS C:> $profile.Endpoints[0].EndpointStatus = "Disabled"
	PS C:> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

### Excluir um perfil do Gerenciador de Tráfego
Para excluir um perfil do Gerenciador de Tráfego, use o cmdlet Remove-AzureTrafficManagerProfile, especificando o nome do perfil e o nome do grupo de recursos:

	PS C:> Remove-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup [-Force]

Esse cmdlet solicitará uma confirmação. A opção '-Force' pode ser usada para suprimir essa solicitação. O perfil a ser excluído também pode ser especificado usando um objeto de perfil:

	PS C:> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:> Remove-AzureTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

Essa sequência também pode ser transferida:

	PS C:> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerProfile [-Force]


## Veja também

[Visão geral do Gerenciador de Tráfego](traffic-manager-overview.md)

[Introdução aos cmdlets do Azure](https://msdn.microsoft.com/library/jj554332.aspx)
 

<!---HONumber=July15_HO4-->