<properties 
	pageTitle="Gerenciando o Azure AD Connect" 
	description="Aprenda a estender configuração padrão e tarefas operacionais para o Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Gerenciando o Azure AD Connect 

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="O que é">O que é</a> <a href="../active-directory-aadconnect-how-it-works/" title="Como ele funciona">Como ele funciona</a> <a href="../active-directory-aadconnect-get-started/" title="Introdução">Introdução</a> <a href="../active-directory-aadconnect-whats-next/" title="Próximos passos" class="current">Próximos passos</a> <a href="../active-directory-aadconnect-learn-more/" title="Saiba mais">Saiba mais</a>
</div>

A seguir estão tópicos operacionais avançados que permitem que você personalize o Azure Active Directory Connect para atender às necessidades e requisitos de sua organização.

## Atribuir licenças aos usuários do Azure AD Premium e do Enterprise Mobility

Agora que os usuários foram sincronizados para a nuvem, você precisará atribua uma licença para que eles possam começar a usar aplicativos de nuvem, como o Office 365.

### Como atribuir uma licença do Enterprise Mobility Suite ou do Azure AD Premium
--------------------------------------------------------------------------------
<ol>
<li>Entre no portal do Azure como administrador.</li>
<li>Selecione **Active Directory** à esquerda.</li>
<li>Na página do Active Directory, clique duas vezes no diretório que tenha os usuários que você deseja habilitar.</li>
<li>Na parte superior da página do diretório, selecione **Licenças**.</li>
<li>Na página de licenças, selecione Active Directory Premium ou Enterprise Mobility Suite e, em seguida, clique em **Atribuir**.</li>
<li>Na caixa de diálogo, selecione os usuários para os quais você deseja atribuir licenças e, em seguida, clique no ícone de marca de seleção para salvar as alterações.</li>


## Verificando a tarefa de sincronização agendada
Se você quiser verificar o status de uma sincronização, você pode fazer isso verificando no portal do Azure.

### Como verificar a tarefa de sincronização agendada
--------------------------------------------------------------------------------

1. Entre no portal do Azure como administrador.
2. Selecione **Active Directory** à esquerda.
3. Na página do Active Directory, clique duas vezes no diretório que tenha os usuários que você deseja habilitar.
4. Na parte superior da página do diretório, selecione **Integração de diretório**.
5. Na integração com o Active Directory local, observe o horário da última sincronização.

<center>![Nuvem](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## Iniciando uma tarefa de sincronização agendada
Se você precisar executar uma tarefa de sincronização, você pode fazer isso executando novamente por meio do assistente do Azure AD Connect. Você precisará fornecer suas credenciais do Azure AD. No assistente, selecione a tarefa **Personalizar opções de sincronização** e clique em Avançar no assistente. No final, certifique-se de que a caixa **Iniciar o processo de sincronização assim que a configuração for concluída** esteja marcada.

<center>![Nuvem](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>




## Tarefas adicionais disponíveis no Azure AD Connect
Após a instalação inicial do Azure AD Connect, você pode sempre iniciar o assistente novamente de um atalho da área de trabalho ou da página inicial do Azure AD Connect. Você observará que executar novamente o assistente fornece algumas novas opções na forma de tarefas adicionais.

A tabela a seguir fornece um resumo dessas tarefas e uma breve descrição de cada uma delas.

<center>![Regra de junção](./media/active-directory-aadconnect-whats-next/addtasks.png) </center>

Tarefas adicionais | Descrição 
------------- | ------------- |
Exibir o cenário selecionado |Permite que você exiba sua solução atual para o Azure AD Connect. Isso inclui configurações gerais, diretórios sincronizados, configurações de sincronização, etc.
Personalizar opções de sincronização | Permite que você altere a configuração atual, incluindo a adição de florestas do Active Directory para a configuração ou habilitando opções de sincronização como usuário, grupo, dispositivo ou write-back de senha.
Habilitar o modo de preparação | Isso permite que você prepare as informações que posteriormente serão sincronizadas, mas nada será exportado para o Azure AD ou Active Directory. Isso permite que você visualize as sincronizações antes que elas ocorram.


 






## Documentação adicional
Para obter documentação adicional sobre como trabalhar com o Azure AD Connect, consulte a seguir:

- [Alterando a configuração padrão do Azure AD Connect](active-directory-aadconnect-whats-next-change-default-config.md)
- [Usando o editor de regras de sincronização do Azure AD Connect](active-directory-aadconnect-whats-next-synch-rules-editor.md)
- [Usando provisionamento declarativo](active-directory-aadconnect-whats-next-declarative-prov.md)

Além disso, algumas das documentações criadas para o Azure AD Sync ainda são relevantes e aplicam-se ao Azure AD Connect. Embora todos os esforços estejam sendo feitos para trazer essa documentação para o Azure.com, uma parte dela ainda reside na biblioteca de escopo do MSDN. Para obter documentação adicional, consulte [Azure AD Connect no MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) e [Azure AD Sync no MSDN](https://msdn.microsoft.com/library/azure/dn790204.aspx).


 

<!---HONumber=July15_HO4-->