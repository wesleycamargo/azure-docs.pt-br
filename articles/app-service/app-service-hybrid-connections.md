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
ms.openlocfilehash: f0b148cb9c304c54b47be9601740e7634462d59b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Conexões Híbridas do Serviço de Aplicativo do Azure #

## <a name="overview"></a>Visão geral ##

Conexões Híbridas é um serviço no Azure e uma funcionalidade no Serviço de Aplicativo do Azure.  Como um serviço, ele tem uso e recursos além daqueles usados no Serviço de Aplicativo do Azure.  Para saber mais sobre Conexões Híbridas e seu uso fora do Serviço de Aplicativo do Azure, você pode começar aqui, [Conexões Híbridas de Retransmissão do Azure][HCService]

No Serviço de Aplicativo do Azure, as Conexões Híbridas podem ser usadas para acessar recursos do aplicativo em outras redes. Ele fornece acesso DE seu aplicativo PARA um ponto de extremidade do aplicativo.  Ele não permite que uma funcionalidade alternativa acesse seu aplicativo.  Conforme usado no Serviço de Aplicativo, cada conexão híbrida se correlaciona com uma única combinação de host e de porta TCP.  Isso significa que o ponto de extremidade de conexão híbrida pode estar em qualquer sistema operacional e em qualquer aplicativo, desde que você esteja atingindo uma porta de escuta TCP. As Conexões Híbridas não sabem e nem se importam com qual é o protocolo de aplicativo ou com o que você está acessando.  Elas estão simplesmente fornecendo acesso à rede.  


## <a name="how-it-works"></a>Como ele funciona ##
O recurso de Conexões Híbridas consiste em duas chamadas de saída para a Retransmissão do Barramento de Serviço.  Há uma conexão de uma biblioteca no host em que o aplicativo está sendo executado no Serviço de Aplicativo e há uma conexão do HCM (Gerenciador de Conexões Híbridas) com a Retransmissão do Barramento de Serviço.  O HCM e um serviço de retransmissão que você implanta dentro da rede que hospeda o recurso que você está tentando acessar. 

Por meio de duas conexões ingressadas, seu aplicativo tem um túnel TCP para uma combinação fixa de host:porta no outro lado do HCM.  A conexão usa a TLS 1.2 para segurança e chaves SAS para autenticação/autorização.    

![Fluxo de alto nível da conexão híbrida][1]

Quando seu aplicativo faz uma solicitação DNS que corresponde a um ponto de extremidade de Conexão Híbrida configurado, o tráfego TCP de saída será redirecionado por meio da Conexão Híbrida.  

> [!NOTE]
> Isso significa que você deve tentar sempre usar um nome DNS para sua Conexão Híbrida.  Se em vez disso o ponto de extremidade usar um endereço IP, alguns softwares cliente não farão uma pesquisa de DNS.
>
>

Há dois tipos de Conexões Híbridas: as novas Conexões Híbridas, oferecidas como um serviço na Retransmissão do Azure e as Conexões Híbridas do BizTalk mais antigas.  As Conexões híbridas do BizTalk mais antigas são denominadas Conexões Híbridas Clássicas no portal.  Há mais informações posteriormente neste documento sobre elas.

### <a name="app-service-hybrid-connection-benefits"></a>Benefícios da Conexão Híbrida do Serviço de Aplicativo ###

Há inúmeros benefícios para a funcionalidade de Conexões Híbridas, incluindo:

- Os aplicativos podem acessar sistemas e serviços locais com segurança.
- O recurso não requer um ponto de extremidade acessível pela Internet.
- É rápido e fácil de configurar. 
- Cada Conexão Híbrida corresponde a uma única combinação de host:porta, que é um excelente aspecto de segurança.
- Normalmente, ela não exige buracos de firewall, já que todas as conexões são de saída por meio de portas da Web padrão.
- Como o recurso está no nível de rede, ele independente da linguagem usada pelo seu aplicativo e da tecnologia usada pelo ponto de extremidade.
- Ele pode ser usado para fornecer acesso em várias redes de um único aplicativo. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Coisas que você não pode fazer com as Conexões Híbridas ###

