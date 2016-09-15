<properties
   pageTitle="Criar uma regra com base no caminho para um application gateway usando o portal | Microsoft Azure"
   description="Saiba como criar uma regra com base no caminho para um application gateway usando o portal"
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
   ms.date="08/18/2016"
   ms.author="gwallace" />

# Criar uma regra baseada em caminho para um application gateway usando o portal

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-url-route-portal.md)
- [PowerShell do Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)

O Roteamento com base em caminho de URL permite que você associe rotas com base no caminho de URL da solicitação Http. Ele verifica se há uma rota para um pool de back-end configurado para as listas de URLs no Application Gateway, e envia o tráfego de rede para o pool de back-end definido. Um uso comum para o roteamento com base em URL é balancear a carga das solicitações para tipos de conteúdo diferentes para pools de servidores back-end diferentes.

O roteamento com base em URL apresenta um novo tipo de regra ao application gateway. O application gateway tem dois tipos de regra: básica e com base no caminho. O tipo de regra básica fornece o serviço de round robin para os pools de back-end, enquanto as regras com base no caminho também leva em consideração, além da distribuição round robin, o padrão de caminho da URL da solicitação ao escolher o pool de back-end.



## Cenário

O cenário a seguir passa pela criação de uma regra com base no caminho em um application gateway existente. O cenário pressupõe que você já seguiu as etapas para [Criar um Application Gateway](application-gateway-create-gateway-portal.md).

![roteamento de url][scenario]

## <a name="createrule"></a>Criar a regra com base no caminho

Uma regra com base no caminho exige seu próprio ouvinte. Antes da criação da regra, não se esqueça de verificar se você tem um ouvinte disponível para uso.

### Etapa 1

Navegue até http://portal.azure.com e selecione um Application Gateway existente. Clique em **Regras**

![Visão geral do Application Gateway][1]

### Etapa 2

Clique no botão **Com base no caminho** para adicionar uma nova regra com base em caminho.

### Etapa 3

A folha **Adicionar regra com base no caminho** tem duas seções. A primeira seção é onde você definiu o ouvinte, o nome da regra e as configurações de caminho padrão. As configurações do caminho padrão são para rotas que não se ajustam à rota com base no caminho personalizada. A segunda seção da folha **Adicionar regra com base no caminho** é onde você define as regras com base no caminho em si.

**Configurações Básicas**

- **Nome**: um nome amigável para a regra que está acessível no portal.
- **Ouvinte**: ouvinte usado para a regra.
- **Pool de back-end padrão**: essa configuração é a que define o back-end a ser usado para a regra padrão
- **Configurações HTTP padrão**: essa configuração é a que define as configurações HTTP a serem usadas para a regra padrão.

**Regras com base no caminho**

- **Nome**: um nome amigável para a regra com base no caminho.
- **Caminhos**: essa configuração define o caminho que a regra vai procurar ao encaminhar o tráfego
- **Pool de back-end**: essa configuração é a que define o back-end a ser usado para a regra padrão
- **Configuração HTTP**: essa configuração é a que define as configurações HTTP a serem usadas para a regra.

>[AZURE.IMPORTANT] Caminhos: a lista de padrões de caminho para correspondência. Cada um deve começar com /, e o único lugar no qual um "\*" é permitido, é no final. Exemplos válidos são /xyz, /xyz* ou /xyz/*.

![Folha Adicionar regra com base no caminho com informações preenchidas][2]

Adicionar uma regra com base no caminho a um application gateway existente é um processo fácil por meio do portal. Quando uma regra com base no caminho tiver sido criada, ela poderá ser editada para adicionar mais regras facilmente.

![adicionando mais regras com base no caminho][3]

## Próximas etapas

Para saber como configurar o descarregamento SSL com o Azure Application Gateway, consulte [Configurar descarregamento SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png

<!---HONumber=AcomDC_0824_2016-->