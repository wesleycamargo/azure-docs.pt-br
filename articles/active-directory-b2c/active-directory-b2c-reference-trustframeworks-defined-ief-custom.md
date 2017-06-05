---
title: "Azure Active Directory B2C: Referência: Estruturas confiáveis | Microsoft Docs"
description: "Um tópico sobre as políticas personalizadas do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 019dd66d5abe4242ffb6cdc3276929dcd7f04466
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017


---

# <a name="defining-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Defina estruturas confiáveis com o Identity Experience Framework do Azure AD B2C

As políticas personalizadas Azure AD B2C que aproveitam o Identity Experience Framework fornecem um serviço centralizado para a sua organização, que reduz a complexidade da federação de identidade em uma grande comunidade relevante de uma relação de confiança único e uma troca de metadados única.

Isso requer políticas personalizadas do Azure AD B2C aproveitando o Identity Experience Framework para permitir que você responda às seguintes perguntas.

- *Quais políticas de dados, proteção de dados, jurídica, segurança e privacidade devem ser cumpridas?*
- *Quem são os contatos e quais são os processos para se tornar um participante reconhecido?*
- *Quem são os provedores de informações de identidade reconhecida (também conhecido como provedores de declarações) e o que eles oferecem?*
- *Quais são as partes confiantes reconhecidas [e, opcionalmente, quais são suas exigências]?*
- *Quais são os requisitos interoperabilidade da técnica "na rede" para os participantes?*
- *Quais regras operacionais de "tempo de execução" devem ser aplicadas para a troca de informações de identidade digital?*

Para responder a todas essas perguntas, as políticas personalizadas do Azure AD B2C influenciam o Identity Experience Framework, que influenciam o constructo da estrutura confiável (TF). Vamos considerar esse constructo e o que ele oferece nesse sentido.

## <a name="understanding-the-trust-framework-and-federation-management-foundation"></a>Noções básicas sobre a base de gerenciamento de federação da estrutura confiável

Um constructo deve ser compreendido como uma especificação escrita da identidade, segurança, privacidade e políticas de proteção de dados ao qual os participantes de uma comunidade relevante devem estar em conformidade.

A identidade federada fornece uma base para atingir a garantia de identidade do usuário final em escala de Internet.  Ao delegar o gerenciamento de identidade para terceiras partes, uma única identidade digital para um usuário final pode ser reutilizada com várias partes confiáveis.  

A garantia de identidade realmente requer que os provedores de identidade (IdPs) e provedores de atributos (AtPs) obedeçam a políticas operacionais e práticas recomendadas específicas de segurança e privacidade.  As partes confiáveis (RPs) não conseguem realizar inspeções diretas, portanto, devem desenvolver relações de confiança com os IdPs e AtPs que escolherem como parceiros de trabalhos.  Como o número de consumidores e provedores dos cogumelos de informações de identidade digital, torna-se inviável emparelhar o gerenciamento dessas relações de confiança, ou até mesmo emparelhar a troca de metadados técnicos necessários para a conectividade de rede.  Os hubs de federação só atingiram sucesso limitado ao solucionar esses problemas.

Os TFs são a base do modelo de estrutura confiável de troca de identidade aberta (OIX), onde cada comunidade relevante é controlada por uma especificação determinada de TF. Tal especificação TF define:

- As métricas de segurança e privacidade para a comunidade relevante com a definição de:
    - Os níveis de garantia (LOA) oferecidos/exigidos pelos participantes, ou seja, um conjunto ordenado de classificações de segurança para autenticidade das informações de identidade digital.
    - Os níveis de proteção (LOP) oferecidos/exigidos pelos participantes, ou seja, um conjunto ordenado de classificações de confiança para a proteção das informações de identidade digital manipuladas por participantes da comunidade relevante.
- A descrição das informações de identidade digital oferecidas/exigidas pelos participantes.
- As políticas técnicas para produção e o consumo de informações de identidade digital e, portanto, para medir LOA e LOP. Essas políticas normalmente incluem as seguintes categorias de políticas:
    - Políticas de verificação de identidade: *Qual é o nível de avaliação das informações de uma pessoa?*
    - Políticas de segurança: *Qual é o nível de proteção de confidencialidade e integridade das informações?*
    - As políticas de privacidade: *Qual controle um usuário tem sobre as próprias informações de identificação pessoal (PII)*?
    - Políticas de sustentabilidade: continuidade e proteção de PII se um provedor interromper as operações.
