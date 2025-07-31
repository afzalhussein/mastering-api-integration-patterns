# **Module 7: API Monitoring & Observability**  
**Objective:** Gain real-time visibility into API health, detect issues proactively, and troubleshoot failures using logs, metrics, and traces.  

---

## **Lesson 7.1: The Three Pillars of Observability**  
### **1. Logs**  
- **What:** Timestamped records of events (errors, requests).  
- **Tools:** ELK Stack (Elasticsearch, Logstash, Kibana), Datadog.  
- **Best Practice:**  
  ```python
  # Structured logging (JSON format)
  log.info("User logged in", {"user_id": 123, "ip": "1.2.3.4"})
  ```

### **2. Metrics**  
- **What:** Quantitative data (request rate, latency, errors).  
- **Key Metrics to Track:**  
  - **Throughput:** Requests per second (RPS).  
  - **Latency:** P50, P90, P99 response times.  
  - **Error Rate:** 5xx errors / total requests.  
- **Tools:** Prometheus, Grafana, New Relic.  

### **3. Traces**  
- **What:** Follow a request across microservices.  
- **Tools:** Jaeger, Zipkin, AWS X-Ray.  
- **Example Trace:**  
  ```
  Request → API Gateway (10ms) → Auth Service (5ms) → DB Query (20ms)
  ```

---

## **Lesson 7.2: Implementing Alerts**  
### **1. Alerting Rules**  
- **Critical:** `Error rate > 5% for 5 minutes` → Page the team.  
- **Warning:** `Latency P99 > 1s` → Slack notification.  

### **2. SLOs (Service Level Objectives)**  
- Define targets like:  
  - **Availability:** 99.9% uptime.  
  - **Latency:** 95% of requests < 500ms.  

**Example Prometheus Alert:**  
```yaml
# Alert if error rate exceeds 5%
- alert: HighErrorRate
  expr: rate(http_requests_total{status=~"5.."}[5m]) / rate(http_requests_total[5m]) > 0.05
  for: 10m
  labels:
    severity: critical
  annotations:
    summary: "High error rate on {{ $labels.api_path }}"
```

---

## **Lesson 7.3: Distributed Tracing**  
### **1. Instrument Your API**  
- **Python (Flask + OpenTelemetry):**  
  ```python
  from opentelemetry import trace
  tracer = trace.get_tracer(__name__)

  @app.route("/users/<id>")
  def get_user(id):
      with tracer.start_as_current_span("get_user"):
          user = db.query_user(id)  # Auto-traced if DB supports it
          return jsonify(user)
  ```

### **2. Visualize Traces**  
![Jaeger UI](https://jaegertracing.io/img/screenshot-trace.png)  
*Identify slow spans (e.g., a slow SQL query).*  

---

## **🔥 Hands-on Lab: Set Up Monitoring**  
**Task:**  
1. **Instrument a Node.js API** with Prometheus metrics.  
2. **Create a Grafana dashboard** showing RPS and latency.  
3. **Add an alert** for high error rates.  

**Solution (Node.js):**  
```javascript
// 1. Add Prometheus client
const promClient = require('prom-client');
const httpRequestDuration = new promClient.Histogram({
  name: 'http_request_duration_ms',
  help: 'HTTP request duration (ms)',
  labelNames: ['route', 'method'],
});

// 2. Track request duration
app.use((req, res, next) => {
  const end = httpRequestDuration.startTimer();
  res.on('finish', () => end({ route: req.path, method: req.method }));
  next();
});

// 3. Expose metrics endpoint
app.get('/metrics', (req, res) => {
  res.set('Content-Type', promClient.register.contentType);
  res.end(promClient.register.metrics());
});
```

**Grafana Query:**  
```
rate(http_request_duration_ms_sum[5m]) / rate(http_request_duration_ms_count[5m])
```

---

## **📌 Key Takeaways**  
✅ **Logs:** Debug individual errors (use structured JSON).  
✅ **Metrics:** Track trends (latency, errors, RPS).  
✅ **Traces:** Find bottlenecks in microservices.  
✅ **Alerting:** Catch issues before users do.  

**Next Module:** **Documentation & Developer Experience**  

---  
**Quiz:**  
1. What’s the difference between P50 and P99 latency?  
   - **Answer:** P50 = median, P99 = worst 1% of requests.  

2. Why is distributed tracing hard without instrumentation?  
   - **Answer:** Requests lose context across service boundaries.  

Ready to build great docs? Let’s go! 🚀
