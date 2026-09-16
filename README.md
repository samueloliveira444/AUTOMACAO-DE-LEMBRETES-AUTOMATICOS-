# AUTOMACAO DE LEMBRETES AUTOMATICOS

Sistema de lembretes automáticos por WhatsApp que reduz faltas (no-show) em negócios que trabalham com agenda. Monitora os horários de todos os profissionais a cada hora e envia lembretes personalizados no momento certo — 24h, 12h e 2h antes de cada atendimento.

**Em produção hoje com clientes reais** em nichos diferentes: clínica de saúde (Updents), autoescola, petshops e barbearia. O mesmo core serve qualquer negócio baseado em agenda.

## O problema

Falta em agendamento é um dos maiores custos invisíveis de negócios que vendem horário. Clínica perde consulta, autoescola perde aula, barbearia perde cadeira vazia. Lembrete manual não escala, e lembrete genérico ("Você tem horário amanhã") tem taxa de resposta baixa. O resultado é agenda furada e dinheiro deixado na mesa.

## Como funciona

**Monitoramento contínuo**
A cada hora, o sistema verifica as agendas de hoje e de amanhã de todos os profissionais ativos.

**Detecção inteligente dos momentos certos**
Para cada atendimento, calcula exatamente quando enviar: 24h, 12h ou 2h antes. Ignora horários fora do expediente, não envia na madrugada e descarta entradas que não são clientes (feriados, almoço, bloqueios de agenda).

**Mensagens geradas por IA — não templates fixos**
Cada lembrete é escrito por IA na hora, com tom humano e cordial. Cada tipo tem uma função:
- **24h antes:** pede confirmação de presença (o cliente responde "Ok" e o negócio sabe que ele vem)
- **12h antes:** lembrete gentil, sem exigir resposta
- **2h antes:** mensagem de acolhimento ("já estamos te esperando")

**Personalização por profissional**
A IA adapta o estilo da mensagem conforme o tipo de serviço — psicólogo não recebe emoji de dente, barbeiro não recebe emoji de pet. Parece detalhe, mas é o que faz o cliente ler a mensagem como humana.

**Anti-duplicata garantido**
Cada lembrete recebe um ID único registrado num log. Se o sistema rodar duas vezes no mesmo intervalo, ninguém recebe mensagem repetida.

## Onde está rodando

- **Clínica de saúde** (multiespecialidades) — lembretes de consulta entre dentistas, médicos, psicólogo, acupuntura
- **Autoescola** — lembretes de aulas teóricas e práticas
- **Petshops** — banho, tosa e consultas veterinárias
- **Barbearia** — cortes e horários agendados

Em cada nicho, o que muda é a fonte da agenda e o tom das mensagens. O motor é o mesmo.

## Stack

- n8n (orquestração)
- LLM via OpenRouter — Claude Haiku (escolhido por custo/benefício pra tarefa de escrita)
- Evolution API (WhatsApp)
- API do sistema de agenda do cliente (substituível — já integrei com sistemas diferentes)
- Google Sheets (log de envios / anti-duplicata)

## Decisões técnicas

- **Fuso horário tratado explicitamente** — todos os cálculos usam America/Sao_Paulo, evitando o clássico bug de automação que dispara na hora errada
- **Envios suprimidos entre 22h e 7h** — nenhum cliente é acordado por lembrete
- **Blacklist de termos** — agendas reais têm lixo (FERIADO, ALMOÇO, BLOQUEIO). O sistema ignora antes de processar
- **Modelo de IA econômico** — pra escrita de mensagens curtas, um modelo leve resolve. Custo baixo em escala
- **Tratamento de falhas** — falhas em APIs externas não derrubam o fluxo
