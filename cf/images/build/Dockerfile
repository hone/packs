ARG stack
FROM packs/${stack}

ARG buildpacks

WORKDIR /workspace

RUN \
  mkdir /var/lib/buildpacks && \
  echo "${buildpacks}" > /var/lib/buildpacks/config.json && \
  echo "${buildpacks}" | jq -c '.[]' | while read row; do \
    name=$(echo "${row}" | jq -r '.name') && \
    uri=$(echo "${row}" | jq -r '.uri') && \
    curl -fsSLo /tmp/buildpack.zip "$uri" && \
    unzip -qq /tmp/buildpack.zip -d "/var/lib/buildpacks/$(echo -n "$name" | md5sum | awk '{ print $1 }')" && \
    rm /tmp/buildpack.zip; \
  done

RUN mkdir /packs
COPY builder /packs/

ENTRYPOINT [ \
  "/packs/builder", \
  "-buildpacksDir", "/var/lib/buildpacks", \
  "-outputDroplet", "/out/droplet.tgz", \
  "-outputBuildArtifactsCache", "/cache/cache.tgz", \
  "-outputMetadata", "/out/result.json" \
]

