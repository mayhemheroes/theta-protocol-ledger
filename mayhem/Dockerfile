FROM golang:1.17.13 as fuzzbuilder
ARG project='theta-protocol-ledger'
ARG fuzzTargetPath='crypto/bn256'

ADD . ${project}
WORKDIR /go/${project}/${fuzzTargetPath}

RUN go get -u github.com/dvyukov/go-fuzz/go-fuzz@latest github.com/dvyukov/go-fuzz/go-fuzz-build@latest
RUN GO_ENABLED=0 go-fuzz-build -libfuzzer -func=FuzzAdd -o /add-fuzz.a .

FROM silkeh/clang:latest as sanitizer
COPY --from=fuzzbuilder /add-fuzz.a add-fuzz.a
RUN clang -fsanitize=fuzzer /add-fuzz.a -o /add-fuzz.libfuzzer

FROM ubuntu:20.04
COPY --from=sanitizer /add-fuzz.libfuzzer /
