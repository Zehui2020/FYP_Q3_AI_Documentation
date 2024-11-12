# Comfy Prompt Ctr

1. We'll need to define a few vairables first:
    1. `Response Data` is a `holder class` for the `prompt_id` of the current prompt ComfyUI is generating. This is for tracking purposes.
    1. `promptJson` is a `string` that stores the API JSON mentioned in [Unity Integration](../unity-integration.md).
    1. `pInput` and `nInput` are unity Input Fields for taking the player's input.
    ```C#
    [System.Serializable]
    public class ResponseData
    {
        public string prompt_id;
    }

    [SerializeField] private string promptJson;
    [SerializeField] private InputField pInput, nInput;
    ```

1. Next, we create a function to call to queue the prompt. We'll also be making use of Unity's `web request` to upload the JSON API to ComfyUI `asynchronously` through a coroutine. 
    1. Since ComfyUI runs on port 8188 on local host, we can hard code the web URL to `http://127.0.0.1:8188`. We can then add `/prompt` at the back to access the prompt queue in ComfyUI.
    
        ```C#
        private string GeneratePromptJson()
        {
            string guid = Guid.NewGuid().ToString();
            string promptJsonWithGuid = $@"
            {{
                ""id"": ""{guid}"",
                ""prompt"": {promptJson}
            }}";

            return promptJsonWithGuid;
        }
        ```

    1. We'll also create a function that generates a random GUID to be sent to ComfyUI. This will be the `promptID` for each prompt. This is to make identifying one prompt from another easily. This function will then format it as JSON and return the string.

    1. We then modify the Positive and Negative prompts in the JSON API to `Pprompt` and `Nprompt` respectively. This is crucial as we'll be using `string.Replace()` to manipulate the JSON API string to add in the player's own Positive and Negative prompts.

    1. We then create a web request and send the JSON API to the prompt queue in ComfyUI. Upon getting the confirmation, ComfyUI will queue the prompt and send back the `prompt_id` in JSON format, which we can format and parse it using ResponseData and then assign it to the websocket's `promptID`.

        ```C#
        public void QueuePrompt()
        {
            StartCoroutine(QueuePromptCoroutine(pInput.text,nInput.text));
        }

        private IEnumerator QueuePromptCoroutine(string positivePrompt,string negativePrompt)
        {
            string url = "http://127.0.0.1:8188/prompt";
            string promptText = GeneratePromptJson();
            promptText = promptText.Replace("Pprompt", positivePrompt);
            promptText = promptText.Replace("Nprompt", negativePrompt);
            Debug.Log(promptText);

            UnityWebRequest request = new UnityWebRequest(url, "POST");
            byte[] bodyRaw = System.Text.Encoding.UTF8.GetBytes(promptText);
            request.uploadHandler = (UploadHandler)new UploadHandlerRaw(bodyRaw);
            request.downloadHandler = (DownloadHandler)new DownloadHandlerBuffer();
            request.SetRequestHeader("Content-Type", "application/json");

            yield return request.SendWebRequest();

            if (request.result != UnityWebRequest.Result.Success)
            {
                Debug.Log(request.error);
            }
            else
            {
                Debug.Log("Prompt queued successfully." + request.downloadHandler.text);
                ResponseData data = JsonUtility.FromJson<ResponseData>(request.downloadHandler.text);
                GetComponent<ComfyWebsocket>().promptID = data.prompt_id;
            }
        }
        ```