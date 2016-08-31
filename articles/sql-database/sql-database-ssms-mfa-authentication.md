<properties
   pageTitle="Suporte do SSMS para MFA do Azure AD com o Banco de Dados SQL e o SQL Data Warehouse | Microsoft Azure"
   description="Use o Multi-Factor Authentication com SSMS para Banco de Dados SQL e SQL Data Warehouse."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/15/2016"
   ms.author="rick.byham@microsoft.com"/>

# Suporte do SSMS para MFA do Azure AD com o Banco de Dados SQL e o SQL Data Warehouse

O Banco de Dados SQL do Azure e o SQL Data Warehouse do Azure agora dão suporte a conexões do SSMS (SQL Server Management Studio) usando a *Autenticação Universal do Active Directory*. A Autenticação Universal do Active Directory é um fluxo de trabalho interativo que dá suporte ao *Azure MFA (Multi-Factor Authentication)*. O Azure MFA ajuda a proteger o acesso a dados e aplicativos, ao mesmo tempo que atende à demanda dos usuários por um processo de entrada simples. Ele fornece autenticação eficiente com uma variedade de opções de verificação fáceis, como chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação por aplicativos móveis, os quais permitem que os usuários escolham seu método de preferência. Para encontrar uma descrição do Multi-Factor Authentication, consulte [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).

O SSMS agora dá suporte:

- MFA interativo com o Azure AD com o potencial para validação de caixa de diálogo pop-up.
- Métodos de Autenticação Integrada do Active Directory e Senha do Active Directory não interativos que podem ser usados em muitos aplicativos diferentes (ADO.NET, JDBC, ODBC etc.). Esses dois métodos nunca resultam em caixas de diálogo pop-up.

Quando a conta de usuário é configurada para MFA, o fluxo de trabalho de autenticação interativa requer interação adicional do usuário por meio de caixas de diálogo pop-up, o uso de cartão inteligente etc. Quando a conta de usuário é configurada para MFA, o usuário deve selecionar a Autenticação Universal do Azure para se conectar. Se a conta de usuário não exigir MFA, o usuário ainda poderá usar as outras duas opções de Autenticação do Azure Active Directory.

## Limitações da Autenticação Universal para o Banco de Dados SQL e SQL Data Warehouse

- O SSMS é a única ferramenta atualmente habilitada para MFA por meio da Autenticação Universal do Active Directory.
- Apenas uma única conta do Azure Active Directory pode fazer logon para uma instância do SSMS usando a Autenticação Universal. Para fazer logon como outra conta do Azure AD, você deve usar outra instância do SSMS. (Essa restrição é limitada à Autenticação Universal do Active Directory, você pode fazer logon em diferentes servidores usando a Autenticação de Senha do Active Directory, a Autenticação Integrada do Active Directory ou a Autenticação do SQL Server).
- O SSMS dá suporte à Autenticação Universal do Active Directory para a visualização do Pesquisador de Objetos, do Editor de Consultas e do Repositório de Consultas.
- O DacFx nem o Designer de Esquema dão suporte à Autenticação Universal.
- Não há suporte para contas MSA para a Autenticação Universal do Active Directory.
- Não há suporte para a Autenticação Universal do Active Directory no SSMS para usuários que são importados para o Active Directory atual de outros Active Directories.
- Não há nenhum requisito de software adicional para a Autenticação Universal do Active Directory, exceto que você deve usar uma versão do SSMS com suporte.

## Etapas da configuração

A implementação do Multi-Factor Authentication requer quatro etapas básicas.

1. **Configurar um Azure Active Directory**: para saber mais, confira [Integração de suas identidades locais com o Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Adicionar seu próprio nome de domínio ao Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [O Microsoft Azure agora dá suporte à federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administrando seu diretório Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx) e [Gerenciar o Azure AD usando o Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

2. **Configurar MFA**: para obter instruções passo a passo, consulte [Configurando o Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-whats-next.md).

3. **Configurar Banco de Dados SQL ou o SQL Data Warehouse para autenticação do Azure AD**: para obter instruções passo a passo, consulte [Conexão ao Banco de Dados SQL ou ao SQL Data Warehouse usando a autenticação do Azure Active Directory](sql-database-aad-authentication.md).

4. **Baixar o SSMS**: no computador cliente, baixe o SSMS mais recente (pelo menos de agosto de 2016) de [Baixar o SSMS (SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx).

## Conectando-se usando a Autenticação Universal com o SSMS

As etapas a seguir mostram como se conectar ao Banco de Dados SQL ou ao SQL Data Warehouse usando o SSMS mais recente.

1. Para se conectar usando a Autenticação Universal, na caixa de diálogo **Conectar ao Servidor**, selecione **Active Directory Universal Authentication (Autenticação Universal do Active Directory)**. ![1mfa-universal-connect][1]

2. Como de costume para o Banco de Dados SQL e o SQL Data Warehouse, você deve clicar em **Opções** e especificar o banco de dados na caixa de diálogo **Opções**. E clique em **Conectar**.
3. Quando a caixa de diálogo **Conectar-se à sua conta** aparecer, forneça a conta e a senha de sua identidade do Azure Active Directory. ![2mfa-sign-in][2]

    > [AZURE.NOTE] Para a Autenticação Universal com uma conta que não exige o MFA, você se conecta neste momento. Para usuários que necessitam de MFA, continue com as etapas a seguir.
 
4. Devem aparecer duas caixas de diálogo de configuração de MFA. Essa operação única depende da configuração de administrador de MFA e, portanto, pode ser opcional. Para um domínio habilitado para MFA, essa etapa às vezes é predefinida (por exemplo, o domínio requer que os usuários usem um cartão inteligente e um PIN). ![3mfa-setup][3]

5. A segunda caixa de diálogo única possível permite que você selecione os detalhes do seu método de autenticação. As possíveis opções são configuradas pelo administrador. ![4mfa-verify-1][4]
 
6. O Azure Active Directory envia as informações de confirmação para você. Quando receber o código de verificação, insira-o na caixa **Digite o código de verificação** e clique em **Entrar**. ![5mfa-verify-2][5]

Quando a verificação for concluída, o SSMS se conectará normalmente considerando as credenciais válidas e o acesso ao firewall.

##Próximas etapas  

Conceder aos outros o acesso ao seu banco de dados: [Autenticação e Autorização do Banco de Dados SQL: Concessão de Acesso](sql-database-manage-logins.md) Certificar-se de que outras pessoas possam se conectar por meio do firewall: [Configurar uma regra de firewall no nível de servidor do Banco de Dados SQL do Azure usando o Portal do Azure](sql-database-configure-firewall-settings.md)


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

<!---HONumber=AcomDC_0817_2016-->