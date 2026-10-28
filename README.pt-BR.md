# Stallion VTOL Perception

[English](README.md) | **Português**

VANT de asa fixa VTOL impresso em 3D (Flightory Stallion), construído como plataforma de percepção aérea. A aeronave transmite vídeo e telemetria para uma estação terrestre, onde um modelo de detecção de objetos (YOLO) identifica as classes treinadas, como pessoas e veículos. As fases seguintes acrescentam detecções georreferenciadas e fusão de sensores.

Aplicações: reconhecimento tático, busca e salvamento (SAR), levantamento de áreas de risco (por exemplo, apoio à desminagem humanitária), monitoramento de desastres e inspeção.

<img src="docs/images/airframe-assembled.jpg" alt="Fuselagem montada" width="420">

## O problema

Operadores de drones de reconhecimento precisam encontrar pessoas e veículos no solo usando imagens de câmeras RGB, quase sempre em vídeo ao vivo, por longos períodos e muitas vezes com pouca luz ou clima ruim. Alvos pequenos, camuflados ou parcialmente encobertos passam despercebidos, e a fadiga do operador cresce com o tempo de missão.

A fusão de sensores, apoiada por um modelo de IA treinado para reconhecer esses padrões, dá ao operador um apoio decisivo em reconhecimento tático, busca e salvamento, levantamento de áreas de risco e monitoramento de desastres.

## Por que é importante

Sistemas autônomos e sistemas operados por pessoas serão empregados cada vez mais em conjunto com IA, ampliando a capacidade operacional de equipes pequenas a um nível que antes não era possível.

Na prática, isso significa varrer áreas maiores em menos tempo, perder menos alvos e entregar ao operador a localização do alvo, e não apenas a imagem. Em uma busca e salvamento, cada minuto economizado aumenta a chance de encontrar a pessoa com vida.

## Limitações das soluções atuais

- **Noite e pouca luz:** câmeras RGB perdem quase toda a capacidade de detecção.
- **Alvos pequenos:** em altitude, uma pessoa ocupa poucos pixels, e detectores treinados com imagens ao nível do solo (como o COCO) erram mais nessa escala.
- **Movimento:** alvos rápidos e a vibração da aeronave geram borrão e perda de rastreamento.
- **Falsos positivos:** sombras, rochas, vegetação e entulho são confundidos com pessoas ou veículos, e cada alarme falso precisa ser descartado pelo operador.
- **Clima adverso:** chuva, neblina, fumaça e poeira degradam a imagem.
- **Camuflagem e oclusão:** alvos sob vegetação ou parcialmente encobertos.
- **Sensor único:** a câmera térmica resolve parte do problema à noite, mas tem baixa resolução, sofre com o cruzamento térmico (quando alvo e fundo ficam na mesma temperatura) e não mostra cor nem textura.
- **Detecção sem localização:** a maioria das soluções aponta o alvo na tela, mas não entrega a coordenada no mapa.
- **Peso, energia e enlace:** plataformas pequenas não carregam computação embarcada pesada, e o vídeo transmitido sofre com banda e latência.
- **Carga do operador:** horas de vídeo ao vivo levam à fadiga e a alvos perdidos.

## Como o projeto aborda esses pontos

- Fusão do RGB com um segundo sensor (térmico ou LiDAR) para manter a detecção à noite e em condições ruins.
- Inferência na estação terrestre, o que permite modelos maiores sem pesar a aeronave.
- Fine-tuning com imagens aéreas próprias, focado em alvos pequenos vistos de cima.
- Rastreamento entre quadros para reduzir falsos positivos e acompanhar alvos em movimento.
- Georreferenciamento: cada detecção sai com latitude e longitude.
- Métricas medidas e publicadas a cada fase.

## Status

| Fase | Descrição | Status |
| --- | --- | --- |
| 0 | Fuselagem: impressão, montagem e acabamento | Concluída |
| 1 | Sistema elétrico e voo (manual + ajuste VTOL) | Em andamento |
| 2 | Enlace de vídeo e telemetria com a estação terrestre | Planejada |
| 3 | Detecção de objetos em solo (YOLO pré-treinado) | Planejada |
| 4 | Dataset aéreo próprio e fine-tuning | Planejada |
| 5 | Georreferenciamento das detecções (lat, lon) | Planejada |
| 6 | Fusão de sensores e rastreamento | Planejada |

Detalhes e critérios de conclusão de cada fase: [ROADMAP.md](ROADMAP.md). Diário de construção: [PROGRESS.md](PROGRESS.md).

## Plataforma

| Item | Valor |
| --- | --- |
| Fuselagem | Flightory Stallion, versão VTOL |
| Envergadura / comprimento | 1.340 mm / 990 mm |
| Layout VTOL | Tricóptero: motor dianteiro basculante + motores traseiros fixos |
| Peso de decolagem (AUW, publicado) | 2.000-3.000 g |
| Velocidade de cruzeiro (publicada) | 60-70 km/h |
| Material | LW-PLA; ASA nas peças que pedem mais resistência e tolerância ao calor |

## Componentes

Baseados na especificação da Flightory. A lista será atualizada conforme a instalação.

| Item | Escolha |
| --- | --- |
| Controladora de voo | SpeedyBee F405 |
| GPS | Matek M10Q |
| Motores | EMAX |
| Câmera | Moonlight com gimbal GM3 |
| Firmware | ArduPilot (QuadPlane, tilt-rotor) |

## Arquitetura

A inferência roda na estação terrestre, não a bordo. Assim a aeronave fica leve e o modelo pode ser maior. O diagrama está no [README em inglês](README.md#architecture).

Stack de software previsto: ArduPilot, Mission Planner / QGroundControl, pymavlink ou MAVROS, ROS 2, Python, PyTorch, Ultralytics YOLO, OpenCV.

## Métricas

- mAP@0.5 por classe em um conjunto de teste aéreo separado
- FPS de inferência no hardware da estação terrestre
- Latência ponta a ponta (câmera até a detecção na tela)
- Erro de georreferenciamento em metros, contra alvos com coordenada GPS conhecida
- Ganho da fusão de sensores em relação à câmera sozinha

## Segurança

Os voos de teste seguem a regulamentação local de UAS, em área autorizada, em linha de visada e com failsafe (RTL na perda de link) configurado. Os datasets publicados aqui não terão pessoas identificáveis.

## Créditos

Projeto da fuselagem: [Flightory](https://flightory.com).

## Autor

Bruno Barroso, mestrando em Inteligência Artificial na University of Colorado Boulder. [LinkedIn](https://www.linkedin.com/in/brunobarroso-ai/)
