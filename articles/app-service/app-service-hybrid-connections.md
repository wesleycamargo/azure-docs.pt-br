---
title: "Conexões Híbridas do Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Como criar e usar Conexões Híbridas para acessar recursos em redes diferentes"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: ccompy
ms.openlocfilehash: 677642e4e97523ed71ff5857ae27263743dca535
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Conexões Híbridas do Serviço de Aplicativo do Azure #

Conexões Híbridas é um serviço no Azure e uma funcionalidade no Serviço de Aplicativo do Azure. Como um serviço, ele tem uso e recursos além daqueles usados no Serviço de Aplicativo do Azure. Para saber mais sobre Conexões Híbridas e seu uso fora do Serviço de Aplicativo do Azure, confira [Conexões Híbridas da Retransmissão do Azure][HCService].

Com o Serviço de Aplicativo, as Conexões Híbridas podem ser usadas para acessar recursos do aplicativo em outras redes. Ele fornece acesso de seu aplicativo para um ponto de extremidade do aplicativo. Ele não permite que uma funcionalidade alternativa acesse seu aplicativo. Conforme usado no Serviço de Aplicativo, cada conexão híbrida se correlaciona com uma única combinação de host e de porta TCP. Isso significa que o ponto de extremidade de Conexões Híbridas pode estar em qualquer sistema operacional e em qualquer aplicativo, desde que você esteja acessando uma porta de escuta TCP. O recurso Conexões Híbridas não sabe e nem se importa com o protocolo de aplicativo ou o que você está acessando. Ele simplesmente fornece acesso à rede.  


## <a name="how-it-works"></a>Como ele funciona ##
O recurso Conexões Híbridas consiste em duas chamadas de saída para a Retransmissão do Barramento de Serviço do Azure. Há uma conexão de uma biblioteca no host em que seu aplicativo está em execução no Serviço de Aplicativo. Também há uma conexão do HCM (Gerenciador de Conexões Híbridas) para a Retransmissão do Barramento de Serviço. O HCM e um serviço de retransmissão que você implanta dentro da rede que hospeda o recurso que você está tentando acessar. 

Por meio de duas conexões ingressadas, seu aplicativo tem um túnel TCP para uma combinação fixa de host:porta no outro lado do HCM. A conexão usa o protocolo TLS 1.2 para segurança e as chaves de SAS (Assinatura de Acesso Compartilhado) para autenticação e autorização.    

![Diagrama de alto nível da Conexão Híbrida][1]

Quando seu aplicativo faz uma solicitação DNS que corresponde a um ponto de extremidade de Conexão Híbrida configurado, o tráfego TCP de saída será redirecionado por meio da Conexão Híbrida.  

> [!NOTE]
> Isso significa que você deve tentar sempre usar um nome DNS para sua Conexão Híbrida. Se em vez disso o ponto de extremidade usar um endereço IP, alguns softwares cliente não farão uma pesquisa de DNS.
>
>

O recurso Conexões Híbridas tem dois tipos: as Conexões Híbridas que são oferecidas como um serviço da Retransmissão do Barramento de Serviço e, as mais antigas, Conexões Híbridas dos serviços BizTalk. Estas são denominadas Conexões Híbridas clássicas no portal. Há outras informações sobre elas mais adiante neste artigo.

### <a name="app-service-hybrid-connection-benefits"></a>Benefícios da Conexão Híbrida do Serviço de Aplicativo ###

Há inúmeros benefícios para a funcionalidade de Conexões Híbridas, incluindo:

- Os aplicativos podem acessar sistemas e serviços locais com segurança.
- O recurso não requer um ponto de extremidade acessível pela Internet.
- É rápido e fácil de configurar. 
- Cada Conexão Híbrida corresponde a uma única combinação de host:porta, que é útil para segurança.
- Ele geralmente não exigem aberturas do firewall. As conexões são todas de saídas por meio de portas padrão da Web.
- Como o recurso está no nível de rede, ele independente da linguagem usada pelo seu aplicativo e da tecnologia usada pelo ponto de extremidade.
- Ele pode ser usado para fornecer acesso em várias redes de um único aplicativo. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Coisas que você não pode fazer com as Conexões Híbridas ###

Há algumas coisas que não podem ser feitas com Conexões Híbridas, incluindo:

- A montagem de uma unidade.
- O uso de UDP.
- O acesso a serviços baseados em TCP que usam portas dinâmicas, como o Modo passivo de FTP ou o Modo passivo estendido.
- Suporte LDAP, porque às vezes ele requer UDP.
- Suporte ao Active Directory.

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Adicionar e criar Conexões Híbridas em seu aplicativo ##

Você pode criar Conexões Híbridas por meio do aplicativo do Serviço de Aplicativo no portal do Azure ou da Retransmissão do Azure no Portal do Azure. Recomendamos que você crie Conexões Híbridas por meio do aplicativo Serviço de Aplicativo que você deseja usar com a Conexão Híbrida. Para criar uma Conexão Híbrida, acesse o [Portal do Azure][portal] e selecione seu aplicativo. Selecione **Rede** > **Configurar seus pontos de extremidade de Conexão Híbrida**. Daqui, é possível ver as Conexões Híbridas configuradas para seu aplicativo.  

