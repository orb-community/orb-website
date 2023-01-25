# Running Orb Agent

An Orb agent needs to run on all the infrastructure (computers, servers, switches, VMs, k8s, etc.) to be monitored. It is a small, lightweight Docker process with an embedded [pktvisor agent](https://pktvisor.dev) which connects into the Orb control plane to receive policies and send its metric output.

To run an agent, you will need:


1. Docker, to run the agent image ([ns1labs/orb-agent:develop](https://hub.docker.com/repository/docker/ns1labs/orb-agent))
2. [Agent Credentials](#agent-credentials), which are provided to you by the Orb UI or REST API after [creating an agent](/getting_started/#create-agent-credentials)
3. The Orb Control Plane host address (e.g. `localhost` or `orb.live`)

!!! tip

    If you are unsure which network interface to monitor, you may list the available interfaces on your host. Note that to allow 
    the agent access to these interfaces, you must run the container with `--net=host`
    

    === "Linux"

        ``` shell 
        ip -stats -color -human addr
        ```


    === "OSX"

        ``` shell 
        ifconfig
        ```

## Agent credentials

The agent credentials include *three pieces of information*, each of which is a UUID in the form `5dc34ded-6a53-44c0-8d15-7e9c8c95391a`.

1. ==Agent ID==, which uniquely identifies the agent.
2. ==Agent Channel ID==, which uniquely identifies the agent's communication channel.
3. ==Agent Key==, which is a private access token for the agent. Note you will only be shown the key once upon creation!

## Sample provisioning commands
!!! example

    === "Generic"

      Use this command as a template by substituting in the appropriate values:

      ``` shell 
        docker run -d --net=host
        -e ORB_CLOUD_ADDRESS=<HOST>
        -e ORB_CLOUD_MQTT_ID=<AGENTID>
        -e ORB_CLOUD_MQTT_CHANNEL_ID=<CHANNELID>
        -e ORB_CLOUD_MQTT_KEY=<AGENTKEY>
        -e PKTVISOR_PCAP_IFACE_DEFAULT=auto
        ns1labs/orb-agent
      ```
      
    === "Localhost, Docker Compose"
        
      This command is useful for connecting to a local develop environment, perhaps running on [Docker compose](/documentation/install/#orb-with-docker-compose). 
      Note that the "mock" interface will generate random traffic rather than observe real traffic.

      ```  shell 
        docker run -d --net=host
        -e ORB_CLOUD_ADDRESS=localhost
        -e ORB_CLOUD_MQTT_ID=7fb96f61-5de1-4f56-99d6-4eb8b43f8bad
        -e ORB_CLOUD_MQTT_CHANNEL_ID=3e60e85d-4414-44d9-b564-0c1874898a4d
        -e ORB_CLOUD_MQTT_KEY=44e42d90-aaef-45de-9bc2-2b2581eb30b3
        -e PKTVISOR_PCAP_IFACE_DEFAULT=mock
        -e ORB_TLS_VERIFY=false
        ns1labs/orb-agent
      ```

    === "Orb.live, eth0"
        
      This command is similar to one you would use on the orb.live SaaS platform

      ``` shell 
        docker run -d --net=host
        -e ORB_CLOUD_ADDRESS=orb.live
        -e ORB_CLOUD_MQTT_ID=7fb96f61-5de1-4f56-99d6-4eb8b43f8bad
        -e ORB_CLOUD_MQTT_CHANNEL_ID=3e60e85d-4414-44d9-b564-0c1874898a4d
        -e ORB_CLOUD_MQTT_KEY=44e42d90-aaef-45de-9bc2-2b2581eb30b3
        -e PKTVISOR_PCAP_IFACE_DEFAULT=eth0
        ns1labs/orb-agent
      ```

    === "Specifying agent port"
      You may want to run more than one agent on the same node and for that you must specify different pktvisor control ports for them, since the containers run in host networking mode, only one is allowed to run per port. By default, the pktvisor control port runs on port *10853*, but this value can be set through the environment variable `ORB_BACKENDS_PKTVISOR_API_PORT`


      ``` shell 
        docker run -d --net=host
        -e ORB_CLOUD_ADDRESS=orb.live
        -e ORB_CLOUD_MQTT_ID=7fb96f61-5de1-4f56-99d6-4eb8b43f8bad
        -e ORB_CLOUD_MQTT_CHANNEL_ID=3e60e85d-4414-44d9-b564-0c1874898a4d
        -e ORB_CLOUD_MQTT_KEY=44e42d90-aaef-45de-9bc2-2b2581eb30b3
        -e PKTVISOR_PCAP_IFACE_DEFAULT=eth0
        -e ORB_BACKENDS_PKTVISOR_API_PORT=10854
        ns1labs/orb-agent
      ```

    === "🎁 BONUS - Debug"
      You can access agent debug logs by passing the `-d` command


      ``` shell 
        docker run -d --net=host
        -e ORB_CLOUD_ADDRESS=orb.live
        -e ORB_CLOUD_MQTT_ID=7fb96f61-5de1-4f56-99d6-4eb8b43f8bad
        -e ORB_CLOUD_MQTT_CHANNEL_ID=3e60e85d-4414-44d9-b564-0c1874898a4d
        -e ORB_CLOUD_MQTT_KEY=44e42d90-aaef-45de-9bc2-2b2581eb30b3
        -e PKTVISOR_PCAP_IFACE_DEFAULT=eth0
        ns1labs/orb-agent run -d
      ```

!!! question

    Is the agent Docker image not starting correctly? Do you have a specific use case? Have you found a bug? Come talk to us [live on Slack](https://netdev.chat/) in the `#orb` channel,
    or [file a GitHub issue here](https://github.com/orb-community/orb/issues/new/choose).

## Configuration files

Most configuration options can be passed to the container as environment variables, but there are some situations that require a configuration file.

You will need to use a configuration file if:

* You want to assign tags to the agent at the edge
* You want to set up custom pktvisor Taps
* You want the agent to [auto-provision](#advanced-auto-provisioning-setup)

The configuration file is written in YAML.
You can use the latest [template configuration file](https://raw.githubusercontent.com/orb-community/orb/develop/cmd/agent/agent.example.yaml) as a starting point and check more information in [Configuring Orb Agent](/documentation/orb_agent_configs/#orb-agent-configuration), or
start here:

```yaml
# this section is used by orb-agent
# most sections and keys are optional
orb:
   # these are arbitrary key value pairs used for dynamically define a group of agents by matching against agent group tags 
   tags:
      region: EU
      pop: ams02
      node_type: dns
   cloud:
      config:
         # optionally specify an agent name to use during auto provisioning
         # hostname will be used if it's not specified here
         agent_name: my-agent1
         auto_provision: true
      api:
         address: https://orb.live
         # if auto provisioning, specify API token here (or pass on the command line)
         token: TOKEN
      mqtt:
         address: tls://agents.orb.live:8883
         # if not auto provisioning, specify agent connection details here
         id: "AGENT_UUID"
         key: "AGENT_KEY_UUID"
         channel_id: "AGENT_CHANNEL_UUID"
   backends:
      pktvisor:
         binary: "/usr/local/sbin/pktvisord"
         # this example assumes the file is saved as agent.yaml. If your file has another name, you must replace it with the proper name
         config_file: "/opt/orb/agent.yaml"

version: "1.0"

# this section is used by pktvisor
# see https://orb.community/documentation/orb_agent_configs/#pktvisor-configuration
visor:
   taps:
      default_pcap:
         input_type: pcap
         config:
            iface: "eth0"
            host_spec: "192.168.0.54/32,192.168.0.55/32,127.0.0.1/32"
```

You must mount your configuration file into the `orb-agent` container. For example, if your configuration file
is on the host at `/local/orb/agent.yaml`, you can mount it into the container with this command:

```shell
docker run -v /local/orb:/opt/orb/ --net=host \
      ns1labs/orb-agent run -c /opt/orb/agent.yaml
```


## Advanced auto-provisioning setup
Some use cases require a way to provision agents directly on edge infrastructure without creating an agent manually in the UI or REST API ahead of time. To do so, you will need to create an API key which can be used by `orb-agent` to provision itself.

!!! warning

    Auto-provisioning is an advanced use case. Most users will find [creating an agent in the UI](/getting_started/#create-agent-credentials) easier.

1. If you have not already done so, register a new account with an email address and password at https://HOST/auth/register.

2. Create a `SESSION_TOKEN` with the `EMAIL_ADDRESS` and `PASSWORD` from registration:

        curl --location --request POST 'https://HOST/api/v1/tokens' \
        --header 'Content-Type: application/json' \
        --data-raw '{
        "email": "<EMAIL_ADDRESS>",
        "password": "<PASSWORD>"
        }'

3. The output from creating a session token looks like this:

        {
            "token": "SESSION_TOKEN"
        }

4. Because session tokens expire after 24 hours, you can create a permanent API token for agent provisioning by using the `SESSION_TOKEN` above:

        curl --location --request POST 'https://HOST/api/v1/keys' \
        --header 'Authorization: <SESSION_TOKEN>' \
        --header 'Content-Type: application/json' \
        --data-raw '{
        "type": 2
        }'

5. The output from creating a `PERMANENT_TOKEN` looks like the following. Please take note of the `id` (used later to revoke) and the `value` (the permanent API token):

        {
            "id": "710c6a92-b463-42ec-bf24-8ae24eb13081",
            "value": "PERMANENT_TOKEN",
            "issued_at": "2021-09-07T15:29:49.70146088Z"
        }

6. ==Currently, the permanent token allows access to all API functionality, not just provisioning.== You can revoke this permanent token at any time with the following call, using the `id` field above:

        curl --location --request DELETE 'HOST:80/api/v1/keys/<PERMANENT_TOKEN_ID>' \
        --header 'Authorization: <SESSION_TOKEN>'

7. Create a config for Orb and pktvisor taps, for example, `/opt/orb/agent.yaml`:
```yaml
version: "1.0"

visor:
   taps:
      ethernet:
         input_type: pcap
         config:
            iface: "eth0"

orb:
   db:
      file: /usr/local/orb/orb-agent.db
   tags:
      region: EU
      pop: ams02
      node_type: dns
   cloud:
      config:
         agent_name: myagent1
      api:
         address: https://HOST
      mqtt:
         address: tls://HOST:8883
```

8. You can now pull and run `ns1labs/orb-agent` to auto-provision, substituting in the `PERMANENT_TOKEN` and optionally configuring agent name and Orb tags. If you don't set the agent name, it will attempt to use a hostname. You must mount the directory to save the agent state database and the config file:

```shell
docker pull ns1labs/orb-agent
docker run -v /local/orb:/opt/orb/ --net=host \
       -e ORB_CLOUD_API_TOKEN=<PERMANENT_TOKEN> \
      ns1labs/orb-agent run -c /opt/orb/agent.yaml
```
