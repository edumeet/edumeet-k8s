# ![eduMEET](https://github.com/edumeet/edumeet-docker/raw/main/images/logo.edumeet.svg) with k8s for version (4.x)

This is a "k8s" version of [eduMEET](https://github.com/edumeet/edumeet). 

Install 
```
helm install edumeet-core --values edumeet-k8s/helm/edumeet-core/values.yaml edumeet-k8s/helm/edumeet-core/
helm install edumeet-media --values edumeet-k8s/helm/edumeet-media/values.yaml edumeet-k8s/helm/edumeet-media/
```
Uninstall 
```
helm uninstall edumeet-core
helm uninstall edumeet-media
```
Edumeet simple setup with authentication (by helm files) 
```mermaid
graph TD;
    subgraph "edumeet-core"
        Management-service
        eduMEET-room-server
        eduMEET-client
        eduMEET-management-server
        DB
    end
    Proxy-->eduMEET-room-server;
    Proxy-->eduMEET-client;
    Proxy-->Management-service;
    Management-service-->eduMEET-management-server;
    eduMEET-management-server-->DB;
    subgraph "edumeet-media"
      Media-service-->TURN-server;
      Media-service-->eduMEET-media-node;
    end
    Proxy-->edumeet-media;


```


Edumeet setup (by components)
```mermaid
graph TD;

    subgraph "Frontend"
        eduMEET-client
    end

    subgraph "Media-service pool"
        eduMEET-media-node-A
        eduMEET-media-node-B
        TURN-server-A
        TURN-server-B
    end

    subgraph "eduMEET Backend"
        eduMEET-room-server
    end

    subgraph "Management Services"
        Management-service
        eduMEET-management-server
        Tenant-A
        Tenant-B
        Tenant-C
        DB
    end
    
    subgraph "External Services"
        OIDC-provider-A
        OIDC-provider-B
        OIDC-provider-federation
    end

    subgraph "eduMEET Domain(s)"
        private-domain
    end

    K8S --> private-domain
    private-domain --> eduMEET-room-server
    private-domain --> eduMEET-client
    private-domain --> Media-service
    private-domain --> Management-service

    Management-service --> eduMEET-management-server
    eduMEET-management-server --> Tenant-A
    eduMEET-management-server --> Tenant-B
    eduMEET-management-server --> Tenant-C
    eduMEET-management-server --> DB

    Tenant-A --> OIDC-provider-A
    Tenant-B --> OIDC-provider-B
    Tenant-C --> OIDC-provider-federation

```
For the authentication to work:

The room server config has to have a management server defined

The edumeet client has to have managementUrl and loginEnabled parameters.

If the default roles are defined in the room server it will be used for an unmanaged room.

There is no current default role options for a tenant, only for a managed room inside of the tenant.

There is an option to define multiple media nodes, the server will ping them periodically and try to load balance between them. 

Changing configuration at the moment requires restart for the room-server/media-node/management-server pods.
