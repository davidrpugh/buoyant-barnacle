---
title: "Using the LLaMA C++ CLI" # Episode title
teaching: 5   # teaching time in minutes
exercises: 10 # exercise time in minutes
---

:::::: questions
 - question 1
 - question 2
::::::

:::::: objectives
 - objective 1
 - objective 2
::::::

# Getting started with LLaMA C++

## Checking your LLaMA C++ installation


```bash
%%bash 

which llama-cli
```

## Getting help!

You can easily display a help message showing all available options and their default values. This is particularly useful for checking the latest options and default values, as they can change frequently, and the information in this document may become outdated.



```bash
%%bash

llama-cli --help
```

## Downloading a model

### Manually downloading a model from a URL


```bash
%%bash

MODEL_URL=https://huggingface.co/ggml-org/gemma-1.1-7b-it-Q4_K_M-GGUF/resolve/main/gemma-1.1-7b-it.Q4_K_M.gguf
curl --location --output ../models/gemma-1.1-7b-it.Q4_K_M.gguf $MODEL_URL

```


```python
MODEL = "../models/gemma-1.1-7b-it.Q4_K_M.gguf"
```

### Downloading a model from a URL


```bash
%%bash

export LLAMA_CACHE="../models"

MODEL_URL=https://huggingface.co/ggml-org/gemma-1.1-7b-it-Q4_K_M-GGUF/resolve/main/gemma-1.1-7b-it.Q4_K_M.gguf
llama-cli --model-url "$MODEL_URL" --prompt "Once upon a time"

```


```python
MODEL = "../models/gemma-1.1-7b-it.Q4_K_M.gguf"
```

### Downloading a model from a Hugging Face repository


```bash
%%bash

export LLAMA_CACHE="../models"

HF_REPO="ggml-org/gemma-1.1-7b-it-Q4_K_M-GGUF"
HF_MODEL_FILE="gemma-1.1-7b-it.Q4_K_M.gguf"
llama-cli --hf-repo "$HF_REPO" --hf-file "$HF_MODEL_FILE" --prompt "Once upon a time"

```


```python
MODEL = "../models/gemma-1.1-7b-it.Q4_K_M.gguf"
```

## Basic text generation

### Simple prompting using `--prompt`

After downloading the model file we can just pass the path to the model file as a command line argument using `-m` or `--model` to specify the path to the GGUF model file together with a prompt using the `--prompt` argument to generate a response.

```bash
%%bash -s "$MODEL"

llama-cli \
    --model "$1" \
    --prompt "Once upon a time"

```

### More elaborate prompting using `--file`

You can also prompt a model by providing one or more prompts stored in a file using the `--file` option. This is useful when you are working with longer prompts.

```python
language = "English"
tone_of_voice = "Informative"
topic = "Computer Science"
writing_style = "Conversational"

prompt_template = f"""Please ignore all previous instructions. Please respond \
only in the {language} language. You are a Twitter influencer with a large \
following. You have a {tone_of_voice} tone of voice. You have a \
{writing_style} writing style. Do not self reference. Do not explain what you \
are doing. Please create a thread about {topic}. Add emojis to the thread \
when appropriate. The character count for each thread should be between 270 \
to 280 characters. Your content should be casual, informative, and an \
engaging Twitter thread. Please use simple and understandable words. Please \
include statistics, personal experience, and fun facts in the thread. Please \
add relevant hashtags to the post and encourage the readers join the \
conversation.
"""

```


```python
print(prompt_template)
```


```python
PROMPT_FILE = "../prompts/engaging-twitter-thread.txt"

with open(PROMPT_FILE, 'w') as f:
    f.write(prompt_template)

```


```bash
%%bash -s "$MODEL" "$PROMPT_FILE"

llama-cli \
    --model "$1" \
    --file  "$2"

```

### Don't want to always return your prompt?

By default, the generated out will always display your prompt before returning the generated output. You can disable this behavior by providing the `--no-display-prompt` flag.



```bash
%%bash -s "$MODEL" "$PROMPT_FILE"

llama-cli \
    --model "$1" \
    --file  "$2" \
    --no-display-prompt

```

## Interactive Modes

The `llama-cli` program offers a seamless way to interact with models which allows users to engage in real-time conversations or provide instructions for specific tasks.

-   `-i, --interactive`: Run the program in interactive mode, allowing users to engage in real-time conversations or provide specific instructions to the model.
-   `--interactive-first`: Run the program in interactive mode and immediately wait for user input before starting the text generation.
-   `-cnv,  --conversation`:  Run the program in conversation mode (does not print special tokens and suffix/prefix, use default chat template) (default: false)
-   `--color`: Enable colorized output to differentiate visually distinguishing between prompts, user input, and generated text.

By understanding and utilizing these interaction options, you can create engaging and dynamic experiences with your models, tailoring the text generation process to your specific needs.

In interactive mode, users can participate in text generation by injecting their input during the process. Users can press `Ctrl+C` at any time to interject and type their input, followed by pressing `Return` to submit it to the LLaMA model. To submit additional lines without finalizing input, users can end the current line with a backslash (`\`) and continue typing.


### `--interactive` example

Open a new terminal and type the following command.

```bash
MODEL='./models/gemma-1.1-7b-it.Q4_K_M.gguf'
llama-cli \
    --model "$MODEL" \
    --interactive \
    --color
```

### `--interactive-first` example

Open a new terminal and type the following command.

```bash
MODEL='./models/gemma-1.1-7b-it.Q4_K_M.gguf'
llama-cli \
    --model "$MODEL" \
    --interactive-first \
    --color
```

### `--conversation` example

Open a new terminal and type the following command.

```bash
MODEL='./models/gemma-1.1-7b-it.Q4_K_M.gguf'
llama-cli \
    --model "$MODEL" \
    --conversation \
    --color
```

## Context Management

During text generation, models have a limited context size, which means they can only consider a certain number of tokens from the input and generated text. When the context fills up, the model resets internally,  otentially losing some information from the beginning of the conversation or instructions. Context management options help maintain continuity and coherence in these situations.

### Context Size

The simplest way to manage the context size is to set the size of the prompt context directly using `--ctx-size`. By default, the context size will be inferred from the model file.


```bash
%%bash -s "$MODEL" "$PROMPT_FILE"

llama-cli \
    --model "$1" \
    --file  "$2" \
    --ctx-size 256

```

### Keep Prompt

The `--keep` option allows users to retain the original prompt when the model runs out of context, ensuring a connection to the initial instruction or conversation topic is maintained. Using the `--keep N` option you can directly specify the number of tokens from the initial prompt to retain when the model resets its internal context. By default, this value is set to 0 (meaning no tokens are kept). Use `-1` to retain all tokens from the initial prompt.



```bash
%%bash -s "$MODEL" "$PROMPT_FILE"

llama-cli \
    --model "$1" \
    --file  "$2" \
    --ctx-size 256 \
    --keep -1

```

:::::: keypoints
 - keypoint 1
 - keypoint 2
::::::

