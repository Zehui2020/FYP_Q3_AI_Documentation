# ComfyUI Audio

1. ComfyUI also supports generative audio. You'll have to download the `stable_audio_open_1.0` [model](https://huggingface.co/stabilityai/stable-audio-open-1.0/tree/main) from Hugging Face. Unfortunately, due to fair use, you must have a Hugging Face account and take accountability for your actions while using this model.

1. Download the model `safetensor` file and place it under the `checkpoints` folder in ComfyUI. You can find this folder in `ComfyUI > models > checkpoints`.

1. Next up, you'll have to download the `t5-base` clip [model](https://huggingface.co/google-t5/t5-base/tree/main) from Hugging Face. Place this under the `clip` folder in ComfyUI. You can find this folder in `ComfyUI > models > clip`. 

1. You can return to the ComfyUI webpage and load this super basic [workflow](../others/audio.json) using the `Load` button on the sidebar. Make sure to assign the `t5-base` clip model in the `Load Clip` node, and the `stable_audio_open_1.0` model in the `Load Checkpoint` node.

1. Hit the `Queue Prompt` button and if you've done everything right, it should load a simple 90s melody consisting of snare drums and an electric piano!