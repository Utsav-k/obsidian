🎯 Must-Know Fundamentals
1. Load Balancer Types
text

Layer 4 (L4) - Transport Layer
- Works with TCP/UDP
- Faster, less overhead
- No content awareness

Layer 7 (L7) - Application Layer  
- Works with HTTP/HTTPS
- Smarter routing (path, headers)
- SSL termination

2. Key Algorithms (Know 3-4 well)

    Round Robin - Simple rotation

    Least Connections - Best for long-lived connections

    IP Hash - Session persistence

    Least Response Time - Performance optimization

🔥 Core Interview Points
Architecture Patterns
text

Client → Load Balancer → [Backend Servers]
                      ↳ Health Checks
                      ↳ Service Discovery

Health Checking

    Active checks: LB proactively pings servers

    Passive checks: Monitors real traffic responses

    Grace periods: Avoid flapping (rise/fall config)

Session Persistence

    Why: Shopping carts, user sessions

    How:

        IP-based stickiness

        Cookie insertion

        SSL session ID

💡 Real-World Scenarios to Discuss
Scenario 1: E-commerce Site

"We'd use L7 LB for path-based routing:

    /api/ → API servers

    /static/ → CDN/cache servers

    / → Web servers

Session persistence for shopping carts, health checks every 5s"
Scenario 2: High-Traffic API

"L4 LB for raw performance, least connections algorithm, active health checks, circuit breaker pattern to isolate failing servers"
Scenario 3: Canary Deployment

"Route 5% traffic to new version, monitor metrics, gradually increase if stable"
🛠️ Technical Deep Dives
SSL Termination

    What: LB handles SSL, backend gets plain HTTP

    Benefit: Reduces backend CPU load

    Trade-off: LB becomes security critical point

High Availability

    Active-Active: Multiple LBs sharing load

    Active-Passive: Hot standby with failover

    Use VIP (Virtual IP) for client-facing endpoint

📊 Monitoring & Scaling
Key Metrics

    Requests per second

    Error rates (4xx, 5xx)

    Response time (p50, p95, p99)

    Backend server health

Auto-scaling Integration

"LB health checks trigger auto-scaling groups to replace unhealthy instances automatically"
🚨 Common Interview Questions

    "L4 vs L7 - when to use which?"

        L4: Raw performance, simple routing

        L7: Content-aware routing, SSL termination

    "How do you handle server failures?"

        Health checks detect failures

        Remove from pool automatically

        Circuit breaker pattern

    "Session persistence strategies?"

        IP hash (simple but problematic with NAT)

        Application cookies (more reliable)

    "Scaling considerations?"

        LB can become bottleneck

        Consider multiple LB tiers

        DNS round robin for geographic distribution

🎤 Interview Framework

When asked to design:

    "First, I'd identify the traffic pattern - is this HTTP API or raw TCP?"

    "Choose L4 for performance, L7 for smart routing"

    "Implement health checks with appropriate intervals"

    "Consider session persistence needs"

    "Plan for high availability with multiple LBs"

    "Set up monitoring and auto-scaling integration"

⚡ Quick Recall Points

    L4 = Fast, dumb | L7 = Smart, feature-rich

    Health checks prevent routing to dead servers

    Session persistence maintains user state

    SSL termination offloads crypto work

    Multiple LBs prevent single point of failure
