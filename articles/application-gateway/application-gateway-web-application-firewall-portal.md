<properties
   pageTitle="Criar um gateway de aplicativo com o firewall do aplicativo Web usando o portal | Microsoft Azure"
   description="Saiba como criar um Gateway de Aplicativo com o firewall do aplicativo Web usando o portal"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="gwallace" />


# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>Criar um gateway de aplicativo com o firewall do aplicativo Web usando o portal

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-web-application-firewall-portal.md)
- [PowerShell do Azure Resource Manager](application-gateway-web-application-firewall-powershell.md)

O Azure Application Gateway é um balanceador de carga de camada 7. Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local.
O aplicativo fornece muitos recursos do Controlador de Entrega de Aplicativos (ADC), incluindo o balanceamento de carga de HTTP, a afinidade de sessão baseada em cookies, o descarregamento de Secure Sockets Layer (SSL), as sondas de integridade personalizadas, suporte para vários sites e muitos outros.
Para localizar uma lista completa dos recursos com suporte, visite [Visão geral do Gateway de Aplicativo](application-gateway-introduction.md)

O firewall de aplicativo Web (WAF) no Gateway de Aplicativo do Azure protege os aplicativos Web contra ataques comuns baseados na Web, como injeção de SQL, ataques de scripts entre sites e sequestros de sessão.

## <a name="scenarios"></a>Cenários

Neste artigo, há dois cenários:

