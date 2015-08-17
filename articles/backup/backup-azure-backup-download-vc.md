<properties
   pageTitle="Baixar credenciais do cofre no Backup do Azure | Microsoft Azure"
   description="Saiba como usar as credenciais do cofre para autenticar o computador no cofre de Backup e no serviço de Backup do Azure"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="shreeshd"
   editor=""/>
<tagsms.service="backup" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="storage-backup-recovery" ms.date="07/01/2015" ms.author="jimpark"; "aashishr"/>

# Usando as credenciais do cofre para autenticação no serviço Backup do Azure
O servidor local (cliente Windows, Windows Server ou servidor do SCDPM) precisa ser autenticado em um cofre de backup antes de poder fazer backup de dados no Azure. A autenticação é feita com as "credenciais do cofre". O conceito de credenciais do cofre é semelhante ao conceito de um arquivo de "configurações de publicação" que é usado no Azure PowerShell.

## O que é o arquivo de credencial do cofre?
O arquivo de credenciais do cofre é um certificado gerado pelo portal para cada cofre de backup. O portal, em seguida, carrega a chave pública no Serviço de Controle de Acesso (ou ACS). A chave privada do certificado é disponibilizada para o usuário como parte do fluxo de trabalho que é fornecido como uma entrada no fluxo de trabalho de registro do computador. Isso autentica o computador para enviar os dados de backup para um cofre identificado no serviço de Backup do Azure.

Vale a pena destacar que a credencial do cofre é usada somente durante o fluxo de trabalho de registro. É responsabilidade do usuário garantir que o arquivo de credenciais do cofre não seja comprometido. Se esse arquivo acabar em poder de qualquer usuário não autorizado, o arquivo de credenciais do cofre poderá ser usado para registrar outros computadores no mesmo cofre. No entanto, como os dados de backup são criptografados usando uma senha que pertence ao cliente, os dados de backup existentes não poderão ser comprometidos. Para atenuar esse problema, as credenciais do cofre são configuradas para expirar em 48 horas. Você pode baixar as credenciais de um cofre de backup quantas vezes quiser – mas apenas o arquivo mais recente de credencial de cofre será aplicável durante o fluxo de trabalho de registro.

## Baixe o arquivo de credencial do cofre
O arquivo de credencial do cofre é baixado por meio de um canal seguro no Portal do Azure. O serviço de Backup do Azure não tem conhecimento da chave privada do certificado e a chave privada não é mantida no portal nem no serviço. Use as seguintes etapas para baixar a credencial do cofre em um computador local.

1.  Entre no [Portal de Gerenciamento](https://manage.windowsazure.com/)
2.  Clique em **Serviços de Recuperação** no painel de navegação à esquerda e selecione o cofre de backup que você criou.
3. Clique no ícone de nuvem para ir para a exibição de Início Rápido do cofre de backup.

    ![Visualização rápida](./media/backup-azure-backup-download-vc/quickview.png)

4.  Na página Início Rápido, clique em **Baixar credenciais do cofre**. O portal gera o arquivo de credencial de cofre, que fica disponível para download.

    ![Baixar](./media/backup-azure-backup-download-vc/downloadvc.png)

5.  O portal gerará uma credencial de cofre usando uma combinação do nome do cofre e a data atual. Clique em **Salvar** para baixar as credenciais do cofre para a pasta de downloads da conta local ou selecione Salvar Como no menu Salvar para especificar um local para as credenciais do cofre.

## Observações
- Desde março de 2015, os usuários não têm uma forma programática (por exemplo: PowerShell) de baixar credenciais de cofre.
- Certifique-se de que as credenciais do cofre sejam salvas em um local que pode ser acessado no seu computador. Se forem armazenadas em um compartilhamento de arquivos/SMB, verifique as permissões de acesso.
- O arquivo de credenciais do cofre é usado somente durante o fluxo de trabalho de registro.
- O arquivo de credenciais do cofre expira após 48 horas e pode ser baixado no portal.
- Consulte as [Perguntas frequentes](backup-azure-backup-faq.md) do Backup do Azure em caso de dúvidas sobre o fluxo de trabalho.

## Próximas etapas
[Baixar, registrar e instalar o Agente de Backup do Azure](backup-azure-backup-download-register)

<!---HONumber=August15_HO6-->