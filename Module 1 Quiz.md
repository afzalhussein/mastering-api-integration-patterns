Great! Let’s reinforce **Module 1** with a quick interactive recap before moving to **Module 2**.  

---

### **🔍 Module 1 Recap Quiz**  
**1️⃣ Scenario:** A healthcare company stores patient records on-premises but wants to use a cloud-based AI API for diagnostics. What deployment model is this?  
- A) On-premises  
- B) Cloud  
- C) **Hybrid** ✅ (Correct! Mix of on-prem + cloud.)  

**2️⃣ True or False:** An API with a 99.5% SLA means it can be down for ~44 hours per year.  
- **True ✅** (Correct! 99.9% = ~9h, 99.5% = ~44h downtime/year.)  

**3️⃣ What’s the main advantage of routing third-party APIs through your own API gateway?**  
- A) Faster response times  
- B) **Centralized control (security, caching, logging)** ✅ (Correct!)  
- C) Cheaper pricing  

---

### **🚀 Module 2 Preview: Error Handling & HTTP Status Codes**  
**What You’ll Learn:**  
- How to design **meaningful error responses** (e.g., `404 Not Found` vs. `429 Too Many Requests`).  
- Best practices for **API resilience** (retries, circuit breakers).  
- Hands-on: **Fix a broken API response** in a mock scenario.  

**Let’s dive in!**  

---

### **📝 Lesson 2.1: HTTP Status Codes Demystified**  
APIs communicate success/failure via HTTP status codes. Major categories:  

| Code Range | Type          | Common Examples |  
|------------|---------------|------------------|  
| `2xx`      | Success       | `200 OK`, `201 Created` |  
| `4xx`      | Client Error  | `400 Bad Request`, `401 Unauthorized` |  
| `5xx`      | Server Error  | `500 Internal Server Error`, `503 Service Unavailable` |  

**Key Rules:**  
1. **Never invent custom codes** (e.g., `420 Enhance Your Calm` is non-standard).  
2. **Use the most specific code** (e.g., `409 Conflict` is better than `400` for duplicate data).  

---

### **💡 Pro Tip: Error Response Structure**  
Always return a **consistent JSON error format**:  
```json
{
  "error": {
    "code": "invalid_request",
    "message": "Date parameter must be in YYYY-MM-DD format",
    "details": "Provided: '2024/31/01'"
  }
}
```
*(This helps developers debug issues faster!)*  

---

### **🔧 Hands-on Exercise**  
**Task:** Analyze this broken API response and fix it:  
```http
GET /users/123 HTTP/1.1
Response: 500 Internal Server Error  
Body: "Oops, something broke :("
```  
**What’s wrong?**  
- A `500` error should **never expose raw internal errors**.  
- **Fix:** Return a structured `404 Not Found` if the user doesn’t exist, or a `400` with details if the request is malformed.  

*(Try rewriting it yourself before checking the solution below!)*  

---

### **📜 Solution**  
```http
HTTP/1.1 404 Not Found  
Content-Type: application/json  

{
  "error": {
    "code": "user_not_found",
    "message": "User ID 123 does not exist"
  }
}
```

---

### **📌 Key Takeaways**  
✅ **Use standard HTTP codes**—don’t reinvent the wheel!  
✅ **Structured error responses** save debugging time.  
✅ **5xx errors** = server-side, **4xx errors** = client-side.  

**Next:** We’ll cover **authentication (OAuth 2.0 vs. API keys)** in Module 3!  

---  
**Think you’ve got it? Try this:**  
1. What status code would you return for an invalid API key?  
   - **Answer:** `401 Unauthorized` 🔐  

Ready to move forward? Let me know!
