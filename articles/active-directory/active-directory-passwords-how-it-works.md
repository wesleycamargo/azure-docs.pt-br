---
title: Como ele funciona? SSPR do Azure AD | Microsoft Docs
description: "Aprofundamento no autoatendimento de redefinição de senha no Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 71310534ec62b62bcd408d75060859c79bc470cf
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Aprofundamento no autoatendimento de redefinição de senha no Azure AD

Como funciona o SSPR? O que essa opção significa na interface? Continue lendo para saber mais sobre o autoatendimento de redefinição de senha do Azure AD.

## <a name="how-does-the-password-reset-portal-work"></a>Como funciona o portal de redefinição de senha

Quando um usuário navega para o portal de redefinição de senha, um fluxo de trabalho é iniciado para determinar:

   * Como a página deve ser localizada?
   * A conta de usuário é válida?
   * A qual organização o usuário pertence?
   * Em que local a senha do usuário é gerenciada?
   * O usuário é licenciado para usar o recurso?


Leia as etapas abaixo para saber mais sobre a lógica por trás da página de redefinição de senha.

1. O usuário clica no link Não consegue acessar sua conta ou acessa diretamente [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
2. Com base na localidade do navegador, a experiência é renderizada no idioma apropriado. A experiência de redefinição de senha é localizada nos mesmos idiomas para os quais o Office 365 dá suporte.
3. O usuário insere uma ID de usuário e passa um captcha.
4. O Azure AD verifica se o usuário é capaz de usar esse recurso fazendo as seguintes verificações:
   * Verifica se o usuário tem esse recurso habilitado e uma licença do Azure AD atribuída.
     * Se o usuário não tiver esse recurso habilitado ou uma licença atribuída, ele deverá contatar o administrador para redefinir sua senha.
   * Verifica se o usuário tem os dados de desafio corretos definidos em sua conta de acordo com a política do administrador.
     * Se a política exige apenas um desafio, fica garantido que o usuário tem os dados apropriados definidos em pelo menos um dos desafios habilitados pela política do administrador.
       * Se o usuário não estiver configurado, ele deverá contatar o administrador para redefinir sua senha.
     * Se a política exige dois desafios, fica garantido que o usuário tem os dados apropriados definidos em pelo menos dois dos desafios habilitados pela política do administrador.
       * Se o usuário não estiver configurado, ele deverá contatar o administrador para redefinir sua senha.
   * Verifica se a senha do usuário é gerenciada localmente (federada ou sincronizada com hash de senha).
     * Se o write-back estiver implantado e a senha do usuário for gerenciada localmente, o usuário poderá continuar a autenticação e a redefinição de sua senha.
     * Se o write-back não estiver implantado e a senha do usuário for gerenciada localmente, o usuário deverá contatar o administrador para redefinir sua senha.
5. Se for determinado que o usuário pode redefinir sua senha com êxito, ele será orientado pelo processo de redefinição.

## <a name="authentication-methods"></a>Métodos de autenticação

Se o SSPR (Autoatendimento de Redefinição de Senha) estiver habilitado, você deverá selecionar pelo menos um das opções a seguir como métodos de autenticação. Às vezes, essas opções também podem ser chamadas de portões. É altamente recomendável escolher pelo menos dois métodos de autenticação para que os usuários tenham mais flexibilidade.

* Email
* Telefone celular
* Telefone comercial
* Perguntas de segurança

![Autenticação][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-authentication-data"></a>Quais campos são usados no diretório para os dados de autenticação

* Telefone Comercial corresponde ao Telefone Comercial
    * Os usuários não conseguem definir esse campo por conta própria; ele deve ser definido por um administrador
* Telefone Celular corresponde ao Telefone de Autenticação (não visível publicamente) ou ao Telefone Celular (visível publicamente)
    * O serviço procura o Telefone de Autenticação primeiro e, em seguida, recorre ao Telefone Celular se aquele não estiver presente
* O Endereço de Email Alternativo corresponde ao Email de Autenticação (não visível publicamente) ou ao Email Alternativo
    * O serviço procura o Email de Autenticação primeiro e, em seguida, recorre ao Email Alternativo

Por padrão, somente os atributos de nuvem Telefone Comercial e Telefone Celular são sincronizados com o diretório de nuvem do seu diretório local para os dados de autenticação.

Os usuários somente poderão redefinir suas senhas se tiverem dados presentes nos métodos de autenticação habilitados e definidos como obrigatórios pelo administrador.

Se os usuários não desejarem que seus números de telefone celular fiquem visíveis no diretório, mas ainda desejarem usá-los para a redefinição de senha, os administradores não deverão populá-los no diretório e, em seguida, o usuário deverá popular seu atributo **Telefone de Autenticação** por meio do [portal de registro de redefinição de senha](http://aka.ms/ssprsetup). Os administradores poderão ver essas informações no perfil do usuário, mas elas não serão publicadas em nenhum outro lugar.

### <a name="number-of-authentication-methods-required"></a>Quantidade necessária de métodos de autenticação

Essa opção determina o número mínimo de métodos de autenticação disponíveis ou portões pelos quais um usuário deverá passar para redefinir ou desbloquear sua senha e pode ser definida como 1 ou 2.

Os usuários podem optar por fornecer mais métodos de autenticação se eles forem habilitados pelo administrador.

Se um usuário não tiver os métodos mínimos necessários registrados, ele verá uma página de erro que o direcionará para solicitar a um administrador para redefinir sua senha.

### <a name="how-secure-are-my-security-questions"></a>Qual o nível de segurança de minhas perguntas de segurança

Se você usar perguntas de segurança, recomendamos usá-las juntamente com outro método, pois elas podem ser menos seguras do que outros métodos, já que algumas pessoas podem saber as respostas para as perguntas de outros usuários.

> [!NOTE] 
> As perguntas de segurança são armazenadas de forma privada e protegida em um objeto de usuário no diretório e somente podem ser respondidas pelos usuários durante o registro. Não há nenhuma maneira de um administrador ler ou modificar as perguntas ou respostas de um usuário.
>

### <a name="security-question-localization"></a>Localização das perguntas de segurança

Todas as perguntas predefinidas a seguir são localizadas no conjunto completo de idiomas do Office 365 de acordo com a localidade do navegador do usuário.

* Em qual cidade você conheceu seu primeiro cônjuge/parceiro?
* Em qual cidade seus pais se conheceram?
* Em qual cidade seu irmão mais próximo mora?
* Em qual cidade seu pai nasceu?
* Em qual cidade você teve seu primeiro emprego?
* Em qual cidade sua mãe nasceu?
* Em qual cidade você estava no ano de 2000?
* Qual era o sobrenome de seu professor favorito no ensino médio*?
* Qual é o nome de uma faculdade que você tentou entrar, mas que não frequentou?
* Qual é o nome do lugar em que você realizou sua primeira festa de casamento?
* Qual é o segundo nome de seu pai?
* Qual é sua comida favorita?
* Qual é o nome e sobrenome de sua avó materna?
* Qual é o segundo nome de sua mãe?
* Qual é o mês e ano de aniversário de seu irmão mais velho? (por exemplo, novembro de 1985)
* Qual é o segundo nome de seu irmão mais velho?
* Qual é o nome e sobrenome de seu avô paterno?
* Qual é o segundo nome de seu irmão mais novo?
* Em qual escola você concluiu a sexta série?
* Qual era o nome e sobrenome de seu melhor amigo de infância?
* Qual era o nome e sobrenome de seu primeiro parceiro?
* Qual era o sobrenome de seu professor favorito no ensino médio?
* Qual era a marca e o modelo de seu primeiro carro ou sua primeira moto?
* Qual era o nome da primeira escola em que você estudou?
* Qual é o nome do hospital em que você nasceu?
* Qual é o nome da rua da primeira casa em que morou na infância?
* Qual é o nome de seu herói de infância?
* Qual é o nome de seu animal de pelúcia preferido?
* Qual era o nome de seu primeiro animal de estimação?
* Qual era seu apelido de infância?
* Qual era seu esporte favorito no ensino médio?
* Qual foi seu primeiro emprego?
* Quais eram os últimos quatro dígitos de seu primeiro número de telefone?
* Quando criança, o que você queria ser quando crescesse?
* Quem é a pessoa mais famosa que você já conheceu?

### <a name="custom-security-questions"></a>Perguntas de segurança personalizadas

As perguntas de segurança personalizadas não são localizadas para localidades diferentes. Todas as perguntas personalizadas são exibidas no mesmo idioma em que são inseridas na interface do usuário administrativo, mesmo se a localidade do navegador do usuário é diferente. Se precisar de perguntas localizadas, use as perguntas predefinidas.

O tamanho máximo de uma pergunta de segurança personalizada é de 200 caracteres.

### <a name="security-question-requirements"></a>Requisitos das perguntas de segurança

* O limite mínimo para a resposta é de 3 caracteres
* O limite máximo para a resposta é de 40 caracteres
* Os usuários não podem responder à mesma pergunta mais de uma vez
* Os usuários não podem fornecer a mesma resposta a mais de uma pergunta
* Qualquer conjunto de caracteres pode ser usado para definir as perguntas e respostas, incluindo caracteres Unicode
* O número de perguntas definidas deve ser maior ou igual ao número de perguntas obrigatórias para o registro

## <a name="registration"></a>Registro

### <a name="require-users-to-register-when-signing-in"></a>Exigir que os usuários se registrem ao entrar

A habilitação dessa opção exige que um usuário que está habilitado para a redefinição de senha conclua o registro de redefinição de senha caso ele faça logon em aplicativos que usam o Azure AD para conexão, como os seguintes:

* Office 365
* Portal do Azure
* Painel de acesso
* Aplicativos federados
* Aplicativos personalizados que usam o Azure AD

Quando estiver desabilitado, os usuários ainda poderão registrar manualmente suas informações de contato acessando [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) ou clicando no link **registrar para redefinição de senha** na guia Perfil do painel de acesso.

> [!NOTE]
> Os usuários podem ignorar o portal de registro de redefinição de senha clicando em Cancelar ou fechando a janela, mas verão esse prompt sempre que fizerem logon até concluírem o registro.
>

### <a name="number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Número de dias antes que os usuários precisem reconfirmar suas informações de autenticação

Essa opção determina o período entre a configuração e a reconfirmação das informações de autenticação e só estará disponível se você habilitar a opção **Exigir que os usuários se registrem ao entrar**.

Os valores válidos são 0 a 730 dias, com 0 indicando “Nunca solicitar aos usuários a reconfirmação de suas informações de autenticação”

## <a name="notifications"></a>Notificações

### <a name="notify-users-on-password-resets"></a>Notificar os usuários de redefinições de senha

Se essa opção for definida como Sim, o usuário que está redefinindo a senha receberá um email notificando-o de que sua senha foi alterada por meio do portal do SSPR em seus endereços de email primário e alternativo registrados no Azure AD. Ninguém mais é notificado desse evento de redefinição.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Notificar todos os administradores quando outros administradores redefinirem suas senhas

Se essa opção for definida como Sim, **todos os administradores** receberão um email em seu endereço de email primário registrado no Azure AD notificando-os de que outro administrador alterou sua senha usando o SSPR.

Exemplo: Há quatro administradores em um ambiente. O administrador “A” redefine sua senha usando o SSPR. Os administradores B, C e D recebem um email avisando-os que isso está ocorrendo.

## <a name="on-premises-integration"></a>Integração local

Se você instalou, configurou e habilitou o Azure AD Connect, terá as seguintes opções adicionais para as integrações locais.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Write-back de senhas para o diretório local

Controla se o write-back de senha está habilitado para esse diretório e, se estiver, indica o status do serviço de write-back local. Isso será útil se você desejar desabilitar o write-back de senha temporariamente sem reconfigurar o Azure AD Connect.

* Se a opção estiver definida como Sim, o write-back será habilitado e os usuários federados e sincronizados com hash de senha poderão redefinir suas senhas.
* Se a opção estiver definida como Não, o write-back será desabilitado e os usuários federados e sincronizados com hash de senha não poderão redefinir suas senhas.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Permitir aos usuários desbloquear contas sem redefinir a senha

Determina se os usuários que visitam o portal de redefinição de senha receber a opção de desbloquear suas contas locais do Active Directory sem redefinir a senha. Por padrão, o Azure AD desbloqueia contas ao executar uma redefinição de senha, essa configuração permite que você separe as duas operações. 

* Se for definido como "Sim", os usuários terão a opção de redefinir suas senhas e desbloquear a conta ou desbloquear sem redefinir a senha.
* Se for definido como "Não", os usuários só poderão executar uma operação combinada de redefinição de senha e de desbloqueio de conta.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Como a redefinição de senha funciona para usuários B2B?
A redefinição e a alteração de senhas têm suporte completo em todas as configurações B2B. Os três casos a seguir dão suporte à redefinição de senha de usuário de B2B.

1. **Usuários de uma organização de parceiros com um locatário existente do Azure AD** – se a organização com a qual você está fazendo uma parceria tiver um locatário existente do Azure AD, **respeitaremos todas as políticas de redefinição de senha habilitadas no locatário**. Para que a redefinição de senha funcione, a organização parceira só precisa garantie que o Azure AD SSPR esteja habilitado, o que não acarreta custo adicional para os clientes do O365 e pode ser habilitado seguindo as etapas em nosso guia de [Introdução ao gerenciamento de senhas](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords).
2. **Usuários que se inscreveram usando a [inscrição para autoatendimento](active-directory-self-service-signup.md)** – se a organização com a qual você está fazendo uma parceria tiver usado o recurso de [inscrição para autoatendimento](active-directory-self-service-signup.md) para entrar em um locatário, permitiremos que eles redefinam com o email com o qual se registraram.
3. **Usuários B2B** – os novos usuários B2B criados com as novas [funcionalidades do Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md) também poderão redefinir suas senhas com o email com o qual se registraram durante o processo de convite.

Para testar esse cenário, acesse http://passwordreset.microsoftonline.com com um desses usuários parceiros. Desde que eles tenham um email alternativo ou um email de autenticação definido, a redefinição de senha funcionará como esperado.

## <a name="next-steps"></a>Próximas etapas

Os links a seguir fornecem informações adicionais sobre a redefinição de senha usando o Azure AD

* [Como concluir uma implementação do SSPR com êxito?](active-directory-passwords-best-practices.md)
* [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md).
* [Registro para redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md).
* [Você tem uma pergunta sobre licenciamento?](active-directory-passwords-licensing.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](active-directory-passwords-data.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](active-directory-passwords-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](active-directory-passwords-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](active-directory-passwords-how-it-works.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)

[Authentication]: ./media/active-directory-passwords-how-it-works/sspr-authentication-methods.png "Métodos de autenticação do Azure AD e quantidade necessária"