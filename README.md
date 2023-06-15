![alt text](https://images.squarespace-cdn.com/content/646ad2edeaaf682a9bbc36da/297fde6c-f5b4-4076-83bc-81dcfdbffebe/Vector+Vault+Header+5000.jpg)

Vector Vault is a vector database cloud service built to make generative ai chat quick and easy. It allows you to seamlessly vectorize data and access it from the cloud. It's scalable to both small projects and large applications with millions of users. Vector Vault has been designed with a user-friendly code interface to make the process of working with vector search easy and let you focus on what matters, results. Vector Vault ensures secure and isolated data handling and enables you to create and interact vector databases - aka "vaults" - in the cloud with under one second response times.

The `vectorvault` package comes with extensive chat functionality, so that you don't have to think about the details and can make smooth chat applications with ease. Speaking of smooth chat experiences, `vectorvault` also comes with streaming built-in, so  you can make customer-ready applications fast (see below).

Popular packages for building functionality with llms, like LangChain, can often be complicated and difficult when it comes to referencing vector databases, but referencing your vector database is crucial to create the chat experience you are going for. This is one of the reasons we built `vectorvault`... We've integrated all the chat options people like to use with LangChain, but made them all easier and more straight forward to use. Now with Vector Vault, integrating vector database results into generative chat applications is not only easy, it's the default. With `vectorvault` have total control over every aspect with parameters. If you have been looking for an easy and reliable way to use vector databases with ChatGPT, then Vector Vault is for you.

By combining vector similarity search with generative ai chat, new possibilities for conversation and communication emerge. For example, product information can be added to a Vault, and when a customer asks a product question, the right product information can be instantly retreived and seamlessly used in conversation by chatgpt for an accurate response. This capability allows for informed conversation and the possibilites range from ai automated customer support, to new ways to get news, to ai code reviews that reference source documentation, to ai domain experts for specific knowledge sets, and much more. You will need an api key in order to access the Vault Cloud. If you don't already have one, you can sign up at [VectorVault.io](https://vectorvault.io)

<br>

Basic Interactions:

`add()` : Prepares data to be added to the Vault, with automatic text splitting and processing for long texts. 
<br>
`get_vectors()` : Retrieves vectors embeddings for all prepared data 
<br>
`save()` : Saves the data with embeddings to the Vault (cloud), along with any metadata
<br>
`delete()` : Deletes the current Vault and all contents
<br>
`get_vaults()` : Retrieves a list of Vaults within the current Vault directory
<br>
`get_similar()` : Retrieves similar texts from the Vault for a given input text - We process vectors in the cloud
<br>
`get_similar_local()` : Retrieves similar texts from the Vault for a given input text - You process vectors locally
<br>
`get_chat()` : Retrieves a response from ChatGPT, with support for handling conversation history, summarizing responses, and retrieving context-based responses by referencing similar data in the vault

>> `get_vectors()` utilizes openai embeddings api and internally batches vector embeddings with OpenAI's text-embeddings-ada-002 model, and comes with auto rate-limiting and concurrent requests for maximum processing speed


<br>

# Access The Vault:
<p align="center">
  <img src="https://images.squarespace-cdn.com/content/646ad2edeaaf682a9bbc36da/2acebcaa-f5dd-44c9-8bba-c10723bc7064/Vector+Vault+Vault+2000.png" width="60%" height="60%" />
</p>

Install Vector Vault:
```
pip install vector-vault
```
<br>

### Get Your Vector Vault API Key:
```python
from vectorvault import register

register(first_name='John', last_name='Smith', email='john@smith.com', password='make_a_password')
```
The api key will be sent to your email.

<br>

# Build The Vault:

Set your openai key as an envorionment variable
```python
os.environ['OPENAI_API_KEY'] = 'your_openai_api_key'
```

1. Create a Vault instance - (new vault will be created if name does not exist)
2. Gather some text data we want to store
3. Add the data to the Vault
4. Get vectors embeddings 
5. Save to the cloud vault

```python
from vectorvault import Vault

vault = Vault(user='your@email.com', api_key='your_api_key', vault='name_of_vault)

text_data = 'some data'

vault.add(text_data)

vault.get_vectors()

vault.save()
```

<br>
<br>

Now that you have saved some data to the vault, you can add more at anytime. `vault.add()` is very versitile. You can add any length of text, even a full book...and it will be all automatically split and processed. `vault.get_vectors()` is also extremely flexible, because you can `vault.add()` as much as you want, then when you're done, process all the vectors at once with a `vault.get_vectors()` call - Which internally batches vector embeddings with OpenAI's text-embeddings-ada-002, and comes with auto rate-limiting and concurrent requests for maximum processing speed. 
```python
vault.add(very_large_text)
vault.get_vectors() 
vault.save() 
```
^ these three lines execute fast and can be called as often as you like. For example: you can use `add()`, `get_vectors()`, and `save()` mid conversation to save every message to the vault as soon as they comes in. Small loads are usually finished in less than a second. Large loads depend on total data size. 
>> A test was done adding the full text of 37 books at once. The `get_vectors()` function took 8 minutes and 56 seconds. (For comparison, processing one at a time via openai's embedding function would take roughly two days)

<br>
<br>

# Use The Vault:
<p align="center">
  <img src="https://images.squarespace-cdn.com/content/646ad2edeaaf682a9bbc36da/5ae905b0-43d0-4b86-a965-5b447ee8c7de/Vector+Vault+Vault.jpg?content-type=image%2Fjpeg" width="60%" height="60%" />
</p>

You can create a javascript or HTML post to `"https://api.vectorvault.io/get_similar"`, to run front end apps.
Since your Vault lives in the cloud, making a call to it is really easy. You can even do it with a `curl` from command line:
```
curl -X POST "https://api.vectorvault.io/get_similar" \
     -H "Content-Type: application/json" \
     -d '{
        "user": "your_username",
        "api_key": "your_api_key",
        "vault": "your_vault_name",
        "text": "Your text input"
     }'
```
>> {"results":[{"data":"NASA Mars Exploration... **shortend for brevity**","metadata":{"created_at":"2023-05-29T19:21:20.846023","item_id":0,"name":"webdump-0","updated_at":"2023-05-29T19:21:20.846028"}}]}
    
This is the same exact call, but in Python:
```python
similar_data = vault.get_similar("Your text input") 

for result in similar_data:
    print(result['data'])
```
>> NASA Mars Exploration... NASA To Host Briefing... Program studies Mars... A Look at a Steep North Polar...

^ this prints each similar item that was retieved. The `get_similar()` function retrieves items from the vault using vector cosine similarity search algorithm to find results. Default returns a list with 4 results. 
`similar_data = vault.get_similar(text_input, n = 10)` returns 10 results instead of 4.

<br>

Print the metadata:
```python
similar_data = vault.get_similar("Your text input") 

for result in similar_data:
    print(result['data'])
    print(result['metadata'])
```
>> NASA Mars Exploration... {"created_at":"2023-05-29T19...} NASA To Host Briefing... {"created_at":"2023-05-29T19...} Program studies Mars... {"created_at":"2023-05-29T19...} A Look at a Steep North Polar... {"created_at":"2023-05-29T19...}

<br>
<br>

### Use `get_chat()` with `get_context=True` to get response from chatgpt referencing vault data
Retrieving items from the vault, is useful when using it supply context to a large language model, like chatgpt for instance, to get a contextualized response. The follow example searches the vault for 4 similar results and then give those to chatgpt as context, asking chatgpt answer the question using the vault data.
```python
question = "Should I use Vector Vault for my next generative ai application"

answer = vault.get_chat(question, get_context=True)  
print(answer)
```
The following line will send chatgpt the question for response and not interact with the vault in any way
```python
answer = vault.get_chat(question) 
```


<br>
<br>

# ChatGPT
## Use ChatGPT with `get_chat()` 

<p align="center">
  <img src="https://images.squarespace-cdn.com/content/646ad2edeaaf682a9bbc36da/74776e31-4bfd-4d6b-837b-674790ca4288/wisdomandwealth_Electric_Yellow_and_Dark_Blue_-_chat_messages_g_c81a4325-5347-44a7-879d-a58a6d115446.png" width="60%" height="60%" />
</p>
<br>

Get chat response from OpenAI's ChatGPT. 
Rate limiting, auto retries, and chat histroy slicing auto-built-in so you can create complex chat capability without getting complicated. 
Enter your text, optionally add chat history, and optionally choose a summary response instead (default: summmary=False)

- Example Signle Usage: 
`response = vault.get_chat(text)`

- Example Chat: 
`response = vault.get_chat(text, chat_history)`

- Example Summary: 
`summary = vault.get_chat(text, summary=True)`

- Example Context-Based Response:
`response = vault.get_chat(text, get_context=True)`

- Example Context-Based Response w/ Chat History:
`response = vault.get_chat(text, chat_history, get_context=True)`

- Example Context-Response with Context Samples Returned:
`vault_response = vault.get_chat(text, get_context=True, return_context=True)`
<br>

Response from ChatGPT in string format, unless `return_context=True` is passed, then response will be a dictionary containing the results - response from ChatGPT, and the vault data.
```python
# print response:
print(vault_response['response'])

# print context:
for item in vault_response['context']:
    print("\n\n", f"item {item['metadata']['name']}")
    print(item['data'])
```

<br>
<br>

# Summarize Anything:
<p align="center">
  <img src="https://images.squarespace-cdn.com/content/646ad2edeaaf682a9bbc36da/e1ff4ca3-e18b-4c8f-b3c9-ff6ddcc907a1/wisdomandwealth_a_summary_being_created._A_bunch_of_texts_are_f_df58744a-13cb-46fd-b39d-3f090349bbb7.png" width="60%" height="60%" />
</p>

You can summarize any text, no matter how large - even an entire book all at once. Long texts are split into the largest possible chunk sizes and a summary is generated for each chunk. When all summaries are finished, they are concatenated and returned as one.
```python
summary = vault.get_chat(text, summary=True)
```
<br>

want to make a summary of a certain length?...
```python
summary = vault.get_chat(text, summary=True)

while len(summary) > 1000:
    summary = vault.get_chat(summary, summary=True)
```
^ in the above example, we make a summary, then we enter while loop that continues until the summary recieved back is a certain lenght. You could use this to summarize a 1000 page book to less than 1000 characters of text. 

<br>
<br>
<br>

# Streaming:
Use the built-in streaming functionality to get interactive chat streaming. Here's an [app](https://philbrosophy.web.app) we built to showcase what you can do with Vector Vault:
<br>

![Alt text](https://github.com/John-Rood/VectorVault/blob/778c11dfc8b71675d704c5f559c3452dc65b910a/digital%20assets/Streaming%20Demo%20Offish.gif)

## get_chat_stream():
See it in action. Check our [examples folder](https://github.com/John-Rood/VectorVault/tree/main/examples) that has Colab notebooks you can be running in the browser seconds from now.


`get_chat()` function returns the whole message at once. `get_chat_stream` yields each word as it's received.

```python
## get_chat()
print(vault.get_chat(text, history))

## get_chat_stream()
for word in vault.get_chat_stream(text, history):
        print(word)
```

```python
# But it's best to use the built in print function print_stream() function
vault.print_stream(vault.get_chat_stream(text, history))
```
<br>

Because streaming is a key functionality for end user applications, we also have a `cloud_stream` function to make cloud streaming to your front end app easy. In a flask app, your return would look like: 
```python
return Response(vault.cloud_stream(vault.get_chat_stream(text, history, get_context=True)), mimetype='text/event-stream')
```
This makes going live with highly functional cloud apps really easy. Now you can build impressive applications in record time! If have any questions, message in [Discord](https://discord.gg/AkMsP9Uq). Check out our [examples folder](https://github.com/John-Rood/VectorVault/tree/main/examples) that has notebooks you can run in the browser with Google Colab.


<br>
<br> 



<br>
<br>
<br>

# Build an AI Cusomter Service Chat Bot
<p align="center">
  <img src="https://images.squarespace-cdn.com/content/646ad2edeaaf682a9bbc36da/dceb5c7d-6ec6-4eda-82f2-b8848c7b519d/ai_chatbot_having_a_conversation.png" width="60%" height="60%" />
</p>
<br>

In the following code, we will add all of a company's past support conversations to a cloud Vault. (We load the company support texts from a .txt file, vectorize them, then add them to the Vault). As new people message in, we will vector search the Vault for similar questions and answers. We take the past answers returned from the Vault and instruct ChatGPT to use those previous answers to answer this new question. (NOTE: This will also work based on a customer FAQ, or customer support response templates).

<br>

### Create the Customer Service Vault
```python
from vectorvault import Vault

os.environ['OPENAI_API_KEY'] = 'your_openai_api_key'

vault = Vault(user='your_user_id', api_key='your_api_key', vault='Customer Service')

with open('customer_service.txt', 'r') as f:
    vault.add(f.read())

vault.get_vectors()

vault.save()
```

<br>

And just like that, in a only a few lines of code we created a customer service vault. Now whenever you want to use it in production, just connect to that vault, and use the `get_chat()` with `get_context=True`. When you call `get_chat(text, get_context=True)` it will take the customer's question, search the vault to find the most similar questions and answers, then have ChatGPT reply to the customer using that information.

```python
question = 'customer question'

answer = vault.get_chat(question, get_context=True)
```
<br>

That's all it takes to create an AI customer service chatbot that responds as well as any support rep!


<br>
<br>

---
<br>
<br>



## Getting Started:
Open the [examples folder](https://github.com/John-Rood/VectorVault/tree/main/examples) and try out the Google Colab tutorials we have! They will show you a lot, plus they are in Google Colab, so no local set up required, just open them up and press play.

<br>
<br>

## Contact:
### If have any questions, drop a message in the Vector Vault [Discord channel](https://discord.gg/AkMsP9Uq), happy to help.

Happy coding!
<br>
<p align="center">
  <img src="https://images.squarespace-cdn.com/content/646ad2edeaaf682a9bbc36da/7d1a596b-7560-446b-aa69-1827819d198b/Looking+out+with+hope+vector+vault.png" width="60%" height="60%" />
</p>

<br>
