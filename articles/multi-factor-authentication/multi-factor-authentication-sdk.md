<properties 
	pageTitle="Integrando suas identidades locais ao Active Directory do Azure." 
	description="Este é o Azure AD Connect que descreve o que ele é e por que usá-lo." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>

# Criando uma autenticação multifator em aplicativos personalizados (SDK)

O SDK (Software Development Kit) do Azure Multi-Factor Authentication permite que você crie verificação de chamada telefônica e mensagem de texto diretamente nos processos de transação ou entrada de aplicativos no locatário do AD do Azure.

O SDK do Multi-Factor Authentication está disponível para C#, Visual Basic (.NET), Java, Perl, PHP e Ruby. O SDK fornece um thin wrapper em torno da autenticação multifator. Ele inclui tudo o que você precisa para escrever seu código, incluindo arquivos de código-fonte comentados, arquivos de exemplo e um arquivo Leiame detalhado. Cada SDK também inclui um certificado e a chave privada para criptografar transações, que é exclusiva do seu Provedor Multi-Factor Authentication. Desde que você tenha um provedor, é possível baixar o SDK em quantos formatos e idiomas precisar.

A estrutura das APIs no SDK do Multi-Factor Authentication é bastante simples. Você faz uma única chamada de função a uma API com os parâmetros de opção de multifator, como o modo de verificação, e dados do usuário, como o número de telefone para o qual ligar ou o número PIN a ser validado. As APIs convertem a chamada de função em solicitações de serviço Web para o Serviço Azure Multi-Factor Authentication baseado na nuvem. Todas as chamadas devem incluir uma referência ao certificado privado que é incluído em cada SDK.

Como as APIs não têm acesso aos usuários registrados no Active Directory do Azure, você deve fornecer informações do usuário, como números de telefone e códigos PIN, em um arquivo ou banco de dados. Além disso, as APIs não fornecem recursos de gerenciamento de usuários e registros, de modo que você precisa criar esses processos em seu aplicativo.






## Baixar o SDK do Azure Multi-Factor Authentication 

Há duas maneiras diferentes de baixar o SDK do Azure Multi-Factor Authentication. Ambas são feitas por meio do portal do Azure. A primeira é por meio do gerenciamento do Provedor de Autenticação Multifator diretamente. A segunda é através de configurações de serviço. A segunda opção requer um Provedor do Multi-Factor Authentication ou uma licença do Azure MFA, do Azure AD Premium ou do Enterprise Mobility Suite.


### Para baixar o SDK do Azure Multi-Factor Authentication no Portal do Azure


1. Entre no Portal do Azure como Administrador.
2. Selecione Active Directory à esquerda.
3. Na parte superior da página do Active Directory, clique em **Provedores de autenticação multifator**
4. Na parte inferior, clique em **Gerenciar**
5. Isso abrirá uma nova página. À esquerda, na parte inferior, clique em SDK.
<center>![Download](./media/multi-factor-authentication-sdk/download.png)</center>
6. Selecione o idioma desejado e clique em um dos links de download associados.
7. Salve o download.



### Para baixar o SDK do Azure Multi-Factor Authentication por meio das configurações de serviço


1. Entre no Portal do Azure como Administrador.
2. À esquerda, selecione Active Directory.
3. Clique duas vezes em sua instância do AD do Azure.
4. Na parte superior, clique em **Configurar**.
5. Em autenticação multifator, selecione **Gerenciar configurações de serviço**![Baixar](./media/multi-factor-authentication-sdk/download2.png)
6. Na página das configurações de serviços, na parte inferior da tela, clique em **Ir para o portal**.![Baixar](./media/multi-factor-authentication-sdk/download3a.png)
7. Isso abrirá uma nova página. À esquerda, na parte inferior, clique em SDK.
8. Selecione o idioma desejado e clique em um dos links de download associados.
9. Salve o download.

## Conteúdo do SDK do Azure Multi-Factor Authentication
Dentro do SDK, você encontrará os seguintes itens:

- **LEIAME**. Explica como usar as APIs do Multi-Factor Authentication em um aplicativo novo ou existente.
- **Arquivo(s) de origem** para Multi-Factor Authentication
- **Certificado do cliente** que você usa para se comunicar com o serviço Multi-Factor Authentication
- **Chave privada** do certificado
- **Resultados da chamada.** Uma lista de códigos do resultado da chamada. Para abrir esse arquivo, use um aplicativo com formatação de texto, como o WordPad. Use os códigos do resultado da chamada para testar e solucionar problemas de implementação do Multi-Factor Authentication no aplicativo. Eles não são códigos de status de autenticação.
- **Exemplos.** Código de exemplo para uma implementação básica de trabalho do Multi-Factor Authentication.


>[AZURE.WARNING]O certificado do cliente é um certificado privado único que foi gerado especialmente para você. Não compartilhe nem perca este arquivo. Ele é sua chave para garantir a segurança da comunicação com o serviço Multi-Factor Authentication.

## Exemplo de código: verificação de telefone no modo padrão

