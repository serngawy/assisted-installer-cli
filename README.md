This is a sample python client on top of the generated assisted-installer python library to ease working with assisted installer

Available features:

- create/delete cluster (autoinjecting pull secret file and ssh public key)
- create/download discovery iso
- list cluster/hosts
- update hostnames and host roles
- update cluster attributes such as api vip and ingress vip 
- launch cluster install

# Deploying

## Cli

First, you need to clone the [openshift/assisted-service](https://github.com/openshift/assisted-service) repository. Then proceed to install the assisted-installer autogenerated python library, with the following steps:

```
ENGINE="docker"
curl https://raw.githubusercontent.com/openshift/assisted-service/master/Dockerfile.assisted-service > Dockerfile.assisted-service
$ENGINE build -t ocpmetal/assisted-service -f Dockerfile.assisted-service .
$ENGINE run -v $PWD:/here --rm ocpmetal/assisted-service:latest cp -r /clients/assisted-service-client-1.0.0.tar.gz /here
tar zxvf assisted-service-client-1.0.0.tar.gz
cd assisted-service-client-1.0.0
python3 setup.py install
```

Then you can install the cli with

```
python3 setup.py install
```

## Assisted installer Plan

To deploy assisted installer, you can use kcli with the provided plan in the kcli_plan directory

```
cd kcli_plan
kcli create plan
``` 

# How to use

## Setting target url

You need to indicate a target AI_URL, which can be done with the flag *--url*

The url is in this format `http://$AI_IP:8080` 

Alternatively, you can set it as an environment variable

## Parameters

For most of the commands, you can pass parameters either on the command line by repeating `-P key=value` or by putting them in a parameter file, in yaml format.

If present, the file  `ai_parameters.yml` is also parsed.

## Using the cli

### Help

```
aicli -h
```

### List 

```
aicli list cluster
```

```
aicli list host
```

## Creating/Deleting a cluster

```
aicli create cluster myclu
```

```
aicli delete cluster myclu
```

## Get info

```
aicli info cluster mycluster
```

```
aicli info host myhost
```

## Update objects

```
aicli update cluster myclu -P api_vip=192.168.122.253
```

```
aicli update host host_id -P name=coolname.testk.karmalabs.com
```

The following command can be used to update the names of all the nodes named localhost to master-X instead in each cluster

```
aicli update host localhost -P role=master
```

## Handling iso

```
aicli create iso myclu
aicli download iso myclu
```

#3 Update data

```
aicli create iso myclu
aicli download iso myclu
```

## Launch an install

```
aicli start cluster myclu
```

## Add extra workers

For this purpose, we assume we already have an installer cluster (named myclu). When creating a new cluster with the same name and the '-day2' extension, the api code will create a dedicated cluster for adding host purposes.

```
aicli create cluster myclu-day2
# gather the discovery iso and launch hosts as usual then
aicli start cluster myclu-day2
```