No primeiro cenário, você aprende a [adicionar o firewall do aplicativo Web a um gateway de aplicativo existente](#add-web-application-firewall-to-an-existing-application-gateway).

No segundo cenário, você aprenderá a [criar um gateway de aplicativo com o firewall do aplicativo Web](#create-an-application-gateway-with-web-application-firewall)

![Cenário de exemplo][scenario]

>[AZURE.NOTE] A configuração adicional do Application Gateway, incluindo investigações de integridade personalizadas, endereços de pool de back-end e regras adicionais são configuradas após o Application Gateway ser configurado e não durante a implantação inicial.

## <a name="before-you-begin"></a>Antes de começar

O Azure Application Gateway requer sua própria sub-rede. Ao criar uma rede virtual, certifique-se de deixar espaço de endereço suficiente para ter várias sub-redes. Depois de implantar um gateway de aplicativo a uma sub-rede, apenas gateway de aplicativos adicionais poderão ser adicionados à sub-rede.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>adicionar o firewall do aplicativo Web a um gateway de aplicativo existente

Este cenário atualiza um gateway de aplicativo existente para dar suporte ao firewall do aplicativo Web no modo de prevenção.

### <a name="step-1"></a>Etapa 1

Navegue até o portal do Azure, selecione um Gateway de Aplicativo existente.

![Criação de um gateway de aplicativo][1]

### <a name="step-2"></a>Etapa 2

Clique em **Configuração** e atualize as configurações do gateway de aplicativo. Quando concluir, clique em **Salvar**

As configurações para atualizar um gateway de aplicativo existente para dar suporte ao firewall do aplicativo da Web são:

- **Camada** - a camada selecionada deve ser **WAF** para dar suporte ao firewall do aplicativo Web
- **Tamanho do SKU** - essa configuração é o tamanho do gateway de aplicativo com o firewall de aplicativo Web, as opções disponíveis são (**Médio** e **Grande**).
- **Firewall status** - essa configuração também desabilita ou habilita o firewall do aplicativo Web.
- **Modo firewall** - essa configuração é como o firewall do aplicativo Web lida com o tráfego mal-intencionado. O modo **Detecção** só registra em log os eventos, onde o modo **Prevenção** registra os eventos e interrompe o tráfego mal-intencionado.

![folha mostrando configurações básicas][2]

>[AZURE.NOTE] Para exibir logs de firewall do aplicativo Web, o diagnóstico deve ser habilitado e ApplicationGatewayFirewallLog selecionado. É possível escolher uma contagem de instâncias de 1 para fins de teste. É importante saber que qualquer contagem de instâncias em duas instâncias não é coberta por um SLA e, portanto, não são recomendadas. Os gateways pequenos não estão disponíveis ao usar o firewall do aplicativo Web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>criar um gateway de aplicativo com o firewall do aplicativo Web

Este cenário:

- Crie um gateway de aplicativo de firewall do aplicativo Web médio com duas instâncias.
- Criará uma rede virtual chamada AdatumAppGatewayVNET, com um bloco CIDR 10.0.0.0/16 reservado.
- Criará uma sub-rede chamada Appgatewaysubnet que usa 10.0.0.0/28 como seu bloco CIDR.
- Configurará um certificado para descarregamento SSL.

### <a name="step-1"></a>Etapa 1

Navegue até o Portal do Azure, clique em **Novo** > **Rede** > **Gateway de Aplicativo**

![Criação de um gateway de aplicativo][1-1]

### <a name="step-2"></a>Etapa 2

Em seguida, preencha as informações básicas sobre o Application Gateway. Escolha **WAF** como a camada. Ao concluir, clique em **OK**

As informações necessárias para as configurações básicas são:

- **Name** - o nome para o Application Gateway.
- **Camada** - a camada do gateway de aplicativo, as opções disponíveis são (**Standard** e **WAF**). O firewall do aplicativo Web só está disponível na camada WAF.
- **Tamanho do SKU** - essa configuração é o tamanho do gateway de aplicativo, as opções disponíveis são (**Médio** e **Grande**).
- **Contagem de instâncias** - o número de instâncias; esse valor deve ser um número entre **2** e **10**.
- **Grupo de recursos** - o grupo de recursos para manter o gateway de aplicativo; ele pode ser um grupo de recursos existente ou um novo.
- **Localização** - a região do gateway de aplicativo; essa é a mesma localização no grupo de recursos. *O local é importante porque a rede virtual e o IP público devem estar na mesma localização do que o gateway*.

![folha mostrando configurações básicas][2-2]

>[AZURE.NOTE] É possível escolher uma contagem de instâncias de 1 para fins de teste. É importante saber que qualquer contagem de instâncias em duas instâncias não é coberta por um SLA e, portanto, não são recomendadas. Não há suporte para gateways pequenos para cenários de firewall do aplicativo Web.

### <a name="step-3"></a>Etapa 3

Quando as configurações básicas forem definidas, a próxima etapa será definir a rede virtual a ser usada. A rede virtual contém o aplicativo para o qual o gateway de aplicativo faz balanceamento de carga.

Clique em **Escolher uma rede virtual** para configurar a rede virtual.

![folha mostrando configurações básicas do Application Gateway][3]

### <a name="step-4"></a>Etapa 4

Na folha **Escolher Rede Virtual**, clique em **Criar Novo**

Enquanto não é explicada neste cenário, uma Rede Virtual existente pode ser selecionada neste momento.  Se uma rede virtual existente for usada, é importante saber que a rede virtual precisa usar uma sub-rede vazia ou uma sub-rede somente com recursos de gateway de aplicativo.

![escolher folha da rede virtual][4]

### <a name="step-5"></a>Etapa 5

Preencha as informações de rede na folha **Criar Rede Virtual** conforme descrito anteriormente na descrição de [Cenário](#scenario) .

![Criar folha de rede virtual com as informações inseridas][5]

### <a name="step-6"></a>Etapa 6

Depois de criar a rede virtual, a próxima etapa será definir o IP de front-end para o Application Gateway. Neste ponto, a escolha é entre um endereço IP público ou privado para o front-end. A escolha depende se o aplicativo estiver voltado para a Internet ou somente para fins internos. Este cenário pressupõe o uso de um endereço IP público. Para escolher um endereço IP privado, o botão **Particular** pode ser clicado. Um endereço IP atribuído automaticamente é escolhido e você pode clicar na caixa de seleção **Escolher um endereço IP privado específico** para inserir um manualmente.

Clique em **Escolher um endereço IP público**. Se houver um endereço IP público existente disponível, ele poderá ser escolhido nesse momento; nesse cenário, você criará um novo endereço IP público. Clique em **Criar Novo**

![Escolher uma folha de endereço IP público][6]

### <a name="step-7"></a>Etapa 7

Em seguida, dê ao endereço IP público um nome amigável e clique em **OK**

![Criar uma folha de endereço IP público][7]

### <a name="step-8"></a>Etapa 8

Em seguida, você poderá definir a configuração de ouvinte.  Se **http** for usado, nada mais precisará ser configurado e você poderá clicar em **OK**. Para utilizar **https** , será necessária configuração adicional.

Um certificado é necessário para usar **https**. A chave privada do certificado é necessária, portanto uma exportação .pfx do certificado é necessária para ser fornecida, além da senha do arquivo.

Clique em **HTTPS**, clique no ícone de **pasta** ao lado da caixa de texto **Carregar certificado PFX**.
Navegue até o arquivo de certificado .pfx no sistema de arquivos. Quando ele for selecionado, dê um nome amigável de certificado e digite a senha para o arquivo. pfx.

Clique uma vez concluído **OK** para examinar as configurações do Application Gateway.

![Seção de Configuração de ouvinte na folha Configurações][8]

### <a name="step-9"></a>Etapa 9

Definir as configurações específicas do **WAF** .

- **Status do firewall** - essa configuração ativa ou desativa o WAF.
- **Modo de firewall** - essa configuração determina as ações tomadas pelo WAF em relação ao tráfego mal-intencionado. Se **Detecção** for escolhido, o tráfego só será registrado em log.  Se **Prevenção** for escolhido, o tráfego será registrado em log e interrompido com um 403 Não Autorizado.

![configurações de firewall do aplicativo Web][9]

### <a name="step-10"></a>Etapa 10

Examine a página Resumo e clique em **OK**.  Agora o Application Gateway está na fila e será criado.

### <a name="step-12"></a>Etapa 12

Quando o Application Gateway tiver sido criado, navegue até ele no portal para continuar a configuração do Application Gateway.

![Modo de exibição de recursos do Application Gateway][10]

Estas etapas criam um gateway de aplicativo básico com configurações padrão para o ouvinte, pool de back-end, configurações de http de back-end e regras. Você pode modificar essas configurações de acordo com sua implantação quando o provisionamento for bem-sucedido

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar o log de diagnóstico para registrar os eventos detectados ou impedidos pelo Firewall do Aplicativo Web ao visitar o [Diagnóstico do Gateway de Aplicativo](application-gateway-diagnostics.md)

Saiba como criar investigações de integridade personalizados visitando [Criar uma investigação de integridade personalizada](application-gateway-create-probe-portal.md)

Saiba como configurar o Descarregamento de SSL e levar a descriptografia SSL cara longe dos seus servidores Web visitando [Configurar Descarregamento de SSL](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[1-1]: ./media/application-gateway-web-application-firewall-portal/figure1-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[4]: ./media/application-gateway-web-application-firewall-portal/figure4.png
[5]: ./media/application-gateway-web-application-firewall-portal/figure5.png
[6]: ./media/application-gateway-web-application-firewall-portal/figure6.png
[7]: ./media/application-gateway-web-application-firewall-portal/figure7.png
[8]: ./media/application-gateway-web-application-firewall-portal/figure8.png
[9]: ./media/application-gateway-web-application-firewall-portal/figure9.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png


<!--HONumber=Oct16_HO2-->


