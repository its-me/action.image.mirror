# Cross-registry image mirror

A GitHub Action that logs in to a source and destination registry, then
copies each of a set of tags from one image repository to another via
`docker buildx imagetools create`.

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
    - uses: its-me/action.image.mirror@v0
      with:
        source: ghcr.io/its-me/workflow
        source-registry: ghcr.io
        source-username: ${{ github.actor }}
        source-password: ${{ secrets.GITHUB_TOKEN }}
        destination: ${{ secrets.HUB_USERNAME }}/workflow
        destination-username: ${{ secrets.HUB_USERNAME }}
        destination-password: ${{ secrets.HUB_TOKEN }}
        tags: ${{ needs.build.outputs.tags }}
```

For a Quay.io destination, set `destination-registry: quay.io` and point
`destination` at `quay.io/<namespace>/<repository>`.

## Inputs

| Name                    | Description                                                                                 | Default  |
| ------------------------ | ------------------------------------------------------------------------------------------------ | -------- |
| `source`                 | Source image repository, e.g. `ghcr.io/its-me/workflow`.                                          | _(none)_ |
| `source-registry`        | Registry hostname to log in to for the source image, e.g. `ghcr.io`. Leave unset for Docker Hub.  | `""`     |
| `source-username`        | Username for logging in to the source registry.                                                   | _(none)_ |
| `source-password`        | Password or token for logging in to the source registry.                                          | _(none)_ |
| `destination`             | Destination image repository, e.g. `1tsme/workflow` or `quay.io/itsme/workflow`.                  | _(none)_ |
| `destination-registry`   | Registry hostname to log in to for the destination image, e.g. `quay.io`. Leave unset for Docker Hub. | `""`     |
| `destination-username`   | Username for logging in to the destination registry.                                              | _(none)_ |
| `destination-password`   | Password or token for logging in to the destination registry.                                     | _(none)_ |
| `tags`                    | Whitespace-separated list of tag names to copy from source to destination.                        | _(none)_ |

## License

This project is licensed under the MIT License.

See [LICENSE](LICENSE) for the full text.
