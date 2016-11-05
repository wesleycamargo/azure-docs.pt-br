---
title: Criar uma VM que executa o MySQL | Microsoft Docs
description: Crie uma máquina virtual do Azure que executa o Windows Server 2012 R2 e o banco de dados MySQL usando o modelo de implantação clássica.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: cynthn

---
# Instalar o MySQL em uma máquina virtual criada com o modelo de implantação clássico que executa o Windows Server 2012 R2
[MySQL](http://www.mysql.com) é um banco de dados SQL fonte aberto popular. Este tutorial mostra como instalar e executar a versão de comunidade do MySQL 5.6.23 como um MySQL Server em uma máquina virtual que executa o Windows Server 2012 R2. Para obter instruções sobre como instalar o MySQL no Linux, consulte: [Como instalar o MySQL no Azure](virtual-machines-linux-mysql-install.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## Criar uma máquina virtual que executa Windows Server 2012 R2
Se você ainda não tiver uma VM que executa o Windows Server 2012 R2, você pode usar este [tutorial](virtual-machines-windows-classic-tutorial.md) para criar a máquina virtual.

## Anexar um disco de dados
Depois de criada a máquina virtual, você pode, opcionalmente, anexar um disco de dados adicional. Isso é recomendado para cargas de trabalho de produção e para evitar a falta de espaço na unidade do sistema operacional (C:), que inclui o sistema operacional.

Veja [Como anexar um disco de dados a uma máquina virtual do Windows](virtual-machines-windows-classic-attach-disk.md) e siga as instruções para anexar um disco vazio. Defina a configuração de cache de host como **Nenhum** ou **Somente leitura**.

## Faça logon na máquina virtual
A seguir, você [fará logon na máquina virtual](virtual-machines-windows-classic-connect-logon.md) para poder instalar o MySQL.

## Instalar e executar MySQL Community Server na máquina virtual
Siga estas etapas para instalar, configurar e executar a versão de comunidade do MySQL Server:

> [!NOTE]
> Estas etapas são para a versão Community 5.6.23.0 do MySQL e Windows Server 2012 R2. Sua experiência pode ser diferente em versões diferentes de servidor MySQL ou de servidor Windows.
> 
> 

1. Depois de se conectar à máquina virtual usando a Área de Trabalho Remota, clique em **Internet Explorer** no menu Iniciar.
2. Selecione o botão **Ferramentas** no canto superior direito (o ícone de engrenagem) e clique em **Opções da Internet**. Clique na guia **Segurança**, clique no ícone **Sites Confiáveis** e, em seguida, no botão **Sites**. Adicione http://*.mysql.com à lista de sites confiáveis. Clique em **Fechar** e, em seguida, em **OK**.
3. Na barra de endereços do Internet Explorer, digite http://dev.mysql.com/downloads/mysql/.
4. Use o site do MySQL para localizar e baixar a versão mais recente do instalador do MySQL para Windows. Ao escolher o Instalador do MySQL, baixe a versão que tem o conjunto completo de arquivos (por exemplo, o mysql-installer-community-5.6.23.0.msi, com um tamanho do arquivo de 282,4 MB) e salve o instalador.
5. Quando tiver terminado de baixar o instalador, clique em **Executar** para iniciar a instalação.
6. Na página **Contrato de Licença**, aceite o contrato de licença e clique em **Avançar**.
7. Na página **Escolhendo um Tipo de Instalação**, clique no tipo de instalação desejada e clique em **Avançar**. As etapas a seguir pressupõem a seleção do tipo de instalação **Somente servidor**.
8. Na página **Instalação**, clique em **Executar**. Quando a instalação for concluída, clique em **Avançar**.
9. Na página **Configuração do Produto**, clique em **Avançar**.
10. Na página **Tipo e Rede**, especifique o tipo de configuração desejada e as opções de conectividade, inclusive a porta TCP, se necessário. Selecione **Mostrar Opções Avançadas** e clique em **Próximo**.
    
    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_TypeNetworking.png)
11. Na página **Contas e Funções**, especifique uma senha forte de raiz de MySQL. Adicione mais contas de usuário do MySQL, conforme a necessidade, e clique em **Avançar**.
    
    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AccountsRoles_Filled.png)
12. Na página **Serviço Windows**, especifique as alterações a serem aplicadas às configurações padrão para executar o servidor MySQL como um serviço Windows, conforme a necessidade, e clique em **Avançar**.
    
    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_WindowsService.png)
