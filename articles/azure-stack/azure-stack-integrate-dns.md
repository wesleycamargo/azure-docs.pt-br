---
title: "Integração do data center do Azure pilha - DNS"
description: Saiba como integrar o DNS de pilha do Azure com seu datacenter DNS
services: azure-stack
author: jeffgilb
ms.service: azure-stack
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: 
ms.openlocfilehash: 504cbabe6ea4b7ad71601186dac853515f8c4709
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-stack-datacenter-integration---dns"></a>Integração do data center do Azure pilha - DNS
Para poder acessar pontos de extremidade de pilha do Azure (`portal`, `adminportal`, `management`, `adminmanagement`, etc.)  da pilha fora do Azure, você precisa integrar os serviços DNS de pilha do Azure com os servidores DNS que hospedam as zonas DNS que você deseja usar na pilha do Azure.

## <a name="azure-stack-dns-namespace"></a>Namespace DNS da pilha do Azure
É necessário fornecer algumas informações importantes relacionadas ao DNS quando você implanta a pilha do Azure.


|Campo  |DESCRIÇÃO  |Exemplo|
|---------|---------|---------|
|Região|A localização geográfica de sua implantação de pilha do Azure.|`east`|
|Nome de domínio externo|O nome da zona que você deseja usar para sua implantação de pilha do Azure.|`cloud.fabrikam.com`|
|Nome de domínio interno|O nome da zona interno que é usada para serviços de infraestrutura na pilha do Azure.  É integrada ao serviço de diretório e privados (não pode ser acessado de fora da implantação da pilha do Azure).|`azurestack.local`|
|Encaminhador de DNS|Servidores DNS que são usados para encaminhar consultas DNS, zonas DNS e os registros que são hospedados fora do Azure pilha, tanto na intranet corporativa ou na internet pública.|`10.57.175.34`<br>`8.8.8.8`|
|Prefixo de nome (opcional)|O prefixo de nomenclatura que você deseja que seus nomes de máquina do Azure pilha infraestrutura função instância tenha.  Se não for fornecido, o padrão é `azs`.|`azs`|

O nome de domínio totalmente qualificado (FQDN) de sua implantação de pilha do Azure e os pontos de extremidade é a combinação do parâmetro região e o parâmetro de nome de domínio externo. Usando os valores de exemplos na tabela anterior, o FQDN para essa implantação de pilha do Azure seria o seguinte nome:

`east.cloud.fabrikam.com`

Como tal, exemplos de alguns dos pontos de extremidade para essa implantação pareceria com as seguintes URLs:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Para usar esse namespace DNS de exemplo para uma implantação de pilha do Azure, as seguintes condições são necessárias:

- A zona `fabrikam.com` está registrado com um registrador de domínio, um servidor DNS corporativo interno ou ambos, dependendo dos requisitos de resolução de nome.
- O domínio filho `cloud.fabrikam.com` existe na zona `fabrikam.com`.
- Os servidores DNS que hospedam as zonas `fabrikam.com` e `cloud.fabrikam.com` pode ser acessada a partir da implantação da pilha do Azure.

Para ser capaz de resolver nomes DNS para pontos de extremidade de pilha do Azure e as instâncias da pilha fora do Azure, você precisa integrar os servidores DNS que hospedam a zona DNS externa para a pilha do Azure com os servidores DNS que hospedam a zona pai que você deseja usar.


## <a name="resolution-and-delegation"></a>Resolução e delegação

Existem dois tipos de servidores DNS:

- Um servidor DNS autoritativo hospeda as zonas DNS. Ele responde a consultas DNS apenas para registros nessas zonas.
- Um servidor DNS recursivo não hospeda as zonas DNS. Ele responde a todas as consultas DNS chamando os servidores DNS autoritativos para coletar os dados de que precisa.

A pilha do Azure inclui tanto autoritativo e servidores DNS recursivo. Os servidores recursiva são usados para resolver nomes de tudo, exceto a zona privada interna e externa zona DNS pública para que a implantação do Azure pilha. 

