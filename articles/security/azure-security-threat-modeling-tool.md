---
title: "A ferramenta - Azure de modelagem de ameaças do Microsoft | Microsoft Docs"
description: "página principal da ferramenta de modelagem de ameaças Microsoft, que contém atenuação para a mais exposta gerado ameaças"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: rodsan
translationtype: Human Translation
ms.sourcegitcommit: 2ad59f9b463671e2b74708871a9a9faae9bd24eb
ms.openlocfilehash: b75de472508bbf44c6e02e0d751054f37b0e1b8c
ms.lasthandoff: 02/21/2017


---

# <a name="microsoft-threat-modeling-tool"></a>Ferramenta de modelagem de ameaças do Microsoft 
A ferramenta de modelagem de ameaça é um elemento principal do Microsoft Security Development Lifecycle (SDL). Ele permite identificar e mitigar possíveis problemas de segurança no início, quando eles são relativamente fácil e econômica para resolver os arquitetos de software. Como resultado, ele reduz o custo total de desenvolvimento. Além disso, criamos a ferramenta com especialistas de não-segurança em mente, facilitando a modelagem de ameaças para todos os desenvolvedores fornecendo orientação clara sobre a criação e análise de modelos de ameaças. 

A ferramenta permite que qualquer pessoa:
* Comunicar sobre o design de segurança de seus sistemas
* Analisar esses designs para problemas potenciais de segurança usando uma metodologia comprovada
* Sugerir e gerenciar atenuações para problemas de segurança

Aqui estão alguns recursos de ferramentas e inovações, apenas para citar alguns:
* **Automação:** orientação e comentários em um modelo de desenho
* **STRIDE por elemento:** interativa de análise de ameaças e atenuações
* **Emissão de relatórios:** as atividades de segurança e testes na fase de verificação
* **Metodologia exclusiva:** permite aos usuários visualizar melhor e entender as ameaças
* **Projetado para desenvolvedores e centralizado no Software:** muitas abordagens são centralizadas em ativos ou invasores. Nós são centralizados em software. Criamos em atividades que todos os arquitetos e desenvolvedores de software estão familiarizados com-- como o desenho de imagens para sua arquitetura de software
* **Voltada para análise de Design:** a termo "modelagem de ameaças" pode se referir a um requisitos ou uma técnica de análise de design. Às vezes, ele se refere a uma mistura complexa de ambos. A abordagem de SDL da Microsoft para a modelagem de ameaças é uma técnica de análise de design focalizado

## <a name="threats"></a>Ameaças

A ferramenta de modelagem de ameaças ajuda a responder algumas perguntas, como aqueles abaixo:

* Como um invasor pode alterar os dados de autenticação?
* Qual é o impacto se um invasor pode ler os dados de perfil do usuário?
* O que acontece se o acesso é negado para o banco de dados de perfil do usuário?

A melhor ajuda você formula esses tipos de perguntas pontuais, a Microsoft usa o modelo STRIDE, que categoriza os diferentes tipos de ameaças e simplifica as conversas de segurança geral.

| Categoria | Descrição |
| -------- | ----------- |
| Falsificação | Envolve acessar ilegalmente e, em seguida, usando as informações de autenticação de outro usuário, como nome de usuário e senha |
| Violação | Envolve a modificação mal-intencionada de dados. Os exemplos incluem alterações não autorizadas feitas em dados persistentes, como as mantidas em um banco de dados e a alteração de dados enquanto estão fluindo entre dois computadores em uma rede aberta, como a Internet |
| Repúdio | Associado aos usuários que negam a execução de uma ação sem ter alguma maneira de provar o contrário — por exemplo, um usuário executa uma operação ilegal em um sistema que não tem capacidade para rastrear as operações proibidas. Não-repúdio refere-se à capacidade de um sistema para combater ameaças de repúdio. Por exemplo, um usuário que adquire um item pode ter que inscrever o item após o recebimento. O vendedor pode usar o recibo assinado como prova de que o usuário recebeu o pacote |
| Revelação de informações | Envolve a exposição de informações para os indivíduos que não devem para ter acesso a ele — por exemplo, a capacidade dos usuários para ler um arquivo que não tem permissão de acesso para ou a capacidade de um intruso ler dados em trânsito entre dois computadores |
| Negação de Serviço | Ataques negação de serviço (DoS) negar serviços a usuários válidos — por exemplo, fazendo um servidor Web temporariamente indisponível ou inutilizável. Você deve se proteger contra determinados tipos de ameaças DoS simplesmente para melhorar a confiabilidade e disponibilidade do sistema |
| Elevação de privilégio | Um usuário sem privilégios obtém acesso privilegiado e, portanto, tem acesso suficiente para comprometer ou destruir todo o sistema. Elevação de ameaças de privilégios incluem situações em que um invasor penetra de forma eficaz todas as defesas do sistema e se tornam parte do sistema confiável em si, uma situação perigosa, de fato |

