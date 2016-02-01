<properties 
   pageTitle="Criando um certificado autoassinado para configurações de Gateway de VPN entre locais de Ponto a Site usando makecert | Microsoft Azure"
   description="Este artigo contém etapas para usar o makecert para criar os certificados raiz autoassinados no Windows 10."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/15/2016"
   ms.author="cherylmc" />

# Trabalhando com certificados raiz autoassinados para configurações de Ponto para Site

As tarefas a seguir o ajudarão a criar um certificado raiz usando o makecert e gerar certificado do cliente do certificado raiz. As etapas a seguir são escritas para usar o makecert no Windows 10.

## Criar um certificado raiz autoassinado

Makecert é uma maneira de criar um certificado raiz autoassinado. As etapas abaixo o orientarão pela criação de um certificado raiz autoassinado usando o makecert. Essas etapas não são específicas do modelo de implantação. Elas são válidas tanto para o Gerenciador de Recursos quanto para o clássico.

### Para criar um certificado autoassinado

1. Em um computador com Windows 10, baixe e instale o [SDK (Windows Software Development Kit) para Windows 10](https://dev.windows.com/pt-BR/downloads/windows-10-sdk).

2. Após a instalação, localize o utilitário makecert.exe nesse caminho: C:\\Program Files (x86) \\Windows Kits\\10\\bin<arch>.
		
	Exemplo:
	
		C:\Program Files (x86)\Windows Kits\10\bin\x64\makecert.exe

3. Em seguida, crie e instale um certificado raiz no repositório de certificados Pessoal em seu computador. O exemplo abaixo cria um arquivo *.cer* correspondente que você carregará posteriormente. Execute o comando a seguir como administrador, no qual *RootCertificateName* é o nome que você deseja usar para o certificado.

	Observação: se você executar o exemplo a seguir sem alterações, o resultado será um certificado raiz e o arquivo *RootCertificateName.cer* correspondente. Você pode encontrar o arquivo .cer no diretório onde o comando foi executado. O certificado estará em seus Certificados - Usuário Atual\\Pessoal\\Certificados.

    	makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

	>[AZURE.NOTE]Como você criou um certificado raiz do qual serão gerados certificados de cliente, você pode querer exportar o certificado juntamente com a sua chave privada e salvá-lo em um local seguro onde possa ser recuperado.

## Gerar, exportar e instalar certificado do cliente

Você pode gerar um certificado do cliente com base em um certificado raiz autoassinado e, em seguida, a exportar e instalar o certificado do cliente. As etapas abaixo não são específicas do modelo de implantação. Elas são válidas tanto para o Gerenciador de Recursos quanto para o clássico.

### Para gerar um certificado do cliente com base em um certificado raiz autoassinado.

As etapas abaixo orientarão você sobre uma maneira de gerar um certificado de cliente a partir de um certificado autoassinado. Você pode gerar vários certificados de cliente a partir do mesmo certificado raiz. Cada certificado de cliente pode ser exportado e instalado no computador cliente.

1. No mesmo computador usado para criar o certificado raiz autoassinado, abra um prompt de comando como administrador.

2. Altere o diretório para o local no qual você deseja salvar o arquivo de certificado de cliente. *RootCertificateName* refere-se ao certificado raiz autoassinado que você gerou. Se você executar o exemplo a seguir (alterando o RootCertificateName para o nome do seu certificado raiz), o resultado será um certificado de cliente chamado "ClientCertificateName" no seu repositório de certificados Pessoais.

3. Digite o seguinte comando:

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Todos os certificados são armazenados no repositório Certificado - Usuário Atual\\Pessoal\\Certificados do computador. Você pode gerar quantos certificados de cliente forem necessários com base neste procedimento.

### Para exportar um certificado do cliente

1. Para exportar um certificado do cliente, use *certmgr.msc*. Clique com o botão direito do mouse no certificado de cliente que você deseja exportar, clique em **todas as tarefas** e clique em **exportar**. Isso abrirá o Assistente de Exportação de Certificado.

2. No Assistente, clique em **Avançar**, escolha **Sim, exportar a chave privada** e clique em **Avançar**.

3. Na página **Exportar Formato de Arquivo**, você pode deixar o padrão selecionado. Em seguida, clique em **Próximo**.
 
4. Na página **Segurança**, você deve proteger a chave privada. Se você optar por usar uma senha, não deixe de anotar ou lembrar da senha definida para esse certificado. Em seguida, clique em **Avançar**.

5. Em **Arquivo a ser Exportado**, **Procure** o local para o qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Próximo**.

6. Clique em **Concluir** para exportar o certificado.

### Para instalar um certificado do cliente

Cada cliente que você deseja conectar à sua rede virtual usando uma conexão Ponto a Site deve ter um certificado do cliente instalado. Esse certificado está além do pacote de configuração de VPN necessário. As etapas a seguir guiarão você pela instalação manual do certificado do cliente.

1. Localize e copie o arquivo *.pfx* no computador cliente. No computador cliente, clique duas vezes no arquivo *.pfx* para instalá-lo. Deixe **Local do Repositório** como **Usuário Atual** e clique em **Avançar**.

2. Na página **Arquivo** a importar, não faça nenhuma alteração. Clique em **Próximo**.

3. Na página **Proteção da chave privada**, insira a senha do certificado, caso tenha usado uma, ou verifique se a entidade de segurança que está instalando o certificado está correta e clique em **Avançar**.

4. Na página **Repositório de Certificados**, deixe o local padrão e, em seguida, clique em **Avançar**.

5. Clique em **Concluir**. No **Aviso de Segurança** da instalação do certificado, clique em **Sim**. O certificado foi importado com êxito.

## Próximas etapas

Continue com a configuração de Ponto a Site.

- Para as etapas do modelo de implantação do **Gerenciador de Recursos**, veja [Configurar uma conexão Ponto a Site com uma rede virtual usando o PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md). 
- Para as etapas do modelo de implantação **clássico**, veja [Configurar uma conexão VPN Ponto a Site para uma Rede Virtual](vpn-gateway-point-to-site-create.md).

<!---HONumber=AcomDC_0121_2016-->