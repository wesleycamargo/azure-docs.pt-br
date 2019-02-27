---
title: Versão prévia da proteção por senha do Azure AD
description: Proibir senhas fracas no Active Directory local usando a versão prévia da proteção por senha do Azure AD
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
ms.openlocfilehash: f1beae186f6eb276b9aa302d3d51f0ba8688e591
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415741"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Visualização: Impor a proteção por senha do Azure AD para o Active Directory do Windows Server

|     |
| --- |
| A lista de senhas proibidas personalizada e a proteção por senha do Azure AD são recursos de visualização pública do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

A proteção por senha do Azure AD é um novo recurso na visualização pública com base no Azure AD (Azure Active Directory) para aprimorar as políticas de senha em uma organização. A implantação local da proteção por senha do Azure AD usa as listas de senhas proibidas globais e personalizadas armazenadas no Azure AD e realiza as mesmas verificações no local que as alterações baseadas em nuvem do Azure AD.

## <a name="design-principles"></a>Princípios de design

A Proteção de Senha do Azure AD para o Active Directory foi criada com os seguintes princípios em mente:

* Os controladores de domínio nunca precisam se comunicar diretamente com a Internet
* Nenhuma porta de rede nova é aberta em controladores de domínio.
* Nenhuma alteração de esquema do Active Directory é necessária. O software usa o contêiner do Active Directory existente e os objetos de esquema serviceConnectionPoint.
* Não é necessário nenhum Domínio do Active Directory ou nível Funcional da Floresta (DFL\FFL) mínimo.
* O software não cria nem exige nenhuma conta nos domínios do Active Directory que ele protege.
* As senhas de texto não criptografado do usuário nunca deixam o controlador de domínio (durante as operações de validação de senha ou em qualquer outro momento).
* A implantação incremental tem suporte com a compensação de que a política de senha somente é aplicada quando o agente do controlador de domínio é instalado.
* É recomendável instalar o agente DC em todos os DCs para garantir a imposição de segurança proteção de senha onipresente.

## <a name="architectural-diagram"></a>Diagrama de arquitetura

É importante ter uma compreensão dos conceitos funcionais e de design subjacentes antes de implantar a Proteção de Senha do Azure AD em um ambiente do Active Directory local. O diagrama a seguir mostra como os componentes da Proteção de Senha do Azure AD funcionam em conjunto:

![Como os componentes de proteção por senha do Azure AD trabalham em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

O diagrama acima mostra os três componentes de software básicos que formam a proteção por senha do Azure AD:

* O serviço de Proxy de Proteção por Senha do Azure AD é executado em qualquer computador ingressado no domínio na floresta atual do Active Directory. Sua funcionalidade principal é encaminhar solicitações de download de política de senha dos controladores de Domínio para o Azure AD e retornar a resposta do Azure AD para o controlador de domínio.
* A dll do filtro de senha do Agente DC da Proteção por Senha do Azure AD recebe solicitações de validação de senha do sistema operacional e as encaminha para o serviço do agente DC de Proteção por Senha do Azure AD executado localmente no controlador de domínio.
* O serviço do Agente DC de Proteção por Senha do Azure AD recebe solicitações de validação de senha da dll do filtro de senha do agente DC, processa-as usando a política de senha atual disponível localmente e retorna o resultado (aprovado/reprovado).

## <a name="theory-of-operations"></a>Teoria de operações

Considerando os princípios de design e o diagrama acima, como a Proteção por Senha do Azure AD realmente funciona?

Cada serviço de Proxy de Proteção por Senha do Azure AD se anuncia para controladores de domínio na floresta criando um objeto serviceConnectionPoint no Active Directory.

Cada serviço do Agente DC da Proteção por Senha do Azure AD também cria um objeto serviceConnectionPoint no Active Directory. No entanto, isso é usado principalmente para relatórios e diagnósticos.

O serviço do Agente DC da Proteção por Senha do Azure AD é responsável por iniciar o download de uma nova política de senha do Azure AD. A primeira etapa é localizar um serviço de Proxy de Proteção por Senha do Azure AD consultando a floresta para objetos serviceConnectionPoint do proxy. Depois que um serviço de proxy disponível é encontrado, a solicitação de download de política de senha é enviada do serviço do Agente DC para o serviço de proxy, que, por sua vez, a envia para o Azure AD e retorna a resposta para o serviço do Agente DC. Depois de receber uma nova política de senha do Azure AD, o serviço de Agente DC armazena a política em uma pasta dedicada na raiz de seu compartilhamento do sysvol do domínio. O serviço de Agente DC também monitora essa pasta no caso de políticas mais recentes serem replicadas de outros serviços de Agente DC no domínio.

O serviço do Agente DC de Proteção por Senha do Azure AD sempre solicitará uma nova política na inicialização do serviço. Após o serviço do Agente DC ser iniciado, ele periodicamente (uma vez a cada hora) verificará a idade da política disponível localmente no momento. Se a política atual tiver mais de uma hora, o serviço do Agente DC solicitará uma nova política do Azure AD conforme descrito acima, caso contrário, o Agente DC continuará a usar a política atual.

O serviço do Agente DC de Proteção por Senha do Azure AD se comunica com o serviço de Proxy de Proteção por Senha do Azure AD usando a RPC (chamada de procedimento remoto) por TCP. O serviço de Proxy ouve essas chamadas em uma porta de RPC dinâmica ou estática (conforme configurado).

O Agente DC de Proteção por Senha do Azure AD nunca ouve em uma porta disponível na rede e o serviço de Proxy nunca tenta chamar o serviço do Agente DC.

O serviço de Proxy de Proteção por Senha do Azure AD é sem estado, ele nunca armazena em cache políticas ou qualquer outro estado baixado do Azure.

O serviço do Agente DC de Proteção por Senha do Azure AD avaliará a senha de um usuário somente usando a política de senha disponível localmente mais recente. Se não houver nenhuma política de senha disponível no DC local, a senha será aceita automaticamente e uma mensagem de log de evento será registrada para alertar o administrador.

A Proteção por Senha do Azure AD não é um mecanismo de aplicativo de política em tempo real. Pode haver um atraso no tempo entre uma alteração na configuração da política de senha é realizada no Azure AD e o tempo que ela atinge e é imposta em todos os controladores de domínio.

## <a name="foresttenant-binding-for-azure-ad-password-protection"></a>Associação de floresta\locatário para a Proteção por Senha do Azure AD

A implantação da Proteção por Senha do Azure AD em uma floresta do Active Directory requer o registro da floresta e de qualquer serviço de Proxy de Proteção por Senha do Azure AD implantado, com o Azure AD. Esses registros (floresta e proxies) estão associados a um locatário do Azure AD específico que é identificado implicitamente por meio das credenciais usadas durante o registro. Sempre que uma política de senha de Proteção por Senha do Azure AD é baixada, ela sempre é específica para esse locatário (isto é, a política sempre será uma combinação da lista de senhas banidas global da Microsoft e a lista de senhas banidas personalizada por locatário). A configuração de diferentes domínios ou proxies em uma floresta associada a locatários diferentes do Azure AD não é compatível.

## <a name="license-requirements"></a>Requisitos de licença

Os benefícios da lista global de senhas proibidas aplicam-se a todos os usuários do Azure AD (Active Directory do Azure).

A lista de senhas proibidas personalizada requer licenças do Azure AD Basic.

A Proteção por Senha do Azure AD para o Windows Server Active Directory requer licenças do Azure AD Premium.

Informações adicionais sobre licenciamento, incluindo custos, podem ser encontradas no [site de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Baixar

Os dois instaladores de agente necessários para a Proteção de Senha do Azure AD que podem ser baixados do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="next-steps"></a>Próximas etapas

[Implantar proteção de senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
