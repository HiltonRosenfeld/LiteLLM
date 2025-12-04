# LiteLLM Proxy

## Docker Deployment

### litellm_config.yaml

This config file defines the list of supported models on the server and has the following parameters:

- model_name: the name to pass TO litellm from the external client
- litellm_params.model: the model string passed to the litellm.completion() function:

For example, in the config below:

- model=gpt-4.1-mini: will will route to openai/gpt-4.1-mini

```yaml
model_list:
  - model_name: gpt-4.1-mini
    litellm_params:
      model: openai/gpt-4.1-mini
      api_key: os.environ/OPENAI_API_KEY
```

### Deploy as Docker Image

```sh
docker run \
    -v $(pwd)/litellm_config.yaml:/app/config.yaml \
    -e OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxxxxxxxxxx \
    -p 4000:4000 \
    ghcr.io/berriai/litellm:main-latest \
    --config /app/config.yaml --detailed_debug
```

### Make ChatCompletions Request to Proxy

```sh
import openai # openai v1.0.0+
client = openai.OpenAI(api_key="anything",base_url="http://0.0.0.0:4000") # set proxy to base_url
response = client.chat.completions.create(model="gpt-4.1-mini", messages = [
    {
        "role": "user",
        "content": "this is a test request, write a short poem"
    }
])

print(response)
```
