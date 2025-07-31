# **Module 10: Real-World API Case Studies**  
**Objective:** Learn from high-profile API successes and failures to avoid pitfalls and emulate best practices.  

---

## **Case Study 1: Twitter API v1 → v2 Migration**  
### **What Happened?**  
- **v1 (2006):** REST + ad-hoc features → became messy.  
- **v2 (2021):** Complete redesign with:  
  - Clearer resource models (Tweets vs. Retweets).  
  - OAuth 2.0 mandatory.  
  - Free tier limited to 500k tweets/month.  

### **Key Takeaways:**  
✅ **Breaking changes require long lead times** (Twitter gave 18+ months).  
✅ **Deprecate incrementally:** v1 ran alongside v2 for years.  
❌ **Developer backlash:** Sudden free-tier cuts hurt startups.  

**Lesson:** *Plan migrations early, communicate transparently.*  

---

## **Case Study 2: Slack’s Webhook Outage (2020)**  
### **What Happened?**  
- Webhooks stopped delivering for 4+ hours due to:  
  - **Overloaded workers** (no auto-scaling).  
  - **No dead-letter queues** (lost events).  

### **Fix:**  
1. Added auto-scaling for webhook processors.  
2. Implemented retries + DLQs (Dead-Letter Queues).  

**Lesson:** *Design for failure—assume webhooks will backlog.*  

---

## **Case Study 3: Stripe’s API Idempotency**  
### **Genius Design Choice:**  
- **Problem:** Network retries could double-charge customers.  
- **Solution:** Idempotency keys:  
  ```http
  POST /v1/charges  
  Idempotency-Key: abc123  
  ```  
  - First request: Processes normally.  
  - Duplicate request: Returns same response.  

**Lesson:** *Idempotency is critical for payments/state-changing APIs.*  

---

## **Case Study 4: GitHub’s GraphQL API**  
### **Why GraphQL?**  
- **REST Pain Points:**  
  - Over-fetching (e.g., loading entire user object for just logins).  
  - Under-fetching (multiple roundtrips for related data).  
- **GraphQL Solution:**  
  ```graphql
  query {
    user(login: "torvalds") {
      name
      repositories(first: 5) {
        nodes { name }
      }
    }
  }
  ```  

**Lesson:** *Use GraphQL for complex, nested data needs.*  

---

## **Case Study 5: AWS API Gateway Throttling**  
### **The Good:**  
- **Request shaping:** Protects backends from traffic spikes.  
- **Usage plans:** Tiered access (free/paid/enterprise).  

### **The Bad:**  
- Default limit: **10,000 RPS per account** → surprises at scale.  

**Lesson:** *Know your platform limits before scaling.*  

---

## **🔥 Hands-on Lab: Analyze a Public API**  
**Task:** Pick one API (e.g., OpenAI, Spotify) and:  
1. **Evaluate its documentation** (clarity, examples).  
2. **Test error handling** (malformed requests).  
3. **Check for idempotency** (retry a POST with same ID).  

**Example (OpenAI):**  
```bash
# 1. Test error handling
curl https://api.openai.com/v1/chat/completions \
  -H "Authorization: Bearer invalid_key" \
  -d '{"model":"gpt-4"}'

# 2. Check idempotency
curl -X POST https://api.openai.com/v1/completions \
  -H "Idempotency-Key: test123" \
  -d '{"model":"text-davinci-003", "prompt":"Hello"}'
```

---

## **📌 Key Takeaways**  
✅ **Learn from others:** Avoid Twitter’s v1 mistakes, emulate Stripe’s idempotency.  
✅ **Design for scale:** Slack’s outage teaches queue resilience.  
✅ **Choose the right paradigm:** REST for simplicity, GraphQL for flexibility.  

**Course Complete!** 🎉  
**Final Project:** Build and document an API applying all 10 modules’ lessons.  

---  
**Quiz:**  
1. Why did Twitter’s v2 migration anger developers?  
   - **Answer:** Sudden free-tier restrictions with short notice.  

2. What’s the benefit of idempotency keys?  
   - **Answer:** Prevents duplicate operations (e.g., double charges).  

Congrats! You’re now an API expert. Go build something amazing. 🚀
