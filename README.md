🎯 Objetivo
Validar a performance do fluxo de compra de passagem aérea com sucesso no site BlazeDemo, garantindo que o sistema atenda aos critérios de aceitação definidos sob diferentes condições de carga.

🛒 Cenário de Teste
Fluxo de negócio: Compra de passagem aérea - Passagem comprada com sucesso

O cenário contempla as seguintes etapas:

Home - GET /
Acessa a página inicial do BlazeDemo
Validação: presença do texto "Welcome to the BlazeDemo"
Buscar Voos - POST /reserve.php
Envia origem (fromPort) e destino (toPort)
Validação: presença do texto "Flights from"
Extração: ID do voo selecionado
Selecionar Voo - POST /purchase.php
Envia o ID do voo escolhido
Validação: presença do texto "Your flight from"
Comprar Passagem - POST /confirmation.php
Envia dados do passageiro (nome, endereço, cartão de crédito, etc.)
Validação: presença do texto "Thank you for your purchase!"
✅ Critério de Aceitação
250 requisições por segundo (RPS) sustentadas
90º percentil (90th percentile) do tempo de resposta da transação de negócio < 2 segundos
Taxa de erro aceitável: < 1%
🛠 Ferramentas Utilizadas
Apache JMeter 5.6.3
Java JDK 8+ (requisito do JMeter)
Componentes nativos do JMeter:
Thread Group
HTTP Request Sampler
Transaction Controller
Response Assertion
Regex Extractor
Cookie Manager
Cache Manager
📁 Estrutura do Repositório
blazedemo-performance-tests/
├── jmeter/
│   ├── blazedemo_load_test.jmx          # Plano de teste de carga
│   └── blazedemo_spike_test.jmx         # Plano de teste de pico
├── results/
│   ├── load/
│   │   ├── results_load.jtl             # Resultados brutos do teste de carga
│   │   └── report/                      # HTML Dashboard do teste de carga
│   │       └── index.html
│   └── spike/
│       ├── results_spike.jtl            # Resultados brutos do teste de pico
│       └── report/                      # HTML Dashboard do teste de pico
│           └── index.html
├── screenshots/                          # Prints dos relatórios (opcional)
└── README.md                             # Este arquivo
⚙️ Configuração do Ambiente
1. Instalar o JMeter
Baixe o Apache JMeter em: https://jmeter.apache.org/download_jmeter.cgi
Extraia o arquivo ZIP/TGZ
Certifique-se de que o Java JDK 8+ está instalado:
bash

java -version
2. Verificar instalação
bash

cd apache-jmeter-5.6.3/bin
./jmeter --version
Saída esperada:

    _    ____   _    ____ _   _ _____       _ __  __ _____ _____ _____ ____
   / \  |  _ \ / \  / ___| | | | ____|     | |  \/  | ____|_   _| ____|  _ \
  / _ \ | |_) / _ \| |   | |_| |  _|    _  | | |\/| |  _|   | | |  _| | |_) |
 / ___ \|  __/ ___ \ |___|  _  | |___  | |_| | |  | | |___  | | | |___|  _ <
/_/   \_\_| /_/   \_\____|_| |_|_____|  \___/|_|  |_|_____| |_| |_____|_| \_\ 5.6.3
🏗 Arquitetura dos Testes
Componentes principais
Test Plan
User Defined Variables:
baseUrl: https://www.blazedemo.com
fromCity: Boston
toCity: London
HTTP Request Defaults
Domain: www.blazedemo.com
Protocol: https
Content Encoding: UTF-8
Timeouts: Connect 10s, Response 30s
Cookie Manager
Simula comportamento de navegador (sessões)
Cache Manager
Simula cache de navegador
Thread Group - Teste de Carga
Nome: TG_Load_Compra_Passagem
Objetivo: Simular carga sustentada próxima a 250 RPS
Configuração:
Number of Threads: 300 usuários
Ramp-Up Period: 300 segundos (5 minutos)
Loop Count: 100 iterações por usuário
Duração aproximada: 20–25 minutos
Thread Group - Teste de Pico
Nome: TG_Spike_Compra_Passagem
Objetivo: Simular pico abrupto de carga acima de 250 RPS
Configuração:
Number of Threads: 500 usuários
Ramp-Up Period: 30 segundos
Loop Count: 50 iterações por usuário
Duração aproximada: 8–10 minutos
Transaction Controller
Nome: TC_Compra_Passagem
Generate parent sample: Habilitado
Agrupa as 4 requisições HTTP do fluxo de compra
Assertions
Response Assertion em cada etapa para validar sucesso funcional
Extractors
Regex Extractor para capturar flightId dinamicamente
▶️ Execução dos Testes
Teste de Carga (Load Test)
Via linha de comando (recomendado)
bash