Há algumas coisas que não podem ser feitas com Conexões Híbridas, incluindo:

- A montagem de uma unidade
- O uso de UDP
- Acessando serviços baseados em TCP que usam portas dinâmicas, como o Modo passivo de FTP ou o Modo passivo estendido
- suporte a LDAP, já que isso às vezes requer UDP
- suporte ao Active Directory

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>Adicionar e criar uma Conexão Híbrida em seu aplicativo ##

As Conexões Híbridas podem ser criadas por meio do aplicativo Serviço de Aplicativo no Portal do Azure ou da Retransmissão do Azure no Portal do Azure.  É recomendável que você crie Conexões Híbridas por meio do aplicativo Serviço de Aplicativo que você deseja usar com a Conexão Híbrida.  Para criar uma Conexão Híbrida, acesse o [Portal do Azure][portal] e selecione seu aplicativo.  Selecione **Rede > Configurar seus pontos de extremidade de Conexão Híbrida**.  Daqui, é possível ver as Conexões Híbridas configuradas para seu aplicativo.  

![Lista de conexões híbridas][2]

Para adicionar uma nova Conexão híbrida, clique em Adicionar Conexão Híbrida.  A interface do usuário lista as Conexões Híbridas que você já criou.  Para adicionar uma ou mais delas ao seu aplicativo, clique nas que você deseja e pressione **Adicionar Conexão Híbrida selecionada**.  

![Portal de conexão híbrida][3]

Se desejar criar uma nova Conexão Híbrida, clique em **Criar nova Conexão Híbrida**.  Daqui, especifique o: 

- Nome do ponto de extremidade
- Nome do host do ponto de extremidade
- Porta do ponto de extremidade
- O namespace do Barramento de Serviço que você deseja usar

![Criar uma Conexão Híbrida][4]

Cada Conexão Híbrida está associada a um namespace do Barramento de Serviço e cada namespace do Barramento de Serviço está em uma região do Azure.  É importante tentar usar um namespace do Barramento de Serviço na mesma região do seu aplicativo para evitar a latência de rede induzida.

Se você desejar remover sua Conexão Híbrida do seu aplicativo, clique com o botão direito do mouse nele e selecione **Desconectar**.  

Depois que uma Conexão Híbrida é adicionada ao seu aplicativo, basta clicar nela para ver seus detalhes. 

![Detalhes da Conexão Híbrida][5]

### <a name="creating-a-hybrid-connection-in-the-azure-relay-portal"></a>Criando uma Conexão Híbrida no Portal de Retransmissão do Azure ###

Além da experiência do portal de dentro do seu aplicativo, também há uma capacidade de criar Conexões Híbridas de dentro do Portal de Retransmissão do Azure. Para que uma Conexão Híbrida seja usada pelo Serviço de Aplicativo do Azure, ela deve atender a dois critérios. Ela deverá:

* Exigir autorização do cliente
* Ter um item de metadados denominado ponto de extremidade que contém a combinação de host:porta como o valor

## <a name="hybrid-connections-and-app-service-plans"></a>Conexões Híbridas e Planos de Serviço de Aplicativo ##

As Conexões Híbridas estão disponíveis apenas nos SKUs Básico, Standard, Premium e de preço isolado.  Há limites vinculados ao plano de preços.  

> [!NOTE] 
> É possível criar apenas novas Conexões Híbridas com base na Retransmissão do Azure. Não é possível criar novas Conexões Híbridas do BizTalk.
>

| Plano de Preços | Número de Conexões Híbridas utilizáveis no plano |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolado | 200 |

Já que há restrições do Plano do Serviço de Aplicativo, também há uma interface do usuário no Plano do Serviço de Aplicativo que mostra quantas Conexões Híbridas estão sendo usadas e por quais aplicativos.  

