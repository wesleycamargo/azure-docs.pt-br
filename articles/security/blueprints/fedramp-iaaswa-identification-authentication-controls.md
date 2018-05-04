---
title: "Segurança e Conformidade do Azure Blueprint – Automação de Aplicativos Web para FedRAMP – Identificação e Autorização"
description: "Automação de Aplicativos Web para FedRAMP – Identificação e Autenticação"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1033f63f-daf0-4174-a7f6-7b0f569020e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 21b5c453716f99be26c8dd6400bb3489477b4956
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2018
---
# <a name="identification-and-authentication-ia"></a>Identificação e Autenticação (IA)

## <a name="nist-800-53-control-ia-1"></a>Controle NIST 800-53 AU-1

> [!NOTE]
> Estes controles são definidos pelo NIST e pelos EUA. Departamento de Comércio dos EUA como parte da Publicação especial do NIST 800-53 Revisão 4. Consulte o NIST 800-53 Rev. 4 para obter informações sobre os procedimentos de teste e orientações para cada controle.

#### <a name="identification-and-authentication-policy-and-procedures"></a>Política de Identificação e Autenticação e Procedimentos

**IA-1** A organização desenvolve, documenta e distribui à [Atribuição: equipe ou funções definidas pela organização] uma política de identificação e autenticação que aborda a finalidade, o escopo, as funções, as responsabilidades, o compromisso de gerenciamento, a coordenação entre entidades organizacionais e a conformidade; e os procedimentos para facilitar a implementação da política de identificação e autenticação e os controles de identificação e autenticação; e revisa e atualiza a política de proteção de identificação e autenticação atual [Atribuição: frequência definida pela organização]; e os procedimentos de identificação e autenticação [Atribuição: frequência definida pela organização].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A política e os procedimentos de identificação e autenticação de nível empresarial do cliente podem ser suficientes para atender a esse controle. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-2"></a>Controle NIST 800-53 IA-2

#### <a name="identification-and-authentication-organizational-users"></a>Identificação e autenticação (Usuários organizacionais)

**IA-2** O sistema de informações exclusivamente identifica e autentica usuários organizacionais (ou processos atuando em nome dos usuários organizacionais).

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Contas criadas por esse projeto têm identificadores exclusivos. Contas internas com identificadores não exclusivos são desabilitadas ou removidas. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-2-1"></a>Controle NIST 800-53 IA-2 (1)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Identificação e autenticação (usuários organizacionais) | Acesso à rede para contas com privilégios

**IA-2 (1)** O sistema de informações implementa a autenticação multifator para acesso à rede para contas com privilégios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por implementar a autenticação multifator para acesso à rede para contas com privilégios. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-2-2"></a>Controle NIST 800-53 IA-2 (2)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Identificação e autenticação (usuários organizacionais) | Acesso à rede para contas sem privilégios

**IA-2 (2)** O sistema de informações implementa a autenticação multifator para acesso à rede para contas sem privilégios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por implementar a autenticação multifator para acesso à rede para contas sem privilégios. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-2-3"></a>Controle NIST 800-53 IA-2 (3)

#### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>Identificação e autenticação (usuários organizacionais) | Acesso local para contas com privilégios

**IA-2 (3)** O sistema de informações implementa a autenticação multifator para acesso local para contas com privilégios.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso local a nenhum recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure não permite o acesso local, a menos que o acesso físico seja necessário. O acesso do Administrador Local deve ser usado somente para solucionar problemas em situações em que o servidor membro está com problemas de rede e a autenticação de domínio não está funcionando. <br /> O Azure implementa a autenticação multifator para acesso local por meio de mecanismos de controle de acesso necessários para o acessar físico ao ambiente. Salas em datacenters do Azure que contêm todos os sistemas de infraestrutura do Azure dentro do limite do sistema são restringidas por meio de vários mecanismos de segurança física, incluindo a necessidade de acesso por cartão inteligente corporativo e dispositivos biométricos. As duas formas de autenticação são necessárias para o acesso físico no ponto de entrada a colocações do datacenter do Azure. |


 ### <a name="nist-800-53-control-ia-2-4"></a>Controle NIST 800-53 IA-2 (4)

#### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>Identificação e autenticação (usuários organizacionais) | Acesso local para contas sem privilégios

