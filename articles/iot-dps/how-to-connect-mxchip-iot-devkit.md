---
title: "Como usar o MXKhip IoT DevKit para conectar ao Serviço de Provisionamento de Dispositivos no Hub IoT do Azure | Microsoft Docs"
description: "Como usar o MXKhip IoT DevKit para conectar ao Serviço de Provisionamento de Dispositivos no Hub IoT do Azure"
services: iot-dps
keywords: 
author: liydu
ms.author: liydu
ms.date: 02/20/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 77c8a6a5e384d27dca2582cc0fedc2bd23c0592e
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-hub-device-provisioning-service"></a>Conectar o MXChip IoT DevKit ao Serviço de Provisionamento de Dispositivos no Hub IoT do Azure

Este artigo descreve como configurar o DevKit para que seja registrado automaticamente no Hub IoT usando o Serviço de Provisionamento de Dispositivos. Neste tutorial, você aprenderá a:

* Configurar o ponto de extremidade global do DPS no dispositivo
* Use UDS (Unique Device Secret) para gerar certificado X.509
* Inscrever um dispositivo individual
* Verificar se o dispositivo está registrado

O [MXChip IoT DevKit](https://aka.ms/iot-devkit)é uma placa compatível com Arduino all-in-one periféricos e sensores avançados. É possível desenvolvê-lo, usando a [extensão do Visual Studio Code para Arduino](https://aka.ms/arduino). E vem com um [ catálogo de projetos ](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) crescente para guiá-lo no protótipo de soluções de IoT (Internet das Coisas) que aproveitam os serviços do Microsoft Azure.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste tutorial, você precisará do seguinte:

* Prepare o DevKit com [Guia de Introdução](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Atualize para o firmware mais recente (>= 1.3.0) com o tutorial [Atualização de Firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Crie e vincule o Hub IoT com a instância de Serviço de Provisionamento de Dispositivos com [Configurar provisionamento automático](https://docs.microsoft.com/en-us/azure/iot-dps/quick-setup-auto-provision).

## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>Definir a configuração do Serviço de Provisionamento de Dispositivos no dispositivo

Para habilitar o DevKit para conectar à instância de Serviço de Provisionamento de Dispositivos, você criou:

1. No Portal do Azure, selecione a **Visão Geral** do Serviço de Provisionamento de Dispositivos e anote o valor do **Ponto de extremidade do dispositivo global** e **Escopo da ID**.
  ![ Escopo da ID e Ponto de extremidade global do DPS](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Verifique se o `git` está instalado em seu computador e é adicionado às variáveis de ambiente que podem ser acessadas pela janela de comando. Consulte [ferramentas de cliente de Software Freedom Conservancy's Git ](https://git-scm.com/download/) para ter a versão mais recente instalada.

3. Abra um prompt de comando. Clone o repositório GitHub para código de exemplo DPS:
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Inicie o Código VS e conecte o DevKit ao computador, abra a pasta contendo o código clonado.

5. Abra **DevKitDPS.ino**, localize e substitua `[Global Device Endpoint]` e `[ID Scope]` com os valores que você acabou de anotar.
  ![Ponto de extremidade do DPS](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) Você pode deixar **registrationId** como vazio, o aplicativo irá gerar uma com base no endereço MAC e na versão do firmware. Se você quiser personalizá-la, a ID do registro deve usar combinações alfanuméricas, minúsculas e hífens com no máximo 128 caracteres. Para obter mais informações, consulte [Gerenciar registros de dispositivos com o Portal do Azure](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-manage-enrollments).

6. Use a **Abertura Rápida** no VS Code (Windows: `Ctrl+P`, macOS: `Cmd+P`) e insira **task device-upload** para compilar e carregar o código para o DevKit.

7. Observe o sucesso da tarefa na janela de saída.

## <a name="save-unique-device-secret-on-stsafe-security-chip"></a>Salvar o Unique Device Secret no circuito integrado de segurança STSAFE

O Serviço de Provisionamento de Dispositivos pode ser configurado no dispositivo com base no [HSM (Módulo de Segurança de Hardware)](https://azure.microsoft.com/en-us/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/). O DevKit usa [DICE (Device Identity Composition Engine)](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) do [ TCG (Trusted Computing Group)](https://trustedcomputinggroup.org). Um **UDS (Unique Device Secret)** salvo no circuito integrado de segurança STSAFE no DevKit é utilizado para gerar o certificado [X.509](https://docs.microsoft.com/en-us/azure/iot-dps/tutorial-set-up-device#select-a-hardware-security-module) exclusivo. O certificado poderá ser utilizado posteriormente para o processo de inscrição no Serviço de Provisionamento de Dispositivos.

Um **UDS (Unique Device Secret)** típico é uma cadeia de caracteres de 64 caracteres. Um exemplo de UDS é o seguinte:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Cada um dos dois caracteres é usado como valor hex. no cálculo de segurança. Portanto, o exemplo UDS acima é resolvido para: "`0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16`, `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`".

Para salvar o Unique Device Secret no DevKit:

1. Abra o monitor serial, utilizando uma ferramenta como Putty, consulte [Usar o modo de Configuração](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) para obter mais detalhes.

2. Com o DevKit conectado ao computador, mantenha pressionado o botão A e, em seguida, pressione e solte o botão de reinicialização para entrar no modo de configuração. A tela deve mostrar a ID do DevKit e **'Configuration'**.

3. Na cadeia de caracteres longa do UDS de exemplo acima e altere um ou vários caracteres para outros valores entre `0` e `f`. Isso é usado como seu próprio UDS.

4. Na janela do monitor serial, digite **set_dps_uds [your_own_uds_value]** pressione a tecla Enter para salvá-lo.
  > [!NOTE]
  > Por exemplo, se você definir seu próprio UDS alterando os dois últimos caracteres para `f`, será necessário inserir o comando como **set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff**.

5. Sem fechar a janela do monitor serial, pressione o botão de reinicialização no DevKit.

6. Ante o valor do **Endereço do DevKit MAC** e **Versão do Firmware do DevKit**.
  ![Versão do Firmware](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-x509-certificate"></a>Gerar certificado X.509

### <a name="windows"></a>Windows

1. Abra o explorador de arquivos e acesse a pasta que contém o código de exemplo DSP que você clonou, há uma pasta **.build**, localize e copie **DPS.ino.bin** e **DPS.ino.map** nela.
  ![Arquivos gerados](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Se a configuração `built.path` para Arduino foi alterada para outra pasta. Localize esses arquivos na pasta que você configurou.

2. Cole esses dois arquivos na pasta **ferramentas** no mesmo nível com a pasta **.build**.

3. Execute **dps_cert_gen.exe**, siga as instruções para inserir seu **UDS**, **endereço MAC** para o DevKit e a **versão do firmware** para gerar o certificado X.509.
  ![Executar dps-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. Observe o sucesso da geração, um certificado **.pem** é salvo na mesma pasta.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Criar uma entrada de registro de dispositivo no Serviço de Provisionamento de Dispositivos

1. No Portal do Azure, navegue até o serviço de provisionamento. Clique em **Gerenciar registros** e selecione a guia **Registros individuais**. ![Registros individuais](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Clique em **Adicionar**.

3. No **Mecanismo**, escolha **X.509**.
  ![Baixar o certificado](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

4. No **arquivo Certificado .pem ou .cer**, carregue apenas o certificado **.pem** que você possui.

5. Deixe o restante como padrão e clique em **Salvar**.

## <a name="start-the-devkit"></a>Iniciar o DevKit

1. Inicie o VS Code e abra o monitor serial.

2. Pressione o botão de **Reinicialização** no seu DevKit.

Você deve visualizar o DevKit iniciar o registro no Serviço de Provisionamento de Dispositivos.

![Saída do VS Code](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-the-devkit-is-registered-on-iot-hub"></a>Verifique se o DevKit está registrado no Hub IoT

Quando o dispositivo inicializar, as ações a seguir deverão ocorrer:

1. O dispositivo envia uma solicitação de registro para o Serviço de Provisionamento de Dispositivos.
2. O Serviço de Provisionamento de Dispositivos envia de volta um desafio de registro que é respondido pelo dispositivo.
3. Após um registro bem-sucedido, o Serviço de Provisionamento de Dispositivos envia o URI do Hub IoT, a ID do dispositivo e a chave criptografada de volta para o dispositivo.
4. O aplicativo cliente do Hub IoT no dispositivo se conecta então ao seu hub.
5. Após a conexão bem-sucedida ao hub, você verá o dispositivo aparecer no Device Explorer do Hub IoT.
  ![Dispositivo registrado](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-device-id"></a>Alterar ID do dispositivo

A ID do dispositivo padrão registrada no Hub IoT do Azure é **AZ3166**. Se você quiser modificá-lo, siga as [instruções aqui](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Comentários e problemas

Se você encontrar problemas, consulte [Perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou entre em contato conosco pelos canais abaixo:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Próximas etapas

Você aprendeu a preparar o DevKit para registrar um dispositivo de forma segura no DPS usando o DICE, de modo que ele possa registrar-se automaticamente no Hub IoT sem toque. Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Configurar o ponto de extremidade global do DPS no dispositivo
> * Use UDS (Unique Device Secret) para gerar certificado X.509
> * Inscrever um dispositivo individual
> * Verificar se o dispositivo está registrado

Avance para os outros tutoriais para aprender:

> [!div class="nextstepaction"]
> [Criar e provisionar um dispositivo simulado](./quick-create-simulated-device.md)

