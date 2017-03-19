---
title: Trabalhar com servidores proxy locais existentes e o Azure AD | Microsoft Docs
description: Cobre como trabalhar com os servidores proxy locais existentes.
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
ms.sourcegitcommit: 07adf751001a49e0365dde7cb8d7d2317a6b8134
ms.openlocfilehash: d79c8418769656ecfd35a71a450176bd27427f68
ms.lasthandoff: 02/27/2017


---

# <a name="work-with-existing-on-premises-proxy-servers"></a>Trabalhar com servidores proxy locais existentes

Este artigo explica como configurar o Conector Proxy do Aplicativo do Azure Active Directory (Azure AD) para trabalhar com os servidores proxy de saída. Ele destina-se aos clientes com ambientes de rede que já têm proxies.

Começamos analisando este principais cenários de implantação:
* Configure os conectores para ignorar os proxies de saída locais.
* Configure os conectores para usar um proxy de saída para acessar o Proxy do Aplicativo do Azure AD.

Para saber mais sobre como os conectores funcionam, confira [Como fornecer acesso remoto seguro a aplicativos locais](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="configure-your-connectors"></a>Configurar os conectores

O principal serviço do conector usa o Barramento de Serviço do Azure para tratar de algumas comunicações subjacentes com o serviço de Proxy de Aplicativo do Azure AD. O Barramento de Serviço traz com ele requisitos de configuração adicionais.

Para saber mais sobre como resolver problemas de conectividade do Azure AD, confira [How to troubleshoot Azure AD Application Proxy connectivity problems](https://blogs.technet.microsoft.com/applicationproxyblog/2015/03/24/how-to-troubleshoot-azure-ad-application-proxy-connectivity-problems) (Como solucionar problemas de conectividade do Proxy de Aplicativo do Azure AD).

## <a name="configure-the-outbound-proxy"></a>Configurar o proxy de saída

Se você tiver um proxy de saída em seu ambiente, verifique se a conta que está fazendo a instalação está devidamente configurada para usar o proxy de saída. Como o instalador é executado no contexto do usuário que está fazendo a instalação, você pode verificar a configuração usando o Microsoft Edge ou outro navegador da Internet.

Para definir as configurações do proxy usando o Microsoft Edge:

1. Vá para **Configurações** > **Exibir Configurações Avançadas** > **Abrir Configurações do Proxy** > **Configuração do Proxy Manual**.
2. Defina **Usar um servidor proxy** para **Ativado**, selecione a caixa de texto **Não usar o servidor proxy para endereços locais (intranet)**, em seguida, altere o endereço e a porta para refletir seu servidor proxy local.
3. Preencha as configurações do proxy necessárias.

   ![Caixa de diálogo para as configurações do proxy](./media/application-proxy-working-with-proxy-servers/proxy-bypass-local-addresses.png)

## <a name="bypass-outbound-proxies"></a>Ignorar proxies de saída

Por padrão, os componentes subjacentes do SO que o conector usar para fazer solicitações de saída tentam localizar automaticamente um servidor proxy na rede. Eles usarão a Descoberta Automática de Proxy da Rede (WPAD), se ela estiver habilitada no ambiente.

Os componentes do SO tentam localizar um servidor proxy realizando uma pesquisa de DNS para wpad.domainsuffix. Se isso for resolvido no DNS, uma solicitação HTTP será feita para o endereço IP de wpad.dat. Essa solicitação se tornará o script de configuração do proxy em seu ambiente. O conector usará o script para selecionar um servidor proxy de saída. No entanto, o tráfego do conector ainda pode continuar interrompido devido às definições de configuração adicionais necessárias no proxy.

Na próxima seção, abordaremos as etapas de configuração necessárias no proxy de saída para que o tráfego flua por ele. Mas, primeiro, veremos como é possível configurar o conector para ignorar o proxy local de modo a garantir que ele use uma conectividade direta com os serviços do Azure. Isso é o que recomendamos (se sua política de rede permitir), porque significa que você terá uma menor configuração para manter.

Para desabilitar o uso do proxy de saída para o conector, edite o arquivo C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config e adicione a seção [system.net] mostrada neste exemplo de código:

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
Para garantir que o serviço Atualizador do Conector também ignore o proxy, faça uma alteração semelhante no arquivo ApplicationProxyConnectorUpdaterService.exe.config localizado em C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

Faça cópias dos arquivos originais, caso você precise voltar para os arquivos .config padrão.

## <a name="use-the-outbound-proxy-server"></a>Usar o servidor proxy de saída

Como mencionado antes, alguns ambientes do cliente requerem que todo o tráfego de saída passe por um proxy de saída, sem exceção. Consequentemente, ignorar o proxy não é uma opção.

Você pode configurar que o tráfego do conector passe pelo proxy de saída, como mostrado no diagrama a seguir.

 ![Configurar o tráfego do conector para passar por um proxy de saída para o Proxy do Aplicativo do Azure AD](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

Como consequência de ter apenas o tráfego de saída, não há necessidade de configurar o balanceamento de carga entre os conectores nem configurar o acesso de entrada por meio de firewalls.

Em qualquer caso, é necessário executar as seguintes etapas:
1. configurar o serviço Atualizador do Conector para passar pelo proxy de saída.
2. configurar as definições do proxy para permitir conexões com o serviço Proxy do Aplicativo do Azure AD.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Etapa 1: Configurar o conector e serviços relacionados para passar pelo proxy de saída

Como descrito antes, se a WPAD estiver habilitada no ambiente e corretamente configurada, o conector descobrirá automaticamente o servidor proxy de saída e tentará usá-lo. No entanto, você pode configurar explicitamente o conector para passar por um proxy de saída.

Para isso, edite o arquivo C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config e adicione a seção [system.net] mostrada no exemplo de código:

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

Em seguida, configure o serviço Atualizador do Conector para usar o proxy, fazendo uma alteração semelhante no arquivo localizado em C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Etapa 2: Configurar o proxy para permitir o tráfego a partir do conector e que os serviços relacionados passem por ele

Há quatro aspectos a considerar no proxy de saída:
* Regras de saída do proxy
* Autenticação do proxy
* Portas do proxy
* Inspeção SSL

#### <a name="proxy-outbound-rules"></a>Regras de saída do proxy
Permita o acesso aos seguintes pontos de extremidade para acesso ao serviço de conector:

* *.msappproxy.net
* *.servicebus.microsoft.net

Para o registro inicial, permita o acesso aos seguintes pontos de extremidade:

* login.windows.net
* login.microsoftonline.com

Os canais de controle subjacentes do Barramento de Serviço que o serviço do conector usa também requerem uma conectividade com endereços IP específicos. Até o Barramento de Serviço ir para um FQDN, há duas opções:

* permitir o acesso de saída do conector para todos os destinos.
* permitir o acesso de saída do conector para os [intervalos IP do datacenter do Azure](https://www.microsoft.com/en-gb/download/details.aspx?id=41653).

>[!NOTE]
>O desafio de usar a lista de intervalos IP do datacenter do Azure é que ela é atualizada semanalmente. Você precisará implantar um processo para garantir que as regras de acesso sejam atualizadas de acordo.
>

#### <a name="proxy-authentication"></a>Autenticação do proxy

No momento, não há suporte à autenticação do proxy. Nossa recomendação atual é permitir acesso anônimo ao conector aos destinos da Internet.

#### <a name="proxy-ports"></a>Portas do proxy

O conector faz conexões de saída baseadas em SSL usando o método CONNECT. Basicamente, esse método configura um túnel pelo proxy de saída. Alguns servidores proxy, por padrão, permitem o túnel de saída somente para as portas SSL padrão, como a 443. Se for este o caso, o servidor proxy precisará ser configurado para permitir o túnel para portas adicionais.

Configure o servidor proxy para permitir o túnel para as portas SSL não padrão 8080, 9090, 9091 e 10100-10120.

>[!NOTE]
>Quando o Barramento de Serviço for executado por HTTPS, ele usará a porta 443. No entanto, por padrão, o Barramento de Serviço tenta as conexões TCP diretas e voltará para o HTTPS somente se a conectividade direta falhar.
>

Para garantir que o tráfego do Barramento de Serviço também seja enviado pelo servidor proxy de saída, verifique se o conector não pode conectar diretamente os serviços do Azure pelas portas 9350, 9352 e 5671.

#### <a name="ssl-inspection"></a>Inspeção SSL
Não use a inspeção SSL para o tráfego do conector, pois isso causará problemas no tráfego dele.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Solucionar problemas do proxy do conector e problemas de conectividade do serviço
Agora, você deve ver todo o tráfego fluindo pelo proxy. Se você tiver problemas, as seguintes informações de solução de problemas deverão ajudar.

A melhor maneira de identificar e solucionar os problemas de conectividade do conector é usar uma captura da rede no serviço do conector ao iniciar tal serviço. Como essa tarefa pode ser um tanto quanto assustadora, vamos observar umas dicas rápidas sobre como capturar e filtrar rastreamentos de rede.

Você pode usar a ferramenta de monitoramento de sua preferência. Para este artigo, usamos o Microsoft Network Monitor 3.4. Você pode [baixá-lo na Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=4865).

Os exemplos e filtros que usamos nas seções a seguir são específicos do Monitor de Rede, mas os princípios podem ser aplicados a qualquer ferramenta de análise.

### <a name="take-a-capture-by-using-network-monitor"></a>Fazer uma captura usando o Monitor de Rede

Para iniciar uma captura:

1. Abra o Monitor de Rede e clique em **Nova Captura**.
2. Clique no botão **Iniciar**.

   ![Janela Monitor de Rede](./media/application-proxy-working-with-proxy-servers/network-capture.png)

Depois de concluir uma captura, clique no botão **Parar** para encerrar.

### <a name="take-a-capture-of-connector-traffic"></a>Fazer uma captura do tráfego do conector

Para a solução de problemas inicial, execute as seguintes etapas:

1. Em services.msc, pare o serviço Conector do Proxy de Aplicativo do Azure AD.
2. Inicie a captura da rede.
3. Inicie o serviço Conector do Proxy de Aplicativo do Azure AD.
4. Pare a captura de rede.

   ![Serviço Conector do Proxy de Aplicativo do Azure AD em services.msc](./media/application-proxy-working-with-proxy-servers/services-local.png)

### <a name="look-at-the-requests-from-the-connector-to-the-proxy-server"></a>Examinar as solicitações do conector para o servidor proxy

Agora que você tem uma captura de rede, está pronto para filtrá-la. O mais importante ao examinar o rastreamento é entender como filtrar a captura.

Um filtro é como a seguir (onde 8080 é a porta do serviço proxy):

**(http.Request ou http.Response) e tcp.port==8080**

Se você inserir esse filtro na janela **Filtro de Exibição** e selecionar **Aplicar**, ele filtrará o tráfego capturado com base no filtro.

O filtro anterior mostrará apenas as solicitações e respostas HTTP para/da porta do proxy. Para a inicialização de um conector, na qual ele está configurado para usar um servidor proxy, o filtro mostraria algo assim:

 ![Lista de exemplo das solicitações e respostas HTTP filtradas](./media/application-proxy-working-with-proxy-servers/http-requests.png)

Agora, você está procurando especificamente as solicitações CONNECT que mostram a comunicação com o servidor proxy. No caso de sucesso, você receberá uma resposta HTTP OK (200).

Se você vir outros códigos de resposta, como 407 ou 502, o proxy está exigindo autenticação ou não está permitindo o tráfego por algum outro motivo. Neste ponto, você interage com a equipe de suporte do servidor proxy.

### <a name="identify-failed-tcp-connection-attempts"></a>Identificar tentativas de conexão TCP com falha

Outro cenário comum no qual você pode estar interessado é quando o conector está tentando conectar diretamente, mas está falhando.

Outro filtro do Monitor de Rede que ajuda a identificar com facilidade o problema é:

**property.TCPSynRetransmit**

Um pacote SYN é o primeiro pacote enviado para estabelecer uma conexão TCP. Se o pacote não retornar uma resposta, o SYN fará uma nova tentativa. Você pode usar o filtro anterior para ver quaisquer SYNs retransmitidos. Assim, você pode verificar se eles correspondem ao tráfego relacionado a algum conector.

O exemplo a seguir mostra uma tentativa de conexão que falhou na porta 9352 do Barramento de Serviço:

 ![Resposta de exemplo para uma tentativa de conexão com falha](./media/application-proxy-working-with-proxy-servers/failed-connection-attempt.png)

Se você vir algo como a resposta anterior, o conector está tentando comunicar-se diretamente com o Barramento de Serviço do Azure. Se você espera que o conector faça conexões diretas com os serviços do Azure, essa resposta será uma clara indicação de que há um problema de rede ou firewall.

>[!NOTE]
>Se você estiver configurado para usar um servidor proxy, essa resposta poderá significar que o Barramento de Serviço está tentando uma conexão TCP direta antes de tentar uma conexão via HTTPS.
>

A análise do rastreamento da rede não é para qualquer um. Mas pode ser uma ferramenta valiosa para obter informações rápidas sobre o que está acontecendo com sua rede.

Se você continuar a ter dificuldades com os problemas de conectividade do conector, crie um tíquete com nossa equipe de suporte. A equipe pode ajudar a solucionar problemas no futuro.

Para obter informações sobre como resolver os erros do Conector do Proxy do Aplicativo, consulte [Solucionar Problemas do Proxy do Aplicativo](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-troubleshoot).

## <a name="next-steps"></a>Próximas etapas

[Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-understand-connectors.md)<br>
[Como instalar silenciosamente o Conector de Proxy de Aplicativo do Azure AD](active-directory-application-proxy-silent-installation.md)

