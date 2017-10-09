---
title: "Problemas conhecidos e Guia de solução de problemas | Microsoft Docs"
description: "Lista de problemas conhecidos e um guia para ajudar a solucioná-los"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ef5e058d81b64c46e8bad6f85c0bef0c69fb1512
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench – problemas conhecidos e Guia de solução de problemas 
Este artigo ajuda a localizar e corrigir os erros ou falhas encontrados como parte do uso do aplicativo Azure Machine Learning Workbench. 

> [!NOTE]
> Ao se comunicar com a equipe de suporte por emails ou postagens do fórum, é útil ter o número de build. Você pode descobrir o número de build do aplicativo clicando no menu **Ajuda**. Clicar no número de build copia-o para a sua área de transferência. Você pode colá-lo em emails ou fóruns de suporte para ajudar a relatar problemas.

![verificar número de versão](media/known-issues-and-troubleshooting-guide/version.png)

## <a name="windows-and-mac"></a>Windows e Mac
### <a name="centos-based-azure-data-science-virtual-machine"></a>Máquina Virtual de Ciência de Dados do Azure baseada em CentOS 
* Não há suporte para o envio de trabalhos para DSVM (Máquina Virtual de Ciência de Dados) do Azure baseada em CentOS. Você pode direcionar à [DSVM baseada em Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

### <a name="docker-error"></a>Erro de docker 
* Ao executar em um contêiner do Docker local, se você encontrar o erro a seguir, poderá corrigi-lo alterando o Servidor DNS do Docker de Automático para Fixo 8.8.8.8. 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```
![Imagem](media/known-issues-and-troubleshooting-guide/docker_dns.png)


### <a name="linux-vm-password"></a>Senha da VM Linux 
* Se você alterar a senha do portal do Azure em uma VM do Linux Ubuntu, talvez você receba o seguinte erro ao executar um trabalho nele:
  ```
  sudo: no tty present and no askpass program specified.
  ``` 

  A solução alternativa é adicionar um arquivo (exemplo _myDockerUsers_) em _/etc/sudoers.d_ com o seguinte conteúdo:
  ```
  <your_username> ALL = NOPASSWD: /usr/bin/docker, /usr/bin/nvidia-docker
  ```

### <a name="ssh-keys"></a>Chaves SSH 
* Não há suporte para chaves SSH ao se conectar a um computador remoto ou um cluster do Spark via SSH. Há suporte somente para o modo de nome de usuário/senha.

## <a name="windows-only"></a>Somente Windows 
### <a name="sharing-c-drive-in-docker"></a>Compartilhamento da unidade C no Docker 
* Verifique o compartilhamento na unidade C usando o explorador de arquivos
* Abra as configurações do adaptador de rede e desinstale/reinstale "Compartilhamento de arquivo e impressora para redes Microsoft" para vEthernet
* Abra as configurações do docker e compartilhe a unidade C nas configurações do docker
* Alterações à senha do Windows afetam o compartilhamento. Abra o explorador de arquivos, compartilhe novamente a unidade C e digite a nova senha.
* Você também pode encontrar o problema de firewall ao tentar compartilhar sua unidade C com o Docker. Esta [postagem de Stack Overflow](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) pode ser útil.
* Ao compartilhar a unidade C usando as credenciais de domínio, o compartilhamento pode deixar de funcionar em redes em que o controlador de domínio não esteja acessível (por exemplo, redes domésticas, WiFi pública etc.). Para obter mais informações, confira [esta postagem](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).



## <a name="mac-only"></a>Somente Mac 
* Não há suporte para transformações de clustering de texto no Mac.
* Não há suporte para a biblioteca RevoScalePy no Mac.

## <a name="azure-machine-learning-service-limits"></a>Limites de serviço do Azure Machine Learning

- Tamanho máximo permitido da pasta do projeto: 25 MB
    >[!Note]
    >Esse limite não se aplica às pastas `.git`, `docs` e `outputs`. Esses nomes de pasta diferenciam maiúsculas de minúsculas.

- Tempo máximo de execução de experimento permitido: sete dias
- Tamanho máximo do arquivo controlado na pasta `outputs` após uma execução: 512 MB 

>[!NOTE]
>Se você estiver trabalhando com arquivos grandes, consulte [Como manter alterações e lidar com arquivos grandes](how-to-read-write-files.md).

## <a name="other-issues"></a>Outros problemas
Se você estiver ciente de outros problemas não documentados, envie-nos comentários por meio de _Enviar um Smiley/Rosto Triste_. 




