---
title: Proteção por senha do AD do Azure - Active Directory do Azure
description: Senhas fracas de vetar no Active Directory local por meio da proteção de senha do AD do Azure
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d58c019cf3d801ce938a4ca6eca70b1606bf4ff6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60415847"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Impor a proteção por senha do Azure AD para o Active Directory do Windows Server

Proteção por senha do AD do Azure é um recurso que aprimora as políticas de senha em uma organização. Proteção de senha de implantação de local usa os dois as globais e personalizadas senha banida listas que são armazenadas no AD do Azure. Ele faz o mesma verificações no local como o Azure AD para alterações com base em nuvem.

## <a name="design-principles"></a>Princípios de design

Proteção por senha do AD do Azure destina-se com esses princípios em mente:

* Controladores de domínio nunca precisam se comunicar diretamente com a internet.
* Nenhuma porta de rede nova é aberta em controladores de domínio.
* Nenhuma alteração de esquema do Active Directory é necessária. O software usa o Active Directory existente **recipiente** e **serviceConnectionPoint** objetos de esquema.
* Nenhum do Active Directory domínio ou floresta nível funcional mínimo (DFL/FFL) é necessário.
* O software não criar nem exigem contas de domínios do Active Directory que ele protege.
* Senhas de texto não criptografado do usuário nunca deixam o controlador de domínio, durante as operações de validação de senha ou qualquer outro momento.
* O software não é dependente de outros recursos do Azure AD; Por exemplo, a sincronização de hash de senha do Azure AD não está relacionada e não é necessária na ordem do Azure AD para proteção por senha para a função.
* Implantação incremental tem suporte, mas a política de senha é imposta apenas onde o agente de controlador de domínio (agente de controlador de domínio) está instalado. Consulte o próximo tópico para obter mais detalhes.

## <a name="incremental-deployment"></a>Implantação incremental

Proteção por senha do AD do Azure dá suporte à implantação incremental em controladores de domínio em um domínio do Active Directory, mas é importante entender o que isso realmente significa e quais são as vantagens e desvantagens.

O software de agente de proteção DC de senha do Azure AD pode validar apenas senhas quando ele é instalado em um controlador de domínio e somente para as alterações de senha são enviadas ao controlador de domínio. Não é possível controlar quais controladores de domínio são escolhidos por máquinas de cliente do Windows para o processamento de alterações de senha do usuário. Para garantir um comportamento consistente e imposição de segurança de proteção de senha universal, o software do agente de controlador de domínio deve ser instalado em todos os controladores de domínio em um domínio.

Muitas organizações desejarão fazer um teste cuidadoso da proteção por senha do Azure AD em um subconjunto de seus controladores de domínio antes de fazer uma implantação completa. Proteção por senha do AD do Azure dá suporte a implantação parcial, ou seja o software do agente de controlador de domínio em um determinado DC ativamente validará as senhas, mesmo quando outros controladores de domínio no domínio não têm o software do agente de controlador de domínio instalado. Implantações parciais desse tipo não são seguro e que não são recomendados para fins de teste.

## <a name="architectural-diagram"></a>Diagrama de arquitetura

É importante entender os conceitos de funções e design subjacente antes de implantar a proteção por senha do Azure AD em um ambiente do Active Directory local. O diagrama a seguir mostra como os componentes da proteção por senha funcionam juntos:

