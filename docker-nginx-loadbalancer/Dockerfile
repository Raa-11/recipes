# STEP 1 build executable binary
FROM golang:alpine@sha256:cdc86d9f363e8786845bea2040312b4efa321b828acdeb26f393faa864d887b0 AS builder

RUN adduser -D -g '' appuser
RUN mkdir /app

WORKDIR /app

# copy go mod and sum files
COPY go.mod .
COPY go.sum .

RUN go mod download

# copy the source code
COPY main.go .

# build the binary
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -a -installsuffix cgo -ldflags="-w -s" -o /go/bin/app .

RUN chmod 0555 /go/bin/app

# STEP 2 build a small image
# start from scratch
FROM scratch

WORKDIR /app

COPY --from=builder /etc/passwd /etc/passwd

USER appuser

# Copy our static executable
COPY --from=builder /go/bin/app ./app_binary

EXPOSE 5000

CMD ["./app_binary"]
