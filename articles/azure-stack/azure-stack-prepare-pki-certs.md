---
title: Prepare certificados de infraestrutura de chave pública do Azure Stack para a implantação de sistemas integrados do Azure Stack ou rotação do segredo | Microsoft Docs
description: Descreve como preparar os certificados PKI de pilha do Azure para sistemas integrados do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 7a4c0d62fd1ea675c6569840c26ed1fb8e388850
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53631474"
---
# <a name="prepare-azure-stack-pki-certificates-for-use-in-deployment-or-rotation"></a>Preparar certificados PKI de pilha do Azure para uso na implantação ou rotação
Os arquivos de certificado [obtido da autoridade de certificação de escolha](azure-stack-get-pki-certs.md) devem ser importados e exportados com propriedades que correspondem a requisitos de certificado do Azure Stack.


## <a name="prepare-certificates-for-deployment"></a>Preparar certificados para implantação
Use estas etapas para preparar e validar os certificados PKI de pilha do Azure que serão usados para implantar um novo ambiente do Azure Stack ou para a rotação dos segredos em um ambiente existente do Azure Stack: 

### <a name="import-the-certificate"></a>Importar o certificado

1.  Copiar as versões originais do certificado [obtido da autoridade de certificação de escolha](azure-stack-get-pki-certs.md) em um diretório no host de implantação. 
  > [!WARNING]
  > Não copie os arquivos que já foi importados, exportados ou alterados de alguma forma dos arquivos fornecidos diretamente pela autoridade de certificação.

1.  Clique com botão direito no certificado e selecione **Instalar certificado** ou **Instalar PFX** , dependendo de como o certificado foi entregue da autoridade de certificação.

1. No **Assistente de importação de certificado**, selecione **Máquina Local** como o local de importação. Selecione **Avançar**. Na tela seguinte, clique em Avançar novamente.

    ![Local de importação de máquina local](./media/prepare-pki-certs/1.png)

1.  Escolher **colocar todos os certificados no repositório a seguir** e, em seguida, selecione **confiança empresarial** como o local. Clique em **Okey** para fechar a caixa de diálogo de seleção de repositório de certificado e, em seguida **próxima**.

    ![Configurar o repositório de certificados](./media/prepare-pki-certs/3.png)

     a. Se você estiver importando um PFX, você verá uma caixa de diálogo adicionais. Sobre o **proteção de chave privada** página, insira a senha para seus arquivos de certificado e, em seguida, habilitar o **marcar esta chave como exportável. Isso permite que você faça o backup ou transportar suas chaves em um momento posterior** opção. Selecione **Avançar**.

    ![Marcar chaves como exportáveis](./media/prepare-pki-certs/2.png)

1. Clique em Concluir para concluir a importação.

### <a name="export-the-certificate"></a>Exportar o certificado

Abra o console do MMC do Gerenciador de certificados e conecte-se ao repositório de certificados do computador Local.

1. Abra o Console de gerenciamento Microsoft, no Windows 10 Clique com botão direito no Menu Iniciar, em seguida, clique em executar. Tipo de **mmc** clique okey.

1. Clique em arquivo, Adicionar/Remover Snap-In e em seguida, selecione os certificados, clique em Adicionar.

    ![Adicionar Snap-in de certificados](./media/prepare-pki-certs/mmc-2.png)
 
1. Selecione conta de computador, clique em Avançar e em seguida, selecione o computador Local e concluir. Clique em okey para fechar a página Adicionar/Remover Snap-In.

    ![Adicionar Snap-in de certificados](./media/prepare-pki-certs/mmc-3.png)

1. Procurar certificados > confiança empresarial > local do certificado. Verifique se seu certificado à direita.

1. No console do Gerenciador de barra de certificado tarefas, selecione **ações** > **todas as tarefas** > **exportar**. Selecione **Avançar**.

  > [!NOTE]
  > Dependendo de quantos Azure Stack, certificados, que você talvez precise concluir esse processo mais de uma vez.

1. Selecione **Sim, exportar a chave privada**e, em seguida, clique em **próxima**.

1. Na seção Exportar formato de arquivo, selecione **exportar todas as propriedades estendidas** e, em seguida, clique em **próxima**.

1. Selecione **senha** e forneça uma senha para os certificados. Guarde essa senha, pois ele é usado como um parâmetro de implantação. Selecione **Avançar**.

1. Escolha um nome de arquivo e o local para o arquivo pfx exportar. Selecione **Avançar**.

1. Selecione **Concluir**.

## <a name="next-steps"></a>Próximas etapas
[Validar certificados PKI](azure-stack-validate-pki-certs.md)
