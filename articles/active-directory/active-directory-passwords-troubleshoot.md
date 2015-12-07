<properties 
	pageTitle="Solução de problemas: Gerenciamento de senha do Azure AD | Microsoft Azure" 
	description="Etapas de solução de problemas comuns para o gerenciamento de senha do Azure AD, incluindo redefinição, alteração, write-back, registro e quais informações devem ser incluídas ao procurar ajuda." 
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
	ms.date="11/16/2015" 
	ms.author="asteen"/>

# Como solucionar problemas de gerenciamento de senha
Se você estiver tendo problemas com o gerenciamento de senhas, estamos aqui para ajudar. A maioria dos problemas que podem ocorrer são resolvidos com algumas etapas de solução de problemas simples que você pode ler abaixo para solucionar problemas na sua implantação:

* [**Informações a serem incluídas quando precisar de ajuda**](#information-to-include-when-you-need-help)
* [**Problemas de configuração de gerenciamento de senhas no Portal de Gerenciamento do Azure**](#troubleshoot-password-reset-configuration-in-the-azure-management-portal)
* [**Problemas com relatórios de gerenciamento de senha no Portal de Gerenciamento do Azure**](#troubleshoot-password-management-reports-in-the-azure-management-portal)
* [**Problemas com o Portal de registro de redefinição de senha**](#troubleshoot-the-password-reset-registration-portal)
* [**Problemas com o Portal de redefinição de senha**](#troubleshoot-the-password-reset-portal)
* [**Problemas com o write-back de senha**](#troubleshoot-password-writeback)
  - [Códigos de erro de log de eventos de write-back de senha](#password-writeback-event-log-error-codes)
  - [Problemas de conectividade de write-back de senha](#troubleshoot-password-writeback-connectivity)

Se você tentou as etapas de solução de problemas abaixo e ainda está com problemas, pode postar uma pergunta nos [Fóruns do Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) ou contatar o suporte; daremos uma olhada no seu problema assim que possível.

## Informações a serem incluídas quando precisar de ajuda

Se você não conseguir solucionar seu problema com as orientações abaixo, pode entrar em contato com nossos engenheiros de suporte. Quando você entrar em contato com eles, é recomendável incluir as seguintes informações:

 - **Descrição geral do erro**: que mensagem de erro exatamente o usuário viu? Se não havia nenhuma mensagem de erro, descreva o comportamento inesperado observado em detalhes.
 - **Página**: que página você estava quando viu o erro? (Inclua a URL.)
 - **Data/ hora/Fuso horário**: qual era a exata data e hora em que você viu o erro? (Inclua o fuso horário.)
 - **Código de suporte**: qual foi o código de suporte gerado quando o usuário viu o erro (para encontrá-lo, reproduza o erro, clique no link do Código de suporte na parte inferior da tela e envie ao engenheiro de suporte a GUID resultante). 
   - Se você estiver em uma página sem um código de suporte na parte inferior, pressione F12 para o SID e o CID e envie esses dois resultados para o engenheiro de suporte.

    ![][001]

 - **ID de usuário**: qual foi a ID do usuário que viu o erro (por exemplo, user@contoso.com)?
 - **Informações sobre o usuário**: o usuário era federado, a senha era sincronizada com hash, somente na nuvem? O usuário tinha uma licença AAD Premium ou AAD básica atribuída?
 - **Log de eventos do aplicativo**: se você estiver usando o write-back de senha e o erro estiver em sua infraestrutura local, compacte uma cópia do log e eventos do aplicativo do servidor do seu servidor Azure AD Connect AD e envie junto com sua solicitação.

A inclusão dessas informações nos ajudará a resolver o problema o mais rápido possível.


## Solucionar problemas de configuração de redefinição de senha no Portal de Gerenciamento do Azure
Se você encontrar um erro ao configurar a redefinição de senha, pode solucioná-lo seguindo as etapas de solução de problemas abaixo:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de erro</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Qual é o erro exibido ao usuário?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solução</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não vejo a seção <strong>Política de redefinição de senha do usuário </strong>seção na guia <strong>Configurar</strong> no portal de gerenciamento do Azure</p>
            </td>
            <td>
              <p>A seção <strong>Política de redefinição de senha do usuário </strong> não está visível na guia <strong>Configurar</strong> no Portal de Gerenciamento do Azure.</p>
            </td>
            <td>
              <p>Isso pode ocorrer se você não tiver uma licença AAD Premium ou AAD básica atribuída ao administrador para realizar essa operação. </p>
              <p>Para corrigir isso, atribua uma licença AAD Premium ou AAD básica para a conta de administrador em questão, navegando até a guia <strong>licenças</strong>, e tente novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não vejo nenhuma das opções de configuração na seção <strong>Política de redefinição de senha do usuário</strong> descritas na documentação.</p>
            </td>
            <td>
              <p>A seção <strong>Política de redefinição de senha do usuário </strong> está visível, mas o único sinalizador que aparece nela é o sinalizador<strong>Usuários habilitados para redefinição de senha</strong>.</p>
            </td>
            <td>
              <p>O restante da interface do usuário aparecerá quando você alternar o sinalizador <strong>Usuários habilitados para redefinição de senha</strong> para <strong>Sim.</strong></p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não vejo uma opção de configuração específica.</p>
            </td>
            <td>
              <p>Por exemplo, não vejo a opção <strong>Número de dias antes que um usuário precise confirmar seus dados de contato</strong> opção quando percorro a seção <strong>Política de redefinição de senha do usuário</strong> (ou outros exemplos do mesmo problema).</p>
            </td>
            <td>
              <p>Muitos elementos da interface do usuário ficam ocultos até que sejam necessários. Tente habilitar todas as opções na página, se quiser vê-las.</p>
              <p>Confira <a href="../active-directory-passwords-customize#password-management-behavior">Personalização da política de redefinição de senha do usuário</a> para saber mais sobre todos os controles que estão disponíveis para você.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não vejo a opção de configuração <strong>Gravar as senhas de volta para o local</strong></p>
            </td>
            <td>
              <p>A opção <strong>Gravar as senhas de volta para o loca</strong> não fica visível na guia <strong>Configurar</strong> no Portal de Gerenciamento do Azure</p>
            </td>
            <td>
              <p>Essa opção só é visível se você tiver baixado o Azure AD Connect e configurado o write-back de senha. Quando tiver feito isso, essa opção é exibida e permite habilitar ou desabilitar o write-back da nuvem.</p>
              <p>Confira <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">Como habilitar/desabilitar write-back de senha</a> para saber mais sobre como fazer isso.</p>
            </td>
          </tr>
        </tbody></table>

## Solucionar problemas de relatórios de gerenciamento de senha no Portal de Gerenciamento do Azure
Se você encontrar um erro ao usar os relatórios de gerenciamento de senha, pode solucioná-lo seguindo as etapas de solução de problemas abaixo:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de erro</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Qual é o erro exibido ao usuário?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solução</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não vejo os relatórios de gerenciamento de senha</p>
            </td>
            <td>
              <p>Os relatórios de <strong>Atividade de redefinição de senha</strong> e de <strong>Atividade de registro de redefinição de senha</strong> não ficam visíveis nos relatórios de <strong>Log de atividades</strong> na guia <strong>Relatórios</strong>.</p>
            </td>
            <td>
              <p>Isso pode ocorrer se você não tiver uma licença AAD Premium ou AAD básica atribuída ao administrador para realizar essa operação. </p>
              <p>Para corrigir isso, atribua uma licença AAD Premium ou AAD básica para a conta de administrador em questão, navegando até a guia <strong>licenças</strong>, e tente novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Os registros do usuário aparecem várias vezes</p>
            </td>
            <td>
              <p>Quando um usuário registra email alternativo, telefone celular e perguntas de segurança, eles aparecem como linhas separadas em vez de uma única linha.</p>
            </td>
            <td>
              <p>Atualmente, quando um usuário se registra, nós não pode presumir que registará tudo o que está na página de registro. Como resultado, atualmente registramos cada dado individual que é registrado como um evento separado.</p>
              <p>Se você quiser agregar esses dados, pode baixar o relatório e abrir os dados como uma tabela dinâmica no Excel para ter maior flexibilidade.</p>
            </td>
          </tr>
        </tbody></table>

## Solucionar problemas do portal de registro de redefinição de senha
Se você encontrar um erro ao registrar um usuário para redefinição de senha, pode solucioná-lo seguindo as etapas de solução de problemas abaixo:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de erro</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Qual é o erro exibido ao usuário?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solução</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>O diretório não está habilitado para redefinição de senha</p>
            </td>
            <td>
              <p>O administrador não habilitou você para usar esse recurso.</p>
            </td>
            <td>
              <p>Alterne o sinalizador <strong>Usuários habilitados para redefinição de senha</strong> para <strong>Sim</strong> e clique em <strong>Salvar</strong> na guia de configuração de diretório do Portal de Gerenciamento do Azure. Você deve ter uma licença Azure AD Premium ou Básica atribuída ao administrador que está realizando essa operação.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>O usuário não tem uma licença AAD Premium ou AAD Básica atribuída</p>
            </td>
            <td>
              <p>O administrador não habilitou você para usar esse recurso.</p>
            </td>
            <td>
              <p>Atribua uma licença do Azure AD Premium Azure AD Básica para o usuário na guia <strong>Licenças</strong> no Portal de Gerenciamento do Azure. Você deve ter uma licença Azure AD Premium ou Básica atribuída ao administrador que está realizando essa operação.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erro ao processar solicitação</p>
            </td>
            <td>
              <p>O usuário vê um erro que diz:</p>
              <p>
                
              </p>
              <p>Erro ao processar solicitação </p>
              <p>durante a tentativa de redefinir uma senha.</p>
            </td>
            <td>
              <p>Isso pode ser causado por vários problemas, mas, em geral, esse erro é causado por uma paralisação do serviço ou problema de configuração que não pode ser resolvido. </p>
              <p>Se você vir esse erro e ele estiver afetando seus negócios, entre em contato com o suporte e iremos ajudá-lo urgentemente. Confira <a href="#information-to-include-when-you-need-help">Informações a serem incluídas quando precisar de Ajuda</a> para ver o que você deve fornecer ao engenheiro de suporte para auxiliá-lo em uma rápida resolução.</p>
            </td>
          </tr>
        </tbody></table>

## Solucionar problemas do portal de redefinição de senha
Se você encontrar um erro ao redefinir uma senha para um usuário, pode solucioná-lo seguindo as etapas de solução de problemas abaixo:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de erro</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Qual é o erro exibido ao usuário?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solução</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>O diretório não está habilitado para redefinição de senha</p>
            </td>
            <td>
              <p>Sua conta não está habilitada para redefinição de senha</p>
              <p>O administrador não configurou sua conta para usar esse serviço. </p>
              <p>
                
              </p>
              <p>Se desejar, podemos pode contatar um administrador na sua organização para redefinir a senha para você.</p>
            </td>
            <td>
              <p>Alterne o sinalizador <strong>Usuários habilitados para redefinição de senha</strong> para <strong>Sim</strong> e clique em <strong>Salvar</strong> na guia de configuração de diretório do Portal de Gerenciamento do Azure. Você deve ter uma licença Azure AD Premium ou Básica atribuída ao administrador que está realizando essa operação.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>O usuário não tem uma licença AAD Premium ou AAD Básica atribuída</p>
            </td>
            <td>
              <p>Embora não possamos redefinir senhas de contas não administrativas automaticamente, podemos entrar em contato com o administrador de sua organização para fazer isso por você.</p>
            </td>
            <td>
              <p>Atribua uma licença do Azure AD Premium Azure AD Básica para o usuário na guia <strong>Licenças</strong> no Portal de Gerenciamento do Azure. Você deve ter uma licença Azure AD Premium ou Básica atribuída ao administrador que está realizando essa operação.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>O diretório está habilitado para redefinição de senha, mas o usuário tem informações de autenticação ausentes ou incorretas</p>
            </td>
            <td>
              <p>Sua conta não está habilitada para redefinição de senha</p>
              <p>O administrador não configurou sua conta para usar esse serviço. </p>
              <p>
                
              </p>
              <p>Se desejar, podemos pode contatar um administrador na sua organização para redefinir a senha para você.</p>
            </td>
            <td>
              <p>Verifique se o usuário preencheu corretamente os dados de contato no arquivo no diretório antes de continuar. Confira <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">Quais dados são usados na redefinição de senha</a> para saber mais sobre como configurar informações de autenticação no diretório para que os usuários não vejam esse erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>O diretório está habilitado para redefinição de senha, mas o usuário tem somente uma parte dos dados de contato no arquivo quando a política está definida para exigir duas etapas de verificação</p>
            </td>
            <td>
              <p>Sua conta não está habilitada para redefinição de senha</p>
              <p>O administrador não configurou sua conta para usar esse serviço. </p>
              <p>
                
              </p>
              <p>Se desejar, podemos pode contatar um administrador na sua organização para redefinir a senha para você.</p>
            </td>
            <td>
              <p>Verifique se o usuário tem pelo menos dois métodos de contato configurados corretamente (por exemplo, telefone celular e telefone comercial) antes de continuar. Confira <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">Quais dados são usados na redefinição de senha</a> para saber mais sobre como configurar informações de autenticação no diretório para que os usuários não vejam esse erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>O diretório está habilitado para redefinição de senha e o usuário está configurado corretamente, mas não pode ser contatado </p>
            </td>
            <td>
              <p>Algo deu errado! Encontramos um erro inesperado ao entrar em contato com você.</p>
            </td>
            <td>
              <p>Isso pode ser o resultado de um erro de serviço temporário ou dados de contato configurados incorretamente que não pudemos detectar corretamente. Se o usuário aguardar 10 segundos, aparecerá um link tente novamente e "entre em contato com seu administrador". Clicar em tentar novamente enviará novamente a chamada, enquanto clicar em "entre em contato com seu administrador" enviará um formulário de email aos administradores de usuário, senha ou globais (nessa ordem) solicitando uma redefinição de senha para ser executada para essa conta de usuário.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>O usuário nunca recebe a chamada telefônica ou redefinição de senha de SMS</p>
            </td>
            <td>
              <p>O usuário clica em “me mande mensagem” ou “telefone para mim”, mas não recebe nada.</p>
            </td>
            <td>
              <p>Isso pode ser o resultado de um número de telefone incorreto no diretório. Verifique se o número de telefone está no formato "+ ccc xxxyyyzzzzXeeee". Para saber mais sobre a formatação de números de telefone para uso com a senha de redefinição, confira <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">Quais dados são usados na redefinição de senha</a>.</p>
              <p>Se você precisar de uma extensão para ser roteada para o usuário em questão, observe que a redefinição de senha não dá suporte a extensões, mesmo se você especificar uma no diretório (elas são removidas antes de a chamada ser despachada). Tente usar um número sem uma extensão ou integrar a extensão ao número de telefone no seu PBX.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>O usuário nunca receber o email de redefinição de senha</p>
            </td>
            <td>
              <p>O usuário clica em "email" e nunca recebe nada.</p>
            </td>
            <td>
              <p>A causa mais comum desse problema é que a mensagem foi rejeitada por um filtro de spam. Verifique o email no spam, na pasta Lixo eletrônico ou nos itens de email excluídos.</p>
              <p>Verifique também se você está procurando a mensagem no email correto...muitas pessoas têm endereços de email muito semelhantes e procurando a mensagem na caixa de entrada errada. Se nenhuma dessas opções funcionar, também é possível que o endereço de email no diretório esteja incorreto; verifique se o endereço de email é o correto e tente novamente. Para saber mais sobre como formatar endereços de email para uso com a senha de redefinição, confira <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">Quais dados são usados na redefinição de senha</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Defini uma política de redefinição de senha, mas quando uma conta de administrador usa a redefinição de senha, a política não é aplicada</p>
            </td>
            <td>
              <p>As contas de administrador que redefinem as senhas veem as mesmas opções habilitadas para redefinição de senha, email e celular, independentemente da política definida na seção <strong>Política de redefinição de senha do usuário</strong> na guia <strong>Configurar</strong>.</p>
            </td>
            <td>
              <p>As opções configuradas na seção <strong>Política de redefinição de senha do usuário</strong> na guia <strong>Configurar</strong> se aplicam somente a usuários finais na sua organização.</p>
              <p>A Microsoft gerencia e controla a política de redefinição de senha de administrador para garantir o nível mais alto de segurança</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>O usuário foi impedido de tentar a redefinição de senha muitas vezes em um mesmo dia</p>
            </td>
            <td>
              <p>O usuário vê um erro dizendo:</p>
              <p>
                
              </p>
              <p>use outra opção.</p>
              <p>Você tentou verificar sua conta muitas vezes na última hora. Por motivos de segurança, você terá que aguardar 24 horas antes de tentar novamente. </p>
              <p>Se desejar, podemos pode contatar um administrador na sua organização para redefinir a senha para você.</p>
            </td>
            <td>
              <p>Implementamos um mecanismo de limitação automático para impedir que os usuários tentem redefinir suas senhas muitas vezes em um curto período de tempo. Isso ocorre quando:</p>
              <ol class="ordered">
                <li>
										O usuário tenta validar um número de telefone cinco vezes em uma hora.<br\><br\></li>
                <li>
										O usuário tenta usar o portão de perguntas de segurança cinco vezes em uma hora.<br\><br\></li>
                <li>
										O usuário tenta redefinir uma senha para a mesma conta de usuário cinco vezes em uma hora.<br\><br\></li>
              </ol>
              <p>Para corrigir isso, instrua o usuário a aguardar 24 horas após a última tentativa para poder redefinir a senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>O usuário vê um erro ao validar o seu número de telefone</p>
            </td>
            <td>
              <p>Ao tentar verificar um telefone a ser usado como método de autenticação, o usuário vê um erro dizendo:</p>
              <p>
                
              </p>
              <p>número de telefone especificado incorreto.</p>
            </td>
            <td>
              <p>Esse erro ocorre quando o número de telefone digitado não coincide com o número de telefone no arquivo.</p>
              <p>Verifique se o usuário está inserindo o número de telefone completo, incluindo o código de área e país, ao tentar usar um método baseado em telefone para a redefinição de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erro ao processar solicitação</p>
            </td>
            <td>
              <p>O usuário vê um erro que diz:</p>
              <p>
                
              </p>
              <p>Erro ao processar solicitação </p>
              <p>durante a tentativa de redefinir uma senha.</p>
            </td>
            <td>
              <p>Isso pode ser causado por vários problemas, mas, em geral, esse erro é causado por uma paralisação do serviço ou problema de configuração que não pode ser resolvido. </p>
              <p>Se você vir esse erro e ele estiver afetando seus negócios, entre em contato com o suporte e iremos ajudá-lo urgentemente. Confira <a href="#information-to-include-when-you-need-help">Informações a serem incluídas quando precisar de Ajuda</a> para ver o que você deve fornecer ao engenheiro de suporte para auxiliá-lo em uma rápida resolução.</p>
            </td>
          </tr>
        </tbody></table>

## Solucionar problemas de write-back de senha
Se você encontrar um erro ao habilitar, desabilitar ou usar write-back de senha, pode solucioná-lo seguindo as etapas de solução de problemas abaixo:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de erro</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Qual é o erro exibido ao usuário?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solução</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Falhas gerais de inicialização e integração</p>
            </td>
            <td>
              <p>O serviço de redefinição de senha não é iniciado no local com erro 6800 no log de eventos de aplicativo do Azure AD Connect do computador.</p>
              <p>
                
              </p>
              <p>Depois da integração, usuários federados ou com senha de sincronização de hash não conseguem redefinir suas senhas.</p>
            </td>
            <td>
              <p>Quando o write-back de senha estiver habilitado, o mecanismo de sincronização chamará a biblioteca de write-back para realizar a configuração (integração) conversando com o serviço de integração em nuvem. Todos os erros encontrados durante a integração ou ao iniciar o ponto de extremidade WCF para write-back de senha resultará em erros no Log de eventos e no log de eventos do Azure AD Connect de seu computador.</p>
              <p>Durante a reinicialização do serviço ADSync, se o write-back tiver sido configurado, o ponto de extremidade do WCF será inicializado. No entanto, se a inicialização do ponto de extremidade falhar, nós simplesmente registraremos o evento 6800 e permitiremos a inicialização do serviço de sincronização. A presença desse evento significa que o write-back de senha de ponto de extremidade não foi iniciado. Os detalhes do log de eventos para esse evento (6800) e as entradas do log de eventos para gerar o componente PasswordResetService indicarão por que o ponto de extremidade não pôde ser inicializado. Revise esses erros do log de eventos e tente reiniciar o Azure AD Connect se o Write-back de senha não estiver funcionando. Se o problema persistir, tente desabilitar e reabilitar o write-back de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erro ao configurar o write-back durante a instalação do Azure AD Connect.</p>
            </td>
            <td>
              <p>Na última etapa do processo de instalação do Azure AD Connect, você verá um erro indicando que não foi possível configurar o write-back de senha.</p>
              <p>
                
              </p>
              <p>O log de eventos do aplicativo Azure AD Connect contém erro 32009 com texto "Erro ao obter token de autenticação".</p>
            </td>
            <td>
              <p>Esse erro ocorre nos seguintes casos:</p>
              <ul>
                <li class="unordered">
										Você especificou uma senha incorreta para a conta de administrador global indicada no início do processo de instalação do Azure AD Connect.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Você tentou usar um usuário federado para a conta de administrador global especificada no início do processo de instalação do Azure AD Connect <br\><br\></li>
              </ul>
              <p>Para corrigir esse erro, verifique se você não está usando uma conta federada para o administrador global que você indicou no início do processo de instalação do Azure AD Connect e se a senha especificada está correta.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erro ao configurar o write-back durante a instalação do Azure AD Connect.</p>
            </td>
            <td>
              <p>O log de eventos do computador do Azure AD Connect contém o erro 32002 gerado pelo PasswordResetService.</p>
              <p>
                
              </p>
              <p>O erro é: "erro de conexão com o barramento de serviço; o provedor de token não pôde fornecer um token de segurança..."</p>
              <p>
                
              </p>
            </td>
            <td>
              <p>A causa desse erro é que o serviço de redefinição de senha em execução no seu ambiente local não é capaz de conectar-se ao ponto de extremidade do barramento de serviço na nuvem. Esse erro normalmente é causado por uma regra de firewall bloqueando uma conexão de saída para uma determinada porta ou endereço Web.</p>
              <p>
                
              </p>
              <p>Verifique se o firewall permite conexões de saída para o seguinte:</p>
              <ul>
                <li class="unordered">
										Todo o tráfego sobre TCP 443 (HTTPS)<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Conexões de saída para <br\><br\></li>
              </ul>
              <p>
                
              </p>
              <p>Depois de atualizar essas regras, reinicialize o computador do Azure AD Connect e o write-back de senha deve começar a funcionar novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>O ponto de extremidade de write-back de senha local não pode ser acessado</p>
            </td>
            <td>
              <p>Depois de trabalhar por algum tempo, os usuários federados com senha de sincronização de hash não conseguem redefinir as suas senhas.</p>
            </td>
            <td>
              <p>Em alguns casos raros, o serviço de write-back de senha pode falhar ao iniciar novamente quando o Azure AD Connect for iniciado novamente. Nesses casos, primeiro verifique se o write-back de senha parece estar habilitado no local. Isso pode ser feito usando o Assistente do Azure AD Connect ou o PowerShell (confira a seção de tutoriais acima). Se o recurso parece estar habilitado, tente habilitar ou desabilitar o recurso novamente por meio da interface do usuário ou do PowerShell. Confira "Etapa 2: habilitar write-back de senha no seu computador de sincronização de diretórios e configurar regras de firewall" em <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">Como habilitar/desabilitar write-back de senha</a> para saber mais sobre como fazer isso.</p>
              <p>
                
              </p>
              <p>Se isso não funcionar, tente desinstalar completamente e reinstalar o Azure AD Connect.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erros de permissões</p>
            </td>
            <td>
              <p>Os usuários federados ou com senha de sincronização de hash que tentam redefinir as suas senhas veem um erro depois de enviar a senha indicando que houve um problema de serviço.</p>
              <p>
                
              </p>
              <p>Além disso, durante as operações de redefinição de senha, você pode ver um erro de que o agente de gerenciamento teve acesso negado em seus logs de eventos no local.</p>
            </td>
            <td>
              <p>Se você vir esses erros no log de eventos, confirme se a conta do AD MA (que foi especificada no assistente no momento da configuração) tem as permissões necessárias para write-back de senha.</p>
              <p>
                
              </p>
              <p>Observe que, depois que essa permissão é fornecida, pode levar até 1 hora para as permissões sejam repassadas através de tarefa em segundo plano sdprop no controlador de domínio. </p>
              <p>Para que a redefinição de senha funcione, a permissão deve ser marcada no descritor de segurança do objeto do usuário cuja senha esteja sendo redefinida. Até que essa permissão seja exibida no objeto do usuário, a redefinição de senha continuará a falhar com acesso negado.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erro ao configurar o write-back de senha do Assistente de configuração do Azure AD Connect </p>
            </td>
            <td>
              <p>Erro "não foi possível localizar MA" no assistente ao habilitar/desabilitar write-back de senha</p>
            </td>
            <td>
              <p>Há um bug conhecido na versão lançada do Azure AD Connect que se manifesta na seguinte situação:</p>
              <ol class="ordered">
                <li>
										Você configura o Azure AD Connect para o locatário abc.com (domínio verificado) usando credenciais. Isso resulta na criação do conector AAD com o nome "abc.com – AAD".<br\><br\></li>
                <li>
										Você então altera as credenciais AAD para o conector (usando a IU antiga) para (observe que é o mesmo locatário, mas com nome de domínio diferente).<br\><br\></li>
                <li>
										Agora você tenta habilitar/desabilitar o write-back de senha. O assistente construirá o nome do conector usando as credenciais como "abc.onmicrosoft.com – AAD" e passar para o cmdlet de write-back de senha. Isso irá falhar porque não há nenhum conector criado com esse nome.<br\><br\></li>
              </ol>
              <p>Esse problema foi corrigido em nossas compilações mais recentes. Se você tiver uma compilação mais antiga, uma solução é usar o cmdlet do PowerShell para habilitar/desabilitar o recurso. Confira "Etapa 2: habilitar write-back de senha no seu computador de sincronização de diretórios e configurar regras de firewall" em <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">Como habilitar/desabilitar write-back de senha</a> para saber mais sobre como fazer isso.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não é possível redefinir a senha de usuários em grupos especiais, como Administradores do domínio/Administradores corporativos, etc.</p>
            </td>
            <td>
              <p>Usuários federados ou com senha de sincronização de hash que fazem parte dos grupos protegidos e tentam redefinir sua senhas veem um erro depois de enviar a senha indicando que houve um problema de serviço.</p>
            </td>
            <td>
              <p>Os usuários privilegiados no Active Directory são protegidos com o AdminSDHolder. Confira <a href="https://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx">http://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx</a> para obter mais detalhes. </p>
              <p>
                
              </p>
              <p>Isso significa que os descritores de segurança desses objetos são verificados periodicamente para coincidir com o especificado no AdminSDHolder e são redefinidos se eles forem diferentes. Portanto, as permissões adicionais necessárias para o write-back de senha não aparecem para esses usuários. Isso pode resultar no não funcionamento do write-back de senha para esses usuários. Assim, não há suporte para gerenciamento de senhas para os usuários nesses grupos porque ele rompe o modelo de segurança do AD.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Falha nas operações de redefinição com a mensagem Objeto não pôde ser encontrado</p>
            </td>
            <td>
              <p>Os usuários federados ou com senha de sincronização de hash que tentam redefinir as suas senhas veem um erro depois de enviar a senha indicando que houve um problema de serviço.</p>
              <p>
                
              </p>
              <p>Além disso, durante as operações de redefinição de senha, você verá um erro em seus logs de eventos do serviço do Azure AD Connect indicando o erro "Objeto não encontrado".</p>
            </td>
            <td>
              <p>Esse erro geralmente indica que o mecanismo de sincronização não consegue encontrar o objeto de usuário no espaço do conector AAD ou o objeto de espaço de conector AD ou MV vinculado. </p>
              <p>
                
              </p>
              <p>Para solucionar esse problema, verifique se o usuário está realmente sincronizado do local para o AAD pela instância atual do Azure AD Connect e inspecione o estado dos objetos nos espaços de conector e MV. Confirme se o objeto do AD CS é conector para o objeto de MV através da regra "Microsoft.InfromADUserAccountEnabled.xxx".</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Falha de operações de redefinição com erro de várias correspondências encontradas</p>
            </td>
            <td>
              <p>Os usuários federados ou com senha de sincronização de hash que tentam redefinir as suas senhas veem um erro depois de enviar a senha indicando que houve um problema de serviço.</p>
              <p>
                
              </p>
              <p>Além disso, durante as operações de redefinição de senha, você pode ver um erro em seus logs de evento do serviço do Azure AD Connect indicando o erro "Várias correspondências encontradas".</p>
            </td>
            <td>
              <p>Isso indica que o mecanismo de sincronização detectou que o objeto de MV está conectado a mais de um objeto de AD CS através de "Microsoft.InfromADUserAccountEnabled.xxx". Isso significa que o usuário tem uma conta habilitada em mais de uma floresta. </p>
              <p>
                
              </p>
              <p>No momento,não há suporte para write-back de senha nesse cenário.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Falha nas operações de senha com um erro de configuração.</p>
            </td>
            <td>
              <p>Falha nas operações de senha com um erro de configuração. O log de eventos do aplicativo contém o erro Azure AD Connect 6329 com texto: 0x8023061f (a operação falhou porque a sincronização de senha não está habilitada nesse agente de gerenciamento.)</p>
            </td>
            <td>
              <p>Isso ocorre se a configuração de conexão do AD do Azure é alterada para adicionar uma nova floresta do AD (ou para remover e adicionar novamente uma floresta existente) <strong>após</strong> o recurso de write-back de senha já ter sido habilitado. As operações de senha para usuários em tais florestas recém-adicionadas falharão. Para corrigir o problema, desabilite e reabilite o recurso de write-back de senha após a conclusão das alterações de configuração da floresta.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>O write-back das senhas que foram redefinidas pelos usuários funciona corretamente, mas a gravação das senhas alteradas por um usuário ou redefinidas para um usuário por um administrador falha.</p>
            </td>
            <td>
              <p>Durante a tentativa de redefinir uma senha em nome de um usuário do Portal de Gerenciamento do Azure, você vê uma mensagem dizendo: "o serviço de redefinição de senha em execução no seu ambiente local não dá suporte a administradores redefinindo senhas de usuário. Atualize para a versão mais recente do Azure AD Connect para resolver o problema."</p>
            </td>
            <td>
              <p>Isso ocorre quando a versão do mecanismo de sincronização não dá suporte para a operação de write-back de senha específica que foi usada. As versões do Azure AD Connect posteriores a 1.0.0419.0911 dão suporte a todas as operações de gerenciamento de senha, incluindo write-back de redefinição de senha, write-back de alteração de senha e write-back de redefinição de senha iniciadas pelo administrador do Portal de Gerenciamento do Azure.&#160; As versões do DirSync mais recentes que 1.0.6862 dão suporte somente a write-back de redefinição de senha. Para resolver esse problema, recomendamos que você instale a versão mais recente do Azure AD Connect ou Connect do Active Directory do Azure (para saber mais, confira <a href="../active-directory-aadconnect#download-azure-ad-connect">Ferramentas de integração de diretório</a>) para resolver esse problema e obter o máximo proveito do write-back de senha na sua organização.</p>
            </td>
          </tr>
        </tbody></table>


## Códigos de erro do log de eventos de write-back de senha
É uma prática recomendada para solucionar problemas com write-back de senha inspecionar esse Log de eventos do aplicativo no computador do Azure AD Connect. Esse log de eventos contém eventos de duas fontes de interesse para write-back de senha. A fonte PasswordResetService descreve operações e problemas relacionados à operação de write-back de senha. A fonte ADSync descreve operações e problemas relacionados à configuração de senhas em seu ambiente do AD.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Código</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Nome/Mensagem</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Fonte</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Descrição</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>6329</p>
            </td>
            <td>
              <p>BAIL: MMS(4924) 0x80230619 – "Uma restrição impede que a senha seja alterada para a atual especificada."</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Esse evento ocorre quando o serviço de write-back de senha tenta definir uma senha em seu diretório local que não atende à idade, ao histórico, à complexidade da senha ou a requisitos de filtragem do domínio.</p>
              <ul>
                <li class="unordered">
										Se você tiver um tempo de vida mínimo da senha e tiver alterado recentemente a senha dentro desse intervalo de tempo, não poderá alterar a senha novamente até atingir a data especificada em seu domínio. Para fins de teste, a idade mínima deve ser definida como 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Se você tiver requisitos de histórico de senha habilitados, deve selecionar uma senha que não foi usada nas últimas X vezes, onde X é a configuração de histórico de senha. Se você selecionar uma senha que foi usada nas últimas X vezes, você verá uma falha. Para fins de teste, o histórico deve ser definido como 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Se você tiver requisitos de complexidade de senha, todos eles serão aplicados quando o usuário tentar alterar ou redefinir uma senha.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Se você tiver filtros de senha ativados e um usuário selecionar uma senha que não atende aos critérios de filtragem, a operação de redefinição ou de alteração falhará.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>HR 8023042</p>
            </td>
            <td>
              <p>O Mecanismo de Sincronização retornou um erro hr = 80230402, mensagem= Houve uma falha na tentativa de obter um objeto, porque existem entradas duplicadas com a mesma âncora</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Esse evento ocorre quando a mesma ID de usuário está habilitada em vários domínios. Por exemplo, se você estiver sincronizando florestas de Contas/Recursos e tiver a mesma ID de usuário presente e habilitada em cada uma delas, esse erro poderá ocorrer.  </p>
              <p>Esse erro também pode ocorrer se você estiver usando um atributo de âncora não exclusivo (como alias ou UPN) e dois usuários compartilharem o mesmo atributo de âncora.</p>
              <p>Para resolver esse problema, certifique-se de que você não tem nenhum usuário duplicado em seus domínios e de que você está usando um atributo de âncora exclusivo para cada usuário.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31001</p>
            </td>
            <td>
              <p>PasswordResetStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o serviço local detectou uma redefinição de senha de solicitação de usuário federado ou com senha de sincronização de hash vinda da nuvem. Esse evento é o primeiro evento em cada operação de write-back de redefinição de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31002</p>
            </td>
            <td>
              <p>PasswordResetSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o usuário selecionou uma nova senha durante uma operação de redefinição de senha; determinamos que essa senha atende aos requisitos de senha corporativa e essa senha foi gravada com êxito no ambiente AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31003</p>
            </td>
            <td>
              <p>PasswordResetFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que um usuário selecionou uma senha e que a senha chegou com sucesso ao ambiente local, mas, quando tentamos definir a senha no ambiente do AD local, ocorreu uma falha. Isso pode ocorrer por diversos motivos:</p>
              <ul>
                <li class="unordered">
										A senha do usuário não atende aos requisitos de idade, histórico, complexidade ou de filtragem para o domínio. Tente uma senha totalmente nova para resolver esse problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										A conta de serviço MA não tem as permissões apropriadas para definir a nova senha da conta de usuário em questão.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										A conta do usuário está em um grupo protegido, como administradores corporativos ou de domínio, que não permite operações de conjunto de senha.<br\><br\></li>
              </ul>
              <p>Confira <a href="#troubleshoot-password-writeback">Solucionar problemas de write-back de senha</a> para saber mais sobre outros situações que podem causar esse erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31004</p>
            </td>
            <td>
              <p>OnboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento ocorre se você habilitar o write-back de senha com o Azure AD Connect e indica que começamos a integração de sua organização para o serviço Web de write-back de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31005</p>
            </td>
            <td>
              <p>OnboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o processo de integração foi bem-sucedido e que o recurso de write-back de senha está pronto para uso.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31006</p>
            </td>
            <td>
              <p>ChangePasswordStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o serviço local detectou uma solicitação de alteração de senha para usuário federado ou com senha de sincronização de hash vinda da nuvem. Esse evento é o primeiro evento em cada operação de write-back de alteração de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31007</p>
            </td>
            <td>
              <p>ChangePasswordSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o usuário selecionou uma nova senha durante uma operação de alteração de senha; determinamos que essa senha atende aos requisitos de senha corporativa e essa senha foi gravada com êxito no ambiente AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31008</p>
            </td>
            <td>
              <p>ChangePasswordFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que um usuário selecionou uma senha e que a senha chegou com sucesso ao ambiente local, mas, quando tentamos definir a senha no ambiente do AD local, ocorreu uma falha. Isso pode ocorrer por diversos motivos:</p>
              <ul>
                <li class="unordered">
										A senha do usuário não atende aos requisitos de idade, histórico, complexidade ou de filtragem para o domínio. Tente uma senha totalmente nova para resolver esse problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										A conta de serviço MA não tem as permissões apropriadas para definir a nova senha da conta de usuário em questão.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										A conta do usuário está em um grupo protegido, como administradores corporativos ou de domínio, que não permite operações de conjunto de senha.<br\><br\></li>
              </ul>
              <p>Confira <a href="#troubleshoot-password-writeback">Solucionar problemas de write-back de senha</a> para saber mais sobre outras situações que podem causar esse erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31009</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>O serviço local detectou uma solicitação de redefinição de senha para um usuário federado ou com senha de sincronização de hash vinda do administrador em nome do usuário. Esse evento é o primeiro evento em cada operação de write-back de redefinição de senha iniciada pelo administrador.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31010</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>O administrador selecionou uma nova senha durante uma operação de redefinição de senha; determinamos que essa senha atende aos requisitos de senha corporativa e essa senha foi gravada com êxito no ambiente AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31011</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>O administrador selecionou uma senha em nome de um usuário e a senha chegou com sucesso no ambiente local, mas, quando tentamos definir a senha no ambiente do AD local, ocorreu uma falha. Isso pode ocorrer por diversos motivos:</p>
              <ul>
                <li class="unordered">
										A senha do usuário não atende aos requisitos de idade, histórico, complexidade ou de filtragem para o domínio. Tente uma senha totalmente nova para resolver esse problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										A conta de serviço MA não tem as permissões apropriadas para definir a nova senha da conta de usuário em questão.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										A conta do usuário está em um grupo protegido, como administradores corporativos ou de domínio, que não permite operações de conjunto de senha.<br\><br\></li>
              </ul>
              <p>Confira <a href="#troubleshoot-password-writeback">Solucionar problemas de write-back de senha</a> para saber mais sobre outros situações que podem causar esse erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31012</p>
            </td>
            <td>
              <p>OffboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento ocorre se você desabilitar o write-back de senha com o Azure AD Connect e indica que começamos a desvinculação de sua organização do serviço Web de write-back de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31013</p>
            </td>
            <td>
              <p>OffboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o processo de integração foi bem-sucedido e que o recurso de write-back de senha foi desabilitado com êxito.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31014</p>
            </td>
            <td>
              <p>OffboardingEventFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o processo de desvinculação não foi bem-sucedido. Isso pode ser devido a um erro de permissões da conta de administrador em nuvem ou no local especificada durante a configuração, ou porque você está tentando usar um administrador global de nuvem federado ao desabilitar o write-back de senha. Para corrigir esse problema, verifique as suas permissões administrativas e se não está usando uma conta federada ao configurar o recurso de write-back de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31015</p>
            </td>
            <td>
              <p>WriteBackServiceStarted </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o serviço de write-back de senha foi iniciado com êxito e está pronto para aceitar solicitações de gerenciamento de senha da nuvem.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31016</p>
            </td>
            <td>
              <p>WriteBackServiceStopped </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o serviço de write-back de senha foi interrompido e as solicitações de gerenciamento de senha da nuvem não serão bem-sucedidas.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31017</p>
            </td>
            <td>
              <p>AuthTokenSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que recuperamos com êxito um token de autorização para o administrador global especificado durante a instalação do Azure AD Connect para iniciar o processo de desvinculação ou de integração.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31018</p>
            </td>
            <td>
              <p>KeyPairCreationSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que criamos com êxito a chave de criptografia de senha que será usada para criptografar senhas da nuvem que serão enviadas ao seu ambiente local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32000</p>
            </td>
            <td>
              <p>UnknownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica um erro desconhecido durante uma operação de gerenciamento de senha. Examine o texto da exceção no evento para obter mais detalhes. Se você estiver tendo problemas, tente desabilitar e reabilitar o write-back de senha. Se isso não ajudar, inclua uma cópia do seu log de eventos com a id de acompanhamento especificada internamente ao seu engenheiro de suporte.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32001</p>
            </td>
            <td>
              <p>ServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que houve um erro ao conectar ao serviço de redefinição de senha na nuvem e isso geralmente ocorre quando o serviço local não pôde se conectar ao serviço Web de redefinição de senha. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32002</p>
            </td>
            <td>
              <p>ServiceBusError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que houve um erro ao conectar-se a instância do barramento de serviço do seu locatário. Isso pode acontecer porque você está bloqueando as conexões de saída no seu ambiente local. Verifique o seu firewall para que permita conexões sobre TCP 443 e para <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, e tente novamente. Se você ainda estiver tendo problemas, tente desabilitar e reabilitar o write-back de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32003</p>
            </td>
            <td>
              <p>InPutValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que a entrada passada para a API do nosso serviço Web era inválida. Tente a operação novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32004</p>
            </td>
            <td>
              <p>DecryptionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que houve um erro ao descriptografar a senha que chegou da nuvem. Isso pode ser devido a uma falta de correspondência de chave de descriptografia entre o serviço de nuvem e o ambiente local. Para resolver esse problema, desabilite e habilite novamente o write-back de senha em seu ambiente local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32005</p>
            </td>
            <td>
              <p>ConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Durante a migração, nós salvamos informações específicas de locatário em um arquivo de configuração no seu ambiente local. Esse evento indica que houve um erro ao salvar esse arquivo ou que, quando o serviço foi iniciado, havia erro na leitura do arquivo. Para corrigir esse problema, tente desabilitar e reabilitar o write-back de senha para forçar a regravação desse arquivo de configuração. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32006</p>
            </td>
            <td>
              <p>EndPointConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>PRETERIDO – Esse evento não está presente no Azure AD Connect, somente em compilações antigas do DirSync que davam suporte ao write-back.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32007</p>
            </td>
            <td>
              <p>OnBoardingConfigUpdateError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Durante a migração, podemos enviar dados da nuvem para o serviço de redefinição de senha local. Esses dados são gravados em um arquivo na memória antes de serem enviados ao serviço de sincronização para armazená-los com segurança no disco. Esse evento indica um problema com a gravação ou a atualização dos dados na memória. Para corrigir esse problema, tente desabilitar e reabilitar o write-back de senha para forçar a regravação dessa configuração.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32008</p>
            </td>
            <td>
              <p>ValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que recebemos uma resposta inválida do serviço Web de redefinição de senha. Para corrigir esse problema, tente desabilitar e reabilitar o write-back de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32009</p>
            </td>
            <td>
              <p>AuthTokenError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que não conseguimos obter um token de autorização para a conta de administrador global especificada durante a instalação do Azure AD Connect. Esse erro pode ser causado por um nome de usuário ou senha inválidos especificados para a conta de administrador global ou porque a conta de administrador global especificada é federada. Para corrigir esse problema, execute novamente a configuração com o nome de usuário e senha corretos e verifique se o administrador é uma conta gerenciada (somente em nuvem ou sincronização de senha).</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32010</p>
            </td>
            <td>
              <p>CryptoError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que houve um erro ao gerar a chave de criptografia de senha ou ao descriptografar uma senha que chegou do serviço de nuvem. Esse erro provavelmente indica um problema com o seu ambiente. Examine os detalhes de seu log de eventos para saber mais e resolver esse problema. Você também pode tentar desabilitar e reabilitar o serviço de write-back de senha para resolver o problema.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32011</p>
            </td>
            <td>
              <p>OnBoardingServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o serviço local não pôde se comunicar corretamente com o serviço Web de redefinição de senha para iniciar o processo de integração. Isso pode ser devido a uma regra de firewall ou a um problema na obtenção de um token de autenticação para o seu locatário. Para corrigir esse problema, verifique se você não está bloqueando as conexões de saída sobre TCP 443 e TCP 9350 9354 ou para <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, e que a conta de administrador AAD que você está usando para a integração não é federada. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32012</p>
            </td>
            <td>
              <p>OnBoardingServiceDisableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>PRETERIDO – Esse evento não está presente no Azure AD Connect, somente em compilações antigas do DirSync que davam suporte ao write-back.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32013</p>
            </td>
            <td>
              <p>OffBoardingError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o serviço local não pôde se comunicar corretamente com o serviço Web de redefinição de senha para iniciar o processo de desvinculação. Isso pode ser devido a uma regra de firewall ou a um problema na obtenção de um token de autorização para o seu locatário. Para corrigir esse problema, verifique se você não está bloqueando as conexões de saída para 443 ou para <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, e que a conta de administrador AAD que você está usando para a integração não é federada. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32014</p>
            </td>
            <td>
              <p>ServiceBusWarning </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que tivemos que tentar novamente nos conectar à instância de barramento de serviço do locatário. Em condições normais, isso deve não ser uma preocupação, mas se você vir esse evento muitas vezes, considere verificar a sua conexão de rede para o barramento de serviço, especialmente se for uma conexão de largura de banda baixa ou de alta latência.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32015</p>
            </td>
            <td>
              <p>ReportServiceHealthError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Para monitorar a integridade do seu serviço de write-back de senha, podemos enviar dados de pulsação para o nosso serviço Web de redefinição de senha a cada cinco minutos. Esse evento indica que houve um erro ao enviar essas informações de integridade para o serviço Web em nuvem. Essas informações de integridade não incluem dados OII ou PII e é essencialmente uma pulsação e estatísticas básicas de serviço para que possamos fornecer informações de status do serviço na nuvem.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33001</p>
            </td>
            <td>
              <p>ADUnKnownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que foi retornado um erro desconhecido pelo AD; verifique no log de eventos do servidor Azure AD Connect eventos da fonte ADSync para saber mais sobre esse erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33002</p>
            </td>
            <td>
              <p>ADUserNotFoundError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o usuário que está tentando redefinir ou alterar uma senha não foi encontrado no diretório local. Isso pode ocorrer quando o usuário foi excluído no local, mas não na nuvem, ou se houver um problema com a sincronização. Verifique os logs de sincronização, bem como os detalhes das últimas sincronizações para saber mais.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33003</p>
            </td>
            <td>
              <p>ADMutliMatchError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Quando uma solicitação de redefinição ou de alteração de senha são provenientes da nuvem, usamos a âncora de nuvem especificada durante a instalação do Azure AD Connect para determinar como vincular essa solicitação a um usuário no seu ambiente local. Esse evento indica que encontramos dois usuários em seu diretório local com o mesmo atributo de âncora de nuvem. Verifique os logs de sincronização, bem como os detalhes das últimas sincronizações para saber mais.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33004</p>
            </td>
            <td>
              <p>ADPermissionsError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que a conta de serviço do Agente de Gerenciamento não tem as permissões apropriadas na conta em questão para definir uma nova senha. Verifique se a conta MA na floresta do usuário tem permissões para Redefinir e Alterar senha em todos os objetos na floresta. Para saber mais sobre como fazer isso, confira <a href="../active-directory-passwords-getting-started#step-4-set-up-the-appropriate-active-directory-permissions">Etapa 4: configurar as permissões apropriadas do Active Directory</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33005</p>
            </td>
            <td>
              <p>ADUserAccountDisabled </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que tentamos redefinir ou alterar uma senha de uma conta que foi desabilitada no local. Habilite a conta e tente a operação novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33006</p>
            </td>
            <td>
              <p>ADUserAccountLockedOut </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que tentamos redefinir ou alterar uma senha de uma conta que foi bloqueada no local. Bloqueios podem ocorrer quando um usuário tiver tentado alterar ou redefinir a senha muitas vezes em um curto período de tempo. Desbloqueie a conta e tente a operação novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33007</p>
            </td>
            <td>
              <p>ADUserIncorrectPassword </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o usuário especificou uma senha atual incorreta durante a operação de alteração de senha. Especifique a senha atual correta e tente novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33008</p>
            </td>
            <td>
              <p>ADPasswordPolicyError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento ocorre quando o serviço de write-back de senha tenta definir uma senha em seu diretório local que não atende à idade, ao histórico, à complexidade da senha ou a requisitos de filtragem do domínio.</p>
              <ul>
                <li class="unordered">
										Se você tiver um tempo de vida mínimo da senha e tiver alterado recentemente a senha dentro desse intervalo de tempo, não poderá alterar a senha novamente até atingir a data especificada em seu domínio. Para fins de teste, a idade mínima deve ser definida como 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Se você tiver requisitos de histórico de senha habilitados, deve selecionar uma senha que não foi usada nas últimas X vezes, onde X é a configuração de histórico de senha. Se você selecionar uma senha que foi usada nas últimas X vezes, você verá uma falha. Para fins de teste, o histórico deve ser definido como 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Se você tiver requisitos de complexidade de senha, todos eles serão aplicados quando o usuário tentar alterar ou redefinir uma senha.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Se você tiver filtros de senha ativados e um usuário selecionar uma senha que não atende aos critérios de filtragem, a operação de redefinição ou de alteração falhará.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>33009</p>
            </td>
            <td>
              <p>ADConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que houve um problema na gravação de senha no seu diretório local devido a um problema de configuração com o Active Directory. Verifique as mensagens do serviço ADSync no log de eventos de aplicativo do Azure AD Connect do computador para saber mais sobre o erro. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34001</p>
            </td>
            <td>
              <p>ADPasswordPolicyOrPermissionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>PRETERIDO – Esse evento não está presente no Azure AD Connect, somente em compilações antigas do DirSync que davam suporte ao write-back.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34002</p>
            </td>
            <td>
              <p>ADNotReachableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>PRETERIDO – Esse evento não está presente no Azure AD Connect, somente em compilações antigas do DirSync que davam suporte ao write-back.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34003</p>
            </td>
            <td>
              <p>ADInvalidAnchorError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>PRETERIDO – Esse evento não está presente no Azure AD Connect, somente em compilações antigas do DirSync que davam suporte ao write-back.</p>
            </td>
          </tr>
        </tbody></table>
## Solucionar problemas de conectividade de write-back de senha

Se você estiver enfrentando interrupções de serviço com o componente de write-back de senha do Azure AD Connect, abaixo estão algumas etapas rápidas que você pode seguir para resolver esse problema:

 - [Reiniciar o serviço de sincronização do Azure AD Connect](#restart-the-azure-AD-sync-service)
 - [Desabilitar e reabilitar o recurso de write-back de senha](#disable-and-re-enable-the-password-writeback-feature)
 - [Instalar a versão mais recente do Azure AD Connect](#install-the-latest-azure-ad-connect-release)
 - [Solucionar problemas de write-back de senha](#troubleshoot-password-writeback)

Em geral, recomendamos que você execute essas etapas na ordem acima para recuperar o serviço da maneira mais rápida.

### Reiniciar o serviço de sincronização do Azure AD Connect
A reinicialização do serviço de sincronização do Azure AD Connect pode ajudar a resolver problemas de conectividade ou outros problemas temporários com o serviço.

 1.	Como administrador, clique em **Iniciar** no servidor que executa o **Azure AD Connect**.
 2.	Digite **"Services.msc"** na caixa de pesquisa e pressione **Enter**.
 3.	Procure a entrada do **Microsoft Azure AD Connect**.
 4.	Com o botão direito do mouse na entrada do serviço, clique em **Reiniciar** e aguarde a conclusão da operação.

    ![][002]

Essas etapas irão restabelecer a conexão com o serviço em nuvem e resolver qualquer interrupção que você esteja enfrentando. Se a reinicialização do serviço de sincronização não resolver o seu problema, recomendamos que você tente desabilitar e reabilitar o recurso de write-back de senha como uma próxima etapa.

### Desabilitar e reabilitar o recurso de write-back de senha
Desabilitar e reabilitar o recurso de write-back de senha podem ajudar a resolver problemas de conectividade.

 1.	Como administrador, abra o **Assistente de configuração do Azure AD Connect**.
 2.	No diálogo **Conectar ao Azure AD**, insira suas **credenciais de administrador global do Azure AD**
 3.	No diálogo **Conectar-se ao AD DS**, insira suas **credenciais de administrador de serviços de domínio do AD**.
 4.	No diálogo **Identificar exclusivamente os seus usuários**, clique no botão **Próximo**.
 5.	No diálogo **Recursos opcionais**, desmarque a caixa de seleção **Write-back de senha**.

    ![][003]

 6.	Clique em **Próximo** nas páginas de diálogo restantes sem alterar nada até chegar à página **Pronto para configurar**.
 7.	Verifique se a página de configuração mostra a **Opção de write-back de senha como desabilitada** e clique no botão verde **Configurar** para confirmar as suas alterações.
 8.	No diálogo **Concluído**, desmarque a opção **Sincronizar agora** e clique em **Concluir** para fechar o assistente.
 9.	Abra novamente o **Assistente de configuração do Azure AD Connect**.
 10.	**Repita as etapas de 2 a 8**, mas confirme que você **marcou a opção de write-back de senha** na tela **Recursos opcionais** para reabilitar o serviço.

    ![][004]

Essas etapas irão restabelecer a conexão com o serviço em nuvem e resolver qualquer interrupção que você esteja enfrentando.

Se desabilitar e reabilitar o recurso de write-back de senha não resolver o seu problema, recomendamos que você tente instalar novamente o Azure AD Connect como uma próxima etapa.

### Instalar a versão mais recente do Azure AD Connect
A reinstalação do pacote do Azure AD Connect resolverá qualquer problema de configuração que possa estar afetando a sua capacidade de se conectar aos nossos serviços em nuvem ou de gerenciar senhas em seu ambiente AD local. Recomendamos que você execute esta etapa somente após tentar as duas primeiras etapas descritas acima.

 1.	Baixe a versão mais recente do Azure AD Connect [aqui](active-directory-aadconnect.md#download-azure-ad-connect).
 2.	Uma vez que você já tenha instalado o Azure AD Connect, só precisará realizar uma atualização in-loco para atualizar sua instalação do Azure AD na versão mais recente.
 3.	Execute o pacote baixado e siga as instruções na tela para atualizar o computador do Azure AD Connect. Não há etapas manuais adicionais necessárias, a menos que você tenha personalizado as regras para sincronização fora da caixa; nesse caso, você deve **fazer o backup antes de prosseguir com a atualização e reimplantá-lo novamente depois de terminar**.

Essas etapas irão restabelecer a conexão com o serviço em nuvem e resolver qualquer interrupção que você esteja enfrentando.

Se a instalação da versão mais recente do servidor Azure AD Connect não resolver o seu problema, recomendamos que você tente desabilitar e reabilitar o write-back de senha como uma etapa final depois de instalar a QFE de sincronização mais recente.

Se isso não resolver o problema, recomendamos que você dê uma olhada em [Solucionar problemas de write-back de senha](#troubleshoot-password-writeback) e [Perguntas frequentes sobre o gerenciamento de senhas do Azure AD](active-directory-passwords-faq.md) para ver se o problema pode ter sido discutido ali.


<br/> <br/> <br/>

## Links para a documentação de redefinição de senha
Veja abaixo links para todas as páginas de documentação sobre Redefinição de Senha do AD do Azure:

* [**Redefinir sua própria senha**](active-directory-passwords-update-your-own-password.md): saiba mais sobre como redefinir ou alterar sua própria senha como um usuário do sistema
* [**Como funciona**](active-directory-passwords-how-it-works.md) - saiba mais sobre os seis diferentes componentes do serviço e o que cada um deles faz
* [**Introdução**](active-directory-passwords-getting-started.md) - saiba como permitir que os usuários redefinam e alterem suas senhas na nuvem ou no local
* [**Personalizar**](active-directory-passwords-customize.md) - aprenda a personalizar a aparência e o comportamento do serviço de acordo com as necessidades de sua organização
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md) - aprenda a implantar rapidamente e gerenciar com eficiência as senhas em sua organização
* [**Obter percepções**](active-directory-passwords-get-insights.md) - saiba mais sobre nossos recursos integrados de relatórios
* [**Perguntas frequentes**](active-directory-passwords-faq.md) - obtenha respostas para perguntas frequentes
* [**Saiba mais**](active-directory-passwords-learn-more.md) - aprofunde-se nos detalhes técnicos do funcionamento do serviço



[001]: ./media/active-directory-passwords-troubleshoot/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-troubleshoot/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-troubleshoot/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-troubleshoot/004.jpg "Image_004.jpg"

<!---HONumber=AcomDC_1125_2015-->