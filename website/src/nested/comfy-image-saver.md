# Comfy Image Saver

1. After recieving the image, you might want to save it somewhere first instead of immediately displaying it on a UI Image. To do this, you can either:
    1. Set up your own server and save it there.
    1. Save the image locally.
    
    For the sake of simplicity, let's go with saving locally.


1. Create a class called `ImageSaver`. Please make it a static class so you can access it anywhere without needing to attach it to the gameobject. My 1 brain cell made it a monobehaviour and I'm lazy to change it, so for all the functions show below, add `static` beforw `void`.

    ```c#
    Do:
    public static class ImageSaver {}

    NOT:
    public class ImageSaver : MonoBehaviour {}
    ```

1. We can then create a function called SaveImageToLocalDisk that takes in the texture and the filename of the image to save. To save a texture as a PNG, we have to encode it to PNG using `EncodeToPNG()` and store it within a `byte array`. We can then use `File.WriteAllBytes()` to save the image to the `persistentDataPath`.
    ```c#
    public void SaveImageToLocalDisk(Texture2D texture, string fileName)
    {
        if (texture == null)
        {
            Debug.Log("Nothing to save to local disk");
            return;
        }

        byte[] textureBytes = texture.EncodeToPNG();
        File.WriteAllBytes(Application.persistentDataPath + "_" + fileName, textureBytes);
        Debug.Log("Saved to local disk!");
    }
    ```

1. To check on the texutre, you can go to `c:/> users > yourUserHere > AppData > LocalLow > DefaultCompany`. If you do not see `AppData`, make sure File Explorer is `showing hidden files`by enabling the setting  under `... > Options > View > Hidden files and folders > Show hidden files, folders, and drives > Apply`.

1. To Load the texture, we can create a function called `GetTextureFromLocalDisk()`. The process is basically the same as saving it. But this time, I added a failsave where if the file doesn't exit in the `persistentDataPath`, I will instead load a texture stored in the `Resources` folder using `Resources.Load()`.
    ```c#
    public Texture2D GetTextureFromLocalDisk(string fileName)
    {
        byte[] textureBytes;
        Texture2D loadTexture;

        if (!File.Exists(Application.persistentDataPath + "_" + fileName))
        {
            loadTexture = Resources.Load<Texture2D>("FYP_Q3_AI_" + fileName);
            loadTexture.filterMode = FilterMode.Point;

            return loadTexture;
        }

        textureBytes = File.ReadAllBytes(Application.persistentDataPath + "_" + fileName);
        loadTexture = new Texture2D(0, 0);
        loadTexture.LoadImage(textureBytes);
        loadTexture.filterMode = FilterMode.Point;

        return loadTexture;
    }
    ```