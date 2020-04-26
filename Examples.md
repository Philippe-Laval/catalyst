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
