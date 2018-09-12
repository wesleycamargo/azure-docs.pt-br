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
ms.openlocfilehash: 748489785241c0eab6022e3585164974f330d6f9
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44049666"
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
- **Sub-rede dedicada**: a sub-rede não deve conter nenhum outro serviço de nuvem associado a ela e não deve ser a sub-rede do Gateway. Não será possível criar a Instância Gerenciada na sub-rede que contenha outros recursos, além da instância gerenciada, ou adicionar outros recursos dentro da sub-rede posteriormente.
- **Sem NSG**: a sub-rede não deve ter um Grupo de Segurança de Rede associado a ela. 
- **Ter uma tabela de rotas específica**: a sub-rede deve ter uma UDR (Tabela de Rotas do Usuário) com Próximo Salto para a Internet 0.0.0.0/0 como a única rota atribuída a ela. Para obter mais informações, consulte [Criar a tabela de rotas necessária e associá-la](#create-the-required-route-table-and-associate-it)
3. **DNS personalizado opcional**: se o DNS personalizado for especificado na VNet, o endereço IP do resolvedor recursivo do Azure (como 168.63.129.16) deverá ser adicionado à lista. Para obter mais informações, consulte [Configurar DNS personalizado](sql-database-managed-instance-custom-dns.md).
4. **Sem ponto de extremidade de serviço**: a sub-rede não deve ter um Ponto de extremidade de serviço associado a ela. Certifique-se de que a opção Pontos de extremidade de serviço esteja Desabilitada ao criar a VNet.
5. **Endereços IP suficientes**: a sub-rede deve ter suporte para no mínimo 16 endereços IP (o mínimo recomendado é de 32 endereços IP). Para obter mais informações, consulte [Determinar o tamanho da sub-rede para Instâncias Gerenciadas](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> Não será possível implantar nova Instância Gerenciada se a sub-rede de destino não estiver compatível com todos os requisitos anteriores. A Vnet de destino e a sub-rede devem ser mantidas em conformidade com esses requisitos da Instância Gerenciada (antes e após a implantação), pois qualquer violação pode fazer a instância entrar em estado defeituoso e tornar-se indisponível. A recuperação desse estado exigirá a criação de uma nova instância em uma VNet com as políticas de rede compatíveis, recriar dados em nível de instância e restaurar os bancos de dados. Isso introduz tempo de inatividade significativo aos aplicativos.

Com a introdução da _Política de intenção de rede_, você pode adicionar um NSG (Grupo de Segurança de Rede) em uma sub-rede de Instância Gerenciada depois que a Instância Gerenciada é criada.

Agora você pode usar um NSG para restringir os intervalos de IP dos quais aplicativos e usuários podem consultar e gerenciar os dados filtrando o tráfego de rede que vai para a porta 1433. 

> [!IMPORTANT]
> Quando você estiver configurando as regras do NSG que vão restringir acesso à porta 1433, também precisará inserir as regras de entrada de prioridade mais alta exibidas na tabela a seguir. Caso contrário, a Política de intenção de rede bloqueará a alterações como fora de conformidade.

| NOME       |PORTA                        |PROTOCOLO|FONTE           |DESTINO|AÇÃO|
|------------|----------------------------|--------|-----------------|-----------|------|
|gerenciamento  |9000, 9003, 1438, 1440, 1452|Qualquer     |Qualquer              |Qualquer        |PERMITIR |
|mi_subnet   |Qualquer                         |Qualquer     |SUB-REDE DA MI        |Qualquer        |PERMITIR |
|health_probe|Qualquer                         |Qualquer     |AzureLoadBalancer|Qualquer        |PERMITIR |

A experiência de roteamento também foi aprimorada para que, além do roteamento de Internet 0.0.0.0/0 do tipo próximo salto, agora você também possa adicionar o UDR para rotear o tráfego em direção aos seus intervalos de IP privados locais por meio do gateway de rede virtual ou do NVA (dispositivo de rede virtual).

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Determinar o tamanho da sub-rede para Instâncias Gerenciadas

Quando você cria uma Instância Gerenciada, o Azure aloca uma série de máquinas virtuais, dependendo da camada selecionada durante o provisionamento. Como essas máquinas virtuais estão associadas à sua sub-rede, elas exigem endereços IP. Para garantir alta disponibilidade durante operações regulares e manutenção do serviço, o Azure pode alocar máquinas virtuais adicionais. Como resultado, o número de endereços IP necessários em uma sub-rede é maior que o número de Instâncias Gerenciadas nessa sub-rede. 

Por design, uma Instância Gerenciada precisa de um mínimo de 16 endereços IP em uma sub-rede e pode usar até 256 endereços IP. Como resultado, é possível usar máscaras de sub-rede /28 a /24 ao definir os intervalos de IP da sub-rede. 

> [!IMPORTANT]
> O tamanho da sub-rede com 16 endereços IP é o mínimo com potencial limitado para uma expansão futura da Instância Gerenciada. É altamente recomendável escolher uma sub-rede com o prefixo /27 ou abaixo. 

Se você planeja implantar múltiplas instâncias gerenciadas dentro da sub-rede e precisa otimizar o tamanho da sub-rede, use estes parâmetros para formar um cálculo: 

- O Azure usa cinco endereços IP na sub-rede para necessidades próprias 
- Cada instância de Uso Geral precisa de dois endereços 
- Cada instância Comercialmente Crítica precisa de quatro endereços

**Exemplo**: Você planeja ter três Usos Gerais e duas instâncias Gerenciadas Comercialmente Críticas. Isso significa que você precisa de 5 + 3 * 2 + 2 * 4 = 19 endereços IP. Como os intervalos de IP são definidos em potência de 2, você precisa do intervalo de IP de 32 (2^5) endereços IP. Portanto, é necessário reservar a sub-rede com a máscara de sub-rede de /27. 

> [!IMPORTANT]
> O cálculo exibido acima se tornará obsoleto com as melhorias futuras. 

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

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Modificar uma rede virtual existente para Instâncias Gerenciadas 

As perguntas e respostas nesta seção mostram como adicionar uma Instância Gerenciada à rede virtual existente. 

**Você está utilizando o modelo de implantação do Azure Resource Manager ou Clássico para a rede virtual existente?** 

Só é possível criar uma Instância Gerenciada nas redes virtuais do Resource Manager. 

**Você gostaria de criar uma nova sub-rede para a Instância Gerenciada do SQL ou utilizar uma existente?**

Se quiser criar uma nova: 

- Calcule o tamanho da sub-rede, seguindo as diretrizes na seção [Determinar o tamanho da sub-rede para Instâncias Gerenciadas](#determine-the-size-of-subnet-for-managed-instances).
- Siga as etapas em [Adicionar, alterar ou excluir uma sub-rede da rede virtual](../virtual-network/virtual-network-manage-subnet.md). 
- Crie uma tabela de rotas que contenha entrada única, **0.0.0.0/0**, como próximo salto para a Internet e associe-a à sub-rede para a Instância Gerenciada.  

Se você quiser criar uma Instância Gerenciada dentro de uma sub-rede existente, recomendamos o seguinte script do PowerShell para preparar a sub-rede.
```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```
A preparação da sub-rede é feita em três etapas simples:

- Validar – a sub-rede e a rede virtual selecionadas são validadas em relação aos requisitos de rede da Instância Gerenciada
- Confirmar – o usuário vê um conjunto de alterações que precisam ser feitas para preparar a sub-rede para a implantação da Instância Gerenciada e é solicitado a dar consentimento
- Preparar – a Rede virtual e a sub-rede são configuradas corretamente

**Há um servidor DNS personalizado configurado?** 

Se sim, consulte [Configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md). 

- Crie a tabela de rotas desejada e associe-a: consulte [Criar a tabela de rotas necessária e associá-la](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [O que é uma Instância Gerenciada](sql-database-managed-instance.md)
- Para um tutorial que mostra como criar uma VNet, criar uma Instância Gerenciada e restaurar um banco de dados a partir de um backup de banco de dados, consulte [Criar uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-get-started.md).
- Para problemas de DNS, consulte [Configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md)
