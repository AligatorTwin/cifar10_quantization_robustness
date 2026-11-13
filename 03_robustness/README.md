# 03 Robustness study

The same models as the quantization study, hit with attacks, bad image conditions and resize.

• Notebooks : `Robustness_vs_Quantization.ipynb` (first run, results inside), `Robustness_vs_Quantization_v2.ipynb` (the full run)
• Work log : `02-11-2026.md`

The design follows the RobustMQ paper on the robustness of quantized models.

## What gets thrown at the models

• Attacks. FGSM and PGD at budgets 1/255, 2/255 and 4/255.
• Corruptions. 10 kinds, 3 levels. Gaussian noise, shot noise, impulse noise, defocus blur, motion blur, zoom blur, brightness, contrast, pixelate, fog.
• System noise. Resize with nearest, bilinear, bicubic and area.

## Metrics, taken from the paper

• ACC, clean accuracy
• AR, accuracy under one attack
• AAI, relative drop
• WCAR, accuracy when no attack in the set works
• mNR, mean accuracy over all corruptions
• NCI, relative corruption drop
• SR, spread of accuracy over the resize modes

## Full results, 5000 test photos, 7 models

| model | clean | WCAR 2/255 | AAI | mNR | NCI | SR |
|---|---|---|---|---|---|---|
| 32 bit | 81.20 | 11.66 | 85.64 | 46.70 | 42.49 | 5.46 |
| 8 bit PTQ | 81.14 | 11.94 | 85.28 | 46.51 | 42.68 | 5.41 |
| 8 bit QAT | 81.24 | 13.30 | 83.63 | 47.26 | 41.83 | 5.92 |
| 4 bit PTQ | 78.78 | 12.30 | 84.39 | 43.40 | 44.90 | 5.24 |
| 4 bit QAT | 81.40 | 11.74 | 85.58 | 47.47 | 41.69 | 6.10 |
| 2 bit PTQ | 24.12 | 5.78 | 76.04 | 18.27 | 24.27 | 1.79 |
| 2 bit QAT | 76.32 | 17.52 | 77.04 | 44.18 | 42.11 | 5.71 |

Noise floor for this run is 1.11 points. PTQ means quantized after training. QAT means trained with the rounding in the loop.

## What the results say

• 8 bit behaves like 32 bit in every family of noise.
• 4 bit loses 2.4 points clean and 3.3 points under corruptions. It is the only working model that clearly pays a price.
• 2 bit is not usable without training with the rounding on. 24 percent clean, and 76 percent once trained.
• The trained 2 bit model is the toughest against attacks. 17.52 percent survive, against 11.66 percent for the 32 bit model.
• The paper says fewer bits are harder to attack. That shows up for the trained models and not for the plain ones.
• The paper says quantized models suffer more under corruptions. That did not show up here. Only the plain 4 bit model was clearly worse.
• The trained 2 bit model shrugs off attacks made for the other models, 34 percent survive, but its own attack drops it to 17 percent. White box testing is needed or its robustness gets overstated.
• The 4 bit QAT model ended up the highest on clean accuracy, but 0.20 points above 32 bit is inside the noise. That is not an improvement.

## Two bugs found in the first run, both fixed in v2

• The transfer matrix diagonal printed 0.0 because it was never written.
• Bilinear resize scored worse than nearest, which was a grid alignment problem rather than a real result. Antialias was switched on.

## Notes

• Numbers come from this notebook's own training run, so the clean accuracies differ by a fraction from the quantization study.
• The first version ran on CPU with 1000 photos and a weaker model. It is kept because its bugs are documented in the code.
• The 8 bit models cannot be attacked with gradients, so their attacks are made on a float stand in that carries the dequantized weights. It agrees with the real model on 98.2 percent of images.
