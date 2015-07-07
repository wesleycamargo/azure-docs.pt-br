<properties 
   pageTitle="O que é uma ACL (Lista de Controle de Acesso) de rede?"
   description="Saiba mais sobre ACLs."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2015"
   ms.author="telmos" />

# O que é uma ACL (Lista de Controle de Acesso) de rede?

Uma ACL (Lista de Controle de Acesso) de rede é um aprimoramento de segurança disponível para sua implantação do Azure. Uma ACL fornece a capacidade para permitir ou negar seletivamente o tráfego para um ponto de extremidade de máquina virtual. Essa capacidade de filtragem de pacotes proporciona uma camada adicional de segurança. Você pode especificar ACLs de rede apenas para pontos de extremidade. Não é possível especificar uma ACL para uma rede virtual ou uma sub-rede específica contida em uma rede virtual.

> [AZURE.IMPORTANT]Recomendamos que você use NSGs (Grupos de segurança de rede) em vez de ACLs sempre que possível. Para saber mais sobre NSGs, confira [O que é um Grupo de segurança de rede?](../virtual-network-nsg).

As ACLs podem ser configuradas usando o PowerShell ou o Portal de Gerenciamento. Para configurar ACLs usando o PowerShell, confira [Gerenciando ACLs (Listas de controle de acesso) para pontos de extremidade usando o PowerShell](https://msdn.microsoft.com/library/azure/dn376543.aspx). Para configurar uma ACL de rede usando o Portal de Gerenciamento, consulte [Como configurar pontos de extremidade para uma máquina virtual](../virtual-machines-set-up-endpoints/).

Com as ACLs de rede, você pode fazer o seguinte:

- Permitir ou negar seletivamente o tráfego de entrada com base no intervalo de endereços IPv4 de sub-rede remota para um ponto de extremidade de entrada de máquina virtual.

- Inserir endereços IP em uma lista negra

- Criar várias regras por ponto de extremidade de máquina virtual

- Especificar até 50 regras de ACL por ponto de extremidade de máquina virtual

- Usar a ordenação de regra para garantir que o conjunto correto de regras seja aplicado a um certo ponto de extremidade de máquina virtual (mais baixa para a mais alta)

- Especificar uma ACL para um endereço IPv4 de sub-rede remota específica.

## Como funcionam as ACLs

Uma ACL é um objeto que contém uma lista de regras. Quando você cria uma ACL e a aplica a um ponto de extremidade de máquina virtual, a filtragem de pacotes ocorre no nó do host da VM. Isso significa que o tráfego de endereços IP remotos é filtrado pelo nó do host a fim de estabelecer uma correspondência de regras de ACL na sua VM. Isso impede que a VM gaste os preciosos ciclos de CPU na filtragem de pacotes.

Quando uma máquina virtual é criada, uma ACL padrão é colocada no lugar para bloquear todo o tráfego de entrada. No entanto, se um ponto de extremidade é criado para a (porta 3389), a ACL padrão é modificada a fim de permitir todo o tráfego de entrada para esse ponto de extremidade. O tráfego de entrada de qualquer sub-rede remota recebe permissão nesse ponto de extremidade e nenhum provisionamento de firewall é necessário. Todas as outras portas são bloqueadas para tráfego de entrada, a menos que os pontos de extremidade sejam criados para essas portas. O tráfego de saída é permitido por padrão.

**Exemplo de tabela ACL padrão**

| **Nº da regra** | **Sub-rede remota** | **Ponto de extremidade** | **Permitir/Negar** |
|--------|---------------|----------|-------------|
| 100 | 0.0.0.0/0 | 3389 | Permitir |

## Permitir e negar

Você pode permitir ou negar seletivamente o tráfego de rede para um ponto de extremidade de entrada de máquina virtual criando regras que especificam "permitir" ou "negar". É importante observar que, por padrão, quando um ponto de extremidade é criado, todo o tráfego é negado para o ponto de extremidade. Por esse motivo, é importante entender como criar regras para permitir/negar e colocá-las na ordem apropriada de precedência se você quiser um controle granular sobre o tráfego de rede escolhido para acessar o ponto de extremidade de máquina virtual.

Considere o seguinte:

1. **Não ACL –** por padrão, quando um ponto de extremidade é criado, permitimos tudo para o ponto de extremidade.

1. **Permitir -** quando você adiciona um ou mais intervalos de "permissão", está negando todos os outros intervalos por padrão. Somente os pacotes do intervalo IP permitido poderão se comunicar com o ponto de extremidade de máquina virtual.

1. **Negar -** quando você adiciona um ou mais intervalos de "negação", está permitindo, por padrão, todos os outros intervalos do tráfego.

1. **Combinação de permitir e negar -** você pode usar uma combinação de "permitir" e "negar" quando quiser permitir ou negar um intervalo IP específico.

## Regras e precedência de regras

As ACLs de rede podem ser configuradas em pontos de extremidade de máquina virtual específicos. Por exemplo, você pode especificar uma ACL de rede para um ponto de extremidade RDP criado em uma máquina virtual que bloqueia o acesso de determinados endereços IP. A tabela a seguir mostra uma maneira de conceder acesso a IPs virtuais públicos (VIPs) de um determinado intervalo a fim de permitir o acesso para RDP. Todos os outros IPs remotos são negados. Seguimos uma ordem de regras em que a *mais baixa tem precedência*.

### Várias regras

No exemplo a seguir, se você quiser permitir acesso ao ponto de extremidade RDP apenas para dois intervalos de endereços IPv4 público (65.0.0.0/8 e 159.0.0.0/8), especifique duas regras *Permitir*. Nesse caso, uma vez que o RDP é criado por padrão para uma máquina virtual, convém bloquear o acesso à porta RDP com base em uma sub-rede remota. O exemplo a seguir mostra uma maneira de conceder acesso a IPs virtuais públicos (VIPs) de um determinado intervalo a fim de permitir o acesso para RDP. Todos os outros IPs remotos são negados. Isso funciona porque as ACLs de rede podem ser configuradas para um ponto de extremidade específico de máquina virtual e o acesso é negado por padrão.

**Exemplo – Várias regras**

| **Nº da regra** | **Sub-rede remota** | **Ponto de extremidade** | **Permitir/Negar** |
|--------|---------------|----------|-------------|
| 100 | 65.0.0.0/8 | 3389 | Permitir |
| 200 | 159.0.0.0/8 | 3389 | Permitir |

### Ordem das regras

Como é possível especificar várias regras para um ponto de extremidade, deve haver uma maneira de organizar regras para determinar quais regras têm precedência. A ordem das regras especifica a precedência. As ACLs de rede seguem uma ordem de regras em que a *mais baixa tem precedência*. No exemplo abaixo, o ponto de extremidade na porta 80 somente recebe seletivamente o acesso a determinados intervalos de endereços IP. Para configurar isso, temos uma regra de negação (regra número 100) para endereços no espaço 175.1.0.1/24. Uma segunda regra é especificada com precedência de 200, permitindo o acesso a todos os outros endereços em 175.0.0.0/8.

**Exemplo – Precedência de regra**

| **Nº da regra** | **Sub-rede remota** | **Ponto de extremidade** | **Permitir/Negar** |
|--------|---------------|----------|-------------|
| 100 | 175.1.0.1/24 | 80 | Negar |
| 200 | 175.0.0.0/8 | 80 | Permitir |

## ACLs de rede e conjuntos de cargas balanceadas

As ACLs de rede podem ser especificadas em um ponto de extremidade com conjunto de balanceamento de carga (Conjunto de LB). Se uma ACL for especificada para um conjunto de LB, a ACL de rede será aplicada a todas as máquinas virtuais nesse conjunto de LB. Por exemplo, se um conjunto de LB for criado com a "porta 80" e o conjunto de LB contiver 3 VMs, a ACL de rede criada no ponto de extremidade da "Porta 80" de uma VM será aplicada automaticamente às outras VMs.

![ACLs de rede e conjuntos de cargas balanceadas](./media/virtual-networks-acl/IC674733.png)

## Próximas etapas

[Como gerenciar ACLs (Listas de controle de acesso) para pontos de extremidade usando o PowerShell](../virtual-networks-acl-powershell)

<!---HONumber=62-->