**IA-2 (4)** O sistema de informações implementa a autenticação multifator para acesso local para contas sem privilégios.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso local a nenhum recurso do sistema em datacenters do Azure. |
| **Fornecedor (Microsoft Azure)** | O Microsoft Azure considera todas as contas do Microsoft Azure Governamental usadas pela equipe do Microsoft Azure Governamental como com privilégios. A autenticação multifator é implementada em todas as contas da equipe do Microsoft Azure Governamental usando cartões inteligentes e PINs, o que inclui acesso local. |


 ### <a name="nist-800-53-control-ia-2-5"></a>Controle NIST 800-53 IA-2 (5)

#### <a name="identification-and-authentication-organizational-users--group-authentication"></a>Identificação e autenticação (Usuários organizacionais) | Autenticação de grupo

**IA-2 (5)** A organização exige que os indivíduos sejam autenticados com um autenticador individual quando um autenticador de grupo é utilizado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As contas compartilhadas/de grupo não são habilitadas nos recursos implantados por esse projeto. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-2-8"></a>Controle NIST 800-53 IA-2 (8)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>Identificação e autenticação (usuários organizacionais) | Acesso à rede para contas com privilégios - Resistente à reprodução

**IA-2 (8)** O sistema de informações implementa a autenticação resistente à repetição para acesso à rede para contas com privilégios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O acesso a recursos implantados por este projeto é protegido contra ataques de repetição pela funcionalidade interna Kerberos do Azure Active Directory, pelo Active Directory e pelo sistema operacional Windows. Na autenticação Kerberos, o autenticador enviado pelo cliente contém dados adicionais, como uma lista de IP criptografada, carimbos de data/hora do cliente e vida útil do tíquete. Se um pacote é repetido, o carimbo de data/hora é verificado. Se o carimbo de data/hora for anterior ou igual a um autenticador anterior, o pacote será rejeitado. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-2-9"></a>Controle NIST 800-53 IA-2 (9)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>Identificação e Autenticação (Usuários Organizacionais) | Acesso à rede para Contas Sem Privilégios - Resistente à Repetição

**IA-2 (9)** O sistema de informações implementa a autenticação resistente à repetição para acesso à rede para contas sem privilégios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O acesso a recursos implantados por este projeto é protegido contra ataques de repetição pela funcionalidade interna Kerberos do Azure Active Directory, pelo Active Directory e pelo sistema operacional Windows. Na autenticação Kerberos, o autenticador enviado pelo cliente contém dados adicionais, como uma lista de IP criptografada, carimbos de data/hora do cliente e vida útil do tíquete. Se um pacote é repetido, o carimbo de data/hora é verificado. Se o carimbo de data/hora for anterior ou igual a um autenticador anterior, o pacote será rejeitado. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-2-11"></a>Controle NIST 800-53 IA-2 (11)

#### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>Identificação e Autenticação (Usuários Organizacionais) | Acesso Remoto - Dispositivo Separado

**IA-2 (11)** O sistema de informações implementa a autenticação multifator para acesso remoto de contas com e sem privilégios, de modo que um dos fatores é fornecido por um dispositivo separado do sistema que está realizando o acesso e o dispositivo atende aos [Atribuição: requisitos de força do mecanismo definidos pela organização].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por implementar a autenticação multifator para acessar remotamente recursos implantados pelo cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-2-12"></a>Controle NIST 800-53 IA-2 (12)

#### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>Identificação e Autenticação (Usuários Organizacionais) | Aceitação de credenciais PIV

**IA-2 (12)** O sistema de informações aceita e eletronicamente verifica as credenciais de Verificação de Identidade Pessoal (PIV).

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por aceitar e verificar as Credenciais de Verificação de Identidade Pessoal (PIV). |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-3"></a>Controle NIST 800-53 IA-3

#### <a name="device-identification-and-authentication"></a>Identificação e Autenticação de Dispositivo

**IA-3** O sistema de informações identifica exclusivamente e autentica [Atribuição: específico definido pela organização e/ou tipos de dispositivos] antes de estabelecer uma [seleção (um ou mais): local; remoto; rede] conexão.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por implementar a autenticação e identificação do dispositivo. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-4a"></a>Controle NIST 800-53 IA-4.a

