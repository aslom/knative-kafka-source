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
2019/03/15 11:34:17 Building github.com/knative/eventing-sources/contrib/kafka-container-source/cmd
2019/03/15 11:34:18 Using base gcr.io/distroless/static:latest for github.com/knative/eventing-sources/contrib/kafka-container-source/cmd
2019/03/15 11:34:19 Loading ko.local/cmd-da9621faa5d1b885383c5f2066d4a429:b43a619b71ff8c7cdf8c4bdc9a95502639fd010b83936b1cea3f145215669f36
2019/03/15 11:34:22 Loaded ko.local/cmd-da9621faa5d1b885383c5f2066d4a429:b43a619b71ff8c7cdf8c4bdc9a95502639fd010b83936b1cea3f145215669f36
2019/03/15 11:34:22 Adding tag latest
2019/03/15 11:34:22 Added tag latest
containersource.sources.eventing.knative.dev/my-kafka-source1 unchanged
```

# Testing

Use dumper https://github.com/knative/eventing-sources/blob/master/contrib/gcppubsub/samples/README.md

```
cp $GOPATH/src/github.com/knative/eventing-sources/contrib/gcppubsub/samples/dumper.yaml .
```

```
ko apply -f dumper1.yaml
2019/03/15 11:34:05 Building github.com/knative/eventing-sources/cmd/message_dumper
2019/03/15 11:34:06 Using base gcr.io/distroless/static:latest for github.com/knative/eventing-sources/cmd/message_dumper
2019/03/15 11:34:07 Loading ko.local/message_dumper-147eae4dc6ef877ac915642c58dec291:85912a65f1b3ce0bcfc1aaf306ba3a218c423289e6f2c92220b3c5ee6770d32a
2019/03/15 11:34:13 Loaded ko.local/message_dumper-147eae4dc6ef877ac915642c58dec291:85912a65f1b3ce0bcfc1aaf306ba3a218c423289e6f2c92220b3c5ee6770d32a
2019/03/15 11:34:13 Adding tag latest
2019/03/15 11:34:14 Added tag latest
deployment.apps/message-dumper1 unchanged
service/message-dumper1 unchanged
```

Publish to test topic

```
kafkacat -b ak6.sl.cloud9.ibm.com:9092 -t test -P
Test8
```

Verify it published in kafka

```
kafkacat -b ak6.sl.cloud9.ibm.com:9092 -t test -C
Test1
Test3
Test4
Test5
Test7
Test8
```

Verify it was received

```
$ kail -d message-dumper1 --since=10m

```

Check logs?

```
$ k get pods
NAME                                               READY   STATUS             RESTARTS   AGE
message-dumper1-869f8d5dd-h6m57                    1/1     Running            0          61s
my-kafka-source1-nfhkk-64f6b4544b-ghmsr            2/2     Running            0          49s

$ k logs my-kafka-source1-nfhkk-64f6b4544b-ghmsr
Error from server (BadRequest): a container name must be specified for pod my-kafka-source1-nfhkk-64f6b4544b-ghmsr, choose one of: [source istio-proxy] or one of the init containers: [istio-init]

$ k logs my-kafka-source1-nfhkk-64f6b4544b-ghmsr  -c source
$
```
