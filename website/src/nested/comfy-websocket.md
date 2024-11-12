# Comfy Websocket

1. We'll need to define a few vairables first:
    1. The ComfyUI `serverAddress`. As mentioned in [ComfyPromptCtr](./comfy-prompt-ctr.md), since ComfyUI is running on local host `port 8188`, we can just hardcode this address for the sake of simplicity.

    1. The `clientID` for the client connecting to the server. This is required for each connection.

    1. The `client webSocket` connection will be used to establish a constant connection with ComfyUI.

    1. `ImageCtr` is referenced here to pass the image as reference once its recieved by the websocket.

    ```C#
    private string serverAddress = "127.0.0.1:8188";
    private string clientId = Guid.NewGuid().ToString();
    private ClientWebSocket ws = new ClientWebSocket();
    public ComfyImageCtr comfyImageCtr;
    public string promptID;
    ```

1. We'll modify the `Start()` to be an async operation and await for the connection to the websocket using the hardcoded `serverAddress` and `clientID`. 
1. We'll then call `StartListening()` to listen for any updates in the server.
    ```C#
    async void Start()
    {
        await ws.ConnectAsync(new Uri($"ws://{serverAddress}/ws?clientId={clientId}"), CancellationToken.None);
        StartListening();
    }    
    ```

1. `StartListening()` is an async function that creates a `buffer` to store the response from the comfyUI server. While the websocket is open and the response from the server has not ended yet, we'll keep `ReceiveAsync()` and storing it into the `buffer`.

1. We then check if the message is a close message, and if so, await a graceful shutdown of the websocket. Else, parse the message to string.

1. Upon getting the response form the server in string format, we can then check for the string `"queue_remaining: 0"`. This means that comfyUI has finished processing the image, which causes the queue to change from `1` to `0`.

1. Once we know ComfyUI is done, we can send the promptID to `ComfyImageCtr` for it to download and process the image.

    ```C#
    private async void StartListening()
    {
        var buffer = new byte[1024 * 4];
        WebSocketReceiveResult result = null;

        while (ws.State == WebSocketState.Open)
        {
            var stringBuilder = new StringBuilder();

            do
            {
                result = await ws.ReceiveAsync(new ArraySegment<byte>(buffer), CancellationToken.None);
                if (result.MessageType == WebSocketMessageType.Close)
                {
                    await ws.CloseAsync(WebSocketCloseStatus.NormalClosure, string.Empty, CancellationToken.None);
                }
                else
                {
                    var str = Encoding.UTF8.GetString(buffer, 0, result.Count);
                    stringBuilder.Append(str);
                }
            }

            while (!result.EndOfMessage);

            string response = stringBuilder.ToString();
            Debug.Log("Received: " + response);
        
            if (response.Contains("\"queue_remaining\": 0"))
            {
                comfyImageCtr.RequestFileName(promptID);
            }
        }
    }
    ```

1. To ensure the closure of the websocket when it's no longer needed (4e.g. when player exits game / switches scene), we close the websocket connection in `OnDestroy()`.
    ```C#
    void OnDestroy()
    {
        if (ws != null && ws.State == WebSocketState.Open)
        {
            ws.CloseAsync(WebSocketCloseStatus.NormalClosure, string.Empty, CancellationToken.None);
        }
    }
    ```

1. BONUS! Check the `response` string from ComfyUI, you are be able to create a really cool loading bar for the image generation in your project!