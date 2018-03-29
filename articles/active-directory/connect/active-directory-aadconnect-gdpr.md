---
title: Azure AD Connect e Regulamento Geral sobre a Proteção de Dados | Microsoft Docs
description: Este documento descreve como obter a conformidade GDPR com o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: c3956dd379961b119f65bdebe1f5a8038c4fa8f0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect"></a>Conformidade GDPR e Azure AD Connect 

A partir de maio de 2018, entra em vigor uma legislação de privacidade europeia, o [Regulamento Geral de Proteção de Dados (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm). O GDPR impõe novas regras às empresas, órgãos governamentais, organizações sem fins lucrativos e outras organizações que oferecem bens e serviços para pessoas da União Europeia (UE) ou que coletam e analisam dados vinculados a residentes da UE. O GDPR se aplica independentemente de onde você está localizado. 

Os produtos e serviços da Microsoft estão disponíveis atualmente para ajudá-lo a atender aos requisitos do GDPR. Leia mais sobre a Política de Privacidade da Microsoft na [Central de Confiabilidade](https://www.microsoft.com/trustcenter)

>[!NOTE] 
>Este artigo trata do Azure AD Connect e conformidade com o GDPR.  Para obter informações sobre o Azure AD Connect Health e conformidade com o GDPR consulte o artigo [aqui](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md).

A conformidade com o Regulamento Geral de Proteção de Dados para instalações do Azure AD Connect pode ser acessada de duas maneiras:

1.  Mediante solicitação, extraia dados de uma pessoa e remova os dados dessa pessoa das instalações
2.  Certifique-se de que nenhum dado é retido além de 48 horas.

A equipe do Azure AD Connect recomenda a segunda opção, pois é muito mais fácil de implementar e manter.

Um servidor de sincronização do Azure AD Connect armazena os dados a seguir que estão no escopo para conformidade com o GDPR:
1.  Dados sobre uma pessoa no **banco de dados do Azure AD Connect**
2.  Dados de arquivos de **log do Windows Event** que podem conter informações sobre uma pessoa
3.  Dados de **arquivos de log de instalação do Azure AD Connect** que podem conter sobre uma pessoa

Para ser compatível com o GDPR, os clientes de conexão do Azure AD Connect devem seguir as seguintes orientações:
1.  Exclua o conteúdo da pasta que contém os arquivos de log de instalação do Azure AD Connect regularmente – pelo menos a cada 48 horas
2.  Este produto também pode criar Logs de Eventos.  Para saber mais sobre os logs de Logs de Eventos, consulte a [documentação aqui](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Dados sobre uma pessoa serão removidos automaticamente do banco de dados do Azure AD Connect quando os dados dessa pessoa são removidos do sistema de origem onde ele foi originado. Nenhuma ação específica dos administradores é necessária para GDPR ser compatível.  No entanto, ele requer que os dados do Azure AD Connect estejam sincronizados com a fonte de dados, pelo menos a cada dois dias.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Exclua o conteúdo de pasta de arquivo de log de instalação do Azure AD Connect
Verifique regularmente e exclua o conteúdo da pasta **c:\programdata\aadconnect** – exceto para o arquivo **PersistedState.Xml**. Esse arquivo mantém o estado da instalação anterior do Azure AD Connect e é usado quando uma instalação de atualização é executada. Esse arquivo não contém dados sobre uma pessoa e não deve ser excluído.

>[!IMPORTANT]
>Não exclua o arquivo PersistedState.xml.  Esse arquivo não contém nenhuma informação de usuário e mantém o estado da instalação anterior.

Você pode revisar e excluir esses arquivos usando o Windows Explorer, ou você pode usar um script semelhante ao seguinte para executar as ações necessárias:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Agende esse script para executar a cada 48 horas
Use as etapas a seguir para agendar o script para executar a cada 48 horas.

1.  Salve o script em um arquivo com a extensão **&#46;PS1**, em seguida, abra o painel de controle e clique em **Sistemas e Segurança**.
    ![Sistema](media\active-directory-aadconnect-gdpr\gdpr2.png)

2.  Sob o título de ferramentas administrativas, clique em **Agendar tarefas**.
    ![Tarefa](media\active-directory-aadconnect-gdpr\gdpr3.png)
3.  No Agendador de Tarefas, clique com botão direito em **Biblioteca de agendamento de tarefas** e clique em **Criar tarefa básica...**
4.  Insira o nome para a nova tarefa e clique em **Avançar**.
5.  Selecione **Diariamente** para o acionador de tarefa e clique em **Avançar**.
6.  Defina a recorrência para **2 dias** e clique em **Avançar**.
7.  Selecione **Iniciar um programa** como a ação e clique em **Avançar**.
8.  Digite **PowerShell** na caixa para o programa/script e, na caixa rotulada **Adicionar argumentos (opcional)**, insira o caminho completo para o script que você criou anteriormente e clique em **Avançar**.
9.  A próxima tela mostra um resumo da tarefa que você está prestes a criar. Verifique os valores e clique em **Concluir** para criar a tarefa.



## <a name="next-steps"></a>Próximas etapas
- [Integração de suas identidades locais com o Azure Active Directory](active-directory-aadconnect.md).
- [Azure AD Connect Health e GDPR](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)