![Propriedades do nível do plano de Serviço de Aplicativo][6]

É possível ver detalhes sobre sua Conexão Híbrida clicando nela.  Nas propriedades mostradas aqui, é possível ver todas as informações que você viu no modo de exibição do aplicativo e também ver quantos outros aplicativos no mesmo Plano de Serviço de Aplicativo estão usando essa Conexão Híbrida.

Embora haja um limite no número de pontos de extremidade de Conexão Híbrida que podem ser usados em um Plano de Serviço de Aplicativo, cada Conexão Híbrida usada pode ser usada entre inúmeros aplicativos nesse Plano de Serviço de Aplicativo.  Em outras palavras, uma única Conexão Híbrida usada em cinco aplicativos separados em um Plano de Serviço de Aplicativo conta como uma Conexão Híbrida.

Há um custo adicional para usar Conexões Híbridas.  Para obter detalhes sobre preços da Conexão Híbrida, acesse aqui: [Preços do Barramento de Serviço][sbpricing].

## <a name="hybrid-connection-manager"></a>Gerenciador de Conexão Híbrida ##

Para que as Conexões Híbridas funcionem, é necessário um agente de retransmissão na rede que hospeda o seu ponto de extremidade de Conexão Híbrida.  Esse agente de retransmissão é chamado de HCM (Gerenciador de Conexão Híbrida).  Essa ferramenta pode ser baixada da interface do usuário **Rede > Configurar seus pontos de extremidade de Conexão Híbrida** disponível no seu aplicativo no [Portal do Azure][portal].  

Essa ferramenta é executada no Windows Server 2012 e versões posteriores do Windows.  Uma vez instalado, o HCM executa como um serviço.  Esse serviço conecta-se à Retransmissão do Barramento de Serviço do Azure baseada nos pontos de extremidade configurados.  As saída das conexões provenientes do HCM destinam-se ao Azure pela porta 443.    

A HCM tem uma interface do usuário para configurá-la.  Após o HCM ser instalado, será possível exibir a interface do usuário executando o HybridConnectionManagerUi.exe que fica no diretório de instalação do Gerenciador de Conexões Híbridas.  Ela também pode ser facilmente acessada no Windows 10 digitando *Interface do usuário do Gerenciador de Conexões Híbridas* na caixa de pesquisa.  

![Portal de Conexão Híbrida][7]

Quando a interface do usuário do HCM é iniciada, a primeira coisa que você vê é uma tabela que lista todas as Conexões Híbridas configuradas com esta instância do HCM.  Se você desejar fazer alterações, será necessário autenticar-se com o Azure. 

Para adicionar uma ou mais Conexões Híbridas a seu HCM:

1. Inicie a interface do usuário do HCM
1. Clique em Configurar outra Conexão Híbrida ![Adicionar uma HC no HCM][8]

1. Entre com sua conta do Azure
1. Escolha uma assinatura
1. Clique nas Conexões Híbridas que você deseja que o HCM retransmita ![Selecione uma HC][9]

1. Clique em Salvar

Neste ponto, você verá as Conexões Híbridas que você adicionou.  Também é possível clicar na Conexão Híbrida configurada e ver detalhes sobre ela.

![Detalhes da HC][10]

Para que seu HCM possa dar suporte às Conexões Híbridas com as quais ele está configurado, é necessário:

- Acesso TCP ao Azure nas portas 80 e 443
- Acesso TCP ao ponto de extremidade da Conexão Híbrida
- Capacidade de fazer pesquisas de DNS no host do ponto de extremidade e no namespace do Barramento de Serviço do Azure

O HCM dá suporte tanto a novas Conexões Híbridas quanto às Conexões Híbridas do BizTalk mais antigas.

> [!NOTE]
> A Retransmissão do Azure depende de soquetes da Web para ter conectividade. Essa funcionalidade só está disponível no Windows Server 2012 ou mais recente. Por isso, o Gerenciador de Conexões Híbridas não é compatível com nada anterior ao Windows Server 2012.
>