![Captura de tela da lista de Conexões Híbridas][2]

Para adicionar uma nova Conexão Híbrida, clique em **Adicionar conexão híbrida**.  Você verá uma lista das Conexões Híbridas criadas. Para adicionar uma ou mais delas ao seu aplicativo, selecione as que você deseja e selecione **Adicionar Conexão Híbrida selecionada**.  

![Captura de tela do portal de Conexões Híbridas][3]

Se desejar criar uma nova Conexão Híbrida, clique em **Criar nova conexão híbrida**. Especifique: 

- Nome do ponto de extremidade.
- Nome do host do ponto de extremidade.
- Porta do ponto de extremidade.
- O namespace do Barramento de Serviço que você deseja usar.

![Captura de tela da caixa de diálogo Criar nova conexão híbrida][4]

Cada Conexão Híbrida está associada a um namespace do Barramento de Serviço e cada namespace do Barramento de Serviço está em uma região do Azure. É importante tentar usar um namespace do Barramento de Serviço na mesma região do seu aplicativo para evitar a latência de rede induzida.

Se você desejar remover sua Conexão Híbrida do seu aplicativo, clique com o botão direito do mouse nele e selecione **Desconectar**.  

Depois que uma Conexão Híbrida é adicionada ao seu aplicativo, basta clicar nela para ver seus detalhes. 