cd apache-jmeter-5.6.3/bin

./jmeter -n \
  -t ../../jmeter/blazedemo_load_test.jmx \
  -l ../../results/load/results_load.jtl \
  -e -o ../../results/load/report
Parâmetros:

-n: modo não-GUI (non-interactive)
-t: caminho do arquivo .jmx
-l: arquivo de saída com resultados (.jtl)
-e -o: gera HTML Dashboard na pasta especificada
Via GUI (apenas para validação inicial)
bash

./jmeter
File → Open → blazedemo_load_test.jmx
Ajustar parâmetros se necessário
Run → Start
Visualizar resultados em tempo real (não recomendado para testes longos)
Teste de Pico (Spike Test)
bash

cd apache-jmeter-5.6.3/bin

./jmeter -n \
  -t ../../jmeter/blazedemo_spike_test.jmx \
  -l ../../results/spike/results_spike.jtl \
  -e -o ../../results/spike/report
Visualizar Relatórios
Após a execução, abra os relatórios HTML:

Load Test: results/load/report/index.html
Spike Test: results/spike/report/index.html
📊 Relatório de Execução - Teste de Carga
Configuração Executada
Parâmetro	Valor
Número de usuários (threads)	300
Ramp-up	300 segundos (5 min)
Iterações por usuário	100
Duração total	~22 minutos
Cenário	Fluxo completo de compra
Resultados Principais
Nota: Os valores abaixo são exemplos. Substitua pelos valores reais obtidos no seu relatório HTML após a execução.

Métrica	Valor	Status
Throughput médio (plateau)	252 req/s	✅ Dentro do esperado
Throughput pico	268 req/s	✅ Dentro do esperado
90th percentile (TC_Compra_Passagem)	1.450 ms	✅ < 2.000 ms
95th percentile	1.780 ms	✅ < 2.000 ms
99th percentile	2.150 ms	⚠️ Ligeiramente acima
Tempo médio de resposta	980 ms	✅ Excelente
Taxa de erro	0,3%	✅ < 1%
Total de requisições	~330.000	-
Gráficos Relevantes
Throughput ao longo do tempo


Observações:

Ramp-up suave de 0 a 250 RPS nos primeiros 5 minutos
Plateau estável entre 245–260 RPS durante 15 minutos
Sem degradação progressiva
Response Time Percentiles


Observações:

50th percentile: ~850 ms
90th percentile: ~1.450 ms (dentro do critério)
95th percentile: ~1.780 ms (dentro do critério)
Erros ao longo do tempo


Observações:

Erros concentrados no início do ramp-up (timeouts de conexão)
Taxa de erro estabilizada em < 0,5% durante o plateau
Análise do Teste de Carga
Critério de aceitação:

✅ 250 requisições por segundo: Atingido (média de 252 req/s no plateau)
✅ 90th percentile < 2 segundos: Atingido (1.450 ms)
✅ Taxa de erro < 1%: Atingido (0,3%)
Conclusão:

Durante o período de plateau (minutos 5–20), o sistema manteve uma taxa média de aproximadamente 252 requisições por segundo, com o 90º percentil do tempo de resposta da transação de negócio TC_Compra_Passagem em 1.450 ms, bem abaixo do limite de 2.000 ms estabelecido no critério de aceitação.

A taxa de erro permaneceu em 0,3%, composta principalmente por timeouts esporádicos durante o ramp-up inicial, o que é esperado em testes de performance. Não houve tendência de degradação ao longo do tempo, indicando que o sistema é capaz de sustentar essa carga de forma estável.

O critério de aceitação do teste de carga foi SATISFATÓRIO.

📊 Relatório de Execução - Teste de Pico
Configuração Executada
Parâmetro	Valor
Número de usuários (threads)	500
Ramp-up	30 segundos
Iterações por usuário	50
Duração total	~8 minutos
Cenário	Fluxo completo de compra
Resultados Principais
Nota: Os valores abaixo são exemplos. Substitua pelos valores reais obtidos no seu relatório HTML após a execução.

Métrica	Valor	Status
Throughput médio (pico)	315 req/s	⚠️ Acima do critério (esperado)
Throughput pico	380 req/s	⚠️ Acima do critério (esperado)
90th percentile (TC_Compra_Passagem)	2.450 ms	❌ > 2.000 ms
95th percentile	3.200 ms	❌ > 2.000 ms
99th percentile	5.800 ms	❌ Degradação significativa
Tempo médio de resposta	1.580 ms	⚠️ Aceitável
Taxa de erro	4,2%	❌ > 1%
Total de requisições	~150.000	-
Gráficos Relevantes
Throughput ao longo do tempo


Observações:

Subida abrupta de 0 a 350+ RPS em 30 segundos
Pico sustentado entre 300–380 RPS por 5 minutos
Sistema sob stress significativo
Response Time Percentiles


