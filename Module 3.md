# **Module 3: API Authentication & Security Patterns**  
**Objective:** Master API security with API keys, OAuth 2.0, OpenID Connect, and best practices for protecting your integrations.

---

## **Lesson 3.1: Authentication Fundamentals**  
### **1. Why Authentication Matters**  
- **Verifies identity:** Who is calling your API?  
- **Controls access:** What can they do?  
- **Prevents abuse:** Rate limiting, auditing.  

### **2. Common Threats**  
- 🚨 **API key leaks** (accidental exposure in code)  
- 🚨 **Token theft** (man-in-the-middle attacks)  
- 🚨 **DDoS attacks** (excessive unauthenticated requests)  

---

## **Lesson 3.2: Authentication Methods**  
### **1. API Keys**  
**How it works:**  
- Simple string passed in headers or query params:  
  ```http
  GET /users?api_key=abc123
  ```
**Pros:**  
- Easy to implement  
- Low overhead  

**Cons:**  
- ❌ **Never store in frontend code** (use backend proxies)  
- ❌ No granular permissions  

**Best Practices:**  
- 🔐 **Rotate keys regularly** (e.g., every 90 days)  
- 📛 **Use headers, not URLs** (avoid logging leaks):  
  ```http
  Authorization: ApiKey abc123
  ```

---

### **2. OAuth 2.0**  
**For delegated access** (e.g., "Sign in with Google").  

**Flow:**  
1. User clicks "Login" → Redirect to OAuth provider (e.g., GitHub).  
2. User approves permissions → Returns `authorization_code`.  
3. Your backend exchanges `code` for `access_token`.  

**Key Terms:**  
- **Access Token:** Short-lived (1 hour)  
- **Refresh Token:** Long-lived (used to get new access tokens)  

**Example Request:**  
```http
POST /oauth/token  
Body: {
  "client_id": "your_app_id",
  "client_secret": "your_secret",
  "code": "authorization_code"
}
```

---

### **3. OpenID Connect (OIDC)**  
**Extension of OAuth 2.0** that adds identity verification (e.g., user email, profile).  

**Use Case:**  
- "Sign in with Google" that also fetches the user’s name/email.  

**ID Token Format (JWT):**  
```json
{
  "iss": "https://accounts.google.com",
  "sub": "1234567890",
  "email": "user@example.com",
  "exp": 1670000000
}
```

---

## **Lesson 3.3: Security Best Practices**  
### **1. Always Use HTTPS**  
- 🔒 Encrypts data in transit.  
- 🛑 Prevents token theft via sniffing.  

### **2. Validate Inputs**  
- **SQL Injection:** Use parameterized queries.  
- **XSS Attacks:** Sanitize user inputs in responses.  

### **3. Rate Limiting**  
- **Defense:** Return `429 Too Many Requests` with headers:  
  ```http
  X-RateLimit-Limit: 100  
  X-RateLimit-Remaining: 0  
  Retry-After: 60
  ```

---

## **🔥 Hands-on Lab: Secure This API**  
**Scenario:**  
A weather API uses API keys but has these vulnerabilities:  
1. Keys are passed in URLs (`/forecast?api_key=abc123`).  
2. No rate limiting.  
3. No HTTPS.  

**Your Task:**  
1. Fix the key placement (use headers).  
2. Add rate-limiting logic (pseudo-code).  
3. Explain why HTTPS is mandatory.  

*(Try it yourself before checking the solution!)*  

---

## **📜 Lab Solution**  
**1. Key in Headers:**  
```http
GET /forecast  
Authorization: ApiKey abc123
```

**2. Rate Limiting (Node.js pseudo-code):**  
```javascript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100 // Limit each IP to 100 requests per window
});

app.use(limiter);
```

**3. HTTPS Explanation:**  
- Prevents eavesdropping on tokens.  
- Google Chrome marks HTTP sites as "Not Secure."  

---

## **📌 Key Takeaways**  
✅ **API Keys:** Simple but insecure for frontend use.  
✅ **OAuth 2.0:** Best for delegated access (e.g., third-party logins).  
✅ **OIDC:** Adds identity to OAuth (get user profile data).  
✅ **HTTPS + Rate Limiting:** Non-negotiable for production APIs.  

**Next Module:** **Advanced Patterns (Webhooks, Bulk APIs, gRPC)**!  

---  
**Quiz:**  
1. What’s the risk of putting API keys in URLs?  
   - **Answer:** They get logged in server logs/exposed in browser history.  

2. Which is more secure for a mobile app: API keys or OAuth?  
   - **Answer:** OAuth (tokens are short-lived and scope-limited).  

Ready to level up? Let’s go! 🚀
