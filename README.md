# logspout-firehose

[Logspout](https://github.com/gliderlabs/logspout) adapter for writing Docker container logs to [AWS Kinesis Firehose](https://aws.amazon.com/kinesis/firehose/)

## Deploy

Build a logspout image with this firehose adapter (see Build section below).

Then run using whatever docker launch method you prefer. The important things to note are the AWS config ENV vars, the volume mount for docker socket, and the firehose launch command.

```
$ docker run --name="logspout" \
    --volume=/var/run/docker.sock:/var/run/docker.sock \
    -e AWS_ACCESS_KEY_ID=... \
    -e AWS_REGION=us-east-1 \
    -e AWS_SECRET_ACCESS_KEY=... \
    your-logspout-image \
    firehose://delivery-stream-name
```

See the logspout docs for more launch options.

## Build

```sh
# Build logspout locally with custom fluentd module using Dockerfile
docker build --build-arg LOGSPOUT_VERSION=master -t daysofwonder/logspout:v3.2.11-dow01 .

# Example to run custom built logspout locally:
docker run -it --rm -e DEBUG=yes -e LOG_CONTENT=yes -e DEBUG_CONTAINERS=a4d1cae3aaf8 -e ALLOW_TTY=true -e HTTP_PORT=8181 -e AWS_REGION=us-east-1 -e BACKLOG=false -e TAIL=0 -v $HOME/.aws:/root/.aws -e AWS_PROFILE=admin -e AWS_SDK_LOAD_CONFIG=1 -v /var/run/docker.sock:/var/run/docker.sock daysofwonder/logspout:v3.2.11-dow01 firehose://test
```

Then start a container:

```sh
docker run -p 1234:1234 -it alpine/socat tcp-listen:1234,fork,reuseaddr STDOUT
```

And echo a few log lines:

```sh
echo '{"message":"toto"}{"message":"titi"}' | nc localhost 1234
```
