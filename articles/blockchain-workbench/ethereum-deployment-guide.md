---
title: Modelo de solução de prova de trabalho Ethereum Consortium
description: Usar o modelo de solução de prova de trabalho Etherereum Consortium para implantar e configurar uma rede Ethereum Consortium de vários membros
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 0ea9bd2c7d23aa227e62858983e7170b771751da
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658816"
---
# <a name="ethereum-proof-of-work-consortium-solution-template"></a>Modelo de solução de prova de trabalho Ethereum Consortium

O modelo de solução de prova de trabalho Ethereum Consortium foi projetado para tornar mais fácil e rápido a implantação e configuração de uma rede Ethereum Consortium de vários membros com o mínimo de conhecimento do Azure e Ethereum.

Com algumas entradas de usuário e uma implantação de clique simples por meio do portal do Azure, cada membro pode provisionar sua superfície da rede, usando os serviços de computação, rede e armazenamento do Microsoft Azure em todo o mundo. A superfície de rede de cada membro consiste em um conjunto de nós de transação com balanceamento de carga com o qual um aplicativo ou o usuário pode interagir para enviar transações, um conjunto de nós de mineração para transações de registro e um gateway de VPN. Uma etapa de conexão subsequente conecta os gateways para criar uma rede blockchain de vários membros totalmente configurada.

## <a name="about-blockchain"></a>Sobre blockchain

Para aqueles que são novos na comunidade de blockchain, o lançamento dessa solução é uma grande oportunidade para saber mais sobre a tecnologia de uma maneira fácil e configurável no Azure. No entanto, para começar, é recomendável implantar a topologia de rede Ethereum autônomo mais simples com este passo a passo guiado, antes da criação de redes consortium de vários membros.

### <a name="mining-node-details"></a>Detalhes do nó de mineração

Os nós que exploram as transações são separados de nós que aceitam transações para garantir que as duas ações não estejam competindo pelos mesmos recursos.

Um membro de consortium pode provisionar até cinco regiões que contenham um ou mais nós de mineração, com o apoio de um disco gerenciado. Um ou mais nós na região são configurados como um nó de inicialização para oferecer suporte a descoberta dinâmica de nós na rede. Os nós de mineração se comunicam com outros nós de mineração para chegar a consenso sobre o estado da razão distribuído subjacente. Não é necessário que seu aplicativo esteja ciente ou se comunique com esses nós. Ao concentrar-se em redes privadas, os nós de mineração são isolados do tráfego da internet pública de entrada para adicionar uma camada secundária de proteção. O tráfego de saída é permitido, mas não para a porta de descoberta Ethereum.

Todos os nós têm uma versão estável do cliente Go Ethereum (Geth) e são configurados para serem nós de mineração. Se você não forneceu um bloco gênese personalizado, todos os nós usarão o mesmo endereço Ethereum e um par de chaves que é protegido por senha de conta Ethereum. A senha Ethereum fornecida é usada para gerar a conta padrão (coinbase) para cada nó de mineração. Como nós de mineração, exploram e coletam taxas que são adicionadas a esta conta.

O número de nós de mineração por membro consortium depende do tamanho geral da rede desejado e a quantidade de energia hash dedicada para cada membro. Quanto maior a rede, mais nós precisam ser comprometidos para obter uma vantagem desleal. O modelo dá suporte a até 15 nós de mineração por região provisionados usando conjuntos de dimensionamento de máquina virtual.

### <a name="transaction-node-details"></a>Detalhes do nó de transação

Um membro consortium também tem um conjunto de nós de transação com balanceamento de carga. Esses nós são acessíveis de fora da rede virtual, para que os aplicativos possam usar esses nós para enviar transações ou executar contratos inteligentes dentro da rede blockchain. Todos os nós têm uma versão estável do cliente Go Ethereum (Geth) e são configurados para manter uma cópia completa do razão distribuído. Se não for fornecido um bloco gênese personalizado, esses nós de usarão a mesma conta Ethereum, protegida por senha de conta Ethereum.

Os nós de transação são com balanceamento de carga dentro de uma conjunto de disponibilidade para manter a alta disponibilidade. O modelo dá suporte a até cinco nós de transação provisionados usando conjuntos de dimensionamento de máquina virtual.

### <a name="log-analytics-details"></a>Detalhes do Log Analytics

