<properties
   pageTitle="Suporte do Gerenciador de Recursos para a Visualização do Gerenciador de Tráfego do Azure | Microsoft Azure"
   description="Usando o Powershell para o Gerenciador de Tráfego com o Gerenciador de Recursos do Azure (ARM) no modo de visualização"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/25/2016"
   ms.author="joaoma" />

# Suporte do Gerenciador de Recursos do Azure para a Visualização do Gerenciador de Tráfego do Azure
O Gerenciador de Recursos do Azure (ARM) é a nova estrutura de gerenciamento de serviços do Azure. Os perfis do Gerenciador de Tráfego do Azure agora podem ser gerenciados usando ferramentas e APIs baseadas no Gerenciador de Recursos do Azure. Para saber mais sobre o Gerenciador de Recursos do Azure, confira [Usando grupos de recursos para gerenciar recursos do Azure](../azure-preview-portal-using-resource-groups.md).

## Modelo de recursos

O Gerenciador de Tráfego do Azure é configurado usando um conjunto de configurações chamado de perfil do Gerenciador de Tráfego. Esse perfil contém as configurações de DNS, as configurações de roteamento de tráfego, as configurações de monitoramento de ponto de extremidade e a lista de pontos de extremidade de serviço para os quais o tráfego será roteado.

No ARM, cada perfil do Gerenciador de Tráfego é representado por um recurso do ARM, do tipo “TrafficManagerProfiles”, gerenciado pelo provedor de recursos “Microsoft.Network”. No nível da API REST, o URI para cada perfil é:

	https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## Comparação com a API de Gerenciamento do Serviço do Gerenciador de Tráfego do Azure

Usar o ARM para configurar perfis do Gerenciador de Tráfego fornece acesso para o mesmo conjunto de recursos do Gerenciador de Tráfego, como a API de Gerenciamento de Serviço do Azure (ASM), exceto as Limitações de Visualização abaixo.

No entanto, embora os recursos permanecem os mesmos, alguns termos foram alterados:

- O “método de balanceamento de carga”, que determina como o Gerenciador de Tráfego escolhe para qual ponto de extremidade rotear o tráfego ao responder a uma determinada solicitação de DNS, foi renomeado como “método de roteamento de tráfego”.

- O método de roteamento de tráfego “round robin” foi renomeado como “ponderado”.

- O método de roteamento de tráfego “failover” foi renomeado como “prioridade”.

## Limitações
Atualmente, há algumas limitações no suporte do ARM para o Gerenciador de Tráfego do Azure:

- Os perfis do Gerenciador de Tráfego criados usando a API de Gerenciamento de Serviço do azure (ASM) (não ARM), as ferramentas e o portal ‘clássico’ não estão disponíveis por meio do ARM e vice-versa. A migração de perfis do ASM para APIs do ARM não tem suporte no momento, somente a exclusão e a recriação do perfil.

- Os pontos de extremidade ‘aninhados’ do Gerenciador de Tráfego têm suporte por meio da API do ARM, do PowerShell do ARM e da CLI do Azure em modo ARM. Atualmente, eles não têm suporte no Portal do Azure (que também usa a API do ARM).

- Os pontos de extremidade do Gerenciador de Tráfego do tipo 'AzureEndpoints', ao fazer referência a um Aplicativo Web, pode apenas fazer referência ao [slot do Aplicativo Web](../app-service-web/web-sites-staged-publishing.md) padrão (produção). Os slots personalizados ainda não são permitidos. Como alternativa, os slots personalizados podem ser configurados usando o tipo 'ExternalEndpoints'.

## Configurando o PowerShell do Azure

Estas instruções usam o PowerShell do Microsoft Azure, que precisa ser configurado usando as etapas a seguir.

Para usuários que não sejam do PowerShell ou do Windows, operações análogas poderão ser executadas por meio da CLI do Azure. Todas as operações, com exceção do gerenciamento de perfis do Gerenciador de Tráfego ‘aninhado’, também estão disponíveis por meio do Portal do Azure.

