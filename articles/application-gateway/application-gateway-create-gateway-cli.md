<properties
   pageTitle="Criar um gateway de aplicativo usando a CLI do Azure no Resource Manager | Microsoft Azure"
   description="Saiba como criar um Gateway de Aplicativo usando a CLI do Azure no Resource Manager"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2016"
   ms.author="gwallace" />

# Criar um gateway de aplicativo usando a CLI do Azure

O Azure Application Gateway é um balanceador de carga de camada 7. Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local. O Application Gateway tem os seguintes recursos de entrega de aplicativo: balanceamento de carga HTTP, afinidade de sessão baseada em cookie e descarregamento SSL (protocolo SSL), investigações de integridade personalizadas e suporte para vários sites.

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-gateway-portal.md)
- [PowerShell do Azure Resource Manager](application-gateway-create-gateway-arm.md)
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Modelo do Azure Resource Manager ](application-gateway-create-gateway-arm-template.md)
- [CLI do Azure](application-gateway-create-gateway-cli.md)

## Pré-requisito: instalar a CLI do Azure

Para executar as etapas deste artigo, será necessário [instalar a Interface de Linha de Comando do Azure para Mac, Linux e Windows (CLI do Azure)](../xplat-cli-install.md) e você precisará [fazer logon no Azure](../xplat-cli-connect.md).

> [AZURE.NOTE] Se você não tiver uma conta do Azure, crie uma. Inscreva-se em uma [avaliação gratuita aqui](../active-directory/sign-up-organization.md).

## Cenário

Nesse cenário, você aprenderá como criar um Application Gateway usando o portal do Azure.

Este cenário:

- Criará um Application Gateway com duas instâncias.
- Criará uma rede virtual chamada AdatumAppGatewayVNET, com um bloco CIDR 10.0.0.0/16 reservado.
- Criará uma sub-rede chamada Appgatewaysubnet que usa 10.0.0.0/28 como seu bloco CIDR.
- Configurará um certificado para descarregamento SSL.

![Cenário de exemplo][scenario]

>[AZURE.NOTE] A configuração adicional do Application Gateway, incluindo investigações de integridade personalizadas, endereços de pool de back-end e regras adicionais são configuradas após o Application Gateway ser configurado e não durante a implantação inicial.

## Antes de começar

O Azure Application Gateway requer sua própria sub-rede. Ao criar uma rede virtual, certifique-se de deixar espaço de endereço suficiente para ter várias sub-redes. Depois de implantar um gateway de aplicativo a uma sub-rede, apenas gateway de aplicativos adicionais poderão ser adicionados à sub-rede.

## Fazer logon no Azure

Abra o **Prompt de comando do Microsoft Azure** e faça logon.

    azure login

Depois de digitar o exemplo anterior, um código será fornecido. Navegue até https://aka.ms/devicelogin em um navegador para continuar o processo de logon.

![cmd mostrando logon de dispositivo][1]

No navegador, digite o código recebido. Você será redirecionado para uma página de entrada.

![navegador para inserir código][2]

Depois que o código foi inserido, você estará conectado. Feche o navegador para continuar com o cenário.

![conectado com êxito][3]

## Alternar para o modo do Resource Manager

    azure config mode arm

## Criar o grupo de recursos

Antes de criar o gateway de aplicativo, um grupo de recursos é criado para conter o gateway de aplicativo. O código a seguir mostra o comando.

    azure group create -n AdatumAppGatewayRG -l eastus

## Criar uma rede virtual

Após a criação do grupo de recursos, uma rede virtual é criada para o gateway de aplicativo. No exemplo a seguir, o espaço de endereço foi 10.0.0.0/16, conforme definido nas anotações do cenário anterior.

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

## Criar uma sub-rede

Após a criação da rede virtual, uma sub-rede é adicionada ao gateway de aplicativo. Se você planeja usar o gateway de aplicativo com um aplicativo Web hospedado na mesma rede virtual do que o gateway de aplicativo, não se esqueça de deixar espaço suficiente para outra sub-rede.

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

## Criar o gateway de aplicativo

Depois que a rede virtual e a sub-rede forem criadas, os pré-requisitos para o gateway de aplicativo estarão completos. Além disso, um certificado .pfx exportado anteriormente e a senha do certificado são necessários para a etapa seguinte: os endereços IP usados para o back-end são os endereços IP para seu servidor de back-end. Esses valores podem ser IPs privados na rede virtual, ips públicos ou nomes de domínio totalmente qualificados para seus servidores de back-end.

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd -z 2 -a Standard_Medium -w Basic -j 443 -f Enabled -o 80 -i http -b https -u Standard



Este exemplo cria um Application Gateway básico com configurações padrão para o ouvinte, pool de back-end, configurações de http de back-end e regras. Ele também configura o descarregamento de SSL. Você pode modificar essas configurações de acordo com sua implantação quando o provisionamento for bem-sucedido. Se você já tiver o seu aplicativo Web definido com o pool de back-end nas etapas anteriores, o balanceamento de carga começará depois que ele for criado.

## Próximas etapas

Saiba como criar investigações de integridade personalizados visitando [Criar uma investigação de integridade personalizada](application-gateway-create-probe-portal.md)

Saiba como configurar o Descarregamento de SSL e levar a descriptografia SSL cara longe dos seus servidores Web visitando [Configurar Descarregamento de SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png

<!---HONumber=AcomDC_0921_2016-->