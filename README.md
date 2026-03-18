# CS218_A3_EthanMachado
CS218 Assignment 3 Repository for Ethan Machado

Due date: 03/17/2026

Local setup steps:

1. Use the following command to clone this respository: "git clone https://github.com/ethanmachado2/CS218_A3_EthanMachado.git && cd CS218_A3_EthanMachado".
2. Activate python virtual environment using the following command: "source venv/bin/activate".
3. Execute the following command to build and start the api and postgres containers: "docker-compose up -d --build".
4. Execute the following command to display all logs generated while the containers are running: "docker-compose logs -f api".
5. To perform a health check, use the following command: "curl -i http://localhost:8080/health".
6. Database migrations are handled via the entrypoint.sh file. The command "flask db upgrade" is executed in the entrypoint.sh script.
7. Note: To perform a manual database migration, use the following commands: "docker-compose run --rm api flask db init \ docker-compose run --rm api flask db migrate -m "your_migration_message" \ docker-compose run --rm api flask db upgrade".
8. Create an order using the following curl command: curl http://localhost:8080/orders -H "Content-Type:application/json" -H "Idempotency-Key:test-001" -d '{"customer_id":"cust1","item_id":"item1","quantity":1}'.
9. To restart the api container service, use the following command: "docker-compose restart api".
10. To confirm persistence across an API restart, use the following command: "curl -s http://localhost:8080/orders/{order_id}".
11. To restart the postgres container service, use the following command: "docker-compose restart postgres".
12. To confirm postgres volume persistence, use the following command: "curl -s http://localhost:8080/orders/{order_id}".

Locust test summary:

Configuration:

Number of users: 50;
Ramp up (users/second): 5;
Host: http://localhost:8080;
Run time: 2 minutes

<img width="1214" height="765" alt="image" src="https://github.com/user-attachments/assets/18885d2a-8df8-40a5-8b1a-3529c490c7d5" />

Locust results: 33.2 RPS, 22 P95 (ms), 46 P99 (ms)

<img width="1215" height="551" alt="image" src="https://github.com/user-attachments/assets/34c03e00-9ffe-4316-a27b-1b924fa0026c" />

<img width="1215" height="769" alt="image" src="https://github.com/user-attachments/assets/99e61288-bbfa-4304-a63f-3f00548b19cb" />

<img width="1213" height="387" alt="image" src="https://github.com/user-attachments/assets/c49d83ce-1db8-4d49-9cbe-394e154b5959" />

Brief analysis of Locust results: The two initial failures occurred due to no {order_id} being found in the orders DB table. This is correct behavior because the test script stores created orders in a list. At the beginning of the stress test, no orders are created. Thus, a particular request can return a 404 code. Overall, the application exhibited strong stability with only 2 errors out of 3871 requests. In terms of performance, the application exhibited solid metrics. The P95 and P99 metrics were both under 50 ms in the aggregate. Thus, the application exhibits good scalability under load. Peak latency experienced could be attributed to the small Task size used (0.5 vCPU and 2GiB memory) and the small RDS Class used (db.t4g.micro).

AWS configuration details:

ECS task definition added to repository as "cs218-api-task-4.json".

ALB target group health check configuration: The ALB Target Group health check monitors the /health endpoint. An additional container health check is configured.

<img width="1651" height="263" alt="image" src="https://github.com/user-attachments/assets/25aff2e7-742b-46f2-83e4-ab2c2b5ff9a2" />

<img width="1593" height="284" alt="image" src="https://github.com/user-attachments/assets/8787ee6b-8aa9-4edd-9c6b-7fa22c1bea97" />

Secrets injection: The "database_url" and the "db_password" variables are stored via AWS SSM Parameter Store and referenced by the container at runtime. Both variables are stored as a SecureString for security. The database host, user, password, and dbname are environment driven - as directed in the assignment instructions. 

<img width="1917" height="547" alt="image" src="https://github.com/user-attachments/assets/6efae1ea-97d3-4c00-9f78-56961764e355" />
