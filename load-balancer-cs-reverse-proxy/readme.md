They are related, but they solve **different problems**.

### Simple idea

* **Load Balancer** → distributes traffic across multiple servers.
* **Reverse Proxy** → receives client requests and forwards them to backend servers.

Think of a restaurant:

> **Reverse proxy = receptionist**
> **Load balancer = person deciding which waiter should handle you**

### 1. Reverse Proxy

A reverse proxy sits **in front of your servers**.

```text
User
  |
  v
Reverse Proxy
  |
  +----> Backend Server
  |
  +----> Frontend Server
  |
  +----> API Server
```

It can do things like:

* SSL/TLS termination
* Domain/path routing
* Authentication
* Header modification
* Caching
* Compression
* Hide backend server IPs
* Route `/api` → backend
* Route `/` → frontend

Examples:

* Nginx
* Traefik
* HAProxy
* Apache

For example:

```text
example.com/api/users
        |
        v
     Nginx
        |
        v
   Backend:3000
```

---

### 2. Load Balancer

A load balancer's primary job is to **distribute traffic across multiple backend instances**.

```text
                  +----> Server 1
                  |
User ---> Load Balancer ----> Server 2
                  |
                  +----> Server 3
```

Suppose you have:

```text
xora-server-1
xora-server-2
xora-server-3
```

The load balancer can distribute:

```text
Request 1 ---> server 1
Request 2 ---> server 2
Request 3 ---> server 3
Request 4 ---> server 1
```

It can also perform **health checks**:

```text
           +---- Server 1 ✓
           |
LB --------+---- Server 2 ✗  <- don't send traffic
           |
           +---- Server 3 ✓
```

Examples:

* AWS Application Load Balancer (ALB)
* AWS Network Load Balancer (NLB)
* Azure Load Balancer
* HAProxy
* Nginx
* Kubernetes LoadBalancer service

---

## The important part

A **reverse proxy can also act as a load balancer**.

For example, Nginx can do both:

```text
                    Nginx
               Reverse Proxy
                    +
              Load Balancer
                    |
          +---------+---------+
          |         |         |
          v         v         v
       Server 1  Server 2  Server 3
```

Nginx receives the request as a reverse proxy and then chooses a backend server using load-balancing algorithms.

---

## Real-world example

Imagine your architecture:

```text
Internet
   |
   v
Cloudflare
   |
   v
AWS ALB
   |
   +--------> Kubernetes Pod 1
   |
   +--------> Kubernetes Pod 2
   |
   +--------> Kubernetes Pod 3
```

Here the **ALB is a load balancer**, but it also performs reverse-proxy-like HTTP routing.

For example:

```text
api.example.com
       |
       v
     ALB
       |
       +----> xora-api pod 1
       +----> xora-api pod 2
       +----> xora-api pod 3
```

And you could have rules such as:

```text
/api/*       ---> backend
/admin/*     ---> admin service
/mobile/*    ---> mobile service
```

So modern cloud load balancers often provide **both load balancing and reverse-proxy functionality**.

### Quick comparison

| Feature                  | Reverse Proxy | Load Balancer        |
| ------------------------ | ------------- | -------------------- |
| Sits in front of servers | ✅             | ✅                    |
| Forwards requests        | ✅             | ✅                    |
| Distributes traffic      | Sometimes     | ✅                    |
| Health checks            | Often         | ✅                    |
| SSL termination          | Usually       | Usually              |
| Path-based routing       | ✅             | Often                |
| Hides backend servers    | ✅             | ✅                    |
| Main purpose             | Proxy/routing | Traffic distribution |

**In one sentence:**

> A **reverse proxy decides where a request should go**, while a **load balancer decides which healthy instance should handle it**—and one system can do both.
