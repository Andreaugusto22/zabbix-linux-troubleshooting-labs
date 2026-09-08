# 🚨 Cenário 2: Resolução de Indisponibilidade de Agente/Serviço (Zabbix Agent Down)

## 📌 Visão Geral
Simulação prática de atendimento a um incidente de monitoramento de infraestrutura. O sistema Zabbix detectou a perda de comunicação e interrupção do serviço do agente (`zabbix-agent`) em um servidor Linux. A investigação, análise do estado do serviço e a restauração da conectividade foram conduzidas via CLI (Terminal Linux).

---

## 🛠️ Tecnologias e Ferramentas Aplicadas
* Monitoramento: Zabbix Server 7.0 & Zabbix Agent
* Sistema Operacional: Ubuntu Linux Server (ARM64)
* Virtualização / Ambiente: UTM no macOS (Apple Silicon)
* Terminal & CLI: systemctl, status, start, killall

---

## 📋 Fluxo de Atendimento do Incidente

### 1. Detecção da Falha (Zabbix)
O Zabbix disparou uma trigger de criticidade Média/Alta informando que o agente parou de responder às checagens do servidor: `Zabbix agent is not available (for 3m)`.
<img width="1470" height="956" alt="Captura de Tela 2026-09-08 às 15 32 56" src="https://github.com/user-attachments/assets/b947d862-23e9-4816-89a4-ec99f86a292a" />


---

### 2. Investigação da Causa Raiz & Remediação (CLI Linux)
Acessando o servidor via terminal, foi realizada a validação do estado do daemon e o reestabelecimento do serviço:

1. Checagem do status do serviço:
   Comando: `sudo systemctl status zabbix-agent`
   Resultado: Serviço confirmado no estado `Active: inactive (dead)`.

2. Reativação do serviço e limpeza de processos de estresse:
   Comando: `sudo systemctl start zabbix-agent`
   Comando: `killall md5sum`

3. Validação do estado pós-intervenção:
   Comando: `sudo systemctl status zabbix-agent`
   Resultado: Serviço restaurado com sucesso para o estado `Active: active (running)`.


<img width="1470" height="956" alt="Captura de Tela 2026-09-08 às 15 34 52" src="https://github.com/user-attachments/assets/acfc30c9-4ec7-4d3b-ae4d-f336f6d0343d" />


---

### 3. Validação e Fechamento Automático (Zabbix)
Após a reativação do serviço no servidor, a coleta de métricas voltou a operar normalmente no ciclo seguinte do Zabbix, alterando o status do incidente para RESOLVIDO (OK).

<img width="1470" height="956" alt="Captura de Tela 2026-09-08 às 15 35 43" src="https://github.com/user-attachments/assets/b6dbe7fd-8597-444a-b063-5b069f819126" />


---

> ⚠️ Aviso de Boa Prática e Segurança em Produção:
> Em um ambiente de produção real, ao se deparar com um agente ou serviço essencial inativo, a restauração direta via `systemctl start` deve ser acompanhada de uma verificação prévia de causa raiz (análise de logs via `journalctl -u zabbix-agent -n 50`). 
> 
> Caso a queda do serviço tenha sido causada por falha de dependência, falta de memória (OOM Killer) ou manutenção agendada, é fundamental alinhar com o time responsável de Infraestrutura/NOC antes da reinicialização, garantindo a integridade dos logs de auditoria.

---

## 💡 Conclusão
Este cenário demonstrou a capacidade de isolar uma falha de comunicação entre o sistema de monitoramento e o host monitorado, diagnosticar o status do serviço inativo via terminal e restabilizar a coleta de métricas com rapidez e precisão operacional.
