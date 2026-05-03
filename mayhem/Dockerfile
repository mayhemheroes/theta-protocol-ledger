FROM golang:1.25 AS fuzzbuilder
ARG project='theta-protocol-ledger'
ARG fuzzTargetPath='crypto/bn256'

WORKDIR /go/src/github.com/thetatoken/theta
ADD . .

RUN go install github.com/dvyukov/go-fuzz/go-fuzz@latest github.com/dvyukov/go-fuzz/go-fuzz-build@latest
RUN GOFLAGS=-mod=mod go get github.com/dvyukov/go-fuzz/go-fuzz-dep
RUN CGO_ENABLED=1 go-fuzz-build -libfuzzer -func=FuzzAdd -o /add-fuzz.a ./${fuzzTargetPath}

FROM silkeh/clang:latest AS sanitizer
COPY --from=fuzzbuilder /add-fuzz.a add-fuzz.a
RUN clang -fsanitize=fuzzer /add-fuzz.a -o /add-fuzz.libfuzzer

FROM ubuntu:20.04
COPY --from=sanitizer /add-fuzz.libfuzzer /
