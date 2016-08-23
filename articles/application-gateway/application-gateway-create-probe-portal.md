<properties
   pageTitle="Criar uma investigação personalizada para um Application Gateway usando o portal | Microsoft Azure"
   description="Saiba como criar uma investigação personalizada para o Application Gateway usando o portal"
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

# Criar uma investigação personalizada para o Application Gateway usando o portal

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-probe-portal.md)
- [PowerShell do Azure Resource Manager](application-gateway-create-probe-ps.md)
- [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

<BR>

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## Cenário

O cenário a seguir passa pela criação de uma investigação de integridade personalizada em um Application Gateway existente. O cenário pressupõe que você já seguiu as etapas para [Criar um Application Gateway](application-gateway-create-gateway-portal.md).

## <a name="createprobe"></a>Criar a investigação

As investigações são configuradas em um processo de duas etapas pelo portal. A primeira etapa é criar a investigação, em seguida, você adiciona a investigação às configurações de http do back-end do Application Gateway.

### Etapa 1

Navegue até http://portal.azure.com e selecione um Application Gateway existente.

![Visão geral do Application Gateway][1]

### Etapa 2

Clique em **Investigações** e clique no botão **Adicionar** para adicionar uma nova investigação.

![Folha Adicionar Investigação com as informações preenchidas][2]

### Etapa 3

Preencha as informações necessárias para a investigação e ao concluir, clique em **OK**.

- **Nome**: este é um nome amigável para a investigação que está acessível no portal.
- **Host**: este é o nome de host usado para a investigação.
- **Caminho**: o restante da URL completa para a investigação personalizada.
- **Intervalo (segundos)**: frequência com que a investigação é executada para verificar a integridade.
- **Tempo limite (segundos)**: a quantidade de tempo que a investigação espera antes de atingir o tempo limite.
- **Limite não íntegro**: número de tentativas com falha para ser considerado não íntegro.

> [AZURE.IMPORTANT] o nome do host não é o nome do servidor. Esse é o nome do host virtual em execução no servidor de aplicativos. A investigação é enviada para http://(host name):(port from httpsetting)/urlPath

![definições de configuração da investigação][3]

## Adicionar a investigação ao gateway

Agora que a investigação foi criada, é hora de adicioná-la ao gateway. As configurações da investigação são definidas nas configurações de http do back-end do Application Gateway.

### Etapa 1

Clique em **Configurações de HTTP** do Application Gateway e clique nas configurações de http do back-end atual na janela para abrir a folha de configuração.

![janela de configurações de https][4]

### Etapa 2

Na folha de configuração **appGatewayBackEndHttp**, clique em **Usar sonda personalizada** e selecione a investigação usada na seção [Criar a investigação](#createprobe). Ao concluir, clique em **OK** e as configurações são aplicadas.

![folha de configurações de appgatewaybackend][5]

A investigação de padrão verifica o acesso padrão ao aplicativo Web. Agora que uma investigação personalizada foi criada, o Application Gateway usa o caminho personalizado definido para monitorar a integridade do back-end selecionado. Com base nos critérios definidos, o Application Gateway verifica o arquivo especificado na investigação. Se a chamada para host:Porta/caminho não retornar uma resposta de status HTTP 200 OK, o servidor será retirado da rotação após o limite não íntegro ser atingido. A investigação continua na instância não íntegra para determinar quando ela se torna íntegra novamente. Após a instância ser adicionada novamente ao pool de servidores íntegros, o tráfego começa a fluir para ela novamente e a investigação da instância continua no intervalo especificado pelo usuário como normalmente.


## Próximas etapas

Para saber como configurar o descarregamento SSL com o Azure Application Gateway, consulte [Configurar descarregamento SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png

<!---HONumber=AcomDC_0810_2016-->