<properties 
	pageTitle="Obter percepções: relatórios de gerenciamento de senhas do AD do Azure | Microsoft Azure" 
	description="Este artigo descreve como usar os relatórios para obter informações sobre operações de gerenciamento de senhas em sua organização." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="asteen"/>

# Como obter percepções operacionais com relatórios de gerenciamento de senhas
Esta seção descreve como você pode usar relatórios de gerenciamento de senhas do Active Directory do Azure para ver como os usuários estão usando a redefinição e alteração de senhas em sua organização.

- [**Visão geral de relatórios de gerenciamento de senhas**](#overview-of-password-management-reports)
- [**Como exibir relatórios de gerenciamento de senhas**](#how-to-view-password-management-reports)
- [**Exibir atividade de registro de redefinição de senhas em sua organização**](#view-password-reset-registration-activity)
- [**Exibir atividade de redefinição de senha em sua organização**](#view-password-reset-activity)

## Visão geral dos relatórios de gerenciamento de senhas
Após a implantação da redefinição de senhas, uma das próximas etapas mais comuns é ver como ela está sendo usada na organização. Por exemplo, talvez você queira obter uma visão de como os usuários estão s registrando para a redefinição de senha ou quantas redefinições de senha foram feitas nos últimos dias. Aqui estão algumas perguntas comuns que você poderá responder com os relatórios de gerenciamento de senhas que existem nos [Portal de Gerenciamento do Azure](https://manage.windowsazure.com) hoje:

- Quantas pessoas foram registradas para a redefinição de senhas?
- Quem se registrou para a redefinição de senhas?
- Que dados as pessoas estão registrando?
- Quantas pessoas redefiniram suas senhas nos últimos sete dias?
- Quais são os métodos mais comuns que os usuários ou administradores usam para redefinir suas senhas?
- Quais são os problemas comuns que os usuários ou administradores enfrentam ao tentar usar a redefinição de senhas?
- Quais administradores estão redefinindo suas próprias senhas com frequência?
- Há qualquer atividade suspeita acontecendo na redefinição de senhas?


## Como exibir relatórios de gerenciamento de senhas
Para localizar os relatórios de gerenciamento de senhas, siga as etapas abaixo:

1.	Clique na **Extensão do Active Directory** no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2.	Selecione o diretório na lista que aparece no portal.
3.	Clique na guia **Relatórios**.
4.	Procure na seção **Logs de Atividade**.
5.	Selecione o relatório **Atividade de redefinição de senhas** ou **Atividade de registro de redefinição de senhas**.

    ![][001]

## Exibir atividade de registro de redefinição de senhas

O relatório de atividade de registro de redefinição de senhas mostra todos os registros de redefinição de senhas que ocorreram na sua organização. Um registro de redefinição de senhas é exibido no relatório para qualquer usuário que registrou com êxito as informações de autenticação no portal de registro de senha de redefinição de senhas ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)).

- **Intervalo de tempo máximo**: um mês
- **Número máximo de linhas**: ilimitado
- **Baixável**: sim, por meio de arquivo CSV

    ![][002]

### Descrição das colunas do relatório
A lista a seguir explica cada uma das colunas do relatório em detalhes:

- **Usuário** – o usuário que tentou uma operação de registro de redefinição de senha.
- **Função** – a função do usuário no diretório.
- **Data e hora** – a data e a hora da tentativa.
- **Dados Registrados** – os dados de autenticação fornecidos pelo usuário durante o registro de redefinição de senha.

### Descrição dos valores de relatório
A tabela a seguir descreve os diferentes valores permitidos para cada coluna:

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Coluna</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Valores permitidos e seus significados</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Dados Registrados</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>Email Alternativo</strong> – email alternativo ou email de autenticação usado pelo usuário para se autenticar<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Telefone Comercial</strong> – telefone comercial usado pelo usuário para se autenticar<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Celular</strong> – celular ou telefone de autenticação usado pelo usuário para se autenticar<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Perguntas de Segurança</strong> – perguntas de segurança usadas pelo usuário para se autenticar<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Qualquer combinação dos itens acima (por exemplo, Email Alternativo + Celular)</strong> – ocorre quando uma política de duas portas é especificada e mostra os dois métodos usados pelo usuário para autenticação de sua solicitação de redefinição de senha.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Exibir atividade de redefinição de senha

Esse relatório mostra todas as tentativas de redefinição de senha que ocorreram na sua organização.

- **Intervalo de tempo máximo**: um mês
- **Número máximo de linhas**: ilimitado
- **Baixável**: sim, por meio de arquivo CSV

    ![][003]

### Descrição das colunas do relatório
A lista a seguir explica cada uma das colunas do relatório em detalhes:

1. **Usuário** – o usuário que tentou uma operação de redefinição de senha (com base no campo ID de Usuário fornecido quando o usuário redefine uma senha).
2. **Função** – a função do usuário no diretório.
3. **Data e hora** – a data e a hora da tentativa.
4. **Método(s) usado(s)** – os métodos de autenticação de usuário usados para essa operação de redefinição.
5. **Resultado** – resultado final da operação de redefinição de senha.
6. **Detalhes** – os detalhes do motivo pelo qual a redefinição de senha resultou no valor obtido. Também inclui as etapas de atenuação que você pode tomar para resolver um erro inesperado.

### Descrição dos valores de relatório
A tabela a seguir descreve os diferentes valores permitidos para cada coluna:

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Coluna</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Valores permitidos e seus significados</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Métodos usados</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>Email Alternativo</strong> – email alternativo ou email de autenticação usado pelo usuário para se autenticar<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Telefone Comercial</strong> – telefone comercial usado pelo usuário para se autenticar<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Celular</strong> – celular ou telefone de autenticação usado pelo usuário para se autenticar<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Perguntas de Segurança</strong> – perguntas de segurança usadas pelo usuário para se autenticar<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Qualquer combinação dos itens acima (por exemplo, Email Alternativo + Celular)</strong> – ocorre quando uma política de duas portas é especificada e mostra os dois métodos usados pelo usuário para autenticação de sua solicitação de redefinição de senha.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Result</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>Abandonado</strong> – redefinição de senha iniciada pelo usuário mas, em seguida, interrompida pela metade sem ser concluída<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Bloqueado</strong> – a conta do usuário foi impedida de usar a redefinição de senha devido à tentativa de usar a página de redefinição de senha ou uma porta única de redefinição de senha muitas vezes em um período de 24 horas<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Cancelado</strong> – redefinição de senha iniciada pelo usuário, mas o botão Cancelar foi clicado para cancelar a sessão pela metade <br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Administrador Contatado</strong> – o usuário teve um problema durante a sessão que ele não pôde resolver, assim, o usuário clicou no link "Entre em contato com seu administrador" em vez de concluir o fluxo de redefinição de senha<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Falha</strong> – o usuário não pôde redefinir uma senha, provavelmente porque o usuário não foi configurado para usar o recurso (por exemplo, nenhuma licença, informações de autenticação ausentes, senha gerenciada no local, mas o write-back está desativado).<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>Êxito</strong> – a redefinição de senha foi bem-sucedida.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Detalhes</p>
              </td>
              <td>
                <p>Consulte a tabela abaixo.</p>
              </td>
            </tr>
          </tbody></table>

### Valores permitidos para a coluna de detalhes
Abaixo está a lista de tipos de resultado que você pode esperar ao usar o relatório de atividade de redefinição de senha:

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Detalhes</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Tipo de resultado</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Abandonado pelo usuário depois de concluir a opção de verificação de email</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Abandonado pelo usuário depois de concluir a opção de verificação de SMS móvel</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Abandonado pelo usuário depois de concluir a opção de verificação de chamada de voz móvel</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Abandonado pelo usuário depois de concluir a opção de verificação de chamada de voz comercial</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Abandonado pelo usuário depois de concluir a opção de perguntas de segurança</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Abandonado pelo usuário depois de inserir sua ID de usuário</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Abandonado pelo usuário depois de iniciar a opção de verificação de email</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Abandonado pelo usuário depois de iniciar a opção de verificação de SMS móvel</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Abandonado pelo usuário depois de iniciar a opção de verificação de chamada de voz móvel</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Abandonado pelo usuário depois de iniciar a opção de verificação de chamada de voz comercial</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Abandonado pelo usuário depois de iniciar a opção de perguntas de segurança</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Abandonado pelo usuário antes de selecionar uma nova senha</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Abandonado pelo usuário ao selecionar uma nova senha</p>
              </td>
              <td>
                <p>Abandonado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>O usuário inseriu muitos códigos de verificação de email inválidos e está bloqueado para 24 horas</p>
              </td>
              <td>
                <p>Bloqueado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>O usuário inseriu muitos códigos de verificação de SMS inválidos e está bloqueado para 24 horas</p>
              </td>
              <td>
                <p>Bloqueado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>O usuário tentou muitas vezes a verificação de voz por telefone celular e está bloqueado por 24 horas</p>
              </td>
              <td>
                <p>Bloqueado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>O usuário tentou muitas vezes a verificação de voz comercial e está bloqueado por 24 horas</p>
              </td>
              <td>
                <p>Bloqueado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>O usuário tentou responder às perguntas de segurança muitas vezes e está bloqueado por 24 horas</p>
              </td>
              <td>
                <p>Bloqueado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>O usuário tentou verificar um número de telefone muitas vezes e está bloqueado por 24 horas</p>
              </td>
              <td>
                <p>Bloqueado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>O usuário fez o cancelamento antes de passar pelos métodos de autenticação obrigatórios</p>
              </td>
              <td>
                <p>Cancelado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>O usuário fez o cancelamento antes de enviar uma nova senha</p>
              </td>
              <td>
                <p>Cancelado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>O usuário contatou um administrador depois de tentar a opção de verificação de email</p>
              </td>
              <td>
                <p>Administrador contatado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>O usuário contatou um administrador depois de tentar a opção de verificação de SMS móvel</p>
              </td>
              <td>
                <p>Administrador contatado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>O usuário contatou um administrador depois de tentar a opção de verificação de chamada de voz móvel</p>
              </td>
              <td>
                <p>Administrador contatado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>O usuário contatou um administrador depois de tentar a opção de verificação de chamada de voz comercial</p>
              </td>
              <td>
                <p>Administrador contatado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>O usuário contatou um administrador depois de tentar a opção de verificação de pergunta de segurança</p>
              </td>
              <td>
                <p>Administrador contatado</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>A redefinição de senha não está habilitada para este usuário. Habilite a redefinição de senha na guia Configurar para resolver o problema</p>
              </td>
              <td>
                <p>Falha</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>O usuário não tem uma licença. Você pode adicionar uma licença para o usuário para resolver o problema</p>
              </td>
              <td>
                <p>Falha</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>O usuário tentou redefinir em um dispositivo sem cookies habilitados</p>
              </td>
              <td>
                <p>Falha</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>A conta do usuário tem métodos de autenticação insuficiente definidos. Adicione informações de autenticação para resolver esse problema</p>
              </td>
              <td>
                <p>Falha</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>A senha do usuário é gerenciada localmente. Você pode habilitar o Write-back de Senha para resolver o problema</p>
              </td>
              <td>
                <p>Falha</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Não foi possível acessar o serviço de redefinição de senha no local. Verifique o log de eventos de seu computador de sincronização</p>
              </td>
              <td>
                <p>Falha</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Encontramos um problema durante a redefinição de senha local do usuário. Verifique o log de eventos de seu computador de sincronização</p>
              </td>
              <td>
                <p>Falha</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Este usuário não é membro do grupo de usuários de redefinição de senha. Adicione esse usuário ao grupo para resolver o problema.</p>
              </td>
              <td>
                <p>Falha</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>A redefinição de senha foi desabilitada inteiramente para este locatário. Consulte <a href="http://aka.ms/ssprtroubleshoot">http://aka.ms/ssprtroubleshoot</a> para resolver o problema.</p>
              </td>
              <td>
                <p>Falha</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>O usuário redefiniu a senha com êxito</p>
              </td>
              <td>
                <p>Bem-sucedido</p>
              </td>
            </tr>
          </tbody></table>

<br/> <br/> <br/>

**Recursos adicionais**


* [O que é o Gerenciamento de Senhas](active-directory-passwords.md)
* [Como funciona o gerenciamento de senhas](active-directory-passwords-how-it-works.md)
* [Introdução ao gerenciamento de senhas](active-directory-passwords-getting-started.md)
* [Personalizar o gerenciamento de senhas](active-directory-passwords-customize.md)
* [Práticas recomendadas de gerenciamento de senhas](active-directory-passwords-best-practices.md)
* [Perguntas frequentes sobre gerenciamento de senhas](active-directory-passwords-faq.md)
* [Solucionar problemas do Gerenciamento de Senhas](active-directory-passwords-troubleshoot.md)
* [Saiba mais](active-directory-passwords-learn-more.md)
* [Gerenciamento de senhas no MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"
 

<!---HONumber=July15_HO4-->