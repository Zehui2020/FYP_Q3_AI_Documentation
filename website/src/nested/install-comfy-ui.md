# Install ComfyUI
ComfyUI will be the interface where you build your stable diffusion workflow. It also serves as a server (using localhost)!

1. Download ComfyUI using [this](https://github.com/comfyanonymous/ComfyUI/archive/refs/heads/master.zip)

1. Unzip the folder to somewhere easily accessible. I'd recommend unzipping the file to a drive that has at least 10-20 GB of free memory as some of the necessary files are quite large!

1. Double click on the `run_nvidia_gpu` batch file to start the ComfyUI server. If your computer doesn't have a NVIDIA GPU, you can use your CPU by running `run_cpu` instead, but be warned: the image generation time might take excrutiatingly long and the process is extremely taxing on your CPU!

    ![ ](../../images/run_nvidia_gpu.png)

1.  This should pull up a command prompt indicating the server is running. After a while, a webpage should open on your default browser. This will be the interface you'll be working with.

    ![ ](../../images/comfyui_default.png)