Cada implantação também cria uma nova instância do Log Analytics ou pode ingressar em uma instância existente. Isso permite o monitoramento de várias métricas de desempenho de cada máquina virtual que compõe a rede implantada.

## <a name="deployment-architecture"></a>Arquitetura de implantação

### <a name="description"></a>DESCRIÇÃO

Este modelo de solução pode implantar a rede consortium Ethereum de vários membros baseadas em uma ou várias regiões. A rede virtual de cada região está conectada a outra região em uma topologia de cadeia usando os recursos de conexão e gateways de VNET. Ela também fornece um registrador, que contém as informações necessárias de todos os nós de mineração e transação implantados em cada região.

### <a name="standalone-and-consortium-leader-overview"></a>Visão geral de preenchimento autônomo e consortium

![Visão geral de preenchimento autônomo e consortium](./media/ethereum-deployment-guide/leader-overview.png)

### <a name="joining-consortium-member-overview"></a>Visão geral do ingresso de membro consortium

![Visão geral do ingresso de membro consortium](./media/ethereum-deployment-guide/member-overview.png)

## <a name="getting-started"></a>Introdução

Esse processo requer uma assinatura do Azure que pode suportar a implantação de vários discos gerenciados e conjuntos de dimensionamento de máquinas virtuais. Se necessário, crie uma conta gratuita do Azure para começar.

Quando uma assinatura for protegida, vá para o portal do Azure. Selecione **+ Criar um recurso**, Marketplace (Ver todos) e procure **Prova de trabalho Consortium Ethereum**.

A implantação de modelo irá orientá-lo a configurar o volume do primeiro membro na rede. O fluxo de implantação é dividido em cinco etapas: Noções básicas, Operations Management Suite, Regiões de implantação, Tamanho de rede e desempenho, Configurações de Ethereum.

### <a name="basics"></a>Noções básicas

Em **Noções básicas de**, especifique os valores para os parâmetros padrão para qualquer tipo de implantação, como assinatura, o grupo de recursos e propriedades básicas da máquina virtual.

![Noções básicas](./media/ethereum-deployment-guide/sample-deployment.png)

Nome do Parâmetro|DESCRIÇÃO| Valores Permitidos|Valores padrão
---|---|---|---
Criar uma nova rede ou ingressar em uma rede existente?|Criar uma nova rede ou ingressar em uma rede de consortium pré-existente|Criar Nova Ingressar em Existente|Criar Novo
Implantar uma rede que fará parte de um consórcio?|Uma rede de consortium permite implantações futuras para ingressar nesta rede (visível quando *Criar novo* estiver selecionado acima)|Consortium autônomo|Autônomo
Prefixo de recursos |Cadeia de caracteres usada como base para nomear recursos (2 a 4 caracteres alfanuméricos). Um hash exclusivo é acrescentado à cadeia de caracteres para alguns recursos, enquanto informações específicas do recurso são anexadas.|Caracteres alfanuméricos com comprimento de 2 a 4|ND
Nome de usuário da VM| Nome de usuário do administrador de cada VM implantada (somente caracteres alfanuméricos)|1 a 64 caracteres |gethadmin
Tipo de autenticação.|O método para autenticar para a máquina virtual. |Senha ou chave pública SSH|Senha
Senha (Tipo de Autenticação = Senha)|A senha para a conta de administrador para cada uma das máquinas virtuais implantadas. A senha deve ter três dos seguintes requisitos: um caractere maiúsculo, um caractere minúsculo, um número e um caractere especial. <br />Embora todas as máquinas virtuais tenham inicialmente a mesma senha, você pode alterar a senha após o provisionamento.|12 a 72 caracteres|ND
Chave SSH (Tipo de autenticação = Chave pública)|A chave do secure shell usada para logon remoto.|| ND
Assinatura| A assinatura para a qual deseja implantar a rede consortium||ND
Grupo de recursos| O grupo de recursos para a qual deseja implantar a rede consortium.||ND
Local padrão| A região do Azure para o grupo de recursos. ||ND



### <a name="operations-management-suite"></a>Operations Management Suite

A folha Operations Management Suite (OMS) permite que você configure um recurso do OMS para sua rede. OMS coletará e revestirá métricas úteis e logs de sua rede, fornecendo a capacidade para verificar rapidamente a integridade da rede ou depurar problemas. A oferta gratuita do OMS falhará depois que a capacidade for atingida.

