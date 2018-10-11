---
title: Consórcio Hyperledger Fabric
description: Use o modelo de solução Hyperledger Fabric Consortium para implantar e configurar uma rede de membro único
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: ee8057be98d18db5963a3e5f1ba1f8bd8d76fe05
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48240325"
---
# <a name="hyperledger-fabric-single-member-network"></a>Rede de membro único do Hyperledger Fabric

Você pode usar o modelo de solução Hyperledger Fabric Consortium para implantar e configurar uma rede de um único membro (vários nós) do Hyperledger Fabric.

Depois de ler este artigo, você irá:

- Obter conhecimento prático de blockchain, Hyperledger Fabric e arquiteturas de rede de consórcio mais complexas
- Saiba como implantar e configurar uma rede de consórcio Hyperledger Fabric de membro único no portal do Azure

## <a name="about-blockchain"></a>Sobre o blockchain

Se você é novo na comunidade blockchain, essa é uma ótima oportunidade para aprender sobre a tecnologia de maneira fácil e configurável no Azure. Blockchain é a tecnologia por trás do Bitcoin; no entanto, é muito mais do que apenas um ativador de uma moeda virtual. É uma combinação de banco de dados existente, sistema distribuído e tecnologias criptográficas que permitem a computação segura de vários participantes com garantias em relação à imutabilidade, verificabilidade, capacidade de auditoria e resiliência ao ataque. Diferentes protocolos empregam mecanismos diferentes para fornecer esses atributos. [Malha Hyperledger](https://github.com/hyperledger/fabric) é um protocolo de tal.

## <a name="consortium-architecture-on-azure"></a>Arquitetura do consórcio no Azure

Esse modelo implementa uma topologia para ajudar a testar e simular a produção para usuários em uma única organização (membro único). Essa implantação é composta por uma rede de vários nós em uma única região, que em breve será expandida para várias regiões.

A rede é composta por três tipos de nós:

1. **Nó de Membro**: um nó que executa o serviço de associação de Fabric que registra e gerencia membros da rede. Este nó pode eventualmente ser armazenado em cluster para escalabilidade e alta disponibilidade, no entanto, neste laboratório, um único nó de membro será usado.
2. **Nós do comprador**: um nó que executa o serviço de comunicação que implementa uma garantia de entrega, como transmissões de ordem total de transmissão ou transações atômicas.
3. **Nós pares**: um nó que confirma transações e mantém o estado e uma cópia do ledger distribuído.

## <a name="getting-started"></a>Introdução

Para começar, você precisará de uma assinatura do Azure que possa suportar a implantação de várias máquinas virtuais e contas de armazenamento padrão. Se você não tiver uma assinatura do Azure, poderá [ criar uma conta gratuita do Azure ](https://azure.microsoft.com/free/).

Por padrão, a maioria dos tipos de assinatura suporta uma pequena topologia de implantação sem precisar aumentar a cota. A menor implantação possível para um membro precisará:

- 5 máquinas virtuais (5 núcleos)
- 1 VNet
- 1 balanceador de carga
- 1 endereço IP público

Depois de ter uma assinatura, acesse o [ Portal do Azure ](https://portal.azure.com). Selecione **+**, selecione **Blockchain** e selecione **Hyperledger Fabric Single Blockchain**.

![Modelo de mercado de blockchain de membro único de tecido Hyperledger](./media/hyperledger-fabric-single-member-blockchain/marketplace-template.png)

## <a name="deployment"></a>Implantação

Para começar, selecione o **Bloco de Bloqueio Único de Membro do Hyperledger Fabric** e clique em **Criar**. Isso abrirá o blade **Basics** no assistente.

A implantação de modelos orientará você na configuração da rede de vários nós. O fluxo de implementação é dividido em três etapas: Básico, Configuração de Rede e Configuração de Malha.

### <a name="basics"></a>Noções básicas

No blade **Básico**, especifique valores para parâmetros padrão para qualquer implementação, como assinatura, grupo de recursos e propriedades básicas da máquina virtual.

![Noções básicas](./media/hyperledger-fabric-single-member-blockchain/basics.png)

Nome do Parâmetro| DESCRIÇÃO| Valores Permitidos|Valor Padrão
---|---|---|---
**Prefixo do recurso**| Uma string usada como base para nomear os recursos implementados.|6 caracteres ou menos|ND
**nome de usuário da VM**| O nome de usuário do administrador de cada uma das máquinas virtuais implementadas para esse membro.|1 a 64 caracteres|azureuser
**Tipo de autenticação**| O método para autenticar para a máquina virtual.|Senha ou chave pública SSH|Senha
**Senha (Tipo de Autenticação = Senha)**|A senha para a conta de administrador para cada uma das máquinas virtuais implantadas. A senha deve conter 3 dos seguintes: 1 caractere maiúsculo, 1 caractere minúsculo, 1 número e 1 caractere especial.<br /><br />Embora todas as VMs tenham inicialmente a mesma senha, você pode alterar a senha após o fornecimento.|12 a 72 caracteres|ND
**Chave SSH (tipo de autenticação = chave pública)**|A chave de shell segura usada para login remoto.||ND
**Restringir o acesso pelo endereço IP**|Configuração para determinar o tipo, se o acesso do terminal do cliente é restrito ou não.|Sim/Não| Não 
**Endereço IP ou sub-rede permitidos (restringir acesso por endereço IP = Sim)**|O endereço IP ou o conjunto de endereços IP com permissão para acessar o terminal do cliente quando o controle de acesso está ativado.||ND
**Assinatura** |A assinatura para a qual implantar.
**Grupo de recursos** |O grupo de recursos para o qual implantar a rede do consórcio.||ND
**Localidade** |A região do Azure na qual implantar a pegada de rede do primeiro membro.

### <a name="network-size-and-performance"></a>Tamanho e desempenho da rede

Em seguida, em **Tamanho de rede e desempenho,** especifique entradas para o tamanho da rede do consórcio, como o número de nós de associação, ordenador e ponto. Escolha as opções de infraestrutura e o tamanho da sua máquina virtual.

![Tamanho e desempenho da rede](./media/hyperledger-fabric-single-member-blockchain/network-size-performance.png)

Nome do Parâmetro| DESCRIÇÃO| Valores Permitidos|Valor Padrão
---|---|---|---
**Número de nós de associação**|O número de nós que executam o serviço de associação. Para obter detalhes adicionais sobre o serviço de associação, consulte Serviços de segurança e associação na [documentação do Hyperledger ](https://media.readthedocs.org/pdf/hyperledger-fabric/latest/hyperledger-fabric.pdf).<br /><br />Atualmente, este valor está restrito a 1 nó, mas planejamos dar suporte à escalabilidade por meio de cluster na próxima revisão.|1| 1
**Número de nós do comprador** |O número de nós que ordenam (organizam) transações em um bloco .--> Esta declaração é expressiva e confusa. Para obter detalhes adicionais sobre o serviço de pedidos, visite a [documentação do Hyperledger ](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html).<br /><br />Este valor está atualmente restrito a 1 nó. |1 |1
**Número de nós pares**| Nós que são de propriedade de membros do consórcio que executam transações e mantêm o estado e uma cópia do ledger.<br /><br />Para obter detalhes adicionais sobre o serviço de pedidos, visite a [documentação do Hyperledger ](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|3| 3 - 9
**desempenho de armazenamento**|O tipo de armazenamento que suporta cada um dos nós implementados. Para saber mais sobre armazenamento, visite [ Introdução ao Armazenamento do Microsoft Azure ](https://docs.microsoft.com/azure/storage/common/storage-introduction) e [ Armazenamento Premium ](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage).|Standard ou Premium|Standard
**Tamanho da máquina virtual** |O tamanho da máquina virtual usado para todos os nós na rede|Standard A,<br />Standard D,<br />Standard D-v2,<br />Standard F séries,<br />Standard DS,<br />e Standard FS|Standard D1_v2

### <a name="fabric-specific-settings"></a>Configurações específicas de tecido

Finalmente, em **Fabric Settings**, especifique as definições de configuração relacionadas ao Fabric.

![Configurações de tecido](./media/hyperledger-fabric-single-member-blockchain/fabric-settings.png)

Nome do Parâmetro| DESCRIÇÃO| Valores Permitidos|Valor Padrão
---|---|---|---
**Nome de Usuário do Bootstrap**| O usuário autorizado inicial que será registrado com os serviços do membro na rede implantada.|9 ou menos caracteres|administrador
**Senha do usuário de inicialização para autoridade de certificação de malha**|A senha do administrador usada para proteger a conta CA do Fabric importada para o nó da associação.<br /><br />A senha deve conter um caractere maiúsculo, um caractere minúsculo e um número.|12 ou mais caracteres|ND

### <a name="deploy"></a>Implantar

No **Resumo**, revise as entradas especificadas e execute a validação básica de pré-implantação.

![Resumo](./media/hyperledger-fabric-single-member-blockchain/summary.png)

Revise os termos legais e de privacidade e clique em **Compra** para implantar. Dependendo do número de VMs provisionadas, o tempo de implantação pode variar de alguns minutos a dezenas de minutos.

### <a name="accessing-nodes"></a>Acessando nós

Quando a implantação estiver concluída, uma **Visão geral** será exibida.

![Implantações](./media/hyperledger-fabric-single-member-blockchain/deployments.png)

Se a tela não aparecer automaticamente (talvez porque você se moveu pelo portal de gerenciamento enquanto a implantação estava em execução), você sempre poderá encontrá-la na guia Grupos de recursos na barra de navegação à esquerda. Clique no nome do grupo de recursos que você inseriu na etapa 1 para acessar a página **Visão geral**.

A visão geral lista todos os recursos que foram implantados pelo modelo de solução. Você pode explorá-los à vontade, mas a partir dessa tela você também pode acessar os _parâmetros de saída_ gerados pelo modelo. Esses parâmetros de saída fornecerão informações úteis ao se conectar à sua rede Hyperledger Fabric.

Para acessar os parâmetros de saída, primeiro clique na guia **Implementações** na folha Grupo de recursos. O histórico de implantação é exibido.

![Histórico de implantações](./media/hyperledger-fabric-single-member-blockchain/deployment-history.png)

No Deployment History, clique na primeira implantação na lista para ver os detalhes.

![Detalhes da implantação](./media/hyperledger-fabric-single-member-blockchain/deployment-details.png)

A tela de detalhes mostrará um resumo da implantação, seguido por três parâmetros de saída úteis:

- O _API ENDPOINT_ pode ser usado depois que você implanta um aplicativo na rede.
- O _PREFIX_, também chamado _prefixo de implementação_, identifica de forma exclusiva seus recursos e sua implementação. Ele será usado ao usar as ferramentas baseadas em linha de comando.
- O _SSH-TO-FIRST-VM_ fornece um comando ssh pré-montado com todos os parâmetros corretos necessários para conectar-se à primeira VM em sua rede; no caso do Hyperledger Fabric, será o nó Fabric-CA.

Você pode se conectar remotamente às máquinas virtuais para cada nó via SSH com seu nome de usuário e senha / chave SSH fornecidos pelo administrador. Como as máquinas virtuais do nó não têm seus próprios endereços IP públicos, você precisará passar pelo balanceador de carga e especificar o número da porta. O comando SSH para acessar o primeiro nó de transação é a terceira saída de modelo, ** SSH-TO-FIRST-VM (para a implementação de amostra: `sh -p 3000 azureuser@hlf2racpt.northeurope.cloudapp.azure.com`). Para obter nós de transação adicionais, incremente o número da porta em um (por exemplo, o primeiro nó de transação está na porta 3000, o segundo está em 3001, o terceiro está em 3002, etc.).

## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para se concentrar no desenvolvimento de aplicações e chaincode em sua rede blockchain de consórcio Hyperledger.