### <a name="redundancy"></a>Redundância ###

Cada HCM pode ser compatível com várias Conexões Híbridas.  Além disso, qualquer Conexão Híbrida determinada pode ser compatível com vários HCMs.  O comportamento padrão é transferir o tráfego pelo método round robin pelos HCMs configurados para qualquer determinado ponto de extremidade.  Se desejar alta disponibilidade nas Conexões Híbridas da sua rede, basta criar uma instância de vários HCMs em computadores separados. 

### <a name="manually-adding-a-hybrid-connection"></a>Adicionando uma Conexão Híbrida manualmente ###

Se desejar que alguém fora da sua assinatura hospede uma instância HCM para uma determinada Conexão Híbrida, será possível compartilhar com eles a cadeia de conexão de gateway para a Conexão Híbrida.  É possível ver isso nas propriedades de uma Conexão Híbrida no [Portal do Azure][portal]. Para usar essa cadeia de caracteres, clique no botão **Inserir manualmente** no HCM e cole-o na cadeia de conexão de gateway.


## <a name="troubleshooting"></a>Solucionar problemas ##

O status de conexão para uma Conexão Híbrida significa que pelo menos um HCM é configurado com essa Conexão Híbrida e é capaz de alcançar o Azure.  Se o status da sua Conexão Híbrida não indica **Conectado**, então sua Conexão Híbrida não está configurada em nenhum HCM com acesso ao Azure.

O principal motivo pelo qual os clientes não podem se conectar ao ponto de extremidade é porque o ponto de extremidade foi especificado usando um endereço IP em vez de um nome DNS.  Se seu aplicativo não puder acessar o ponto de extremidade desejado e você tiver usado um endereço IP, mude e passe a usar um nome DNS válido no host em que o HCM está em execução.  Outros itens a serem verificados são que o nome DNS seja resolvido corretamente no host em que o HCM está em execução e que haja conectividade do host em que o HCM está em execução com o ponto de extremidade de Conexão Híbrida.  

Há uma ferramenta no Serviço de Aplicativo que pode ser invocada do console de Ferramentas avançadas (Kudu) chamado tcpping.  Essa ferramenta pode informar se você tem acesso a um ponto de extremidade TCP, mas ela não diz se você tem acesso a um ponto de extremidade de Conexão Híbrida.  Quando usado no console em relação a um ponto de extremidade de Conexão Híbrida, um ping bem-sucedido apenas informará que você tem uma Conexão Híbrida configurada para o aplicativo que usa essa combinação de host:porta.  

## <a name="biztalk-hybrid-connections"></a>Conexões Híbridas do BizTalk ##

A funcionalidade mais antiga das Conexões Híbridas do BizTalk foi encerrada para novas Conexões Híbridas do BizTalk.  É possível continuar usando suas Conexões Híbridas do BizTalk existentes com seus aplicativos, mas elas devem ser migradas para as novas Conexões Híbridas que usam a Retransmissão do Azure.  Entre os benefícios no novo serviço em relação à versão do BizTalk, estão:

- Não é necessária nenhuma conta adicional do BizTalk.
- O TLS é 1.2 em vez de 1.0 como nas Conexões Híbridas do BizTalk.
- A comunicação é pelas portas 80 e 443 e usa um nome DNS para acessar o Azure, em vez de usar endereços IP e uma variedade de outras portas.  

Para adicionar uma Conexão Híbrida do BizTalk existente ao seu aplicativo, acesse seu aplicativo no [Portal do Azure][portal] e clique em **Rede > Configurar seus pontos de extremidade de Conexão Híbrida**.  Na tabela de Conexões Híbridas clássicas, clique em **Adicionar Conexão Híbrida clássica**.  Daqui, você verá uma lista de suas Conexões Híbridas do BizTalk.  


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
