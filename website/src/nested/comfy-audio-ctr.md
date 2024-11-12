# ComfyAudioCtr

1. `ComfyAudioCtr` is very similar to `ComfyPromptCtr`, the only method that needs to be changed is the `DowloadImage()`. As of the time of writing this, you're unable to access and download the output audio using `http://127.0.0.1:8188/view?filename=output.flac`. You are therefore unable to use `webrequest` to download the audio in the web.

1. Thankfully, ComfyUI stores all the images & audios in an `output` folder locally. You can therefore use `webrequest` to download the audio from this folder. 
    1. There are some problems with this method, the most obvious one being the hardcoded file path to the `output` folder in ComfyUI. Move ComfyUI to another location and this method will instantly break.

    1. Other than the vairables found in [ComfyPromptCtr](./comfy-prompt-ctr.md), we'll need the filepath to ComfyUI's `audio output` folder. This can be found in `ComfyUI > output > audio`. 

    ```c#
    [SerializeField] private string comfyAudioFilePath;
    ```

    3. We'll have to then change `DowloadImage()` to `DownloadAudio()`. Instead of accessing a web URL, we tell the webrequest to access a file location instead by putting `"file://"` at the front of the filepath.
    
    ```c#
    private IEnumerator DownloadAudio(string imageUrl)
    {
        yield return new WaitForSeconds(0.5f);

        string fileName = ExtractFilenameFromURL(imageUrl);
        imageUrl = "file://" + comfyAudioFilePath  + "/"  + fileName;
        Debug.Log("Image URL: " + imageUrl);
        using (UnityWebRequest webRequest =  UnityWebRequestMultimedia.GetAudioClip(imageUrl, AudioType.UNKNOWN))
        {
            yield return webRequest.SendWebRequest();

            if (webRequest.result == UnityWebRequest.Result.Success)
            {
                // Get the downloaded texture
                AudioClip audio = ((DownloadHandlerAudioClip)webRequest.downloadHandler).audioClip;
                OnRecieveAudio?.Invoke(currentID, audio);
            }
            else
            {
                Debug.LogError("Audio download failed: " + webRequest.error);
            }
        }
    }
    ```

1. We'll have to make changes to [Image Saver](./comfy-image-saver.md) as well. Saving an `AudioClip` as `wav` is quite complicated. I'l just link the `SavWav` [class](../others/SavWav.cs) for you to look through. To save the audio to the `persistentDataPath`, call the `Save()` function.

1. To load the audio into the game, we'll have to use `webrequests` again, similar to what was done in `DownloadAudio()`.