![Criar nova OMS](./media/ethereum-deployment-guide/new-oms.png)

Nome do Parâmetro|DESCRIÇÃO| Valores Permitidos|Valores padrão
---|---|---|---
Conectar-se a OMS existente|Criar uma nova instância do Log Analytics ou ingressar em uma instância existente|Criar Nova Ingressar em Existente|Criar novo local do Log Analytics|A região em que o novo Log Analytics será implantado (visível se *Criar novo* estiver selecionado)
ID do espaço de trabalho OMS existente|ID do espaço de trabalho da instância existente (visível se *Ingressar em existente* estiver selecionado) camada de serviço do OMS|Escolha o tipo de preço para a nova instância. Para obter mais informações em https://azure.microsoft.com/pricing/details/log-analytics/ (visível se *Ingressar em existente* estiver selecionado)|Autônomo gratuito por nó|Grátis
Chave primária do OMS existente|A chave primária usada para conectar-se à instância do OMS existente (visível se *Ingressar em existente* estiver selecionado)

### <a name="deployment-regions"></a>Regiões de implantação

Em seguida, em **Regiões de implantação**, especificar entradas para **Número de regiões** para implantar a rede consortium e seleção de regiões do Azure com base no número de regiões fornecido. O usuário pode implantar no máximo cinco regiões.

![Regiões de implantação](./media/ethereum-deployment-guide/deployment-regions.png)

Nome do Parâmetro| DESCRIÇÃO| Valores Permitidos |Valores padrão
---|---|---|---
Número de regiões| Número de regiões para implantar a rede consortium|1, 2, 3, 4, 5| 2
Primeira região| Primeira região para implantar a rede consortium|Todas as regiões permitidas do Azure| Oeste dos EUA
Segunda região |Segunda região para implantar a rede consortium (visível somente quando o número de regiões é selecionado como 2)|Todas as regiões permitidas do Azure| Leste dos EUA
Terceira região| Terceira região para implantar a rede consortium (visível somente quando o número de regiões é selecionado como 3)|Todas as regiões permitidas do Azure| Centro dos EUA
Quarta região| Quarta região para implantar a rede consortium (visível somente quando o número de regiões é selecionado como 4)|Todas as regiões permitidas do Azure| Leste dos EUA 2
Quinta região| Quinta região para implantar a rede consortium (visível somente quando o número de regiões é selecionado como 5)|Todas as regiões permitidas do Azure| Oeste dos EUA 2

### <a name="network-size-and-performance"></a>Tamanho e desempenho da rede

Em seguida, em **Tamanho e desempenho de rede** especifique as entradas para o tamanho da rede consortium, como número e tamanho de nós de mineração e de transação.

![Tamanho e desempenho da rede](./media/ethereum-deployment-guide/network-size-performance.png)

Nome do Parâmetro |DESCRIÇÃO |Valores Permitidos| Valores padrão
---|---|---|---
Número de nós de mineração|O número de nós de mineração implantados por região|2 - 15| 2
Desempenho do armazenamento de nó de mineração|O tipo de disco gerenciado de suporte de cada um de nós de mineração implantado.|Standard ou Premium|Standard
Tamanho da máquina virtual do nó de mineração|O tamanho de máquina virtual usado para nós de mineração.|A Padrão, <br />D Padrão, <br />D-v2 Padrão, <br />Série F Padrão, <br />DS Padrão, <br />e FS Padrão|D1v2 Padrão
Número de nós de transação com balanceamento de carga|O número de nós de transação para provisionar como parte da rede.|1 - 5| 2
Desempenho do armazenamento de nó de transação|O tipo de disco gerenciado de suporte de cada um de nós de transação implantado.|Standard ou Premium|Standard
Tamanho da máquina virtual do nó de transação|O tamanho de máquina virtual usado para nós de transação.|A Padrão, <br />D Padrão, <br />D-v2 Padrão, <br />Série F Padrão, <br />DS Padrão, <br />e FS Padrão|D1v2 Padrão

### <a name="ethereum-settings"></a>Configurações de Ethereum

Em seguida, em **Configurações de Ethereum**, especifique as configurações Ethereum relacionadas, como a ID de rede e bloco gênese ou senha da conta Ethereum.

![Configurações de Ethereum](./media/ethereum-deployment-guide/standalone-leader-deployment.png)

Nome do Parâmetro |DESCRIÇÃO |Valores Permitidos|Valores padrão
---|---|---|---
ID de ConsortiumMember|A ID associada a cada membro que está participando da rede consortium usada para configurar os espaços de endereço IP para evitar a colisão. <br /><br />A ID do membro deve ser exclusiva em diferentes organizações na mesma rede. É necessária uma ID de membro exclusiva mesmo quando a mesma organização implanta em várias regiões.<br /><br />Anote o valor desse parâmetro, pois você precisará compartilhá-lo com outros membros que ingressam.|0 - 255
ID de rede Ethereum|A ID de rede para a rede de Ethereum consortium que está sendo implantada. Cada rede Ethereum tem sua própria ID de rede, sendo que 1 indica a ID para a rede pública. Embora o acesso à rede seja restrito para nós de mineração, ainda é recomendável usar um número grande para evitar colisões.|5 - 999.999.999| 10101010
Bloco gênese personalizado|Opção para gerar um bloco gênese ou fornecer um personalizado automaticamente.|Sim/Não| Não 
Senha da conta Ethereum (bloco gênese personalizado = não)|A senha de administrador usada para proteger a conta Ethereum importada para cada nó. A senha deve conter o seguinte: 1 caractere maiúsculo, 1 caractere minúsculo e 1 número.|12 ou mais caracteres|ND
Senha da chave privada Ethereum (bloco gênese personalizado = não)|A senha usada para gerar a chave privada de ECC associada à conta de Ethereum padrão que é gerada. Uma chave privada gerada previamente não precisa ser passada explicitamente.<br /><br />Considere uma frase secreta com aleatoriedade suficiente para garantir uma chave privada forte e nenhuma sobreposição com outros membros do consortium. A senha deve conter o seguinte no mínimo: 1 caractere maiúsculo, 1 caractere minúsculo e 1 número.<br /><br />Observe que, se dois membros usam a mesma senha as contas geradas serão as mesmas. A mesma senha será útil se uma única organização está tentando implantar em regiões e deseja compartilhar uma única conta (moeda base) em todos os nós.|12 ou mais caracteres|ND
Bloco gênese (bloco gênese personalizado = Sim)|Cadeia de caracteres JSON que representa o bloco gênese personalizado. Você pode encontrar mais detalhes sobre o formato do bloco gênese aqui, em Redes personalizadas.<br /><br />Uma conta de Ethereum ainda é criada ao fornecer um bloco gênese personalizado. Considere a especificação de uma conta de Ethereum criada previamente no bloco gênese para não aguardar a mineração.|JSON válido |ND
Chave compartilhada para conexão|Uma Chave compartilhada para a conexão entre os gateways de VNET.| 12 ou mais caracteres|ND
URL de dados do Consortium|A URL que indica os dados de configuração relevantes do consortium, fornecidos pela implantação de outro membro. <br /><br />Essas informações são fornecidas por um membro já conectado que tenha uma implantação. Se você implantou o restante da rede, a URL é a saída de implantação de modelo, chamada CONSORTIUM-DATA.||ND
Gateway de VNET para conectar-se|O caminho do recurso do Gateway de VNET à qual se conectar.<br />Essas informações são fornecidas por um membro já conectado que tenha uma implantação. Se você implantou o restante da rede, a URL está na saída de implantação de modelo, chamada CONSORTIUM_MEMBER_GATEWAY_ID. Observação: Devem ser usadas a URL de dados do consortium do mesmo membro e o recurso de gateway de VNET.||ND
Registrador de ponto de extremidade de informações de ponto a ponto|Ponto de extremidade de informações ponto a ponto fornecido pela implantação de outro membro|Ponto de extremidade válido do primeiro membro no consortium|ND
Registrador de chave de informações de ponto a ponto|Chave primária de informações ponto a ponto fornecida pela implantação de outro membro|Chave primária válida do primeiro membro no consortium|ND

### <a name="summary"></a>Resumo

Clique na folha de resumo para revisar as entradas especificadas e executar uma validação básica antes da implantação.

![Resumo](./media/ethereum-deployment-guide/summary.png)

Revise os termos legais e de privacidade e clique em **Comprar** para implantar. Se a implantação tem mais de uma região ou é para uma rede consortium, este modelo implantará previamente os gateways de VPN necessários para oferecer suporte à conectividade de rede com outros membros. A implantação do gateway pode levar até 45-50 minutos.

## <a name="post-deployment-sanity-checks"></a>Verificações de integridade de implantação de postagem

### <a name="administrator-page"></a>Página do administrator

Depois que a implantação foi concluída com êxito e todos os recursos foram provisionados, você pode ir para a página do administrador para obter uma exibição da sua rede blockchain e verificar a integridade do estado de implantação. A URL da página do administrador é o nome DNS do balanceador de carga; ela está presente na seção de saída da implantação do modelo denominada ADMIN_SITE.

Para localizá-la, selecione o grupo de recursos que foi implantado. Em seguida, selecione **Visão geral**e clique no link imediatamente abaixo de **Implantações** que mostra o número com êxito.

![Visão geral do grupo de recursos](./media/ethereum-deployment-guide/resource-overview.png)

A nova tela mostra o histórico de implantação. Selecione o primeiro recurso de implantação (por exemplo, microsoft-azure-blockchain.azure-blockchain-Service...) e procure a seção **Saídas** na parte inferior da página. Você verá a URL para a página de administração listada no parâmetro de saída de implantação de modelo, como ADMIN_SITE.

![Implantações de recursos](./media/ethereum-deployment-guide/resource-deployments.png)

![Saída de implantação](./media/ethereum-deployment-guide/deployment-output.png)

Para obter a página de administração, copie a saída **ADMIN-SITE** e abra-a em outra guia.

Na página de administração, você pode obter uma visão geral de alto nível da topologia que você implantou, examinando a seção de Status de Nó Ethereum. Ela inclui todos os nomes de host do nó, sua contagem de ponto a ponto e o último bloco visto. A contagem de ponto a ponto para cada nó está entre o mínimo de um menos que a *contagem total de nós* e a contagem de ponto a ponto máximo configurado. Observe que a contagem de ponto a ponto não restringe o número de nós que podem ser implantados na rede.
Ocasionalmente, você verá a contagem de ponto a ponto flutuar e ser menor do que (número total de nós - 1). A diferença na contagem nem sempre é um sinal de que os nós estão íntegros, uma vez que bifurcações no razão podem causar algumas pequenas alterações na contagem de ponto a ponto. Por fim, você pode inspecionar o último bloco visto por cada nó na rede para determinar as bifurcações ou atrasos no sistema.

![Status do nó](./media/ethereum-deployment-guide/node-status.png)

o status do nó é atualizado a cada 10 segundos. Recarregue a página por meio do navegador ou do botão **Recarregar** para atualizar a exibição.

### <a name="oms-portal"></a>Portal do OMS

Você pode localizar seu portal do OMS clicando no link na saída da implantação (OMSPORTALURL) ou selecionando o recurso do OMS no seu grupo de recursos implantados.

![URL do OMS](./media/ethereum-deployment-guide/oms-url.png)

![Recurso do OMS](./media/ethereum-deployment-guide/oms-resource.png)

O portal exibirá primeiro estatísticas de rede de alto nível como uma visão geral.

![Visão geral do OMS](./media/ethereum-deployment-guide/oms-overview.png)

Clicar na visão geral direcionará você para um portal para exibir estatísticas por nó.

![Estatísticas por nó](./media/ethereum-deployment-guide/per-node-stats.png)

### <a name="accessing-nodes"></a>Acessar nós

Você pode se conectar remotamente às máquinas virtuais para os nós de transação via SSH com seu nome de usuário admin e senha/chave SSH fornecidos. Como as VMs do nó de transação não possuem seus próprios endereços IP públicos, você precisará passar pelo balanceador de carga e especificar o número da porta. O comando SSH de execução para acessar o primeiro nó de transação está listado no parâmetro de saída de implantação de modelo como, **SSH_TO_FIRST_TX_NODE** (para a implantação de exemplo: ssh -p 4000 gethadmin@leader4vb.eastus.cloudapp.azure.com). Para obter nós de transações adicionais, incremente o número da porta em um (por exemplo, o primeiro nó de transação está na porta 4000).

