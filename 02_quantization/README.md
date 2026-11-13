# 02 Quantization study

Train a small CNN on CIFAR 10, then quantize that same trained model to 8 bit and 4 bit and compare all three.

• Notebook : `Quantization_FP32_vs_INT8.ipynb`
• Report : `02_New_Implementation_FP32_vs_INT8.docx`
• Work log : `27-10-2026.md`

## How to run

• Open in Colab, set the runtime to T4 GPU, run all.
• On CPU it trains on 12000 images instead of 50000 and the accuracy is lower.
• The first cell downloads CIFAR 10, about 170 MB.

## What the notebook does, in order

1. Pulls CIFAR 10. 50000 train photos, 10000 test photos.
2. Trains the CNN in 32 bit. 2.34 million weights.
3. Tests it on the 10000 test photos.
4. Quantizes that same trained model to 8 bit. Weights and activations. Real integer kernels.
5. Tests the 8 bit model on the same photos.
6. Quantizes to 4 bit. Weights only, codes packed two per byte.
7. Tests the 4 bit model on the same photos.
8. Compares the three. Accuracy, file size, CPU time, per class accuracy, weight histogram.

## Results, measured

| | 32 bit | 8 bit |
|---|---|---|
| test accuracy | 80.66 percent | 80.61 percent |
| file size | 9.38 MB | 2.36 MB |
| CPU per batch of 128 | 352 ms | 178 ms |
| weight storage | float32 | int8 |

• The two models agree on 98.2 percent of the 10000 test photos.
• 70 photos went right to wrong. 65 went wrong to right. Net effect, 5 photos.
• The 8 bit weights use 241 of the 256 possible levels.
• The difference is 0.05 points, well inside the noise for this many photos.

The 4 bit cells are in the notebook but were not run when this was written. The same 4 bit recipe scored 78.78 percent against a 81.20 percent baseline in the robustness study, so expect roughly 2 to 3 points of loss and a file around 1.18 MB.

## Extra cells

• A proof cell. Checks weight dtype, layer names, value counts, file size, CPU only kernels and 4 bit packing.
• A bit width sweep, 8 down to 2 bits, to show where accuracy breaks.
• A weights against activations test.
• A calibration test. The same 8 bit model fed wrong calibration photos.

## Notes

• Every model uses the same trained weights. No retraining anywhere.
• Every model sees the same test photos in the same order.
• Calibration uses training photos only, so the test set stays untouched.
• Runs are seeded and repeat.
