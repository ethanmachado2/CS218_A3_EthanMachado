# CS218_A3_EthanMachado
CS218 Assignment 3 Repository for Ethan Machado

Due date: 03/17/2026

Local setup steps:

1. Activate python virtual environment using the following command: "source venv/bin/activate".
2. Execute the following command to build and start the api and postgres containers: "docker-compose up -d --build".
3. Execute the following command to display all logs generated while the containers are running: "docker-compose logs -f api".
4. To perform a health check, use the following command: "curl -i http://localhost:8080/health".
5. To perform a database migration, use the following commands: "docker-compose run --rm api flask db init \ docker-compose run --rm api flask db migrate -m "your_migration_message" \ docker-compose run --rm api flask db upgrade".
6. Create an order using the following curl command: curl http://localhost:8080/orders -H "Content-Type:application/json" -H "Idempotency-Key:test-001" -d '{"customer_id":"cust1","item_id":"item1","quantity":1}'.
7. To restart the api container service, use the following command: "docker-compose restart api".
8. To confirm persistence across an API restart, use the following command: "curl -s http://localhost:8080/orders/{order_id}".
9. To restart the postgres container service, use the following command: "docker-compose restart postgres".
10. To confirm postgres volume persistence, use the following command: "curl -s http://localhost:8080/orders/{order_id}".

Locust test summary:

Configuration:

Number of users: 50
Ramp up (users/second): 5
Host: http://localhost:8080
Run time: 2 minutes

<img width="1214" height="765" alt="image" src="https://github.com/user-attachments/assets/18885d2a-8df8-40a5-8b1a-3529c490c7d5" />

Locust results: 33.2 RPS, 22 P95 (ms), 46 P99 (ms)

<img width="1215" height="551" alt="image" src="https://github.com/user-attachments/assets/34c03e00-9ffe-4316-a27b-1b924fa0026c" />

<img width="1215" height="769" alt="image" src="https://github.com/user-attachments/assets/99e61288-bbfa-4304-a63f-3f00548b19cb" />

<img width="1213" height="387" alt="image" src="https://github.com/user-attachments/assets/c49d83ce-1db8-4d49-9cbe-394e154b5959" />

Brief analysis of Locust results: The two initial failures occurred due to no {order_id} being found in the orders DB table. This is correct behavior because the test script stores created orders in a list. At the beginning of the stress test, no orders are created. Thus, a particular request can return a 404 code. Overall, the application exhibited strong stability with only 2 errors out of 3871 requests. In terms of performance, the application exhibited solid metrics. The P95 and P99 metrics were both under 50 ms in the aggregate. Thus, the application exhibits good scalability under load.

Steps to deploy to AWS:
