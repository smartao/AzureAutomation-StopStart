# AzureAutomation-StopStart

Scripts em powershell para automatizar as tarefas de ligar e desligar VMs em horários determinados.

## Overview

* A conta de automação é responsável pela execução de script no Azure.  
* Após publicar o script na conta de automação coloraremos no agendamento apenas os scripts StartTX.ps1 e StopTX, onde X é o número referente do agendamento.  
* o StartX.ps1 será executado para ligar as VMs conforme o agendamento.  
* o StopX.ps1 será executado para desligar as VMs conforme o agendamento.  
* Conforme o agendamento esses scripts irão ler todas as tags das VMs do grupo de recursos e em seguida chamar os scripts StartVM.ps1 e StopVM.ps1, dessa forma, se existir 100 VMs para ser ligada, será chamado 100 vezes o script StartVM.ps1  

### Resumo

1. Criar conta de automação
1. Verificar permissões da conta de automação
1. Baixar projeto
1. Editar scripts
1. Importa-los na conta de automação
1. Publicá-los
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

#### 1. Criar conta de automação.

Botão mais (+), procurar por automação.

![alt-tag](https://github.com/smartao/AzureAutomation-StopStart/blob/master/images/Imagem01.png)

#### 2. Verificar conexões da conta de automação.

Acesse:  
Conta de automação >> Recursos compartilhados >> Conexões >> Deve existir duas conexões.

```
AzureRunAsConnection
AzureClassicRunAsConnection
```

![alt-tag](https://github.com/smartao/AzureAutomation-StopStart/blob/master/images/Imagem02.png)

#### 3. Baixar o projeto

`git clone https://github.com/smartao/AzureAutomation-StopStart.git`

#### 4 - Editar os scripts startT1.ps1 e stopT1.ps1 alterando apenas as três linhas.

```
################################
##### Edit these variables #####
################################
   $AutomationRG = "POC-Sergei",
   $AutomationAcct = "automationrunbook",
   $StartType = "AutoT1"
################################
```

Onde:  
`$AutomationRG` = Deve receber o grupo de recursos que as VMs pertencem, nesse exemplo é o "POC-Sergei".  
`$AutomationAcct` = Nome da conta de automação, foi criada no passo 1.  
`$StartType` = Conteúdo da tag para a VM ser ligada, ex: AutoT1, existe apenas no script StartT1.ps1 e StartT2.ps1.  
`$StopType` = Conteúdo da tag para a VM ser desligada, ex: AutoT1, existe apenas no script StopT1.ps1 e StopT2.ps1.  

Considerações:  
AutoT1 = Faz referência ao Tempo 1, que pode ser um agendamento de horário especéfico, exemplo das 8h ás 18h.  
Os scripts `StartT1.ps1` e `StartT2.ps1`, são idênticos, o intuito é alterar apenas a variável `$StartType`.    
Assim o `StartT1.ps1` fica responsavel pelo agendamento 1 e o `StartT2.ps1` responsável pelo agendamento 2.  
Os scripts `StopT1.ps1` e `StopT2.ps1` seguem exatamente a mesma linha de raciocínio mencionada anteriormente.  
Caso precise de mais agendamentos basta duplicar o script renomeando (ex: StartT3.ps1) e alterando a variável `$StartType`.  
#### 5. Importa-los na conta de automação

Acesse:
Conta de automação >> Runbooks >> Adicionar um runbook >> Importar um runbook existente

Repita o processos para os demais scripts

![alt-tag](https://github.com/smartao/AzureAutomation-StopStart/blob/master/images/Imagem03.png)

![alt-tag](https://github.com/smartao/AzureAutomation-StopStart/blob/master/images/Imagem04.png)

#### 6. Publicá-los

Após importá-los é necessário publicá-los
Clique em um script >> Editar >> 

![alt-tag](https://github.com/smartao/AzureAutomation-StopStart/blob/master/images/Imagem05.png)

![alt-tag](https://github.com/smartao/AzureAutomation-StopStart/blob/master/images/Imagem06.png)

Caso precise é possível editar o runbook nessa tela.  

![alt-tag](https://github.com/smartao/AzureAutomation-StopStart/blob/master/images/Imagem07.png)

Runbooks após publicação.  

![alt-tag](https://github.com/smartao/AzureAutomation-StopStart/blob/master/images/Imagem08.png)

#### 7. Configurar o agendamento

O Agendamento deve ser configurado apenas para os scripts StartTx.ps e StopTx.ps.

![alt-tag](https://github.com/smartao/AzureAutomation-StopStart/blob/master/images/Imagem09.png)

![alt-tag](https://github.com/smartao/AzureAutomation-StopStart/blob/master/images/Imagem10.png)

#### 8. Adicionar tags nas VMs

```
Start : AutoT1
Stop : AutoT1
```

![alt-tag](https://github.com/smartao/AzureAutomation-StopStart/blob/master/images/Imagem11.png)

Caso exista muitas VMs criadas existe a possibilidade de adicionar as tags usando o Azure Cli pelo portal

Exemplo:  
```
# Substitua POC-Sergei pelo grupo de recursos que estão as VMs para atualizar a Tag
RG="POC-Sergei" 
for i in $(az vm list -g $RG --query "[].name" -o tsv)
do az vm update \
    --resource-group $RG \
    --name $i \
    --set tags.Start=AutoT1 tags.Stop=AutoT1 \
    --no-wait
done
```

Atenção:  
O runbook lê tods as VMs da assinatura, mesmo que estejam em grupo de recursos diferentes.  
Por isso adicione as tags somente nas VMs que precisam de automação.  

#### 9. Testes pós instalação

Executar o script manualmente para validar
Conta de automação >> Runbooks >> StartT1.ps1 >> Iniciar

Repetir o processo para o script StopT1.ps1

![alt-tag](https://github.com/smartao/AzureAutomation-StopStart/blob/master/images/Imagem12.png)

Acessar os jobs e verificar se houve erros

![alt-tag](https://github.com/smartao/AzureAutomation-StopStart/blob/master/images/Imagem13.png)

Fontes:  
[Azure Automation - Stop/Start Virtual Machine](https://www.youtube.com/watch?v=NCVeBYMQeLM)