Como as máquinas virtuais nas quais são executados os nós de mineração não são acessíveis externamente, você precisa passar por um dos nós de transação. Depois que você tiver uma sessão SSH para um nó de transação, instale sua chave privada no nó de transação ou use a senha para iniciar uma sessão SSH em qualquer um dos nós de mineração.

**Observação**

Os nomes de host podem ser obtido do Site de Administração ou do portal do Azure. No portal do Azure, os nomes de host de nós presentes no recurso de conjunto de dimensionamento de máquinas virtuais (VMSS) estão listados em **Instâncias**, que difere do nome de host real. Por exemplo, o nome do host no portal do Azure pode parecer **mn-asdfmv-reg1_0**, mas o nome de host real seria **mn-asdfmv-reg**.

Por exemplo: 

Nome do host de portal do Azure| Nome de host real
---|---
mn-ethwvu-reg1_0| mn-ethwvu-reg1000000
mn-ethwvu-reg1_1 |mn-ethwvu-reg1000001
mn-ethwvu-reg1_2 |mn-ethwvu-reg1000002

## <a name="adding-a-new-consortium-member"></a>Adicionar um novo membro consortium

### <a name="sharing-data"></a>Compartilhamento de dados

Como primeiro membro (ou um membro conectado) do consortium, você precisa fornecer aos outros membros algumas informações para que possam ingressar e estabelecer sua conexão. Especificamente:

1. **Dados de configuração compartilhados do Consortium**: há um conjunto de dados que são usados para orquestrar a conexão Ethereum entre dois membros. As informações necessárias, como o bloco de gênese, ID de rede consortium e nós de inicialização são gravadas em um arquivo em nós de transação de preenchimento ou outro membro implantado. O local deste arquivo está listado no parâmetro de saída de implantação modelo denominado **CONSORTIUM-DATA**.
2. **Ponto de extremidade de informações de ponto a ponto**: O ponto de extremidade de registrador de informações de ponto a ponto para obter informações de todos os nós já conectados à rede Ethereum a partir da implantação do Preenchimentos ou outro membro. O banco de dados armazena um conjunto de informações sobre cada nó conectado à rede, informações como nome do host do nó, endereço IP privado e etc. Este é o parâmetro de saída de implantação de modelo denominado **PEER_INFO_ENDPOINT**.
3. **Chave primária informações de ponto a ponto**: A chave primária de registrador de informações de ponto a ponto é usada para obter acesso à chave primária de informações de ponto a ponto do preenchimento ou outro membro. Este é o parâmetro de saída de implantação de modelo denominado **PEER_INFO_PRIMARY_KEY**.


4. **Gateway de VNET**: Cada membro estabelece uma conexão com a rede blockchain inteira por meio de um membro existente. Para se conectar à rede virtual, é necessário o caminho do recurso para o Gateway de VNET do membro ao qual você está se conectando. Este é o parâmetro de saída de implantação de modelo denominado **CONSORTIUM_MEMBER_GATEWAY_ID**.
5. **Chave compartilhada**: Um segredo previamente estabelecido entre dois membros da rede consortium que estabelecem uma conexão. Isso é uma cadeia de caracteres alfanumérica (entre 1 e 128 caracteres) que foi acordada fora do contexto da implantação. (Por exemplo, **MySharedKeyAbc123**)

### <a name="acceptance-of-new-member"></a>Aceitação do novo membro

Esta etapa deve ser feita depois que o membro que ingressa implantou sua rede com êxito. Antes de um membro poder ingressar na rede e ver o tráfego de transação, um membro existente deve executar uma configuração final no seu Gateway de VPN para aceitar a conexão. Isso significa que os nós de Ethereum do membro que ingressa não serão executados até que uma conexão seja estabelecida. Essa configuração pode ser feita por meio do PowerShell ou xPlat CLI. Um módulo do PowerShell ou script do xPlat CLI também são armazenados no nó de transação, juntamente com os dados de consortium. O local do script são os parâmetros de saída de implantação denominados **PAIR-GATEWAY-PS-MODULE** e **PAIR-GATEWAY AZURE CLISCRIPT**, respectivamente.

**Instalação do PowerShell/CLI**

Informações adicionais sobre como começar a usar os cmdlets do Azure PowerShell e Azure xPlat CLI podem ser encontradas na documentação do Azure.

