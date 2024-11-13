# Lora
1. LoRA (Low-Rank Adaptation) is another training technique for fine-tuning Stable Diffusion models. The benefit of LoRAs are their relatively small file sizes compared to `controlnet` and `checkpoint` models, while offering decent training power.

1. Unforunatley, unlike `checkpoint models`, LoRAs cannot be used on their own. Similar to `controlnet`, it's added on top as fine tuning. You can find many LoRA models on [CivitAI](https://civitai.com/models).

1. To make your output image take on a pixel art style, you can try out [this](https://civitai.com/models/120096/pixel-art-xl) LoRA and place it in the `loras` folder under `ComfyUI>models>loras`. IMPORTANT! Similar to ControlNet, LORAs depend on the version of your `checkpoint` model, do remember this when downloading LoRAs online.

1. To apply a LoRA in ComfyUI, you need to add the `Load LoRA` node to your workflow. Connect the `MODEL` output of `Load Checkpoint` to the `model` input of the `Load Lora`. Do the same with the `CLIP` input of `LoadLora`. Then link the output to `model` input in `KSampler`.

    ![](../images/setup_lora.gif)

1. Upon pressing `Queue Prompt`, you'll notice the output image looking kind of like pixel art. Unfortunately this LoRA isn't the best and the results may vary.

## More Info
To learn more about LoRAs and how they function, refer to [this](https://stable-diffusion-art.com/lora/)