13. Na página **Opções Avançadas**, especifique as alterações a serem aplicadas às opções de logon, conforme a necessidade, e clique em **Avançar**.
    
    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AdvOptions.png)
14. Na página **Aplicar a Configuração de Servidor**, clique em **Executar**. Quando as etapas de configuração forem concluídas, clique em **Concluir**.
15. Na página **Configuração do Produto**, clique em **Avançar**.
16. Na página **Instalação Concluída**, clique em **Copiar Log para a Área de Transferência**, se quiser examiná-lo mais tarde, e clique em **Concluir**.
17. Na tela inicial, digite **mysql** e clique em **Cliente de Linha de Comando do MySQL 5.6**.
18. Insira a senha raiz que você especificou na etapa 11 e você verá um prompt onde você pode emitir comandos para configurar o MySQL. Para saber mais sobre os comandos e a sintaxe, confira [Manuais de referência do MySQL](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).
    
    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_CommandPrompt.png)
19. Você também pode configurar definições padrão de configuração do servidor, como os diretórios e unidades básicos e de dados, com entradas no arquivo C:\\Arquivos de Programas (x86)\\MySQL\\MySQL Server 5.6\\my-default.ini. Para obter mais informações, confira [5\.1.2 Padrões de configuração do servidor](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

## Configurar pontos de extremidade
Se quiser que o serviço do servidor MySQL esteja disponível para computadores cliente MySQL na Internet, você deverá configurar um ponto de extremidade para a porta TCP na qual o serviço MySQL Server escutará e criará uma regra adicional do Firewall do Windows. É a porta TCP 3306, a menos que você tenha especificado uma porta diferente na página **Tipo e Rede** (etapa 10 do procedimento anterior).

> [!NOTE]
> Você deve considerar cuidadosamente as implicações de segurança de fazer isso, porque isso disponibilizará o serviço MySQL Server para todos os computadores na Internet. Você pode definir o conjunto de endereços IP de origem que têm permissão para usar o ponto de extremidade com uma lista de controle de acesso (ACL). Para obter mais informações, confira [Como configurar pontos de extremidade para uma Máquina Virtual](virtual-machines-windows-classic-setup-endpoints.md)
> 
> 

Para configurar um ponto de extremidade para o serviço do servidor MySQL:

1. No portal clássico do Azure, clique em **Máquinas Virtuais**, clique no nome de sua máquina virtual MySQL e clique em **Pontos de Extremidade**.
2. Na barra de comandos, clique em **Adicionar**.
3. Na página **Adicionar um ponto de extremidade a uma máquina virtual**, clique na seta para a direita.
4. Se você estiver usando a porta TCP 3306 padrão do MySQL, clique em **MySQL** em **Nome** e clique na marca de seleção.
5. Se você estiver usando uma porta TCP diferente, digite um nome exclusivo em **Nome**. Selecione **TCP** no protocolo, digite o número da porta em **Porta Pública** e em **Porta Privada** e clique na marca de seleção.

## Adicionar uma regra do Firewall do Windows para permitir o tráfego do MySQL
Para adicionar uma regra do Firewall do Windows que permite o tráfego MySQL da Internet, execute o comando a seguir em um prompt de comando elevado do Windows PowerShell na máquina virtual do servidor MySQL.

    New-NetFirewallRule -DisplayName "MySQL56" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public



## Testar sua conexão remota
Para testar sua conexão remota com o serviço do servidor MySQL em execução na máquina virtual do Azure, primeiro você deve determinar o nome DNS correspondente ao serviço de nuvem que contém a máquina virtual que executa o servidor MySQL.

1. No portal clássico do Azure, clique em **Máquinas Virtuais**, clique no nome de sua máquina virtual de servidor MySQL e clique em **Painel**.
2. No painel da máquina virtual, observe o valor de **Nome DNS** na seção **Visão Rápida**. Veja um exemplo:
   
   ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_DNSName.png)
3. De um computador local executando o MySQL ou o cliente MySQL, execute o comando a seguir para fazer logon como um usuário do MySQL:
   
     mysql -u <yourMysqlUsername> -p -h <yourDNSname>
   
   Por exemplo, para o nome de usuário do MySQL dbadmin3 e nome de DNS testmysql.cloudapp.net para a máquina virtual, use o comando a seguir.
   
     mysql -u dbadmin3 -p -h testmysql.cloudapp.net

## Próximas etapas
Para saber mais sobre como executar o MySQL, consulte a [Documentação do MySQL](http://dev.mysql.com/doc/).

<!---HONumber=AcomDC_0727_2016-->