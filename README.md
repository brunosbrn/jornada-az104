# Implantação de Máquina Virtual no Azure

## 📘 Introdução

Este guia detalha o processo completo realizado para implantar uma máquina virtual (VM) no Microsoft Azure usando **uma conta gratuita**, sem recursos existentes. A configuração proposta contempla:

- Resource Group: `az104-rg8`
- Nome da VM: `az104-vm1`
- Região: **East US**
- Zonas de disponibilidade: 2 zonas
- Imagem: **Windows Server 2019 Datacenter - x64 Gen2**
- Tamanho: `Standard_D2s_v3`
- Disco do SO: **SSD Premium com redundância local (LRS)**
- Usuário administrador: `localadmin`
- Senha: gerada aleatoriamente e forte
- Portas públicas: **todas bloqueadas**
- Rede: nova VNET e sub-rede criadas no processo

---

## 📝 Pré-requisitos

- Conta Microsoft (Outlook, Hotmail, etc.)
- Cartão de crédito válido (para verificação, **sem cobranças**)
- Navegador web atualizado

---

## 🧾 Etapa 1 – Criar Conta Gratuita no Azure

1. Acesse [https://azure.microsoft.com/pt-br/free](https://azure.microsoft.com/pt-br/free)
2. Clique em **“Comece gratuitamente”**
3. Faça login com sua conta Microsoft
4. Insira os dados de verificação, aceite os termos e finalize o cadastro

---

## 📁 Etapa 2 – Criar o Resource Group

1. Acesse o [Portal do Azure](https://portal.azure.com)
2. No menu lateral, clique em **“Grupos de recursos”**
3. Clique em **“+ Criar”**
4. Preencha os campos:
   - **Assinatura**: Gratuita
   - **Nome**: `az104-rg8`
   - **Região**: **East US**
5. Clique em **“Revisar + criar”** e depois em **“Criar”**

---

## 💻 Etapa 3 – Criar a Máquina Virtual

### 🔹 Aba: Básico

- **Grupo de recursos**: `az104-rg8`
- **Nome da VM**: `az104-vm1`
- **Região**: East US
- **Zona de disponibilidade**: selecione **Zona 1** e **Zona 2**
- **Imagem**: Windows Server 2019 Datacenter - Gen2
- **Tamanho**: `Standard_D2s_v3` (2 vCPU, 8 GB RAM)
- **Usuário administrador**: `localadmin`
- **Senha**: Exemplo forte: Mv1!Secure#P@ss987
- **Portas de entrada pública**: Nenhuma

Clique em **Próximo: Discos >**

---

### 🔸 Aba: Discos

- **Tipo de disco do SO**: SSD Premium
- **Redundância**: LRS (Armazenamento com Redundância Local)

Clique em **Próximo: Rede >**

---

### 🔹 Aba: Rede

Como esta é uma conta nova, é necessário criar toda a infraestrutura de rede.

#### VNET:

- Clique em **“Criar novo”**
- Nome: `az104-vnet`
- Espaço de endereço: `10.0.0.0/16`
- Sub-rede padrão: `10.0.0.0/24`

#### Outras configurações:

- **Sub-rede**: será criada automaticamente como `default`
- **Endereço IP público**: automático
- **Grupo de segurança de rede (NSG)**: Padrão
- **Portas de entrada**: Nenhuma
- **Rede acelerada**: Desativada

Clique em **Próximo: Gerenciamento >**

---

### ⚙️ Aba: Gerenciamento

- **Monitoramento de diagnóstico**: Desativado (opcional)
- **Licenciamento**: Desmarcado (a menos que tenha licença existente)

Clique em **Próximo** até **Revisar + Criar**

---

## ✅ Etapa 4 – Revisar e Implantar

1. Verifique o resumo da configuração
2. Clique em **Criar**
3. Aguarde a implantação ser concluída (~5 minutos)

---

## 🔄 Etapa 5 – Alterar o Tamanho da VM (Família)

Caso você queira alterar a VM para a família `Standard_DS1_v2`, siga os passos abaixo:

1. Acesse o **Portal do Azure**
2. Navegue até **Máquinas Virtuais > az104-vm1**
3. **Pare a VM** clicando em **“Parar”**
4. Após parar, vá em **"Tamanho"** no menu lateral da VM
5. Selecione **`Standard_DS1_v2`**
 - 1 vCPU, 3.5 GB RAM, compatível com SSD Premium
6. Clique em **"Redimensionar"**
7. Após o redimensionamento, clique em **"Iniciar"** para ligar a VM novamente

> ⚠️ É necessário parar a VM antes de redimensionar.

---

## 💾 Etapa 6 – Adicionar um Disco de Dados à VM

Agora, vamos adicionar um disco de dados com as seguintes características:

- Nome: `vm1-disk1`
- Tipo: Standard HDD
- Tamanho: 32 GB

### Passos:

1. Acesse o **Portal do Azure**
2. Vá para **Máquinas Virtuais > az104-vm1**
3. No menu lateral, clique em **"Discos"**
4. Clique em **“+ Adicionar disco de dados”**
5. Configure:
 - **Nome**: `vm1-disk1`
 - **Tipo de disco**: HDD Standard
 - **Tamanho**: 32 GB
 - **Redundância**: LRS (padrão)
6. Clique em **Salvar**

### Inicializar Disco na VM (Windows):

Após adicionar o disco, conecte-se à VM (via RDP ou Azure Bastion) e siga:

1. Abra o **Gerenciamento de Disco**
2. Localize o novo disco (não alocado)
3. Inicialize como **GPT**
4. Crie um novo volume simples
5. Formate como **NTFS** e atribua uma letra de unidade (ex: `E:`)

---

## 🔄 Etapa 7 – Desanexar Disco de Dados da VM (sem excluir)

Em alguns cenários, pode ser necessário **remover o disco da VM sem excluir o disco em si**, preservando seus dados para uso futuro ou reanexação.

### Passos para desanexar:

1. Acesse o **Portal do Azure**
2. Vá para **Máquinas Virtuais > az104-vm1**
3. No menu lateral, clique em **"Discos"**
4. Localize o disco de dados `vm1-disk1` (não é o disco do SO!)
5. Clique no ícone de **lixeira (🗑)** ao lado do disco

> ⚠️ O botão de lixeira **apenas desanexa** o disco da VM. Ele **não exclui** o disco.

6. Confirme a operação

### Verificação:

- Após desanexar, o disco `vm1-disk1` continua visível em:
  > **Portal > Discos > vm1-disk1**

- Ele pode ser posteriormente:
  - Reanexado à mesma VM ou outra
  - Usado para backup, exportação ou análise

---

## 🔐 Considerações de Segurança

- Nenhuma porta pública foi exposta — acesso remoto não está habilitado
- Para acessar via RDP:
- Você pode habilitar a porta 3389 no NSG temporariamente
- Ou utilizar **Azure Bastion** (fora do escopo da conta gratuita)

---

## 💡 Dica de Gerenciamento

Monitore o uso de créditos acessando:  
**Portal > Custos e Faturamento > Créditos da Conta Gratuita**

---

## 🧾 Resumo dos Recursos Criados

| Recurso              | Nome           | Região   | Notas                                  |
|----------------------|----------------|----------|-----------------------------------------|
| Resource Group       | `az104-rg8`    | East US  | Agrupador de todos os recursos          |
| Máquina Virtual      | `az104-vm1`    | East US  | 2 zonas de disponibilidade              |
| Imagem               | Win 2019 Gen2  | -        | x64, Datacenter                         |
| Disco do SO          | Premium SSD    | -        | Redundância Local (LRS)                 |
| Disco de Dados       | `vm1-disk1`    | -        | 32 GB, Standard HDD                     |
| Tamanho da VM        | `Standard_DS1_v2` | -      | Após redimensionamento                  |
| Usuário da VM        | `localadmin`   | -        | Senha forte gerada                      |
| Rede Virtual (VNet)  | `az104-vnet`   | East US  | Endereço 10.0.0.0/16                    |
| Sub-rede             | `default`      | -        | Endereço 10.0.0.0/24                    |
| Segurança            | NSG padrão     | -        | Nenhuma porta pública liberada          |

---

## 📚 Referências

- [Documentação oficial do Azure](https://learn.microsoft.com/pt-br/azure/virtual-machines/)
- [Portal Azure](https://portal.azure.com)
- [Conta gratuita no Azure](https://azure.microsoft.com/pt-br/free)

---

> Criado para fins educacionais e laboratoriais. Ajustes adicionais podem ser necessários para ambientes de produção.
