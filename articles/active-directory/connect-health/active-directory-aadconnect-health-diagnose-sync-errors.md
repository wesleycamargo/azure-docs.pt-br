---
title: Azure Active Directory Connect Health - erros de sincronização atribuídos ao diagnóstico duplicado | Microsoft Docs
description: Este documento descreve o processo de diagnóstico de erros de sincronização de atributo duplicado e corrige potencial dos cenários de objeto órfão diretamente do portal do Azure.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: zhiweiw
ms.openlocfilehash: 0a345fd3443fb33d6f5912c8a04677091e20ecc8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305530"
---
# <a name="duplicate-attribute-sync-errors-diagnosis-and-remediation"></a>Diagnóstico e correção de erros de sincronização atribuídos duplicados 

## <a name="overview"></a>Visão geral
Coloque uma etapa adicional de realce de erros de sincronização, o Azure Active Directory Connect Health está introduzindo uma experiência de correção de autoatendimento para solucionar problemas de erros de sincronização de atributo duplicado e corrigir os objetos órfãos do Azure Active Directory. O principal benefício do recurso de diagnóstico:
- Fornece o procedimento de diagnóstico para restringir cenários de erro de sincronização de atributo duplicados e indicar resoluções específicas
- Aplicar a correção para cenários dedicados do Microsoft Azure Active Directory e para resolver o erro em um único clique
- Nenhuma atualização ou a configuração é necessária para habilitar esse recurso.
Leia mais detalhes sobre a resiliência duplicada do [Microsoft Azure Active Direcotry](https://aka.ms/dupattributeresdocs).

## <a name="problems"></a>Problemas
### <a name="common-scenario"></a>Cenário comum
Quando ocorrer os erros de sincronização **QuarantinedAttributeValueMustBeUnique** e **AttributeValueMustBeUnique**, é comum ver o conflito de nome Principal do usuário ou endereços de Proxy no Microsoft Azure Active Directory. Você pode resolver os erros de sincronização, atualizando o objeto de origem conflitantes do lado do local. O erro de sincronização será resolvido após a sincronização a seguir. Por exemplo, a figura a seguir indica que dois usuários estão tendo conflito de seu UserPrincipalName como *Joe.J@contoso.com*. Os objetos conflitantes são colocados em quarentena no Microsoft Azure Active Directory. 

![Diagnosticar cenário comum de erro de sincronização](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Cenário de objeto órfão
Ocasionalmente, você pode achar que um usuário existente perde a âncora de origem. A exclusão do objeto de origem aconteceu no AD local, mas a alteração do sinal de exclusão de nunca foi sincronizada com o Microsoft Azure Active Directory. Isso pode acontecer devido a motivos, como migração de problema ou domínio do mecanismo de sincronização. Quando o mesmo objeto for restaurado ou recriado, o usuário logicamente existente deve ser o usuário para sincronizar a partir da âncora de origem. Usuário existente como um único objeto de nuvem, você também pode ver o usuário conflitante sincronizado ao Microsoft Azure Active Directory e não pode ser correspondido na sincronização para o objeto existente. Não há nenhuma maneira direta de remapear a Âncora de Origem. Leia mais sobre o [KB existente](https://support.microsoft.com/help/2647098). Por exemplo, o objeto existente no Microsoft Azure Active Directory preserva a licença de Joe. Objeto sincronizado recentemente com âncora de origem diferentes ocorreu no estado do atributo duplicado no Microsoft Azure Active Directory. Alterações de Joe no local AD não poderão ser aplicadas ao usuário original de Joe (objeto existente) no Microsoft Azure Active Directory.  

![Diagnosticar o cenário do objeto órfão de erro de sincronização](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Etapas de diagnóstico e solução de problemas no Connect Health 
Diagnosticar o recurso dá suporte a objetos de usuário com os seguintes atributos duplicados:

| Nome do atributo | Tipos de Erro de Sincronização|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique ou AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique ou AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Requer permissão do **Administrador Global** ou **Colaborador** nas configurações de RBAC para ser capaz de acessar esse recurso. 
>

Seguindo as etapas no portal do Azure, você poderá restringir os detalhes do erro de sincronização e fornecer soluções mais específicas:

![Diagnosticar as etapas de diagnóstico do erro de sincronização](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

No portal do Azure, você poderá passar por algumas etapas para identificar os cenários específicos de correção:  
1.  Na coluna de status de diagnóstico, o status será exibido se houver um fluxos de solução de problemas potenciais para reduzir a ocorrência de erro e corrigir potencialmente diretamente do Microsoft Azure Active Directory.
| Status | O que isso significa? |
| ------------------ | -----------------|
| Não iniciado | Você não visitou esse processo de diagnóstico. Depende no resultado diagnóstico, potencialmente há uma maneira de corrigir o erro de sincronização a partir do portal diretamente. |
| Correção Manual Necessária | O erro não se ajusta aos critérios de correção disponível a partir do portal. O caso pode ser (1) Tipos de objetos conflitantes que não são usuários (2) Você já seguiu as etapas de diagnósticas e não há resolução disponível no portal. Nesse caso, a correção no lado local ainda será uma das soluções. [Leia mais sobre correção local](https://support.microsoft.com/help/2647098) | 
| Sincronização Pendente | A correção foi aplicada. Aguardando o próximo ciclo de sincronização para limpar o erro. |
>[!IMPORTANT]
> A coluna de status de diagnóstico será redefinida após cada ciclo de sincronização. 
>

2.  Clicando no botão **diagnosticar** botão na folha detalhes do erro, você precisará responder algumas perguntas e identificar os detalhes do erro de sincronização. Responder as perguntas ajudará a identificar o caso de cenário de objeto órfão. 

3.  Se houver um botão **Fechar** botão no final do diagnóstico, significa que nenhuma correção rápida estará disponível a partir do portal com base nas respostas determinadas. Consulte a solução mostrada na última etapa. A correção nos locais ainda será as soluções. Depois de clicar no botão Fechar, você encontrará o status de erro de sincronização atual que passará a ser **correção Manual necessária**. O status será mantido durante o ciclo de sincronização atual.

4.  Depois que o caso de objeto órfão é identificado, você poderá corrigir os erros de atributos duplicados de sincronização diretamente do portal. Clique no botão **Aplicar Correção** para disparar o processo. O status de erro de sincronização atual será atualizado para ser **Sincronização pendente**.

5.  Após o ciclo de sincronização seguinte, o erro deve ser removido da lista.

## <a name="how-to-answer-the-diagnosis-questions"></a>Como responder às perguntas de diagnóstico 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>O usuário existe nas suas premissas do Active Directory?

A pergunta está tentando identificar o objeto de usuário existente do Active Directory no local de origem.  
1.  Verifique se o Active Directory tem um objeto com o UserPrincipalName fornecido. Se Não, responda Não.
2.  Se Sim, verifique se o objeto ainda está em escopo para Sincronização.  
  - Pesquise no Espaço Conector do Microsfot Azure Active Directory com o DN.
  - Se o objeto foi encontrado com o estado **Adicionar Pendente**, a resposta é Não. A Conexão do Microsoft Azure Active Directory não é capaz de conectar o objeto ao objeto AD.
  - Se o objeto não for encontrado, responda Sim.

> Colocar o diagrama a seguir, por exemplo, a pergunta é tentar identificar se *Joe Jackson* ainda existe no Active Directory no local.
Para **cenário comum**, ambos os usuários *Joe Johnson* e *Joe Jackson* estarão presentes no Active Directory local. Os objetos em quarentena são dois usuários diferentes.

![Diagnosticar cenário comum de erro de sincronização](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

> Para **cenário objeto órfão**, apenas um único usuário – *Joe Johnson* estará presente no Active Directory local:

![Diagnosticar o cenário do objeto órfão de erro de sincronização](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-these-accounts-belong-to-the-same-user"></a>Ambas as contas pertencem ao mesmo usuário?
A pergunta está verificando se há usuário conflitante de entrada e o objeto de usuário existente no Microsoft Azure Active Directory para ver se eles pertencem ao mesmo usuário.  
1.  O objeto conflitante recentemente é sincronizado com o Microsoft Azure Active Directory. Compare o objeto a partir de seu:  
  - Nome de exibição
  - Nome UPN
  - ID de objeto
2.  Se falhou ao compará-los, verifique se Microsoft Active Directory tem objetos com o UserPrincipalNames fornecido. Responda NÃO se ambos forem encontrados.  

> No caso abaixo, os dois objetos pertencem ao mesmo usuário *Joe Johnson*.

![Diagnosticar o cenário do objeto órfão de erro de sincronização](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happened-after-fix-is-applied-for-orphaned-object-scenario"></a>O que aconteceu após a correção ser aplicada para o cenário de objeto órfão
Com base nas respostas das perguntas geradas, você poderá ver o botão **Aplicar Correção** botão quando houver uma correção disponível no Microsoft Azure Active Directory. Nesse caso, o objeto local no está sincronizando com um objeto inesperado do Microsoft Azure Active Directory. Os dois objetos são mapeados usando a "Âncora de origem". A aplicação da alteração executará etapas, como:
- Atualiza a Âncora de Origem para o objeto correto no Microsoft Azure Active Directory.
- Exclua o objeto conflitante no Microsoft Azure Active Directory se apresentar.

![Diagnosticar o erro de sincronização após a correção](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> A alteração de Aplicar Correção será aplicada somente aos casos de objeto órfão.
>

Após as etapas acima, o usuário será capaz de acessar recursos de original, que é um link para o objeto existente. O valor **Diagnosticar Status** na exibição de lista será atualizada para ser **Sincronização Pendente**. O erro de sincronização será resolvido após a sincronização a seguir. O Connect Health não mostrará mais o erro de sincronização resolvido da exibição de lista. 


## <a name="faq"></a>Perguntas frequentes
 -  O que aconteceu, se a execução da aplicação falhou?  
Se a execução falhar, é possível que o Azure AD Connect esteja executando o erro de exportação naquele momento. Atualize a página do portal e tente novamente após a sincronização a seguir. O ciclo de sincronização padrão é de 30 minutos. 

 -  E se o **objeto existente** deve ser o objeto a ser excluído?  
Se o objeto existente deve ser excluído neste caso, o processo não envolve a alteração da Âncora de Origem. Você deve ser capaz de corrigi-lo do seu local AD.  

 -  O que é a permissão para o usuário poder aplicar a correção?  
Administrador global ou colaborador das configurações de RBAC terá permissão para acessar o diagnóstico e o processo de solução de problemas.

 -  Tenho que configurar o Azure Active Direct ou atualizar o agente do Azure Active Directory Connect Health para esse recurso?  
Não, o processo de diagnóstico é um recurso completo baseado em nuvem.

 -  Se o objeto existente é excluído flexível, o processo de diagnosticar restaurará o objeto para o ativo novamente?  
Não, a correção não irá atualizar o atributo de objeto diferente da Âncora de Origem. 
