docker build -t hello-go:ubuntu-latest -f Dockerfile.ubuntu .
docker buildx build . -f Dockerfile.ubuntu-ms --platform linux/amd64 -t hello-go:ubuntu-latest-ms
docker build -t hello-go:alpine-latest -f Dockerfile.alpine .
docker buildx build . -f Dockerfile.alpine-ms --platform linux/amd64 -t hello-go:alpine-latest-ms
docker run --privileged --rm -v "${PWD}":/work   cgr.dev/chainguard/melange build melange.yaml   --arch amd64,aarch64   --signing-key melange.rsa
docker run --rm --workdir /work -v ${PWD}:/work cgr.dev/chainguard/apko   build apko.yaml hello-go:test hello-go.tar --arch host

melange build melange.yaml --arch amd64 --git-repo-url https://github.com/maligin/hello-melange-apko-go.git --signing-key melange.rsa && \
 apko build apko.yaml hello-go:test hello-go.tar --arch host && \
 docker load < hello-go.tar

docker buildx build . -f Dockerfile.chainguard-go --platform linux/amd64 -t hello-go:go-latest 
