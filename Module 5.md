# **Module 5: API Versioning & Deprecation Strategies**  
**Objective:** Learn how to evolve APIs without breaking existing clients using versioning techniques and graceful deprecation.  

---

## **Lesson 5.1: Why Versioning Matters**  
### **1. The Challenge of API Evolution**  
- APIs change over time (new features, bug fixes, security updates).  
- **Breaking changes** can crash existing apps:  
  - Renaming/deleting fields  
  - Changing authentication  
  - Modifying response structures  

### **2. Versioning Goals**  
- ✅ **Backward compatibility** (old clients keep working).  
- ✅ **Clear migration paths** (documented, timed deprecations).  
- ✅ **Minimize fragmentation** (avoid too many active versions).  

---

## **Lesson 5.2: Versioning Strategies**  
### **1. URL Path Versioning (`/v1/users`)**  
**Pros:**  
- Simple to implement.  
- Easy to debug (version visible in logs).  

**Cons:**  
- Clutters URLs.  

**Example:**  
```http
GET /v1/users/123  
GET /v2/users/123  
```

### **2. Header Versioning (`Accept: application/vnd.myapi.v1+json`)**  
**Pros:**  
- Clean URLs.  
- Supports content negotiation.  

**Cons:**  
- Harder to debug (version hidden in headers).  

**Example:**  
```http
GET /users/123  
Headers:  
  Accept: application/vnd.myapi.v2+json  
```

### **3. Query Param Versioning (`/users/123?version=1`)**  
**Pros:**  
- Easy for testing (no header setup).  

**Cons:**  
- Poor caching (query params often ignored by CDNs).  

---

## **Lesson 5.3: Deprecation Best Practices**  
### **1. The 3-Phase Deprecation Plan**  
| **Phase**       | **Action**                          | **Timeline**          |  
|-----------------|-------------------------------------|-----------------------|  
| **Announce**    | Document deprecation + migration guide | 6+ months before cutoff |  
| **Warn**       | Add `Deprecation: true` header + logs | 3 months before cutoff |  
| **Sunset**     | Return `410 Gone` for old versions  | Post-cutoff            |  

**Example Deprecation Header:**  
```http
HTTP/1.1 200 OK  
Deprecation: true  
Sunset: Mon, 1 Jan 2025 00:00:00 GMT  
Link: <https://api.example.com/v2-migration>; rel="deprecation"  
```

### **2. Breaking Change Communication**  
- **Changelog:** Publicly track changes (e.g., GitHub Releases).  
- **Email Alerts:** Notify registered developers.  
- **API Analytics:** Identify active users of old versions.  

---

## **🔥 Hands-on Lab: Version a Breaking Change**  
**Scenario:**  
Your `User` API response is changing:  
- Old field: `username` (string)  
- New field: `display_name` (string)  

**Task:**  
1. Implement **backward compatibility** for `/v1/users`.  
2. Design a **deprecation timeline** for `username`.  

**Solution (Node.js pseudo-code):**  
```javascript
// v1/users/123 - Backward compatible
app.get('/v1/users/:id', (req, res) => {
  const user = db.getUser(req.params.id);
  res.json({
    id: user.id,
    username: user.display_name, // Legacy field
    display_name: user.display_name // New field
  });
});

// v2/users/123 - New version
app.get('/v2/users/:id', (req, res) => {
  const user = db.getUser(req.params.id);
  res.json({
    id: user.id,
    display_name: user.display_name
  });
});
```

**Deprecation Plan:**  
1. **Announce (Today):** Document that `username` will be removed in 6 months.  
2. **Warn (Month 4):** Add `Deprecation` headers to `/v1` responses.  
3. **Sunset (Month 6):** Remove `username` and return `410 Gone` for `/v1`.  

---

## **📌 Key Takeaways**  
✅ **URL versioning** is simplest; **header versioning** is cleaner.  
✅ **Deprecate gracefully:** Announce → Warn → Sunset.  
✅ **Monitor usage** to avoid stranding users.  

**Next Module:** **Performance Optimization (Caching, Compression, CDNs)**  

---  
**Quiz:**  
1. Why might you avoid query param versioning?  
   - **Answer:** Caching issues (params often ignored by CDNs).  

2. What HTTP status indicates a deprecated API?  
   - **Answer:** `410 Gone` (after sunset) or `200 OK` with `Deprecation` header.  

Ready to optimize? Let’s go! 🚀
