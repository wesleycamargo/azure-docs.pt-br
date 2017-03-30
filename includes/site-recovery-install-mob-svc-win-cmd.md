1. Copie o instalador para uma pasta local (digamos, C:\Temp) no servidor que você deseja proteger. Execute os comandos a seguir como administrador em um prompt de comando:

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Para instalar o Serviço de Mobilidade, execute o seguinte comando:

  ```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP address of the configuration server" /PassphraseFilePath <Full path to the passphrase file>``
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Argumentos de linha de comando do instalador do Serviço de Mobilidade

```
Usage :
UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation directory>] [/CSIP <IP address>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log file path>]<br/>
```

  | Parâmetro|Tipo|Descrição|Valores possíveis|
  |-|-|-|-|
  |/Role|Obrigatório|Especifica se o Serviço de Mobilidade deve ser instalado|Agente </br> MasterTarget|
  |/InstallLocation|Obrigatório|Local onde o Serviço de Mobilidade está instalado|Qualquer pasta no computador|
  |/CSIP|Obrigatório|Endereço IP do servidor de configuração| Qualquer endereço IP válido|
  |/PassphraseFilePath|Obrigatório|Local da frase secreta |Qualquer caminho de arquivo UNC ou local válido|
  |/LogFilePath|Opcional|Local do log de instalação|Qualquer pasta válida no computador|

#### <a name="example"></a>Exemplo

```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "I192.168.2.35" /PassphraseFilePath "C:\Temp\MobSvc.passphrase"
```
