---
title: Configuração VNet de Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Este tópico descreve opções de configuração para uma VNet (rede virtual) com uma Instância Gerenciada do Banco de Dados SQL do Azure.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: f634167f24c221e702696174ea86a212c535695b
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246374"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Configurar VNet para Instância Gerenciada do Banco de Dados SQL do Azure

A Instância Gerenciada do Banco de Dados SQL do Azure (versão prévia) deve ser implantada em uma [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md) do Azure. Essa implantação permite os cenários a seguir: 
- Conectar uma Instância Gerenciada diretamente de uma rede local 
- Conectar uma Instância Gerenciada ao servidor vinculado ou a outro armazenamento de dados local 
- Conectar uma Instância Gerenciada a recursos do Azure  

## <a name="plan"></a>Plano

Planeje como implantar uma Instância Gerenciada na rede virtual, usando as respostas para as seguintes perguntas: 
- Você planeja implantar Instâncias Gerenciadas simples ou múltiplas? 

  O número de Instâncias Gerenciadas determina o tamanho mínimo da sub-rede para alocar suas Instâncias Gerenciadas. Para obter mais informações, consulte [Determinar o tamanho da sub-rede para Instância Gerenciada](#determine-the-size-of-subnet-for-managed-instances). 
- Você precisa implantar a Instância Gerenciada em uma rede virtual existente ou você está criando uma nova rede? 

   Se você planeja utilizar uma rede virtual existente, será necessário modificar essa configuração de rede para acomodar sua Instância Gerenciada. Para obter mais informações, consulte [Modificar a rede virtual existente para Instância Gerenciada](#modify-an-existing-virtual-network-for-managed-instances). 

   Se você planeja criar uma nova rede virtual, consulte [Criar nova rede virtual para Instância Gerenciada](#create-a-new-virtual-network-for-managed-instances).

## <a name="requirements"></a>Requisitos

Para a criação de Instância Gerenciada, é necessário dedicar uma sub-rede dentro da VNet que atenda aos seguintes requisitos:
- **Estar vazia**: a sub-rede não deve conter nenhum outro serviço de nuvem associado a ela e não deve ser a sub-rede do Gateway. Não será possível criar a Instância Gerenciada na sub-rede que contenha outros recursos, além da instância gerenciada, ou adicionar outros recursos dentro da sub-rede posteriormente.
- **Sem NSG**: a sub-rede não deve ter um Grupo de Segurança de Rede associado a ela.
- **Ter uma tabela de rotas específica**: a sub-rede deve ter uma UDR (Tabela de Rotas do Usuário) com Próximo Salto para a Internet 0.0.0.0/0 como a única rota atribuída a ela. Para obter mais informações, consulte [Criar a tabela de rotas necessária e associá-la](#create-the-required-route-table-and-associate-it)
3. **DNS personalizado opcional**: se o DNS personalizado for especificado na VNet, o endereço IP do resolvedor recursivo do Azure (como 168.63.129.16) deverá ser adicionado à lista. Para obter mais informações, consulte [Configurar DNS personalizado](sql-database-managed-instance-custom-dns.md).
4. **Sem ponto de extremidade de serviço**: a sub-rede não deve ter um ponto de extremidade de serviço (Sql ou Armazenamento) associado a ela. Certifique-se de que a opção Pontos de Extremidade de Serviço está Desabilitada ao criar a VNet.
5. **Endereços IP suficientes**: a sub-rede deve ter no mínimo 16 endereços IP. Para obter mais informações, consulte [Determinar o tamanho da sub-rede para Instâncias Gerenciadas](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> Não será possível implantar nova Instância Gerenciada se a sub-rede de destino não estiver compatível com todos os requisitos anteriores. A Vnet de destino e a sub-rede devem ser mantidas em conformidade com esses requisitos da Instância Gerenciada (antes e após a implantação), pois qualquer violação pode fazer a instância entrar em estado defeituoso e tornar-se indisponível. A recuperação desse estado exigirá a criação de uma nova instância em uma VNet com as políticas de rede compatíveis, recriar dados em nível de instância e restaurar os bancos de dados. Isso introduz tempo de inatividade significativo aos aplicativos.

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Determinar o tamanho da sub-rede para Instâncias Gerenciadas

Quando você uma Instância Gerenciada, o Azure aloca uma série de máquinas virtuais, dependendo do tamanho da camada selecionado durante o provisionamento. Como essas máquinas virtuais estão associadas à sua sub-rede, elas exigem endereços IP. Para garantir alta disponibilidade durante operações regulares e manutenção do serviço, o Azure pode alocar máquinas virtuais adicionais. Como resultado, o número de endereços IP necessários em uma sub-rede é maior que o número de Instâncias Gerenciadas nessa sub-rede. 

Por design, uma Instância Gerenciada precisa de um mínimo de 16 endereços IP em uma sub-rede e pode usar até 256 endereços IP. Como resultado, é possível usar máscaras de sub-rede /28 a /24 ao definir os intervalos de IP da sub-rede. 

Se você planeja implantar múltiplas instâncias gerenciadas dentro da sub-rede e precisa otimizar o tamanho da sub-rede, use estes parâmetros para formar um cálculo: 

- O Azure usa cinco endereços IP na sub-rede para necessidades próprias 
- Cada instância de Uso Geral precisa de dois endereços 
- Cada instância Comercialmente Crítica precisa de quatro endereços

**Exemplo**: Você planeja ter três Usos Gerais e duas instâncias Gerenciadas Comercialmente Críticas. Isso significa que você precisa de 5 + 3 * 2 + 2 * 4 = 19 endereços IP. Como os intervalos de IP são definidos em potência de 2, você precisa do intervalo de IP de 32 (2^5) endereços IP. Portanto, é necessário reservar a sub-rede com a máscara de sub-rede de /27. 

## <a name="create-a-new-virtual-network-for-managed-instance-using-azure-resource-manager-deployment"></a>Criar uma nova rede virtual para Instância Gerenciada usando a implantação do Azure Resource Manager

A maneira mais fácil de criar e configurar a rede virtual é usar o modelo de implantação do Azure Resource Manager.

1. Entre no Portal do Azure.

2. Use o botão **Implantar no Azure** para implantar a rede virtual na nuvem do Azure:

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  Este botão abrirá um formulário que poderá ser utilizado para configurar o ambiente de rede onde será possível implantar a Instância Gerenciada.

  > [!Note]
  > Esse modelo do Azure Resource Manager implantará a rede virtual com duas sub-redes. Uma sub-rede chamada **ManagedInstances** é reservada para Instâncias Gerenciadas e tem uma tabela de roteamento pré-configurada, enquanto a outra sub-rede chamada **Padrão** é usada para outros recursos que devem acessar a Instância Gerenciada (por exemplo, Máquinas Virtuais do Microsoft Azure). É possível remover a sub-rede **Padrão** se ela não for necessária.

3. Configure o ambiente de rede. É possível configurar os parâmetros do ambiente de rede, conforme a seguir:

![Configurar rede do Azure](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

É possível alterar os nomes da VNet e sub-redes e ajustar os intervalos de IP associados aos recursos de rede. Após pressionar o botão "Comprar", esse formulário criará e configurará o ambiente. Se você não precisar de duas sub-redes, poderá excluir uma padrão. 

## <a name="create-a-new-virtual-network-for-managed-instances-using-portal"></a>Criar uma nova rede virtual para Instâncias Gerenciadas usando o portal

Criar uma rede virtual do Azure é um pré-requisito para a criação de uma Instância Gerenciada. Você pode utilizar o Portal do Azure, [PowerShell](../virtual-network/quick-create-powershell.md) ou [CLI do Azure](../virtual-network/quick-create-cli.md). A seção a seguir mostra as etapas utilizando o Portal do Azure. Os detalhes discutidos aqui aplicam-se a cada um desses métodos.

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Localize e, em seguida, clique em **Rede Virtual**, verifique se o **Gerenciador de Recursos** está selecionado como o modo de implantação e, em seguida, clique em **Criar**.

   ![criação de rede virtual](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Preencha o formulário de rede virtual com as informações solicitadas, de modo semelhante à captura de tela a seguir:

   ![formulário de criação de rede virtual](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Clique em **Criar**.

   O espaço de endereço e a sub-rede são especificados na notação CIDR. 

   > [!IMPORTANT]
   > Os valores padrão criam uma sub-rede que ocupa todo o espaço de endereço da VNet. Se escolher essa opção, você não poderá criar outros recursos dentro da rede virtual, exceto Instância Gerenciada. 

   A abordagem recomendada seria a seguinte: 
   - Calcule o tamanho da sub-rede de acordo com a seção [Determinar o tamanho da sub-rede para Instância Gerenciada](#determine-the-size-of-subnet-for-managed-instances)  
   - Avaliar as necessidades para o restante da VNet 
   - Preencher VNet e intervalos de endereço de sub-rede adequadamente 

   Certifique-se de que a opção Pontos de Extremidade de Serviço permaneça **Desabilitado**. 

   ![formulário de criação de rede virtual](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

### <a name="create-the-required-route-table-and-associate-it"></a>Criar a tabela de rotas necessária e associá-la

1. Entre no Portal do Azure  
2. Localize e clique em **Tabela de rotas** e, em seguida, clique em **Criar** na página Tabela de rotas.

   ![formulário de criação da tabela de rotas](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. Crie uma rota de Próximo Salto para a Internet 2.0 0.0.0.0/0, de modo semelhante às capturas de tela a seguir:

   ![adicionar tabela de rotas](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![rota](./media/sql-database-managed-instance-tutorial/route.png)

4. Associe essa rota à sub-rede para a Instância Gerenciada, de modo semelhante às capturas de tela a seguir:

    ![sub-rede](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![definir tabela de rotas](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![definir tabela de rotas - salvar](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


Quando a VNet for criada, você estará pronto para criar a Instância Gerenciada.  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Modificar uma rede virtual existente para Instâncias Gerenciadas 

As perguntas e respostas nesta seção mostram como adicionar uma Instância Gerenciada à rede virtual existente. 

**Você está utilizando o modelo de implantação do Azure Resource Manager ou Clássico para a rede virtual existente?** 

Só é possível criar uma Instância Gerenciada nas redes virtuais do Resource Manager. 

**Você gostaria de criar uma nova sub-rede para a Instância Gerenciada do SQL ou utilizar uma existente?**

Se quiser criar uma nova: 

- Calcule o tamanho da sub-rede, seguindo as diretrizes na seção [Determinar o tamanho da sub-rede para Instâncias Gerenciadas](#determine-the-size-of-subnet-for-managed-instances).
- Siga as etapas em [Adicionar, alterar ou excluir uma sub-rede da rede virtual](../virtual-network/virtual-network-manage-subnet.md). 
- Crie uma tabela de rotas que contenha entrada única, **0.0.0.0/0**, como próximo salto para a Internet e associe-a à sub-rede para a Instância Gerenciada.  

Se quiser criar uma Instância Gerenciada dentro de uma sub-rede existente: 
- Verifique se a sub-rede está vazia - uma Instância Gerenciada não pode ser criada em uma sub-rede contendo outros recursos, incluindo a sub-rede do Gateway 
- Calcule o tamanho da sub-rede, seguindo as diretrizes na seção [Determinar o tamanho da sub-rede para Instâncias Gerenciadas](#determine-the-size-of-subnet-for-managed-instances) e verifique se está dimensionada adequadamente. 
- Verifique se os pontos de extremidade de serviço não estão habilitados na sub-rede.
- Certificar-se de que não há grupos de segurança de rede associados à sub-rede 

**Há um servidor DNS personalizado configurado?** 

Se sim, consulte [Configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md). 

- Crie a tabela de rotas desejada e associe-a: consulte [Criar a tabela de rotas necessária e associá-la](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [O que é uma Instância Gerenciada](sql-database-managed-instance.md)
- Para um tutorial que mostra como criar uma VNet, criar uma Instância Gerenciada e restaurar um banco de dados a partir de um backup de banco de dados, consulte [Criar uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-create-tutorial-portal.md).
- Para problemas de DNS, consulte [Configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md)
