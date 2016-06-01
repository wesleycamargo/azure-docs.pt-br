<properties
   pageTitle="Azure AD Connect: atualizar da ferramenta de sincronização do AD do Microsoft Azure (DirSync) | Microsoft Azure"
   description="Saiba como atualizar do DirSync para o Azure AD Connect. Esse artigo descreve as etapas para atualizar sua atual ferramenta de sincronização do AD do Microsoft Azure (DirSync) ao Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="05/19/2016"
   ms.author="andkjell;shoatman;billmath"/>

# Atualizando a sincronização do Active Directory do Microsoft Azure (DirSync) com o Azure AD Connect

A documentação a seguir ajudará você a atualizar sua instalação DirSync existente para o Azure AD Connect.

## Documentação relacionada
Se você não leu a documentação em [Integrando suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md), a tabela a seguir fornece links para tópicos relacionados. Os dois primeiros tópicos em negrito são necessários antes de iniciar a atualização do DirSync.

 Tópico |  
--------- | ---------
**Baixar o Azure AD Connect** | [Baixar o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)
**Hardware e pré-requisitos** | [Azure AD Connect: hardware e pré-requisitos](active-directory-aadconnect-prerequisites.md)
**Contas usadas para instalação** | [Mais informações sobre permissões e contas do Azure AD Connect](active-directory-aadconnect-accounts-permissions.md)

## Atualizar do DirSync
Dependendo de sua implantação atual do DirSync, há diferentes opções de atualização. Se o tempo de atualização esperado for menor que 3 horas, é recomendável fazer uma atualização in-loco. Se o tempo de atualização esperado for maior que 3 horas, é recomendável fazer uma implantação paralela em outro servidor. Estima-se que se você tiver mais de 50.000 objetos levará mais de 3 horas para fazer a atualização.

