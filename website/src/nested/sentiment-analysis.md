# Sentiment Analysis

As our game features NPCs that are run by AI, we also wanted to make these NPCs feel more “alive” and “interactable”. We would accomplish this by giving our Shopkeeper NPC a “mood meter” to simulate changes in attitude towards the player depending on how they interact with the Shopkeeper.

Sentiment Analysis is the process of analyzing digital text to determine if the emotional tone of a message is `positive`, `negative` or `neutral`.

1. For our project, we used a Sentiment Analysis `Unity Package` that runs on a local database. There is no need to train or provide sentiment data, the Sentiment Analysis in the package is ready to use. Here’s the [link](https://assetstore.unity.com/packages/tools/sentiment-analysis-41832).

1. To setup the Sentiment Analysis, add the `SentimentAnalysis` script to the NPC as a component. Then create a copy of the provided `SendTextToAnalyse` script, rename it to `TextAnalysis` and attach it to the gameobject as well.

1. Modify the `PrintAnalysis()` method in `TextAnalysis` to feature a simple mood meter with defined thresholds for `positive`, `negative` and `neutral`.

1. Lastly, you can modify the text generation script mentioned in [Llama Integration](../nested/unity-integration-text.md) to call the `SendPredictionText()` method in `TextAnalysis` script.

After going through these steps, we managed to implement `Sentiment Analysis` into our Shop System for a `Bargaining System`, linking the prices of the Shop items to the changes in the Shopkeeper NPC’s “mood”. We also programmed the NPC’s sprite to change with the “mood”, simulating expressions! Pretty cool stuff!