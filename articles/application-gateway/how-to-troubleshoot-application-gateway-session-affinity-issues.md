---
title: Solução de problemas de problemas de afinidade de sessão de Gateway de aplicativo do Azure
description: Este artigo fornece informações sobre como solucionar problemas de afinidade de sessão no Gateway de aplicativo do Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: bcfb50a2d7d5221abaa15a5b286163cf63b7a875
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446669"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Solucionar problemas de afinidade de sessão de Gateway de aplicativo do Azure

Saiba como diagnosticar e resolver problemas de afinidade de sessão com o Gateway de aplicativo do Azure.

## <a name="overview"></a>Visão geral

O recurso de afinidade de sessão baseada em cookies é útil quando você deseja manter uma sessão de usuário no mesmo servidor. Usando cookies gerenciados pelo gateway, o Gateway de Aplicativo pode direcionar o tráfego seguinte de uma sessão de usuário para o mesmo servidor para processamento. Isso é importante em casos em que o estado de sessão é salvo localmente no servidor para uma sessão de usuário.

## <a name="possible-problem-causes"></a>Faz com que o possível problema

O problema em manter a afinidade de sessão baseada em cookie pode ocorrer pelos seguintes motivos principais:

- Configuração de "afinidade de baseada em cookie" não está habilitada
- Seu aplicativo não pode lidar com a afinidade de baseada em cookie
- Aplicativo está usando a afinidade de baseada em cookie, mas ainda a alternância entre servidores back-end de solicitações

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Verifique se a configuração de "afinidade baseada em Cookie" está habilitada

Às vezes, os problemas de afinidade de sessão podem ocorrer quando você se esqueça de habilitar a configuração de "Afinidade baseada em Cookie". Para determinar se você tiver habilitado a configuração de "Afinidade baseada em Cookie" na guia Configurações de HTTP no portal do Azure, siga as instruções:

