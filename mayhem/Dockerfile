# Grab go 1.17 image
FROM golang:1.17.13 as fuzzbuilder
ARG project='theta-protocol-ledger'
ARG fuzzTargetPath='crypto/bn256'
# Navigate to fuzz directory
ADD . ${project}
WORKDIR /go/${project}/crypto/bn256
# Install go-fuzz-build
RUN go get -u github.com/dvyukov/go-fuzz/go-fuzz@latest github.com/dvyukov/go-fuzz/go-fuzz-build@latest
# Compile fuzzers
RUN GO_ENABLED=0 go-fuzz-build -libfuzzer -func=FuzzAdd -o /fuzz.a .

# Sanitize fuzzers in clang image
FROM silkeh/clang:latest as sanitizer
COPY --from=fuzzbuilder /fuzz.a fuzz.a
RUN clang -fsanitize=fuzzer /fuzz.a -o /fuzz.libfuzzer

# Copy sanitized fuzzers to ubuntu
FROM ubuntu:20.04
COPY --from=sanitizer /fuzz.libfuzzer /
