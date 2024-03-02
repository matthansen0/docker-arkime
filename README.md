# [Arkime](https://github.com/arkime/arkime) Docker image/container

This repository contains the files to build a Docker image of the Arkime software:

**https://arkime.com/**

Ready to use Docker images can be pulled from https://hub.docker.com/r/mammo0/docker-arkime

To run this image a **working OpenSearch** environment is required. Please stick to their documentation for setting this up. (E.g. for Docker: https://opensearch.org/docs/latest/opensearch/install/docker)



### Run the container
This image can be run with

```shell
docker run \
    -e OS_HOST=opensearch \
    -e OS_PORT 9200 \
    -v <config_volume>:/data/config \
    -v <pcap_volume>:/data/pcap \
    -v <log_volume>:/data/logs \
    mammo0/docker-arkime:<tag>
```

These parameters are available:


| ENV VARIABLE | DEFAULT VALUE |  NOTES |
| - | - | - |
| OS_HOST | opensearch | The hostname or IP address where OpenSearch is running.  |
| OS_PORT | 9200 | The port where OpenSearch is reachable. |
| ARKIME_ADMIN_PASSWORD | admin | This is the password for the Arkime admin user that is needed for the first login. It can be changed afterwards. |
| ARKIME_INTERFACE | eth0 | Network interface where `capture` process listens. **This feature is not tested yet!** |
| ARKIME_HOSTNAME | localhost | With this hostname Arkime authenticates at OpenSearch. So if you change this you'll lose your OpenSearch configuration. |
| CAPTURE | off | Set to "on" to activate the `capture` process. **This feature is not tested yet!** |
| VIEWER | on | Set to "off" to deactivate the Arkime viewer. |

| VOLUME | CONTAINER PATH | NOTES |
| - | - | - |
| config_volume | /data/config | In this directory the Arkime configuration is saved. |
| pcap_volume | /data/pcap | In this directory the pcap files are saved that are generated by `capture`. |
| log_volume | /data/logs | In this directory the Arkime log files are saved. |



### Use `docker-compose`
You can use `docker-compose` if you want to setup OpenSearch together with Arkime in one step.

#### Configuration
1. Copy the file `env/docker-compose.env` to `.env` **before** running `docker-compose up`:
    ```shell
    cp env/docker-compose.env .env
    ```
    You may want to change some of the variables inside. Each variable is documented there.

2. Check the settings in `env/arkime-compose.env` file. These are used for the Arkime container. The available settings and values are documented in the `ENV VARIABLE` table above.

3. Ensure you have done this step (otherwise OpenSearch won't start): https://opensearch.org/docs/latest/opensearch/install/docker/#important-host-settings

    To apply the setting on a live system, run:
    ```shell
    sysctl -w vm.max_map_count=262144
    ```

#### Start
Simply run
```shell
docker-compose up -d
```
*It can take some time until the OpenSearch instance(s) is/are fully started.*

The Arkime viewer instance can be accessed via

http://localhost:8005

*Assuming you don't changed the default port in the `.env` file.*


### Import pcap files
To import existing pcap files have a look at `scripts/arkime-parse-pcap-folder.sh` script. It can be run within the running container:

```shell
docker exec -it <container_name> \
    /data/arkime-parse-pcap-folder.sh -d <directory_with_pcap_files> -t <tag>
```

If parameter `-d` is omitted, the script asks if you want to import the default direcotry `/data/pcap`.
