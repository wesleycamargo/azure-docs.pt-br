---
title: Solucionar problemas de Gateway de Aplicativo no Azure – ILB ASE | Microsoft Docs
description: Saiba como solucionar problemas de um gateway de aplicativo usando um Balanceador de Carga Interno com um ambiente de serviço de aplicativo no Azure
services: vpn-gateway
documentationCenter: na
author: genlin
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: 16cfe4c1db8fe9ba4c80f6451611237e3ee12c55
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617814"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>O certificado de servidor back-end não está na lista de permissões para um gateway de aplicativo usando um Balanceador de Carga Interno com um ambiente de serviço de aplicativo

Este artigo soluciona o seguinte problema: um certificado não é permitido quando você cria um gateway de aplicativo usando um ILB (Balanceador de Carga Interno) juntamente com um ASE (Ambiente do Serviço de Aplicativo) no back-end ao usar SSL de ponta a ponta no Azure.

## <a name="symptoms"></a>Sintomas

Quando você cria um gateway de aplicativo usando um ILB com um ASE no back-end, o servidor back-end pode se tornar não íntegro. Esse problema ocorrerá se o certificado de autenticação do gateway de aplicativo não corresponder ao certificado configurado no servidor back-end. Observe o seguinte cenário como um exemplo:

**Configuração do Gateway de Aplicativo:**

- **Ouvinte:** Multissite
- **Porta:** 443
- **Nome do host:** test.appgwtestase.com
- **Certificado SSL:** CN=test.appgwtestase.com
- **Pool de back-end:** endereço IP ou FQDN
- **Endereço IP:**: 10.1.5.11
- **Configurações de HTTP:** HTTPS
- **Porta:**: 443
- **Investigação personalizada:** nome do host – test.appgwtestase.com
- **Certificado de autenticação:** .cer do test.appgwtestase.com
- **Integridade de back-end:** Não íntegro – O certificado do servidor back-end não está na lista de permissões do Gateway de Aplicativo.

**Configuração do ASE:**

- **IP do ILB:** 10.1.5.11
- **Nome de domínio:** appgwtestase.com
- **Serviço de Aplicativo:** test.appgwtestase.com
- **Associação SSL:** SNI SSL – CN=test.appgwtestase.com

Ao acessar o gateway de aplicativo, você receberá a seguinte mensagem de erro porque o servidor back-end está não íntegro:

**502 – o servidor Web recebeu uma resposta inválida ao atuar como gateway ou servidor proxy.**

## <a name="solution"></a>Solução

Quando você não usar um nome de host para acessar um site HTTPS, o servidor back-end retornará o certificado configurado no site padrão. Para um ILB ASE, o certificado padrão é fornecido pelo certificado ILB. Se não houver certificados configurados para o ILB, o certificado será fornecido pelo aplicativo ASE.

Quando você usar um FQDN (nome de domínio totalmente qualificado) para acessar o ILB, o servidor back-end retornará o certificado correto que será carregado nas configurações de HTTP. Nesse caso, considere as seguintes opções:

- Use o FQDN no pool de back-end do gateway de aplicativo para apontar para o endereço IP do ILB. Essa opção somente funcionará se você tiver uma zona DNS privada ou um DNS personalizado configurado. Caso contrário, será necessário criar um registro "A" para um DNS público.

- Use o certificado carregado no ILB ou o certificado padrão nas configurações de HTTP. O gateway de aplicativo obterá o certificado quando acessar o IP do ILB para a investigação.

- Use um certificado curinga no ILB e no servidor back-end.

- Desmarque a opção **Usar para serviço de aplicativo** do gateway de aplicativo.

Para reduzir a sobrecarga, você poderá carregar o certificado ILB nas configurações de HTTP para fazer com que o caminho da investigação funcione. (Esta etapa é apenas para lista de permissões. Não será usada para comunicação SSL.) É possível recuperar o certificado ILB, acessando o ILB com o endereço IP em HTTPS, em seguida, exportando o certificado SSL em um formato de CER codificado com Base 64 e carregando o certificado nas respectivas configurações de HTTP.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
