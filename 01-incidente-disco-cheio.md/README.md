# 🚨 Cenário 1: Resolução de Incidente de Disco Cheio (High Disk Space Usage)

## 📌 Visão Geral
Simulação prática de atendimento a um incidente de infraestrutura. O sistema de monitoramento Zabbix identificou consumo crítico da partição raiz (/) em um servidor Linux Ubuntu. A investigação da causa raiz e a remediação foram realizadas via CLI (Terminal Linux) em um ambiente controlado (VM Linux no UTM).

---

## 🛠️ Tecnologias e Ferramentas Aplicadas
* Monitoramento: Zabbix Server 7.0 & Zabbix Agent
* Virtualização / Ambiente: UTM no macOS (Apple Silicon)
* Sistema Operacional: Ubuntu Linux Server
* Terminal & CLI: df, du, sort, rm

---

## 📋 Fluxo de Atendimento do Incidente

### 1. Detecção da Falha (Zabbix)
O Zabbix disparou uma trigger de criticidade Média/Alta alertando que o uso de disco da partição raiz superou 90% (Disk space is critically low).

<img width="1470" height="956" alt="Captura de Tela 2026-09-08 às 14 22 15" src="https://github.com/user-attachments/assets/ebdebc65-081c-4356-b79a-617853949433" />


---

### 2. Investigação da Causa Raiz & Remediação (CLI Linux)
Acessando o servidor via terminal, foi executada a seguinte sequência técnica para identificar e resolver a anomalia:

1. Checagem de uso do disco:
   Comando: `df -h /`
   Resultado: Partição `/dev/vda2` operando em 100% de capacidade ocupada.

2. Mapeamento dos maiores arquivos no diretório de logs (/var/log):
   Comando: `sudo du -sh /var/log/* | sort -rh | head -n 5`
   Resultado: Identificado o arquivo `teste_disco.img` ocupando 49GB de espaço.

3. Remoção e Purga do Arquivo Superfluo:
   Comando: `sudo rm -f /var/log/teste_disco.img`

4. Validação do Espaço Liberado:
   Comando: `df -h /`
   Resultado: Armazenamento normalizado para 22% de uso (47GB livres).

> ⚠️ **Aviso de Boa Prática e Segurança em Produção:**
> Em uma empresa real, apagar arquivos direto do servidor com o comando `rm -f` nunca deve ser a primeira opção sem antes alinhar com a equipe. 
> 
> Antes de deletar qualquer dado em um ambiente de produção, o procedimento correto envolve:
> 1. **Confirmar com a equipe/responsável:** Validar com o time de Aplicação, Sistemas ou com o líder do chamado se o arquivo/log pode ser removido sem afetar o sistema.
> 2. **Checar se o arquivo está em uso:** Garantir que nenhum serviço ativo dependa dele no momento.
> 3. **Fazer backup ou comprimir:** Mover o arquivo para um storage secundário ou compactar (`gzip`) para liberar espaço de forma segura.
> 
> *A exclusão direta neste teste foi feita apenas porque o arquivo foi criado por mim unicamente para simular o disco cheio no laboratório.*

<img width="1470" height="956" alt="Captura de Tela 2026-09-08 às 14 25 07" src="https://github.com/user-attachments/assets/2c41f265-2bb5-4a88-bdfb-b0d1a50d9b0f" />


---

### 3. Validação e Fechamento Automático (Zabbix)
No ciclo seguinte de checagem do Zabbix Agent, a métrica retornou aos parâmetros normais de operação, alterando o status do incidente para RESOLVIDO (OK).

<img width="1470" height="956" alt="Captura de Tela 2026-09-08 às 14 25 37" src="https://github.com/user-attachments/assets/6881cce4-bbe1-45b4-a7b5-ab2508410b09" />


---

## 💡 Conclusão
Esse cenário mostrou como identificar rapidamente um gargalo de disco via terminal e resolver a indisponibilidade. Mais do que executar comandos no Linux, a principal lição desse chamado é a importância de analisar a causa raiz com cuidado e alinhar as ações com o time antes de tomar qualquer medida destrutiva em servidores de produção.
