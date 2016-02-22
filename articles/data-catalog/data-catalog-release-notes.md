<properties
   pageTitle="Notas de versão do Catálogo de Dados do Azure"
   description="Notas de versão para a versão de 28 de agosto de 2015 de visualização pública do Catálogo de Dados do Azure"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="02/08/2016"
   ms.author="maroche"/>

# Notas de versão do Catálogo de Dados do Azure

## Notas da versão de 20 de novembro de 2015 do Catálogo de Dados do Azure

### Abrindo fontes no Power BI Desktop

Ao usar a opção "Abrir no Power BI Desktop" no portal do **Catálogo de Dados do Azure**, os usuários podem encontrar um de dois problemas no aplicativo Power BI Desktop:

- Será exibida uma caixa de diálogo com o título "Não é possível abrir o documento"
- O Power BI Desktop abre, mas o arquivo parece estar vazio

Para cada situação, o problema pode ser resolvido ao baixar e instalar a versão mais recente do Power BI Desktop em [PowerBI.com](https://powerbi.com).

## Notas da versão de 13 de novembro de 2015 do Catálogo de Dados do Azure

### Registrando e conectando ao Teradata

Ao se conectar a fontes de dados Teradata, os usuários devem ter instalado o driver ODBC correto do Teradata que coincida com o número de bits (32 bits ou 64 bits) do software que está sendo usado.

A partir dessa data de lançamento do ADC, o [driver ODBC do Teradata para windows (versão 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) mais recente é compatível com o Office 2013, mas não com o Office 2016.

## Notas da versão de 13 de julho de 2015 do Catálogo de Dados do Azure

### Registrando e conectando-se ao Banco de Dados do Oracle

Ao conectar-se às fontes de dados do banco de dados Oracle, os usuários devem ter instalado os drivers corretos do Oracle que correspondem ao número de bits (32 bits ou 64 bits) do software que está sendo usado.

-	Ao registrar fontes de dados Oracle em um computador executando o Windows de 32 bits, os drivers do Oracle de 32 bits serão usados
-	Ao registrar fontes de dados Oracle em um computador executando o Windows de 64 bits, os drivers do Oracle de 64 bits serão usados
-	Ao conectar-se às fontes de dados Oracle usando o Excel em um computador executando a versão de 32 bits do Microsoft Office, inclusive no Windows de 64 bits, os drivers do Oracle de 32 bits serão usados
-	Ao conectar-se às fontes de dados Oracle usando o Excel em um computador executando a versão de 64 bits do Microsoft Office, os drivers do Oracle de 64 bits serão usados

### Registro e conexão ao SQL Server Reporting Services

O suporte para fontes de dados do SQL Server Reporting Services (SSRS) na versão de teste inicial do Catálogo de Dados do Azure está limitado somente aos servidores de modo nativo. O suporte para o SSRS no modo SharePoint será adicionado em uma versão posterior.

### Abrindo ativos de dados no Excel

Ao abrir ativos de dados no Microsoft Excel no portal do **Catálogo de Dados do Azure**, uma caixa de diálogo **Aviso de segurança do Microsoft Excel** pode ser exibida aos usuários. Esse é um comportamento padrão e esperado, e os usuários podem selecionar **Habilitar** para continuar.

Para obter mais informações, veja [Habilitar ou desabilitar alertas de segurança sobre links e arquivos de sites suspeitos](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### Colunas BLOB e UDT não encontradas nas visualizações

Ao registrar tabelas e exibições que contêm BLOB (objeto binário grande) e colunas de UDT (tipo de dados definido pelo usuário) e ao selecionar incluir visualização dos ativos de dados, essas colunas não serão incluídas na visualização.

### Registro da fonte de dados e configuração de proxy e política

Os usuários podem encontrar uma situação em que podem acessar o portal do Catálogo de Dados do Azure, mas quando tentam fazer logon na ferramenta de registro da fonte de dados encontram uma mensagem de erro que impede o logon.

Há duas causas possíveis para esse comportamento de problema:

**Causa 1: configuração dos Serviços de Federação do Active Directory** A ferramenta de registro de fonte de dados usa a Autenticação de formulários para validar logons de usuário no Active Directory. Para um logon bem-sucedido, a autenticação de formulários deve ser habilitada na Política de Autenticação Global por um administrador do Active Directory.

Em algumas situações, esse comportamento de erro pode ocorrer apenas quando o usuário está na rede da empresa, ou quando está se conectando de fora da rede da empresa. A Política de Autenticação Global permite que os métodos de autenticação sejam habilitados separadamente para conexões intranet e extranet. Erros de logon poderão ocorrer se a autenticação de formulários não estiver habilitada para a rede por meio da qual o usuário está se conectando.

Para obter mais informações, consulte [Configurando políticas de autenticação](https://technet.microsoft.com/library/dn486781.aspx).

**Causa 2: configuração de proxy da rede** Se a rede corporativa usar um servidor proxy, a ferramenta de registro não poderá se conectar ao Active Directory do Azure por meio do proxy. Os usuários podem garantir a ferramenta de registro editando o arquivo de configuração da ferramenta, adicionando esta seção ao arquivo:


	  <system.net>
	    <defaultProxy useDefaultCredentials="true" enabled="true">
	      <proxy usesystemdefault="True"
	                      proxyaddress="http://<your corporate network proxy url>"
	                      bypassonlocal="True"/>
	    </defaultProxy>
	  </system.net>


Para localizar o arquivo RegistrationTool.exe.config, inicie a ferramenta de registro e, em seguida, abra o utilitário Gerenciador de Tarefas do Windows. Na guia Detalhes do Gerenciador de tarefas, clique com o botão direito em RegistrationTool.exe e escolha Abrir local do arquivo no menu pop-up.

<!---HONumber=AcomDC_0211_2016-->