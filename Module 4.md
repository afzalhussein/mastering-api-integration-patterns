# **Module 4: Advanced API Integration Patterns**  
**Objective:** Master real-time integrations (Webhooks), bulk data transfers, and high-performance protocols like gRPC.  

---

## **Lesson 4.1: Real-Time Data with Webhooks**  
### **1. Webhooks vs. Polling**  
| **Criteria**       | **Webhooks** (Push)            | **Polling** (Pull)               |  
|--------------------|--------------------------------|----------------------------------|  
| **Latency**        | Instant                        | Delayed (depends on poll interval)|  
| **Server Load**    | Low (only on events)           | High (constant requests)         |  
| **Complexity**     | Requires a public endpoint     | Simple to implement              |  

**When to Use Webhooks:**  
- Payment notifications (e.g., Stripe)  
- Chat apps (e.g., Slack message events)  

---

### **2. Webhook Security**  
**Problem:** Anyone can send fake webhook requests to your endpoint.  
**Solutions:**  
1. **Signature Verification**  
   - Provider signs payloads with a secret key:  
     ```python
     # Server sends this header:
     X-Signature: sha256=abc123...
     ```
   - You verify it:  
     ```python
     expected_sig = hmac.new(secret, payload, hashlib.sha256).hexdigest()
     assert received_sig == expected_sig
     ```  
2. **IP Whitelisting**  
   - Only accept requests from known provider IPs (e.g., GitHub’s webhook IP ranges).  

---

## **Lesson 4.2: Bulk Data Transfers**  
### **1. When to Use Bulk APIs**  
- Migrating databases (e.g., CRM records)  
- Daily analytics syncs  

**Example (Salesforce Bulk API):**  
```http
POST /jobs/ingest  
Content-Type: text/csv  

id,name,email  
1,John,john@example.com  
2,Emma,emma@example.com  
```

### **2. Bulk API Patterns**  
1. **Asynchronous Processing**  
   - Submit job → Get job ID → Poll for completion.  
2. **Chunking**  
   - Split large files into 10k-record batches.  
3. **Retry Logic**  
   - Exponential backoff for failed batches.  

---

## **Lesson 4.3: High-Performance APIs with gRPC**  
### **1. gRPC vs. REST**  
| **Criteria**   | **gRPC**                      | **REST**                     |  
|---------------|-------------------------------|------------------------------|  
| **Protocol**  | HTTP/2 + Protobuf (binary)    | HTTP/1.1 + JSON/XML (text)   |  
| **Speed**     | 5-10x faster (binary + multiplexing)| Slower (text parsing)     |  
| **Use Cases** | Microservices, IoT, real-time | Public-facing APIs           |  

### **2. Key gRPC Features**  
1. **Streaming**  
   - Server can push multiple responses over one connection.  
2. **Code Generation**  
   - `.proto` files auto-generate client/server code.  

**Example `.proto`:**  
```protobuf
service UserService {
  rpc GetUser (UserRequest) returns (UserResponse);
}

message UserRequest { int32 id = 1; }
message UserResponse { string name = 1; }
```

---

## **🔥 Hands-on Lab: Build a Webhook Receiver**  
**Task:**  
1. Use **Python + Flask** to create an endpoint that:  
   - Accepts GitHub push event webhooks.  
   - Verifies the `X-Hub-Signature-256` header.  
   - Logs the commit message.  

**Starter Code:**  
```python
from flask import Flask, request
import hmac
import hashlib

app = Flask(__name__)
WEBHOOK_SECRET = b'your_secret_here'

@app.route('/webhook', methods=['POST'])
def webhook():
    signature = request.headers.get('X-Hub-Signature-256')
    payload = request.data
    
    # Verify signature (compare with HMAC-SHA256 of payload)
    expected_sig = 'sha256=' + hmac.new(WEBHOOK_SECRET, payload, hashlib.sha256).hexdigest()
    
    if not hmac.compare_digest(signature, expected_sig):
        return "Invalid signature", 403
    
    commit_msg = request.json.get('head_commit', {}).get('message')
    print(f"New commit: {commit_msg}")
    return "OK", 200
```

**Testing:**  
1. Use **ngrok** to expose your local server:  
   ```bash
   ngrok http 5000
   ```  
2. Configure GitHub repo webhooks to point to your ngrok URL.  

---

## **📌 Key Takeaways**  
✅ **Webhooks:** Best for real-time events (secure them with signatures).  
✅ **Bulk APIs:** Optimize for large data transfers (async + chunking).  
✅ **gRPC:** Use for internal microservices needing high performance.  

**Next Module:** **API Versioning & Deprecation Strategies**  

---  
**Quiz:**  
1. Why is binary data (like Protobuf) faster than JSON?  
   - **Answer:** Less parsing overhead + smaller payload size.  

2. How do you prevent replay attacks in webhooks?  
   - **Answer:** Timestamp checks + nonce validation.  

Ready for the next challenge? Let’s go! 🚀
