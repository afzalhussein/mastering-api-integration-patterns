# **Module 6: API Performance Optimization**  
**Objective:** Drastically improve API speed and scalability with caching, compression, CDNs, and efficient payload strategies.  

---

## **Lesson 6.1: Caching Strategies**  
### **1. Where to Cache**  
| **Layer**          | **Use Case**                          | **Tools**                     |  
|--------------------|---------------------------------------|-------------------------------|  
| **Client-Side**    | Static data (e.g., country lists)     | `Cache-Control` headers       |  
| **Edge (CDN)**     | Global read-heavy data (e.g., products) | Cloudflare, Fastly           |  
| **Server-Side**    | Database query results                | Redis, Memcached             |  

### **2. Cache-Control Headers**  
```http
# Cache publicly for 1 hour (browsers/CDNs)
Cache-Control: public, max-age=3600

# Prevent caching (dynamic data)
Cache-Control: no-store
```

**Pro Tip:** Use `ETag` for conditional requests (saves bandwidth):  
```http
GET /users/123  
Headers:  
  If-None-Match: "abc123"  

304 Not Modified  # No body sent if unchanged
```

---

## **Lesson 6.2: Payload Optimization**  
### **1. Compression**  
- **Gzip/Brotli** reduces JSON/XML size by 70%+.  
- **Always enable:**  
  ```nginx
  # Nginx config
  gzip on;
  gzip_types application/json;
  ```

### **2. Partial Responses**  
Fetch only needed fields (GraphQL-style):  
```http
GET /users/123?fields=name,email
```

### **3. Pagination**  
**Keyset (Cursor) Pagination > Offset** (better performance):  
```http
GET /articles?limit=10&after_id=20
```

---

## **Lesson 6.3: Database & Query Optimization**  
### **1. N+1 Query Problem**  
**Problem:** Fetching user + posts triggers 1 query for user + N for posts.  
**Solution:** **Eager loading** (JOINs or batch queries):  
```sql
SELECT * FROM users 
LEFT JOIN posts ON users.id = posts.user_id 
WHERE users.id = 123;
```

### **2. Read Replicas**  
- Offload read traffic to replica databases.  
- **Tools:** AWS RDS Read Replicas, PostgreSQL hot standby.  

---

## **🔥 Hands-on Lab: Optimize This API**  
**Scenario:** A slow e-commerce API:  
1. **No caching:** Product listings hit the DB every request.  
2. **Uncompressed JSON:** 50KB payloads.  
3. **Offset pagination:** `GET /products?page=1000` scans 10k rows.  

**Your Task:**  
1. Add Redis caching for product data (TTL: 1 hour).  
2. Enable Gzip compression in Nginx.  
3. Switch to keyset pagination (`after_id`).  

**Solution Hints:**  
```python
# 1. Redis caching (Python Flask)
import redis
cache = redis.Redis()

@app.get("/products")
def get_products():
    cached = cache.get("all_products")
    if cached: 
        return json.loads(cached)
    products = db.query("SELECT * FROM products")
    cache.setex("all_products", 3600, json.dumps(products))
    return products
```

```nginx
# 2. Nginx compression
gzip on;
gzip_types application/json;
```

```http
# 3. Keyset pagination
GET /products?limit=10&after_id=100
```

---

## **📌 Key Takeaways**  
✅ **Cache aggressively:** Client → CDN → Server layers.  
✅ **Compress everything:** Gzip/Brotli for JSON/XML.  
✅ **Optimize queries:** Avoid N+1, use read replicas.  

**Next Module:** **Monitoring & Observability**  

---  
**Quiz:**  
1. Why is keyset pagination faster than `LIMIT/OFFSET` for large datasets?  
   - **Answer:** `OFFSET` scans all skipped rows; keyset jumps directly.  

2. What header tells clients "this response is valid for 1 hour"?  
   - **Answer:** `Cache-Control: max-age=3600`  

Ready to monitor your APIs? Let’s go! 🚀
