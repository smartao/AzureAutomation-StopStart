# AzureAutomation-StopStart

Scripts em powershell para automatizar as ações de ligar e desligar VMs em horários determinados.

## Overview

* A conta de automação é responsável pela execução de script no Azure.  
* Após publicar o script na conta de automação sera colocado no agendamento apenas os scripts StartTX.ps1 e StopTX, onde X é o número referente do agendamento.  
* o StartX.ps1 sera executado para ligar as VMs conforme o agendamento.  
* o StopX.ps1 sera executado para desligar as VMs conforme o agendamento.  
* Conforme o agendamento esses scripts irão ler todas as tags das VMs do grupo de recursos e em seguida chamar os scripts StartVM.ps1 e StopVM.ps1, dessa forma, se existir 100 VMs para ser ligada, sera chamado 100 vezes o script StartVM.ps1  

### Resumo

1. Criar conta de automação
1. Verificar permissões da conta de automação
1. Baixar projeto
1. Editar scripts
1. Importa-los na conta de automação
1. Publica-los
1. Configurar o agendamento
1. Adicionar tags nas VMs
1. Testes pós instalação

### Pré requisitos

```
Uma assinatura do Azure
Ter permissões de dono da assinatura
Uma VMs criada para testes
```

### Instalação

##### 1. Criar conta de automação.

Botão mais (+), procurar por automação.

Imagem01

##### 2. Verificar conexões da conta de automação.

Acesse:  
Conta de automação >> Recursos compartilhados >> Conexões >> Deve existir duas conexões.

```
AzureRunAsConnection
AzureClassicRunAsConnection
```

Imagem02

##### 3. Baixar o projeto

`git clone https://github.com/smartao/AzureAutomation-StopStart.git`

##### 4 - Editar os scripts startT1.ps1 e stopT1.ps1 alterando apenas as três linhas.

```
################################
##### Edit these variables #####
################################
   $AutomationRG = "POC-Sergei",
   $AutomationAcct = "automationrunbook",
   $StartType = "AutoT1"
################################
```

**Onde:**  
`$AutomationRG` = Deve receber o grupo de recursos que as VMs pertencem, nesse exemplo é o "POC-Sergei".  
`$AutomationAcct` = Nome da conta de automação, foi criada no passo 1.  
`$StartType` = Conteúdo da tag para a VM ser ligada, ex: AutoT1, existe apenas no script StartT1.ps1 e StartT2.ps1.  
`$StopType` = Conteúdo da tag para a VM ser desligada, ex: AutoT1, existe apenas no script StopT1.ps1 e StopT2.ps1.  

**Considerações:**  
AutoT1 = Faz referência ao Tempo 1, que pode ser um agendamento de horário especifico, exemplo das 8h as 18h.
Os scripts `StartT1.ps1` e `StartT2.ps1`, são idênticos, o intuito é alterar apenas a váriavel `$StartType`.    
Assim o `StartT1.ps1` fica responsavel pelo agendamento 1 e o `StartT2.ps1` responsável pelo agendamento 2.  
Os scripts `StopT1.ps1` e `StopT2.ps1` seguem exatamente a mesma linha de raciociono mencionada anteriormente.  
Caso precise de mais agendamentos basta duplicar o script renomeando (ex: StartT3.ps1) e alterando a variável `$StartType`.  
##### 5. Importa-los na conta de automação

Acesse:
Conta de automação >> Runbooks >> Adicionar um runbook >> Importar um runbook existente

Repita o processos para os demais scripts

Imagem03

Imagem04

##### 6. Publica-los

Após o importa-los é necessário publica-los
Clique em um script >> Editar >> 

Imagem05

Imagem06

Imagem07

Imagem08

##### 7. Configurar o agendamento

Imagem09

Imagem10

##### 8. Adicionar tags nas VMs

Imagem11

Adicionar tags em larga escala
mostrar comando


##### 9. Testes pós instalação

Executar o script manualmente para validar
Conta de automação >> Runbooks >> StartT1.ps1 >> Iniciar

Repetir o processo para o script StopT1.ps1

Imagem12

Acessar os jobs e verificar se houve erros

Imagem13

