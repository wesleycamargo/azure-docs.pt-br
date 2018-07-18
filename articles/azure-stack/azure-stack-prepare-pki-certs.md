---
title: Preparar certificados de infraestrutura de chave pública do Azure pilha para implantação de sistemas de pilha do Azure integradas | Microsoft Docs
description: Descreve como preparar os certificados PKI de pilha do Azure para sistemas de pilha do Azure integradas.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 934585082e2832c41885874c82ab43d64a1fa361
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2018
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>Preparar certificados PKI de pilha do Azure para implantação
Os arquivos de certificado [obtido da autoridade de certificação de escolha](azure-stack-get-pki-certs.md) devem ser importados e exportados com propriedades que correspondem a requisitos de certificado da pilha do Azure.


## <a name="prepare-certificates-for-deployment"></a>Preparar certificados para implantação
Siga estas etapas para preparar e validar os certificados PKI de pilha do Azure: 

### <a name="import-the-certificate"></a>Importar o certificado

1.  Copiar as versões originais do certificado [obtido da autoridade de certificação de escolha](azure-stack-get-pki-certs.md) em um diretório no host de implantação. 
  > [!WARNING]
  > Não copie os arquivos que já foram importados, exportados ou alterados de alguma forma dos arquivos fornecidos diretamente pela autoridade de certificação.

2.  Clique com botão direito no certificado e selecione **Instalar certificado** ou **Instalar PFX** dependendo de como o certificado foi entregue da autoridade de certificação.

3. No **o Assistente de importação de certificado**, selecione **Máquina Local** como o local de importação. Selecione **Avançar**. Na tela seguinte, clique em Avançar novamente.

    ![Local de importação do computador local](.\media\prepare-pki-certs\1.png)

4.  Escolha **local todos os certificados no repositório a seguir** e, em seguida, selecione **confiabilidade corporativa** como o local. Clique em **Okey** para fechar a caixa de diálogo de seleção de repositório de certificado e, em seguida, **próximo**.

    ![Configurar o repositório de certificados](.\media\prepare-pki-certs\3.png)

    a. Se você estiver importando um PFX, você verá uma caixa de diálogo adicional. Sobre o **proteção de chave privada** página, insira a senha para os arquivos de certificado e, em seguida, habilitar o **marcar esta chave como exportável. Isso permite que você faça o backup ou transportar suas chaves posteriormente** opção. Selecione **Avançar**.

    ![Marcar chaves como exportáveis](.\media\prepare-pki-certs\2.png)

5. Clique em Concluir para concluir a importação.

### <a name="export-the-certificate"></a>Exportar o certificado

Abra o console do MMC do Gerenciador de certificados e conecte-se ao repositório de certificados do computador Local.

1. Abra o Console de gerenciamento Microsoft, no Windows 10 Clique com botão direito no Menu Iniciar, em seguida, clique em executar. Tipo **mmc** clique okey.

2. Clique em arquivo, Adicionar/Remover Snap-In, em seguida, selecionados certificados, clique em Adicionar.

    ![Adicionar Snap-in de certificados](.\media\prepare-pki-certs\mmc-2.png)
 
3. Selecione conta de computador, clique em Avançar e em seguida, selecione o computador Local e concluir. Clique okey para fechar a página Adicionar/Remover Snap-In.

    ![Adicionar Snap-in de certificados](.\media\prepare-pki-certs\mmc-3.png)

4. Procurar certificados > confiabilidade corporativa > local do certificado. Verifique se que você veja seu certificado à direita.

5. No console do Gerenciador de barra de certificado tarefas, selecione **ações** > **todas as tarefas** > **exportar**. Selecione **Avançar**.

  > [!NOTE]
  > Dependendo de quantos pilha do Azure certificados que você precisará concluir esse processo mais de uma vez.

4. Selecione **Sim, exportar a chave privada**e, em seguida, clique em **próximo**.

5. Na seção de formato do arquivo de exportação, selecione **exportar todas as propriedades estendidas** e, em seguida, clique em **próximo**.

6. Selecione **senha** e fornecer uma senha para os certificados. Guarde essa senha, pois ele é usado como um parâmetro de implantação. Selecione **Avançar**.

7. Escolha um nome de arquivo e um local para o arquivo pfx exportar. Selecione **Avançar**.

8. Selecione **Concluir**.

## <a name="next-steps"></a>Próximas etapas
[Validar certificados PKI](azure-stack-validate-pki-certs.md)