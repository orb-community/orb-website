# Documentation
Find process-oriented instructions and descriptions below to help you get moving with Orb.

## Running Orb Agent
### Sample provisioning command
Replace `mock` interface with a host network interface (e.g. `eth0`).

        docker run -d --net=host
        -e ORB_CLOUD_ADDRESS=<HOST>
        -e ORB_CLOUD_MQTT_ID=<AGENTID>
        -e ORB_CLOUD_MQTT_CHANNEL_ID=<CHANNELID>
        -e ORB_CLOUD_MQTT_KEY=<AGENTKEY>
        -e PKTVISOR_PCAP_IFACE_DEFAULT=mock
        ns1labs/orb-agent

### Advanced auto-provisioning setup
1. If you have not already done so, register a new account with an email address and password at https://beta.orb.live/auth/register.

2. Create a `SESSION_TOKEN` with the `EMAIL_ADDRESS` and `PASSWORD` from registration:

        curl --location --request POST 'https://orb.live/api/v1/tokens' \
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

        curl --location --request POST 'https://orb.live/api/v1/keys' \
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

6. **Currently, the permanent token allows access to all API functionality, not just provisioning.** You can revoke this permanent token at any time with the following call, using the `id` field above:

        curl --location --request DELETE 'orb.live:80/api/v1/keys/<PERMANENT_TOKEN_ID>' \
        --header 'Authorization: <SESSION_TOKEN>'

7. Create a config for Orb and pktvisor taps, for example, /`local/orb/agent.yaml`:

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
              address: https://orb.live
            mqtt:
              address: tls://orb.live:8883

8. You can now pull and run `ns1labs/orb-agent` to auto-provision, substituting in the `PERMANENT_TOKEN` and optionally configuring agent name and Orb tags. If you don't set the agent name, it will attempt to use a hostname. You must mount the directory to save the agent state database and the config file:

        docker pull ns1labs/orb-agent:develop
    and:

        docker run -v /local/orb:/usr/local/orb/ --rm --net=host \
        -e ORB_CLOUD_API_TOKEN=<PERMANENT_TOKEN> \
        ns1labs/orb-agent:develop -d -c /usr/local/orb/agent.yaml

## Working with API Docs
Follow the links below for API documentation of each respective Orb microservice:

* [Fleet](https://getorb.io/api/fleet.html)
* [Policies](https://getorb.io/api/policies.html)
* [Sinks](https://getorb.io/api/sinks.html)
