# Aram-Radif-AI-Translate-text-with-Azure-Translator-service

Provision an Azure Translator resource language detection, translation, and transliteration Specify translation options Define custom translations

Aram Radif AI Translator – End‑to‑End Demo Project

This repository is a GitHub‑ready implementation of the Azure AI Translator service, rewritten from Microsoft Learn materials and labs while preserving all original code logic and API usage.

It demonstrates how to use Azure Translator to perform:

Language detection

Text translation (one‑to‑many)

Script transliteration

Advanced translation options

Custom translation (conceptual + API usage)

This project is suitable for AI Engineer, Data Scientist, Cloud Engineer, and Azure certification portfolios.

 Repository Structure
azure-ai-translator-demo/
│
├── README.md
├── requirements.txt
├── .env.example
├── .gitignore
│
├── src/
│   └── translate.py
│
├── docs/
│   ├── rest-api-examples.md
│   └── custom-translation.md
│
└── assets/
    └── architecture.png
⚙️ Prerequisites
Azure subscription

Azure AI Translator resource (F0 or S tier)

Python 3.9+

Git

 Environment Variables

Create a .env file in the root directory using the template below:

TRANSLATOR_KEY=your_azure_translator_key
TRANSLATOR_REGION=your_azure_resource_region

 Do not commit real credentials to GitHub.
Installation
python -m venv venv
source venv/bin/activate   # Windows: venv\Scripts\activate
pip install -r requirements.txt
requirements.txt
azure-ai-translation-text==1.0.1
python-dotenv
azure-core

 Running the Application
python src/translate.py
Application Behavior

Lists supported target languages

Prompts user to choose a language code

Accepts free‑form text input

Automatically detects the source language

Translates the text

Repeats until the user types quit

Core Concepts Covered
1. Language Detection (REST)
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" \
  -H "Ocp-Apim-Subscription-Region: <your-region>" \
  -H "Ocp-Apim-Subscription-Key: <your-key>" \
  -H "Content-Type: application/json" \
  -d "[{ 'Text': 'こんにちは' }]"
2. Translation (REST)
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=ja&to=en&to=fr" \
  -H "Ocp-Apim-Subscription-Key: <your-key>" \
  -H "Ocp-Apim-Subscription-Region: <your-region>" \
  -H "Content-Type: application/json" \
  -d "[{ 'Text': 'こんにちは' }]"
3. Transliteration (REST)
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&fromScript=Jpan&toScript=Latn" \
  -H "Ocp-Apim-Subscription-Key: <your-key>" \
  -H "Ocp-Apim-Subscription-Region: <your-region>" \
  -H "Content-Type: application/json" \
  -d "[{ 'Text': 'こんにちは' }]"

 Translation Options
Word Alignment
"alignment": {
  "proj": "0:4-0:1 6:13-2:3"
}
Sentence Length
"sentLen": {
  "srcSentLen": [12],
  "transSentLen": [20]
}
Profanity Filtering
"text": "JSON ist *** erstaunlich."

Custom Translation (Conceptual)

Custom translation models are created using the Custom Translator Portal:

Create a workspace

Upload domain‑specific training data

Train and publish a model

Use the category parameter in translate calls

curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=nl&category=<category-id>" \
  -H "Ocp-Apim-Subscription-Key: <your-key>" \
  -H "Content-Type: application/json" \
  -d "[{ 'Text': 'Where can I find my employee details?' }]"
Python SDK Implementation
src/translate.py
import os
from dotenv import load_dotenv
from azure.core.credentials import AzureKeyCredential
from azure.ai.translation.text import TextTranslationClient
from azure.ai.translation.text.models import InputTextItem




def main():
    load_dotenv()


    translatorKey = os.getenv("TRANSLATOR_KEY")
    translatorRegion = os.getenv("TRANSLATOR_REGION")


    credential = AzureKeyCredential(translatorKey)
    client = TextTranslationClient(credential=credential, region=translatorRegion)


    languagesResponse = client.get_supported_languages(scope="translation")
    print(f"{len(languagesResponse.translation)} languages supported.")
    print("Enter a target language code (example: en):")


    supportedLanguage = False
    while not supportedLanguage:
        targetLanguage = input()
        if targetLanguage in languagesResponse.translation.keys():
            supportedLanguage = True
        else:
            print(f"{targetLanguage} is not supported.")


    inputText = ""
    while inputText.lower() != "quit":
        inputText = input("Enter text to translate ('quit' to exit): ")
        if inputText.lower() != "quit":
            body = [InputTextItem(text=inputText)]
            response = client.translate(body=body, to_language=[targetLanguage])


            translation = response[0]
            sourceLanguage = translation.detected_language.language


            for t in translation.translations:
                print(f"'{inputText}' translated from {sourceLanguage} to {t.to}: '{t.text}'")




if __name__ == "__main__":
    main()

Cleanup

After testing:

Delete the Azure AI Translator resource from the Azure Portal

Remove the local virtual environment

 Skills Demonstrated

Azure AI Services

REST & SDK integration

Secure configuration management

Python CLI application design

Cloud‑based language processing

--

Aram Radif
