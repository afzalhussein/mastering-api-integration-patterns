# **Module 8: API Documentation & Developer Experience (DX)**  
**Objective:** Craft API documentation that delights developers, reduces support overhead, and accelerates adoption.  

---

## **Lesson 8.1: Principles of Great API Docs**  
### **1. The 5 Must-Have Sections**  
1. **Getting Started**  
   - "Hello World" example in 5 mins.  
2. **Authentication**  
   - Clear examples for API keys/OAuth.  
3. **Endpoints**  
   - Method, URL, params, request/response samples.  
4. **Error Codes**  
   - List all possible errors + fixes.  
5. **SDKs & Libraries**  
   - Code snippets in Python, JavaScript, etc.  

**Example (Stripe-Style):**  
```markdown
# Charge a Credit Card  
`POST /v1/charges`  

**Request:**  
```curl
curl https://api.example.com/v1/charges \
  -u sk_test_123: \
  -d amount=1000 \
  -d currency=usd \
  -d source=tok_visa
```

**Response:**  
```json
{
  "id": "ch_123",
  "amount": 1000,
  "status": "succeeded"
}
```
```

---

## **Lesson 8.2: Tools for API Documentation**  
### **1. OpenAPI/Swagger**  
- **What:** Machine-readable API spec (YAML/JSON).  
- **Benefits:**  
  - Auto-generate interactive docs (Swagger UI).  
  - Client SDK generation.  

**Example `openapi.yaml`:**  
```yaml
paths:
  /users/{id}:
    get:
      summary: Get a user
      parameters:
        - name: id
          in: path
          required: true
          schema: { type: integer }
      responses:
        '200':
          description: A user object
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
```

### **2. ReadMe / Stoplight**  
- **What:** Hosted docs with analytics.  
- **Features:**  
  - Versioning  
  - Try-it-out consoles  

---

## **Lesson 8.3: Improving Developer Experience (DX)**  
### **1. SDKs in Multiple Languages**  
- **Why:** Reduce boilerplate code.  
- **Tools:**  
  - **OpenAPI Generator** (auto-generate SDKs).  
  - **LibLab** (custom SDKs as a service).  

**Example (Python SDK):**  
```python
from example_sdk import ExampleAPI

api = ExampleAPI(api_key="abc123")
user = api.get_user(42)
```

### **2. Interactive API Playgrounds**  
- **Tools:** Postman Collections, Swagger UI.  
- **Example:**  
  ![Postman Example](https://assets.postman.com/postman-docs/v10/api-documentation-postman.jpg)  

### **3. Onboarding Checklists**  
1. Sign up for API key  
2. Try the `/ping` endpoint  
3. Make your first real request  
4. Join Slack community  

---

## **🔥 Hands-on Lab: Document This API**  
**Task:**  
1. Write OpenAPI specs for:  
   ```http
   GET /v1/products?in_stock=true
   Response: [{"id": 1, "name": "Shoes", "price": 99.99}]
   ```  
2. Generate a Swagger UI page.  

**Solution:**  
```yaml
# openapi.yaml
openapi: 3.0.0
info:
  title: Product API
  version: 1.0.0
paths:
  /v1/products:
    get:
      summary: List products
      parameters:
        - name: in_stock
          in: query
          schema: { type: boolean }
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Product'
components:
  schemas:
    Product:
      type: object
      properties:
        id: { type: integer }
        name: { type: string }
        price: { type: number }
```

**Run Swagger UI:**  
```bash
docker run -p 8080:8080 -v $(pwd):/tmp swaggerapi/swagger-ui --url /tmp/openapi.yaml
```

---

## **📌 Key Takeaways**  
✅ **Docs = Marketing:** Great docs drive API adoption.  
✅ **OpenAPI is King:** Enables auto-generated SDKs + interactive docs.  
✅ **Invest in DX:** SDKs, playgrounds, and communities reduce friction.  

**Next Module:** **API Governance & Scaling**  

---  
**Quiz:**  
1. What’s the fastest way to generate SDKs for multiple languages?  
   - **Answer:** Use OpenAPI Generator with your `openapi.yaml`.  

2. Why include a "Try It" button in docs?  
   - **Answer:** Lets developers test APIs without writing code.  

Ready to scale your API? Let’s go! 🚀
