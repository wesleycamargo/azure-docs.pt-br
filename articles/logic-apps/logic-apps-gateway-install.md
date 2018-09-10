---
title: Instalar gateway de dados local - Aplicativos Lógicos do Azure | Microsoft Docs
description: Como baixar e instalar o gateway de dados local antes de acessar dados no local a partir dos aplicativos lógicos
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/20/2018
ms.reviewer: yshoukry, LADocs
ms.suite: integration
ms.openlocfilehash: 616e3d81d577fd30e65117ec15c65250d3b3e27e
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503641"
---
# <a name="install-the-on-premises-data-gateway-for-azure-logic-apps"></a>Instalar o gateway de dados local para Aplicativo Lógico do Azure

Antes de conectar os aplicativos lógicos às fontes de dados locais, baixe e instale o gateway de dados local em um computador local. O gateway funciona como uma ponte que fornece transferência de dados rápida e criptografia entre as fontes de dados locais (fora da nuvem) e os aplicativos lógicos. Este artigo mostra como você pode baixar, instalar e configurar o gateway de dados local. 

É possível usar a mesma instalação do gateway com outros serviços, como o Power BI, Microsoft Flow, PowerApps e o Azure Analysis Services. Saiba mais sobre [como o gateway de dados funciona](#gateway-cloud-service).

<a name="supported-connections"></a>

O gateway dá suporte a [conectores locais](../connectors/apis-list.md#on-premises-connectors) no Aplicativo Lógico do Azure para essas fontes de dados:

*   BizTalk Server 2016
*   Sistema de Arquivos
*   IBM DB2  
*   IBM Informix
*   IBM MQ
*   MySQL
*   Banco de dados Oracle
*   PostgreSQL
*   Servidor de aplicativos SAP 
*   Servidor de mensagens SAP
*   SharePoint Server
*   SQL Server
*   Teradata

Para obter informações sobre como usar o gateway com outros serviços, consulte estes artigos:

* [Gateway de dados local do Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Gateway de dados local do Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Gateway de dados local do Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
* [Gateway de dados local do Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta corporativa ou de estudante](../active-directory/fundamentals/sign-up-organization.md) que tenha uma [assinatura do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer). Durante a instalação do gateway, você entra nessa conta para poder associar a instalação do gateway à assinatura do Azure. Posteriormente, também é possível usar a mesma conta ao criar um recurso do Azure para a instalação do gateway no portal do Azure. Caso você ainda não tenha uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* Aqui estão os requisitos para o computador local:

  **Requisitos mínimos**
  * .NET Framework 4.5.2
  * Versão de 64 bits do Windows 7 ou Windows Server 2008 R2 (ou posterior)

  **Requisitos recomendados**
  * CPU de 8 núcleos
  * Memória de 8 GB
  * Versão de 64 bits do Windows Server 2012 R2 (ou posterior)

* **Considerações importantes**

  * É possível instalar o gateway de dados local apenas em um computador local, não em um controlador de domínio. No entanto, não é necessário instalar o gateway no mesmo computador que a fonte de dados. Além disso, é necessário apenas um gateway para todas as fontes de dados, portanto, não há necessidade de instalar o gateway para cada fonte de dados.

    > [!TIP]
    > Para minimizar a latência, você pode instalar o gateway o mais próximo possível da sua fonte de dados ou no mesmo computador, presumindo que você tenha permissões.

  * Instale o gateway em um computador que *não* desligue, nem entre em suspensão ou conecte-se à Internet. O gateway não pode ser executado nessas condições. 
  O desempenho do gateway pode ser reduzido em uma rede sem fio.

  * Durante a instalação, somente será possível entrar com uma [conta corporativa ou de estudante](../active-directory/sign-up-organization.md) gerenciada pelo Azure AD (Azure Active Directory), e não uma conta da Microsoft. 
  Além disso, certifique-se de que essa conta não seja uma conta B2B (convidado) do Azure. 
  Também é necessário usar a mesma conta de logon no portal do Azure ao registrar a instalação do gateway, criando um recurso do Azure para o gateway. 
  Em seguida, será possível selecionar esse recurso de gateway ao criar a conexão entre o aplicativo lógico e a fonte de dados local. 
  [Por que eu devo usar uma conta corporativa ou de estudante do Azure AD?](#why-azure-work-school-account)

  > [!TIP]
  > Caso você tenha se inscrito em uma oferta do Office 365 e não tenha fornecido seu email de trabalho real, o endereço de entrada pode ser semelhante a este exemplo: `username@domain.onmicrosoft.com` 
  >
  > Se quiser usar uma conta da Microsoft que tenha uma [assinatura do Visual Studio Standard](https://visualstudio.microsoft.com/vs/pricing/), primeiro [crie um diretório (locatário) no Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md), ou use o diretório padrão, com a sua conta da Microsoft. 
  > Adicione um usuário com uma senha ao diretório, em seguida, forneça o acesso a esse usuário à sua assinatura. 
  > Em seguida, você pode entrar durante a instalação do gateway com esse nome de usuário e senha.

  * A região selecionada para a instalação do gateway determina o local onde é possível registrar posteriormente o gateway no Azure, criando um recurso do Azure. 
  Ao criar esse recurso de gateway no Azure, é necessário selecionar o *mesmo* local como a instalação do gateway. A região padrão é o mesmo local que o locatário do Azure AD, o qual gerencia a conta do Azure, mas é possível alterar o local durante a instalação do gateway.

  * Caso tenha um gateway existente que você queira configurar com um instalador de uma versão anterior à 14.16.6317.4, não será possível alterar o local do gateway executando o instalador mais recente. No entanto, em vez disso, é possível usar o instalador mais recente para configurar um novo gateway com o local desejado.
  
    Caso você tenha um instalador do gateway anterior à versão 14.16.6317.4, mas ainda não tiver instalado o gateway, é possível baixar e usar o instalador mais recente.

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalar gateway de dados

1. [Baixe, salve e execute o instalador do gateway em um computador local](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2. Aceite o caminho de instalação padrão ou especifique o local no computador em que você quer instalar o gateway.

3. Examine e aceite os termos de uso e a política de privacidade e, sem seguida, escolha **Instalar**.

   ![Aceitar os termos de uso e a política de privacidade](./media/logic-apps-gateway-install/accept-terms.png)

4. Depois que o gateway for instalado com êxito, forneça o endereço de email para a conta corporativa ou de estudante e, em seguida, escolha **Entrar**.

   ![Entrar com uma conta corporativa ou de estudante](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Escolha **registrar um novo gateway neste computador** > **Próximo**, o que registra a instalação do gateway com o [serviço de nuvem do gateway](#gateway-cloud-service). 

   ![Registrar gateway](./media/logic-apps-gateway-install/register-new-gateway.png)

6. Forneça essas informações para a instalação do gateway:

   * O nome desejado para a instalação 

   * A chave de recuperação que você quer criar, com pelo menos oito caracteres

     > [!IMPORTANT]
     > Salve e mantenha a chave de recuperação em um local seguro. Essa chave é necessária quando você altera o local do gateway, ou ao migrar, recuperar ou assumir um gateway existente.

   * Confirmação para a chave de recuperação 

     ![Configurar o gateway](./media/logic-apps-gateway-install/set-up-gateway.png)

7. Verifique a região selecionada para o serviço de nuvem do gateway e o Barramento de Serviço do Azure usado pela instalação do gateway. 

   ![Verificar a região](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > Para alterar essa região após concluir a instalação do gateway, será necessária a chave de recuperação para a instalação desse gateway. Além disso, é necessário desinstalar e reinstalar o gateway. Para obter mais informações, consulte [Alterar o local, migrar, recuperar ou assumir gateway existente](#update-gateway-installation).

   *Por que alterar a região de instalação do gateway?* 

   Por exemplo, para reduzir a latência, é possível alterar a região do gateway para a mesma região que o aplicativo lógico. 
   Ou então, é possível selecionar a região mais próxima à fonte de dados local. 
   O *recurso de gateway no Azure* e o aplicativo lógico podem ter locais diferentes.

8. Para aceitar a região padrão, escolha **Configurar**. Ou então, para alterar a região padrão, siga estas etapas:

   1. Ao lado de região atual, selecione **Alterar Região**. 

      ![Alterar região](./media/logic-apps-gateway-install/change-region.png)

   2. Na próxima página, abra a lista **Selecionar Região**, selecione a região desejada e escolha **Concluído**.

      ![Selecionar outra região](./media/logic-apps-gateway-install/select-region-gateway-install.png)

9. Após a página de confirmação ser exibida, escolha **Fechar**. 

   O instalador confirma se o gateway está online e pronto para uso.

   ![Gateway concluído](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

10. Agora registre o gateway no Azure [criando um recurso do Azure para a instalação do gateway](../logic-apps/logic-apps-gateway-connection.md). 

## <a name="enable-high-availability"></a>Habilitar alta disponibilidade

O gateway de dados local dá suporte à alta disponibilidade quando você tiver mais de uma instalação do gateway e configurá-las como clusters. Caso você tenha um gateway existente ao criar outro gateway, é possível, opcionalmente, criar clusters de alta disponibilidade. Esses clusters organizam gateways em grupos que podem ajudar a evitar pontos únicos de falha. Para usar essa funcionalidade, examine estes requisitos e considerações:

* Somente alguns conectores dão suporte à alta disponibilidade, como o conector do Sistema de Arquivos e outros no caminho. 
     
* Você já deve ter a instalação de pelo menos um gateway na mesma assinatura do Azure como o gateway primário e a chave de recuperação para essa instalação. 

* O gateway primário deve estar executando a atualização do gateway de novembro de 2017 ou posterior.

Depois de atender esses requisitos, ao criar o próximo gateway, selecione **Adicionar a um cluster de gateway existente**, selecione o gateway primário para o cluster e forneça a chave de recuperação para esse gateway primário.
Para obter mais informações, consulte [Clusters de alta disponibilidade para gateway de dados locais](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Alterar o local, migrar, restaurar ou assumir um gateway existente

Se for necessário alterar o local do gateway, mover a instalação do gateway para um novo computador, recuperar um gateway danificado ou assumir a propriedade de um gateway existente, você precisará da chave de recuperação que foi fornecida durante a instalação do gateway. Essa ação desconecta o gateway antigo.

1. A partir do **Painel de Controle** do seu computador, acesse **Programas e Recursos**. Na lista de programas, selecione **Gateway de dados local** e, em seguida, escolha **Desinstalar**.

2. [Reinstale o gateway de dados local](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

3. Depois que o instalador for aberto, entre com a mesma conta corporativa ou de estudante usada anteriormente para instalar o gateway.

4. Selecione **Migrar, restaurar ou assumir um gateway existente** e, em seguida, escolha **Próximo**.

   ![Selecionar "Migrar, restaurar ou assumir um gateway existente"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

5. Em **Gateways disponíveis** ou **Clusters de gateway disponíveis**, selecione a instalação do gateway que você quer alterar. Insira a chave de recuperação da instalação do gateway. 

   ![Selecionar o gateway primário](./media/logic-apps-gateway-install/select-existing-gateway.png)

6. Para alterar a região, selecione **Alterar Região** e a nova região.

7. Quando terminar, escolha **Configurar**.

## <a name="configure-proxy-or-firewall"></a>Configurar proxy ou firewall

O gateway de dados local cria uma conexão de saída com o [Barramento de Serviço do Azure](https://azure.microsoft.com/services/service-bus/). Se o ambiente de trabalho exigir que o tráfego passe por um proxy para acessar a Internet, essa restrição pode impedir que o gateway de dados conecte-se ao serviço de nuvem do gateway. Para determinar se a rede usa um proxy, examine este artigo no superuser.com: 

[Como posso saber qual servidor proxy estou usando? (SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using) 

Para fornecer informações de proxy para o gateway, confira [Definir configurações de proxy](https://docs.microsoft.com/power-bi/service-gateway-proxy). Para verificar se o proxy ou o firewall pode bloquear conexões, verifique se o computador pode efetivamente conectar-se à Internet e ao [Barramento de Serviço do Azure](https://azure.microsoft.com/services/service-bus/). Em um prompt do PowerShell, execute este comando:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Esse comando apenas testa a conectividade de rede e a conectividade com o Barramento de Serviço do Azure. O comando não tem qualquer relação com o gateway nem com o serviço de nuvem do gateway que criptografa e armazena as credenciais e detalhes do gateway. 
>
> Além disso, esse comando só está disponível no Windows Server 2012 R2 ou posterior e no Windows 8.1 ou posterior. Em versões anteriores do SO, você pode usar o Telnet para testar a conectividade. Saiba mais sobre [Soluções híbridas e de Barramento de Serviço do Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

Os resultados devem ser semelhantes a este exemplo com **TcpTestSucceeded** definido como **True**:

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Se **TcpTestSucceeded** não for definido como **True**, o gateway pode ser bloqueado por um firewall. Se você quiser ser abrangente, substitua os valores de **ComputerName** e **Port** pelos valores listados em [Configurar portas](#configure-ports) neste artigo.

O firewall também pode bloquear conexões que o Barramento de Serviço do Azure faz com os datacenters do Azure. Se esse cenário ocorrer, aprove (desbloqueie) todos os endereços IP para os datacenters em sua região. Para esses endereços IP, [obtenha a lista de endereços IP do Azure aqui](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Configure portas

O gateway cria uma conexão de saída para o [Barramento de Serviço do Azure](https://azure.microsoft.com/services/service-bus/) e comunica-se nas portas de saída: TCP 443 (padrão), 5671, 5672, 9350 a 9354. O gateway não exige portas de entrada. Saiba mais sobre [Soluções híbridas e de Barramento de Serviço do Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

O gateway usa estes nomes de domínio totalmente qualificados:

| Nomes de domínio | Portas de saída | DESCRIÇÃO | 
| ------------ | -------------- | ----------- | 
| *.analysis.windows.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.microsoftonline p.com | 443 | Usado para autenticação, dependendo da configuração. | 
| *.msftncsi.com | 443 | Usado para testar a conectividade com a Internet quando o gateway não puder ser acessado pelo serviço do Power BI. | 
| * .servicebus.windows.net | 443, 9350-9354 | Ouvintes de Retransmissão do Barramento de Serviço por meio de TCP (requer 443 para aquisição de token de Controle de Acesso) | 
| * .servicebus.windows.net | 5671-5672 | Advanced Message Queuing Protocol (AMQP) | 
| login.microsoftonline.com | 443 | HTTPS | 
||||

Em alguns casos, as conexões do Barramento de Serviço do Azure são feitas com o Endereço IP, em vez de nomes de domínio totalmente qualificados. Então, você talvez queira incluir os endereços IP em uma lista de permissões para a região de dados no firewall. Para colocar endereços IP na lista de permissões em vez de domínios, é possível baixar e usar a [lista de intervalos de IP do Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Os endereços IP nessa lista estão na notação [CIDR (Roteamento entre Domínios sem Classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

### <a name="force-https-communication-with-azure-service-bus"></a>Forçar comunicação HTTPS com o Barramento de Serviço do Azure

Alguns proxies permitem o tráfego somente para as portas 80 e 443. Por padrão, a comunicação com o Barramento de Serviço do Azure ocorre em portas diferentes da 443.
Você pode forçar o gateway para comunicar-se com o Barramento de Serviço do Azure usando HTTPS em vez de TCP direto, mas fazer isso pode reduzir consideravelmente o desempenho. Para executar essa tarefa, siga estas etapas:

1. Navegue até o local do cliente de gateway de dados local, que geralmente é possível encontrar aqui: ```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe```

   Caso contrário, para encontrar o local do cliente, abra o console de Serviços no mesmo computador, localize **Serviço de gateway de dados local** e exiba a propriedade **Caminho do executável**.

2. Abra este arquivo de *configuração*: **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. Altere o valor **ServiceBusSystemConnectivityModeString** de **AutoDetect** para **Https**:

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Conta de serviço do Windows

O gateway de dados local é executado como um serviço Windows nomeado "Serviço de gateway de dados local", mas usa "NT SERVICE\PBIEgwService" para suas credenciais de conta "Fazer logon como". Por padrão, o gateway de dados local tem permissões "Fazer logon como um serviço" para o computador no qual você instala o gateway. Para criar e manter o gateway no portal do Azure, a conta de serviço Windows deve ter, pelo menos, permissões de **Colaborador**. 

> [!NOTE]
> A conta de serviço Windows é diferente da conta usada para conexão a fontes de dados locais e da conta corporativa ou de estudante do Azure usada para entrar em serviços de nuvem.

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Reiniciar o gateway

O gateway de dados é executado como um serviço Windows, então, da mesma forma como qualquer outro serviço Windows, é possível iniciar e parar o gateway de várias maneiras. Por exemplo, você pode abrir um prompt de comando com permissões elevadas no computador em que o gateway está em execução e executar um destes comandos:

* Para parar o serviço, execute este comando:
  
  `net stop PBIEgwService`

* Para iniciar o serviço, execute este comando:
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>Administração de nível de locatário 

Atualmente, não há um único local onde os administradores de locatários possam gerenciar todos os gateways que outros usuários instalaram e configuraram. Se você for um administrador de locatários, talvez você queira pedir aos usuários em sua organização para adicioná-lo como administrador para cada gateway que instalarem. Assim é possível gerenciar todos os gateways na sua organização pela página de Configurações do Gateway ou por meio dos [comandos do PowerShell](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters). 

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>Como o gateway funciona?

O gateway de dados facilita a comunicação rápida e segura entre seu aplicativo lógico, o serviço de nuvem do gateway e sua fonte de dados local. O serviço de nuvem do gateway criptografa e armazena suas credenciais da fonte de dados e os detalhes do gateway. O serviço também encaminha consultas e seus resultados entre seu aplicativo lógico, o gateway de dados local e sua fonte de dados local. 

O gateway funciona com firewalls e usa apenas conexões de saída. Todo o tráfego é originado como tráfego de saída seguro do agente de gateway. O gateway retransmite dados de fontes locais em canais criptografados por meio do Barramento de Serviço do Azure. O barramento de serviço cria um canal entre o gateway e o serviço de chamada, mas não armazena nenhum dado. Todos os dados que trafegam através do gateway são criptografados.

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Estas etapas descrevem o que acontece quando um usuário na nuvem interage com um elemento que está conectado à fonte de dados local:

1. O serviço de nuvem do gateway cria uma consulta, juntamente com as credenciais criptografadas para a fonte de dados, e envia a consulta à fila para processamento pelo gateway.

2. O serviço de nuvem do gateway analisa a consulta e envia a solicitação por push ao Barramento de Serviço do Azure.

3. O gateway de dados local sonda o Barramento de Serviço do Azure para verificar solicitações pendentes.

4. O gateway obtém a consulta, descriptografa as credenciais e conecta-se à fonte de dados com essas credenciais.

5. O gateway envia a consulta à fonte de dados para execução.

6. Os resultados são enviados da fonte de dados de volta ao gateway e, em seguida, para o serviço de nuvem do gateway. O serviço de nuvem do gateway então usa os resultados.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="general"></a>Geral

**P**: Preciso de um gateway para fontes de dados na nuvem, como o Banco de Dados SQL do Azure? <br/>
**R**: Não, o gateway conecta-se apenas a fontes de dados locais.

**Pergunta**: O gateway precisa ser instalado no mesmo computador que a fonte de dados? <br/>
**R**: Não, o gateway se conecta à fonte de dados usando as informações de conexão fornecidas. Considere o gateway como um aplicativo de cliente nesse sentido. O gateway precisa apenas da capacidade de se conectar ao nome do servidor que foi fornecido.

<a name="why-azure-work-school-account"></a>

**P**: Por que eu devo usar uma conta corporativa ou de estudante para entrar? <br/>
**R**: Você somente pode usar um conta corporativa ou de estudante quando instalar o gateway de dados local. Sua conta de entrada é armazenada em um locatário gerenciado pelo Azure AD (Azure Active Directory). Geralmente, o nome UPN da conta do Azure AD corresponde ao endereço de email.

**P**: Em que local minhas credenciais são armazenadas? <br/>
**R**: As credenciais inseridas para uma fonte de dados são criptografadas e armazenadas no serviço de nuvem do gateway. As credenciais são descriptografadas no gateway de dados local.

**P**: Há algum requisito de largura de banda de rede? <br/>
**R**: Verifique se a conexão de rede tem boa taxa de transferência. Cada ambiente é diferente e a quantidade de dados enviados pode afetar os resultados. Para garantir um nível de taxa de transferência entre a fonte de dados local e os datacenters do Azure, tente usar o [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Para ajudar a medir a taxa de transferência, tente usar uma ferramenta externa como o Azure Speed Test.

**P**: Qual é a latência para execução de consultas para uma fonte de dados do gateway? Qual é a melhor arquitetura? <br/>
**R**: Para reduzir a latência de rede, instale o gateway o mais próximo possível da fonte de dados. Se você puder instalar o gateway na fonte de dados real, essa proximidade minimizará a latência introduzida. Além disso, considere a proximidade aos datacenters do Azure. Por exemplo, se o serviço usar o datacenter Oeste dos EUA e o SQL Server estiver hospedado em uma VM do Azure, a VM do Azure também deverá estar na região Oeste dos EUA. Essa proximidade minimiza a latência e evita encargos de saída na VM do Azure.

**P**: Como os resultados são enviados para a nuvem? <br/>
**R**: Os resultados são enviados por meio do Barramento de Serviço do Azure.

**P**: Existem conexões de entrada para o gateway da nuvem? <br/>
**R**: Não, o gateway usa conexões de saída para o Barramento de Serviço do Azure.

**P**: O que acontecerá se eu bloquear conexões de saída? O que preciso abrir? <br/>
**R**: Verifique as portas e os hosts que o gateway usa.

**P**: Qual é o serviço Windows que é de fato chamado? <br/>
**R**: Na guia Serviços no Gerenciador de Tarefas, o nome do serviço é "PBIEgwService" ou Power BI Enterprise Gateway Service. No console Serviços, o nome do serviço é "Serviço de gateway de dados local". O serviço Windows usa "NT SERVICE\PBIEgwService" como o SSID (SID de Serviço).

**P**: O serviço Windows do gateway pode ser executado com uma conta do Azure Active Directory? <br/>
**R**: Não, o serviço Windows deve ter uma conta do Windows válida.

### <a name="disaster-recovery"></a>Recuperação de desastre

**P**: Quais opções estão disponíveis para recuperação de desastre? <br/>
**R**: Você pode usar a chave de recuperação para restaurar ou mover um gateway. Ao instalar o gateway, especifique a chave de recuperação.

**P**: Qual é o benefício da chave de recuperação? <br/>
**R**: A chave de recuperação oferece uma maneira de migrar ou recuperar as configurações do gateway após um desastre.

## <a name="troubleshooting"></a>solução de problemas

Esta seção aborda alguns problemas comuns que você pode encontrar ao configurar e usar o gateway de dados local.

**P**: Por que minha instalação do gateway falhou? <br/>
**R**: Esse problema pode ocorrer se o software antivírus no computador de destino estiver desatualizado. Você pode atualizar ou desabilitar o software antivírus, mas somente durante a instalação do gateway e, em seguida, habilitar o software novamente.

**P**: Por que não vejo minha instalação do gateway ao criar o recurso de gateway no Azure? <br/>
**R**: Esse problema pode ocorrer pelos seguintes motivos:

* Sua instalação do gateway já foi registrada e declarada por outro recurso de gateway no Azure. Instalações do gateway não aparecem na lista de instâncias depois que os recursos de gateway são criados para elas.
Para verificar os registros de gateway no portal do Azure, examine todos os seus recursos do Azure com o tipo dos **Gateways de dados locais** para *toda as* assinaturas do Azure. 

* A identidade do Azure AD da pessoa que instalou o gateway é diferente da pessoa que entrou no portal do Azure. Verifique se você está conectado com a mesma identidade que instalou o gateway.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**P**: Em que local estão os logs do gateway? <br/>
**R**: Consulte a [seção**Logs**](#logs) mais adiante neste artigo.

**P**: Como posso ver quais consultas estão sendo enviadas à fonte de dados local? <br/>
**R**: Você pode habilitar o rastreamento de consulta, que inclui as consultas que são enviadas. Lembre-se de alterar o rastreamento de consulta de volta para o valor original quando concluir a solução de problemas. Deixar o acompanhamento de consulta ativado cria logs maiores.

Você também pode examinar ferramentas de rastreamento de consultas disponibilizadas por sua fonte de dados. Por exemplo, você pode usar o Extended Events ou o SQL Profiler para SQL Server e Analysis Services.

### <a name="outdated-gateway-version"></a>Versão do gateway desatualizada

Muitos problemas podem surgir quando a versão do gateway fica desatualizada. Como prática geral recomendada, certifique-se de ter a última versão. Se você não atualiza o gateway há um mês ou mais, convém instalar a versão mais recente do gateway e verificar se o problema pode ser reproduzido.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Erro: falha ao adicionar usuário ao grupo. (-2147463168 PBIEgwService Performance Log Users)

Você poderá obter esse erro se tentar instalar o gateway em um controlador de domínio, o que não tem suporte. Certifique-se de implantar o gateway em um computador que não seja um controlador de domínio.

<a name="logs"></a>

### <a name="logs"></a>Logs

Para ajudá-lo a solucionar problemas, sempre comece coletando e examinando os logs do gateway. Há várias maneiras de coletar os logs, mas a opção mais simples, após instalar o gateway, é por meio da interface do usuário do instalador do gateway. 

1. Em seu computador, abra o instalador do gateway de dados local.
2. No menu esquerdo, selecione **Diagnósticos**.
3. Em **logs de Gateway**, selecione **Exportar logs**.

   ![Exportar logs do instalador do gateway](./media/logic-apps-gateway-install/export-logs.png)

Aqui estão outros locais onde é possível encontrar vários logs:

| Tipo de log | Local padrão | 
|----------|----------| 
| **Logs do instalador** | %localappdata%\Temp\On-premises_data_gateway_<*yyyymmdd*>.<*number*>.log | 
| **Logs de configuração** | C:\Users\<*username*>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator<*yyyymmdd*>.<*number*>.log | 
| **Logs de serviço do gateway corporativo** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\Gateway<*yyyymmdd*>.<*number*>.log | 
||| 

**Logs de eventos**

Para localizar os logs de eventos do gateway, siga estas etapas:

1. No computador com a instalação do gateway, abra o **Visualizador de Eventos**. 
2. Expanda o **Visualizador de Eventos (Local)** > **Logs de Aplicativos e Serviços**. 
3. Selecione **Serviço de gateway de dados local**.

   ![Exibir logs de eventos do gateway](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="telemetry"></a>Telemetria

Para monitoramento adicional e solução de problemas, é possível ativar e coletar telemetria. 

1. Navegue até o local do cliente do gateway de dados local, que geralmente é possível encontrar aqui: ```C:\Program Files\On-premises data gateway```

   Caso contrário, para encontrar o local do cliente, abra o console de Serviços no mesmo computador, localize **Serviço de gateway de dados local** e exiba a propriedade **Caminho do executável**.

2. Abra este arquivo de *configuração*: **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. Altere o valor **SendTelemetry** para **true**:

   ```html
   <setting name="SendTelemetry" serializeAs="String">
      <value>true</value>
   </setting>
   ```

4. Salve as alterações e, em seguida, reinicie o serviço Windows.

### <a name="review-slow-query-performance"></a>Examinar desempenho de consulta lenta

Se você achar que as consultas são executadas lentamente por meio do gateway, é possível ativar o registro em log adicional que emite consultas e suas durações. Esses logs podem ajudá-lo a descobrir quais consultas são lentas ou de longa execução. Para ajustar o desempenho de consultas, talvez seja necessário modificar a fonte de dados, por exemplo, ajustar índices para consultas do SQL Server.

Para determinar a duração de uma consulta, siga estas etapas:

1. Navegue até o mesmo local que o cliente do gateway, que geralmente é possível encontrar aqui: ```C:\Program Files\On-premises data gateway```

   Caso contrário, para encontrar o local do cliente, abra o console de Serviços no mesmo computador, localize **Serviço de gateway de dados local** e exiba a propriedade **Caminho do executável**.

2. Abra e edite esses arquivos de configuração, conforme descrito:

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     Nesse arquivo, altere o valor **EmitQueryTraces** de **false** para **true** para que o gateway possa registrar consultas enviadas do gateway a uma fonte de dados:

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > Ativar a configuração EmitQueryTraces pode aumentar significativamente o tamanho do log com base no uso do gateway. Ao terminar a revisão dos logs, certifique-se de redefinir EmitQueryTraces para **false** novamente, em vez de deixar essa configuração em longo prazo.

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     Para que o gateway registre entradas detalhadas, incluindo as entradas que mostram a duração, altere o valor **TracingVerbosity** de **4** para **5** executando qualquer etapa: 

     * Nesse arquivo de configuração, altere o valor **TracingVerbosity** de **4** para **5** 

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * Abra o instalador do gateway, selecione **Diagnósticos**, ative **Log adicional** e, em seguida, escolha **Aplicar**:

       ![Ativar registro em log adicional](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > Ativar a configuração TracingVerbosity pode aumentar significativamente o tamanho do log com base no uso do gateway. Ao terminar a revisão dos logs, certifique-se de desativar **Registro em log adicional** no instalador do gateway ou redefinir o TracingVerbosity para **4** novamente no arquivo de configuração, em vez de deixar essa configuração em longo prazo.

3. Para descobrir a duração de uma consulta, siga estas etapas:

   1. [Exporte](#logs) e abra o log de gateway.

   2. Para localizar uma consulta, pesquise um tipo de atividade, por exemplo: 

      | Tipo de atividade | DESCRIÇÃO | 
      |---------------|-------------| 
      | MGEQ | Consultas que são executadas no ADO.NET. | 
      | MGEO | Consultas que são executadas no OLEDB. | 
      | MGEM | Consultas executadas do mecanismo Mashup. | 
      ||| 

   3. Observe o segundo GUID, que é a ID da Solicitação.

   4. Continue pesquisando o tipo de atividade até encontrar uma entrada nomeada "FireActivityCompletedSuccessfullyEvent" que tem uma duração em milissegundos. 
   Confirme se a entrada tem a mesma ID da Solicitação, por exemplo:

      ```text 
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE] 
      > A entrada "FireActivityCompletedSuccessfullyEvent" é uma entrada detalhada e não é registrada, a menos que a configuração "TracingVerbosity" esteja no nível 5.

### <a name="trace-traffic-with-fiddler"></a>Rastrear tráfego com o Fiddler

[Fiddler](http://www.telerik.com/fiddler) é uma ferramenta gratuita da Telerik que monitora o tráfego HTTP. Você pode examinar esse tráfego com o serviço do Power BI da máquina cliente. Esse serviço pode mostrar erros e outras informações relacionadas.

## <a name="next-steps"></a>Próximas etapas
    
* [Conectar-se a dados locais de aplicativos lógicos](../logic-apps/logic-apps-gateway-connection.md)
* [Recursos de integração corporativa](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Conectores de Aplicativos Lógicos do Azure](../connectors/apis-list.md)
