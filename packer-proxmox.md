---
layout: default
---

# Proxmox + Packer = Sucesso

Usa o Proxmox na sua infra? Então bora criar um template do ubuntu usando o packer?

Primeiro faça um clone do repositório:

```bash
git clone https://github.com/bluesball/Packer.git
```

## Proxmox Config

Acesse seu servidor proxmox e crie um token de autenticação para um usuário que tenha permissão de manipular os recursos necessários para criar uma VM.
Para facilitar o teste voce pode usar o root.

Selecione Datacenter e clique em Permissions > API Tokens > Add

Salve o token secret pois ele irá aparecer apenas uma vez. É possivel gerar outro caso precise.

![Token](https://raw.githubusercontent.com/bluesball/Packer/main/imgs/add_token.png)

Verifique qual node irá usar para criar o template, é preciso informar o nome dele no arquivo ```proxmox.pkr.hcl```

Baixe a imagem do sistema operacional no storage local ou ceph destinado a isso.

## Packer Config

Adaptando o que for preciso para seu cenário

### Arquivo de variáveis

```bash
vars.pkr.hcl 
```

É preciso fazer algumas alterações:

* Alterar a URl do seu servidor proxmox
* Alterar o ID do token
* Informar o secret do token
* Altere as informações de rede de acordo com sua realidade

Este ambiente de teste foi preparado para usar IP estático, caso possua DHCP em sua rede, faça as alterações necessarias.

### Arquivo principal

```bash 
proxmox.pkr.hcl 
```

* Verifique o node em que irá gerar o template
* Informe corretamente o storage e nome da imagem a ser usada
* Revise as configurações de hardware
* Nos informes de rede do boot command, caso precise fixar o IP da sua estação de trabalho e porta, substitua os valores.
* Altere o caminho da chave privada.

Na seção provisioner é possível adicionar scripts para personalização da imagem.

### SSH

No Ubuntu 22.04 é preciso fazer ssh usando chaves pública/privada. Nos testes é usada uma chave sem senha.

```bash
ssh-keygen -t ed25519 -C "fulano@email.br" 
```

O caminho da chave privada deve ser informado no arquivo proxmox.pkr.hcl e o conteudo da chave publica no arquivo user-data

### Arquivo user-data

Este é um arquivo que define as configurações gerais do template a ser criado.

É nele que é informado o usuário a ser criado, definições de rede e repositorio de pacotes.

Verifique com atenção todas as opções.

### Provisioner

No final do arquivo está setado a execução do script ``` install.sh ```

```bash
    provisioner "shell" {
      script = "script/install.sh"

    }
```
Nele, deixei apenas o comando de instalação do docker, mas é possivel desenvolver o script para alcançar o objetivo do template.

```bash
#!/bin/bash
curl -fsSL https://get.docker.com | sh
```

Então: é isso, uma imagem básica de exemplo já possivel de ser criada com os arquivos do repositório.

Para aprender mais sobre o Packer:

* [Documentação Oficial](https://www.packer.io/docs)
* [Treinamento de Packer na LinuxTips](https://linuxtips.com.br/products/treinamento-descomplicando-o-packer)
* [Treinamento de IAC na LinuxTips](https://linuxtips.com.br/products/treinamento-infra-as-code-expert)

[Voltar](./)