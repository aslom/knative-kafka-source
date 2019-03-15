# knative-kafka-source
Notes on knative-kafka-source

Installing ko

Use local docker

```
export KO_DOCKER_REPO=ko.local
```

Checkout fork
https://github.com/knative/eventing-sources/blob/master/DEVELOPMENT.md#checkout-your-fork

Get pull request
https://github.com/knative/eventing-sources/pull/246
following
https://help.github.com/en/articles/syncing-a-fork

```
git fetch upstream
git fetch upstream pull/246/head:kafka_container_src246
git checkout kafka_container_src246
```

# Building

```
cd $GOPATH/src/github.com/knative/eventing-sources/contrib/kafka-container-source/samples
```

```
cp sources_v1alpha2_kafka_container_source.yaml sources_v1alpha2_kafka_container_source1.yaml
```

Edit sources_v1alpha2_kafka_container_source1.yaml 

Chnage sink to

```
 sink:
    apiVersion: v1
    kind: Service
    name: message-dumper
```

```
 ko apply -f sources_v1alpha2_kafka_container_source1.yaml
2019/03/15 11:03:02 Building github.com/knative/eventing-sources/contrib/kafka-container-source/cmd
2019/03/15 11:03:10 Using base gcr.io/distroless/static:latest for github.com/knative/eventing-sources/contrib/kafka-container-source/cmd
2019/03/15 11:03:11 Loading ko.local/cmd-da9621faa5d1b885383c5f2066d4a429:b43a619b71ff8c7cdf8c4bdc9a95502639fd010b83936b1cea3f145215669f36
2019/03/15 11:03:16 Loaded ko.local/cmd-da9621faa5d1b885383c5f2066d4a429:b43a619b71ff8c7cdf8c4bdc9a95502639fd010b83936b1cea3f145215669f36
2019/03/15 11:03:16 Adding tag latest
2019/03/15 11:03:16 Added tag latest
containersource.sources.eventing.knative.dev/my-kafka-source created
```

# Testing

Use dumper https://github.com/knative/eventing-sources/blob/master/contrib/gcppubsub/samples/README.md

```
cp $GOPATH/src/github.com/knative/eventing-sources/contrib/gcppubsub/samples/dumper.yaml .
```

```
ko apply -f dumper.yaml
2019/03/15 11:18:27 Building github.com/knative/eventing-sources/cmd/message_dumper
2019/03/15 11:18:27 Building github.com/knative/eventing-sources/cmd/message_dumper
2019/03/15 11:18:29 Using base gcr.io/distroless/static:latest for github.com/knative/eventing-sources/cmd/message_dumper
2019/03/15 11:18:30 Loading ko.local/message_dumper-147eae4dc6ef877ac915642c58dec291:85912a65f1b3ce0bcfc1aaf306ba3a218c423289e6f2c92220b3c5ee6770d32a
2019/03/15 11:18:32 Loaded ko.local/message_dumper-147eae4dc6ef877ac915642c58dec291:85912a65f1b3ce0bcfc1aaf306ba3a218c423289e6f2c92220b3c5ee6770d32a
2019/03/15 11:18:32 Adding tag latest
2019/03/15 11:18:32 Added tag latest
deployment.apps/message-dumper created
Warning: kubectl apply should be used on resource created by either kubectl create --save-config or kubectl apply
service/message-dumper configured
```

Publish to test topic



Verify it was received

```
kail -d message-dumper --since=10m
```