- Os perfis técnicos de produção e o consumo de informações de identidade digital. Esses perfis:
    - Fazem escopo de interfaces para as quais as informações de identidade digital estão disponíveis em um LOA específico.
    - Descrevem os requisitos técnicos para interoperabilidade durante a transmissão.
- As descrições das várias funções que os participantes da comunidade podem realizar junto com as qualificações necessárias para atender a essas funções.

Dessa forma, uma especificação TF controla como as informações de identidade são trocadas entre os participantes da comunidade relevante: partes confiantes, identidade, provedores de atributos e verificadores de atributos.

Na linguagem desse modelo OIX TF, uma especificação TF é constituída de um ou mais documentos que servem como uma referência para o controle da comunidade relevante que regulamenta a asserção e o consumo de informações de identidade digital dentro da comunidade. Ou seja, é um conjunto documentado de políticas e procedimentos projetados para estabelecer confiança em identidades digitais usadas para transações online entre membros de uma comunidade relevante.  **Uma especificação de TF define as regras para a criação de um ecossistema de identidade federada viável para algumas comunidades.**

A partir de hoje, há um acordo generalizado sobre o benefício dessa abordagem de hoje e não há dúvidas de que as especificações da estrutura confiável facilitarão o desenvolvimento de ecossistemas de identidade digital com segurança, garantia e características de privacidade verificáveis, de modo que possam ser reutilizados em várias comunidades relevantes.

Por esse motivo, as políticas personalizadas do Azure AD B2C se aproveitam do Identity Experience Framework, que toma proveito da especificação, uma vez que seu cerne, como base de sua representação de dados, para um TF facilitar a interoperabilidade.  

Ao aproveitar o Identity Experience Framework, as políticas personalizadas do Azure AD B2C representam uma especificação de TF como uma mistura de humanos e dados legíveis de computador: algumas seções desse modelo (normalmente aqueles mais orientados em relação à controle) são representados como referências à documentação publicada de políticas de segurança e privacidade juntamente com os procedimentos relacionados, se houver; enquanto outras seções descrevem detalhadamente as regras de metadados e o tempo de execução de configuração para facilitar a automação operacional.

## <a name="understanding-trust-framework-policies"></a>Noções básicas sobre políticas de estrutura confiável

Em termos de implementação, a especificação do TF acima consiste em políticas personalizadas do Azure AD B2C aproveitando o Identity Experience Framework em um conjunto de políticas que permitem o controle completo sobre comportamentos de identidade e experiências.  As políticas personalizadas do Azure AD B2C aproveitando o Identity Experience Framework, de fato, permitem escrever e criar seu próprio TF por meio dessas políticas declarativas que podem definir e configurar:

- As referências do documento definindo o ecossistema de identidade federada da comunidade que se relaciona com o TF. Eles são links para a documentação do TF. As regras de "tempo de execução" operacionais (predefinidas), ou os percursos do usuário que automatizam e/ou controlam a troca e uso das declarações. Esses percursos do usuário são associados a um LOA (e um LOP). Uma política, portanto, pode ter percursos do usuário com diversos LOAs (e LOPs).
- Os provedores de atributos e de identidade, ou os provedores de declarações na comunidade relevante e os perfis técnicos que eles suportam junto com a capacitação de LOA/LOP (fora de banda) relacionadas a eles.
- A integração com verificadores de atributos ou os provedores de declarações.
- Partes confiáveis da comunidade (por inferência).
- Os metadados para estabelecer comunicações de rede entre os participantes. Esses metadados com os perfis técnicos serão usados durante uma transação para inserir interoperabilidade "na rede" entre a terceira parte confiável e outros participantes da comunidade.
- A conversão de protocolo, se houver (SAML, OAuth2, WS-Federation e OpenID Connect).
- Os requisitos de autenticação.
- A orquestração multifator, se houver.
- Um esquema compartilhado para todas as declarações disponíveis e os mapeamentos para os participantes de uma comunidade relevante.
- Todas as transformações de declarações, junto com a minimização de dados possíveis neste contexto para sustentar a troca e o uso das declarações.
- A associação e criptografia.
- O armazenamento de declarações

