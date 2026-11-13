# CIFAR 10 quantization and robustness

What happens to a neural network when its numbers are rounded down to fewer bits, and how much that changes its behaviour under attacks and bad image quality.

Three stages, one folder each.

## Headline results

• 8 bit costs almost nothing. Accuracy 80.66 to 80.61 percent on 10000 test photos, file 9.38 MB to 2.36 MB, CPU time 352 ms to 178 ms for a batch of 128.
• 4 bit still works. About 1.18 MB, roughly 2 to 3 points of accuracy gone.
• 2 bit does not work unless the model is trained with the rounding switched on. 24 percent plain, 76 percent trained.
• Under attacks, 8 bit behaves like 32 bit, 4 bit loses a little, and the trained 2 bit model ended up the hardest one to break.

## Folders

• `01_first_attempt` : the first try. It failed. Kept for the record, with the defects report.
• `02_quantization` : the real study. A CNN, CIFAR 10, 8 bit and 4 bit versions.
• `03_robustness` : the same models tested under attacks, corruptions and resize. Follows the RobustMQ paper.

Each folder has its own README with the full results.

## How to run

• Everything runs in Google Colab, one notebook per stage.
• Use a GPU runtime. On CPU the training is smaller and the numbers drop.
• The first run downloads CIFAR 10, about 170 MB. Nothing to download by hand.

## What is inside

• Real 8 bit quantization, weights and activations, integer kernels on the CPU.
• Real 4 bit and 2 bit weight only quantization, codes packed two or four per byte.
• Quantization aware training for the low bit cases.
• Attacks, corruptions and resize tests, plus a transfer matrix between the models.
• A proof cell that checks the models really are quantized instead of trusting the printout.

## Limits

• CIFAR 10 and one small model. Not ImageNet, not a large architecture.
• One seed per run. Differences under about 1.2 points sit inside the noise.
• The low bit models store few bit weights but still compute in float, since PyTorch has no 4 bit or 2 bit CPU kernels.
