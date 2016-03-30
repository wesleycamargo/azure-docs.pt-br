<properties 
   pageTitle="Introdução à criação de um balanceador de carga interno no Resource Manager usando o portal do Azure | Microsoft Azure"
   description="Saiba como criar um balanceador de carga interno no Resource Manager usando o portal do Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/04/2016"
   ms.author="joaoma" />

# Introdução à criação de um balanceador de carga interno no portal do Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]<BR>[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## Introdução à criação de um balanceador de carga interno usando o portal do Azure	

Para criar um balanceador de carga interno no portal do Azure, siga as etapas abaixo.

1. Em um navegador, vá até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. No canto superior esquerdo da tela, clique em **Novo** > **Rede** > **Balanceador de Carga**.
4. Na folha **Criar balanceador de carga**, digite um **Nome** para o balanceador de carga.
5. Em **Esquema**, clique em **Interno**.
5. Clique em **Rede virtual** e selecione a rede virtual na qual você deseja criar o balanceador de carga.

>[AZURE.NOTE] Caso você não veja a rede virtual que deseja usar, verifique o **Local** que você está usando para o balanceador de carga e altere-o adequadamente.

6. Clique em **Sub-rede** e selecione a sub-rede onde você quer criar o balanceador de carga.
6. Em **Atribuição de endereço IP**, clique em **Dinâmico** ou em **Estático**, dependendo se você quer que o endereço IP do balanceador de carga seja fixo (estático) ou não.

>[AZURE.NOTE] Se você optar por usar um endereço IP estático, será necessário fornecer um endereço para o balanceador de carga.
    
7. Em **Grupo de recursos** especifique o nome de um novo grupo de recursos para o balanceador de carga ou clique em **selecionar existente** e selecione um grupo de recursos existente. 
8. Clique em **Criar**. 

## Configuração de regras de balanceamento de carga 

Após a criação do balanceador de carga, navegue até o recurso do balanceador de carga para configurá-lo. Primeiro, você precisa configurar um pool de endereços back-end e uma investigação antes de configurar uma regra de balanceamento de carga.

### Etapa 1

Configurar um pool de back-end:

1. No portal do Azure, clique em **Procurar** > **Balanceadores de carga** e clique no balanceador de carga que você criou acima.
2. Na folha **Configurações**, clique em **Pools de back-end**. 
3. Na folha **Investigações**, clique em **Adicionar**.
4. Na folha **Adicionar pool de back-end**, digite um **Nome** para o pool de back-end e clique em **OK**.

### Etapa 2 

Configurar uma investigação:
 
1. No portal do Azure, clique em **Procurar** > **Balanceadores de carga** e clique no balanceador de carga que você criou acima.
2. Na folha **Configurações**, clique em **Investigações**. 
3. Na folha **Pools de endereços back-end**, clique em **Adicionar**.
4. Na folha **Adicionar investigação**, digite um **Nome** para a investigação.
5. Em **Protocolo**, selecione **HTTP** (para sites) ou **TCP** (para outros aplicativos baseados em TCP).
6. Em **Porta**, especifique a porta a ser usada ao acessar a investigação.
7. Em **Caminho** (apenas para investigações HTTP), especifique o caminho a ser usado como uma investigação.
4. Em **Intervalo**, especifique a frequência de investigação do aplicativo.
5. Em **Limite não íntegro**, especifique quantas tentativas devem falhar antes que a VM de back-end seja marcada como não íntegra.
5. Clique em **OK** para criar a investigação.

### Etapa 3

Configurar as regras de balanceamento de carga:

1. No portal do Azure, clique em **Procurar** > **Balanceadores de carga** e clique no balanceador de carga que você criou acima.
2. Na folha **Configurações**, clique em **Regras de balanceamento de carga**. 
3. Na folha **Regras de balanceamento de carga**, clique em **Adicionar**.
4. Na folha **Adicionar regra de balanceamento de carga**, digite um **Nome** para a regra.
5. Em **Protocolo**, selecione **HTTP** (para sites) ou **TCP** (para outros aplicativos baseados em TCP).
6. Em **Porta**, especifique a porta a qual os clientes se conectam no balanceador de carga.
7. Em **Porta back-end**, especifique a porta a ser usada no pool de back-end (normalmente, a porta do balanceador de carga e a porta de back-end são iguais).
8. Em **Pool de back-end**, selecione o pool de back-end criado acima.
9. Em **Persistência da sessão**, selecione como você deseja que as sessões persistam.
10. Em **Tempo limite de ociosidade (minutos)**, especifique o tempo limite de ociosidade.
11. Em **IP flutuante (retorno de servidor direto)**, clique em **Desabilitado** ou **Habilitado**.
12. Clique em **OK**.
 
## Próximas etapas

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0316_2016-->