Observações:

50th percentile: ~1.200 ms
90th percentile: ~2.450 ms (acima do critério)
95th percentile: ~3.200 ms (degradação evidente)
Erros ao longo do tempo


Observações:

Taxa de erro aumentou para 4–5% durante o pico
Erros: timeouts (60%), HTTP 500 (30%), HTTP 503 (10%)
Recuperação parcial após o pico
Análise do Teste de Pico
Critério de aceitação:

⚠️ 250 requisições por segundo: Ultrapassado propositalmente (média de 315 req/s)
❌ 90th percentile < 2 segundos: Não atingido (2.450 ms)
❌ Taxa de erro < 1%: Não atingido (4,2%)
Conclusão:

O teste de pico foi configurado propositalmente para estressar o sistema acima do critério de aceitação, atingindo picos de até 380 requisições por segundo. Sob essa carga extrema, observou-se:

Degradação do tempo de resposta: O 90º percentil subiu para 2.450 ms, ultrapassando o limite de 2 segundos.
Aumento da taxa de erro: A taxa de erro atingiu 4,2%, principalmente devido a timeouts e erros HTTP 500/503, indicando que o sistema começou a rejeitar requisições ou não conseguiu processá-las dentro do tempo limite.
Comportamento esperado: Essa degradação é esperada em um teste de pico (spike test), cujo objetivo é identificar os limites do sistema.
Interpretação:

O sistema demonstra capacidade de sustentar carga estável até aproximadamente 250–270 RPS com bom desempenho. Acima disso, especialmente em picos abruptos (300+ RPS), o desempenho degrada significativamente.

Para o cenário de pico, o critério de aceitação NÃO foi atendido, o que é aceitável e esperado, pois o objetivo deste teste é validar os limites do sistema, não o comportamento em condições normais de operação.

🎯 Análise e Conclusão
Resumo Executivo
Teste	Throughput Médio	90th Percentile	Taxa de Erro	Critério Atendido?
Carga (Load)	252 req/s	1.450 ms	0,3%	✅ SIM
Pico (Spike)	315 req/s	2.450 ms	4,2%	❌ NÃO (esperado)
Conclusão Final
O critério de aceitação foi SATISFATÓRIO para o teste de carga, que é o cenário principal de validação:

✅ 250 requisições por segundo foram sustentadas de forma estável
✅ 90º percentil < 2 segundos foi mantido durante todo o plateau
✅ Taxa de erro < 1% foi respeitada

Motivos que levaram a essa conclusão:

Estabilidade: O sistema manteve performance consistente durante 15 minutos de carga sustentada, sem degradação progressiva.
Tempo de resposta: O 90º percentil de 1.450 ms está 27,5% abaixo do limite de 2.000 ms, indicando margem de segurança.
Confiabilidade: Taxa de erro de 0,3% está bem abaixo do limite de 1%, com erros concentrados apenas no início do ramp-up.
Comportamento sob stress: O teste de pico revelou que o sistema começa a degradar acima de 300 RPS, o que é esperado e fornece informações valiosas sobre os limites operacionais.
Recomendações
Monitoramento em produção: Configurar alertas para quando o throughput ultrapassar 270 RPS ou o 90th percentile ultrapassar 1.800 ms.
Escalabilidade: Para suportar picos acima de 300 RPS, considerar:
Escalonamento horizontal (mais instâncias)
Otimização de queries de banco de dados
Implementação de cache
Testes futuros: Realizar testes de soak (longa duração) para validar comportamento ao longo de horas/dias.
💡 Considerações Finais
Limitações do Teste
Ambiente: Testes executados em ambiente local/controlado. Resultados podem variar em produção.
Dados: Utilizados dados sintéticos (mesmo cartão, mesmas rotas). Em produção, a variabilidade pode impactar performance.
Rede: Latência de rede não foi simulada. Em cenários reais, adicionar 50–200ms de latência.
Boas Práticas Aplicadas
✅ Modelagem por transação de negócio (Transaction Controller)
✅ Validações funcionais em cada etapa (Response Assertions)
✅ Correlação de dados dinâmicos (Regex Extractor para flightId)
✅ Simulação de comportamento de navegador (Cookie e Cache Managers)
✅ Separação clara entre teste de carga e teste de pico
✅ Geração de relatórios HTML profissionais
✅ Análise baseada em dados objetivos (percentis, throughput, taxa de erro)

Próximos Passos
 Executar testes em ambiente de staging/produção
 Integrar testes de performance no pipeline CI/CD
 Configurar monitoramento contínuo (APM)
 Realizar testes de endurance (soak test) com duração de 2–4 horas
 Testar com diferentes perfis de carga (variação de rotas, tipos de cartão, etc.)
