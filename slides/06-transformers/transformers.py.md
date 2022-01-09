---
jupyter:
  jupytext:
    formats: ipynb,md
    split_at_heading: true
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.13.5
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

<!-- LTeX: language=fr -->

<!-- #region slideshow={"slide_type": "slide"} -->

# Cours 6 : *Transformers*, principes et usage

**Loïc Grobol** [&lt;lgrobol@parisnanterre.fr>](mailto:lgrobol@parisnanterre.fr)

2022-01-10

<!-- #endregion -->

## Prologue : encodeurs-décodeurs et traduction automatique

## Mécanisme d'attention

## *Attention is all you need*

## Préentraînement et *fine-tuning*

## 🤗 `transformers`


Cette partie est adaptée du [cours 🤗](https://huggingface.co/course) que je vous encourage très fortement à lire en détails !

```python
import torch
```

```python
import transformers
```

### Charger et utiliser des modèles


On va utiliser DistilBERT ([Sahn et al, 2019](https://arxiv.org/abs/1910.01108)), une version de BERT compressée par distillation. Elle a l'avantage d'être considérablement plus légère (donc gentille avec les notebooks), tout en étant presque aussi performante.

```python
bert_model = transformers.AutoModel.from_pretrained("distilbert-base-multilingual-cased")
```

```python
bert_model.config
```

```python
tokenizer = transformers.AutoTokenizer.from_pretrained("distilbert-base-multilingual-cased")
```

```python
tokenizer.tokenize("Morgan reconnait l'existence du kiwi.")
```

```python
with torch.no_grad():
    transformer_output = bert_model(**tokenizer("Morgan reconnait l'existence du kiwi.", return_tensors="pt"))
transformer_output.keys()
```

```python
final_embeddings = transformer_output.last_hidden_state
display(final_embeddings)
display(final_embeddings.shape)
```

```python
with torch.no_grad():
    transformer_output = bert_model(
        **tokenizer("Morgan reconnait l'existence du kiwi.",return_tensors="pt"),
        output_hidden_states=True
    )
transformer_output.keys()
```

```python
all_embeddings = transformer_output.hidden_states
display(all_embeddings)
display([e.shape for e in all_embeddings])
```

```python
# Pour ne pas manger toute la RAM
del bert_model
del tokenizer
```

### Utiliser des *pipelines*

```python
classifier = transformers.pipeline("sentiment-analysis")
```

```python
classifier("There are not many sentiment analysis models on 🤗 hub")
```

```python
lm = transformers.pipeline("fill-mask", model="distilbert-base-multilingual-cased")
```

```python
lm(f"En France, c'est l'Université de {lm.tokenizer.mask_token} la meilleure.")
```

Pour les autres : voire [la liste des pipelines](https://huggingface.co/docs/transformers/v4.15.0/en/main_classes/pipelines#transformers.pipeline.task) dans la doc.

Attention : tous les modèles n'ont pas étés affinés pour toutes les pipelines, vous pouvez chercher sur [le hub de ](https://huggingface.co/models) les modèles entraînés pour une tâche et une langue donnée. Par exemple les modèles de [NER](https://huggingface.co/models?language=fr&sort=downloads&search=ner).


Et s'il n'y en a pas ? On peut en entraîner un !

## Affiner un modèle

### Données


On va utiliser une des tâches du multi-benchmark [GLUE](https://gluebenchmark.com), dans sa version [🤗 datasets](https://huggingface.co/datasets/glue).

```python
import datasets
```

Il y a beaucoupd de sous-tâches dans GLUE (c'est le principe), on va commencer par regarder celle qui nous est peut-être la plus familière : la détection de polarité (*sentiment analysis*), avec le corpus [*Stanford Sentiment Treebank*](https://nlp.stanford.edu/sentiment/index.html) version 2, `sst2`.

```python
raw_datasets = datasets.load_dataset("glue", "sst2")
raw_datasets
```

```python
raw_train_dataset = raw_datasets["train"]
raw_train_dataset[6]
```

```python
raw_train_dataset.features
```

```python
raw_train_dataset[16:32]
```

```python
raw_train_dataset["sentence"][9]
```

### Tokenization

```python
tokenizer = transformers.AutoTokenizer.from_pretrained("distilbert-base-multilingual-cased")
```

```python
tokenized_sent = tokenizer(raw_datasets["train"]["sentence"][9])
tokenized_sent
```

```python
tokenizer.convert_ids_to_tokens(tokenized_sent["input_ids"])
```

```python
tokenizer(raw_datasets["train"]["sentence"])[:8]
```

```python
tokenized_dataset = tokenizer(
    raw_datasets["train"]["sentence"],
    padding=True,
    truncation=True,
)
tokenized_dataset[0]
```

```python
tokenized_dataset[0].tokens
```

```python
del tokenized_dataset
```

```python
def tokenize_function(example):
    return tokenizer(example["sentence"], truncation=True)
```

```python
tokenized_datasets = raw_datasets.map(tokenize_function, batched=True)
tokenized_datasets
```

```python
data_collator = transformers.DataCollatorWithPadding(tokenizer=tokenizer)
```

```python
samples = tokenized_datasets["train"][:8]
samples = {k: v for k, v in samples.items() if k not in ["idx", "sentence"]}
[len(x) for x in samples["input_ids"]]
```

```python
batch = data_collator(samples)
{k: v.shape for k, v in batch.items()}
```

### Affinage

```python
classifier = transformers.AutoModelForSequenceClassification.from_pretrained(
    "distilbert-base-multilingual-cased", num_labels=2
)
classifier
```

```python
sentiment_pipeline = transformers.pipeline("sentiment-analysis", model=classifier, tokenizer=tokenizer)
sentiment_pipeline("This movie is not so bad")
```

```python
training_args = transformers.TrainingArguments(
    gradient_accumulation_steps=2,
    logging_steps=8,
    max_steps=64,
    output_dir="local/distilbert-base-multilingual-cased+sst2",
    per_device_train_batch_size=4,
    report_to="none",
    warmup_ratio=1/16,
)
```

```python
trainer = transformers.Trainer(
    classifier,
    training_args,
    train_dataset=tokenized_datasets["train"],
    data_collator=data_collator,
    tokenizer=tokenizer,
)
```

```python
trainer.train()
```

### Utiliser le modèle entraîné


Les paramètres du modèle ont été directement modifiés et on peut l'utiliser tout de suite.


Bien penser à mettre le modèle en mode évaluation, sinon les résultats seront partiellement aléatoires

```python
classifier.eval()
```

On peut s'en servir pour faire des prédictions directement

```python
with torch.no_grad():
    classifier_output = classifier(**tokenizer("This movie is not so bad", return_tensors="pt"))
    display(classifier_output)
```

On peut aussi l'utiliser dans la pipeline

```python
sentiment_pipeline("This movie is not so bad")
```

Le score pour la pipeline et le logit correspondant en appliquant directement le modèle sont différent, c'est parce que la pipeline applique un softmax :

```python
classifier_output.logits.softmax(dim=-1)
```

Si on veut utiliser le modèle affiné ailleurs, il faut le sauvegarder

```python
classifier.save_pretrained("local/distilbert-base-multilingual-cased+sst2/model")
```

On peut alors le charger avec le `.from_pretrained("local/distilbert-base-multilingual-cased+sst2/model")` qui va bien, par exemple.

```python
classifier = transformers.AutoModelForSequenceClassification.from_pretrained("local/distilbert-base-multilingual-cased+sst2/model")
```

C'est une bonne pratique de sauvegarder le tokenizer avec.

```python
tokenizer.save_pretrained("local/distilbert-base-multilingual-cased+sst2/model")
```

Et pour rendre votre modèle facilement réutilisable, vous pouvez le mettre sur [le hub](https://huggingface.co/docs/hub). Il y a aussi des intégrations pour le faire directement dans le `Trainer`, voir [la doc](https://huggingface.co/docs/transformers/v4.15.0/en/main_classes/trainer#transformers.Trainer.push_to_hub).

### Évaluation

```python
predictions = trainer.predict(tokenized_datasets["validation"])
print(type(predictions.predictions), predictions.predictions.shape, predictions.label_ids.shape)
```

```python
predicted_labels = predictions.predictions.argmax(axis=-1)
predicted_labels
```

```python
predicted_labels == predictions.label_ids
```

```python
(predicted_labels == predictions.label_ids).sum()/predicted_labels.shape[0]
```

```python
metric = datasets.load_metric("glue", "sst2")
metric.compute(predictions=predicted_labels, references=predictions.label_ids)
```

```python
def compute_metrics(eval_preds):
    metric = datasets.load_metric("glue", "sst2")
    logits, labels = eval_preds
    predictions = logits.argmax(axis=-1)
    return metric.compute(predictions=predictions, references=labels)
```

```python
# On régénère le classifieur pour reprendre l'entraînement de zéro

classifier = transformers.AutoModelForSequenceClassification.from_pretrained(
    "distilbert-base-multilingual-cased", num_labels=2
)

training_args = transformers.TrainingArguments(
    evaluation_strategy="steps",
    eval_steps=16,
    gradient_accumulation_steps=2,
    logging_steps=8,
    max_steps=64,
    output_dir="local/distilbert-base-mutlitlingual-cased+sst2",
    per_device_train_batch_size=4,
    report_to="tensorboard",
    warmup_ratio=1/16,
)

trainer = transformers.Trainer(
    args=training_args,    
    compute_metrics=compute_metrics,
    data_collator=data_collator,
    eval_dataset=tokenized_datasets["validation"],
    model=classifier,
    tokenizer=tokenizer,
    train_dataset=tokenized_datasets["train"],   
    
)

trainer.train()
```

Vous pouvez voir l'évolution de l'entraînement dans [`tensorboard`](https://www.tensorflow.org/tensorboard)


```console
tensorboard serve --logdir slides/06-transformers/local/distilbert-base-multilingual-cased+sst2
```

### À vous de jouer

Affinez un modèle (pour un nombre raisonnable de pas) en français ou multilingue sur la tâche de détection de polarité du benchmark [FLUE](https://huggingface.co/datasets/flue#text-classification-cls) ([Le et al, 2020](https://hal.archives-ouvertes.fr/hal-02890258)).

```python
del classifier
del sentiment_pipeline
```

## Préentraîner un modèle

```python
tokenizer = transformers.AutoTokenizer.from_pretrained("distilbert-base-multilingual-cased")
tokenizer
```

```python
model_config = transformers.AutoConfig.from_pretrained("distilbert-base-multilingual-cased")
```

```python
model = transformers.AutoModelForMaskedLM.from_config(model_config)
```

```python
lm = transformers.pipeline("fill-mask", model=model, tokenizer=tokenizer)
```

```python
lm(f"En France, c'est l'Université de {lm.tokenizer.mask_token} la meilleure.")
```

```python
!mkdir -p local
!wget "https://sharedocs.huma-num.fr/wl/?id=LLYeokePZiJytROQ41iI3fkss6lMmGwd&fmode=download" -O local/ESLO_raw.txt
```

```python
!head local/ESLO_raw.txt
```

```python
raw_dataset = datasets.load_dataset("text", data_files=["local/ESLO_raw.txt"])
raw_dataset
```

```python
def tokenize_function(examples):
    result = tokenizer(examples["text"], truncation=True)
    return result

tokenized_dataset = raw_dataset.map(
    tokenize_function, batched=True
)
tokenized_dataset
```

```python
data_collator = transformers.DataCollatorForLanguageModeling(tokenizer=tokenizer, mlm_probability=0.15)
```

```python
tokenized_dataset["train"][0]
```

```python
samples = [tokenized_dataset["train"][i] for i in range(8)]

for chunk in data_collator(samples)["input_ids"]:
    print(f"\n'>>> {tokenizer.decode(chunk)}'")
```

```python
training_args = transformers.TrainingArguments(
    gradient_accumulation_steps=2,
    learning_rate=2e-5,
    logging_steps=2,
    max_steps=16,
    output_dir=f"local/distilbert-ESLO",
    per_device_train_batch_size=4,
    per_device_eval_batch_size=4,
    report_to="tensorboard",
    warmup_ratio=1/8,
    weight_decay=0.01,
)

trainer = transformers.Trainer(
    model=model,
    args=training_args,
    train_dataset=tokenized_dataset["train"],
    data_collator=data_collator,
)

trainer.train()
```

## Entraîner des tokenizers

## Autres outils

- Pytorch Lightning et [Lightning Transformers](https://lightning-transformers.readthedocs.io/en/latest/)
- [`simpletransformers`](https://simpletransformers.ai)
- [Zelda Rose](https://github.com/LoicGrobol/zeldarose)