### Etapa 1
Instale a última versão do PowerShell do Azure, disponível na página de downloads do Azure.

### Etapa 2
Faça logon na sua conta do Azure.

	PS C:\> Login-AzureRmAccount

Você deverá se autenticar com suas credenciais.

### Etapa 3
Escolha quais das suas assinaturas do Azure deseja usar.

	PS C:\> Set-AzureRmContext -SubscriptionName "MySubscription"

Para ver uma lista das assinaturas disponíveis, use o cmdlet “Get-AzureRmSubscription”.

### Etapa 4

O serviço do Gerenciador de Tráfego é gerenciado pelo provedor de recursos Microsoft.Network. Sua assinatura do Azure precisa ser registrada para usar esse provedor de recursos antes de poder usar o Gerenciador de Tráfego pelo ARM. Essa operação deve ser executa apenas uma vez para cada assinatura.

	PS C:\> Register-AzureRmResourceProvider –ProviderNamespace Microsoft.Network

### Etapa 5
Crie um grupo de recursos (pule esta etapa se você estiver usando um grupo de recursos existente)

	PS C:\> New-AzureRmResourceGroup -Name MyRG -Location "West US"

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. No entanto, como todos os recursos de perfil do Gerenciador de Tráfego são globais, não regionais, a escolha do local do grupo de recursos não afeta o Gerenciador de Tráfego do Azure.

## Criar um perfil do Gerenciador de Tráfego

Para criar um perfil do Gerenciador de Tráfego, use o cmdlet New-AzureRmTrafficManagerProfile:

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Os parâmetros são os seguintes:

- Name: o nome de recurso ARM para o recurso do perfil do Gerenciador de Tráfego. Perfis no mesmo grupo de recursos devem ter nomes exclusivos. Esse nome é diferente do nome DNS usado para consultas de DNS.

- ResourceGroupName: o nome do grupo de recursos do ARM que contém o recurso de perfil.

- TrafficRoutingMethod: especifica o método de roteamento de tráfego, usado para determinar qual ponto de extremidade é retornado na resposta a consultas de DNS de entrada. Os valores possíveis são “Desempenho”, “Ponderado” ou “Prioridade”.

- RelativeDnsName: especifica o nome DNS relativo fornecido por esse perfil do Gerenciador de Tráfego. Esse valor é combinado ao nome de domínio DNS usado pelo Gerenciador de Tráfego do Azure para formar o nome de domínio totalmente qualificado (FQDN) do perfil. Por exemplo, o valor ‘contoso’ fornecerá a um perfil do Gerenciador de Tráfego o nome totalmente qualificado ‘contoso.trafficmanager.net’.

- TLL: especifica a vida útil (TLL) do DNS em segundos. Isso informa os resolvedores DNS locais e os clientes DNS o tempo necessário para armazenar em cache as respostas DNS fornecidas por esse perfil do Gerenciador de Tráfego.

- MonitorProtocol: especifica o protocolo a ser usado para monitorar a integridade do ponto de extremidade. Os valores possíveis são “HTTP” e “HTTPS”.

- MonitorPort: especifica a porta TCP usada para monitorar a integridade do ponto de extremidade.

- MonitorPath: especifica o caminho relativo ao nome de domínio de ponto de extremidade usado para testar a integridade do ponto de extremidade.

