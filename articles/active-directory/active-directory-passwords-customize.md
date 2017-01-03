---
title: 'Personalizar: Gerenciamento de Senhas do Azure AD | Microsoft Docs'
description: "Como personalizar a aparência, o comportamento e as notificações do gerenciamento de senhas no Azure AD para atender às suas necessidades."
services: active-directory
documentationcenter: 
author: asteen
manager: femila
editor: curtand
ms.assetid: 2cddd150-8747-447a-a7cf-1d7d5775c0b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: c02f5a2b0ef127805aed0f8b8b5ec8ccac1f879f


---
# <a name="customizing-password-management-to-fit-your-organizations-needs"></a>Personalização do gerenciamento de senhas para atender às necessidades da sua organização
> [!IMPORTANT]
> **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md).
> 
> 

Para dar aos usuários a melhor experiência possível, recomendamos que você explore e brinque com todas as opções de configuração de gerenciamento de senhas disponíveis para você. Na verdade, você pode começar a explorar isso imediatamente acessando a guia de configuração da **extensão do Active Directory** no [Portal clássico do Azure](https://manage.windowsazure.com). Este tópico orienta você por todas as personalizações de Gerenciamento de Senhas diferentes que é possível fazer como administrador na guia **Configurar** de seu diretório no [Portal clássico do Azure](https://manage.windowsazure.com), incluindo:

| Tópico |  |
| --- | --- |
| Como habilitar ou desabilitar a redefinição de senha? |[Configuração: usuários habilitados para a redefinição de senha](#users-enabled-for-password-reset) |
| Como faço o escopo da redefinição de senha para um conjunto específico de usuários? |[Restringir a redefinição de senha a usuários específicos](#restrict-access-to-password-reset) |
| Como posso alterar os métodos de autenticação com suporte? |[Configuração: métodos de autenticação disponíveis para os usuários](#authentication-methods-available-to-users) |
| Como posso alterar a quantidade necessária de métodos de autenticação? |[Configuração: quantidade necessária de métodos de autenticação](#number-of-authentication-methods-required) |
| Como faço para definir as perguntas de segurança personalizadas? |[Configuração: perguntas de segurança personalizadas](#custom-security-questions) |
| Como posso definir as perguntas de segurança localizadas pré-configuradas? |[Configuração: perguntas de segurança baseadas em conhecimento](#knowledge-based-security-questions) |
| Como posso alterar quantas perguntas de segurança são necessárias? |[Configuração: número de perguntas de segurança para registro ou redefinição](#number-of-questions-required-to-register) |
| Como posso forçar meus usuários a fazer o registro durante a conexão? |[Imposição de distribuição baseada em registro de redefinição de senha](#require-users-to-register-when-signing-in) |
| Como posso forçar meus usuários a reconfirmar seus registros periodicamente? |[Configuração: número de dias antes que os usuários tenham que reconfirmar seus dados de autenticação](#number-of-days-before-users-must-confirm-their-contact-data) |
| Como posso personalizar como um usuário entra em contato com um administrador? |[Configuração: personalizar o link “entre em contato com o seu administrador”](#customize-the-contact-your-administrator-link) |
| Como posso permitir aos usuários desbloquear contas do AD sem redefinir a senha? |[Configuração: permitir que os usuários desbloqueiem suas contas do AD sem redefinir a senha](#allow-users-to-unlock-accounts-without-resetting-their-password) |
| Como habilitar notificações de redefinição de senha para os usuários? |[Configuração: notificar os usuários quando suas senhas forem redefinidas](#notify-users-and-admins-when-their-own-password-has-been-reset) |
| Como habilitar notificações de redefinição de senha para administradores? |[Configuração: notificar outros administradores quando um administrador redefinir suas senhas](#notify-admins-when-other-admins-reset-their-own-passwords) |
| Como posso personalizar a aparência da redefinição de senha? |[Configuração: nome, identidade visual e logotipo da empresa ](#password-management-look-and-feel) |

## <a name="password-management-look-and-feel"></a>Aparência do gerenciamento de senha
A tabela a seguir descreve como cada controle afeta a experiência dos usuários que se registram para a redefinição de senha e que redefinem as suas senhas.  Você pode configurar essas opções na seção **Propriedades do Diretório** da guia **Configurar** do seu diretório no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Controle de Políticas</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Descrição</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Afeta?</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="directory-name">
                  <p>Nome do diretório</p>
                </div>
              </td>
              <td>
                <p>Determina qual nome organizacional os usuários ou administradores veem nas comunicações de email com a redefinição de senha.</p>
              </td>
              <td>
                <p>
                  <strong>Emails "Entre em contato com o seu administrador":</strong>
                </p>
                <ul>
                  <li class="unordered">
Determina o nome amigável do endereço do remetente, por exemplo, "Microsoft em nome de <strong>Wingtip Toys</strong>"<br><br></li>
                  <li class="unordered">
Determina o nome de assunto do email, por exemplo, "Código de verificação do email da conta da <strong>Wingtip Toys</strong>"<br><br></li>
                </ul>
                <p>
                  <strong>Emails de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Determina o nome amigável do endereço do remetente, por exemplo, "Microsoft em nome de <strong>Wingtip Toys</strong>"<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="sign-in-and-access-panel-page-appearance">
                  <p>Entrar e acessar a aparência da página de painel</p>
                </div>
              </td>
              <td>
                <p>Determina se os usuários que acessam a página de redefinição de senha veem o logotipo da Microsoft ou o seu próprio logotipo personalizado.  Esse item de configuração também adiciona a sua identidade visual ao painel de acesso e à página de entrada.</p>
                <p>

                </p>
                <p>Você pode aprender mais sobre o recurso de e de personalização e de identidade visual de locatário em <a href="https://technet.microsoft.com/library/dn532270.aspx">Adicionar identidade visual à sua página de entrada e ao painel de acesso</a>.</p>
              </td>
              <td>
                <p>
                  <strong>Portal de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Determina se o seu logotipo é exibido ou não na parte superior do portal de redefinição de senha em vez do logotipo da Microsoft.<br><br></li>
                  <li class="unordered">
                    <strong>Observação:</strong> talvez você não veja o seu logotipo na primeira página do portal de redefinição de senha se acessar a página de redefinição de senha diretamente.  Quando um usuário inserir seu ID de usuário e clicar em Avançar, o seu logotipo será exibido.  Você pode forçar o aparecimento do seu logotipo no carregamento da página passando o parâmetro whr para a página de redefinição de senha, da seguinte forma: <a href="https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com">https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com</a><br><br></li>
                </ul>
                <p>
                  <strong>Emails "Entre em contato com o seu administrador":</strong>
                </p>
                <ul>
                  <li class="unordered">
Determina se o seu logotipo é exibido ou não na parte inferior dos emails enviados aos administradores quando os usuários optarem por contatá-lo clicando no link "entre em contato com o seu administrador" na interface do usuário de redefinição de senha.<br><br></li>
                </ul>
                <p>
                  <strong>Emails de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Determinam se o seu logotipo é exibido ou não na parte inferior dos emails enviados aos usuários quando eles redefinirem as suas senhas.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## <a name="password-management-behavior"></a>Comportamento de gerenciamento de senha
A tabela a seguir descreve como cada controle afeta a experiência dos usuários que se registram para a redefinição de senha e que redefinem as suas senhas.  Você pode configurar essas opções na seção **Política de redefinição de senha do usuário** da guia **Configurar** do seu diretório no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).

> [!NOTE]
> A conta de administrador que você está usando deve ter uma licença AAD Premium atribuída para ver esses controles de política.<br><br>Eles se aplicam somente aos usuários finais que estão redefinindo as suas senhas, não aos administradores.  **Os administradores têm uma política padrão de email alternativo e/ou telefone celular que é definida para eles pela Microsoft e não pode ser alterada.**
> 
> 

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Controle de Políticas</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Descrição</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Afeta?</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="users-enabled-for-password-reset">
                  <p>Usuários habilitados para a redefinição de senha</p>
                </div>
              </td>
              <td>
                <p>Determina se a redefinição de senha está habilitada para os usuários desse diretório. </p>
              </td>
              <td>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
Se definido como não, nenhum usuário pode registrar seus próprios dados de desafio.<br><br></li>
                  <li class="unordered">
Se definido como sim, qualquer usuário final no diretório pode registrar dados de desafio acessando o portal de registro em <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>.<br><br></li>
                  <li class="unordered">
                    <strong>Observação:</strong> os usuários devem ter uma licença Azure AD Premium ou básica atribuída antes de poderem se registrar para a redefinição de senha.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Se definido como não, os usuários veem uma mensagem dizendo que eles devem contatar o administrador para redefinir as suas senhas.<br><br></li>
                  <li class="unordered">
Se definido como sim, os usuários serão capazes de redefinir as suas senhas automaticamente acessando <a href="http://passwordreset.microsoftonline.com">http://passwordreset.microsoftonline.com</a> ou clicando no link <strong>não é possível acessar sua conta</strong> em qualquer página de entrada com ID organizacional.<br><br></li>
                  <li class="unordered">
                    <strong>Observação:</strong> os usuários devem ter uma licença Azure AD Premium ou básica atribuída antes de poderem redefinir as suas senhas.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="restrict-access-to-password-reset">
                  <p>Restringir o acesso à redefinição de senha</p>
                </div>
              </td>
              <td>
                <p>Determina se somente um determinado grupo de usuários tem permissão para usar a redefinição de senha. (Visível somente se a opção <strong>usuários habilitados para redefinição de senha</strong> estiver definida como <strong>sim</strong>.)</p>
              </td>
              <td>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
Essa configuração não afeta o acesso dos usuários ao portal de registro de redefinição de senha. Se <strong>Usuários habilitados para a redefinição de senha</strong> estiver definida como <strong>sim</strong>, todos os usuários finais em seu diretório poderão se registrar para a redefinição de senha em <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>.
                  </li>
                </ul>
                <p>
                  <strong>Portal de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Se definido como não, todos os usuários finais em seu diretório podem redefinir as suas senhas.<br><br></li>
                  <li class="unordered">
Se definido como sim, somente os usuários finais especificados no controle <strong>grupo que pode executar a redefinição de senha</strong> podem redefinir as suas senhas.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="group-that-can-perform-password-reset">
                  <p>Grupo que pode executar a redefinição de senha</p>
                </div>
              </td>
              <td>
                <p>Determina qual grupo de usuários tem permissão para usar a redefinição de senha. </p>
                <p>

                </p>
                <p>(Visível somente se <strong>restringir o acesso à redefinição de senha</strong> estiver definida como <strong>sim</strong>.)</p>
              </td>
              <td>
                <p>
                  <strong>Observação:</strong>
                </p>
                <ul>
                  <li class="unordered">
Se nenhum grupo for especificado e você clicar em <strong>Salvar</strong>, um grupo vazio chamado <strong>SSPRSecurityGroupUsers</strong> será criado para você.<br><br></li>
                  <li class="unordered">
Se você gostaria de especificar seu próprio grupo, pode fornecer seu próprio nome de exibição.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
Essa configuração não afeta o acesso dos usuários ao portal de registro de redefinição de senha. Se <strong>Usuários habilitados para a redefinição de senha</strong> estiver definida como <strong>sim</strong>, todos os usuários finais em seu diretório poderão se registrar para a redefinição de senha em <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>.
                  </li>
                </ul>
                <p>
                  <strong>Portal de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Se <strong>restringir o acesso à redefinição de senha</strong> estiver definida como <strong>sim</strong>, somente os usuários finais nesse grupo poderão redefinir as suas senhas.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="authentication-methods-available-to-users">
                  <p>Métodos de autenticação disponíveis aos usuários</p>
                </div>
              </td>
              <td>
                <p>Determina para quais desafios um usuário tem a permissão de usar a redefinição de senha.</p>
                <p>

                </p>
                <p>(Visível somente se a opção <strong>usuários habilitados para redefinição de senha</strong> estiver definida como <strong>sim</strong>.)</p>
              </td>
              <td>
                <p>
                  <strong>Observação:</strong>
                </p>
                <ul>
                  <li class="unordered">
Pelo menos uma opção deve ser selecionada.<br><br></li>
                  <li class="unordered">
Recomendamos fortemente que habilite pelo menos duas opções para oferecer aos usuários maior flexibilidade quando redefinirem as suas senhas.<br><br></li>
                  <li class="unordered">
Se você estiver usando perguntas de segurança, recomendamos que as use em conjunto com outro método de autenticação, já que perguntas de segurança podem ser menos seguras do que os métodos de redefinição de senha por telefone ou baseados em email.<br><br></li>
                </ul>
                <p>
                  <strong>Quais campos de diretório são usados?</strong>
                </p>
                <ul>
                  <li class="unordered">
Telefone comercial corresponde ao atributo <strong>Telefone Comercial</strong> em um objeto de usuário no diretório.<br><br></li>
                  <li class="unordered">
Telefone celular corresponde ao atributo <strong>Telefone Celular de Autenticação</strong> (que não esteja visível publicamente) ou ao atributo <strong>Telefone Celular</strong> (que é visível publicamente) em um objeto de usuário no diretório.  O serviço primeiro verifica os dados de <strong>Telefone de Autenticação</strong> e, se não houver um, volta para o atributo <strong>Telefone Celular</strong>.<br><br></li>
                  <li class="unordered">
Endereço de email alternativo corresponde ao atributo <strong>Email de Autenticação</strong> (que não esteja visível publicamente) ou ao atributo <strong>Email Alternativo</strong> em um objeto de usuário no diretório.  O serviço primeiro verifica os dados de <strong>Email de Autenticação</strong> e, se não houver um, volta para o atributo <strong>Email Alternativo</strong>.<br><br></li>
                  <li class="unordered">
As Perguntas de Segurança são armazenadas de forma privada e protegida em um objeto de usuário no diretório e somente podem ser respondidas pelos usuários durante o registro.  Para fins de segurança, não existe atualmente uma maneira de um administrador editar ou visualizar essas respostas.<br><br></li>
                  <li class="unordered">
                    <strong>Observação: </strong>por padrão, somente os atributos de nuvem Office Phone e Mobile Phone são sincronizados com o diretório de nuvem do seu diretório local.  Para saber mais sobre quais atributos locais são sincronizados com a nuvem, confira <a href="https://msdn.microsoft.com/library/azure/dn764938.aspx">Atributos sincronizados com o Azure AD.</a><br><br></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
Define quais métodos de autenticação são exibidos quando os usuários estão se registrando.  Se você não habilitar um método de autenticação específico, os usuários não poderão fazer o registro para esse método de autenticação.<br><br></li>
                  <li class="unordered">
                    <strong>Observação: </strong>os usuários atualmente não podem registrar seus próprios números de telefone comercial; esse método de autenticação deve ser definido pelo administrador.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Determina quais métodos de autenticação um usuário pode usar como desafios para uma determinada etapa de verificação.  Por exemplo, se um usuário tiver dados tanto no campo <strong>Telefone Comercial</strong> quanto no <strong>Telefone de Autenticação</strong> no Azure Active Directory, ele pode usar qualquer um desses métodos de autenticação para recuperar sua senha.<br><br></li>
                  <li class="unordered">
                    <strong>Observação: </strong>os usuários somente serão capazes de redefinir as senhas se tiverem dados presentes para os métodos de autenticação que você habilitou como administrador.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-authentication-methods-required">
                  <p>Quantidade necessária de métodos de autenticação</p>
                </div>
              </td>
              <td>
                <p>Determina a quantidade mínima de métodos de autenticação disponíveis pelos quais um usuário deve passar para redefinir a senha.</p>
                <p>

                </p>
                <p>(Visível somente se a opção <strong>usuários habilitados para redefinição de senha</strong> estiver definida como <strong>sim</strong>.)</p>
              </td>
              <td>
                <p>
                  <strong>Observação:</strong>
                </p>
                <ul>
                  <li class="unordered">
A quantidade pode ser definida entre um e dois métodos de autenticação necessários.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
Determina a quantidade mínima de métodos de autenticação nos quais um usuário deve se registrar antes de poder concluir a experiência de registro.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Afeta o número de etapas de verificação pelas quais um usuário deve passar antes de poder redefinir uma senha.  Uma etapa de verificação é definida como um usuário usando informações de autenticação (como uma chamada para o seu telefone comercial ou um email para o seu email alternativo) a fim de verificar a sua conta.<br><br></li>
                  <li class="unordered">
                    <strong>Observação:</strong> se um usuário não tiver a quantidade necessária de informações de autenticação definidas em sua conta para poder redefinir com êxito a senha de acordo com a política que você definiu, ele verá uma página de erro que o orientará a solicitar a um administrador a redefinição da senha.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-questions-required-to-register">
                  <p>Número de perguntas obrigatórias para o registro</p>
                </div>
              </td>
              <td>
                <p>Determina o número mínimo de perguntas a que um usuário deve responder ao se registrar para a opção de perguntas de segurança.</p>
                <p>(Visível apenas se a caixa de seleção <strong>Perguntas de Segurança</strong> estiver habilitada.)</p>
              </td>
              <td>
                <p>
                  <strong>Observação:</strong>
                </p>
                <ul>
                  <li class="unordered">
A quantidade pode ser definida entre três e cinco perguntas obrigatórias para o registro.<br><br></li>
                  <li class="unordered">
O número de perguntas obrigatórias para o registro deve ser maior ou igual ao número de perguntas obrigatórias para a redefinição.<br><br></li>
                  <li class="unordered">
Recomendamos que você defina o número de perguntas obrigatórias para o registro para ser maior do que o número necessário à redefinição para que os usuários tenham mais flexibilidade ao redefinir as senhas.  Isso também é uma configuração mais segura porque iremos selecionar perguntas aleatoriamente do pool de todas as perguntas que eles já tenham registrado para que o usuário responda.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
Determina o número mínimo de perguntas a que um usuário deve responder antes de ser considerado totalmente registrado para a redefinição de senha.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-questions-required-to-reset">
                  <p>Número de perguntas obrigatórias para a redefinição </p>
                </div>
              </td>
              <td>
                <p>Determina o número mínimo de perguntas a que um usuário deve responder ao redefinir uma senha.</p>
                <p>

                </p>
                <p>(Visível apenas se a caixa de seleção <strong>Perguntas de Segurança</strong> estiver habilitada.)</p>
              </td>
              <td>
                <p>
                  <strong>Observação:</strong>
                </p>
                <ul>
                  <li class="unordered">
A quantidade pode ser definida de 3 a 5 perguntas obrigatórias para a redefinição.<br><br></li>
                  <li class="unordered">
O número de perguntas obrigatórias para a redefinição deve ser menor ou igual ao número de perguntas obrigatórias para o registro.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Determina o número mínimo de perguntas a que um usuário deve responder antes que a senha dele possa ser redefinida.<br><br></li>
                  <li class="unordered">
No momento da redefinição de senha, essa quantidade de perguntas será selecionada aleatoriamente da lista total de perguntas registradas do usuário.  Por exemplo, se o usuário tiver cinco perguntas registradas, três dessas cinco perguntas serão selecionadas aleatoriamente quando o usuário quiser redefinir uma senha.  O usuário deve responder a essas perguntas corretamente antes que senha possa ser redefinida.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="knowledge-based-security-questions">
                  <p>Perguntas de segurança com base em conhecimento</p>
                </div>
              </td>
              <td>
                <p>Define as perguntas de segurança pré-configuradas para escolha dos usuários durante o registro para a redefinição de senha e ao redefinir suas senhas.</p>
                <p>

                </p>
                <p>(Visível apenas se a caixa de seleção <strong>Perguntas de Segurança</strong> estiver habilitada.)</p>
              </td>
              <td>
                <p>
                  <strong>Observação:</strong>
                </p>
                <ul>
                  <li class="unordered">
Todas as perguntas baseadas em conhecimento serão localizadas no conjunto completo de idiomas do O365, com base na localidade do navegador do usuário.<br><br></li>
                  <li class="unordered">
É possível definir até 20 perguntas no total (a soma de suas perguntas personalizadas e baseadas em um conhecimento).<br><br></li>
                 <li class="unordered">
O limite mínimo para a resposta é de três caracteres.<br><br></li>
                  <li class="unordered">
O limite máximo para a resposta é de 40 caracteres.<br><br></li>
                  <li class="unordered">
Os usuários não podem responder à mesma pergunta duas vezes.<br><br></li>
                  <li class="unordered">
Os usuários não podem fornecer a mesma resposta a duas perguntas diferentes duas vezes.<br><br></li>
                  <li class="unordered">
Qualquer conjunto de caracteres pode ser usado para definir as respostas (incluindo caracteres Unicode).<br><br></li>
                  <li class="unordered">
O número de perguntas definidas deve ser maior ou igual ao número de perguntas obrigatórias para o registro.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
Determina a quais perguntas um usuário é capaz de responder ao se registrar para a redefinição de senha.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Determina quais perguntas um usuário é capaz de usar para redefinir uma senha.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="custom-security-questions">
                  <p>Perguntas de segurança personalizadas</p>
                </div>
              </td>
              <td>
                <p>Define as perguntas de segurança que os usuários podem escolher ao se registrar para a redefinição de senha e ao redefinir as suas senhas.</p>
                <p>

                </p>
                <p>(Visível apenas se a caixa de seleção <strong>Perguntas de Segurança</strong> estiver habilitada.)</p>
              </td>
              <td>
                <p>
                  <strong>Observação:</strong>
                </p>
                <ul>
                  <li class="unordered">
É possível definir até 20 perguntas no total (a soma de suas perguntas personalizadas e baseadas em um conhecimento).<br><br></li>
                  <li class="unordered">
O limite máximo para a pergunta é de 200 caracteres.<br><br></li>
                  <li class="unordered">
O limite mínimo para a resposta é de três caracteres.<br><br></li>
                  <li class="unordered">
O limite máximo para a resposta é de 40 caracteres.<br><br></li>
                  <li class="unordered">
Os usuários não podem responder à mesma pergunta duas vezes.<br><br></li>
                  <li class="unordered">
Os usuários não podem fornecer a mesma resposta a duas perguntas diferentes duas vezes.<br><br></li>
                  <li class="unordered">
Qualquer conjunto de caracteres pode ser usado para definir as perguntas e as respostas (incluindo caracteres Unicode).<br><br></li>
                  <li class="unordered">
O número de perguntas definidas deve ser maior ou igual ao número de perguntas obrigatórias para o registro.<br><br></li>
                  <li class="unordered">
A definição de perguntas diferentes para localidades diferentes não tem suporte para perguntas personalizadas.  Todas as perguntas personalizadas serão exibidas no idioma inserido na interface de usuário administrativa, mesmo se a localidade do navegador do usuário for diferente.  Se você precisar que essas perguntas estejam localizadas, use as perguntas "baseadas em conhecimento".<br><br></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
Determina a quais perguntas um usuário é capaz de responder ao se registrar para a redefinição de senha.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Determina quais perguntas um usuário é capaz de usar para redefinir uma senha.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="require-users-to-register-when-signing-in">
                  <p>Exigir que os usuários se registrem ao entrar?</p>
                </div>
                <p>

                </p>
              </td>
              <td>
                <p>Determina se um usuário precisa registrar dados de contato para a redefinição de senha na próxima vez em que ele ou ela entrar.  
                </p>
                <p>Esse recurso funciona em qualquer página de entrada que use uma conta de trabalho ou escolar.  Essas páginas incluem todos os aplicativos do Office 365, o Portal de Gerenciamento do Azure, o Painel de Acesso e quaisquer aplicativos federados ou personalizados que usam o Azure AD para entrar.
                </p>
                <p>

                </p>
                <p>A imposição de registro será aplicada somente aos usuários que podem redefinir a senha. Portanto, se você tiver usado o recurso "restringir acesso à redefinição de senha" e tiver definido o escopo de redefinição de senha a um grupo específico de usuários, somente os usuários nesse grupo precisarão se registrar para a redefinição de senha ao entrar.</p>
                <p>

                </p>
                <p>(Visível somente se a opção <strong>usuários habilitados para redefinição de senha</strong> estiver definida como <strong>sim</strong>.)</p>
              </td>
              <td>
                <p>

                </p>
                <p>

                </p>
                <p>
                  <strong>Observação:</strong>
                </p>
                <ul>
                  <li class="unordered">
Se você desabilitar esse recurso, pode apontá-los manualmente para <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> a fim de que registrem seus dados de contato.  <br><br></li>
                  <li class="unordered">
Os usuários também podem acessar o portal de registro clicando no link <strong>registrar-se para a redefinição de senha</strong> abaixo da guia de perfil no painel de acesso.<br><br></li>
                  <li class="unordered">
O registro por esse método pode ser ignorado clicando no botão Cancelar ou fechando a janela, mas os usuários serão importunados toda vez que entrarem sem o devido registro.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
Essa configuração não afeta o comportamento do portal de registro em si; em vez disso, ela determina se o portal de registro é mostrado aos usuários quando eles se conectam ao painel de acesso.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-days-before-users-must-confirm-their-contact-data">
                  <p>Número de dias antes que os usuários precisem confirmar seus dados de contato</p>
                </div>
              </td>
              <td>
                <p>Quando a opção <strong>exigir que os usuários se registrem</strong> estiver habilitada, essa configuração determina o período de tempo que pode decorrer antes que um usuário precise reconfirmar seus dados. </p>
                <p>

                </p>
                <p>(Visível somente se a opção <strong>exigir que os usuários se registrem ao se conectar ao painel de acesso</strong> estiver definida como <strong>sim</strong>.)</p>
              </td>
              <td>
                <p>

                </p>
                <p>

                </p>
                <p>
                  <strong>Observação: </strong>
                </p>
                <ul>
                  <li class="unordered">
Valores entre 0 e 730 dias são aceitáveis, sendo que 0 significa que os usuários nunca serão solicitados a confirmar novamente seus dados de contato.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
Essa configuração não afeta o comportamento do portal de registro em si; em vez disso, ela determina se o portal de registro é mostrado aos usuários quando seus dados de contato precisam ser reconfirmados. <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="customize-the-contact-your-administrator-link">
                  <p>Personalizar o link Entre em contato com o seu administrador?</p>
                </div>
              </td>
              <td>
                <p>Controla se o link Entre em contato com o seu administrador (mostrado à esquerda) que aparece no portal de redefinição de senha quando ocorre um erro ou quando um usuário aguarda um tempo excessivo em uma operação apontará para uma URL ou para um endereço de email personalizado.</p>
                <p>

                </p>
                <p>(Visível somente se a opção <strong>usuários habilitados para redefinição de senha</strong> estiver definida como <strong>sim</strong>.)</p>
              </td>
              <td>
                <p>
                  <strong>Observação: </strong>
                </p>
                <ul>
                  <li class="unordered">
Se você habilitar essa configuração, deverá escolher uma URL ou um email personalizado preenchendo o campo <strong>URL ou endereço de email personalizado</strong> logo abaixo dessa configuração.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Se definido como não, os usuários que clicarem no link realçado enviarão uma mensagem para o endereço de email principal de todos os administradores de locatários solicitando a redefinição da senha.  Esse email é enviado seguindo a lógica abaixo:<br><br></li>
                  <li class="unordered">
                    <ul>
                      <li class="unordered">
Se houver administradores de senha, enviar o email para todos os administradores de senha até o máximo de 100 destinatários.<br><br></li>
                      <li class="unordered">
Se não houver administradores de senha, enviar o email para todos os administradores de usuário até o máximo de 100 destinatários.<br><br></li>
                      <li class="unordered">
Se não houver administradores de usuário, enviar o email para todos os administradores globais até o máximo de 100 destinatários.<br><br></li>
                    </ul>
                  </li>
                  <li class="unordered">
Se definido como sim, essa configuração personalizará o comportamento do link realçado acima para apontar para uma URL ou endereço de email personalizado que os usuários podem acessar para obter ajuda com a redefinição de senha.<br><br></li>
                  <li class="unordered">
Se você especificar uma URL, ela será aberta em uma nova guia.<br><br></li>
                  <li class="unordered">
Se você especificar um endereço de email, criaremos um link mailto para ele.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="custom-email-address-or-URL">
                  <p>URL ou endereço de email personalizado</p>
                </div>
              </td>
              <td>
                <p>Controla o endereço de email ou URL para a qual o link <strong>Entre em contato com seu administrador</strong> aponta. </p>
                <p>

                </p>
                <p>(Visível somente se <strong>personalizar o link Entre em contato com o seu administrador</strong> estiver definido como <strong>sim</strong>.)</p>
              </td>
              <td>
                <p>
                  <strong>Observação:</strong>
                </p>
                <ul>
                  <li class="unordered">
Deve ser uma URL ou endereço de email intranet ou extranet válido<br><br></li>
                </ul>
                <p>
                  <strong>Portal de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Altera para onde o link <strong>Entre em contato com o seu administrador</strong> aponta.<br><br></li>
                  <li class="unordered">
Se você fornecer um endereço de email, o link se tornará um link "mailto" para esse endereço de email.<br><br></li>
                  <li class="unordered">
Se você fornecer uma URL, o link se tornará um padrão href apontando para a URL que será aberta em uma nova guia.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="write-back-passwords-to-on-premises-directory">
                  <p>Write-back da senha para o diretório local</p>
                </div>
              </td>
              <td>
                <p>Controla se o write-back de senha está habilitado para esse diretório e, se estiver habilitado, indica o status do serviço local de write-back.</p>
                <p>

                </p>
                <p>Essa configuração é útil se você quiser desabilitar temporariamente o serviço sem configurar novamente o Azure AD Connect.</p>
              </td>
              <td>
                <p>

                </p>
                <p>
                  <strong>Observação:</strong>
                </p>
                <ul>
                  <li class="unordered">
Esse controle somente aparece se você tiver instalado o write-back de senha com o download da versão mais recente do Azure AD Connect e habilitado a opção <strong>Write-back de senha</strong> na tela de seleção <strong>recursos opcionais</strong>.<br><br></li>
                  <li class="unordered">
Se você tiver habilitado o write-back de senha e achar que há um problema de configuração com o serviço, pode ir a essa guia e examinar o rótulo <strong>status do serviço de write-back de senha</strong> para ver se algo está errado.<br><br></li>
                  <li class="unordered">
Os status que podem ser mostrados são:<br><br><ul><li class="unordered"><strong>Configurado</strong> – tudo está funcionando conforme o esperado<br><br></li><li class="unordered"><strong>Não configurado</strong> – você tem o write-back instalado, mas não pode acessar o serviço. Verifique se você não está bloqueando as conexões de saída para 443 e tente instalar novamente o serviço se ainda tiver problemas.<br><br></li></ul></li>
                </ul>
                <p>
                  <strong>Portal de registro:</strong>
                </p>
                <ul>
                  <li class="unordered">
Se o write-back for implantado e configurado e essa opção estiver definida como <strong>não</strong>, o write-back será desabilitado e os usuários federados e sincronizados com hash de senha não poderão se registrar para a redefinição das senhas.<br><br></li>
                  <li class="unordered">
Se a opção estiver definida como <strong>sim</strong>, o write-back estará habilitado e os usuários federados e sincronizados com hash de senha poderão redefinir as suas senhas.<br><br></li>
                </ul>
                <p>
                  <strong>Portal de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Se o write-back for implantado e configurado e essa opção estiver definida como <strong>não</strong>, o write-back será desabilitado e federado os usuários federados e sincronizados com hash de senha não poderão redefinir suas senhas.<br><br></li>
                  <li class="unordered">
Se a opção estiver definida como <strong>sim</strong>, o write-back estará habilitado e os usuários federados e sincronizados com hash de senha poderão redefinir as suas senhas.<br><br></li>
                </ul>
              </td>
            </tr>
             <tr>
              <td>
                <div id="allow-users-to-unlock-accounts-without-resetting-their-password">
                  <p>Permitir aos usuários desbloquear contas sem redefinir a senha</p>
                </div>
              </td>
              <td>

              <p>Determina se os usuários que visitam o portal de redefinição de senha receber a opção de desbloquear suas contas locais do Active Directory sem redefinir a senha. Por padrão, o Azure AD sempre desbloqueia contas ao executar uma redefinição de senha, essa configuração permite que você separe as duas operações.</p>

              <p>Se for definido como "Sim", os usuários terão a opção de redefinir suas senhas e desbloquear a conta, ou desbloquear sem redefinir a senha. </p>

              <p>Se for definido como "não", os usuários só poderão executar uma operação combinada de redefinição de senha e de desbloqueio de conta.</p>

              </td>
              <td>
                <p>
                  <strong>Observação:</strong>
                </p>
                <ul>
                  <li class="unordered">
Para usar esse recurso, você deve instalar a versão de agosto de 2015 ou posterior do Azure Connect AD (v. 1.0.8667.0 ou mais recente).<br><br><a href="http://www.microsoft.com/download/details.aspx?id=47594">Clique aqui para baixar a versão mais recente do Azure AD Connect.</a></li>

                  <li class="unordered">
                    <strong>Observação:</strong> para testar esse recurso, você precisa habilitar o write-back de senha e usar uma conta que tenha origem local (como um usuário federado ou sincronizado com senha) e ter uma conta bloqueada.  Os usuários que não vêm do local e não têm uma conta bloqueada não verão a opção de desbloquear suas contas.</li>
                </ul>
                <p>
                  <strong>Portal de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Depois de habilitar essa opção, quando um usuário com uma conta local bloqueada chegar ao portal de redefinição de senha, ele ou ela terá a opção de desbloquear suas contas sem redefinir sua senha.<br><br>Observe que se você estiver usando o write-back de senha, as contas já serão automaticamente desbloqueadas quando a senha for redefinida, e que essa opção simplesmente separa as operações.<br><br>Essa é uma opção útil para habilitar se você perceber que muitos chamadas de assistência técnica são geradas por solicitações de desbloqueio de conta.</li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## <a name="password-management-notifications"></a>Notificações de gerenciamento de senha
A tabela a seguir descreve como cada controle afeta a experiência dos usuários e dos administradores que recebem notificações de redefinição de senha.  Você pode configurar essas opções na seção **Notificações** da guia **Configurar** do seu diretório no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Controle de Políticas</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Descrição</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Afeta?</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="notify-admins-when-other-admins-reset-their-own-passwords">
                  <p>Notificar os administradores quando outros administradores redefinirem suas próprias senhas</p>
                </div>
              </td>
              <td>
                <p>Determina se todos os administradores globais serão notificados por email para seu endereço de email principal quando outro administrador de qualquer tipo redefinir a sua própria senha.</p>
              </td>
              <td>
                <p>
                  <strong>Portal de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Se definido como não, então não nenhuma notificação será enviada.<br><br></li>
                  <li class="unordered">
Se definido como sim, todos os outros administradores globais serão notificados quando qualquer outro administrador redefinir a sua própria senha.<br><br></li>
                  <li class="unordered">
Essa notificação é enviada por email para os endereços de email principal de todos os outros administradores globais na organização.<br><br></li>
                </ul>
                <p>
                  <strong>Exemplo:</strong>
                </p>
                <ul>
                  <li class="unordered">
Se essa opção foi habilitada quando o administrador A redefinir a sua senha e houver outros 3 administradores no locatário, B, C e D, os administradores B, C e D devem receber uma mensagem indicando que o administrador A redefiniu a senha.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="notify-users-and-admins-when-their-own-password-has-been-reset">
                  <p>Notifica os usuários e administradores quando suas próprias senhas tiverem sido redefinidas</p>
                </div>
              </td>
              <td>
                <p>Determina se os usuários finais ou administradores que redefinirem suas próprias senhas receberão uma notificação por email de que a senha foi redefinida.</p>
              </td>
              <td>
                <p>
                  <strong>Portal de redefinição de senha:</strong>
                </p>
                <ul>
                  <li class="unordered">
Se definido como não, então não nenhuma notificação será enviada.<br><br></li>
                  <li class="unordered">
Se definido como sim, sempre que um usuário ou administrador redefinir a sua própria senha, ele receberá uma notificação indicando que a senha foi redefinida.<br><br></li>
                  <li class="unordered">
Essa notificação é enviada por email para o nome UPN e para o endereço de email alternativo (ou de autenticação) do usuário que redefinir a senha.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Links para documentação de redefinição de senha
Veja abaixo links para todas as páginas de documentação sobre Redefinição de Senha do AD do Azure:

* **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md).
* [**Como funciona**](active-directory-passwords-how-it-works.md) – saiba mais sobre os seis diferentes componentes do serviço e o que cada um deles faz
* [**Introdução**](active-directory-passwords-getting-started.md) – saiba como permitir que os usuários redefinam e alterem suas senhas na nuvem ou no local
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md) – aprenda a implantar rapidamente e gerenciar com eficiência as senhas em sua organização
* [**Obter percepções**](active-directory-passwords-get-insights.md) – saiba mais sobre nossos recursos integrados de relatórios
* [**Perguntas frequentes**](active-directory-passwords-faq.md) - obtenha respostas para perguntas frequentes
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) – aprenda a solucionar rapidamente os problemas com o serviço
* [**Saiba mais**](active-directory-passwords-learn-more.md) – aprofunde-se nos detalhes técnicos do funcionamento do serviço

[001]: ./media/active-directory-passwords-customize/001.jpg "Image_001.jpg"



<!--HONumber=Dec16_HO5-->