Será necessário a versão mais recente dos cmdlets do Azure instalados localmente e uma sessão aberta. Certifique-se de fazer logon na sessão com suas credenciais de assinatura do Azure.

**PowerShell: Estabelecer Conexão**

Baixe o módulo do PowerShell e armazene-o localmente. O local do módulo do PowerShell é especificado como o parâmetro de saída de implantação de modelo **PAIR-GATEWAY-PS-MODULE**.

Se já não estiver habilitado, use o cmdlet **Set-ExecutionPolicy** para a sessão local permitir a execução de um módulo sem sinal.

**Set-ExecutionPolicy Unrestricted CurrentUser**

Em seguida, faça logon na sua assinatura do Azure na qual você implantou a implantação de preenchimento usando

**Login-AzureRmAccount**

Em seguida, importe o módulo:

**Import-Module <filepath to downloaded file>**

Por fim, execute a função com a entrada apropriada:

- **MyGatewayResourceId** : Caminho do recurso do seu Gateway. Este é o parâmetro de saída de implantação de modelo denominado **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId** : Caminho do recurso de gateway do membro que ingressa. Isso é fornecido pelo membro que ingressa e o parâmetro de saída de implantação de modelo que também é denominado **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName** : Um nome para identificar essa conexão de Gateway.
- **Chave compartilhada**: O segredo previamente estabelecido entre os dois membros da rede consortium que estabelecem uma conexão.

**CreateConnection** -MyGatewayResourceId <resource path of your Gateway> - OtherGatewayResourceId < caminho do recurso do gateway do membro que ingressa > - ConnectionName myConnection - SharedKey "MySharedKeyAbc123"

**xPlat CLI: Estabelecer Conexão**

Baixe o script de CLI do Azure e armazene-o localmente. O local do script de CLI do Azure é especificado no parâmetro de implantação de modelo denominado **PAIR-GATEWAY-SCRIPT do AZURE-CLI**.

Execute o script com a entrada apropriada:

- **MyGatewayResourceId** : Caminho do recurso do seu Gateway. Este é o parâmetro de saída de implantação de modelo denominado **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId** : Caminho do recurso de gateway do membro que ingressa. Isso é fornecido pelo membro que ingressa e o parâmetro de implantação de modelo de sua implantação também denominado **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName** : Um nome para identificar essa conexão de Gateway.
- **Chave compartilhada**: O segredo previamente estabelecido entre os dois membros da rede consortium que estabelecem uma conexão.
- **Local** : A região do Azure em que o recurso de gateway é implantado.

``` powershell
az network vpn-connection create --name $ConnectionName --resource-group
$MyResourceGroup --vnet-gateway1 $MyGatewayResourceId --shared-key $SharedKey --vnet-
gateway2 $OtherGatewayResourceId --enable-bgp
```

A arquitetura será o seguinte depois que você configurou com êxito a conexão entre as implantações de **preenchimento** e **membro**.

![Arquitetura de preenchimento e membro](./media/ethereum-deployment-guide/leader-member.png)

## <a name="fund-new-member-account"></a>Custear nova conta de membro

### <a name="generated-genesis-block"></a>Bloco gênese gerado

Como o primeiro membro, sua conta Ethereum é suportada com o uso de um trilhão de Ether se a implantação gera o bloco gênese (Bloco gênese personalizado = não). Outros membros terão uma conta que não é patrocinada previamente e deve aguardar para acumular Ether conforme seus nós de mineração comecem a explorar os blocos. Para evitar fazer com que os novos membros aguardem os Ether, você precisará financiar explicitamente contas Ethereum dos membros que ingressam.

Para fazer isso, os membros que ingressam devem fornecer a conta de Ethereum que é exibida em seu site de administração. Você pode usar o site de administração para transferir Ether de sua conta para as contas deles digitando a conta fornecida.

### <a name="custom-genesis-block"></a>Bloco gênese personalizado