O cmdlet cria um perfil do Gerenciador de Tráfego do Azure e retorna um objeto de perfil correspondente. Neste momento, o perfil não contém pontos de extremidade. Confira [Adicionando pontos de extremidade do Gerenciador de Tráfego](#adding-traffic-manager-endpoints) para obter detalhes sobre como adicionar pontos de extremidade a um perfil do Gerenciador de Tráfego.

## Obter um perfil do Gerenciador de Tráfego

Para recuperar um objeto existente de perfil do Gerenciador de Tráfego, use o cmdlet Get-AzureRmTrafficManagerProfle:

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG

Esse cmdlet retorna um objeto de perfil do Gerenciador de Tráfego.

## Atualizar um perfil do Gerenciador de Tráfego [](#update-traffic-manager-profile)

A modificação de perfis do Gerenciador de Tráfego, por exemplo, para adicionar ou remover pontos de extremidade ou modificar configurações de perfil, segue um processo de três etapas:

1.	Recupere o perfil usando Get-AzureRmTrafficManagerProfile (ou use o perfil retornado por New-AzureRmTrafficManagerProfile).

2.	Modifique o perfil adicionando pontos de extremidade, removendo pontos de extremidade, alterando os parâmetros de ponto de extremidade ou alterando os parâmetros de perfil. Essas alterações são operações offline, ou seja, apenas o objeto local que representa o perfil é alterado.

3.	Confirme as alterações usando o cmdlet Set-AzureRmTrafficManagerProfile. Isso substitui o perfil existente no Gerenciador de Tráfego do Azure pelo perfil fornecido.

Todas as propriedades de perfil podem ser alteradas, exceto RelativeDnsName do perfil, que não poderá ser alterado depois que o perfil for criado (para alterar esse valor, exclua e recrie o perfil).

Por exemplo, para alterar a TTL do perfil:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG
	PS C:\> $profile.Ttl = 300
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

## Adicionar Pontos de Extremidade do Gerenciador de Tráfego
Há três tipos de pontos de extremidade do Gerenciador de Tráfego: 1. Pontos de extremidade do Azure: representam os serviços hospedados no Azure. 2. Pontos de extremidade externos: representam os serviços hospedados fora do Azure. 3. Pontos de extremidade aninhados: são usados para construir hierarquias aninhadas de perfis do Gerenciador de Tráfego, para habilitar as configurações avançadas de roteamento de tráfego para aplicativos mais complexos. Eles ainda não têm suporte da API do ARM.

Em todos os três casos, os pontos de extremidade podem ser adicionados de duas maneiras: 1. Usando um processo de três etapas semelhante ao descrito em [Atualizar um perfil do Gerenciador de Tráfego](#update-traffic-manager-profile): obtenha o objeto de perfil usando Get-AzureRmTrafficManagerProfile; atualize-o offline para adicionar um ponto de extremidade usando Add-AzureRmTrafficManagerEndpointConfig; carregue as alterações no Gerenciador de Tráfego do Azure usando Set-AzureRmTrafficManagerProfile. A vantagem desse método é que várias alterações de ponto de extremidade podem ser feitas em uma única atualização. 2. Usando o cmdlet New-AzureRmTrafficManagerEndpoint. Isso adiciona um ponto de extremidade a um perfil existente do Gerenciador de Tráfego em uma única operação.

### Adicionando pontos de extremidade do Azure

Os pontos de extremidade do Azure fazem referência a outros serviços hospedados no Azure. Atualmente, há suporte para três tipos de pontos de extremidade do Azure: 1. Aplicativos Web do Azure 2. Serviços de nuvem ‘clássicos’ (que podem conter um serviço PaaS ou máquinas virtuais IaaS) 3. Recursos Microsoft.Network/publicIpAddress ARM (que podem ser anexados ao balanceador de carga ou a uma NIC de máquina virtual). Observe que o publicIpAddress deve ter um nome DNS atribuído para poder ser usado no Gerenciador de Tráfego.

Em cada caso: - O serviço é especificado usando o parâmetro 'targetResourceId' de Add-AzureRmTrafficManagerEndpointConfig ou de New-AzureRmTrafficManagerEndpoint. - O 'Target' e o 'EndpointLocation' não devem ser especificados, mas são implícitos por meio do TargetResourceId especificado acima - A especificação de 'Weight' é opcional. Os pesos só serão usados se o perfil for configurado para usar o método de roteamento de tráfego ‘Weighted’; caso contrário, eles serão ignorados. Se especificado, ele deverá estar no intervalo de 1...1000. O valor padrão é '1'. -A especificação de 'Priority' é opcional. As prioridades só serão usadas se o perfil for configurado para usar o método de roteamento de tráfego ‘Priority’; caso contrário, elas serão ignoradas. Os valores válidos vão de 1 a 1000 (valores mais baixos têm prioridade mais alta). Se especificados para um ponto de extremidade, deverão ser especificados para todos os pontos de extremidade. Se omitidos, os valores padrão começando por 1, 2, 3 etc. serão aplicados na ordem em que os pontos de extremidade forem fornecidos.

#### Exemplo 1: Adicionando pontos de extremidade do Aplicativo Web usando Add-AzureRmTrafficManagerEndpointConfig
Neste exemplo, criaremos um novo perfil do Gerenciador de Tráfego e adicionaremos dois pontos de extremidade de Aplicativo Web usando o cmdlet Add-AzureRmTrafficManagerEndpointConfig, depois confirmaremos o perfil atualizado no Gerenciador de Tráfego usando Set-AzureRmTrafficManagerProfile.

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> $webapp1 = Get-AzureRMWebApp -Name webapp1
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp1ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled
	PS C:\> $webapp2 = Get-AzureRMWebApp -Name webapp2
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp2ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### Exemplo 2: Adicionando um ponto de extremidade de serviço de nuvem 'clássico' usando New-AzureRmTrafficManagerEndpoint
Neste exemplo, um ponto de extremidade de Serviço de Nuvem 'clássico' é adicionado a um perfil do Gerenciador de Tráfego. Observe que, neste caso, optamos por especificar o perfil usando o nome do perfil e o nome do grupo de recursos em vez de passarmos um objeto de perfil (ambas as abordagens têm suporte).

	PS C:\> $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyCloudServiceEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $cloudService.Id –EndpointStatus Enabled

#### Exemplo 3: Adicionando um ponto de extremidade de publicIpAddress usando New-AzureRmTrafficManagerEndpoint
Neste exemplo, um recurso de endereço IP público ARM é adicionado ao perfil do Gerenciador de Tráfego. O endereço IP público deve ter um nome DNS configurado e pode ser vinculado à NIC de uma VM ou a um balanceador de carga.

	PS C:\> $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyIpEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $ip.Id –EndpointStatus Enabled

### Adicionando pontos de extremidade externos
O Gerenciador de Tráfego usa pontos de extremidade externos para direcionar o tráfego para serviços hospedados fora do Azure. Assim como acontece com os pontos de extremidade do Azure, os pontos de extremidade externos podem ser adicionados usando Add-AzureRmTrafficManagerEndpointConfig seguido por AzureRmTrafficManagerProfile Set ou por New-AzureRMTrafficManagerEndpoint.

Ao especificar pontos de extremidade externos: - o nome de domínio do ponto de extremidade deve ser especificado usando o parâmetro 'Target' - 'EndpointLocation' será necessário se o método de roteamento de tráfego ‘Performance’ for usado; caso contrário, será opcional. O valor deve ser um [nome de região do Azure válido](https://azure.microsoft.com/regions/). - ‘Weight’ e ‘Priority’ são opcionais, assim como para os pontos de extremidade do Azure.

#### Exemplo 1: Adicionando pontos de extremidade externos usando Add-AzureRmTrafficManagerEndpointConfig e Set-AzureRmTrafficManagerProfile
Neste exemplo, podemos criar um novo perfil do Gerenciador de Tráfego, adicionar dois pontos de extremidade externos e confirmar as alterações.

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName eu-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName us-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-us.contoso.com –EndpointStatus Enabled
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### Exemplo 2: Adicionando pontos de extremidade externos usando New-AzureRmTrafficManagerEndpoint
Neste exemplo, adicionamos um ponto de extremidade externo a um perfil existente e especificado usando o nome do perfil e o nome do grupo de recursos.

	PS C:\> New-AzureRmTrafficManagerEndpoint –Name eu-endpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled

### Adicionando pontos de extremidade ‘aninhados’

O Gerenciador de Tráfego permite que você configure um perfil do Gerenciador de Tráfego (que chamaremos de perfil ‘filho’) como um ponto de extremidade em outro perfil do Gerenciador de Tráfego (que chamaremos de perfil ‘pai’).

O aninhamento do Gerenciador de Tráfego permite que você crie roteamento de tráfego e esquemas de failover mais flexíveis e poderosos para dar suporte às necessidades de implantações maiores e mais complexas. [Esta postagem de blog](https://azure.microsoft.com/blog/new-azure-traffic-manager-nested-profiles/) fornece vários exemplos.

Os pontos de extremidade aninhados são configurados no perfil pai, usando um tipo de ponto de extremidade específico, o ‘NestedEndpoints’. Ao especificar pontos de extremidade aninhados: - o ponto de extremidade (ou seja, o perfil filho) deve ser especificado usando o parâmetro 'targetResourceId' - O 'EndpointLocation' será necessário se o método de roteamento de tráfego 'Performance' for usado. Caso contrário, será opcional. O valor deve ser um [nome de uma região do Azure válida](http://azure.microsoft.com/regions/). - 'Weight' e 'Priority' são opcionais, assim como para os pontos de extremidade do Azure. - O parâmetro ‘MinChildEndpoints’ é opcional, o padrão é '1'. Se o número de pontos de extremidade disponíveis no perfil filho cair abaixo desse limite, o perfil pai considerará o perfil filho como ‘degradado’ e desviará o tráfego para os outros pontos de extremidade do perfil pai.


#### Exemplo 1: Adicionando pontos de extremidade aninhados usando Add-AzureRmTrafficManagerEndpointConfig e Set-AzureRmTrafficManagerProfile

Neste exemplo, podemos criar novos perfis pai e filho do Gerenciador de Tráfego, adicionar o filho como um ponto de extremidade aninhado no pai e confirmar as alterações. (Para resumir, não adicionaremos outros pontos de extremidade no perfil filho ou no perfil pai, embora normalmente eles também seriam necessários.)

	PS C:\> $child = New-AzureRmTrafficManagerProfile –Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> $parent = New-AzureRmTrafficManagerProfile –Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName child-endpoint –TrafficManagerProfile $parent –Type NestedEndpoints -TargetResourceId $child.Id –EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

#### Exemplo 2: Adicionando pontos de extremidade aninhados usando New-AzureRmTrafficManagerEndpoint

Neste exemplo, adicionamos um perfil filho existente como um ponto de extremidade aninhado a um perfil pai existente, especificado usando o nome do perfil e o nome do grupo de recursos.

	PS C:\> $child = Get-AzureRmTrafficManagerEndpoint –Name child -ResourceGroupName MyRG
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name child-endpoint –ProfileName parent -ResourceGroupName MyRG –Type NestedEndpoints -TargetResourceId $child.Id –EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2


## Atualizar um Ponto de Extremidade do Gerenciador de Tráfego
Há duas maneiras de atualizar um ponto de extremidade existente do Gerenciador de Tráfego: 1. Obtenha o perfil do Gerenciador de Tráfego usando Get-AzureRmTrafficManagerProfile, atualize as propriedades do ponto de extremidade no perfil e confirme as alterações usando Set-AzureRmTrafficManagerProfile. Esse método tem a vantagem de poder atualizar mais de um ponto de extremidade em uma única operação. 2. Obtenha o ponto de extremidade do Gerenciador de Tráfego usando Get-AzureRmTrafficManagerEndpoint, atualize as propriedades do ponto de extremidade e confirme as alterações usando Set-AzureRmTrafficManagerEndpoint. Esse método é mais simples, pois não requer a indexação na matriz Endpoints no perfil.

#### Exemplo 1: Atualizando pontos de extremidade usando Get-AzureRmTrafficManagerProfile e Set-AzureRmTrafficManagerProfile
Neste exemplo, modificaremos a prioridade em dois pontos de extremidade em um perfil existente.

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG
	PS C:\> $profile.Endpoints[0].Priority = 2
	PS C:\> $profile.Endpoints[1].Priority = 1
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

#### Exemplo 2: Atualizando um ponto de extremidade usando Get-AzureRmTrafficManagerEndpoint e Set-AzureRmTrafficManagerEndpoint
Neste exemplo, modificaremos o peso de um ponto de extremidade simples em um perfil existente.

	PS C:\> $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
	PS C:\> $endpoint.Weight = 20
	PS C:\> Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## Habilitando e desabilitando pontos de extremidade e perfis
O Gerenciador de Tráfego permite que os pontos de extremidade individuais sejam habilitados e desabilitados, além de permitir a habilitação e a desabilitação de perfis inteiros. Faça essas alterações ao obter/atualizar/definir os recursos de ponto de extremidade ou de perfil. Para simplificar essas operações comuns, eles também têm suporte por meio de cmdlets dedicados.

#### Exemplo 1: Habilitando e desabilitando um perfil do Gerenciador de Tráfego
Para habilitar um perfil do Gerenciador de Tráfego, use Enable-AzureRmTrafficManagerProfile. O perfil pode ser especificado usando um objeto de perfil (passado por meio do pipeline ou usando o parâmetro '-TrafficManagerProfile') ou especificando o nome do perfil e o nome do grupo de recursos diretamente, como neste exemplo.

	PS C:\> Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

Da mesma forma, para desabilitar um perfil do Gerenciador de Tráfego:

	PS C:\> Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

O cmdlet Disable-AzureRmTrafficManagerProfile solicitará confirmação, e isso poderá ser suprimido usando o parâmetro '-Force'.

#### Exemplo 2: Habilitando e desabilitando um ponto de extremidade do Gerenciador de Tráfego
Para habilitar um ponto de extremidade do Gerenciador de Tráfego, use Enable-AzureRmTrafficManagerEndpoint. O ponto de extremidade pode ser especificado usando um objeto TrafficManagerEndpoint (passado por meio do pipeline ou usando o parâmetro '-TrafficManagerEndpoint'), ou usando o nome do ponto de extremidade, o tipo de ponto de extremidade, o nome do perfil e o nome do grupo de recursos:

	PS C:\> Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG

Da mesma forma, para desabilitar um ponto de extremidade do Gerenciador de Tráfego:

 	PS C:\> Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force

Assim como Disable-AzureRmTrafficManagerProfile, o cmdlet Disable-AzureRmTrafficManagerEndpoint inclui uma solicitação de confirmação que poderá ser suprimida com o parâmetro '-Force'.

## Excluir um Ponto de Extremidade do Gerenciador de Tráfego
Uma maneira de excluir um ponto de extremidade do Gerenciador de Tráfego é recuperar o objeto de perfil (usando Get-AzureRmTrafficManagerProfile), atualizar a lista de pontos de extremidade no objeto de perfil local e confirmar suas alterações (usando Set-AzureRmTrafficManagerProfile). Esse método permite que várias alterações de ponto de extremidade sejam confirmadas em conjunto.

Outra maneira de remover pontos de extremidade individuais é usar o cmdlet Remove-AzureRmTrafficManagerEndpoint:

	PS C:\> Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
	
Esse cmdlet solicitará confirmação, a menos que o parâmetro '-Force' seja usado para suprimir a solicitação.

## Excluir um perfil do Gerenciador de Tráfego
Para excluir um perfil do Gerenciador de Tráfego, use o cmdlet Remove-AzureRmTrafficManagerProfile, especificando o nome do perfil e o nome do grupo de recursos:

	PS C:\> Remove-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG [-Force]

Esse cmdlet solicitará uma confirmação. A opção '-Force' pode ser usada para suprimir essa solicitação. O perfil a ser excluído também pode ser especificado usando um objeto de perfil:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG
	PS C:\> Remove-AzureTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

Essa sequência também pode ser transferida:

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG | Remove-AzureTrafficManagerProfile [-Force]

## Próximas etapas

[Monitoramento do Gerenciador de Tráfego](traffic-manager-monitoring.md)

[Considerações sobre desempenho do Gerenciador de Tráfego](traffic-manager-performance-considerations.md)
 

<!---HONumber=AcomDC_0204_2016-->