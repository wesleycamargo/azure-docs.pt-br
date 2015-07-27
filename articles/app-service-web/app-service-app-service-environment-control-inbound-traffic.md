<properties 
	pageTitle="Como controlar o tráfego de entrada para um ambiente de serviço de aplicativo" 
	description="Saiba mais sobre como configurar regras de segurança de rede para controlar o tráfego de entrada para um ambiente de serviço de aplicativo." 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="stefsh"/>

# Como controlar o tráfego de entrada para um ambiente de serviço de aplicativo

## Visão geral ##
Um ambiente de serviço de aplicativo sempre é criado em uma sub-rede de uma [rede virtual][virtualnetwork] regional. Uma nova rede virtual regional e nova sub-rede podem ser definidas no momento em que um ambiente de serviço de aplicativo é criado. Como alternativa, um ambiente de serviço de aplicativo pode ser criado em uma rede virtual regional já existente e sub-rede preexistente. Para obter mais detalhes sobre a criação de um ambiente de serviço de aplicativo, consulte [Como criar um ambiente de serviço de aplicativo][HowToCreateAnAppServiceEnvironment].

Um ambiente de serviço de aplicativo sempre deve ser criado em uma sub-rede, porque uma sub-rede fornece um limite de rede que pode ser usado para bloquear o tráfego de entrada por trás de dispositivos e serviços de upstream, de modo que o tráfego HTTP e HTTPS é aceito apenas de endereços IP upstream específicos.

O tráfego de rede de entrada e saída em uma sub-rede é controlado usando um [grupo de segurança de rede][NetworkSecurityGroups]. Controlar o tráfego de entrada requer a criação de regras de segurança de rede em um grupo de segurança de rede, seguida da atribuição do grupo de segurança de rede à sub-rede que contém o ambiente de serviço de aplicativo.

Quando um grupo de segurança de rede é atribuído a uma sub-rede, o tráfego de entrada para aplicativos no ambiente de serviço de aplicativo é permitido/bloqueado com base em regras de permissão e bloqueio definidas no grupo de segurança de rede.

## Portas de rede usadas em um Ambiente de Serviço de Aplicativo ##
Antes de bloquear o tráfego de rede de entrada com um grupo de segurança de rede, é importante saber o conjunto de portas de rede requeridas e opcionais usadas por um ambiente de serviço de aplicativo. Fechar acidentalmente o tráfego para algumas portas pode resultar em perda de funcionalidade em um ambiente de serviço de aplicativo.

A seguir está uma lista de portas usadas por um ambiente de serviço de aplicativo:

- 454: **Porta requerida** usada pela infraestrutura do Azure para gerenciamento e manutenção de ambientes de serviço de aplicativo. Não bloqueie o tráfego para essa porta.
- 455: **Porta requerida** usada pela infraestrutura do Azure para gerenciamento e manutenção de ambientes de serviço de aplicativo. Não bloqueie o tráfego para essa porta.
- 80: Porta padrão para tráfego HTTP de entrada para aplicativos executados em planos de serviço de aplicativo em um ambiente de serviço de aplicativo
- 443: Porta padrão para tráfego SSL de entrada para aplicativos executados em planos de serviço de aplicativo em um ambiente de serviço de aplicativo
- 21: canal de controle para FTP. Essa porta pode ser bloqueada com segurança se o FTP não está sendo usado.
- 10001-10020: canais de dados para FTP. Assim como ocorre com o canal de controle, essas portas podem ser bloqueadas com segurança se o FTP não está sendo usado (**Observação:** os canais de dados de FTP podem ser alterados durante a visualização.)
- 4016: usado para depuração remota com o Visual Studio 2012. Essa porta pode ser bloqueada com segurança se o recurso não está sendo usado.
- 4018: usado para depuração remota com o Visual Studio 2013. Essa porta pode ser bloqueada com segurança se o recurso não está sendo usado.
- 4020: usado para depuração remota com o Visual Studio 2015. Essa porta pode ser bloqueada com segurança se o recurso não está sendo usado.

## Requisitos de DNS e conectividade de saída ##
Observe que, para um ambiente de Serviço de Aplicativo funcionar corretamente, ele também requer acesso de saída ao armazenamento do Azure, bem como ao banco de dados SQL na mesma região do Azure. Se o acesso de saída da Internet for bloqueado na rede virtual, os ambientes do Serviço de Aplicativo não poderão acessar esses pontos de extremidade do Azure.

O cliente também pode ter servidores DNS personalizados configurados na rede virtual. Os ambientes do Serviço de Aplicativo precisam ser capazes de resolver pontos de extremidade do Azure em *.database.windows.net, *.file.core.windows.net e *.blob.core.windows.net. 

Também é recomendável que todos os servidores DNS personalizados na rede virtual sejam configurados com antecedência antes da criação de um ambiente do Serviço de Aplicativo. Se a configuração de DNS da rede virtual for alterada durante a criação de um ambiente do Serviço de Aplicativo, isso resultará em falha no processo de criação do ambiente do Serviço de Aplicativo.

## Criando um grupo de segurança de rede ##
Para obter detalhes completos sobre como funcionam os grupos de segurança de rede, consulte as seguintes [informações][NetworkSecurityGroups]. Os detalhes abaixo tocam em pontos de destaque dos grupos de segurança de rede, com foco em como configurar e aplicar um grupo de segurança de rede a uma sub-rede que contém um ambiente de serviço de aplicativo.