#### <a name="identifier-management"></a>Gerenciamento de Identificador

**IA-4.a** A organização gerencia os identificadores de informações do sistema através do recebimento de autorização de [Atribuição: pessoal ou funções definidas pela organização] para atribuir um indivíduo, o identificador de grupo, função ou dispositivo.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por gerenciar identificadores (ou seja, indivíduos, grupos, funções e dispositivos) para recursos de cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-4b"></a>Controle NIST 800-53 IA-4.b

#### <a name="identifier-management"></a>Gerenciamento de Identificador

**IA-4.b** A organização gerencia os identificadores do sistema de informações, selecionando um identificador que identifica um indivíduo, grupo, função ou dispositivo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Durante a implantação, este projeto solicita identificadores especificados pelo cliente para contas individuais.  |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-4c"></a>Controle NIST 800-53 IA-4.c

#### <a name="identifier-management"></a>Gerenciamento de Identificador

**IA-4.c** A organização gerencia os identificadores do sistema de informações, atribuindo o identificador ao indivíduo, grupo, função ou dispositivo pretendido.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por gerenciar identificadores (ou seja, indivíduos, grupos, funções e dispositivos) para recursos de cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-4d"></a>NIST 800-53 Controle IA-4.d

#### <a name="identifier-management"></a>Gerenciamento de Identificador

**IA-4.d** A organização gerencia os identificadores de informações do sistema, impedindo a reutilização de identificadores para [Atribuição: período de tempo definido pelo organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Active Directory e contas locais do sistema operacional Windows são atribuídas a um identificador de segurança exclusivo (SID). Contas do Active Directory do Azure são atribuídas a uma ID de objeto exclusivo global. Essas IDs exclusivas não estão sujeitas à reutilização. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-4e"></a>Controle NIST 800-53 IA-4.e

#### <a name="identifier-management"></a>Gerenciamento de Identificador

**IA-4.e** A organização gerencia os identificadores de informações do sistema, impedindo a reutilização de identificadores depois de [Atribuição: período de tempo de inatividade definido pelo organização].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este projeto implementa uma tarefa agendada para o Active Directory desabilitar automaticamente contas depois de 35 dias de inatividade. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-4-4"></a>Controle NIST 800-53 IA-4 (4)

#### <a name="identifier-management--identify-user-status"></a>Gerenciamento de Identificador | Identificar o Status do Usuário

**IA-4 (4)** A organização gerencia identificadores individuais ao identificar exclusivamente cada indivíduo como [Atribuição: característica definida pela organização identificando status individuais].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Azure Active Directory e suporte do Active Directory denotando prestadores de serviços, fornecedores e outros tipos de usuário usando as convenções de nomenclatura aplicadas a seus identificadores. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-5a"></a>NIST 800-53 Controle IA-5.a

#### <a name="authenticator-management"></a>Gerenciamento de autenticador

**IA-5.a** A organização gerencia autenticadores de sistema de informações verificando, como parte da distribuição inicial do autenticador, a identidade da pessoa, grupo, função ou dispositivo recebendo o autenticador.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por gerenciar autenticadores. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-5b"></a>Controle NIST 800-53 IA-5.b

#### <a name="authenticator-management"></a>Gerenciamento de autenticador

**IA-5.b** A organização gerencia autenticadores de sistema de informações estabelecendo conteúdo inicial do autenticador para autenticadores definidas pela organização.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Todo o conteúdo inicial do autenticador para contas criadas por esse projeto atende aos requisitos mencionados em IA-5 (1), verificado quando especificado pelo cliente durante a implantação.  |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-5c"></a>Controle NIST 800-53 IA-5.c

#### <a name="authenticator-management"></a>Gerenciamento de autenticador

**IA-5.c** A organização gerencia autenticadores de sistema de informações ao garantir que autenticadores tenham força suficiente de mecanismo para seu uso pretendido.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Autenticadores usados por este projeto atendem aos requisitos de força conforme exigido pelo FedRAMP. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-5d"></a>NIST 800-53 Controle IA-5.d

#### <a name="authenticator-management"></a>Gerenciamento de autenticador

**IA-5.d** A organização gerencia autenticadores de sistema de informações estabelecendo e implementando procedimentos administrativos para distribuição inicial de autenticador, para autenticadores danificados ou perdidos/comprometidos e para revogar autenticadores.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por gerenciar autenticadores. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-5e"></a>NIST 800-53 Controle IA-5.e

#### <a name="authenticator-management"></a>Gerenciamento de autenticador

**IA-5.e** A organização gerencia autenticadores de sistema de informações por meio da alteração de conteúdo padrão do autenticadores antes da instalação do sistema de informações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Todos os autenticadores para componentes deste projeto foram alterados em relação aos padrões. Os autenticadores são especificados pelo cliente durante a implantação dessa solução. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-5f"></a>NIST 800-53 Controle IA-5.f

#### <a name="authenticator-management"></a>Gerenciamento de autenticador

**IA-5.f** A organização gerencia autenticadores de sistema de informações ao estabelecer restrições de tempo de vida mínimo e máximo e reutilização condições autenticadores.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por gerenciar autenticadores. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-5g"></a>NIST 800-53 Controle IA-5.g

#### <a name="authenticator-management"></a>Gerenciamento de autenticador

**IA-5.g** A organização gerencia autenticadores de sistema de informações alterando/atualizando autenticadores [Atribuição: período de tempo definido pela organização pelo tipo de autenticador].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este projeto implanta um controlador de domínio ao qual todas as máquinas virtuais implantadas são unidas. Uma política de grupo é estabelecida e configurada para implementar restrições de tempo de vida da senha (60 dias). |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-5h"></a>NIST 800-53 Controle IA-5.h

#### <a name="authenticator-management"></a>Gerenciamento de autenticador

**IA-5.h** A organização gerencia autenticadores de sistema de informações ao proteger o conteúdo do autenticador contra divulgação não autorizada e modificação.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este projeto implementa o Key Vault para proteger o conteúdo do autenticador contra modificação e a divulgação não autorizada. Os autenticadores a seguir são armazenadas no Key Vault: senha do Azure para implantar conta, senha de administrador da máquina virtual, senha de conta de serviço do SQL Server. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-5i"></a>Controle NIST 800-53 IA-5.i

#### <a name="authenticator-management"></a>Gerenciamento de autenticador

**IA-5.i** A organização gerencia autenticadores de sistema de informações ao exigir que os indivíduos tomem, e tendo implementos de dispositivos, as proteções de segurança específica para proteger os autenticadores.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este projeto implementa o Key Vault para proteger o conteúdo do autenticador contra modificação e a divulgação não autorizada. Os autenticadores a seguir são armazenadas no Key Vault: senha do Azure para implantar conta, senha de administrador da máquina virtual, senha de conta de serviço do SQL Server. O Cofre de Chaves criptografa chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados e senhas) usando chaves que são protegidas por HSMs (módulos de segurança de hardware). |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-5j"></a>NIST 800-53 Controle IA-5.j