1. Faça logon no [portal do Azure](https://portal.azure.com/).

2. No **barra de navegação esquerda** painel, clique em **todos os recursos**. Clique no nome do gateway de aplicativo na folha todos os recursos. Se a assinatura que você selecionou já contém vários recursos, você pode inserir o nome do gateway de aplicativo na **filtrar por nome...** para acessar facilmente o gateway de aplicativo.

3. Selecione **configurações de HTTP** guia sob **configurações**.

   ![solução de problemas-sessão-afinidade-problemas de-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Clique em **appGatewayBackendHttpSettings** no lado direito para verificar se você selecionou **habilitado** para afinidade de baseada em Cookie.

   ![solução de problemas-sessão-afinidade-problemas de-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Você também pode verificar o valor da "**CookieBasedAffinity**" é definido como *Enabled*sob "**backendHttpSettingsCollection**" usando um dos seguintes métodos:

- Execute [Get-AzureRmApplicationGatewayBackendHttpSettings](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermapplicationgatewaybackendhttpsettings) no PowerShell
- Examine o arquivo JSON usando o modelo do Azure Resource Manager

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>O aplicativo não pode lidar com a afinidade de baseada em cookie

#### <a name="cause"></a>Causa

O gateway de aplicativo só pode executar a afinidade baseada em sessão por meio de um cookie.

#### <a name="workaround"></a>Solução alternativa

Se o aplicativo não pode lidar com a afinidade de baseada em cookies, você deve usar um balanceador de carga do azure interno ou externo ou outra solução de terceiros.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Aplicativo está usando a afinidade de baseada em cookie, mas ainda a alternância entre servidores back-end de solicitações

#### <a name="symptom"></a>Sintoma

Você habilitou a configuração de afinidade de baseada em cookies, quando você acessar o Gateway de aplicativo usando um nome curto de URL no Internet Explorer, por exemplo: [ http://website ](http://website/) , a solicitação ainda é a alternância entre servidores back-end.

Para identificar esse problema, siga as instruções:

1. Assuma um rastreamento de depurador da web "Cliente" que está se conectando ao aplicativo por trás de Gateway(We are using Fiddler in this example) o aplicativo.
    **Dica** se você não souber como usar o Fiddler, marque a opção "**quero coletar o tráfego de rede e analise-os usando o depurador da web**" na parte inferior.

2. Verificar e analisar os logs de sessão, para determinar se os cookies fornecidos pelo cliente têm os detalhes de ARRAffinity. Se você não encontrar os detalhes de ARRAffinity, tal como "**ARRAffinity =** *ARRAffinityValue*" dentro do conjunto de cookie, o que significa que o cliente não está respondendo com o cookie ARRANJAR, que é fornecido pelo Gateway de aplicativo.
    Por exemplo: 

    ![solução de problemas-sessão-afinidade-problemas-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

        ![troubleshoot-session-affinity-issues-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

O aplicativo continua a tentar definir o cookie em cada solicitação até que ele obtém a resposta.

#### <a name="cause"></a>Causa

Esse problema ocorre porque o Internet Explorer e outros navegadores não podem armazenar ou usar o cookie com um nome curto de URL.

#### <a name="resolution"></a>Resolução

Para corrigir esse problema, você deve acessar o Gateway de aplicativo usando um FQDN. Por exemplo, use [ http://website.com ](http://website.com/) ou [ http://appgw.website.com ](http://appgw.website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>Logs adicionais para solucionar problemas

Você pode coletar logs adicionais e analisá-los para solucionar a afinidade de sessão baseada em cookies relacionados de problemas

### <a name="analyze-application-gateway-logs"></a>Analisar logs do Gateway de aplicativo

Para coletar os logs do Gateway de aplicativo, siga as instruções:

Habilitar o log por meio do portal do Azure

1. No [portal do Azure](https://portal.azure.com/), localize o recurso e, em seguida, clique em **logs de diagnóstico**.

   Para o Gateway de Aplicativo, três logs estão disponíveis: Log de acesso, o log de desempenho, o log de Firewall

2. Para começar a coletar dados, clique em **ativar o diagnóstico**.

   ![solução de problemas-sessão-afinidade-problemas-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. A folha **Configurações de diagnóstico** fornece as configurações dos logs de diagnóstico. Neste exemplo, o Log Analytics armazena os logs. Clique em **configurar** sob **Log Analytics** para definir seu espaço de trabalho. Você também pode usar os hubs de eventos e uma conta de armazenamento para salvar os logs de diagnóstico.

   ![solução de problemas-sessão-afinidade-problemas-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Confirme as configurações e, em seguida, clique em **salvar**.

   ![solução de problemas-sessão-afinidade-problemas-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Exibir e analisar os logs de acesso do Gateway de aplicativo

1. No portal do Azure sob o modo de exibição de recursos do Gateway de aplicativo, selecione **logs de diagnóstico** na **monitoramento** seção.

   ![solução de problemas-sessão-afinidade-problemas-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. No lado direito, selecione "**ApplicationGatewayAccessLog**" na lista suspensa em **categorias de Log.**  

   ![solução de problemas-sessão-afinidade-problemas-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Na lista de Log de acesso do Gateway de aplicativo, clique no log que você deseja analisar e exportar e, em seguida, exportar o arquivo JSON.

4. Converter o arquivo JSON que você exportou na etapa 3 para arquivo CSV e exibi-los no Excel, Power BI ou qualquer outra ferramenta de visualização de dados.

5. Verifique os seguintes dados:

- **ClientIP**– esse é o endereço IP do cliente do cliente está se conectando.
- **ClientPort** -essa é a porta de origem do cliente está se conectando para a solicitação.
- **RequestQuery** – isso indica que o servidor de destino que a solicitação é recebida.
- **Server-Routed**: Instância de pool de back-end que a solicitação é recebida.
- **X-AzureApplicationGateway-LOG-ID**: ID de correlação usado para a solicitação. Ela pode ser usada para solucionar problemas de tráfego nos servidores back-end. Por exemplo:  X-AzureApplicationGateway-ACERTOS de CACHE = 0 & SERVER-ROUTED = 10.0.2.4.

  - **SERVER-STATUS**: Código de resposta HTTP que o Gateway de Aplicativo recebeu do back-end.

  ![solução de problemas-sessão-afinidade-problemas-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Se você ver dois itens são provenientes do mesmo ClientIP e porta do cliente, e eles são enviados para o mesmo servidor de back-end, isso significa que o Gateway de aplicativo configurado corretamente.

Se você ver dois itens são provenientes do mesmo ClientIP e porta do cliente, e elas são enviadas para os diferentes servidores back-end, isso significa que a solicitação é a alternância entre servidores de back-end, selecionadas "**aplicativo está usando a afinidade de baseada em cookie, mas as solicitações ainda a alternância entre servidores back-end**"na parte inferior para solucioná-lo.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Usar o depurador da web para capturar e analisar o tráfego HTTP ou HTTPS

Ferramentas de depuração, como o Fiddler Web, pode ajudá-lo a depurar aplicativos web, capturando tráfego de rede entre os computadores da Internet e de teste. Essas ferramentas permitem que você inspecione os dados de entrada e saídos como recebe/envios de navegador-los. Fiddler, neste exemplo, tem a opção de reprodução HTTP que pode ajudar você a solucionar problemas do lado do cliente com aplicativos web, especialmente para o tipo de autenticação do problema.

Use o depurador da web de sua escolha. Neste exemplo usaremos o Fiddler para capturar e analisar os tráfegos http ou https, siga as instruções:

1. Baixar a ferramenta Fiddler em <https://www.telerik.com/download/fiddler>.

    > [!NOTE]
    > Escolha Fiddler4 se o computador de captura tem 4 do .NET está instalado. Caso contrário, escolha o Fiddler2.

2. Clique com botão direito no executável de instalação e executar como administrador para instalar.

            ![troubleshoot-session-affinity-issues-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Quando você abre o Fiddler, ele deve começar automaticamente capturar o tráfego (Observe a captura no canto inferior esquerdo). Pressione F12 para iniciar ou parar a captura de tráfego.

        ![troubleshoot-session-affinity-issues-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Provavelmente, você se interessará descriptografado tráfego HTTPS e você pode habilitar a descriptografia de HTTPS, selecionando **ferramentas** > **opções do Fiddler**e marque a caixa " **descriptografar O tráfego HTTPS**".

        ![troubleshoot-session-affinity-issues-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Você pode remover nas sessões anteriores não relacionadas antes de reproduzir o problema clicando **X** (ícone) > **remover tudo** que siga a captura de tela: 

        ![troubleshoot-session-affinity-issues-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Depois de você ter reproduzido o problema, salve o arquivo para análise, selecionando **arquivo** > **salvar** > **todas as sessões...** . 

        ![troubleshoot-session-affinity-issues-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Verificar e analisar os logs de sessão para determinar qual é o problema.

    Por exemplo:

- **Exemplo a:** Você encontrar um log de sessão que a solicitação é enviada do cliente, e ele vai para o endereço IP público do Gateway de aplicativo, clique nesse log para exibir os detalhes.  No lado direito, os dados na caixa inferior são o que o Gateway de aplicativo está retornando para o cliente. Selecione a guia "Brutos" e determinar se o cliente está recebendo um "**Set-Cookie: ARRAffinity=** *ARRAffinityValue*." Se não houver nenhum cookie, afinidade de sessão não está definida ou o Gateway de aplicativo não está aplicando o cookie de volta ao cliente.

   > [!NOTE]
   > Esse valor de ARRAffinity é a id cookie, que o Gateway de aplicativo define para o cliente a ser enviada para um determinado servidor de back-end.

    ![solução de problemas-sessão-afinidade-problemas-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Exemplo b:** O próximo log de sessão seguido anterior um é a resposta de volta para o Gateway de aplicativo, que definiu o ARRAAFFINITY do cliente. Se a id de cookie ARRAffinity corresponder, o pacote deve ser enviado para o mesmo servidor de back-end que foi usado anteriormente. Verifique as próximo várias linhas de comunicação http para ver se o cookie de ARRAffinity do cliente está sendo alterado.

    ![solução de problemas-sessão-afinidade-problemas-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Para a mesma sessão de comunicação, o cookie não deve para alterar. Marque a caixa superior à direita, selecione a guia "Cookies" para ver se o cliente está usando o cookie e enviar de volta para o Gateway de aplicativo. Caso contrário, o navegador do cliente não está mantendo e usando o cookie para conversas. Às vezes, o cliente poderá ficar.

 

## <a name="next-steps"></a>Próximas etapas

Se as etapas anteriores não resolverem o problema, abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).