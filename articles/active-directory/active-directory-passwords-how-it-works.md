---
title: Como a SSPR do Azure Active Directory funciona | Microsoft Docs
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
ms.openlocfilehash: 56ddd5742b63851b9477bae0705ebd24e30ff185
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Aprofundamento no autoatendimento de redefinição de senha no Azure AD

Como funciona a SSPR (redefinição de senha por autoatendimento)? O que essa opção significa na interface? Continue lendo para saber mais sobre a SSPR do Azure AD (Azure Active Directory).

## <a name="how-does-the-password-reset-portal-work"></a>Como funciona o portal de redefinição de senha?

Quando um usuário entra no portal de redefinição de senha, um fluxo de trabalho é iniciado para determinar:

   * Como a página deve ser localizada?
   * A conta de usuário é válida?
   * A qual organização o usuário pertence?
   * Onde a senha do usuário é gerenciada?
   * O usuário é licenciado para usar o recurso?

Leia as etapas abaixo para saber mais sobre a lógica por trás da página de redefinição de senha:

1. O usuário seleciona o link **Não consegue acessar sua conta ou acessa diretamente** ou vai diretamente para [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
   * Com base na localidade do navegador, a experiência é renderizada no idioma apropriado. A experiência de redefinição de senha é localizada nos mesmos idiomas para os quais o Office 365 dá suporte.
2. O usuário insere uma ID de usuário e passa um captcha.
3. O Azure Active Directory verifica se o usuário está apto para usar esse recurso fazendo as seguintes verificações:
   * Verifica se o usuário possui esse recurso habilitado e uma licença do Azure Active Directory atribuída.
     * Se o usuário não tiver esse recurso habilitado ou uma licença atribuída, ele deverá contatar o administrador para redefinir sua senha.
   * Verifica se o usuário tem os dados de desafio corretos definidos em sua conta de acordo com a política do administrador.
     * Se a política exige apenas um desafio, garante que o usuário tenha os dados apropriados definidos para pelo menos um dos desafios habilitados pela política de administrador.
       * Se o desafio do usuário não estiver configurado, o usuário deverá entrar em contato com o administrador para redefinir sua senha.
     * Se a política exige dois desafios, fica garantido que o usuário tem os dados apropriados definidos em pelo menos dois dos desafios habilitados pela política do administrador.
       * Se o desafio do usuário não estiver configurado, o usuário deverá entrar em contato com o administrador para redefinir sua senha.
   * Verifica se a senha do usuário é gerenciada no local (federado ou sincronizada com hash de senha).
     * Se o write-back estiver implantado e a senha do usuário for gerenciada localmente, o usuário poderá continuar a autenticação e a redefinição de sua senha.
     * Se o write-back não estiver implantado e a senha do usuário for gerenciada localmente, o usuário deverá contatar o administrador para redefinir sua senha.
4. Se for determinado que o usuário pode redefinir sua senha com êxito, ele será orientado pelo processo de redefinição.

## <a name="authentication-methods"></a>Métodos de autenticação

Se a SSPR estiver habilitada, você deve selecionar pelo menos uma das seguintes opções para os métodos de autenticação. Às vezes, essas opções referidas como "portões." É recomendável que você escolha pelo menos dois métodos de autenticação para que seus usuários tenham mais flexibilidade.

* Email
* Telefone celular
* Telefone comercial
* Perguntas de segurança

![Autenticação][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-the-authentication-data"></a>Quais campos são usados no diretório para os dados de autenticação?

* **Telefone comercial**: corresponde ao telefone comercial.
    * Os usuários não conseguem configurar esse campo. Ele deve ser definido por um administrador.
* **Telefone celular**: corresponde ao telefone de autenticação (não visível publicamente) ou ao telefone celular (visível publicamente).
    * O serviço procura o telefone de autenticação primeiro e, em seguida, retorna ao celular se o telefone de autenticação não estiver presente.
* **Endereço de email alternativo**: corresponde ao email de autenticação (não visível publicamente) ou ao email alternativo.
    * O serviço procura o email de autenticação primeiro e, em seguida, recorre ao email alternativo.

Por padrão, somente os atributos de nuvem telefone comercial e telefone celular são sincronizados com o diretório de nuvem do seu diretório local para os dados de autenticação.

Os usuários somente poderão redefinir suas senhas se tiverem dados presentes nos métodos de autenticação habilitados e definidos como obrigatórios pelo administrador.

Se os usuários não quiserem que seu número de telefone celular seja visível no diretório, mas eles ainda querem usá-lo para redefinição de senha, os administradores não devem preenchê-lo no diretório. Os usuários devem então preencher o atributo **Telefone de Autenticação** através do [portal de registro de redefinição de senha](http://aka.ms/ssprsetup). Os administradores poderão ver essas informações no perfil do usuário, mas elas não serão publicadas em nenhum outro lugar.

### <a name="the-number-of-authentication-methods-required"></a>Quantidade de necessária de métodos de autenticação

Essa opção determina o número mínimo de métodos de autenticação disponíveis ou os portões que um usuário deve passar para redefinir ou desbloquear sua senha. Pode ser definido para um ou dois.

Os usuários podem optar por fornecer mais métodos de autenticação se o administrador permitir esse método de autenticação.

Se um usuário não tiver os métodos mínimos necessários registrados, ele verá uma página de erro que o direcionará para solicitar a um administrador para redefinir sua senha.

#### <a name="change-authentication-methods"></a>Alterar métodos de autenticação

Se você iniciar com uma política que tenha apenas um método de autenticação requerido para reiniciar ou desbloquear registrado e você alterar esse número para dois métodos, o que acontece?

| Número de métodos registrados | Número de métodos necessários | Result |
| :---: | :---: | :---: |
| 1 ou mais | 1 | **Capaz** de redefinir ou desbloquear |
| 1 | 2 | **Incapaz** de redefinir ou desbloquear |
| 2 ou mais | 2 | **Capaz** de redefinir ou desbloquear |

Se você alterar os tipos de métodos de autenticação que um usuário pode usar, você poderá inadvertidamente impedir que os usuários sejam capazes de usar SSPR se eles não tiverem a quantidade mínima de dados disponíveis.

Exemplo: 
1. A política original é configurada com dois métodos de autenticação necessários. Somente o número de telefone comercial e as questões de segurança são utilizados. 
2. O administrador altera a política para não usar perguntas de segurança, mas permite o uso de telefone celular e um email alternativo.
3. Os usuários sem os campos de celular e de email alternativo populados não podem redefinir suas senhas.

### <a name="how-secure-are-my-security-questions"></a>Qual o nível de segurança de minhas perguntas de segurança?

Se você usar perguntas de segurança, é recomendável usá-las em conjunto com outro método. As perguntas de segurança podem ser menos seguras do que outros métodos porque algumas pessoas podem conhecer as respostas às perguntas de outros usuários.

> [!NOTE] 
> As perguntas de segurança são armazenadas de forma privada e protegida em um objeto de usuário no diretório e somente podem ser respondidas pelos usuários durante o registro. Não há nenhuma maneira de um administrador ler ou modificar as perguntas ou respostas de um usuário.
>

### <a name="security-question-localization"></a>Localização das perguntas de segurança

Todas as perguntas predefinidas a seguir são localizadas no conjunto completo de idiomas do Office 365 e são baseadas na localidade do navegador do usuário:

* Em qual cidade você conheceu seu primeiro cônjuge/parceiro?
* Em qual cidade seus pais se conheceram?
* Em qual cidade seu irmão mais próximo mora?
* Em qual cidade seu pai nasceu?
* Em qual cidade você teve seu primeiro emprego?
* Em qual cidade sua mãe nasceu?
* Em qual cidade você estava no ano de 2000?
* Qual era o sobrenome de seu professor favorito no ensino médio?
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

As perguntas de segurança personalizadas não são localizadas para localidades diferentes. Todas as perguntas personalizadas são exibidas no mesmo idioma em que são inseridas na interface do usuário administrativo, mesmo se a localidade do navegador do usuário for diferente. Se precisar de perguntas localizadas, você deverá usar as perguntas predefinidas.

O tamanho máximo de uma pergunta de segurança personalizada é de 200 caracteres.

### <a name="security-question-requirements"></a>Requisitos das perguntas de segurança

* O limite mínimo para a resposta é de três caracteres.
* O limite máximo para a resposta é de 40 caracteres.
* Os usuários não podem responder à mesma pergunta mais de uma vez.
* Os usuários não podem fornecer a mesma resposta a mais de uma pergunta.
* Qualquer conjunto de caracteres pode ser usado para definir as perguntas e as respostas, incluindo caracteres Unicode.
* O número de perguntas definidas deve ser maior ou igual ao número de perguntas que foram necessárias para se registrar.

## <a name="registration"></a>Registro

### <a name="require-users-to-register-when-they-sign-in"></a>Exigir que os usuários se cadastram ao entrarem

Para habilitar essa opção, um usuário habilitado para redefinição de senha deve completar o registro de redefinição de senha se eles entrarem em aplicativos usando o Azure Active Directory. Incluem os itens a seguir:

* Office 365
* Portal do Azure
* Painel de acesso
* Aplicativos federados
* Aplicativos personalizados que usam o Azure Active Directory

Quando a necessidade de registro estiver desabilitada, os usuários ainda poderão registrar manualmente suas informações de contato. Os usuários podem visitar [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) ou selecionar o link **Registrar para redefinição de senha** na guias **Perfil** no Painel de Acesso.

> [!NOTE]
> Os usuários podem ignorar o portal de registro de redefinição de senha selecionando **cancelar** ou fechando a janela. Mas ele serão solicitados a registrar cada vez que entrar até que eles concluir seu registro.
>
> Isso não interromperá conexão do usuário se ele já estiver conectado.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Definir o número de dias antes que os usuários precisem reconfirmar suas informações de autenticação

Essa opção determina o período entre a configuração e a reconfirmação das informações de autenticação e somente estará disponível se você habilitar a opção **Exigir que os usuários se registrem ao entrar**.

Os valores válidos são de 0 a 730 dias, com "0", o que significa que os usuários nunca são solicitados a reconfirmar suas informações de autenticação.

## <a name="notifications"></a>Notificações

### <a name="notify-users-on-password-resets"></a>Notificar os usuários sobre as redefinições de senha

Se essa opção for definida para **Sim**, então, o usuário que está redefinindo sua senha recebe um email informando que sua senha foi alterada. O email é enviado por meio do portal de SSPR em seus endereços de email primários e alternativos que estão no arquivo no Azure Active Directory. Ninguém mais é notificado desse evento de redefinição.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Notificar todos os administradores quando outros administradores redefinirem suas senhas

Se essa opção for definida para **Sim**, então, *todos os administradores* receberão um email em seu endereço de email primário registrado no Azure Active Directory. O email notifica que outro administrador alterou sua senha utilizando a SSPR.

Exemplo: Há quatro administradores em um ambiente. O administrador A redefine sua senha usando a SSPR. Os administradores B, C e D recebem um email avisando-os da redefinição da senha.

## <a name="on-premises-integration"></a>Integração local

Se você instalar, configurar e habilitar o Azure Active Directory Connect, você terá as seguintes opções adicionais para integrações no local. Se essas opções estiverem esmaecidas, o write-back não foi configurado corretamente. Para obter mais informações, consulte [Configurar write-back de senha](active-directory-passwords-writeback.md#configuring-password-writeback).

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Write-back de senhas para o diretório local

Este controle determina se o write-back de senha está habilitado para este diretório. Se o write-back estiver ativado, ele indica o status do serviço de write-back local. Isso será útil se você desejar desabilitar o write-back de senha temporariamente sem precisar reconfigurar o Azure AD Connect.

* Se a opção estiver definida para **Sim**, o write-back será habilitado e os usuários federados e sincronizados com hash de senha poderão redefinir suas senhas.
* Se a opção estiver definida para **Não**, o write-back será desabilitado e os usuários federados e sincronizados com hash de senha não poderão redefinir suas senhas.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Permitir aos usuários desbloquear contas sem redefinir a senha

Esse controle designa se os usuários que visitam o portal de redefinição de senha devem ter a opção de desbloquear suas contas no Active Directory no local sem ter que redefinir sua senha. Por padrão, o Azure Active Directory desbloqueia contas quando ele executa uma redefinição de senha. Use essa configuração para separar essas duas operações. 

* Se for definida para **Sim**, então, os usuários terão a opção de redefinir sua senha e desbloquear a conta, ou desbloquear sua conta sem precisar redefinir a senha.
* Se for definida para **Não**, os usuários só poderão executar uma operação combinada de redefinição de senha e de desbloqueio de conta.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Como a redefinição de senha funciona para usuários B2B?
A reinicialização e a mudança de senha são totalmente suportadas em todas as configurações B2B (entre empresas). A reinicialização da senha do usuário B2B tem suporte nos três casos a seguir:

   * **Usuários de uma organização de parceiros com um locatário existente do Azure Active Directory**: se a organização com a qual você está fazendo uma parceria tiver um locatário existente do Azure Active Directory, *respeitaremos todas as políticas de redefinição de senha habilitadas no locatário*. Para que a reinicialização da senha funcione, a organização parceira precisa ter certeza de que a SSPR do Azure Active Directory está habilitada. Não há nenhum custo adicional para os clientes do Office 365, e ela pode ser habilitada seguindo as etapas em nosso guia [Introdução ao gerenciamento de senha](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords).
   * **Usuários que se inscreveram usando a inscrição de autoatendimento**: se a organização com a qual você está fazendo parceria usou o recurso [inscrição para autoatendimento](active-directory-self-service-signup.md) para entrar em um locatário, permitiremos que redefinam a senha com o email com a qual se registraram.
   * **Usuários B2B**: os novos usuários B2B criados com as novas [funcionalidades do Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md) também poderão redefinir suas senhas com o email com o qual se registraram durante o processo de convite.

Para testar esse cenário, acesse http://passwordreset.microsoftonline.com com um desses usuários parceiros. Se eles possuem um email alternativo ou um email de autenticação definido, a redefinição de senha funcionará como esperado.

> [!NOTE]
> As contas da Microsoft que receberam o acesso de convidado para seu locatário do Azure Active Directory, como as de Hotmail.com, Outlook.com ou outros endereços de email pessoal, não podem usar a SSPR do 
Azure Active Directory. Será necessário redefinir sua senha usando as informações encontradas no artigo [Quando não é possível entrar com sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir fornecem informações adicionais sobre a redefinição de senha através do Azure Active Directory:

* [Como concluir uma implementação do SSPR com êxito?](active-directory-passwords-best-practices.md)
* [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md)
* [Registro de redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md)
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
