# Artillery Sidewinder X4 Plus - Configuration Klipper

Configuration Klipper personnalisée pour l'imprimante 3D **Artillery Sidewinder X4 Plus**.

![Artillery Sidewinder X4 Plus](https://www.artillery3d.com/cdn/shop/files/X4-PLUS-_1.png?v=1706257626&width=800)

## 📋 Description

Ce dépôt contient une configuration Klipper optimisée pour l'Artillery Sidewinder X4 Plus avec des macros supplémentaires pour améliorer l'expérience d'impression.

### Caractéristiques de l'imprimante :
- **Volume d'impression** : 310 x 310 x 405 mm
- **Carte mère** : STM32F401XC
- **Firmware** : Klipper (KLP_ARTILLERY)
- **Drivers** : TMC2209 (sensorless homing X/Y)
- **Extrudeur** : Direct Drive
- **Capteur de nivellement** : Probe inductif

## ✨ Fonctionnalités

| Fonction | Description |
|----------|-------------|
| ✅ **LED Control** | Contrôle de la LED du bâti via GPIO |
| ✅ **M600** | Changement de filament en cours d'impression |
| ✅ **Exclude Object** | Exclure un objet raté pendant l'impression |
| ✅ **Load/Unload Filament** | Chargement et retrait du filament automatisé |
| ✅ **Nozzle Cleaning** | Nettoyage automatique de la buse |
| ✅ **NeoPixel Status** | LED de statut pendant la chauffe |
| ✅ **Sensorless Homing** | Homing sans fin de course X/Y |
| ✅ **Input Shaper** | Réduction des vibrations (ADXL345) |

## 📁 Fichiers

```
├── printer.cfg              # Configuration principale
├── plr.cfg                  # Power Loss Recovery (Artillery)
├── MCU_ID.cfg               # ID du MCU
├── moonraker_obico_macros.cfg
├── lighton.sh               # Script LED ON
├── lightoff.sh              # Script LED OFF
└── README.md
```
<img width="896" height="280" alt="image" src="https://github.com/user-attachments/assets/7bfde7b0-460f-4075-9b4e-a29d4bedd562" />


## 🚀 Installation

### 1. Sauvegardez votre configuration actuelle

```bash
ssh root@<IP_IMPRIMANTE>
cp /home/mks/klipper_config/printer.cfg /home/mks/klipper_config/printer.cfg.backup
```

### 2. Copiez les fichiers

Téléchargez `printer.cfg` et remplacez-le via Fluidd/Mainsail ou SSH.

### 3. Configuration de la LED du bâti

Créez les scripts pour contrôler la LED :

**Éditez `/etc/rc.local`** et ajoutez avant `exit 0` :
```bash
echo 79 > /sys/class/gpio/export
echo out > /sys/class/gpio/gpio79/direction
echo 0 > /sys/class/gpio/gpio79/value
chown mks /sys/class/gpio/gpio79/value
```

**Créez `/home/mks/lighton.sh`** :
```bash
#!/bin/bash
/bin/echo 1 > /sys/class/gpio/gpio79/value
```

**Créez `/home/mks/lightoff.sh`** :
```bash
#!/bin/bash
/bin/echo 0 > /sys/class/gpio/gpio79/value
```

**Rendez-les exécutables** :
```bash
chmod +x /home/mks/lighton.sh
chmod +x /home/mks/lightoff.sh
```

### 4. Redémarrez

```bash
reboot
```

## 🎮 Macros disponibles

### Contrôle LED
| Commande | Description |
|----------|-------------|
| `LIGHT_ON` | Allumer la LED du bâti |
| `LIGHT_OFF` | Éteindre la LED du bâti |

### Gestion du filament
| Commande | Description |
|----------|-------------|
| `LOAD_FILAMENT` | Charger le filament (utilise temp actuelle ou 200°C) |
| `LOAD_FILAMENT TEMP=240` | Charger avec température spécifique |
| `UNLOAD_FILAMENT` | Retirer le filament |
| `UNLOAD_FILAMENT TEMP=240` | Retirer avec température spécifique |
| `M600` | Changement de filament (pause l'impression) |

### Nettoyage
| Commande | Description |
|----------|-------------|
| `nozzle_clean` | Chauffer et nettoyer la buse |
| `nozzle_wipe` | Nettoyer la buse (déjà chaude) |
| `draw_line` | Ligne de purge |

### Calibration
| Commande | Description |
|----------|-------------|
| `G29` | Calibration du bed mesh |
| `move_to_point_0` à `move_to_point_6` | Points de calibration manuelle |

### Impression
| Commande | Description |
|----------|-------------|
| `PRINT_START` | Début d'impression |
| `PRINT_END` | Fin d'impression |
| `PAUSE` | Mettre en pause |
| `RESUME` | Reprendre |
| `CANCEL_PRINT` | Annuler l'impression |

## ⚙️ Configuration Slicer

### Start G-code
```gcode
PRINT_START
```

### End G-code
```gcode
PRINT_END
```

### Exclude Object (obligatoire pour exclure des objets)

| Slicer | Paramètre |
|--------|-----------|
| **OrcaSlicer** | Activé par défaut ✅ |
| **PrusaSlicer** | Paramètres d'impression → Sortie → Étiqueter les objets |
| **Cura** | Extensions → Post-traitement → Exclude Objects |

## 📊 Spécifications techniques

```
Input Shaper:
  - X: ZV @ 59.4 Hz
  - Y: ZV @ 33.0 Hz

Accélération:
  - Max: 10000 mm/s²
  - Vitesse max: 1000 mm/s

Probe Z-offset: 0.420 mm
```

## ⚠️ Notes importantes

- Cette configuration est pour le firmware **Artillery Klipper** d'origine
- La mise à jour de Klipper peut casser l'écran tactile
- Le système utilise Armbian 22.05 avec des restrictions
- Le WiFi se configure via `/etc/wpa_supplicant/wpa_supplicant-wlan0.conf`

## 🔧 Dépannage

### La LED ne fonctionne pas
1. Vérifiez que `/etc/rc.local` a été modifié
2. Vérifiez les permissions des scripts `.sh`
3. Redémarrez l'imprimante

### Erreur "macro déjà enregistrée"
Certaines macros existent dans `plr.cfg` ou les fichiers inclus. Ne pas les dupliquer.

### Exclude Object ne fonctionne pas
Activez l'étiquetage des objets dans votre slicer.

## 📝 Changelog

### v1.0.0
- Configuration initiale
- Ajout contrôle LED bâti (GPIO 79)
- Ajout M600 changement filament
- Ajout Exclude Object
- Ajout LOAD/UNLOAD filament
- Toutes les macros originales Artillery

## 🙏 Crédits

- Configuration basée sur le firmware Artillery d'origine
- Aide à la configuration : Claude (Anthropic)
- Communauté Klipper

## 📄 Licence

Ce projet est sous licence MIT - voir le fichier [LICENSE](LICENSE) pour plus de détails.

---

**⭐ Si cette configuration vous aide, n'hésitez pas à mettre une étoile !**
