# Pesquisa Script python na AWS

## **1. OBJETIVOS**

Esta pesquisa tem como objetivo encontrar a melhor forma de subir o código ponte do Mqtt Broker com o Banco de dados na AWS, tendo um gerenciamento automático.

## Pré-requisitos

- SO Linux e comandos, bem como conceitos como processos, threads e permissões de arquivos.

- Conceitos de nuvem e conceitos de rede IP (para redes públicas e privadas).

- Conceitos de computação distribuída, como HTTP como um IPC, filas, mensagens, notificações e simultaneidade.

- Familiaridade com os serviços e conceitos de segurança: AWS Identity and Access Management (IAM) e princípios de controle de acesso, e AWS Key Management Service (AWS KMS) e infraestrutura de chave pública.

- Familiaridade com os principais serviços que interagem com o Lambda: Amazon API Gateway, Amazon S3, Amazon Simple Queue Service (Amazon SQS) e DynamoDB.

- Configuração de instâncias do EC2 com Linux.

---

### **1. AWS Lambda ou EC2?**

- 1.1 O que é cada um?
- 1.2 Quando usar cada um?

### **2. Implementação**

- 2.1 Script na EC2

### **3. Conclusão**

### **4. Referências**

---

## **1. AWS LAMBDA ou EC2?**

### **1.1 O que é?**

O Lambda é um serviço de computação que permite que você execute o código sem provisionar ou gerenciar servidores. O Lambda executa seu código em uma infraestrutura de computação de alta disponibilidade e executa toda a administração dos recursos computacionais, incluindo manutenção do servidor e do sistema operacional, provisionamento e escalabilidade automática da capacidade e registro em log do código.

A instância EC2 é uma máquina virtual na nuvem, podendo rodar Amazon Linux, Ubuntu, Windows Server, Red Hat Enterprise Linux, SUSE Linux Enterprise Server, openSUSE Leap, Fedora, Fedora CoreOS, Debian, CentOS, Gentoo Linux, Oracle Linux e FreeBSD.

### **1.2 Quando usar cada um?**

O Lambda é o serviço de computação ideal para muitos cenários de aplicações, desde que você possa gravar o código da aplicação com o ambiente do tempo de execução padrão do Lambda e dentro dos recursos fornecidos pelo Lambda.

Ao usar o Lambda, você é responsável apenas pelo seu código. O Lambda gerencia a frota de computação que oferece um equilíbrio de memória, CPU, rede e outros recursos para executar seu código. Como o Lambda gerencia esses recursos, não é possível fazer login para calcular instâncias ou personalizar o sistema operacional noTempo de execução fornecido. O Lambda executa atividades operacionais e administrativas em seu nome, incluindo gerenciamento de capacidade, monitoramento e registro de suas funções do Lambda.

O problema é que as funções do AWS Lambda podem ser configuradas para execução por até 15 minutos de cada vez. Você pode definir o tempo limite em qualquer valor entre 1 segundo e 15 minutos. Então, ela é ideal pra aplicações simples, como mandar um e-mail caso aconteça alguma ação antes, ou executar algo rápido.

Para um scrip rodar constantemente, 24h por dia, o ideal é ser inicializado junto de uma EC2. Isso pode ser configurado no systemd do ubuntu, para que a aplicação seja inicializada quando a máquina ligar e caso haja alguma falha ou problema no código, é possível configurar para reinicialização.

## **2. IMPLEMENTAÇÃO**

Esta sessão é destinada a descrever o processo de configuração do script python no systemd, sistema operacional: ubuntu.

### **2.1 Script na EC2**

1. Salve seu sript python na sua maquina virtual;
2. `nano /etc/systemd/system/supix.service`
3. Dentro do arquivo supix.service:

```js
[Unit]
Description=Supervisor to LOG your system stats inside RAM.

[Service]
Type=simple
User=root
ExecStart=/usr/bin/python3 -u <Caminho do seu script>
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

3. Após a criação do arquivo de serviço basta iniciá-lo: `systemctl start supix`
4. Verifique o status: `systemctl status supix` se estiver ativo retornará `Active (running)`
5. Configuração do serviço para ser iniciado junto com o sistema: `systemctl enable supix`

## **3. CONCLUSÃO**

O objetivo da pesquisa foi atingido. Os resultados foram positivos em relação aos serviços pesquisados.

O AWS Lambda é um ótimo serviço, porém não é o ideal para a situação atual. É indicado para aplicações rápidas, como mandar um e-mail depois de alguma ação ou enviar uma notificação ao usuário e etc. No caso atual, é necessário que a aplicação fique a todo momento recebendo mensagens do broker e enviando para o banco.

Para isso, foi testado a implementação dentro da instância EC2. Para que o script começe a rodar junto com a inicalização da máquina foi configurado um serviço no systemd e em caso de falha ou erro no código, ele reinicia em 10 segundos.

O recurso da EC2 é ideal para esse tipo de aplicação.

## 3.1 REFERÊNCIAS

Amazon AWS: <https://aws.amazon.com/pt/ec2/faqs/>  
AWS Lambda: <https://docs.aws.amazon.com/lambda/latest/dg/welcome.html>  
Embarcados: <https://embarcados.com.br/systemd-adicionando-scripts-na-inicializacao-do-linux/>  
Simplified: <https://www.simplified.guide/linux/automatically-run-program-on-startup>
