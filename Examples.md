## ✨ LanguageDetection

This project shows how to download already trained model to detect languages.
There are to Language Detector algorithms : cld2 and fastText.

```csharp
var cld2LanguageDetector     = await LanguageDetector.FromStoreAsync(Language.Any, Version.Latest, "");
var fastTextLanguageDetector = await FastTextLanguageDetector.FromStoreAsync(Language.Any, Version.Latest, "");
```

It then takes somes short or long texts (defined in Data.cs) and try to match the language of these texts with the model.

```csharp
foreach (var (lang, text) in Data.ShortSamples)
{
    var doc = new Document(text);
    fastTextLanguageDetector.Process(doc);

    var doc2 = new Document(text);
    cld2LanguageDetector.Process(doc2);

    Console.WriteLine(text);
    Console.WriteLine($"Actual:\t{lang}\nFT:\t{doc.Language}\nCLD2\t{doc2.Language}");
    Console.WriteLine();
}
```

You can also access all predictions via the Predict method:

```csharp
var allPredictions = fastTextLanguageDetector.Predict(new Document(Data.LongSamples[Language.Spanish]));
            
Console.WriteLine($"\n\nTop 10 predictions and scores for the Spanish sample:");
foreach (var kv in allPredictions.OrderByDescending(kv => kv.Value).Take(10))
{
    Console.WriteLine($"{kv.Key.ToString().PadRight(40)}\tScore: {kv.Value:n2}");
}
```

## ✨ EntityRecognition

This project shows hwo to recognize some entities in a text.

Catalyst currently supports 3 different types of models for Named Entity Recognition (NER):
- Gazetteer-like(i.e. [Spotter](https://github.com/curiosity-ai/catalyst/blob/master/Catalyst/src/Models/EntityRecognition/Spotter.cs)) 
- Regex-like(i.e. [PatternSpotter](https://github.com/curiosity-ai/catalyst/blob/master/Catalyst/src/Models/EntityRecognition/PatternSpotter.cs))
- Perceptron (i.e. [AveragePerceptronEntityRecognizer](https://github.com/curiosity-ai/catalyst/blob/master/Catalyst/src/Models/EntityRecognition/AveragePerceptronEntityRecognizer.cs))

If something is not well recognized, it is possible to help the model by hand.

```csharp
//For correcting Entity Recognition mistakes, you can use the Neuralyzer class. 
//This class uses the Pattern Matching entity recognition class to perform "forget-entity" and "add-entity" 
//passes on the document, after it has been processed by all other proceses in the NLP pipeline
var neuralizer = new Neuralyzer(Language.English, 0, "WikiNER-sample-fixes");

//Teach the Neuralyzer class to forget the match for a single token "Amazon" with entity type "Location"
neuralizer.TeachForgetPattern("Location",  "Amazon", mp => mp.Add(new PatternUnit(P.Single().WithToken("Amazon").WithEntityType("Location"))));
            
//Teach the Neuralyzer class to add the entity type Organization for a match for the single token "Amazon"
neuralizer.TeachAddPattern("Organization", "Amazon", mp => mp.Add(new PatternUnit(P.Single().WithToken("Amazon"))));

//Add the Neuralyzer to the pipeline
nlp.UseNeuralyzer(neuralizer);
```

