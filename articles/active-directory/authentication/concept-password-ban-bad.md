---
title: Senhas - Azure Active Directory banidas dinamicamente
description: Proibir senhas fracas do seu ambiente com senhas proibidas dinamicamente pelo Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 645dd7fe35ba3e7d83a3ee374d7ab566ad193cc2
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314120"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminar senhas incorretas na organização

Os líderes do setor orientam que você não utilize a mesma senha em vários lugares, que torne-a complexa e para não simplificá-la como Senha123. Como as organizações podem garantir que os usuários estejam seguindo as diretrizes? Como podem garantir que os usuários não utilizam senhas comuns ou senhas que são conhecidas por estarem incluídas em violações de dados recentes?

## <a name="global-banned-password-list"></a>Lista de senhas proibidas globalmente

A Microsoft está sempre trabalhando para se manter um passo à frente dos cibercriminosos. Portanto, a equipe do Azure AD Identity Protection procura continuamente por senhas comumente usadas e comprometidas. Em seguida, bloqueiam as senhas consideradas muito comuns na lista de senhas proibidas globalmente. Os criminosos cibernéticos também usam estratégias semelhantes em seus ataques, portanto, a Microsoft não publica o conteúdo dessa lista publicamente. Essas senhas vulneráveis são bloqueadas antes de tornarem-se uma ameaça real aos clientes da Microsoft. Para obter mais informações sobre os esforços atuais de segurança, consulte o [Relatório de inteligência de segurança da Microsoft](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="custom-banned-password-list"></a>Lista de senhas proibidas personalizadas

Algumas organizações podem querer levar a segurança um passo adiante, adicionando suas próprias personalizações no topo da lista de senhas proibidas, em que a Microsoft chama de lista de senhas proibidas personalizada. Os clientes empresariais, como a Contoso, podem optar por bloquear variantes de nomes de marca, termos específicos da empresa ou outros itens.

A lista de senhas proibidas personalizada e a capacidade de habilitar a integração do Active Directory local são gerenciadas usando o portal do Azure.

![Modificar a lista de senhas proibidas personalizada em Métodos de Autenticação no portal do Azure](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Cenários híbridos locais

A proteção de contas somente na nuvem é útil, mas muitas organizações mantêm cenários híbridos, incluindo o Windows Server Active Directory local. É possível instalar a proteção por senha do Azure AD para Windows Server Active Directory agentes locais estender as as listas de senhas em sua infraestrutura existente. Agora, os usuários e administradores que alteram, definem ou redefinem senhas locais precisam obedecer à mesma política de senha que os usuários somente na nuvem.

## <a name="how-are-passwords-evaluated"></a>Como as senhas são avaliadas

Sempre que um usuário altera ou redefine a senha, a nova senha é verificada quanto à intensidade e à complexidade validando-a com relação à lista de senhas banidas personalizadas e globais (se estas últimas estiverem configuradas).

Mesmo que a senha do usuário contenha uma senha proibida, ela ainda poderá ser aceita se a senha geral for forte o suficiente em outros aspectos. Uma senha configurada recentemente passará pelas etapas a seguir para avaliar a força geral e determinar se ela deverá ser aceita ou rejeitada.

### <a name="step-1-normalization"></a>Etapa 1: Normalização

Primeiro, uma nova senha passa por um processo de normalização. Isso permite que um pequeno conjunto de senhas banidas seja mapeado para um conjunto muito maior de senhas potencialmente fracas.

A normalização tem duas partes.  Primeiros, todas as letras maiúsculas são alteradas para letras minúsculas.  Por exemplo, as substituições de caractere comuns são realizadas, como:  

| Letra original  | Letra substituída |
| --- | --- |
| '0'  | 'o' |
| '1'  | 'l' |
| '$'  | 's' |
| '\@'  | 'a' |

Exemplo: suponha que a senha "em branco" seja proibida e um usuário tente alterar a senha para “Bl@nK”. Mesmo que “Bl@nk” não seja especificamente proibida, o processo de normalização converterá essa senha como "em branco", que é uma senha banida.

### <a name="step-2-check-if-password-is-considered-banned"></a>Etapa 2: Verificar se a senha é considerada proibida

#### <a name="fuzzy-matching-behavior"></a>Comportamento da correspondência difusa

Correspondência difusa é usada na senha normalizada para identificar se ela contém uma senha encontrada na lista de senhas banidas global ou personalizada. O processo de correspondência baseia-se em uma distância de edição de comparação de um (1).  

Exemplo: suponha que a senha “abcdef” seja proibida e um usuário tente alterar a senha para uma dos seguintes:

‘abcdeg’    *(último caractere foi alterado de ‘f’ para ‘g’)* ‘abcdefg’   *’(g’ acrescentado ao final)* ‘abcde’     *(‘f’ à direita excluído do fim)*

Cada uma das senhas acima não corresponde especificamente à senha banida "abcdef". No entanto, uma vez que cada exemplo está a uma edição do token proibido ‘abcdef’, eles são considerados uma correspondência de “abcdef”.

#### <a name="substring-matching-on-specific-terms"></a>Correspondência de subcadeia de caracteres (em termos específicos)

A correspondência de subcadeia de caracteres é usada na senha normalizada para verificar o nome e o sobrenome do usuário, bem como o nome do locatário (observe que a correspondência de nome de locatário não é feita ao validar senhas em um controlador de domínio do Active Directory).

Exemplo: suponha que um usuário John Doe queira redefinir sua senha para "J0hn123fb". Após a normalização, essa senha seria "john123fb". A correspondência de subcadeia de caracteres determina que a senha contém o nome do usuário "John". Embora "J0hn123fb" não esteja especificamente em nenhuma das listas de senhas banidas, foi encontrada uma correspondência de subcadeia de caracteres "John" na senha. Portanto, essa senha deve ser rejeitada.

#### <a name="score-calculation"></a>Cálculo de pontuação

A próxima etapa é identificar todas as instâncias de senhas banidas na nova senha normalizada do usuário. Em seguida:

1. Cada senha banida encontrada na senha do usuário recebe um ponto.
2. Cada caractere exclusivo restante recebe um ponto.
3. Uma senha deve ter pelo menos cinco pontos para que seja aceita.

Para os próximos dois exemplos, vamos supor que a Contoso esteja usando a proteção de senha do Azure AD e tenha "contoso" em suas listas personalizadas. Vamos supor também que "em branco" está na lista global.

Exemplo: um usuário altera sua senha para “C0ntos0Blank12”

Após a normalização, essa senha se torna “contosoblank12”. O processo de correspondência localiza que essa senha contém duas senhas banidas: contoso e em branco. Essa senha então recebe uma pontuação:

[contoso] + [em branco] + [1] + [2] = 4 pontos como essa senha é em 5 pontos, ele será rejeitado.

Exemplo: um usuário altera a senha para "ContoS0Bl@nkf9!".

Após a normalização, essa senha se torna “contosoblankf9!”. O processo de correspondência localiza que essa senha contém duas senhas banidas: contoso e em branco. Essa senha então recebe uma pontuação:

[contoso] + [em branco] + [f] + [9] + [!] = 5 pontos; uma vez que essa senha tem pelo menos 5 pontos, ela é aceita.

   > [!IMPORTANT]
   > Observe que o algoritmo de senhas banidas junto com a lista global pode mudar e muda a qualquer momento no Azure com base em análise e pesquisa de segurança contínuas. Para o serviço de agente do controlador de domínio local, os algoritmos atualizados só entrarão em vigor depois que o software do agente de controlador de domínio for reinstalado.

## <a name="license-requirements"></a>Requisitos de licença

|   | Proteção por senha do AD do Azure com a lista de senhas banidas global | Proteção por senha do AD do Azure com a lista de senhas banidas personalizado|
| --- | --- | --- |
| Usuários somente na nuvem | Azure AD Gratuito | Azure AD Básico |
| Os usuários sincronizados no local Windows Server Active Directory | O Azure AD Premium P1 ou P2 | O Azure AD Premium P1 ou P2 |

Informações adicionais sobre licenciamento, incluindo custos, podem ser encontradas no [site de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>O que os usuários veem

Quando um usuário tentar redefinir uma senha para alguma que seria proibida, a seguinte mensagem de erro será exibida:

Infelizmente, sua senha contém uma palavra, uma frase ou um padrão que pode ser facilmente adivinhado. Tente novamente com uma senha diferente.

## <a name="next-steps"></a>Próximas etapas

* [Configurar a lista de senhas proibidas personalizada](howto-password-ban-bad.md)
* [Habilitar agentes de proteção por senha do Azure AD local](howto-password-ban-bad-on-premises-deploy.md)
