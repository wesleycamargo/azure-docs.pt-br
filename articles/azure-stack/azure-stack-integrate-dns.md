---
title: Integração do datacenter do Azure Stack - DNS
description: Saiba como integrar o DNS do Azure Stack ao seu datacenter DNS
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
keywords: ''
ms.openlocfilehash: 13525fffb7e6720fe81759876ffd0fe71559279c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182843"
---
# <a name="azure-stack-datacenter-integration---dns"></a>Integração do datacenter do Azure Stack - DNS
Ser capaz de acessar pontos de extremidade do Azure Stack (**portal**, **adminportal**, **gerenciamento**, **adminmanagement**, etc.)  de fora do Azure Stack, você precisa integrar os serviços de DNS do Azure Stack com os servidores DNS que hospedam as zonas DNS que você deseja usar no Azure Stack.

## <a name="azure-stack-dns-namespace"></a>Namespace de DNS de pilha do Azure
Será necessário fornecer algumas informações importantes relacionadas ao DNS quando você implanta o Azure Stack.


|Campo  |DESCRIÇÃO  |Exemplo|
|---------|---------|---------|
|Região|A localização geográfica da sua implantação do Azure Stack.|`east`|
|Nome de domínio externo|O nome da zona que você deseja usar para sua implantação do Azure Stack.|`cloud.fabrikam.com`|
|Nome de domínio interno|O nome da zona interno que é usado para serviços de infraestrutura no Azure Stack.  Ele é integrado ao serviço de diretório e privado (não pode ser acessado de fora a implantação do Azure Stack).|`azurestack.local`|
|Encaminhador de DNS|Servidores DNS que são usados para encaminhar consultas DNS, zonas e registros DNS que são hospedados fora do Azure Stack, seja na intranet corporativa ou na internet pública.|`10.57.175.34`<br>`8.8.8.8`|
|Nomenclatura de prefixo (opcional)|O prefixo de nomenclatura que você deseja que seus nomes de máquina do Azure Stack infraestrutura função instância ter.  Se não for fornecido, o padrão é `azs`.|`azs`|

O nome de domínio totalmente qualificado (FQDN) de sua implantação do Azure Stack e pontos de extremidade é a combinação do parâmetro de região e o parâmetro de nome de domínio externo. Usando os valores dos exemplos na tabela anterior, o FQDN para essa implantação do Azure Stack seria o seguinte nome:

`east.cloud.fabrikam.com`

Como tal, exemplos de alguns dos pontos de extremidade para essa implantação seriam semelhante as URLs a seguir:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Para usar esse namespace DNS de exemplo para uma implantação do Azure Stack, as condições a seguir são necessárias:

- A zona `fabrikam.com` está registrado com um registrador de domínio, um servidor DNS corporativo interno ou ambos, dependendo dos requisitos de resolução de nome.
- O domínio filho `cloud.fabrikam.com` existe sob a zona `fabrikam.com`.
- Os servidores DNS que hospedam as zonas `fabrikam.com` e `cloud.fabrikam.com` pode ser acessado da implantação do Azure Stack.

Para ser capaz de resolver nomes DNS para pontos de extremidade do Azure Stack e instâncias de fora do Azure Stack, você precisa integrar os servidores DNS que hospedam a zona DNS externa para o Azure Stack com os servidores DNS que hospedam a zona pai que você deseja usar.


## <a name="resolution-and-delegation"></a>Resolução e delegação

Existem dois tipos de servidores DNS:

- Um servidor DNS autoritativo hospeda zonas DNS. Ele responde a consultas DNS apenas para registros nessas zonas.
- Um servidor DNS recursivo não hospeda zonas DNS. Ele responde a todas as consultas DNS chamando os servidores DNS autoritativos para coletar os dados de que precisa.

O Azure Stack inclui tanto autoritativo e servidores DNS recursivos. Os servidores recursivas são usados para resolver nomes de tudo, exceto para a zona privada interna e a zona DNS pública externa para que a implantação do Azure Stack. 