## <a name="mitigations"></a>Atenuações

As atenuações da ferramenta de modelagem de ameaças são categorizadas de acordo com o quadro de segurança de aplicativo da Web, que consiste no seguinte:

| Categoria | Descrição |
| -------- | ----------- |
| [Auditoria e Log](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-auditing-and-logging) | Quem fez o que e quando? Auditoria e log consultem como seu aplicativo registra eventos relacionados à segurança |
| [Autenticação](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-authentication) | Quem é você? Autenticação é o processo no qual uma entidade comprova a identidade de outra entidade, normalmente por meio de credenciais, como um nome de usuário e senha |
| [Autorização](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-authorization) | O que você pode fazer? A autorização é como o seu aplicativo fornece controles de acesso para recursos e operações | 
| [Segurança da Comunicação](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-communication-security) | Que você está falando com? Segurança de comunicação garante que toda a comunicação feita é tão segura quanto possível |
| [Gerenciamento da Configuração](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-configuration-management) | Que seu aplicativo executar como? Quais bancos de dados ele se conecta ao? Como o seu aplicativo é administrado? Como essas configurações são protegidas? Gerenciamento de configuração se refere a como o seu aplicativo lida com esses problemas operacionais | 
| [Criptografia](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-cryptography) | Como você mantém os segredos (confidencialidade)? Como são à prova de adulteração seus dados ou bibliotecas (integridade)? Como você gera valores aleatórios que devem ser criptograficamente forte? Criptografia refere-se a como seu aplicativo impõe a integridade e confidencialidade | 
| [Gerenciamento de Exceções](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-exception-management) | Quando uma chamada de método no seu aplicativo falha, o que ele faz? Quanto você revela? Você retornar informações de erro amigável para usuários finais? Você transmite informações úteis de exceção para o chamador? Seu aplicativo não consegue normalmente? |
| [Validação da Entrada](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-input-validation) | Como você sabe que a entrada que recebe seu aplicativo é válidos e seguros? Validação de entrada se refere a como seu aplicativo filtra, limpa ou recusa dados antes do processamento adicional. Considere a restrição de entrada por meio de pontos de entrada e a codificação de saída por meio de pontos de saída. Você confia em dados de fontes, como bancos de dados e compartilhamentos de arquivos? |
| [Dados Confidenciais](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-sensitive-data) | Como o seu aplicativo lida com dados confidenciais? Dados confidenciais se refere a como seu aplicativo lida com dados que precisam ser protegidos na memória, pela rede, ou no armazenamento persistente | 
| [Gerenciamento da Sessão](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-session-management) | Como o seu aplicativo controlar e proteger as sessões de usuário? Uma sessão é uma série de interações relacionadas entre um usuário e o aplicativo da Web | 

Isso ajuda a identificar: 

* Onde os erros mais comuns são feitos
* Onde estão as melhorias mais viáveis

Como resultado, você pode usa essas categorias para se concentrar e priorizar seu trabalho de segurança, para que se você souber que os principais problemas de segurança ocorrem nas categorias de validação, autenticação e autorização de entrada, você pode iniciar lá. Para saber mais, clique [aqui](https://www.google.com/patents/US7818788)

## <a name="next-steps"></a>Próximas etapas
Confira estes links valiosos para começar hoje mesmo:
* [Link de download](https://www.microsoft.com/download/details.aspx?id=49168)
* [Introdução](https://msdn.microsoft.com/magazine/dd347831.aspx)
* [Treinamento Principal](https://www.microsoft.com/download/details.aspx?id=16420)

Enquanto você faz isso, confira o que fez alguns especialistas da ferramenta de modelagem de ameaças:
* [Gerenciador de ameaças](https://simoneonsecurity.com/threatsmanagersetup-v1-5-10/)
* [Blog de segurança Simone Curzi](https://simoneonsecurity.com/)
