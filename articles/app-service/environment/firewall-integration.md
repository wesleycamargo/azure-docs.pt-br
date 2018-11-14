---
title: Bloquear o tráfego de saída do Ambiente do Serviço de Aplicativo do Azure
description: Descreve como integrar-se com o Firewall do Azure para proteger o tráfego de saída
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: ccompy
ms.openlocfilehash: ce0123528b3fb2454d8b83d59b5916363ae0e944
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251569"
---
# <a name="locking-down-an-app-service-environment"></a>Bloqueando um Ambiente do Serviço de Aplicativo

O ASE (Ambiente de Serviço de Aplicativo) tem diversas dependências externas às quais ele requer acesso para funcionar corretamente. O ASE reside na VNet (rede virtual) do Azure do cliente. Os clientes devem permitir o tráfego de dependência do ASE, que é um problema para os clientes que desejam bloquear toda a saída da VNet.

Um ASE tem várias dependências de entrada. O tráfego de gerenciamento de entrada não pode ser enviado por meio de um dispositivo de firewall. Os endereços de origem para esse tráfego são conhecidos e publicados no documento [Endereços de gerenciamento do Ambiente do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/environment/management-addresses). Você pode criar regras de Grupo de Segurança de Rede com essas informações para proteger o tráfego de entrada.

As dependências de saída do ASE são quase que totalmente definidas com FQDNs, que não têm endereços estáticos por trás delas. A falta de endereços estáticos significa que os NSGs (Grupos de Segurança de Rede) não podem ser usados para bloquear o tráfego de saída de um ASE. Os endereços mudam com frequência suficiente para que não seja possível configurar regras com base na resolução atual e usá-las para criar NSGs. 

A solução para proteger os endereços de saída está em usar um dispositivo de firewall que possa controlar o tráfego de saída com base em nomes de domínio. Firewall do Azure pode restringir o tráfego de HTTP e HTTPS de saída com base no FQDN de destino.  

## <a name="configuring-azure-firewall-with-your-ase"></a>Como configurar o Firewall do Azure com seu ASE 

As etapas para bloquear a saída de seu ASE com o Firewall do Azure são:

1. Criar um Firewall do Azure na VNet em que seu ASE está ou estará. [Documentação do Firewall do Azure](https://docs.microsoft.com/azure/firewall/)
2. Na interface do usuário do Firewall do Azure, selecione a tag FQDN do Ambiente do Serviço de Aplicativo
3. Criar uma tabela de rotas com os endereços de gerenciamento dos [Endereços de gerenciamento do Ambiente do Serviço de Aplicativo]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) com um próximo salto da Internet. As entradas da tabela de rota são necessárias para evitar problemas de roteamento assimétrico.
4. Adicione as rotas para as dependências de endereço IP indicadas abaixo nas dependências de endereço IP com um próximo salto de Internet.
5. Adicione uma rota à sua tabela de rotas para 0.0.0.0/0 com o próximo salto sendo seu Firewall do Azure.
6. Crie Service Endpoints para sua sub-rede ASE para o Azure SQL e o Armazenamento do Microsoft Azure.
7. Atribua a tabela de rotas que você criou à sua sub-rede ASE.

## <a name="application-traffic"></a>Tráfego de aplicativo 

As etapas acima permitirão que seu ASE opere sem problemas. Você ainda precisará configurar tudo para acomodar as necessidades do seu aplicativo. Há dois problemas para aplicativos em um ASE configurado com o Firewall do Azure.  

- FQDNs de dependência de aplicativo devem ser adicionados ao Firewall do Azure ou à tabela de rotas
- As rotas devem ser criadas para os endereços dos quais o tráfego virá para evitar problemas de roteamento assimétrico

Se seus aplicativos tiverem dependências, precisarão ser adicionados ao Firewall do Azure. Crie regras de aplicativo para permitir o tráfego HTTP/HTTPS e regras de Rede para todo o resto. 

Se você souber o intervalo de endereços do qual seu tráfego de solicitação de aplicativo virá, poderá adicioná-lo à tabela de rotas atribuída à sua sub-rede do ASE. Se o intervalo de endereços for grande ou não estiver especificado, você poderá usar um dispositivo de rede, como o Gateway de Aplicativo para fornecer um endereço para adicionar à sua tabela de rotas. Para obter detalhes sobre como configurar um Gateway de Aplicativo com seu ILB ASE, leia [Como integrar seu ILB ASE a um Gateway de Aplicativo](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)


## <a name="dependencies"></a>Dependências

O Serviço de Aplicativo do Azure tem várias dependências externas. Eles podem ser categoricamente divididos em várias áreas principais:

- Serviços com capacidade de Ponto de Extremidade de Serviço deverão ser definidos com Pontos de Extremidade de Serviço se você quiser bloquear o tráfego de rede de saída.
- Pontos de extremidade de endereço IP não são abordados com um nome de domínio. Isso pode ser um problema para dispositivos de firewall que esperam que todo o tráfego HTTPS use nomes de domínio. Os pontos de extremidade de endereço IP devem ser adicionados à tabela de rotas definida na sub-rede do ASE.
- Pontos de extremidade HTTP/HTTPS do FQDN podem ser colocados em seu dispositivo de firewall.
- Pontos de extremidade HTTP/HTTPS curinga são dependências que podem variar de acordo com seu ASE com base em vários qualificadores. 
- As dependências do Linux serão uma preocupação apenas se você estiver implantando aplicativos Linux em seu ASE. Se você não estiver implantando aplicativos Linux em seu ASE, esses endereços não precisarão ser adicionados ao firewall. 


#### <a name="service-endpoint-capable-dependencies"></a>Dependências com capacidade de Ponto de Extremidade de Serviço 

| Ponto de extremidade |
|----------|
| SQL do Azure |
| Armazenamento do Azure |
| Azure KeyVault |


#### <a name="ip-address-dependencies"></a>Dependências de endereço IP 

| Ponto de extremidade |
|----------|
| 40.77.24.27:443 |
| 13.82.184.151:443 |
| 13.68.109.212:443 |
| 13.90.249.229:443 |
| 13.91.102.27:443 |
| 104.45.230.69:443 |
| 168.62.226.198:12000 |


#### <a name="fqdn-httphttps-dependencies"></a>Dependências de HTTP/HTTPS do FQDN 

| Ponto de extremidade |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80   |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Dependências de HTTP/HTTPS de curinga 

| Ponto de extremidade |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
|grmdsprod\*mini\*.servicebus.windows.net:443 |
|grmdsprod\*lini\*.servicebus.windows.net:443 |
|grsecprod\*mini\*.servicebus.windows.net:443 |
|grsecprod\*lini\*.servicebus.windows.net:443 |
|graudprod\*mini\*.servicebus.windows.net:443 |
|graudprod\*lini\*.servicebus.windows.net:443 |

#### <a name="linux-dependencies"></a>Dependências do Linux 

| Ponto de extremidade |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |

