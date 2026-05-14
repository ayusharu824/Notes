
What is SAGA pattern ?
A Saga pattern is used in microservices **architecture** to manage a transaction that spans across multiple services/databases.

It helps maintain data consistency when:
1. multiple services are involved
2. each services has its own database
3. distribution transaction is needed

Suppose you have these microservices:

- Order Service
- Payment Service
- Inventory Service
- Shipping Service

Now user places an order.

```
1. Create Order
2. Deduct Payment
3. Reserve Inventory
4. Create Shipment
```

---

# The Problem

What if:

- payment succeeds
- inventory reservation fails

Now system becomes inconsistent

# Why not use normal database transaction?

Traditional SQL transaction:

```
BEGIN TRANSACTION COMMIT ROLLBACK
```

works only:

- inside one database
- inside one service

But in microservices:

- each service has separate DB
- distributed transaction becomes difficult
- 2PC (Two Phase Commit) is slow and not scalable

So Saga is preferred.

Question 
why dont we do this only when data is process from all the microservice then at the last we do db operation for all the tables together?

At first glance it sounds ideal:

```
1. Call all services2. Validate/process everything3. If all succeed → save all DB changes together
```

But in real microservices architecture, this usually does not work well for several reasons.

---

# The Core Problem

In microservices:
Each service owns its own database

Example:

|Service|Own Database|
|---|---|
|Order Service|OrderDB|
|Payment Service|PaymentDB|
|Inventory Service|InventoryDB|

Now no single service should directly write into all databases.

Otherwise:

- services become tightly coupled
- boundaries break
- microservices become mini-monolith