Este exemplo de código mostra como usar as APIs no SDK do Azure Multi-Factor Authentication para adicionar verificação de chamada de voz no modo padrão ao seu aplicativo. O modo padrão é uma chamada telefônica à qual o usuário responde pressionando a tecla #.

Este exemplo usa o SDK do Multi-Factor Authentication .NET 2.0 do C# em um aplicativo básico ASP.NET com a lógica do servidor C#, mas o processo é bastante semelhante para implementações simples em outras linguagens. Como o SDK inclui arquivos de origem, e não arquivos executáveis, você pode criar os arquivos e fazer referência a eles ou incluí-los diretamente no aplicativo.

>[AZURE.NOTE]Ao implementar o Multi-Factor Authentication, use os fatores adicionais como verificação secundária ou terciária para complementar seu método de autenticação primária. Esses métodos não foram desenvolvidos para serem usados como métodos de autenticação primária.

### Visão geral do exemplo de código
Este código de exemplo de um aplicativo de demonstração na Web bastante simples usa uma chamada telefônica com uma resposta pela tecla # para concluir a autenticação do usuário. Esse fator de chamada telefônica é conhecido no Multi-Factor Authentication como modo padrão.

O código do lado do cliente não inclui nenhum elemento específico do Multi-Factor Authentication. Como os fatores de autenticação adicionais são independentes da autenticação primária, você pode adicioná-los sem alterar a interface de logon existente. As APIs no SDK do Multi-Factor permitem personalizar a experiência do usuário, mas talvez você não precise alterar nada.

O código do servidor adiciona a autenticação no modo padrão na Etapa 2. Ele cria um objeto PfAuthParams com os parâmetros que são necessários para a verificação no modo padrão: nome do usuário, número de telefone, modo e o caminho para o certificado do cliente (CertFilePath), que é necessário em cada chamada. Para uma demonstração de todos os parâmetros no PfAuthParams, consulte o arquivo de exemplo no SDK.

Em seguida, o código passa do objeto PfAuthParams para a função pf\_authenticate(). O valor de retorno indica o êxito ou a falha da autenticação. Os parâmetros de saída, callStatus e errorID, contêm informações adicionais do resultado da chamada. Os códigos do resultado da chamada são documentados no arquivo de resultados da chamada no SDK.

Essa implementação mínima pode ser escrita apenas em algumas linhas. No entanto, no código de produção, você inclui tratamento de erro mais sofisticado, código adicional de banco de dados e uma experiência de usuário aprimorada.

### Código do cliente Web

Veja a seguir o código do cliente Web para uma página de demonstração.

	
	<%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %>
	
	<!DOCTYPE html>
	
	<html xmlns="http://www.w3.org/1999/xhtml">
	<head runat="server">
	<title>Multi-Factor Authentication Demo</title>
	</head>
	<body>
	<h1>Azure Multi-Factor Authentication Demo</h1>
	<form id="form1" runat="server">
	
	<div style="width:auto; float:left">
	Username:&nbsp;<br />
	Password:&nbsp;<br />
	</div>
	
	<div">
	<asp:TextBox id="username" runat="server" width="100px"/><br />
	<asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
	</div>
	
	<asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>
	
	<p><asp:Label ID="lblResult" runat="server"></asp:Label></p>
	
	</form>
	</body>
	</html>


### Código do servidor

No código de servidor a seguir, o Multi-Factor Authentication é configurado e executado na Etapa 2. O modo padrão (MODE\_STANDARD) é uma chamada telefônica à qual o usuário responde pressionando a tecla #.

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.UI;
	using System.Web.UI.WebControls;
	
	public partial class _Default : System.Web.UI.Page
	{
	    protected void Page_Load(object sender, EventArgs e)
	    {
	    }
	
	    protected void btnSubmit_Click(object sender, EventArgs e)
	    {
	        // Step 1: Validate the username and password
	        if (username.Text != "Contoso" || password.Text != "password")
	        {
	            lblResult.ForeColor = System.Drawing.Color.Red;
	            lblResult.Text = "Username or password incorrect.";
	        }
	        else
	        {
	            // Step 2: Perform multi-factor authentication
	
	            // Add call details from the user database.
	            PfAuthParams pfAuthParams = new PfAuthParams();
	            pfAuthParams.Username = username.Text;
	            pfAuthParams.Phone = "9134884271";
	            pfAuthParams.Mode = pf_auth.MODE_STANDARD;
	            
	            // Specify a client certificate 
	            // NOTE: This file contains the private key for the client
	            // certificate. It must be stored with appropriate file 
	            // permissions.
	            pfAuthParams.CertFilePath = "c:\\cert_key.p12";
	
	            // Perform phone-based authentication
	            int callStatus;
	            int errorId;
	
	            if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
	            {
	                lblResult.ForeColor = System.Drawing.Color.Green;
	                lblResult.Text = "Multi-Factor Authentication succeeded.";
	            }
	            else
	            {
	                lblResult.ForeColor = System.Drawing.Color.Red;
	                lblResult.Text = " Multi-Factor Authentication failed.";
	            }
	        }
	
	    }
	}

<!---HONumber=AcomDC_0218_2016-->