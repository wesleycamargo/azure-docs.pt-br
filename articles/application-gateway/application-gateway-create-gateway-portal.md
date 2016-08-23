<properties
   pageTitle="Criar um Application Gateway usando o portal | Microsoft Azure"
   description="Saiba como criar um Application Gateway usando o portal"
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
   ms.date="08/09/2016"
   ms.author="gwallace" />

# Criar um Application Gateway usando o portal

O Azure Application Gateway é um balanceador de carga de camada 7. Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local. O Application Gateway tem os seguintes recursos de entrega de aplicativo: balanceamento de carga HTTP, afinidade de sessão baseada em cookie e descarregamento SSL (protocolo SSL), investigações de integridade personalizadas e suporte para vários sites.

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-gateway-portal.md)
- [PowerShell do Azure Resource Manager](application-gateway-create-gateway-arm.md)
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Modelo do Azure Resource Manager ](application-gateway-create-gateway-arm-template.md)


<BR>



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

O Azure Application Gateway requer sua própria sub-rede. Ao criar uma rede virtual, certifique-se de deixar espaço de endereço suficiente para ter várias sub-redes. Depois de implantar um Application Gateway a uma sub-rede, apenas Application Gateways adicionais poderão ser adicionados à sub-rede.

## Criar o Application Gateway

### Etapa 1

Navegue até o portal do Azure, clique em **Novo** > **Rede** > **Application Gateway**

![Criando um novo Application Gateway][1]

### Etapa 2

Em seguida, preencha as informações básicas sobre o Application Gateway. Ao concluir, clique em **OK**

As informações necessárias para as configurações básicas são:

- **Name** - o nome para o Application Gateway.
- **Tamanho do SKU** - esse é o tamanho do Application Gateway, as opções disponíveis são (pequeno, médio e grande).
- **Contagem de instâncias** - o número de instâncias; esse valor deve ser um número entre 2 e 10.
- **Grupo de recursos** - o grupo de recursos para manter o application gateway; ele pode ser um grupo de recursos existente ou um novo.
- **Localização** - a região do Application Gateway; esse é a mesma localização no grupo de recursos. *Isso é importante porque a rede virtual e o IP público devem estar na mesma localização que o gateway*.

![folha mostrando configurações básicas][2]

>[AZURE.NOTE] É possível escolher uma contagem de instâncias de 1 para fins de teste. É importante saber que qualquer contagem de instâncias em duas instâncias não é coberta por um SLA e, portanto, não são recomendadas. Gateways pequenos devem ser usados para teste de desenvolvimento e não para fins de produção.


### Etapa 3

Quando as configurações básicas forem definidas, a próxima etapa será definir a rede virtual a ser usada. A rede virtual conterá o aplicativo para qual o Application Gateway faz balanceamento de carga.

Clique em **Escolher uma rede virtual** para configurar a rede virtual.

![folha mostrando configurações básicas do Application Gateway][3]

### Etapa 4

Na folha *Escolher Rede Virtual*, clique em **Criar Novo**

*Enquanto não é explicada neste cenário, uma Rede Virtual existente pode ser selecionada neste momento*

![escolher folha da rede virtual][4]

### Etapa 5

Preencha as informações de rede na folha **Criar Rede Virtual** conforme descrito anteriormente na descrição de [Cenário](#scenario).

![Criar folha de rede virtual com as informações inseridas][5]

### Etapa 6

Depois de criar a rede virtual, a próxima etapa será definir o IP de front-end para o Application Gateway. Neste ponto, a escolha é entre um endereço IP público ou privado para o front-end. A escolha depende se o aplicativo estiver voltado para a Internet ou somente para fins internos. Este cenário pressupõe o uso de um endereço IP público. Para escolher um endereço IP privado, o botão **Particular** pode ser clicado. Um endereço IP atribuído automaticamente é escolhido e você pode clicar na caixa de seleção **Escolher um endereço IP privado específico** para inserir um manualmente.

### Etapa 7

Clique em **Escolher um endereço IP público**. Se houver um endereço IP público existente disponível, ele poderá ser escolhido nesse momento; nesse cenário, você criará um novo endereço IP público. Clique em **Criar Novo**

![Escolher uma folha de endereço IP público][6]

### Etapa 8

Em seguida, dê ao endereço IP público um nome amigável e clique em **OK**

![Criar uma folha de endereço IP público][7]

### Etapa 9

A última configuração para definir ao criar um Application Gateway é a configuração de ouvinte. Se **http** for usado, nada mais precisará ser configurado e você poderá clicar em **OK**. Para utilizar **https**, será necessária configuração adicional.

Um certificado é necessário para usar **https**. A chave privada do certificado é necessária, portanto uma exportação .pfx do certificado é necessária para ser fornecida, além da senha do arquivo.


![seção de Configuração de ouvinte em branco na folha Configurações][8]

### Etapa 10

Clique em **HTTPS**, clique no ícone de **pasta** ao lado da caixa de texto **Carregar certificado PFX**. Navegue até o arquivo de certificado .pfx no sistema de arquivos. Quando ele for selecionado, dê um nome amigável de certificado e digite a senha para o arquivo. pfx.

Clique uma vez concluído **OK** para examinar as configurações do Application Gateway.

![Seção de Configuração de ouvinte na folha Configurações][9]

### Etapa 11

Examine a página Resumo e clique em **OK**. Agora o Application Gateway está na fila e será criado.

### Etapa 12

Quando o Application Gateway tiver sido criado, navegue até ele no portal para continuar a configuração do Application Gateway.

![Modo de exibição de recursos do Application Gateway][10]

Isso cria um Application Gateway básico com configurações padrão para o ouvinte, pool de back-end, configurações de http de back-end e regras. Você pode modificar essas configurações de acordo com sua implantação quando o provisionamento for bem-sucedido

## Próximas etapas

Saiba como criar investigações de integridade personalizados visitando [Criar uma investigação de integridade personalizada](application-gateway-create-probe-portal.md)

Saiba como configurar o Descarregamento de SSL e levar a descriptografia SSL cara longe dos seus servidores Web visitando [Configurar Descarregamento de SSL](application-gateway-ssl-arm.md)

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[6]: ./media/application-gateway-create-gateway-portal/figure6.png
[7]: ./media/application-gateway-create-gateway-portal/figure7.png
[8]: ./media/application-gateway-create-gateway-portal/figure8.png
[9]: ./media/application-gateway-create-gateway-portal/figure9.png
[10]: ./media/application-gateway-create-gateway-portal/figure10.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png

<!---HONumber=AcomDC_0810_2016-->