#### <a name="authenticator-management"></a>Gerenciamento de autenticador

**IA-5.j** A organização gerencia autenticadores de sistema de informações alterando autenticadores para contas de grupo/função quando se associam às contas de alterações.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | As contas compartilhadas/de grupo não são habilitadas nos recursos implantados por esse projeto. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-1a"></a>Controle NIST 800-53 IA-5 (1).a

#### <a name="authenticator-management--password-based-authentication"></a>Gerenciamento de autenticador | Autenticação Baseada em Senha

**IA-5 (1).a** O sistema de informações, para autenticação baseada em senha impõe a complexidade de senha mínimo de [atribuição: requisitos de organização definido para diferenciar maiúsculas de minúsculas, número de caracteres, uma combinação de letras maiúsculas, minúsculas, números e caracteres especiais, incluindo os requisitos mínimos para cada tipo].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este projeto implanta um controlador de domínio ao qual todas as máquinas virtuais implantadas são unidas. Uma política de grupo é estabelecida e configurada para impor requisitos de complexidade de senha para contas locais da máquina virtual e contas do AD.  |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-1b"></a>Controle NIST 800-53 IA-5 (1).b

#### <a name="authenticator-management--password-based-authentication"></a>Gerenciamento de autenticador | Autenticação Baseada em Senha

