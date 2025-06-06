# Docker Compose [Recommended]

### Requirements

- Docker
- Docker Compose

### 1. Create a new directory

Create a new directory to host the compose file and env variables.

This is where you’ll place the `docker-compose.yml` file from the next step and the environment variables.

For example you could make a new directory called "hoarder-app" with the following command:
```
mkdir hoarder-app
```


### 2. Download the compose file

Download the docker compose file provided [here](https://github.com/hoarder-app/hoarder/blob/main/docker/docker-compose.yml) directly into your new directory.

```
wget https://raw.githubusercontent.com/hoarder-app/hoarder/main/docker/docker-compose.yml
```

### 3. Populate the environment variables

To configure the app, create a `.env` file in the directory and add this minimal env file:

```
HOARDER_VERSION=release
NEXTAUTH_SECRET=super_random_string
MEILI_MASTER_KEY=another_random_string
NEXTAUTH_URL=http://localhost:3000
```

You **should** change the random strings. You can use `openssl rand -base64 36` in a seperate terminal window to generate the random strings. You should also change the `NEXTAUTH_URL` variable to point to your server address.

Using `HOARDER_VERSION=release` will pull the latest stable version. You might want to pin the version instead to control the upgrades (e.g. `HOARDER_VERSION=0.10.0`). Check the latest versions [here](https://github.com/hoarder-app/hoarder/pkgs/container/hoarder-web).

Persistent storage and the wiring between the different services is already taken care of in the docker compose file.

Keep in mind that every time you change the `.env` file, you'll need to re-run `docker compose up`.

If you want more config params, check the config documentation [here](/configuration).

### 4. Setup OpenAI

To enable automatic tagging, you'll need to configure OpenAI. This is optional though but highly recommended.

- Follow [OpenAI's help](https://help.openai.com/en/articles/4936850-where-do-i-find-my-openai-api-key) to get an API key.
- Add the OpenAI API key to the env file:

```
OPENAI_API_KEY=<key>
```

Learn more about the costs of using openai [here](/openai).

<details>
    <summary>If you want to use Ollama (https://ollama.com/) instead for local inference.</summary>

    **Note:** The quality of the tags you'll get will depend on the quality of the model you choose.

    - Make sure ollama is running.
    - Set the `OLLAMA_BASE_URL` env variable to the address of the ollama API.
    - Set `INFERENCE_TEXT_MODEL` to the model you want to use for text inference in ollama (for example: `llama3.1`)
    - Set `INFERENCE_IMAGE_MODEL` to the model you want to use for image inference in ollama (for example: `llava`)
    - Make sure that you `ollama pull`-ed the models that you want to use.
    - You might want to tune the `INFERENCE_CONTEXT_LENGTH` as the default is quite small. The larger the value, the better the quality of the tags, but the more expensive the inference will be.

</details>

### 5. Start the service

Start the service by running:

```
docker compose up -d
```

Then visit `http://localhost:3000` and you should be greeted with the Sign In page.

### [Optional] 6. Enable optional features

Check the [configuration docs](/configuration) for extra features to enable such as full page archival, full page screenshots, inference languages, etc.

### [Optional] 7. Setup quick sharing extensions

Go to the [quick sharing page](/quick-sharing) to install the mobile apps and the browser extensions. Those will help you hoard things faster!

## Updating

Updating hoarder will depend on what you used for the `HOARDER_VERSION` env variable.

- If you pinned the app to a specific version, bump the version and re-run `docker compose up -d`. This should pull the new version for you.
- If you used `HOARDER_VERSION=release`, you'll need to force docker to pull the latest version by running `docker compose up --pull always -d`.
