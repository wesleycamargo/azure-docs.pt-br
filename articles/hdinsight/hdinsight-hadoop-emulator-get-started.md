<properties
	pageTitle="Usar uma área restrita do Hadoop para saber mais sobre o Hadoop | Microsoft Azure"
	description="Para começar a aprender a usar o ecossistema Hadoop, você pode definir uma área restrita do Hadoop da Hortonworks em uma máquina virtual do Azure. "
	keywords="emulador do hadoop,área restrita do hadoop"
	editor="cgronlun"
	manager="jhubbard"
	services="hdinsight"
	authors="nitinme"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="nitinme"/>

# Introdução ao ecossistema Hadoop com uma área restrita do Hadoop em uma máquina virtual

Saiba como instalar uma área restrita do Hadoop da Hortonworks em uma máquina virtual para saber mais sobre o ecossistema do Hadoop. A área restrita fornece um ambiente de desenvolvimento local para saber mais sobre o Hadoop, o HDFS (Sistema de Arquivos Distribuído Hadoop) e o envio de trabalhos.

## Pré-requisitos

* [Oracle VirtualBox](https://www.virtualbox.org/)

Quando estiver familiarizado com o Hadoop, você poderá começar a usar o Hadoop no Azure, criando um cluster do HDInsight. Para saber mais sobre como começar, confira [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

## Baixar e instalar a máquina virtual

1. Em [http://hortonworks.com/downloads/#sandbox](http://hortonworks.com/downloads/#sandbox), selecione o item __DOWNLOAD PARA VIRTUALBOX__ para HDP 2.4 na Hortonworks Sandbox. Você precisará registrar-se na Hortonworks antes de iniciar o download.

    ![Imagem de link para download da Hortonworks Sandbox para VirtualBox](./media/hdinsight-hadoop-emulator-get-started/download-sandbox.png)

2. Na mesma página Web, selecione o __Guia de Instalação do VirtualBox__ para HDP 2.4 na Hortonworks Sandbox. Isso baixará um PDF contendo instruções de instalação para a máquina virtual.

    ![Ver o guia de instalação](./media/hdinsight-hadoop-emulator-get-started/view-install-guide.png)

## Iniciar a máquina virtual

1. Inicie o VirtualBox, selecione a Hortonworks Sandbox, selecione __Iniciar__ e __Início Normal__.

    ![Início normal](./media/hdinsight-hadoop-emulator-get-started/normal-start.png)

2. Quando a máquina virtual tiver terminado o processo de inicialização, ela exibirá instruções de logon. Abra um navegador da Web e acesse a URL exibida (geralmente http://127.0.0.1:8888).

## Definir senhas

1. Na etapa de __introdução__ da página da Hortonworks Sandbox, selecione __Exibir Opções Avançadas__. Use as informações desta página para fazer logon na área restrita usando SSH. Use o nome e a senha fornecidos.

    > [AZURE.NOTE] Se você não tiver um cliente SSH instalado, use o SSH baseado na Web fornecido pela máquina virtual em \_http://localhost:4200/__.

    Na primeira vez que você se conectar usando SSH, você receberá uma solicitação para alterar a senha da conta raiz. Insira uma nova senha, que será usada posteriormente quando você fizer logon usando SSH.

2. Depois de conectado, digite o seguinte comando:

        ambari-admin-password-reset
    
    Quando receber uma solicitação, forneça uma senha para a conta de administrador do Ambari. Isso será usado quando você acessar a interface de usuário da Web do Ambari.

## Usar o comando do Hive

1. De uma conexão SSH com a área restrita, use o seguinte comando para iniciar o shell do Hive:

        hive

2. Quando o shell for iniciado, use o seguinte para exibir as tabelas que são fornecidas com a área restrita:

        show tables;

3. Use o seguinte para recuperar 10 linhas da tabela `sample_07`:

        select * from sample_07 limit 10;

## Próximas etapas

* [Aprenda a usar o Visual Studio com a Hortonworks Sandbox](hdinsight-hadoop-emulator-visual-studio.md)
* [Learning the ropes of the Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/) (Caminho das pedras do Hortonworks Sandbox)
* [Hadoop tutorial - Getting started with HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/) (Tutorial Hadoop - Introdução ao HDP)

<!---HONumber=AcomDC_0921_2016-->