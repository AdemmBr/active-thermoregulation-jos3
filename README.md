# Thermorégulation active (JOS-3)

Projet PRICE 39 avec Safran Tech (incubateur MicroPower).

[Français](#français) | [English](#english)

## Français

On simule un dispositif porté sur le dos qui chauffe ou refroidit (jusqu'à 50 W), piloté par un capteur de température cutanée. Le corps est modélisé avec [JOS-3](https://github.com/TanabeLab/JOS-3) (17 segments). L'idée est de savoir, avant de construire quoi que ce soit, où placer le capteur et si 50 W suffisent.

Trois profils où quelques dixièmes de degré comptent : un sportif qui surchauffe au soleil, une patiente atteinte de sclérose en plaques (une hausse de température aggrave les symptômes, c'est le phénomène d'Uhthoff) et un patient dialysé qui se refroidit pendant la séance. On mesure la peau parce que la température centrale ne se mesure pas avec un objet porté au quotidien.

Contrôleur : éteint entre deux seuils, sinon 5 W par 0,1 °C d'écart, plafonné à 50 W. Seuils à 33,0 / 34,8 °C, abaissés à 32,0 / 33,4 °C pour le patient SEP.

<p align="center"><img src="figures/loi_de_commande.png" width="70%" alt="Loi de commande"></p>

### Scénarios et résultats

| | Sportif (55 min) | Patient SEP (60 min) | Patient dialysé (190 min) |
|---|---|---|---|
| Risque | surchauffe | phénomène d'Uhthoff | refroidissement |
| Température centrale | 36,7 à 38,1 °C | max 37,36 °C | 37,1 à 36,6 °C |
| Dispositif | refroidit 41 min | refroidit 42 min | chauffe 181 min |
| Éteint | 14 min | 18 min | 9 min |
| Chaleur échangée* | ~18 Wh | ~12 Wh | ~148 Wh |
| Peau du dos | 29,9 °C min | 24,7 °C min | 45,4 °C max |

<sub>* Chaleur appliquée sur la peau, pas la consommation électrique, qui dépend du rendement du module Peltier.</sub>

\- Sportif : 50 W ne suffisent pas pour un effort intense au soleil.\
\- SEP : la température centrale reste sous 37,5 °C.\
\- Dialyse : 45 °C sur la peau pendant des heures, c'est un risque de brûlure. Il faudrait répartir la puissance sur plusieurs zones.

![Scénario sportif](figures/scenario_sport.png)
![Scénario SEP](figures/scenario_sep.png)
![Scénario dialyse](figures/scenario_dialyse.png)

### Où mettre le capteur

Corrélation entre la peau de chaque segment et la température centrale :

| | Meilleurs segments | Thorax (capteur actuel) |
|---|---|---|
| Sportif | main 0,94, pied 0,91 | 0,39 |
| SEP | main 0,92, pied 0,89 | 0,01 |
| Dialyse | pied 0,998, bras 0,995 | 0,91 |

Le thorax est un mauvais choix. Les mains et les pieds suivent mieux, mais ils bougent beaucoup et la corrélation ne tient compte ni du retard ni du bruit du capteur.

### Limites et suite

Simulation seulement, un seul segment chauffé/refroidi, seuils réglés à la main, pas de simulation de référence sans dispositif.

À faire : comparer à une simulation sans dispositif, répartir la puissance sur plusieurs zones, ajouter une coupure de sécurité sur la température de peau, filtrer le capteur et tester un régulateur PI.

### Lancer

```bash
git clone https://github.com/AdemmBr/active-thermoregulation-jos3.git
cd active-thermoregulation-jos3
pip install -r requirements.txt
jupyter notebook simulation_thermoregulation.ipynb
```

## English

We simulate a back-worn device that heats or cools (up to 50 W), driven by a skin temperature sensor. The body is modelled with [JOS-3](https://github.com/TanabeLab/JOS-3) (17 segments). The goal is to find out, before building anything, where to put the sensor and whether 50 W is enough.

Three profiles where a few tenths of a degree matter: an athlete overheating in the sun, an MS patient (a rise in temperature worsens symptoms, known as Uhthoff's phenomenon) and a dialysis patient who cools down during the session. We measure skin because core temperature can't be measured with an everyday wearable.

Controller: off between two thresholds, otherwise 5 W per 0.1 °C of deviation, capped at 50 W. Thresholds at 33.0 / 34.8 °C, lowered to 32.0 / 33.4 °C for the MS patient.

### Scenarios and results

| | Athlete (55 min) | MS patient (60 min) | Dialysis patient (190 min) |
|---|---|---|---|
| Risk | overheating | Uhthoff's phenomenon | cooling |
| Core temperature | 36.7 to 38.1 °C | max 37.36 °C | 37.1 to 36.6 °C |
| Device | cools 41 min | cools 42 min | heats 181 min |
| Off | 14 min | 18 min | 9 min |
| Heat exchanged* | ~18 Wh | ~12 Wh | ~148 Wh |
| Back skin | 29.9 °C min | 24.7 °C min | 45.4 °C max |

<sub>* Heat applied to the skin, not electrical consumption, which depends on the Peltier module's efficiency.</sub>

\- Athlete: 50 W isn't enough for hard effort in the sun.\
\- MS: core temperature stays below 37.5 °C.\
\- Dialysis: 45 °C on the skin for hours is a burn risk. Power should be spread over several zones.

### Where to put the sensor

Correlation between each segment's skin and core temperature:

| | Best segments | Chest (current sensor) |
|---|---|---|
| Athlete | hand 0.94, foot 0.91 | 0.39 |
| MS | hand 0.92, foot 0.89 | 0.01 |
| Dialysis | foot 0.998, arm 0.995 | 0.91 |

The chest is a poor choice. Hands and feet track better, but they move a lot, and the correlation ignores sensor lag and noise.

### Limitations and next steps

Simulation only, a single heated/cooled segment, hand-tuned thresholds, no baseline run without the device.

Next: compare against a run without the device, spread power over several zones, add a skin-temperature safety cutoff, filter the sensor and try a PI controller.

### Run

```bash
git clone https://github.com/AdemmBr/active-thermoregulation-jos3.git
cd active-thermoregulation-jos3
pip install -r requirements.txt
jupyter notebook simulation_thermoregulation.ipynb
```

### Reference

Takahashi, Y. et al. (2021). Thermoregulation model JOS-3 with new open source code. *Energy and Buildings*, 231, 110575.
