# Unity Integration

1. To integrate llama.cpp into Unity, we will need to:
    1. Open a Command Prompt window through Unity  
    1. Access llama.cpp, and then the LLM through said window
    1. Take in User’s input, then feed the prompt into the LLM  
    1. Receive the LLM’s text output and display it back to User
    1. Account for previous context when prompting the AI

1. We'll be using a coroutine to handle the connection between Unity and llama.cpp.
    1. We need to define a few vairables:

        ```c#
            private Process process;
            private string previousContext;
        ```

    1. Create an IENumerator that takes in the user's prompt.
        ```C#
        private IEnumerator OpenCommandPrompt(string prompt)
        ```

    1. We have to ensure there is only 1 llama.cpp instance running at a time, or your device might experience some serious throttling. We'll therefore `Kill()` the previous process if it isn't completed yet. 

        ```C#
        if (process != null && !process.HasExited)
            process.Kill();
        string AI_Output = "";
        ```

    1. We then start a new process using `new ProcessStartInfo("cmd.exe", $"/c {command}")`. the `/c` command tells the Command Prompt window to terminate itself after executing the command. We'll also subscribe to the `OutputDataReceived` event which is invoked when the command window closes. Upon recieving the data, we'll store in the `AI_Output` string for parsing later. We then wait for the process to finish.
    
        ```C#
        ProcessStartInfo startInfo = new ProcessStartInfo("cmd.exe", $"/c {command}")
        {
            RedirectStandardOutput = true,
            RedirectStandardError = true,
            UseShellExecute = false,
            CreateNoWindow = true
        };

        process = new Process
        {
            StartInfo = startInfo,
        };

        process.OutputDataReceived += (sender, e) =>
        {
            if (e.Data != null)
                AI_Output += e.Data + "\n";
        };

        process.Start();
        process.BeginErrorReadLine();
        process.BeginOutputReadLine();

        while (!process.HasExited)
        {
            yield return null;
        }
        ```

    1. The output format of the `LLM` is: 
        ```
        <result>Output</result>
        ```
        We therefore have to perform some string manipulation to extract the actual `output` from this string.
        ```C#
        private string ExtractContent(string text)
        {
            string pattern = "<result>(.*?)</result>";
            Match match = Regex.Match(text, pattern);

            if (match.Success)
            {
                return match.Groups[1].Value;
            }
            else
            {
                return text;
            }
        }
        ```

    1. Upon recieving the result, we'll check if the AI had returned the `<result>` tag. We can then call `ExtractContent()` to get the output. We'll then store it inside `previousContext` to pass to the AI upon the next generation, this way the AI is able to pick up from where you left off.

        ```C#
        if (AI_Output.Contains("</result>"))
        {
            previousContext = ExtractContent(AI_Output);
            Debug.Log(previousContext);
        }
        ```