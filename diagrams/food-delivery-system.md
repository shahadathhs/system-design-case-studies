# Food Delivery System Architecture

```mermaid
graph TD
    subgraph Clients
        CustomerApp[Customer Mobile/Web App]
        RestaurantApp[Restaurant Portal/App]
        DriverApp[Driver Mobile App]
    end

    CustomerApp -- HTTPS/WS --> API_GW[API Gateway]
    RestaurantApp -- HTTPS/WS --> API_GW
    DriverApp -- HTTPS/WS --> API_GW

    API_GW -- gRPC/HTTPS --> CustomerSvc[Customer Service]
    API_GW -- gRPC/HTTPS --> RestaurantSvc[Restaurant Service]
    API_GW -- gRPC/HTTPS --> DriverSvc[Driver Service]
    API_GW -- gRPC/HTTPS --> OrderSvc[Order Service]
    API_GW -- gRPC/HTTPS --> MatchingSvc[Matching Service]
    API_GW -- gRPC/HTTPS --> PaymentSvc[Payment Service]
    API_GW -- gRPC/HTTPS --> NotifSvc[Notification Service]
    API_GW -- gRPC/HTTPS --> RatingSvc[Rating Service]
    API_GW -- gRPC/HTTPS --> SearchSvc[Search & Discovery Service]

    DriverApp -- WS/MQTT --> LocSvc[Location Service]
    CustomerApp -- WS --> LocSvc

    MatchingSvc -- Query --> LocSvc
    MatchingSvc -- Query --> DriverSvc
    MatchingSvc -- Query --> RestaurantSvc
    MatchingSvc -- Update --> OrderSvc

    OrderSvc -- Async --> MessageBroker[Message Broker (Kafka)]

    PaymentSvc -- Integrates --> PaymentGateway[External Payment Gateway]

    SearchSvc <--> Elastic[Elasticsearch]
    SearchSvc -- Indexing --> RestaurantSvc
    SearchSvc -- Indexing --> MenuSvc[Menu Service]

    MessageBroker --> NotifSvc
    MessageBroker --> RatingSvc
    MessageBroker --> DataWarehouse[Data Warehouse for Analytics]
    MessageBroker --> OrderSvc
    MessageBroker --> MatchingSvc
    MessageBroker --> PaymentSvc

    subgraph Databases
        CustomerDB[(Customer DB - PostgreSQL)]
        RestaurantDB[(Restaurant DB - PostgreSQL)]
        DriverDB[(Driver DB - PostgreSQL)]
        OrderDB[(Order DB - PostgreSQL)]
        PaymentDB[(Payment DB - PostgreSQL)]
        RatingDB[(Rating DB - PostgreSQL)]
        LocationCache[(Location Cache - Redis/PostGIS)]
    end

    CustomerSvc <--> CustomerDB
    RestaurantSvc <--> RestaurantDB
    DriverSvc <--> DriverDB
    OrderSvc <--> OrderDB
    PaymentSvc <--> PaymentDB
    RatingSvc <--> RatingDB
    LocSvc <--> LocationCache
```
