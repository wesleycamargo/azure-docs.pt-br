---
title: "Perguntas frequentes sobre as funções dos Serviços de Nuvem do Azure | Microsoft Docs"
description: "Perguntas frequentes sobre os Serviços de Nuvem do Azure. Encontre respostas para algumas perguntas comuns sobre certificados, funções da Web e funções de trabalho."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: a0fd75e0797319aecac29b48df75e7a268063e04
ms.lasthandoff: 04/11/2017


---
# <a name="cloud-services-faq"></a>Perguntas frequentes sobre Serviços de Nuvem
Este artigo responde a algumas perguntas frequentes sobre os Serviços de Nuvem do Microsoft Azure. Você também pode visitar as [Perguntas frequentes de suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para informações gerais sobre preços e suporte do Azure. Você também pode consultar a [página de tamanho de VM de Serviços de Nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

## <a name="certificates"></a>Certificados
### <a name="where-should-i-install-my-certificate"></a>Onde devo instalar o certificado?
* **Meu**  
  Certificado do aplicativo com a chave privada (\*. pfx, \*. p12).
* **AC**  
  Todos os certificados intermediários ficam neste repositório (política e Sub ACs).
* **RAIZ**  
  O repositório de AC raiz, para que seu certificado de AC raiz seja inserido aqui.

### <a name="i-cant-remove-expired-certificate"></a>Não é possível remover o certificado expirado
Azure impede a remoção de um certificado enquanto ele está em uso. É necessário excluir a implantação que usa o certificado ou atualizá-la com um certificado diferente ou renovado.

### <a name="delete-an-expired-certificate"></a>Excluir um certificado expirado
Enquanto o certificado não está em uso, você pode usar o cmdlet do PowerShell [AzureCertificate remover](https://msdn.microsoft.com/library/azure/mt589145.aspx) para remover um certificado.

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>Tenho certificados expirados denominados Gerenciamento de Serviço do Microsoft Azure para Extensões
Esses certificados são criados sempre que uma extensão é adicionada ao serviço de nuvem, como a extensão de área de trabalho remota. Esses certificados são usados apenas para criptografar e descriptografar a configuração privada da extensão. Não importa se esses certificados expiram. A data de validade não é verificada.

### <a name="certificates-i-have-deleted-keep-reappearing"></a>Certificados que excluí continuam reaparecendo
Eles continuam reaparecendo muito provavelmente devido a uma ferramenta que você está usando, como o Visual Studio. Sempre que você se reconectar com uma ferramenta que está usando um certificado, ele será carregado novamente no Azure.

### <a name="my-certificates-keep-disappearing"></a>Meus certificados continuam desaparecendo
Quando a instância de máquina virtual for reciclada, todas as alterações locais serão perdidas. Use um [tarefa de inicialização](cloud-services-startup-tasks.md) para instalar certificados na máquina virtual sempre que a função for iniciada.

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>Não consigo encontrar meus certificados de gerenciamento no portal
Os [Certificados de gerenciamento](../azure-api-management-certs.md) estão disponíveis apenas no Portal Clássico do Azure. O Portal do Azure atual não usa certificados de gerenciamento. 

### <a name="how-can-i-disable-a-management-certificate"></a>Como desabilitar um certificado de gerenciamento?
[certificados de gerenciamento](../azure-api-management-certs.md) não podem ser desabilitados. Exclua-os no Portal Clássico do Azure quando você não quiser mais usá-los.

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address"></a>Como criar um certificado SSL para um endereço IP específico?
Siga as instruções no [tutorial Criar um certificado](cloud-services-certs-create.md). Use o endereço IP como o nome DNS.

## <a name="security"></a>Segurança
### <a name="disable-ssl-30"></a>Desabilitar SSL 3.0
Para desabilitar o SSL 3.0 e usar a segurança TLS, crie uma tarefa de inicialização que está documentada nesta postagem de blog: https://azure.microsoft.com/en-us/blog/how-to-disable-ssl-3-0-in-azure-websites-roles-and-virtual-machines/

### <a name="add-nosniff-to-your-website"></a>Adicionar **nosniff** ao seu site
Para evitar que clientes detectem os tipos MIME, adicione uma configuração ao arquivo *web.config*.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

Ela também pode ser adicionada como uma configuração no IIS. Use o comando abaixo com o artigo [tarefas comuns de inicialização](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="customize-iis-for-a-web-role"></a>Personalizar o IIS para uma função web
Use o script de inicialização do IIS do artigo [tarefas comuns de inicialização](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

## <a name="scaling"></a>Dimensionamento
### <a name="i-cannot-scale-beyond-x-instances"></a>Não consigo dimensionar além de X instâncias
Sua Assinatura do Azure tem um limite no número de núcleos que você pode usar. O dimensionamento não funcionará se você tiver usado todos os núcleos disponíveis. Por exemplo, se você tiver um limite de 100 núcleos, isso significa você poderia ter 100 instâncias de máquina virtual A1 dimensionadas para seu serviço de nuvem ou 50 instâncias de máquina virtual A2.

## <a name="networking"></a>Rede
### <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Eu não consigo reservar um IP em um serviço de nuvem de vários VIPs
Primeiro, certifique-se de que a instância de máquina virtual que você está tentando reservar o IP esteja ativada. Segundo, certifique-se de que você esteja usando IPs reservados para as implantações de preparo e produção. **Não** altere as configurações enquanto a implantação é atualizada.

## <a name="remote-desktop"></a>Área de trabalho remota
### <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>Como acesso a área de trabalho remota quando tenho um NSG?
Adicione regras para o NSG que permitem o tráfego nas portas **3389** e **20000**.  A Área de Trabalho Remota usa a porta **3389**.  Instâncias de serviço de nuvem têm a carga balanceada, de modo que você não pode controlar diretamente a escolha da instância à qual se conectar.  Os agentes *RemoteForwarder* e *RemoteAccess* gerenciam o tráfego RDP e permitem que o cliente envie um cookie RDP e especifique uma instância individual à qual se conectar.  Os agentes *RemoteForwarder* e *RemoteAccess* exigem que essa porta **20000*** esteja aberta, a qual poderá ser bloqueada se você tiver um NSG.

