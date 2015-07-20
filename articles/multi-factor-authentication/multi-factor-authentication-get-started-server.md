<properties 
	pageTitle="Introdução ao Servidor Azure Multi-Factor Authentication" 
	description="Esta é a página de autenticação do Azure Multi-Factor Authentication que descreve como começar com o Servidor Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Introdução ao Servidor Azure Multi-Factor Authentication




<center>![Cloud](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Agora que determinamos se utilizar autenticação multifator local, vamos continuar. Esta página aborda uma nova instalação do servidor e sua configuração com o Active Directory local. Se você já tiver o servidor Phonefactor instalado e quiser saber como fazer isso, consulte Atualizando para o Servidor Azure Multi-Factor ou, se estiver procurando informações sobre como instalar apenas o serviço Web, consulte Implantando o Serviço Web do Aplicativo Móvel do Azure Multi-Factor Authentication.



## Baixar o Servidor Azure Multi-Factor Authentication

Há duas maneiras diferentes de baixar o Servidor Azure Multi-Factor Authentication. A primeira é entrando no portal do Azure e a segunda maneira é diretamente em [https://pfweb.phonefactor.net](https://pfweb.phonefactor.net).


### Para baixar o Servidor Azure Multi-Factor Authentication
--------------------------------------------------------------------------------

1. Entre no Portal do Azure como Administrador.
2. Selecione Active Directory à esquerda.
3. Na parte superior da página do Active Directory, clique em **Provedores de autenticação multifator**
4. Na parte inferior, clique em **Gerenciar**
5. Clique em **Downloads**
6. Acima de **Gerar Credenciais de Ativação**, clique em **Baixar**
7. Salve o download.

### Para baixar o Servidor Azure Multi-Factor Authentication diretamente
--------------------------------------------------------------------------------

1. Entre em [https://pfweb.phonefactor.net](https://pfweb.phonefactor.net).
2. Clique em **Downloads**
<center>![Cloud](./media/multi-factor-authentication-get-started-server/download2.png)</center>
3. Acima de **Gerar Credenciais de Ativação**, clique em **Baixar**. Deixe esta página aberta, porque você voltará a ela.
4. Salve o download.



## Instalar e configurar o Servidor Azure Multi-Factor Authentication
Agora que já baixou o servidor, você pode instalá-lo e configurá-lo. Certifique-se de que o servidor em que você está instalando atenda aos seguintes requisitos:



Requisitos do Servidor Azure Multi-Factor Authentication|Descrição|
:------------- | :------------- | 
Hardware|<li>200 MB de espaço em disco</li><li>processador com capacidade de x32 ou x64</li><li>1 GB de RAM ou mais</li>
Software|<li>Windows Server 2003 ou posterior, se o host for um sistema operacional de servidor</li><li>Windows Vista ou posterior, se o host for um sistema operacional cliente</li><li>Microsoft .NET 2.0 Framework</li><li>IIS 6.0 ou superior se estiver instalando o portal do usuário ou o SDK de serviço Web</li>

### Requisitos de firewall do Servidor Azure Multi-Factor Authentication
--------------------------------------------------------------------------------
Cada servidor MFA deve ser capaz de se comunicar na porta 443 de saída para o seguinte:

- https://pfd.phonefactor.net
- https://pfd2.phonefactor.net
- https://css.phonefactor.net

Se os firewalls de saída forem restritos na porta 443, os seguintes intervalos de endereços IP precisarão ser abertos:

Subrede de IP|Máscara de rede|Intervalo IP
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128|134.170.116.1 – 134.170.116.126
134.170.165.0/25|255.255.255.128|134.170.165.1 – 134.170.165.126
70.37.154.128/25|255.255.255.128|70.37.154.129 – 70.37.154.254

Se você não estiver usando recursos de Confirmação de Eventos do Azure Multi-Factor Authentication e se os usuários não estiverem se autenticando com os aplicativos móveis de autenticação multifator em dispositivos na rede corporativa, os intervalos de IP podem ser reduzidos ao seguinte:


Subrede de IP|Máscara de rede|Intervalo IP
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72 – 134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72 – 134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201 – 70.37.154.206


### Para instalar e configurar o Servidor Azure Multi-Factor Authentication
--------------------------------------------------------------------------------


1. Clique duas vezes no arquivo executável. Isso iniciará a instalação.
2. Na tela Selecionar Pasta de Instalação, certifique-se de que a pasta esteja correta e clique em Avançar.
3. Quando a instalação for concluída, clique em Concluir. Isso abre o assistente de configuração.
4. Na tela de boas-vindas do Assistente de Configuração, marque **Ignorar o uso do Assistente de configuração de autenticação** e clique em **Avançar**. Isso fecha o assistente e inicia o servidor.
<center>![Cloud](./media/multi-factor-authentication-get-started-server/skip2.png)</center>

5. De volta à página de onde baixamos o servidor, clique no botão **Gerar Credenciais de Ativação**. Copie essas informações no Servidor Azure MFA nas caixas fornecidas e clique em **Ativar**.


As etapas acima mostram uma instalação expressa com o assistente de configuração. Você pode executar novamente o assistente de autenticação, selecionando-o no menu Ferramentas no servidor.



##Importar usuários do Active Directory

Agora que o servidor está instalado e configurado, você pode importar usuários rapidamente para o Servidor Azure MFA.

### Para importar usuários do Active Directory
--------------------------------------------------------------------------------


1. No Servidor Azure MFA, à esquerda, selecione **Usuários**.
2. Na parte inferior, selecione **Importar do Active Directory**.
3. Agora, você pode procurar por usuários individuais ou buscar no diretório do AD UOs que tenham usuários. Nesse caso, nós especificaremos o UO de usuários.
4. Realce todos os usuários à direita e clique em **Importar**. Você deve receber uma mensagem informando que obteve êxito. Feche a janela de importação.

<center>![Cloud](./media/multi-factor-authentication-get-started-server/import2.png)</center>

<!---HONumber=July15_HO2-->