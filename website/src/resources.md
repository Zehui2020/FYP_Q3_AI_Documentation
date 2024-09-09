# Resources
## Useful Links
1. [Hugging Face](https://huggingface.co/models) is a website / community of developers creating AI related software for the community to use! This includes `Checkpoints`, `LORAs`, `Control Nets`, `Workflows` and many more!

1. [CivitAI](https://civitai.com/models) is similar to [Hugging Face](https://huggingface.co/models), but is more specialized for sharing `Checkpoints`and `LORAs`.

## Terminology
### ComfyUI

| Name  | Description                                                                                                                                                                 |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Nodes` | A node is a visual unit that performs a specific task, connecting to other nodes via input/output ports to define a system's logic or behavior.                             |
| `Workflow` | A workflow is a series of connected nodes that define the step-by-step process for generating an image, specifying how data flows between various operations to achieve the desired output. |
| `Checkpoints` | A checkpoint refers to an AI model used for generating images, which processes input data and produces visual outputs based on the training data and algorithms embedded in the model. |
| `Prompts` | There are 2 common prompt types. Positive and Negative prompts. The text in the positive prompt determines how the final result should look like. The text in the negative prompt determines what the AI Model should avoid in the final image. Both are used to guide the AI to acheive the best result. |
| `Control Net` | ControlNet is an extension that allows users to guide image generation more precisely by providing additional control inputs, such as sketches or depth maps, ensuring that the output adheres more closely to a specified structure or composition. |
| `LORA` | LoRA (Low-Rank Adaptation) is a technique that enables fine-tuning of large AI models with minimal additional data, allowing users to adjust or personalize the model's behavior for specific tasks or styles without retraining the entire model. |