# Teste técnico

## Saque PIX

O projeto se refere á uma plataforma de conta digital que permite o usuário
realizar saque PIX do saldo disponível na plataforma.

## 🛠 Tecnologias base a serem utilizadas:

```
Docker (https://docker.com)
PHP Hyperf 3 (https://hyperf.wiki)
Mysql 8
Mailhog (ou equivalente)
(quaisquer outros serviços que agregue na sua implementação. Descreva
no README.md o porque do uso no projeto)
```
## 💽 Tabelas do banco de dados:

```
account
id: string (uuid)
name: string
balance: decimal
(quaisquer outras colunas que agreguem na sua implementação)
```
```
account_withdraw
id: string (uuid)
account_id: string (uuid)
method: string
amount: decimal
scheduled: boolean
scheduled_for: datetime
```

```
done: boolean
error: boolean
error_reason: string
(quaisquer outras colunas que agreguem na sua implementação)
```
```
account_withdraw_pix
account_withdraw_id: string (uuid)
type: string
key: string
```
ℹ **Definir saldo disponível:** para adicionar saldo na conta, pode ser feito a
atualização diretamente no banco de dados do registro na tabela account.

### Fluxos

## 💵 Realizar saque

```
POST /account/{accountId}/balance/withdraw
Body: {
"method": "PIX",
"pix": {
"type": "email",
"key": "fulano@email.com"
},
"amount" 150.75,
// Define o agendamento do saque
// (null informa que o saque deve ocorrer imediatamente)
"schedule": null | "20260101 1500"
}
```
**Regras de negócio:**
A operação do saque deve ser registrado no banco de dados, usando as
tabelas account_withdraw e account_withdraw_pix.


```
O saque sem agendamento deve realizar o saque de imediato.
O saque com agendamento deve ser processado somente via cron (mais
detalhes abaixo).
O saque deve deduzir o saldo da conta na tabela account.
Atualmente só existe a opção de saque via PIX, podendo ser somente para
chaves do tipo email. A implementação deve possibilitar uma fácil expansão
de outras formas de saque no futuro.
Não é permitido sacar um valor maior do que o disponível no saldo da
conta digital.
O saldo da conta não pode ficar negativo.
Para saque agendado, não é permitido agendar para um momento no
passado.
Para saque agendado, não é permitido agendar para uma data maior que 7
dias no futuro.
```
## 📨 Enviar email de notificação

Após realizar o saque, deve ser enviado um email para o email do PIX,
informando que o saque foi efetuado. O template do email é irrelevante, a única
exigência é conter a data e hora do saque, o valor sacado e os dados do pix
informado.
Utilize um serviço de teste de email, por exemplo, Mailhog.

## ⏰ Processar saque agendado

Uma cron irá verificar se há saques agendados pendentes e fará o
processamento do saque.
Caso no momento do saque for identificado que não há saldo suficiente, deve
ser registrado no banco de dados que o saque foi processado, mas com falha
de saldo insuficiente.


## ⚠ Pontos de atenção

```
Desenvolva o projeto garantindo:
Performance.
Observabilidade.
Compatibilidade com escalabilidade horizontal.
Segurança.
É obrigatório que o projeto seja totalmente dockerizado:
Utilize o docker compose para compor os serviços utilizados.
Antes do envio deste projeto, teste a estrutura docker do zero para
garantir que nada ficou dependente do seu ambiente.
Quaisquer opiniões sobre decisões tomadas e/ou outras formas de
implementações, descreva-as no README.md do projeto.
```

