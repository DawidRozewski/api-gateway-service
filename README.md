# 🌐 API Gateway Service

## 📌 Overview
This service acts as an **entry point** for the microservices architecture.  
It provides **routing, authentication, and request forwarding** using **Spring Cloud Gateway**.

---

## 🏗️ About This Project
This project is designed as a **skeleton for microservices architecture**, providing a foundation for:
- **Securing microservices using OAuth2 + Keycloak**
- **Managing service-to-service communication efficiently**
- **Experimenting with routing, authentication, and request filtering**

The focus is on demonstrating how microservices interact rather than delivering a full production-ready system.

---

## 🛠️ Tech Stack
- **Java 21**
- **Spring Boot 3.4.3**
- **Spring Cloud Gateway**
- **Spring Security + OAuth2 + Keycloak**
- **Maven 3.14.0**
- **Docker**

## 🛠️ Infrastructure Stack (Docker)
- **Keycloak**
- **Kafka**
- **Kafka UI**
- **Prometheus**
- **Grafana**
- **MongoDB**

---

## 🚀 Running API Gateway Service
Before starting this service, ensure that other microservices are running.  
Follow the setup instructions in the **[Config Server README](https://github.com/DawidRozewski/config-server)**.

### **Run using Maven**
```bash
mvn spring-boot:run
```
API Gateway will be available at:  
🔗 [http://localhost:8082](http://localhost:8082)

---

## 🔑 Keycloak Setup (Manual Configuration)
To encourage users to explore Keycloak, this project does not include a pre-configured setup.  
Follow these steps to configure authentication manually:

### **1️⃣ Login to Keycloak Admin Console**
- Open a browser and navigate to: [http://localhost:8085/admin](http://localhost:8085/admin)
- Login using default credentials (`admin` / `admin`).
- Ensure you are working in the **master realm** (default setup).

### **2️⃣ Create a New Client**
- Navigate to **Clients** → **Create**.
- Set **Client ID** to `${your-client-id}`.
- Turn on **Client authentication** and **Authorization**.
- Configure **Root URL**: `http://localhost:8082`
- Save and generate a client secret.

### **3️⃣ Create Roles within the Client**
- Navigate to **Clients** → **${your-client-id}** → **Roles**.
- Add two roles: `admin` and `user`.

### **4️⃣ Create a User and Assign Roles**
- Navigate to **Users** → **Add User**.
- Set the username and enable the user.
- Set a password in the **Credentials** tab.
- Navigate to **Users** → Select the user → **Role Mappings**.
- Assign the previously created roles (`admin` or `user`) under **Client Roles**.

### **5️⃣ Retrieve JWT Token**
Authenticate using Keycloak’s token endpoint:

```bash
curl -X POST "http://localhost:8085/realms/master/protocol/openid-connect/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
-d "client_id=${YOUR_CLIENT_ID}" \
-d "client_secret=${YOUR_CLIENT_SECRET}" \
-d "username=${user}" \
-d "password=${password}" \
-d "grant_type=password"
```

Copy the `access_token` and **use it in API requests** if needed.
---

## 📌 Available API Endpoints

This API Gateway routes requests to the corresponding microservices. Below is a list of available endpoints:

| Endpoint             | Method     | Description                                 | Requires Authentication |
|----------------------|------------|---------------------------------------------|-------------------------|
| `/producer/send`     | **POST**   | Sends a message to Kafka                    | ❌ No                    |
| `/producer/admin`    | **GET**    | Check if user with ROLE_ADMIN has access    | ✅ Yes (JWT required)    |
| `/producer/user`     | **GET**    | Check if user with ROLE_USER has access     | ✅ Yes (JWT required)    |
| `/consumer/messages` | **GET**    | Retrieves messages from Kafka               | ❌ No                    |
| `/consumer/admin`    | **GET**    | Check if user with ROLE_ADMIN has access    | ✅ Yes (JWT required)    |
| `/consumer/user`     | **GET**    | Check if user with ROLE_USER has access     | ✅ Yes (JWT required)    |
| `/actuator/health`   | **GET**    | Checks the health status of the API Gateway | ❌ No                    |
| `/actuator/metrics`  | **GET**    | Provides application metrics             | ❌ No                    |
| `/actuator/gateway`  | **GET**    | Provides registered routes and filters for API Gateway         | ❌ No                    |

📢 **Note:** All protected endpoints require a valid **JWT token** obtained from **Keycloak**.  
Refer to the **Keycloak Setup** section for authentication details.

---

## 🔄 Refactoring Suggestions
Looking to improve API Gateway? Here are some ideas:

1️⃣ **Enhance Security**
- Implement **role-based access control (RBAC)** for finer-grained authorization.
- Ensure **OAuth2 scopes** are properly enforced.
- 🔹 **How can we extend the refresh token lifetime in Keycloak?**
  - Adjusting **token lifespan settings** in Keycloak can help maintain longer user sessions without frequent re-authentication.

2️⃣ **Optimize Request Handling**
- Introduce **global exception handling** for better error messages.
- Implement **rate limiting** to prevent service overload.

3️⃣ **Implement Circuit Breaker Pattern to Improve Fault Tolerance**
- What happens if a microservice crashes or becomes unresponsive?
- Implement a **Circuit Breaker** (e.g., using **Resilience4j**) to prevent cascading failures.
- 🔹 **How can we provide a fallback mechanism when a service is unavailable?**
  - Add a **graceful degradation strategy**, e.g., return a default response or cached data instead of failing completely.
  - Consider **handling timeouts and retries** for transient errors.

---

📢 **This project is a microservices skeleton meant for learning, experimentation, and testing new ideas.**  
Feel free to modify and extend it to fit your needs! 🚀