![Como os componentes de proteção por senha do Azure AD trabalham em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* O serviço de Proxy de Proteção por Senha do Azure AD é executado em qualquer computador ingressado no domínio na floresta atual do Active Directory. Sua finalidade principal é encaminhar solicitações de download da política de senha de controladores de domínio para o Azure AD. Ele então retorna as respostas do Azure AD para o controlador de domínio.
* DLL do agente de controlador de domínio de filtro de senha recebe solicitações de validação de senha do usuário do sistema operacional. Ele encaminha-as para o serviço de agente do DC que está em execução localmente no controlador de domínio.
* O serviço de agente de controlador de domínio da proteção por senha recebe solicitações de validação de senha de DLL do agente de controlador de domínio de filtro de senha. Ele processa-os usando a política de senha (disponíveis localmente) atual e retorna o resultado: *passar* ou *falhar*.

## <a name="how-password-protection-works"></a>Como funciona a proteção de senha

Cada instância de serviço de Proxy de proteção de senha do Azure AD se anuncia aos controladores de domínio na floresta com a criação de um **serviceConnectionPoint** objeto no Active Directory.

Cada serviço de agente de controlador de domínio para a proteção por senha também cria uma **serviceConnectionPoint** objeto no Active Directory. Esse objeto é usado principalmente para relatórios e diagnósticos.

O serviço do agente de controlador de domínio é responsável por iniciar o download de uma nova política de senha do AD do Azure. A primeira etapa é localizar um serviço de Proxy de proteção de senha do Azure AD por meio de consulta da floresta para o proxy **serviceConnectionPoint** objetos. Quando um serviço de proxy disponíveis for encontrado, o agente de controlador de domínio envia uma solicitação de download da política de senha para o serviço de proxy. O serviço de proxy por sua vez envia a solicitação para o Azure AD. O serviço de proxy, em seguida, retorna a resposta para o serviço de agente de controlador de domínio.

Depois que o serviço do agente de controlador de domínio recebe uma nova política de senha do AD do Azure, o serviço armazena a política em uma pasta dedicada na raiz do seu domínio *sysvol* compartilhamento da pasta. O serviço do agente de controlador de domínio também monitora essa pasta, no caso de políticas mais recentes replicam de outros serviços do agente de controlador de domínio no domínio.

O serviço do agente de controlador de domínio sempre solicitará uma nova política na inicialização do serviço. Depois que o serviço de agente de controlador de domínio é iniciado, ele verifica a idade da política atual disponível localmente por hora. Se a política é mais de uma hora, o agente de controlador de domínio solicita uma nova política do Azure AD por meio do serviço de proxy, conforme descrito anteriormente. Se a política atual não for mais de uma hora, o agente de controlador de domínio continua a usar essa política.

Sempre que uma política de senha de proteção de senha do Azure AD é baixada, essa política é específica para um locatário. Em outras palavras, as políticas de senha são sempre uma combinação de lista de senha banida global do Microsoft e a lista de senha banida personalizada por locatário.

O agente de controlador de domínio se comunica com o serviço de proxy por meio do RPC sobre TCP. O serviço de proxy ouve essas chamadas em uma porta RPC dinâmica ou estática, dependendo da configuração.

O agente do controlador de domínio nunca escuta em uma porta de rede disponíveis.

O serviço de proxy nunca chama o serviço de agente de controlador de domínio.

O serviço de proxy é sem monitoração de estado. Ele nunca armazena em cache as políticas ou qualquer outro estado baixado do Azure.

O serviço do agente de controlador de domínio sempre usa a política de senha localmente disponível mais recente para avaliar a senha do usuário. Se nenhuma política de senha está disponível no controlador de domínio local, a senha é aceito automaticamente. Quando isso acontece, uma mensagem de evento é registrada para alertar o administrador.

Proteção por senha do Azure AD não é um mecanismo de aplicação de políticas em tempo real. Pode haver um atraso entre quando uma alteração de configuração de política de senha é feita no Azure AD e quando alteradas atingir e são aplicadas em todos os controladores de domínio.

Proteção por senha do AD do Azure atua como um suplemento para as políticas de senha do Active Directory existentes, não uma substituição. Isso inclui quaisquer outras dlls de filtro de senha 3ª parte que pode ser instalado. O Active Directory sempre requer que todos os componentes de validação de senha concordam antes de aceitar uma senha.

## <a name="foresttenant-binding-for-password-protection"></a>Associação de locatário/floresta para a proteção por senha

Implantação da proteção por senha em uma floresta do Active Directory do Azure AD exige o registro dessa floresta com o Azure AD. Cada serviço de proxy que é implantado também deve ser registrado com o Azure AD. Esses registros de proxy e de floresta estão associados um determinado locatário do AD do Azure, o que é identificado implicitamente pelas credenciais que são usadas durante o registro.

Floresta do Active Directory e todos os serviços de proxy implantados dentro de uma floresta devem ser registrados com o mesmo locatário. Não há suporte para ter uma floresta do Active Directory ou quaisquer serviços de proxy em que a floresta que está sendo registrada para o Azure AD diferente locatários. Os sintomas de tipo de implantação configurado incorretamente incluem a incapacidade de baixar as políticas de senha.

## <a name="download"></a>Baixar

Dois instaladores de agente necessário para a proteção de senha do AD do Azure estão disponíveis na [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Próximas etapas
[Implantar proteção de senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
