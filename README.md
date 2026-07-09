# action.image.mirror

A GitHub Action that copies each of a set of tags from one image repository
to another via `docker buildx imagetools create`.

It replaces the `push-hub` / `push-quay` job pattern duplicated across this
account's image repositories (e.g.
[its-me/image-workflow](https://github.com/its-me/image-workflow)'s
`simple.yaml`) with a single reusable step.

## Usage

```yaml
push-hub:
  name: Deploy to Docker Hub
  needs: build
  runs-on: ubuntu-slim
  permissions:
    packages: read
  steps:
    - name: Log in to GHCR
      uses: docker/login-action@v4
      with:
        registry: ghcr.io
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}

    - name: Log in to Docker Hub
      uses: docker/login-action@v4
      with:
        username: ${{ secrets.HUB_USERNAME }}
        password: ${{ secrets.HUB_TOKEN }}

    - uses: its-me/action.image.mirror@v1
      with:
        source: ghcr.io/its-me/workflow
        destination: ${{ secrets.HUB_USERNAME }}/workflow
        tags: ${{ needs.build.outputs.tags }}
```

The action only calls `docker buildx imagetools create` — logging in to the
source and destination registries (via `docker/login-action` or equivalent)
is the caller's responsibility, same as in the original inline jobs.

## Inputs

| Name          | Description                                                                 | Default  |
| ------------- | ------------------------------------------------------------------------------ | -------- |
| `source`      | Source image repository, e.g. `ghcr.io/its-me/workflow`.                        | _(none)_ |
| `destination` | Destination image repository, e.g. `1tsme/workflow` or `quay.io/itsme/workflow`. | _(none)_ |
| `tags`        | Whitespace-separated list of tag names to copy from source to destination.      | _(none)_ |

## License

This project is licensed under the MIT License.

See [LICENSE](LICENSE) for the full text.