![Captura de tela dos detalhes das Conexões Híbridas][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Criar uma Conexão Híbrida no portal da Retransmissão do Azure ###

Além da experiência do portal de dentro do seu aplicativo, você também pode criar Conexões Híbridas no portal da Retransmissão do Azure. Para uma Conexão Híbrida ser usada pelo Serviço de Aplicativo, ele deverá:

* Exigir autorização do cliente.
* Ter um item de metadados denominado ponto de extremidade que contém a combinação de host:porta como o valor.

## <a name="hybrid-connections-and-app-service-plans"></a>Conexões Híbridas e Planos do Serviço de Aplicativo ##

O recurso Conexões Híbridas está disponível apenas nos SKUs Básico, Standard, Premium e de preço isolado. Há limites vinculados ao plano de preços.  

> [!NOTE] 
> É possível criar apenas novas Conexões Híbridas com base na Retransmissão do Azure. Não é possível criar novas Conexões Híbridas do BizTalk.
>

| Plano de preços | Número de Conexões Híbridas utilizáveis no plano |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolado | 200 |

Observe que o Plano do Serviço de Aplicativo mostra quantas Conexões Híbridas estão sendo usadas e por quais aplicativos.  

![Captura de tela das propriedades do Plano do Serviço de Aplicativo][6]

Selecione a Conexão Híbrida para ver os detalhes. Você pode ver todas as informações exibidas no modo de exibição do aplicativo. Você também pode ver quantos outros aplicativos no mesmo plano estão usando essa Conexão Híbrida.

Há um limite no número de pontos de extremidade de Conexão Híbrida que pode ser usado em um Plano do Serviço de Aplicativo. Cada Conexão Híbrida usada, no entanto, pode ser usado em qualquer quantidade de aplicativos no plano. Por exemplo, uma única Conexão Híbrida usada em cinco aplicativos separados em um Plano do Serviço de Aplicativo conta como uma Conexão Híbrida.

Há um custo adicional para usar Conexões Híbridas. Para obter mais detalhes, confira os[preços do Service Bus ][sbpricing].

## <a name="hybrid-connection-manager"></a>Gerenciador de Conexão Híbrida ##

O recurso Conexões Híbridas exige um agente de retransmissão na rede que hospeda o seu ponto de extremidade de Conexão Híbrida. Esse agente de retransmissão é chamado de HCM (Gerenciador de Conexão Híbrida). Parabaixar o HCM, no seu aplicativo no [portal do Azure][portal], selecione **Rede** > **Configurar seus pontos de extremidade de Conexões Híbridas**.  

Essa ferramenta é executada no Windows Server 2012 e versões posteriores. Quando instalado, o HCM é executado como um serviço que se conecta à Retransmissão do Barramento de Serviço com base nos pontos de extremidade configurados. As saída das conexões provenientes do HCM destinam-se ao Azure pela porta 443.    

Depois de instalar o HCM, você pode executar HybridConnectionManagerUi.exe para usar a interface do usuário da ferramenta. Esse arquivo está no diretório de instalação do Gerenciador de Conexões Híbridas. No Windows 10, você também pode procurar por *Interface do usuário do Gerenciador de Conexões Híbridas* na sua caixa de pesquisa.  

![Captura de tela do Gerenciador de Conexões Híbridas][7]

Ao iniciar a interface do usuário do HCM, a primeira coisa que você vê é uma tabela que lista todas as Conexões Híbridas configuradas com esta instância do HCM. Se você quiser fazer alterações, faça autenticação no Azure primeiro. 

Para adicionar uma ou mais Conexões Híbridas a seu HCM:

1. Inicie a interface do usuário do HCM.
1. Selecione **Configurar outra Conexão Híbrida**.
![Captura de tela de Configurar novas Conexões Híbridas][8]

1. Faça logon usando sua conta do Azure.
1. Escolha uma assinatura.
1. Selecione as Conexões Híbridas que você deseja que o HCM retransmita.
![Captura de tela de Conexões Híbridas][9]

1. Selecione **Salvar**.

Agora você pode ver as Conexões Híbridas adicionadas. Você também pode selecionar a Conexão híbrida configurada para ver os detalhes.

![Captura de tela de detalhes de Conexão Híbrida][10]

Para dar suporte às Conexões Híbridas com que ele está configurado, o HCM requer:

- Acesso TCP ao Azure nas portas 80 e 443.
- Acesso TCP ao ponto de extremidade da Conexão Híbrida.
- A capacidade de fazer pesquisas de DNS no host do ponto de extremidade e no namespace do Barramento de Serviço.

O HCM dá suporte a novas Conexões Híbridas e a conexões Híbridas do BizTalk.

> [!NOTE]
> A Retransmissão do Azure depende de soquetes da Web para ter conectividade. Essa funcionalidade só está disponível no Windows Server 2012 ou versão posterior. Por isso, o HCM não tem suporte em nada anterior ao Windows Server 2012.
>

### <a name="redundancy"></a>Redundância ###

Cada HCM pode ser compatível com várias Conexões Híbridas. Além disso, qualquer Conexão Híbrida determinada pode ser compatível com vários HCMs. O comportamento padrão é direcionar o tráfego pelos HCMs configurados para qualquer ponto de extremidade. Se desejar alta disponibilidade nas Conexões Híbridas da sua rede, execute vários HCMs em computadores separados. 

### <a name="manually-add-a-hybrid-connection"></a>Adicionar uma Conexão Híbrida manualmente ###

Para permitir que alguém fora da sua assinatura hospede uma instância HCM para uma determinada Conexão Híbrida, compartilhe a cadeia de conexão de gateway para a Conexão Híbrida. É possível ver isso nas propriedades de uma Conexão Híbrida no [Portal do Azure][portal]. Para usar essa cadeia de caracteres, selecione **Inserir Manualmente** no HCM e cole-o na cadeia de conexão de gateway.


## <a name="troubleshooting"></a>Solucionar problemas ##

O status “Conectado” significa que pelo menos um HCM está configurado com essa Conexão Híbrida e é capaz de alcançar o Azure. Se o status da sua Conexão Híbrida não indica **Conectado**, sua Conexão Híbrida não está configurada em nenhum HCM com acesso ao Azure.

O principal motivo pelo qual os clientes não podem se conectar ao ponto de extremidade é porque o ponto de extremidade foi especificado usando um endereço IP em vez de um nome DNS. Se seu aplicativo não puder acessar o ponto de extremidade desejado e você tiver usado um endereço IP, mude e passe a usar um nome DNS válido no host em que o HCM está em execução. Verifique também se o nome DNS resolve corretamente no host onde o HCM está em execução. Confirme se há conectividade entre o host onde a HCM está em execução no ponto de extremidade de Conexão Híbrida.  

No Serviço de Aplicativo, a ferramenta tcpping pode ser invocada do console Ferramentas Avançadas (Kudu). Essa ferramenta pode informar se você tem acesso a um ponto de extremidade TCP, mas ela não diz se você tem acesso a um ponto de extremidade de Conexão Híbrida. Quando você usar a ferramenta no console em relação a um ponto de extremidade de Conexão Híbrida, apenas confirmará que ele usa uma combinação host:porta.  

## <a name="biztalk-hybrid-connections"></a>Conexões Híbridas do BizTalk ##

A funcionalidade mais antiga das Conexões Híbridas do BizTalk foi encerrada para novas Conexões Híbridas do BizTalk. É possível continuar usando suas Conexões Híbridas do BizTalk existentes com seus aplicativos, mas você deve migrar para as novas Conexões Híbridas que usam a Retransmissão do Azure. Entre os benefícios no novo serviço em relação à versão do BizTalk, estão:

- Não é necessária nenhuma conta adicional do BizTalk.
- O TLS é a versão 1.2 em vez da versão 1.0.
- A comunicação é pelas portas 80 e 443 e usa um nome DNS para acessar o Azure, em vez de usar endereços IP e uma variedade de outras portas.  

Para adicionar uma Conexão Híbrida do BizTalk existente ao seu aplicativo, acesse seu aplicativo no [Portal do Azure][portal] e selecione **Rede** > **Configurar seus pontos de extremidade de Conexão Híbrida**. Na tabela de Conexões Híbridas clássicas, selecione **Adicionar Conexão Híbrida clássica**. Você pode ver uma lista de suas Conexões Híbridas do BizTalk.  


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/
