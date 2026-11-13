# 01 First attempt

The first version of this work. It did not produce a quantized model. Kept because the defects are worth keeping.

• Notebook : `Untitled12.ipynb`
• Report : `01_Previous_Attempt_And_Defects.docx`
• Work log : `19-10-2026.md`

## What it tried to do

• Train a small model on images, quantize it to 8 bit, test both and compare.

## What went wrong

• An import of a file that did not exist stopped the first real run.
• A trick to hide the notebook environment failed, so it always ran the demo path on 1024 stripe images.
• The fallback dataset was drawn shapes, not photos. Two shapes are too easy to separate.
• The quantized copies got extra training, so two things changed at once.
• All arithmetic stayed in float. No 8 bit model was ever produced. No size and no speed numbers.
• The model expected 32 by 32 inputs and the data was 224 by 224.
• Most of the code was an attack suite. Nothing to do with quantization.

## Outcome

• 100 percent on stripe images. No usable result.
• The pipeline idea was kept and the code was rewritten from scratch in stage 02.