> [!NOTE]
> Coletivamente, nos referimos a todos os possíveis tipos de informações de identidade que podem ser trocadas como declarações: declarações sobre a credencial de autenticação do usuário final, análise de identidade, dispositivo de comunicação, localização física, atributos de identificação pessoal e assim por diante.  
>
> Usamos o termo *declarações* em vez de atributos, que são um subconjunto, porque no caso de transações online esses não são os fatos que podem ser verificados diretamente pela parte confiável; em vez disso, eles são asserções ou declarações sobre fatos para o qual a terceira parte confiável deve desenvolver confiança suficiente para conceder a transação solicitada ao usuário final.  
>
> E, além disso, o fato de que as políticas personalizadas do Azure AD B2C aproveitam o Identity Experience Framework foi projetado para simplificar a troca de todos os tipos de informações de identidade digital de maneira consistente, independentemente se o protocolo subjacente é definido para autenticação de usuário ou recuperação de atributo.  Da mesma forma, usaremos o termo provedores de declarações para nos referirmos coletivamente aos provedores de identidade, provedores de atributos e verificadores de atributos quando não quisermos fazer distinção entre suas funções específicas.   

Assim, eles controlam como as informações de identidade são trocadas entre uma terceira parte confiável, identidade, provedores de atributos e verificadores de atributos. Elas controlam quais identidades e provedores de atributos são necessários para autenticação de uma terceira parte confiável. Eles devem ser considerados como uma linguagem específica do domínio (DSL), ou seja, uma linguagem de computador especializada para um domínio de aplicativo específico com herança, instruções *se*, polimorfismo.

Essas políticas constituem a parte legível do computador do constructo do TF nas políticas personalizadas do Azure AD B2C aproveitando o Identity Experience Framework com todos os detalhes operacionais, incluindo metadados de provedores de declarações, definição de esquema de declarações de perfis técnicos, funções de transformação de declarações e percursos do usuário preenchidos para facilitar a automação e a orquestração operacional.  

Eles são considerados *documentos ativos* nas políticas personalizadas do Azure AD B2C aproveitando o Identity Experience Framework, uma vez que há grandes chances de que o conteúdo deles seja alterado ao longo do tempo em relação a participantes ativos declarados nas políticas e, também, em algumas situações com os termos e condições para ser um participante.  
A configuração de federação e manutenção são amplamente simplificadas pela reconfiguração e confiança contínua de conectividade já que os verificadores/provedores de declarações ingressam ou saem (representados pela comunidade) do conjunto de políticas.

A interoperabilidade é outro desafio significativo porque os verificadores/provedores de declarações adicionais devem ser integrados, como as partes confiáveis provavelmente não oferecem suporte a todos os protocolos necessários. As políticas personalizadas do Azure AD B2C, aproveitando o Identity Experience Framework, resolvem esse problema oferecendo suporte aos protocolos-padrão da indústria e aplicando percursos do usuário específico para transpor solicitações quando partes confiáveis e provedores de atributos não suportam o mesmo protocolo.  

Os percursos do usuário incluem perfis de protocolo e metadados que serão usados para inserir interoperabilidade "na rede" entre a terceira parte confiável e outros participantes.  Também há regras de tempo de execução operacional que serão aplicadas a mensagens de solicitação/resposta de informações de identidade para impor a conformidade com políticas publicadas como parte da especificação do TF. A ideia dos percursos do usuário é fundamental para a personalização da experiência do cliente e esclarece como o sistema funciona no nível do protocolo.

Com essa base, portais e aplicativos de terceiras partes confiáveis podem, dependendo de seu contexto, invocar as políticas personalizadas do Azure AD B2C aproveitando o Identity Experience Framework ao passar o nome de uma política específica e obter exatamente o comportamento e a troca de informações desejadas sem qualquer problema, confusão ou risco.

