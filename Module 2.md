# **Module 2: API Error Handling & Resilience Patterns**  
**Objective:** Master HTTP status codes, structured error responses, and fault tolerance in API integrations.  

---

## **Lesson 2.1: HTTP Status Codes Deep Dive**  
### **1. The 5 Categories of HTTP Codes**  
| Code | Type          | When to Use                          | Example Scenarios                     |  
|------|---------------|--------------------------------------|---------------------------------------|  
| `1xx`| Informational | Rarely used in APIs                  | `102 Processing` (long-running ops)   |  
| `2xx`| Success       | Request succeeded                    | `200 OK` (GET), `201 Created` (POST)  |  
| `3xx`| Redirection   | Client must take further action      | `301 Moved Permanently` (URL changes) |  
| `4xx`| Client Error  | Client sent invalid data/request     | `400 Bad Request`, `429 Too Many Requests` |  
| `5xx`| Server Error  | Server failed to fulfill valid request | `503 Service Unavailable` (maintenance) |  

**Golden Rule:**  
> *"Use the most specific code possible. A `409 Conflict` is more helpful than a generic `400`."*  

---

### **2. Critical Status Codes to Memorize**  
| Code | Meaning                  | Best Practices                          |  
|------|--------------------------|-----------------------------------------|  
| `400`| Bad Request              | Include why it failed (e.g., validation errors) |  
| `401`| Unauthorized             | Missing/invalid auth token              |  
| `403`| Forbidden                | Auth token valid but lacks permissions  |  
| `404`| Not Found                | Resource doesn’t exist                  |  
| `429`| Too Many Requests        | Implement retry-after header            |  
| `500`| Internal Server Error    | Never expose stack traces in production |  
| `503`| Service Unavailable      | Use during maintenance/overload         |  

**Anti-Pattern Alert:**  
❌ Returning `200 OK` for errors with `{ "error": "..." }` in the body.  
✅ **Do this instead:** Use the correct HTTP code + structured error body.  

---

## **Lesson 2.2: Designing Error Responses**  
### **1. The Ideal Error Response**  
```json
{
  "error": {
    "code": "invalid_date_format",  // Machine-readable
    "message": "Date must be YYYY-MM-DD",  // Human-readable
    "details": "Provided: '2024/31/01'",   // Debugging aid
    "documentation_url": "https://api.example.com/errors#invalid_date_format"  
  }
}
```

### **2. Pro Tips**  
- **Log correlation IDs:** Include a `request_id` to trace errors in logs.  
  ```json
  { "error": { ..., "request_id": "abc123" } }
  ```
- **Rate limiting:** Return `429` with headers:  
  ```http
  HTTP/1.1 429 Too Many Requests  
  Retry-After: 60  # Seconds to wait
  X-RateLimit-Limit: 1000  
  X-RateLimit-Remaining: 0  
  ```

---

## **Lesson 2.3: Fault Tolerance Patterns**  
### **1. Retry Mechanisms**  
**When to retry:**  
- Transient failures (e.g., `5xx`, `429`, network timeouts).  
- **Exponential backoff formula:**  
  ```python
  delay = min(initial_delay * (2 ** retry_attempt), max_delay)
  ```

### **2. Circuit Breakers**  
- **Purpose:** Stop hammering a failing API.  
- **States:**  
  - **Closed:** Requests pass through.  
  - **Open:** Fail fast (no requests allowed).  
  - **Half-Open:** Test if service recovered.  

**Tools:**  
- [Resilience4j](https://resilience4j.readme.io/) (Java)  
- [Polly](https://github.com/App-vNext/Polly) (.NET)  

---

## **🔥 Hands-on Lab: Fix These Broken APIs**  
**Scenario 1:**  
```http
GET /orders/999 HTTP/1.1  
Response: 200 OK  
Body: { "error": "Order not found" }  
```  
**What’s wrong?** → **Fix:** Use `404 Not Found`.  

**Scenario 2:**  
```http
POST /users  
Body: { "email": "not-an-email" }  
Response: 500 Internal Server Error  
Body: "Database constraint failed: invalid email"  
```  
**What’s wrong?** → **Fix:** Use `400 Bad Request` with validation details.  

*(Try fixing them yourself before peeking at the solutions below!)*  

---

## **📜 Lab Solutions**  
**Scenario 1 Fixed:**  
```http
HTTP/1.1 404 Not Found  
Content-Type: application/json  

{
  "error": {
    "code": "order_not_found",
    "message": "Order ID 999 does not exist"
  }
}
```

**Scenario 2 Fixed:**  
```http
HTTP/1.1 400 Bad Request  
Content-Type: application/json  

{
  "error": {
    "code": "invalid_email",
    "message": "Email must be a valid format",
    "details": "Provided: 'not-an-email'"
  }
}
```

---

## **📌 Key Takeaways**  
✅ **HTTP codes are contracts:** Misusing them breaks API consumers.  
✅ **Structured errors > string messages:** Include machine-readable codes.  
✅ **Plan for failure:** Retries + circuit breakers prevent cascading crashes.  

**Next Up:** **Module 3 - Authentication (OAuth 2.0 vs. API Keys)**.  

---  
**Check Your Understanding:**  
1. What status code should you return if a user submits a malformed JSON payload?  
   - **Answer:** `400 Bad Request`  

2. When should you use a `503` vs. a `500`?  
   - **Answer:** `503` for planned downtime/overload, `500` for unexpected server crashes.  

Ready for Module 3? Let me know! 🚀