![Arquitetura de pilha DNS do Azure](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Resolver nomes DNS externos da pilha do Azure

Para resolver nomes DNS para pontos de extremidade fora da pilha do Azure (por exemplo: www.bing.com), você precisa fornecer os servidores DNS que pilha do Azure pode usar para encaminhar solicitações DNS para o qual a pilha do Azure não está autorizada. Para implantação, os servidores DNS do Azure pilha encaminha solicitações para são necessários na planilha de implantação (no campo encaminhador de DNS). Forneça pelo menos dois servidores nesse campo para tolerância a falhas. Sem esses valores, a implantação de pilha do Azure falhará.

### <a name="configure-conditional-dns-forwarding"></a>Configurar o encaminhamento condicional do DNS

> [!IMPORTANT]
> Isso se aplica apenas a uma implantação do AD FS.

Para habilitar a resolução de nomes com a infraestrutura DNS existente, configure o encaminhamento condicional.

Para adicionar um encaminhador condicional, você deve usar o ponto de extremidade com privilégios.

Para esse procedimento, use um computador em sua rede de datacenter que pode se comunicar com o ponto de extremidade com privilégios na pilha do Azure.

1. Abra uma sessão do Windows PowerShell com privilégios elevados (Executar como administrador) e conecte-se para o endereço IP do ponto de extremidade com privilégios. Use as credenciais para autenticação de CloudAdmin.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Depois de se conectar ao ponto de extremidade com privilégios, execute o seguinte comando do PowerShell. Substitua os valores de exemplo fornecidos com o nome de domínio e endereços IP dos servidores DNS que você deseja usar.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Resolução de nomes DNS de pilha do Azure da pilha fora do Azure
Os servidores autoritativos são aqueles que contêm as informações de zona DNS externas e as zonas criadas pelo usuário. Integre com esses servidores para habilitar a delegação de zona ou encaminhamento condicional para resolver nomes DNS de pilha do Azure da pilha fora do Azure.

## <a name="get-dns-server-external-endpoint-information"></a>Obter informações de ponto de extremidade externo do servidor DNS

Para integrar a implantação de pilha do Azure com sua infraestrutura DNS, você precisa das seguintes informações:

- Servidor DNS FQDNs
- Endereços IP do servidor DNS

Os FQDNs para os servidores DNS da pilha do Azure têm o seguinte formato:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

Servidores usando os valores de exemplo, os FQDNs para o DNS são:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Essas informações também são criadas no final de todas as implantações de pilha do Azure em um arquivo chamado `AzureStackStampDeploymentInfo.json`. Esse arquivo está localizado no `C:\CloudDeployment\logs` pasta da máquina virtual implantação. Se você não tiver certeza de que os valores que foram usados para sua implantação de pilha do Azure, você pode obter os valores aqui.

Se a máquina virtual de implantação não está mais disponível ou está inacessível, você pode obter os valores se conectar ao ponto de extremidade com privilégios e executando o `Get-AzureStackInfo` cmdlet do PowerShell. Para obter mais informações sobre o ponto de extremidade com privilégios, consulte (inserir) link para o artigo aqui.

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Configurando o encaminhamento condicional a pilha do Azure

É a maneira mais simples e segura para integrar a pilha do Azure com sua infraestrutura DNS fazer o encaminhamento condicional da zona do servidor que hospeda a zona pai. Essa abordagem é recomendada se você tem controle direto sobre os servidores DNS que hospedam a zona pai para seu namespace DNS externo de pilha do Azure.

Se você não estiver familiarizado com como fazer o encaminhamento condicional com o DNS, consulte o seguinte artigo do TechNet: [atribuir um encaminhador condicional para um nome de domínio](https://technet.microsoft.com/library/cc794735), ou a documentação específica referente à sua solução DNS.

Em cenários em que você especificou a zona de DNS de pilha do Azure externos com a aparência de um domínio filho de seu nome de domínio corporativo, o encaminhamento condicional não pode ser usado. Delegação de DNS deve ser configurada.

Exemplo:

- Nome de domínio DNS corporativo:`contoso.com`
- Nome de domínio do DNS externo de pilha do Azure:`azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Delegação de zona DNS externa a pilha do Azure

Para nomes DNS a ser resolvido por meio da fora de uma implantação de pilha do Azure, você precisa configurar a delegação de DNS.

Cada registrador tem suas próprias ferramentas de gerenciamento de DNS para alterar os registros de servidor de nomes para um domínio. Na página de gerenciamento do DNS do registrador, editar os registros NS e substitua os registros NS para a zona com aqueles na pilha do Azure.

A maioria dos registradores DNS exigem que você fornecer um mínimo de dois servidores DNS para concluir a delegação.

## <a name="next-steps"></a>Próximas etapas

[Integração do firewall](azure-stack-firewall.md)
