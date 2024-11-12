# Comfy Image Ctr

1. We only need a reference to a UI image to display the output image.
    ```C#
    [SerializeField] Image outputImage;
    ```

1. We'll then create the `RequestFileName()` being called in [Comfy Prompt Ctr](./comfy-prompt-ctr.md). It takes in the promptID that is ready for image downloading and starts a coroutine for async purposes.

1. Inside the `RequestFileNameRoutine()` coroutine, we can hardcode the URL to access the `history` of the prompt (using the `promptID`). We can then create a `webrequest` to access the history using the URL and look for the output image. Once we successfully locate the history, we can then proceed to download the image.
    ```C#
    public void RequestFileName(string id)
    {
        StartCoroutine(RequestFileNameRoutine(id));
    }

    private IEnumerator RequestFileNameRoutine(string promptID)
    {
        string url = "http://127.0.0.1:8188/history/" + promptID;
        
        using (UnityWebRequest webRequest = UnityWebRequest.Get(url))
        {
            yield return webRequest.SendWebRequest();
            
            switch (webRequest.result)
            {
                case UnityWebRequest.Result.ConnectionError:
                case UnityWebRequest.Result.DataProcessingError:
                    Debug.LogError(": Error: " + webRequest.error);
                    break;
                case UnityWebRequest.Result.ProtocolError:
                    Debug.LogError(": HTTP Error: " + webRequest.error);
                    break;
                case UnityWebRequest.Result.Success:
                    Debug.Log(":\nReceived: " + webRequest.downloadHandler.text);
                    string imageURL = "http://127.0.0.1:8188/view?filename=" + ExtractFilename(webRequest.downloadHandler.text);
                    StartCoroutine(DownloadImage(imageURL));
                    break;
            }
        }
    }
    ```

1. To download the image, we first need a URL to said image through using the filename of the image. We have a function called `ExtractFilename()` just for that.

1. The result from the web request should look something like this:
    ```C#
    {
        ...
        "outputs": {
        "9": {
            "images": [
            {
                "filename": "ComfyUI_00449_.png",
                "subfolder": "",
                "type": "output"
            }
            ]
        }
        },
        ...
    }
    ``` 
1. We'll have to extract `"ComfyUI_00449_.png"` from this long result JSON in order to create a URL to download this image. Do note that you'll have to modify this function when using `controlnets`, more on that later. 
    1. First up, we'll use basic string manipulation to locate where `"filename": ` is. 

    1. We then find the end index, which we use `","` as the identifier. 

    1. We then extract the string between the end of `"filename": ` and before `","`.

    1. Lastly, we remove `""` from the string to get the filename and return it.

    ```C#
    string ExtractFilename(string jsonString)
    {
        string keyToLookFor = "\"filename\":";
        int startIndex = jsonString.IndexOf(keyToLookFor);

        if (startIndex == -1)
        {
            return "filename key not found";
        }

        startIndex += keyToLookFor.Length;
        string fromFileName = jsonString.Substring(startIndex);
        int endIndex = fromFileName.IndexOf(',');
        string filenameWithQuotes = fromFileName.Substring(0, endIndex).Trim();
        string filename = filenameWithQuotes.Trim('"');
        return filename;
    }
    ```

1. Once we have the filename to download, we can hardcode another URL: `"http://127.0.0.1:8188/view?filename="` and append the filename to the back of this string to get the complete URL to download the image.

    1. We then send a webrequest to download the image as a texture using `UnityWebRequestTexture.GetTexture()`. We then wait for the request to be sent.
    1. After getting a response, we can check if the webrequest was successful and if so, convert it to a `Texture2D` and assign it to the UI Image.

    ```C#
    private IEnumerator DownloadImage(string imageUrl)
    {
        yield return new WaitForSeconds(0.5f);

        using (UnityWebRequest webRequest = UnityWebRequestTexture.GetTexture(imageUrl))
        {
            yield return webRequest.SendWebRequest();

            if (webRequest.result == UnityWebRequest.Result.Success)
            {
                Texture2D texture = ((DownloadHandlerTexture)webRequest.downloadHandler).texture;
                outputImage.sprite = Sprite.Create(texture, new Rect(0, 0, texture.width, texture.height), Vector2.zero);

            }
            else
            {
                Debug.LogError("Image download failed: " + webRequest.error);
            }
        }
    }
    ```