Grupos de segurança de rede são criados pela primeira vez como uma entidade autônoma associada a uma assinatura. Como os grupos de segurança de rede são criados em uma região do Azure, certifique-se de que o grupo de segurança de rede é criado na mesma região que o ambiente de serviço de aplicativo.

A seguir, demonstra-se como criar um grupo de segurança de rede:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Depois de um grupo de segurança de rede ter sido criado, uma ou mais regras de segurança de rede são adicionadas a ele. Já que o conjunto de regras pode ser alterado ao longo do tempo, recomenda-se espaçar o esquema de numeração usado para as prioridades de regra, para tornar mais fácil inserir regras adicionais ao longo do tempo.

O exemplo a seguir mostra uma regra que concede explicitamente o acesso às portas de gerenciamento requeridas pela infraestrutura do Azure para gerenciar e manter um ambiente de serviço de aplicativo. Observe que todo o tráfego de gerenciamento passa por SSL e é protegido por certificados de cliente, portanto, mesmo que as portas estejam abertas elas são inacessíveis por qualquer entidade que não seja a infraestrutura de gerenciamento do Azure.


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    

Ao bloquear o acesso às portas 80 e 443 para “ocultar” um ambiente de serviço de aplicativo por trás de serviços ou dispositivos upstream, você precisará saber o endereço IP upstream. Por exemplo, se você estiver usando um firewall de aplicativo Web (WAF), o WAF terá seu próprio endereço (ou endereços) IP, que ele utilizará ao usar proxy de tráfego para um ambiente de serviço de aplicativo downstream. Você precisará usar esse endereço IP no parâmetro *SourceAddressPrefix* de uma regra de segurança de rede.

No exemplo abaixo, o tráfego de entrada de um determinado endereço IP upstream é explicitamente permitido. O endereço *1.2.3.4* é usado como um espaço reservado para o endereço IP de um WAF upstream. Altere o valor para coincidir com o endereço usado pelo serviço ou dispositivo upstream.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
Se desejar suporte a FTP, as regras a seguir podem usadas como um modelo para conceder acesso à porta de controle de FTP e às portas de canal de dados. Como o FTP é um protocolo com monitoramento de estado, você não poderá rotear o tráfego FTP por meio de um dispositivo de proxy ou firewall HTTP/HTTPS tradicional. Nesse caso, será necessário definir o *SourceAddressPrefix* para um valor diferente - por exemplo, o intervalo de endereços IP de computadores de desenvolvedor ou de implantação, nos quais clientes FTP estão em execução.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Observação:** o intervalo de portas de canal de dados pode ser alterado durante o período de visualização.)

Se a depuração remota com o Visual Studio é usada, as regras a seguir demonstram como conceder acesso. Há uma regra separada para cada versão do Visual Studio para a qual há suporte, já que cada versão usa uma porta diferente para a depuração remota. Assim como acontece com acesso ao FTP, o tráfego de depuração remota pode não fluir corretamente por meio de um dispositivo de proxy ou WAF tradicional. O *SourceAddressPrefix* pode ser definido, em vez disso, como o intervalo de endereços IP dos computadores de desenvolvedor executando o Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## Atribuir um grupo de segurança de rede a uma sub-rede ##
Um grupo de segurança de rede tem uma regra de segurança padrão que nega o acesso a todo o tráfego externo. O resultado da combinação entre as regras de segurança de rede descritas acima e a regra de segurança padrão bloqueando o tráfego de entrada é que somente o tráfego de intervalos de endereços de origem associado a uma ação *Permitir* poderá enviar tráfego a aplicativos em execução em um ambiente de serviço de aplicativo.

Depois que um grupo de segurança de rede é preenchido com regras de segurança, ele precisa ser atribuído à sub-rede que contém o ambiente de serviço de aplicativo. O comando de atribuição referencia tanto o nome da rede virtual onde reside o ambiente de serviço de aplicativo como o nome da sub-rede onde o ambiente de serviço de aplicativo foi criado.

O exemplo a seguir mostra um grupo de segurança de rede que está sendo atribuído a uma sub-rede e rede virtual:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Depois que a atribuição de grupo de segurança de rede for bem-sucedida (a atribuição é uma operação demorada e pode levar alguns minutos para ser concluída), somente tráfego de entrada correspondendo às regras *Permitir* alcançará com êxito os aplicativos no ambiente de serviço de aplicativo.

Para fins de exatidão, o exemplo a seguir mostra como remover e, portanto, dissociar o grupo de segurança de rede da sub-rede:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## Considerações especiais para IP-SSL explícito ##
Se um aplicativo for configurado com um endereço IP explícito em vez do endereço IP padrão do ambiente de serviço de aplicativo, o tráfego HTTP e HTTPS flui para a sub-rede em um conjunto de portas diferente das portas 80 e 443.

Durante a visualização inicial de ambientes de serviço de aplicativo, não é possível determinar as portas usadas por IP-SSL. No entanto, quando essas informações são expostas por meio do portal, de ferramentas de linha de comando e APIs REST, os desenvolvedores poderão configurar grupos de segurança de rede para controlar o tráfego também por essas portas.

## Introdução

Para se familiarizar com os ambientes de serviço de aplicativo, consulte [Introdução ao ambiente do serviço de aplicativo][IntroToAppServiceEnvironment]

Para obter detalhes sobre conexão segura de aplicativos a recursos de back-end de um ambiente de serviço de aplicativo, consulte [Conexão segura a recursos de back-end de um ambiente de serviço de aplicativo][SecurelyConnecttoBackend]

Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[SecurelyConnecttoBackend]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/

<!-- IMAGES -->

<!---HONumber=July15_HO3-->