Se um bloco gênese personalizado é fornecido com uma conta de Ethereum especificada, você pode usar o MetaMask ou outra ferramenta para transferir Ether da conta especificada para a conta Ethereum gerada previamente visível no site de administração. Para obter instruções sobre como usar o MetaMask, consulte [Criar conta de Ethereum](#create-ethereum-account).

Se um bloco gênese personalizado é fornecido sem uma conta ou você não tem acesso a todas as contas pré-alocadas, você precisará esperar até que os nós de mineração começam a explorar para gerar Ether em sua conta (base de moedas). A rapidez com que os fundos são gerados depende do nível de dificuldade que você especificar no bloco gênese personalizado.

## <a name="create-ethereum-account"></a>Criar conta Ethereum

Para criar uma conta adicional, você pode usar uma variedade de soluções. Uma solução é o MetaMask, uma extensão do Chrome que fornece um cofre de identidade e conexão a uma rede Ethereum, pública, teste ou personalizada. O MetaMask formula uma transação para registrar a conta na rede.

Esta transação, como qualquer outra transação, irá para um dos nós de transação e, eventualmente, será coletada em um bloco, conforme ilustrado abaixo.

![Nó de transação](./media/ethereum-deployment-guide/transaction-node.png)

Para instalar a extensão no Chrome, vá para Personalizar e controlar o Google Chrome (botão de estouro) > Mais ferramentas > Extensões > Obter mais extensões e pesquise MetaMask.

![Extensão MetaMask](./media/ethereum-deployment-guide/metamask-extension.png)

Uma vez instalado, abra o MetaMask e crie um novo cofre. Por padrão, o cofre será conectado à rede de teste Morden. Você precisará alterar isso para se conectar à rede consortium privada implantada, especificamente para o balanceador de carga na frente de nós de transação. A partir da saída do modelo, recuperar o ponto de extremidade RPC Ethereum exposto na porta 8545, denominado `ETHEREUM-RPC-ENDPOINT` e insira-o no RPC personalizado, conforme mostrado abaixo.

![Configurações do MetaMask](./media/ethereum-deployment-guide/metamask-settings.png)

Ao criar o cofre, você cria uma carteira que contém uma conta. Para criar contas adicionais, selecione **Alterar contas** e, em seguida, o botão **+**.

![Criar conta do MetaMask](./media/ethereum-deployment-guide/metamask-create-account.png)

### <a name="initiate-initial-ether-allocation"></a>Iniciar alocação de Ether inicial

Na página de administrador, você pode formular uma transação para transferir Ether da conta pré-alocada para outra conta Ethereum. Essa transferência Ether é uma transação que é enviada para o nó de transação e extraída em um bloco, conforme ilustrado abaixo.

![Nó de transação](./media/ethereum-deployment-guide/transaction-node-mined.png)

Através do ícone da área de transferência na carteira MetaMask, copie o endereço da conta Ethereum à qual você deseja transferir Ether e volte para a página de administrador. Cole a conta copiada no campo de entrada para transferir 1000 Ether da conta Ethereum pré-alocada para sua conta recém-criada. Clique em **Enviar** e aguarde até que a transação seja extraída em um bloco.

![Status do nó](./media/ethereum-deployment-guide/node-status2.png)

Depois que a transação é confirmada em um bloco explorado, o saldo da conta no MetaMask para sua conta refletirá a transferência de 1000 Ether.

![Transferência de MetaMask](./media/ethereum-deployment-guide/metamask-transfer.png)

### <a name="transfer-of-ether-between-accounts"></a>Transferência de Ether entre contas

Neste ponto, você está pronto para executar transações dentro de sua rede consortium privada. A transação mais simples é transferir Ether de uma conta para outra. Para formular essa transação, você pode usar o MetaMask novamente, a transferência de dinheiro da primeira conta usada acima para uma segunda conta.

Na **Carteira 1** no MetaMask, clique em enviar. Copie o endereço da segunda carteira criada para o campo de entrada **Endereço do destinatário** e a quantidade de Ether a ser transferida no campo de entrada **Quantidade**. Clique em **Enviar** e aceite a transação.

![Transações de envio de MetaMask](./media/ethereum-deployment-guide/metamask-send.png)

Novamente, quando a transação é extraída e confirmada em um bloco, os saldos de conta são refletidos adequadamente. Observe que, no saldo da **Carteira 1** foi deduzido mais de 15 Ether, uma vez que você precisa pagar uma taxa de mineração para processar a transação.

![Carteira 1](./media/ethereum-deployment-guide/wallet1.png)

![Carteira 2](./media/ethereum-deployment-guide/wallet2.png)

## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para se concentrar no desenvolvimento de aplicativo e contratos inteligentes em sua rede de blockchain consortium privada.
