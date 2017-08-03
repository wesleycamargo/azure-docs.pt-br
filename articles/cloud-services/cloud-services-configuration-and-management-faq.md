---
title: "Problemas de configuração e gerenciamento para perguntas frequentes sobre Serviços de Nuvem do Microsoft Azure | Microsoft Docs"
description: "Este artigo lista as perguntas frequentes sobre a configuração e o gerenciamento de Serviços de Nuvem do Microsoft Azure."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 7d7676be26a8b68ab9fda18388e2b8cd5ed5f60b
ms.contentlocale: pt-br
ms.lasthandoff: 06/15/2017


---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de configuração e gerenciamento para Serviços de Nuvem do Azure: perguntas frequentes

Este artigo inclui perguntas frequentes sobre a configuração e o gerenciamento de [Serviços de Nuvem do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Você também pode consultar a [página de tamanho de VM de Serviços de Nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-add-nosniff-to-my-website"></a>Como fazer para adicionar "nosniff" ao meu site?
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

## <a name="how-do-i-customize-iis-for-a-web-role"></a>Como fazer para personalizar o IIS para uma função web?
Use o script de inicialização do IIS do artigo [tarefas comuns de inicialização](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

## <a name="i-cannot-scale-beyond-x-instances"></a>Não consigo dimensionar além de X instâncias
Sua Assinatura do Azure tem um limite no número de núcleos que você pode usar. O dimensionamento não funcionará se você tiver usado todos os núcleos disponíveis. Por exemplo, se você tiver um limite de 100 núcleos, isso significa você poderia ter 100 instâncias de máquina virtual A1 dimensionadas para seu serviço de nuvem ou 50 instâncias de máquina virtual A2.

## <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Como implementar Acesso Baseado em Função para Serviços de Nuvem?
Serviços de Nuvem não dão suporte ao modelo RBAC (Controle de Acesso Baseado em Função), uma vez que ele não é um serviço do baseado no Azure Resource Manager.

Consulte [RBAC do Azure versus administradores de assinatura clássica](../active-directory/role-based-access-control-what-is.md#azure-rbac-vs-classic-subscription-administrators).

## <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Como fazer para configurar o tempo limite para o Azure Load Balancer?
Você pode especificar o tempo limite no arquivo de definição de serviço (csdef) assim:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Consulte [Novo: tempo limite de ociosidade configurável para o Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) para obter mais informações.

## <a name="can-microsoft-internal-engineers-rdp-to-cloud-service-instances-without-permission"></a>Os engenheiros internos da Microsoft podem fazer RDP para instâncias de serviço de nuvem sem permissão?
A Microsoft segue um processo estrito que não permite que os engenheiros internos façam RDP em seu serviço de nuvem sem permissão por escrito (email ou outra comunicação por escrito) do proprietário ou seu representante.

## <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Por que a cadeia de certificados do meu certificado SSL de serviço de nuvem está incompleta?
É recomendável que os clientes instalem uma cadeia de certificados completa (certificado de folha, certificados intermediários e certificado raiz), em vez de apenas o certificado de folha. Ao instalar apenas o certificado de folha, você confia no Windows para criar a cadeia de certificados percorrendo a CTL. Se ocorrerem problemas intermitentes de rede ou DNS no Azure ou no Windows Update quando o Windows estiver tentando validar o certificado, o certificado poderá ser considerado inválido. Ao instalar a cadeia de certificados completa, esse problema pode ser evitado. O blog em [Como instalar um certificado SSL encadeado](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) mostra como fazer isso.

## <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Como fazer para associar um endereço IP estático ao meu serviço de nuvem?
Para configurar um endereço IP estático, você precisa criar um IP reservado. Esse IP reservado pode ser associado a um novo serviço de nuvem ou a uma implantação existente. Consulte os documentos a seguir para obter detalhes:
* [Como criar um endereço IP reservado](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Reservar o endereço IP de um serviço de nuvem existente](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Associar um IP reservado a um novo serviço de nuvem](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Associar um IP reservado a uma implantação em execução](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Associar um IP reservado a um serviço de nuvem usando um arquivo de configuração de serviço](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

## <a name="what-is-the-quota-limit-for-my-cloud-service"></a>O que é o limite de cota para meu serviço de nuvem?
Consulte [Limites específicos do serviço](../azure-subscription-service-limits.md#subscription-limits).

## <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Por que a unidade na VM do meu serviço de nuvem mostra muito pouco espaço livre em disco?
Esse comportamento é esperado e não deve causar nenhum problema ao seu aplicativo. Registro em log está ativado para a unidade %uproot% em VMs virtuais de PaaS do Azure, que consome basicamente o dobro da quantidade de espaço que os arquivos normalmente ocupam. No entanto, há vários elementos a serem considerados que essencialmente transformam isso em um não problema.

O tamanho da unidade %approot% é calculado como <tamanho do .cspkg + tamanho máximo do diário + margem de espaço livre> ou 1,5 GB, o que for maior. O tamanho da sua VM não tem influência sobre esse cálculo. (O tamanho da VM só afeta o tamanho da unidade C: temporária.) 

Não tem suporte para gravar na unidade %approot%. Se você estiver gravando na VM do Azure, deverá fazer isso em um recurso LocalStorage temporário (ou outra opção, como o armazenamento de Blobs, Arquivos do Azure etc.). Portanto, a quantidade de espaço livre na pasta %approot% não é significativa. Se você não tiver certeza de que seu aplicativo está gravando na unidade %approot%, poderá sempre permitir que o serviço seja executado por alguns dias e, em seguida, comparar os tamanhos "antes" e "depois". 

O Azure não gravará nada na unidade %approot%. Quando o VHD tiver sido criado do seu .cspkg e montado na VM do Azure, você poderá gravar apenas seu aplicativo nessa unidade. 

As definições do diário são não configuráveis, assim, não é possível desligá-las.

## <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Quais são os recursos e as funcionalidades que o DDOS e o IPS/IDS básicos do Azure oferecem?
O Azure tem IPS/IDS nos servidores físicos do datacenter para proteger-se contra ameaças. Além disso, os clientes podem implantar soluções de segurança de terceiros, como firewalls de aplicativo Web, firewalls de rede, antimalware, IDS/IPS (sistema de detecção de intrusão/sistema de prevenção de intrusão) e muito mais. Para obter mais informações, consulte [Proteger seus dados e ativos e cumprir padrões de segurança globais](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

A Microsoft monitora continuamente servidores, redes e aplicativos para detectar ameaças. A abordagem de gerenciamento de ameaças em várias frentes do Azure, usa detecção de intrusão, prevenção de ataque de DDoS (negação de serviço distribuída), teste de penetração, análise de comportamento, detecção de anomalias e machine learning para reforçar constantemente sua defesa e reduzir os riscos. O Microsoft Antimalware para Azure protege os serviços de nuvem e as máquinas virtuais do Azure. Você tem a opção de implantar soluções de segurança de terceiros adicionalmente, como firewalls de aplicativo Web, firewalls de rede, antimalware, IDS/IPS (sistema de detecção de intrusão/sistema de prevenção de intrusão) e muito mais.

## <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Por que o IIS para de gravar no diretório de log?
Você esgotou a cota de armazenamento local para gravar no diretório de log. Para corrigir isso, você pode executar uma destas três ações:
* Habilitar o diagnóstico para o IIS e mover o diagnóstico periodicamente para o armazenamento de Blobs.
* Remover manualmente os arquivos de log do diretório de log.
* Aumentar os limite de cota para recursos locais.

Para obter mais informações, consulte um dos seguintes documentos:
* [Armazenar e exibir dados de diagnóstico no Armazenamento do Azure](cloud-services-dotnet-diagnostics-storage.md)
* [Logs do IIS param de gravar no serviço de nuvem](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

## <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Qual é a finalidade do "Certificado de Criptografia das Ferramentas do Microsoft Azure para Extensões"?
Esses certificados são criados automaticamente sempre que uma extensão é adicionada ao serviço de nuvem. Normalmente, essa é a extensão WAD ou a extensão RDP, mas pode ser pessoas, como a extensão Antimalware ou Coletor de Log. Esses certificados são usados apenas para criptografar e descriptografar a configuração privada da extensão. A data de validade nunca é verificada, assim, não importa se o certificado expirou. 

Você pode ignorar esses certificados. Se você quiser limpar os certificados, poderá tentar excluir todos eles. O Azure emitirá um erro se você tentar excluir um certificado que esteja em uso.

## <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Como posso gerar uma CSR (Solicitação de Assinatura de Certificado) sem fazer "RDP" para a instância?
Consulte o documento de diretrizes a seguir:

>[Como obter um certificado para uso com o WAWS (Sites do Microsoft Azure)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

Observe que uma CSR é apenas um arquivo de texto. Ela NÃO precisa ser criada no computador em que o certificado, por fim, será usado. Embora este documento tenha sido escrito para um Serviço de Aplicativo, a criação de CSR é genérica e também se aplica a Serviços de Nuvem.