Cenário |  
---- | ----
[Atualização in-loco](#in-place-upgrade) | Opção preferida se a atualização deve levar menos de 3 horas.
[Implantação paralela](#parallel-deployment) | Opção preferida se a atualização deve levar mais de 3 horas.

>[AZURE.NOTE] Quando você planejar atualizar do DirSync para o Azure AD Connect, não desinstale o DirSync por conta própria antes da atualização. O Azure AD Connect lerá e migrará a configuração do DirSync e a desinstalará depois de inspecionar o servidor.

**Atualização in-loco**

O tempo estimado para concluir a atualização é exibido pelo assistente. Essa estimativa se baseia na suposição de que levará 3 horas para concluir uma atualização para um banco de dados com 50.000 objetos (usuários, contatos e grupos). O Azure AD Connect analisará as configurações atuais do DirSync e recomendará uma atualização in-loco se o número de objetos no banco de dados for menor do que 50.000. Se você decidir continuar, suas configurações atuais serão aplicadas automaticamente durante a atualização e o servidor reiniciará automaticamente a sincronização ativa.

Se você quiser fazer uma migração da configuração e uma implantação paralela, pode substituir a recomendação de atualização in-loco. Por exemplo, você pode aproveitar para atualizar o hardware e o sistema operacional. Consulte a seção de [implantação paralela](#parallel-deployment) para obter mais informações.

**Implantação paralela**

Usar uma implantação paralela é recomendável se você tiver mais de 50.000 objetos. Isso evitará que os usuários tenham atrasos operacionais. A instalação do Azure AD Connect tentará estimar o tempo de inatividade da atualização, mas se você tiver atualizado o DirSync no passado, sua própria experiência será provavelmente o melhor guia.

### Configurações compatíveis com o DirSync a serem atualizadas
As seguintes alterações de configuração são compatíveis com o DirSync e serão atualizadas:

- Domínio e filtragem de unidade organizacional
- ID alternativa (UPN)
- Sincronização de senha e configurações híbridas do Exchange
- Suas configurações de domínio/floresta e do AD do Azure
- Filtragem com base em atributos de usuário

As alterações a seguir não podem ser atualizadas. Se você fez essa alteração, a atualização será bloqueada:

- Alterações de DirSync sem suporte, por exemplo, atributos removidos e uso de uma DLL de extensão personalizada

![Atualização bloqueada](./media/active-directory-aadconnect-dirsync-upgrade-get-started/analysisblocked.png)

Nesses casos, a recomendação é instalar um novo servidor do Azure AD Connect no [modo de preparo](active-directory-aadconnectsync-operations.md#staging-mode) e verificar a antiga configuração do DirSync e a nova configuração do Azure AD Connect. Aplique qualquer alteração novamente usando uma configuração personalizada, como descrito em [Configuração personalizada de sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Senhas usadas pelo DirSync para as contas de serviço não podem ser recuperadas e não serão migradas. Essas senhas serão redefinidas durante a atualização.

### Etapas de alto nível para atualização do DirSync para o Azure AD Connect

1. Bem-vindo ao Azure AD Connect
2. Análise da configuração do DirSync atual
3. Coletar senha de administrador global do AD do Azure
4. Coletar credenciais para uma conta de administrador corporativo (usado somente durante a instalação do Azure AD Connect)
5. Instalação do Azure AD Connect
    * Desinstalar o DirSync
	* Instalar o AD do Azure Connect
	* Opcionalmente, inicie a sincronização

Etapas adicionais são necessárias quando:

* Você está usando no momento o SQL Server completo - local ou remoto
* Você tem mais de 50.000 objetos no escopo para sincronização

## Atualização in-loco

1. Iniciar o instalador do Azure AD Connect (MSI).
2. Leia e concorde com os termos de licença e o aviso de privacidade. ![Bem-vindo ao AD do Azure](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)
3. Clique em Avançar para começar a análise de sua instalação do DirSync existente. ![Analisando instalação de sincronização de diretório existente](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)
4. Quando a análise for concluída, faremos recomendações sobre como proceder.  
    - Se você usar o SQL Server Express e tiver menos de 50.000 objetos, a seguinte tela será mostrada: ![Análise concluída pronta para atualizar a partir do DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)
    - Se você usar um SQL Server completo para o DirSync, você verá esta página: ![Análise concluída pronta para atualizar a partir do DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)<BR/> São exibidas as informações sobre o servidor de banco de dados existente do SQL Server que está sendo usado pelo DirSync. Faça os ajustes apropriados se necessário. Clique em **Avançar** para continuar a instalação.
    - Se você tiver mais de 50.000 objetos, você verá esta tela: ![Análise concluída pronta para atualizar a partir do DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)<BR/> Para continuar com a atualização in-loco, clique na caixa de seleção próxima a esta mensagem: **Continuar atualizando o DirSync neste computador.** Para fazer uma [implantação paralela](#parallel-deployment), você exportará as definições de configuração do DirSync e as moverá para o novo servidor.
5. Forneça a senha da conta que você usa atualmente para se conectar ao AD do Azure. Ela precisa ser a conta usada atualmente pelo DirSync. ![Insira suas credenciais de AD do Azure](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png) Se você encontrar um erro e tiver problemas de conectividade, confira [Solucionar problemas de conectividade](active-directory-aadconnect-troubleshoot-connectivity.md).
6. Forneça uma conta de administrador corporativo para o Active Directory. ![Inserir suas credenciais do ADDS](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)
7. Agora você está pronto para configurar. Ao clicar em **Atualizar**, o DirSync será desinstalado e o Azure AD Connect será configurado e iniciará a sincronização. ![Pronto para configurar](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. Após a conclusão da instalação, saia e entre novamente no Windows antes de usar o Synchronization Service Manager ou o Synchronization Rule Editor, ou tentar fazer outras alterações de configuração.

## Implantação paralela

### Exportar a configuração do DirSync
**Implantação paralela com mais de 50.000 objetos**

Se você tiver mais de 50.000 objetos, a instalação do Azure AD Connect recomendará uma implantação paralela.

Será exibida uma tela semelhante à seguinte:

![Análise concluída](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Se você quiser continuar com a implantação paralela, precisará executar as seguintes etapas:

- Clique no botão **Exportar configurações**. Quando você instala o Azure AD Connect em um servidor separado, essas configurações serão importadas para migrar as configurações de seu DirSync atual para a nova instalação do Azure AD Connect.

Após as configurações terem sido exportadas com êxito, você pode sair do Assistente de conexão do Azure AD Connect no servidor DirSync. Siga para a próxima etapa para [instalar o Azure AD Connect em um servidor separado](#installation-of-azure-ad-connect-on-separate-server)

**Implantação paralela com menos de 50.000 objetos**

Se você tiver menos de 50.000 objetos, mas ainda quiser fazer uma implantação paralela, faça o seguinte:

1. Execute o instalador do Azure AD Connect (MSI).
2. Quando você vir a tela **Bem-vindo ao Azure AD Connect**, saia do assistente de instalação clicando no “X” no canto superior direito da janela.
3. Abra um prompt de comando.
4. No local de instalação do Azure AD Connect (padrão: C:\\Arquivos de Programas\\Microsoft Azure Active Directory Connect), execute o seguinte comando: `AzureADConnect.exe /ForceExport`.
5. Clique no botão **Exportar configurações**. Quando você instala o Azure AD Connect em um servidor separado, essas configurações serão importadas para migrar as configurações de seu DirSync atual para a nova instalação do Azure AD Connect.

![Análise concluída](./media/active-directory-aadconnect-dirsync-upgrade-get-started/forceexport.png)

Após as configurações terem sido exportadas com êxito, você pode sair do Assistente de conexão do Azure AD Connect no servidor DirSync. Siga para a próxima etapa para [instalar o Azure AD Connect em um servidor separado](#installation-of-azure-ad-connect-on-separate-server)

### Instalar o Azure AD Connect em um servidor separado

Quando você instalar o Azure AD Connect em um novo servidor, ele irá considerar que você deseja executar uma instalação limpa do Azure AD Connect. Como você quer usar a configuração do DirSync, há algumas etapas adicionais a serem executadas:

1. Execute o instalador do Azure AD Connect (MSI).
2. Quando você vir a tela **Bem-vindo ao Azure AD Connect**, saia do assistente de instalação clicando no “X” no canto superior direito da janela.
3. Abra um prompt de comando.
4. No local de instalação do Azure AD Connect (padrão: C:\\Arquivos de Programas\\Microsoft Azure Active Directory Connect), execute o seguinte comando: `AzureADConnect.exe /migrate`. O assistente de instalação do Azure AD Connect é iniciado e exibe a seguinte tela: ![Insira suas credenciais de AD do Azure](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)
5. Selecione o arquivo de configurações exportado de sua instalação DirSync.
6. Configure as opções avançadas incluindo:
    - Um local de instalação personalizada para o Azure AD Connect.
	- Uma instância existente do SQL Server (padrão: o Azure AD Connect instala o SQL Server 2012 Express). Não use a mesma instância de banco de dados que o servidor DirSync.
	- Uma conta de serviço usada para conectar-se ao SQL Server (se o banco de dados do SQL Server for remoto, essa conta deverá ser uma conta de serviço de domínio). Essas opções podem ser vistas nesta tela: ![Insira suas credenciais de AD do Azure](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)
7. Clique em **Próximo**.
8. Na página **Pronto para configurar**, deixe a opção **Iniciar o processo de sincronização assim que a configuração for concluída** marcada. O servidor estará no [modo de preparo](active-directory-aadconnectsync-operations.md#staging-mode) para que as alterações não sejam exportadas para o AD do Azure no momento.
9. Clique em **Instalar**.
10. Após a conclusão da instalação, saia e entre novamente no Windows antes de usar o Synchronization Service Manager ou o Synchronization Rule Editor, ou tentar fazer outras alterações de configuração.


>[AZURE.NOTE] A sincronização entre o Active Directory do Windows Server e o Active Directory do Azure começará, mas nenhuma alteração será exportada para o AD do Azure. Apenas uma ferramenta de sincronização pode exportar ativamente alterações de cada vez. Isso é chamado de [modo de preparo](active-directory-aadconnectsync-operations.md#staging-mode).

### Verificar se o Azure AD Connect está pronto para iniciar a sincronização

Para determinar se o Azure AD Connect está pronto para assumir o controle do DirSync, você precisará abrir o **Gerenciador do Serviço de Sincronização** no grupo **Azure AD Connect** do menu Iniciar.

No aplicativo, você precisará exibir a guia **Operações**. Nessa guia, você procure confirmar se as operações a seguir foram concluídas:

- Importação no AD Connector
- Importação no Azure AD Connector
- Sincronização completa no AD Connector
- Sincronização completa no Azure AD Connector

![Importação e sincronização concluídas](./media/active-directory-aadconnect-dirsync-upgrade-get-started/importsynccompleted.png)

Examine o resultado dessas operações e certifique-se de que não há erros.

Se você deseja ver e inspecionar as alterações que estão prestes a serem exportadas para o AD do Azure, saiba como verificar a configuração em [modo de preparo](active-directory-aadconnectsync-operations.md#staging-mode). Faça alterações de configuração necessárias até que você não veja nada inesperado.

Após a conclusão dessas quatro operações, se não houver mais erros e se você estiver satisfeito com as alterações que estiverem prestes a serem exportadas, você estará pronto para desinstalar o DirSync e habilitar a sincronização do Azure AD Connect. Conclua as próximas duas etapas para concluir a migração.

### Desinstalar o DirSync (servidor antigo)

- Em **Programas e recursos**, localize a **Ferramenta de sincronização do Microsoft Azure Active Directory**
- Desinstale a **Ferramenta de sincronização do Microsoft Azure Active Directory**
- Observe que a desinstalação pode levar até 15 minutos para ser concluída.

Com o DirSync desinstalado, não há nenhum servidor ativo exportando para o AD do Azure. A próxima etapa deve ser concluída antes que qualquer alteração no seu Active Directory local continuará a ser sincronizado com o AD do Azure.

### Habilitar o Azure AD Connect (novo servidor)
Após a instalação, a reabertura do Azure AD Connect permitirá fazer alterações de configuração adicionais. Inicie o **Azure AD Connect** no menu iniciar ou em um atalho na área de trabalho. Não tente executar novamente a instalação MSI.

Você deve ver o seguinte:

![Tarefas adicionais](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

- Selecione **Configurar modo de preparo**.
- Desative o preparo desmarcando a caixa de seleção **Modo de preparo habilitado**.

![Insira suas credenciais de AD do Azure](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

- Clique no botão **Avançar**
- Na página de confirmação, clique no botão **Instalar**.

O Azure AD Connect agora é o servidor ativo.

## Próximas etapas
Agora que você tem o Azure AD Connect instalado, pode [verificar a instalação e atribuir licenças](active-directory-aadconnect-whats-next.md).

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0525_2016-->