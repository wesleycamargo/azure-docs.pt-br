---
title: Trabalhar com servidores proxy locais existentes e o Azure AD | Microsoft Docs
description: Cobre como trabalhar com servidores proxy locais existentes.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2fab64ef8bf07532c4fd2fff4f4af68dc0b44b45
ms.openlocfilehash: 659edc19c9f043d6a388f1bb293c72e379fa6e4a


---

# <a name="work-with-existing-on-premises-proxy-servers"></a>Trabalhar com servidores proxy locais existentes

Este artigo explica como configurar o conector do Proxy de Aplicativo para trabalhar com servidores proxy de saída. Ele destina-se aos clientes com ambientes de rede que já têm proxies.

Começamos analisando este principais cenários de implantação:
* Configurando conectores para ignorar os proxies de saída locais.
* Configurando conectores para usar um proxy de saída para acessar o Proxy de Aplicativo do Azure AD.

Para saber mais sobre como os conectores funcionam, confira [Como fornecer acesso remoto seguro a aplicativos locais](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="configure-your-connectors"></a>Configurar os conectores

O principal serviço do conector usa o Barramento de Serviço do Azure para tratar de algumas comunicações subjacentes com o serviço de Proxy de Aplicativo do Azure AD. O Barramento de Serviço traz com ele requisitos de configuração adicionais.

Para saber mais sobre como resolver problemas de conectividade do Azure AD, confira [How to troubleshoot Azure AD Application Proxy connectivity problems](https://blogs.technet.microsoft.com/applicationproxyblog/2015/03/24/how-to-troubleshoot-azure-ad-application-proxy-connectivity-problems) (Como solucionar problemas de conectividade do Proxy de Aplicativo do Azure AD). 

## <a name="configure-the-outbound-proxy"></a>Configurar o proxy de saída

Se você tiver um proxy de saída no seu ambiente, certifique-se de que a conta que está fazendo a instalação esteja apropriadamente configurada para usar o proxy de saída. Como o instalador é executado no contexto do usuário que está fazendo a instalação, isso pode ser feito usando o Microsoft Edge ou outro navegador de Internet. 

Para definir as configurações de proxy usando o Microsoft Edge, vá para Configurações, Exibir Configurações Avançadas, Abrir as Configurações de Proxy, Configuração de Proxy Manual. Defina "Usar um servidor proxy" como Ativado, marque "Não usar o servidor proxy para endereços locais de intranet" e altere o endereço e a porta para refletir seu servidor proxy local.

Preencha as configurações de proxy necessárias, como mostrado na caixa de opções abaixo.

 ![Ignorar endereços locais do Azure AD](./media/application-proxy-working-with-proxy-servers/proxy-bypass-local-addresses.png)
 
## <a name="bypass-outbound-proxies"></a>Ignorar proxies de saída

Por padrão, os componentes subjacentes do SO usados pelo conector para fazer solicitações de saída tentam automaticamente localizar um servidor proxy na rede usando a WPAD (Descoberta Automática de Proxy da Web), se ela estiver habilitada no ambiente.

Normalmente, isso é feito com uma pesquisa de DNS por wpad.domainsuffix. Se isso for resolvido no DNS, uma solicitação HTTP será feita ao endereço IP para wpad.dat, que será o script da configuração de proxy no seu ambiente. O conector usará isso para selecionar um servidor proxy de saída. No entanto, o tráfego do conector pode continuar interrompido, devido às definições de configuração adicionais necessárias no proxy. 

Na próxima seção, abordaremos as etapas de configuração necessárias no proxy de saída para que o tráfego flua por ele. Mas, primeiramente, vamos ver como é possível configurar o conector para ignorar o proxy local de modo a garantir que ele use conectividade direta com os serviços do Azure. Essa é a maneira recomendada de seguir (desde que permitido pela sua política de rede), pois significa que você tem uma configuração a menos para manter.

Para desabilitar o uso do proxy de saída para o conector, edite o arquivo C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config e adicione a seção [system.net] mostrada no exemplo de código a seguir:

```xml
 <?xml version="1.0" encoding="utf-8" ?>
<configuration>
<system.net>
<defaultProxy enabled="false"></defaultProxy>
</system.net>
 <runtime>
<gcServer enabled="true"/>
  </runtime>
  <appSettings>
<add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```
Para garantir que o serviço de Atualizador do Conector também ignore o proxy, faça uma alteração semelhante no arquivo ApplicationProxyConnectorUpdaterService.exe.config, localizado em C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

Não se esqueça de fazer cópias dos arquivos originais, caso você precise reverter para os arquivos .config padrão. 

## <a name="use-the-outbound-proxy-server"></a>Usar o servidor proxy de saída

Conforme mencionado acima, em alguns ambientes de cliente, há um requisito para que todo o tráfego de saída, sem exceção, passe por um proxy de saída. Consequentemente, ignorar o proxy não é uma opção.

Você pode configurar o tráfego do conector para passar pelo proxy de saída, como mostrado abaixo.

 ![Ignorar endereços locais do Azure AD](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

Como consequência de ter apenas o tráfego de saída, não há a necessidade de configurar o balanceamento de carga entre os conectores nem de configurar o acesso de entrada por meio dos firewalls.

Em qualquer caso, será necessário executar as seguintes etapas:
1. Configurar o conector e o serviço do Atualizador para passar pelo proxy de saída.
2. Definir as configurações do proxy para permitir conexões com o serviço de Proxy de Aplicativo do Azure AD.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Etapa 1: Configurar o conector e serviços relacionados para passar pelo proxy de saída

Conforme descrito acima, se a WPAD estiver habilitada no ambiente e corretamente configurada, o conector descobrirá automaticamente o servidor proxy de saída e tentará usá-lo. No entanto, você pode configurar explicitamente o conector para passar por um proxy de saída. 

Para isso, edite o arquivo C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config e adicione a seção [system.net] mostrada no exemplo de código abaixo:

```xml
 <?xml version="1.0" encoding="utf-8" ?>
<configuration>
<system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
</system.net>
  <runtime>
     <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

>[!NOTE]
>Altere _proxyserver:8080_ para refletir o nome do servidor proxy local ou o endereço IP e a porta na qual ele está escutando.
>

Em seguida, você precisa configurar o serviço de atualizador do conector para usar o proxy, fazendo uma alteração semelhante no arquivo localizado em C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

###<a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Etapa 2: Configurar o Proxy para que o fluxo do tráfego do conector e serviços relacionados passe por ele

Há 4 aspectos a serem considerados no proxy de saída:
* Regras de saída do proxy
* Autenticação do proxy
* Portas do proxy
* Inspeção SSL

#### <a name="2a-proxy-outbound-rules"></a>2.A: Regras de saída do proxy
Permita o acesso aos seguintes pontos de extremidade para acesso ao serviço de conector:

* *.msappproxy.net 
* *.servicebus.microsoft.net 

Para o registro inicial, permita o acesso aos seguintes pontos de extremidade:

* login.windows.net 
* login.microsoftonline.com. 

Os canais de controle subjacentes do Barramento de Serviço usados pelo serviço de conector exigem conectividade adicional para endereços IP específicos. Observe que estamos trabalhando com a equipe do Barramento de Serviço para fazer a transição para um FQDN. No entanto, por enquanto, há duas opções: 
 
* Permitir o acesso à saída do conector a todos os destinos ou
* Permitir o acesso à saída do conector aos Intervalos de IP do Datacenter do Azure listados em https://www.microsoft.com/en-gb/download/details.aspx?id=41653

>[!NOTE]
>O desafio ao usar os Intervalos de IP do Datacenter do Azure é que eles são atualizados semanalmente, de modo que você precisará estabelecer um processo para garantir que as regras de acesso sejam atualizadas corretamente.
>

#### <a name="2b-proxy-authentication"></a>2.B: Autenticação do proxy

No momento, não há suporte à autenticação do proxy. Nossa recomendação atual é permitir acesso anônimo ao conector aos destinos da Internet.

#### <a name="2c-proxy-ports"></a>2.C: Portas do proxy

O conector faz conexões de saída baseadas em SSL usando o método CONNECT. Basicamente, isso configura um túnel pelo proxy de saída. Alguns servidores proxy, por padrão, permitem o túnel de saída somente para portas SSL padrão, como a 443. Se for esse o caso, o servidor proxy precisará estar configurado para permitir túnel para portas adicionais.

Configure o servidor proxy para permitir o túnel para as portas SSL não padrão 8080, 9090, 9091 e 10100-10120.

>[!NOTE]
>Quando o Barramento de Serviço for executado por HTTPS, ele usará a porta 443. No entanto, por padrão, o Barramento de Serviço tentará conexões TCP diretas e voltará para HTTPS somente se a conectividade direta falhar.> 
> 

Para garantir que o tráfego do Barramento de Serviço também seja enviado pelo servidor proxy de saída, é preciso ter certeza de que o conector não pode se conectar diretamente com os serviços do Azure pelas portas 9350, 9352 e 5671.

#### <a name="2d-ssl-inspection"></a>2.D: Inspeção SSL
Não use a inspeção SSL para o tráfego do conector, pois isso causará problemas no tráfego. 

Pronto. Agora, você deve ver todo o tráfego fluindo pelo proxy. Caso enfrente problemas, as etapas de solução de problemas a seguir devem ajudar.

### <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Solucionar problemas do Proxy do conector e problemas de conectividade de serviço

A melhor maneira de identificar e solucionar problemas de conectividade com o conector é usar uma captura da rede no serviço de conector ao iniciar esse serviço. Como essa tarefa pode ser um tanto quanto assustadora, vamos observar umas dicas rápidas sobre como capturar e filtrar rastreamentos de rede.

Você pode usar a ferramenta de monitoramento de sua preferência. Para os fins deste artigo, usamos o Monitor de Rede da Microsoft 3.4, que você pode baixar em https://www.microsoft.com/en-us/download/details.aspx?id=4865.

Os exemplos e filtros que usamos abaixo são específicos do Monitor de Rede, mas os princípios podem ser aplicados a qualquer ferramenta de análise.

### <a name="take-a-capture-using-microsoft-network-monitor"></a>Fazer uma captura usando o Monitor de Rede da Microsoft

Para iniciar uma captura, abra o Monitor de Rede e clique em Nova Captura. Pressione o botão Iniciar para começar.

 ![Monitor de Rede do Azure AD](./media/application-proxy-working-with-proxy-servers/network-capture.png)

Depois de concluir uma captura, clique no botão Parar para finalizá-la.

### <a name="take-a-capture-of-connector-traffic"></a>Fazer uma captura do tráfego do conector

Para a solução de problemas inicial, execute as seguintes etapas: 

1. Em services.msc, pare o serviço de conector do Proxy de Aplicativo do Microsoft AAD (como mostrado abaixo).
2. Inicie a captura da rede.
3. Inicie o serviço de conector do Proxy de Aplicativo do Microsoft AAD.
4. Pare a captura de rede.

 ![Monitor de Rede do Azure AD](./media/application-proxy-working-with-proxy-servers/services-local.png)

### <a name="look-at-the-connector-to-proxy-server-requests"></a>Examinar o conector em busca de solicitações do servidor proxy

Agora que temos uma captura de rede, você está pronto para filtrá-la. O mais importante ao examinar o rastreamento é entender como filtrar a captura.

Um dos filtros é o que se segue (onde 8080 é a porta de serviço do proxy): 

(http.Request ou http.Response) e tcp.port==8080

Se você inserir esse filtro na janela Filtro de Exibição e selecionar Aplicar, o tráfego capturado será filtrado com base no filtro.

O filtro acima mostrará apenas as solicitações HTTP e as respostas bidirecionais para a porta do proxy. Para a inicialização de um conector onde ele está configurado para um servidor proxy, algo parecido com isso deverá ser mostrado:

 ![Monitor de Rede do Azure AD](./media/application-proxy-working-with-proxy-servers/http-requests.png)

Agora, você está procurando especificamente as solicitações CONNECT que mostram que estamos nos comunicando com o servidor proxy. Caso seja bem-sucedido, você receberá uma resposta HTTP OK (200).

Caso sejam exibidos outros códigos de resposta, como 407 ou 502, isso indica que o proxy está exigindo autenticação ou não está permitindo o tráfego por algum outro motivo. Nesse ponto, você pode interagir com a equipe de suporte do servidor proxy.

### <a name="identify-failed-tcp-connection-attempts"></a>Identificar tentativas de conexão TCP com falha

Outro cenário comum no qual você talvez possa estar interessado é quando o conector está tentando se conectar diretamente, mas não está conseguindo. 

Outro filtro do Monitor de Rede que ajuda na identificação é:

property.TCPSynRetransmit

Um pacote SYN é o primeiro pacote enviado para estabelecer uma conexão TCP. Caso ele não retorne uma resposta, o SYN faz uma nova tentativa. O filtro acima permite que você veja qualquer SYN retransmitido. Assim, você pode ver se eles correspondem ao tráfego relacionado a algum conector. 

O exemplo a seguir mostra uma tentativa de conexão que falhou com a porta 9352 do Barramento de Serviço:

 ![Monitor de Rede do Azure AD](./media/application-proxy-working-with-proxy-servers/failed-connection-attempt.png)

Caso algo como a resposta acima seja exibido, isso significa que o conector está tentando se comunicar diretamente com o serviço de Barramento de Serviço do Azure. Se você espera que o conector esteja fazendo conexões diretas com os serviços do Azure, essa é uma clara indicação de um problema de rede/firewall.

>[!NOTE]
>Caso a configuração seja para usar um servidor proxy, isso pode ser o Barramento de Serviço tentando uma conexão TCP direta antes de tentar a conexão por HTTPS. Não se esqueça disso.
>

A análise do rastreamento da rede não é para qualquer um. Mas ela pode ser uma ferramenta muito valiosa para obter informações rápidas sobre o que está acontecendo em sua rede. 

Caso você continue tendo problemas de conectividade com o conector, abra um tíquete com nossa equipe de suporte, que ficará satisfeita em poder ajudar com a solução do problema.

Para saber mais sobre como resolver erros do conector do Proxy de Aplicativo, confira [Solucionar problemas de Proxy de Aplicativo](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-troubleshoot).

## <a name="next-steps"></a>Próximas etapas

[Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-understand-connectors.md)<br>
[Como instalar silenciosamente o Conector de Proxy de Aplicativo do Azure AD](active-directory-application-proxy-silent-installation.md)







<!--HONumber=Feb17_HO1-->