![Arquitetura de pilha DNS do Azure](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Resolver nomes DNS externos do Azure Stack

Para resolver nomes DNS para pontos de extremidade fora do Azure Stack (por exemplo: www.bing.com), você precisa fornecer os servidores DNS que pode usar o Azure Stack para encaminhar solicitações DNS para o qual o Azure Stack não está autorizado. Para a implantação, servidores DNS que encaminha as solicitações para Azure Stack são necessários na planilha de implantação (no campo de encaminhador DNS). Forneça pelo menos dois servidores nesse campo para tolerância a falhas. Sem esses valores, a implantação do Azure Stack falhará.

### <a name="configure-conditional-dns-forwarding"></a>Configurar o encaminhamento condicional do DNS

> [!IMPORTANT]
> Isso se aplica somente a uma implantação do AD FS.

Para habilitar a resolução de nomes com a infraestrutura DNS existente, configure o encaminhamento condicional.

Para adicionar um encaminhador condicional, você deve usar o ponto de extremidade com privilégios.

Para este procedimento, use um computador em sua rede de datacenter que pode se comunicar com o ponto de extremidade privilegiado no Azure Stack.

1. Abra uma sessão do Windows PowerShell com privilégios elevados (Executar como administrador) e conecte-se para o endereço IP do ponto de extremidade com privilégios. Use as credenciais para autenticação CloudAdmin.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Depois de se conectar ao ponto de extremidade com privilégios, execute o seguinte comando do PowerShell. Substitua os valores de exemplo fornecidos com seu nome de domínio e endereços IP dos servidores DNS que você deseja usar.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Resolução de nomes DNS de pilha do Azure de fora do Azure Stack
Os servidores autoritativos são aqueles que mantêm as informações de zona DNS externas e todas as zonas criados pelo usuário. Integre com esses servidores para habilitar a delegação de zona ou encaminhamento condicional para resolver nomes DNS de pilha do Azure de fora do Azure Stack.

## <a name="get-dns-server-external-endpoint-information"></a>Obter informações de ponto de extremidade externo do servidor DNS

Para integrar sua implantação do Azure Stack com sua infraestrutura DNS, você precisa ter as seguintes informações:

- Servidor DNS FQDNs
- Endereços IP do servidor DNS

Os FQDNs para os servidores DNS do Azure Stack tem o seguinte formato:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

Servidores usando os valores de exemplo, os FQDNs para o DNS são:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Essas informações também são criadas no final de todas as implantações do Azure Stack em um arquivo chamado `AzureStackStampInformation.json`. Esse arquivo está localizado no `C:\CloudDeployment\logs` pasta da máquina de virtual de implantação. Se você não tiver certeza quais valores foram usados para sua implantação do Azure Stack, você pode obter os valores a partir daqui.

Se a máquina virtual de implantação não está mais disponível ou está inacessível, você pode obter os valores se conectar ao ponto de extremidade com privilégios e executando o `Get-AzureStackStampInformation` cmdlet do PowerShell. Para obter mais informações, consulte [ponto de extremidade com privilégios](azure-stack-privileged-endpoint.md).

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Como configurar o encaminhamento condicional para o Azure Stack

A maneira mais simples e segura para integrar o Azure Stack com sua infraestrutura DNS é fazer o encaminhamento condicional da zona do servidor que hospeda a zona pai. Essa abordagem é recomendada se você tem controle direto sobre os servidores DNS que hospedam a zona pai para seu namespace DNS externo do Azure Stack.

Se você não estiver familiarizado com como fazer condicional de encaminhamento de DNS, consulte o seguinte artigo do TechNet: [Atribuir um encaminhador condicional para um nome de domínio](https://technet.microsoft.com/library/cc794735), ou a documentação específica para sua solução de DNS.

Em cenários em que você especificou que a zona de DNS de pilha do Azure externo para se parecer com um domínio filho de seu nome de domínio corporativo, o encaminhamento condicional não pode ser usado. Delegação de DNS deve ser configurada.

Exemplo:

- Nome de domínio DNS corporativo: `contoso.com`
- Nome de domínio DNS externo do Azure Stack: `azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Delegar a zona DNS externa para o Azure Stack

Para nomes DNS a ser resolvido de fora de uma implantação do Azure Stack, você precisa configurar a delegação de DNS.

Cada registrador tem suas próprias ferramentas de gerenciamento de DNS para alterar os registros de servidor de nomes para um domínio. Na página de gerenciamento do DNS do registrador, edite os registros NS e substitua os registros NS para a zona com aqueles no Azure Stack.

A maioria dos registradores DNS exigem que você fornecer um mínimo de dois servidores DNS para concluir a delegação.

## <a name="next-steps"></a>Próximas etapas

[Integração do firewall](azure-stack-firewall.md)
