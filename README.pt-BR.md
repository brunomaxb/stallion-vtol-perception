# Stallion VTOL Perception

[English](README.md) | **Português**

VANT de asa fixa VTOL impresso em 3D (Flightory Stallion), construído como plataforma de percepção aérea. A aeronave transmite vídeo e telemetria para uma estação terrestre, onde um modelo de detecção de objetos (YOLO) identifica as classes treinadas, como pessoas e veículos. As fases seguintes acrescentam detecções georreferenciadas e fusão de sensores.

Aplicações civis previstas: busca e salvamento, levantamento de áreas de risco (por exemplo, apoio à desminagem humanitária) e inspeção.

![Fuselagem montada](docs/images/airframe-assembled.jpg)

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
