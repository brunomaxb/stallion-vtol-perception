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

## Contribuição potencial

- **Um pipeline de referência aberto, de ponta a ponta:** fuselagem, controle de voo, enlace de vídeo e telemetria, detecção, rastreamento e georreferenciamento, documentados e reproduzíveis em hardware de baixo custo.
- **Evidência sob restrições reais:** FPS, latência e erro de georreferenciamento medidos para métodos publicados (inferência por recortes, fusão RGB-térmico, rastreamento) rodando em um VTOL de baixo custo e numa estação terrestre, e não só em GPUs de benchmark.
- **Dados aéreos novos:** um dataset próprio, de terrenos e condições pouco representados nos benchmarks públicos, publicado sem pessoas identificáveis.
- **Saída pensada para o operador:** alertas com coordenadas no mapa em vez de vídeo bruto, para reduzir a carga em missões longas.
- **Lições aplicáveis** a reconhecimento tático, busca e salvamento, levantamento de áreas de risco e resposta a desastres.

## Trabalhos relacionados

As tabelas comparativas completas, com links, estão no [README em inglês](README.md#related-work). Resumo por categoria:

- **Datasets e benchmarks aéreos:** [VisDrone](https://arxiv.org/abs/2001.06303) (RGB, cidades, detecção e rastreamento), [HIT-UAV](https://arxiv.org/abs/2204.03245) (2.898 imagens térmicas a 60-130 m), [DroneVehicle](https://arxiv.org/abs/2003.02437) (28.439 pares RGB-infravermelho de veículos), [WiSARD](https://arxiv.org/abs/2309.04453) (~56 mil imagens visuais e térmicas para busca e salvamento) e [AIR no HERIDAL](https://arxiv.org/abs/2111.09406) (detecção de pessoas em busca e salvamento). Parecidos por tratarem de alvos pequenos vistos de cima; diferentes por serem dados ou detecção offline, quase sempre com um único sensor e sem georreferenciamento.
- **Detecção de alvos pequenos:** [SAHI](https://arxiv.org/abs/2202.06934) (inferência por recortes, até +14,5% de AP no VisDrone/xView) e [TPH-YOLOv5](https://arxiv.org/abs/2108.11539) (cabeças para objetos pequenos e atenção). Serão testados nas fases 3 e 4, medindo o custo em FPS.
- **Fusão RGB-térmico:** [UA-CMDet](https://arxiv.org/abs/2003.02437) pondera cada modalidade pela iluminação e pela incerteza. É a referência da fase 6, aqui aplicada também a pessoas.
- **Rastreamento:** [ByteTrack](https://arxiv.org/abs/2110.06864) (80,3 MOTA no MOT17 a ~30 FPS) recupera alvos ocultos usando detecções de baixa confiança. Rastreador previsto na fase 6.
- **Georreferenciamento de alvos:** [Pan et al., Drones 2023](https://www.mdpi.com/2504-446X/7/2/87) triangulam alvos em movimento com câmera monocular (erro de ~2,9 m a 50 m de altitude em simulação). Linha de base da fase 5.
- **Plataformas e ferramentas:** [ArduPilot](https://ardupilot.org), [Ultralytics YOLO](https://docs.ultralytics.com), [ROS 2](https://docs.ros.org) e MAVLink; fuselagem [Flightory Stallion](https://flightory.com).

### Como o projeto se apoia nesses trabalhos

- **Reaproveita:** datasets públicos para pré-treino e comparação (VisDrone, HIT-UAV, WiSARD), SAHI para alvos pequenos, ByteTrack para rastreamento e ArduPilot para o controle de voo.
- **Compara:** cada método contra um YOLO de base nos mesmos dados, mostrando lado a lado precisão e custo (FPS e latência).
- **Amplia:** une detecção, fusão, rastreamento e georreferenciamento em um único sistema voando em hardware de baixo custo, enquanto a maioria dos trabalhos estuda uma etapa isolada e offline.
- **Gera conhecimento novo:** medições em voo real, um dataset aéreo próprio e documentação aberta do que funcionou e do que não funcionou.

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
