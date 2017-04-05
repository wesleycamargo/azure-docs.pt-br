---
title: Solucionar problemas do Servidor de Backup do Azure | Microsoft Docs
description: "Solucionar problemas de instalação, registro de Servidor de Backup do Azure e backup e restauração de cargas de trabalho do aplicativo"
services: backup
documentationcenter: 
author: pvrk
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/24/2017
ms.author: pullabhk;markgal;
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: a42488d618c58b36fa8105c1b22fd32ca615d1b1
ms.lasthandoff: 03/27/2017


---

# <a name="troubleshoot-azure-backup-server"></a>Solucionar problemas de Servidor de Backup do Azure

Você pode solucionar os erros encontrados durante a utilização do Servidor de Backup do Azure com as informações listadas na tabela a seguir.

>
>

## <a name="registration-and-agent-related-issues"></a>Problemas relacionados ao registro e ao Agente
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Registrando em um cofre | Credenciais do cofre fornecidas inválidas. O arquivo está corrompido ou não tem as credenciais mais recentes associadas ao serviço de recuperação | Para corrigir esse erro:  <ol><li> Baixe o arquivo de credenciais mais recentes do cofre e tente novamente <br>(OU)</li> <li> Se a ação acima não funcionar, tente baixar as credenciais para um diretório local diferente ou criar um novo cofre <br>(OU)</li> <li> Tente atualizar as configurações de data e hora conforme indicado [neste blog](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/) <br>(OU)</li> <li> Verifique se c:\windows\temp tem mais de 65.000 arquivos. Mover arquivos obsoletos para outro local ou exclua os itens na pasta Temp <br>(OU)</li> <li> Verificar o status dos certificados <br> a. Abrir "Gerenciar Certificados de Computador" (no painel de controle) <br> b. Expandir o nó "Pessoal" e seu nó filho "Certificados" <br> c.  Remover o certificado "Ferramentas do Microsoft Azure" <br> d. Repetir o registro no cliente de Backup do Azure <br> (OU) </li> <li> Verificar se alguma política de grupo está em vigor </li></ol> |
| Enviar agentes por push para servidores protegidos | A operação do agente falhou devido a um erro de comunicação com o serviço Coordenador de Agentes do DPM em \<ServerName> | **Se a ação recomendada exibida no produto não funcionar**, <ol><li> Se você estiver anexando um computador de um domínio não confiável, siga [estas](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx) etapas <br> (OU) </li><li> Se você estiver anexando um computador de um domínio confiável, solucione os problemas usando as etapas descritas [neste blog](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/) <br>(OU)</li><li> Tente desabilitar o Antivírus como uma etapa de solução de problemas. Se isso resolver o problema, modifique as configurações de antivírus conforme sugerido [neste artigo] (https://technet.microsoft.com/library/hh757911.aspx)</li></ol> |
| Enviar agentes por push para servidores protegidos | As credenciais especificadas para o servidor são inválidas | **Se a ação recomendada exibida no produto não funcionar**, <br> Tente instalar o agente de proteção manualmente no servidor de produção, como especificado [neste artigo](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual)|


## <a name="configuring-protection-group"></a>Configurar grupo de proteção
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Configurar grupos de proteção | O DPM não pôde enumerar o componente do aplicativo no computador protegido (nome do computador protegido) | Clique em 'Atualizar' na tela da interface do usuário configurar grupo de proteção no nível de componente/fonte de dados relevante |
| Configurar grupos de proteção | Não é possível configurar a proteção | Se o servidor protegido é um SQL Server, verifique se as permissões da função sysadmin foram fornecidas para a conta do sistema (NTAuthority\System) no computador protegido conforme descrito [neste artigo](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx)
| Configurar grupos de proteção | Não há espaço livre suficiente no pool de armazenamento para esse grupo de proteção | Os discos que são adicionados ao pool de armazenamento [não devem conter uma partição](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Excluir quaisquer volumes existentes nos discos e, em seguida, adicioná-lo ao pool de armazenamento|

## <a name="backup"></a>Backup
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Backup | A réplica está inconsistente | Você pode encontrar mais detalhes sobre as causas de inconsistência entre as réplicas e sugestões relevantes [aqui](https://technet.microsoft.com/library/cc161593.aspx) <br> <ol><li> No caso de backup de BMR/estado do sistema, verifique se o Backup do Windows Server está instalado ou não no Servidor Protegido </li><li> Verificar se há problemas relacionados a espaço no pool de armazenamento do DPM no servidor MABS/DPM e alocar o armazenamento conforme necessário </li><li> Verificar o estado do serviço de cópia de sombra de Volume no servidor protegido. Se ele estiver em estado desabilitado, defina-o para iniciar manualmente e inicie o serviço no servidor. Em seguida, volte para o console do MABS/DPM e inicie a sincronização com o trabalho de verificação de consistência.</li></ol>|
| Backup | Ocorreu um erro inesperado durante a execução do trabalho, o dispositivo não está pronto | **Se a ação recomendada exibida no produto não funcionar**, <br> <ol><li>Defina o Espaço de Armazenamento de Cópia de Sombra para ilimitado nos itens no grupo de proteção e execute a verificação de consistência <br></li> (OU) <li>Tente excluir o grupo de proteção existente e criar vários novos – um com cada item individual</li></ol> |
| Backup | Se você estiver fazendo backup somente do Estado do Sistema, verifique se há espaço livre suficiente no computador protegido para armazenar o backup do Estado do Sistema | <ol><li>Verifique se o WSB no computador protegido está instalado</li><li>Verifique se há espaço suficiente presente no computador protegido para o estado do sistema: A maneira mais fácil de fazer isso é ir para o computador protegido, abrir o WSB, clicar nas seleções e selecionar BMR. A interface do usuário, em seguida, lhe mostrará quanto espaço é necessário para isso. Abra WSB-> Backup local-> Agendamento de backup-> Selecionar Configuração de Backup-> Servidor completo (o tamanho é exibido). Use esse tamanho para verificação.</li></ol>
| Backup | Falha na criação de ponto de recuperação online | Se a mensagem de erro informar que "O Microsoft Azure Backup Agent não pôde criar um instantâneo do volume selecionado", tente aumentar o espaço no volume de ponto de recuperação e de réplica.
| Backup | Falha na criação de ponto de recuperação online | Se a mensagem de erro disser que "A frase secreta de criptografia para este servidor não está definida. Configure uma senha de criptografia", tente configurar uma frase secreta de criptografia. Se isso falhar, <br> <ol><li>verifique se o local temporário existe ou não. O local mencionado no Registro HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config com o nome "ScratchLocation" deve existir.</li><li> Se há um local temporário, tente registrar novamente usando a frase secreta antiga. **Sempre que você configurar uma frase secreta de criptografia, salve-a em um local seguro**</li><ol>
| Backup | Falha de backup de BMR | Se o tamanho de BMR for muito grande, tente novamente depois de mover alguns arquivos do aplicativo para a unidade do sistema operacional |
| Backup | Erro ao acessar pastas/arquivos compartilhados | Tente modificar as configurações de antivírus conforme sugerido [aqui](https://technet.microsoft.com/library/hh757911.aspx)|

