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
	ms.date="09/18/2015" 
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

Coluna|Valores permitidos e seus significados
---|---
Dados Registrados| **Email alternativo**– email alternativo do usuário usado ou email de autenticação para autenticar o<p><p>**Telefone do Office**– telefone comercial usado pelo usuário para autenticar<p>**Celular** - celular usado pelo usuário ou telefone de autenticação para autenticar as<p>**Perguntas de segurança** – perguntas de segurança usadas pelo usuário para autenticar<p>**Qualquer combinação dos itens acima (por exemplo, Email alternativo + Celular)** – ocorre quando uma política de 2 acessos for especificada e mostra quais os dois métodos usados pelo usuário para autenticação da solicitação de redefinição de sua senha.

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


Coluna|Valores permitidos e seus significados
---|---
Métodos usados|**Email alternativo**– email alternativo do usuário usado ou email de autenticação para autenticar o<p>**Telefone do Office**– telefone comercial usado pelo usuário para autenticar<p>**Celular** - celular usado pelo usuário ou telefone de autenticação para autenticar as<p>**Perguntas de segurança** – perguntas de segurança usadas pelo usuário para autenticar<p>**Qualquer combinação dos itens acima (por exemplo, Email alternativo + Celular)** – ocorre quando uma política de 2 acessos for especificada e mostra quais os dois métodos usados pelo usuário para autenticação da solicitação de redefinição de sua senha.
Result|**Abandonado**– usuário iniciou a redefinição de senha mas, em seguida, parou na metade sem concluir<p>**Bloqueado** – a conta de usuário foi impedida de usar a redefinição devido à tentativa de uso da página de redefinição de senha ou do acesso de redefinição da senha muitas vezes em um período de 24 horas<p>**Cancelado** – usuário iniciou a redefinição de senha do usuário, mas, em seguida, clicou no botão de Cancelar no meio do processo<p>**Administrador contatado** – o usuário teve um problema durante a sessão que não foi possível resolver , então o usuário clicou no link "Entre em contato com seu administrador" em vez de concluir o fluxo de redefinição de senha<p>**Falha** – usuário não foi capaz de redefinir uma senha, provavelmente porque o usuário não foi configurado para usar o recurso (por exemplo, nenhuma licença, sem informações de autenticação, senha gerenciada localmente, mas gravação desativada).<p>** Bem-sucedida** – a redefinição de senha foi bem-sucedida.
Detalhes|Consulte a tabela abaixo.

### Valores permitidos para a coluna de detalhes
Abaixo está a lista de tipos de resultado que você pode esperar ao usar o relatório de atividade de redefinição de senha:

Detalhes | Tipo de resultado
----|----
Abandonado pelo usuário depois de concluir a opção de verificação de email | Abandonado
Abandonado pelo usuário depois de concluir a opção de verificação de SMS móvel|Abandonado
Abandonado pelo usuário depois de concluir a opção de verificação de chamada de voz móvel | Abandonado
Abandonado pelo usuário depois de concluir a opção de verificação de chamada de voz comercial | Abandonado
Abandonado pelo usuário depois de concluir a opção de perguntas de segurança|Abandonado
Abandonado pelo usuário depois de inserir sua ID de usuário| Abandonado
Abandonado pelo usuário depois de iniciar a opção de verificação de email|Abandonado
Abandonado pelo usuário depois de iniciar a opção de verificação de SMS móvel|Abandonado
Abandonado pelo usuário depois de iniciar a opção de verificação de chamada de voz móvel|Abandonado
Abandonado pelo usuário depois de iniciar a opção de verificação de chamada de voz comercial|Abandonado
Abandonado pelo usuário depois de iniciar a opção de perguntas de segurança| Abandonado
Abandonado pelo usuário antes de selecionar uma nova senha| Abandonado
Abandonado pelo usuário ao selecionar uma nova senha| Abandonado
O usuário inseriu muitos códigos de verificação de SMS inválidos e está bloqueado para 24 horas|Bloqueado
O usuário tentou muitas vezes a verificação de voz por telefone celular e está bloqueado por 24 horas|Bloqueado
O usuário tentou muitas vezes a verificação de voz comercial e está bloqueado por 24 horas |Bloqueado
O usuário tentou responder às perguntas de segurança muitas vezes e está bloqueado por 24 horas| Bloqueado
O usuário tentou verificar um número de telefone muitas vezes e está bloqueado por 24 horas|Bloqueado
O usuário fez o cancelamento antes de passar pelos métodos de autenticação obrigatórios|Cancelado
O usuário fez o cancelamento antes de enviar uma nova senha|Cancelado
O usuário contatou um administrador depois de tentar a opção de verificação de email |Administrador contatado
O usuário contatou um administrador depois de tentar a opção de verificação de SMS móvel|Administrador contatado
O usuário contatou um administrador depois de tentar a opção de verificação de chamada de voz móvel|Administrador contatado
O usuário contatou um administrador depois de tentar a opção de verificação de chamada de voz comercial |Administrador contatado
O usuário contatou um administrador depois de tentar a opção de verificação de pergunta de segurança|Administrador contatado
A redefinição de senha não está habilitada para este usuário. Habilite a redefinição de senha na guia Configurar para resolver o problema| Falha
O usuário não tem uma licença. Você pode adicionar uma licença para o usuário para resolver o problema|Falha
O usuário tentou redefinir em um dispositivo sem cookies habilitados| Falha
A conta do usuário tem métodos de autenticação insuficiente definidos. Adicione informações de autenticação para resolver esse problema|Falha
A senha do usuário é gerenciada localmente. Você pode habilitar o Write-back de Senha para resolver o problema|Falha
Não foi possível acessar o serviço de redefinição de senha no local. Verifique o log de eventos de seu computador de sincronização|Falha
Encontramos um problema durante a redefinição de senha local do usuário. Verifique o log de eventos de seu computador de sincronização | Falha
Este usuário não é membro do grupo de usuários de redefinição de senha. Adicione esse usuário ao grupo para resolver o problema.|Falha
A redefinição de senha foi desabilitada inteiramente para este locatário. Consulte[aqui](http://aka.ms/ssprtroubleshoot)para resolver o problema. | Falha
O usuário redefiniu a senha com êxito|Bem-sucedido

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

<!---HONumber=Sept15_HO4-->