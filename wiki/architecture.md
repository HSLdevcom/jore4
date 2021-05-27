# Architecture

The architecture is in flux as we learn more about the design and requirements.

![Architecture diagram](images/architecture.png "Architecture diagram")

# Cloud infra

This section contains initial documentation about the cloud infra, especially with regards to network layout.

Note that the image below might be re-done completely since plant-uml has not proven to work well for this sort of diagram.

![Cloud infra diagram](images/cloud_infra_network.png "Cloud infra network diagram")

# Ports

This is the list of the ports used by different components in different environments. The cloud envs column depicts the port used inside the docker container (if used), not the _exposed_ port.

|             | Local development | Cloud envs      |
| ----------- | -----------------:| ---------------:|
| jore4-ui    |              3000 |                 |
| jore4-auth  |              3001 |            8080 |
