---
title: Manipulação de dados e log de pilha do Azure | Microsoft Docs
description: Saiba mais sobre como o Azure Stack coleta informações.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2019
ms.author: PatAltimore
ms.reviewer: chengwei
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 7beb9dc31e8987d69c2cf9cf64be36caf0e0e308
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56318935"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Manipulação de dados de log e o cliente do Azure do Stack 
*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*  

A extensão Microsoft é um processador ou subprocessor dos dados pessoais em conexão com o Azure Stack, Microsoft faz para todos os clientes, efetivos 25 de maio de 2018, os compromissos no (a) no "processamento dos dados pessoais. Provisionar GDPR"da seção de"Termos de proteção de dados"a [termos de serviços Online](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551078818&sdata=cpWsfZTBHpqEFr50DWQOryq342U8shgeFgMXVPQz5ug%3D&reserved=0) e (b) na União Europeia gerais dados proteção regulamento condições em 4 de anexo do [termos de serviços Online](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551088813&sdata=bv1CBiaCnYmjiv6S0dFCbWEd4fNCkPBjBwgylNa%2FNt0%3D&reserved=0). 

Como o Azure Stack reside em datacenters do cliente, a Microsoft é o controlador de dados exclusivamente dos dados que são compartilhados com a Microsoft por meio [diagnósticos](azure-stack-diagnostics.md), [telemetria](azure-stack-telemetry.md), e [decobrança](azure-stack-usage-reporting.md).  

## <a name="data-access-controls"></a>Controles de acesso de dados 
Funcionários da Microsoft, que são atribuídos para investigar um caso de suporte específico, serão concedidos acesso somente leitura aos dados criptografados. Os funcionários da Microsoft também tem acesso a ferramentas que podem ser usados para excluir os dados, se necessário. Todo o acesso aos dados do cliente é auditado e registrado.  

Controles de acesso a dados:
1.  Dados só são mantidos por um máximo de 90 dias após caso feche.
2.  O cliente sempre tem a opção de ter os dados removidos a qualquer momento no período de 90 dias.
3.  Os funcionários da Microsoft recebem acesso a dados caso a caso e somente quando necessário para ajudar a resolverem o problema de suporte. 
4.  No evento em que a Microsoft deve compartilhar os dados do cliente com parceiros de OEM, consentimento do cliente é obrigatório.  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>O que os clientes fazem de controles de solicitações de entidade de dados (DSR) tem?
Como mencionado anteriormente, a Microsoft oferece suporte a exclusão de dados sob demanda por solicitação do cliente. Os clientes podem solicitar que o suporte do nosso engenheiro excluir todos os seus logs para um determinado caso a qualquer momento da escolha do cliente, antes que os dados são apagados permanentemente.  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>Microsoft notificar os clientes quando os dados são excluídos?
Para a ação de exclusão de dados automatizado (90 dias depois de fechar caso), podemos não proativamente chegar aos clientes e notificá-los sobre a exclusão. 

Para a ação de exclusão de dados sob demanda, engenheiro de suporte da Microsoft tem acesso à ferramenta onde eles podem iniciar a exclusão de dados sob demanda e podem fornecer confirmação no telefone com o cliente quando ele for concluído.

## <a name="diagnostic-data"></a>Dados de diagnóstico
Como parte do processo de suporte, os operadores do Azure Stack pode [compartilhar os logs de diagnóstico](azure-stack-diagnostics.md) com as equipes de engenharia e suporte do Azure Stack para facilitar a solução.

A Microsoft fornece uma ferramenta e solicitado de script para os clientes coletar e carregar arquivos de log de diagnóstico. Depois de coletados, os arquivos de log forem transferidos via um HTTPS protegido por uma conexão criptografada para a Microsoft. Como o HTTPS fornece a criptografia durante a transmissão, nenhuma senha é necessária para a criptografia em trânsito. Depois que eles forem recebidos, os logs são criptografados e armazenados até que sejam automaticamente excluídos 90 dias depois que o caso de suporte é fechado.

## <a name="telemetry-data"></a>Dados de telemetria
[Telemetria de pilha do Azure](azure-stack-telemetry.md) carrega automaticamente os dados do sistema para a Microsoft por meio da experiência do usuário conectado. Operadores do Azure Stack tem controles para personalizar as configurações de privacidade e os recursos de telemetria a qualquer momento.

Microsoft não pretende coletar dados confidenciais, como números de cartão de crédito, nomes de usuário e senhas, endereços de email ou informações confidenciais semelhantes. Se determinarmos que informações confidenciais foram recebidas inadvertidamente, podemos excluí-lo. 

## <a name="billing-data"></a>Dados de cobrança
[Cobrança do Azure Stack](azure-stack-usage-reporting.md) aproveita a cobrança do Azure global e o uso do pipeline e, portanto, em alinhamento com as diretrizes de conformidade da Microsoft.

Operadores do Azure Stack pode configurar o Azure Stack para encaminhar informações de uso do Azure para cobrança. Isso é necessário para clientes de vários nós do Azure Stack que escolherem o modelo de cobrança pagamento-como-o uso. Relatório de uso é controlado independentemente da telemetria e não é necessário para os clientes de vários nós que escolher o modelo de capacidade ou para usuários do Kit de desenvolvimento do Azure Stack. Para esses cenários, o relatório de uso pode ser desativado usando [o script de registro](azure-stack-usage-reporting.md).


## <a name="next-steps"></a>Próximas etapas 
[Saiba mais sobre a segurança do Azure Stack](azure-stack-security-foundations.md) 
