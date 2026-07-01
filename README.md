# Qwen2-VL Fine-Tuning für Carotis-Ultraschall

## Projektbeschreibung
Dieses Projekt implementiert das Supervised Fine-Tuning (SFT) von Qwen2-VL-2B-Instruct
auf Carotis-Ultraschallbildern. Ziel ist die automatische Befundgenerierung:
- Schätzung der Intima-Media-Dicke (IMT) in mm aus B-Mode-Längsschnittbildern
- Klassifikation von atherosklerotischer Plaque (vorhanden/nicht vorhanden)

## Datensätze
| Datensatz | Quelle | Bilder | Labels |
|---|---|---|---|
| CUBS | Mendeley Data (DOI: 10.17632/m7ndn58sv6.1) | 500 | IMT-Wert in mm |
| Pahuni | Kaggle (pahunichoudhary) | 129 | Plaque ja/nein + klinische Variablen |

**Gesamt:** 629 Trainingsbeispiele (566 Training / 63 Validation, 90/10-Split)

## Modell & Methode
- **Basismodell:** Qwen2-VL-2B-Instruct (Qwen/Qwen2-VL-2B-Instruct)
- **Fine-Tuning:** QLoRA (Low-Rank Adaptation, r=16, alpha=32)
- **Trainierbare Parameter:** 4.358.144 von 2.213.343.744 (0,20%)
- **Epochen:** 3
- **Umgebung:** Google Colab (Tesla T4, 15 GB VRAM)

## Ergebnisse
| Epoche | Training Loss | Validation Loss |
|---|---|---|
| 1 | 6.823 | 6.704 |
| 2 | 6.724 | 6.591 |
| 3 | 6.674 | 6.580 |

**Qualitative Evaluation:**
- IMT-Klassifikation (normal/grenzwertig/erhöht): korrekt
- Plaque-Klassifikation: korrekt
- Numerischer IMT-Wert: Schätzung mit ±0.02–0.31 mm Abweichung

## Beispielausgabe
**Input:** Carotis-Ultraschallbild im Längsschnitt

**Frage:** "Analysiere dieses Carotis-Ultraschallbild im Längsschnitt. Wie hoch ist die Intima-Media-Dicke?"

**Modell-Antwort:** "Common Carotid Artery, B-Mode-Längsschnitt. Die Intima-Media-Dicke beträgt 0.88 mm und ist im Normbereich."

**Erwartete Antwort:** "Common Carotid Artery, B-Mode-Längsschnitt. Die Intima-Media-Dicke beträgt 0.86 mm und ist im Normbereich."

## Reproduktion
1. Notebook öffnen: `notebooks/qwen2_carotid.ipynb`
2. Colab Secrets: GitHub Token als `GITHUB_TOKEN` hinterlegen
3. Datensätze werden automatisch von Kaggle/Mendeley geladen
4. Basismodell wird automatisch von Hugging Face geladen
5. LoRA-Adapter liegen unter `model/lora_adapter/`

## Limitierungen
- Kleine Datenmenge (629 Beispiele) → eingeschränkte Generalisierbarkeit
- Numerische IMT-Werte sind Schätzungen, keine geometrischen Messungen
- Modell wurde nicht auf Differenzierung zwischen Carotis und V. jugularis interna trainiert
- Kein Conversion Factor (mm/Pixel) für Common-Carotid-Datensatz verfügbar
- Training auf Google Colab durchgeführt (keine lokale GPU verfügbar)

