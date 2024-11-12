# Unity Integration
1. To communicate with ComfyUI through Unity, you'll need to save your current `workflow` as an `API`. This `API` is saved as a JSON file.

1. To save your current `workflow` as an `API`, click on the `Save (API Format)` on the sidebar. You can then rename this workflow and download it. 

    ![ ](../../images/save_api.png)

1. If you do not see this, go to `settings>Comfy>Enable Dev Mode`.

    ![ ](../../images/dev_mode.gif)

1. Opening the JSON file will reveal a huge chunk of text. This is your workflow in JSON format. Don't wory, the only 2 things you need to worry about for now are keys `7` and `8`. They are the `positive` and `negative` prompts respectively. Do note that the keys are directly related to the number of nodes you have in your `workflow`, so DO NOT use them as identifiers!

    ![ ](../../images/api_json.png)

1. Moving over to Unity, we'll be creating 3 scripts.
    1. `ComfyPromptCtr`: This script handles the queueing of prompts.
    1. `ComfyWebsocket`:  This script handles the websocket connection with ComfyUI.
    1. `ComfyImageCtr`: This script handles the recieving / processing of the resulting image.

1. Do note that this is my method of doing this. If you have a better method, feel free to use that instead.