**IA-5 (1).b** O sistema de informações, para autenticação baseada em senha pelo menos impõe o seguinte número de caracteres alterados quando são criadas novas senhas: [Atribuição: número definido pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por utilizar autenticação baseada em senha dentro de recursos de implantação de cliente. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-1c"></a>Controle NIST 800-53 IA-5 (1).c

#### <a name="authenticator-management--password-based-authentication"></a>Gerenciamento de autenticador | Autenticação Baseada em Senha

**IA-5 (1).c** O sistema de informações, para autenticação baseada em senha armazena e transmite senhas somente criptograficamente protegidas.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O Azure Active Directory é usado para garantir que todas as senhas sejam protegidas por criptografia enquanto armazenados e transmitidas. Senhas armazenadas pelo Active Directory e localmente em máquinas virtuais Windows implantadas são codificadas em hash automaticamente como parte da funcionalidade de segurança interna. Sessões de autenticação de área de trabalho remota são protegidas usando TLS para garantir que os autenticadores sejam protegidos quando transmitidos. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-1d"></a>Controle NIST 800-53 IA-5 (1).d

#### <a name="authenticator-management--password-based-authentication"></a>Gerenciamento de autenticador | Autenticação Baseada em Senha

**IA-5 (1).d** O sistema de informações, para autenticação baseada em senha impõe mínimo da senha e restrições de tempo de vida máximo de [Atribuição: números definidos pela organização para o tempo de vida mínimo, tempo de vida máximo].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este projeto implanta um controlador de domínio ao qual todas as máquinas virtuais implantadas são unidas. Uma política de grupo é estabelecida e configurada para impor restrições de senhas que impõem mínimo (1 dia) e o tempo de vida máximo (60 dias) restrições para contas locais e contas do AD. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-1e"></a>Controle NIST 800-53 IA-5 (1).e

#### <a name="authenticator-management--password-based-authentication"></a>Gerenciamento de autenticador | Autenticação Baseada em Senha

**IA-5 (1).e** O sistema de informações, para autenticação baseada em senha impede a reutilização de senha para [Atribuição: número definido de organização] gerações.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este projeto implanta um controlador de domínio ao qual todas as máquinas virtuais implantadas são unidas. Uma política de grupo é estabelecida e configurada para impor restrições sobre condições de reutilização (24 senhas) para contas locais e contas do AD. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-1f"></a>Controle NIST 800-53 IA-5 (1).f

#### <a name="authenticator-management--password-based-authentication"></a>Gerenciamento de autenticador | Autenticação Baseada em Senha

**IA-5 (1).f** O sistema de informações, para autenticação baseada em senha permite o uso de uma senha temporária para logons do sistema com uma alteração de imediata para uma senha permanente.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O Azure Active Directory é usado para gerenciar o controle de acesso ao sistema de informações. Sempre que uma conta é criada inicialmente ou uma senha temporária é gerada, o Azure Active Directory é utilizado para exigir que o usuário altere a senha no próximo logon. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-2a"></a>Controle NIST 800-53 IA-5 (2).a

#### <a name="authenticator-management--pki-based-authentication"></a>Gerenciamento de autenticador | Autenticação Baseada em Pki

**IA-5 (2) .a** O sistema de informações, para a autenticação baseada em PKI, valida certificações construindo e verificando um caminho de certificação para uma âncora de confiança aceita incluindo a verificação das informações de status do certificado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por utilizar autenticação baseada em PKI dentro de recursos de implantação de cliente. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-2b"></a>Controle NIST 800-53 IA-5 (2).b

#### <a name="authenticator-management--pki-based-authentication"></a>Gerenciamento de autenticador | Autenticação Baseada em Pki

**IA-5 (2).b** O sistema de informações, para a autenticação baseada em PKI, impõe acesso autorizado à chave privada correspondente.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por utilizar autenticação baseada em PKI dentro de recursos de implantação de cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-2c"></a>Controle NIST 800-53 IA-5 (2).c

#### <a name="authenticator-management--pki-based-authentication"></a>Gerenciamento de autenticador | Autenticação Baseada em Pki

**IA-5 (2).c** O sistema de informações, para a autenticação baseada em PKI, mapeia a identidade autenticada à conta do indivíduo ou grupo.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por utilizar autenticação baseada em PKI dentro de recursos de implantação de cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-2d"></a>Controle NIST 800-53 IA-5 (2).d

#### <a name="authenticator-management--pki-based-authentication"></a>Gerenciamento de autenticador | Autenticação Baseada em Pki

**IA-5 (2).d** O sistema de informações, para a autenticação baseada em PKI, implementa um cache local de dados de revogação para dar suporte à descoberta de caminho e à validação em caso de dificuldades para acessar as informações de revogação através da rede.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por utilizar autenticação baseada em PKI dentro de recursos de implantação de cliente. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-3"></a>Controle NIST 800-53 IA-5 (3)

#### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>Gerenciamento de autenticador | Registro em pessoa ou por terceiros confiáveis

**IA-5 (3)** A organização requer que o processo de registro para receber [Atribuição: tipos de e/ou autenticadores específicos definidos pela organização] seja conduzido [Seleção: pessoalmente; por um terceiro confiável] antes de [Atribuição: autoridade de registro definida pela organização] com autorização por [Atribuição: pessoal ou funções definidas pela organização].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por registrar autenticadores. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-4"></a>Controle NIST 800-53 IA-5 (4)

#### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>Gerenciamento de autenticador | Suporte automatizado para determinação de força de senha

**IA-5 (4)** A organização emprega ferramentas automatizadas para determinar se autenticadores de senha são suficientemente fortes para atender aos [Atribuição: requisitos definidos pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Contas de usuário implantadas com este projeto incluem contas de usuário local e do AD. Ambos fornecem mecanismos que forçam a conformidade com os requisitos de senha estabelecidos para criar uma senha inicial e durante alterações de senha. O Azure Active Directory é a ferramenta automatizada adotada para determinar se há autenticadores de senha suficientemente fortes para satisfazer às restrições de comprimento, complexidade, rotação e tempo de vida de senha estabelecidas em IA-5 (1). O Azure Active Directory garante que a intensidade de autenticador de senha no momento da criação atenda a esses padrões. Senhas especificadas pelo cliente usadas para implantar essa solução são verificadas para que atendam aos requisitos de força de senha. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-6"></a>Controle NIST 800-53 IA-5 (6)

#### <a name="authenticator-management--protection-of-authenticators"></a>Gerenciamento de autenticador | Proteção dos autenticadores

**IA-5 (6)** A organização protege autenticadores proporcionais à categoria de segurança das informações às quais o uso do autenticado permite o acesso.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por proteger autenticadores. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-7"></a>Controle NIST 800-53 IA-5 (7)

#### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>Gerenciamento de autenticador | Nenhum autenticador estático não criptografado incorporado

**IA-5 (7)** A organização garante que autenticadores estáticos não criptografadas não sejam incorporados a aplicativos ou scripts de acesso nem armazenados em teclas de função.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Não há uso de autenticadores estáticos não criptografadas incorporados a aplicativos, scripts de acesso ou teclas de função implantadas por este projeto. Qualquer script ou aplicativo que usa um autenticador faz uma chamada para um contêiner do Azure Key Vault antes de cada uso. O acesso aos contêineres do Azure Key Vault é auditado, o que permite a detecção de violações dessa proibição se uma conta de serviço é usada para acessar um sistema sem uma chamada correspondente para o contêiner do Azure Key Vault. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-8"></a>Controle NIST 800-53 IA-5 (8)

#### <a name="authenticator-management--multiple-information-system-accounts"></a>Gerenciamento de autenticador | Várias contas de sistema de informações

**IA-5 (8)** A organização implementa [Atribuição: proteções de segurança definidas pela organização] para gerenciar o risco de comprometimento devido a indivíduos com contas em vários sistemas de informações.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente pode depender de garantias de segurança de nível corporativo para gerenciar o risco associado a indivíduos terem contas em vários sistemas. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-11"></a>Controle NIST 800-53 IA-5 (11)

#### <a name="authenticator-management--hardware-token-based-authentication"></a>Gerenciamento de autenticador | Autenticação Baseada em Hardware Token

**IA-5 (11)** O sistema de informações, para autenticação baseada em token de hardware, emprega mecanismos que satisfazem [Atribuição: requisitos de qualidade de token definidos pela organização].

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por empregar mecanismos para atender aos requisitos de qualidade de autenticação baseada em token de hardware. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-5-13"></a>Controle NIST 800-53 IA-5 (13)

#### <a name="authenticator-management--expiration-of-cached-authenticators"></a>Gerenciamento de autenticador | Expiração de Autenticadores de Cache

**IA-5 (13)** O sistema de informações proíbe o uso de autenticadores em cache depois de [Atribuição: período de tempo definido pela organização].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Nenhum recurso implantado por este esquema é configurado para permitir o uso de autenticadores armazenados em cache. A autenticação para máquinas virtuais implantadas requer que um autenticador seja inserido no momento da autenticação. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-6"></a>Controle NIST 800-53 IA-6

#### <a name="authenticator-feedback"></a>Comentários do autenticador

**IA-6** O sistema de informações oculta comentários das informações de autenticação durante o processo de autenticação para proteger as informações contra possível exploração/uso por pessoas não autorizadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O acesso aos recursos implantados por este projeto é feito por meio da Área de Trabalho Remota e se baseia na autenticação do Windows. O comportamento padrão das sessões de autenticação do Windows oculta as senhas quando inseridas durante uma sessão de autenticação.  |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-7"></a>Controle NIST 800-53 IA-7

#### <a name="cryptographic-module-authentication"></a>Autenticação de Módulo Criptográfico

**IA-7** O sistema de informações implementa mecanismos para autenticação para um módulo criptográfico que satisfaça os requisitos de leis federais aplicáveis, Ordens Executivas, diretores, políticas, regulações, padrões, e diretrizes para tal autenticação.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | A autenticação do Windows, a área de trabalho remota e o BitLocker são empregados por este projeto. Esses componentes podem ser configurados para confiar em módulos criptográficos validados por FIPS 140. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ## <a name="nist-800-53-control-ia-8"></a>Controle NIST 800-53 IA-8

#### <a name="identification-and-authentication-non-organizational-users"></a>Identificação e Autenticação (Usuários Não Organizacionais)

**IA-8** O sistema de informações exclusivamente identifica e autentica usuários não organizacionais (ou processos atuando em nome dos usuários não organizacionais).

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por identificar e autenticar usuários não organizacionais acessando recursos implantados pelo cliente. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-8-1"></a>Controle NIST 800-53 IA-8 (1)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>Identificação e Autenticação (Usuários não organizacionais) | Aceitação de credenciais PIV de outras agências

**IA-8 (1)** O sistema de informações aceita e eletronicamente verifica as credenciais de Verificação de Identidade Pessoal (PIV) de outras agências federais.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por aceitar e verificar as Credenciais de Verificação de Identidade Pessoal (PIV) emitidas por outras agências federais. |
| **Provedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-8-2"></a>Controle NIST 800-53 IA-8 (2)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>Identificação e Autenticação (Usuários não organizacionais) | Aceitação de credenciais de terceiros

**IA-8 (2)** O sistema de informações aceita somente credenciais de terceiros aprovados pelo FICAM.

**Responsibilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por aceitar apenas credenciais de terceiros que foram aprovadas pela iniciativa de soluções de estrutura confiável do Gerenciamento Federal de Identidade, Credencial e Acesso (FICAM). |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-8-3"></a>Controle NIST 800-53 IA-8 (3)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>Identificação e Autenticação (Usuários Não Organizacionais) | Uso de produtos aprovados pelo FICAM

**IA-8 (3)** A organização emprega somente informações aprovadas FICAM componentes de sistema [Atribuição: sistemas de informações da organização definido] para aceitar credenciais de terceiros.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por empregar apenas Identidade Federal, Credencial, e iniciativa de Soluções de Estrutura Confiável de Gerenciamento de Acesso (FICAM) para aceitar credenciais de terceiros. |
| **Fornecedor (Microsoft Azure)** | Não aplicável |


 ### <a name="nist-800-53-control-ia-8-4"></a>Controle NIST 800-53 IA-8 (4)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>Identificação e Autenticação (Usuários Não Organizacionais) | Uso de Produtos Emitidos por FICAM

**IA-8 (4)** O sistema de informações está em conformidade com perfis emitidos por FICAM.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por estar em conformidade com os perfis emitidos pela Identidade Federal, Credencial, e iniciativa de Soluções de Estrutura Confiável de Gerenciamento de Acesso (FICAM). |
| **Fornecedor (Microsoft Azure)** | Não aplicável |
